Hi Steve & Justin

I've experimented with Airflow to the point where I can have a "sensor DAG"
trigger a separate "processing DAG".

_Idea: the "sensor" DAG monitors the landing zone; when all expected files
arrive, it creates a timestamped copy and triggers the processing DAG to handle
the actual processing._

To implement this elegantly in a multi-tenant environment, where we may have
multiple processing DAGs for different dataflows, and to have them triggered
declaratively (rather than imperatively from the end of the first DAG), we need
the data partitioning functionality described in [Airflow AIP-76](https://cwiki.apache.org/confluence/display/AIRFLOW/AIP-76+Asset+Partitions)
(suggested to be released in v3.1, roughly around September).

_Unfortunately, it looks like [the AIP-76 work hasn't started yet](https://github.com/orgs/apache/projects/434/views/1)_

### Here are the current options and their trade-offs:

**1) Imperative chaining**
- New pipelines require changes to the sensor DAG (imperative invocations of
  additional processing DAGs).
- This may be acceptable if we don’t expect to add many new processing pipelines
  before AIP-76 is released.

**2) Declarative chaining, with a static customer list**
- Each new customer must be manually added to the processing DAG.
- Operationally cleaner, since we already need to deploy the customer’s
  configuration/mappings for the processing DAG.
- The processing DAG will be modified more frequently than in (1), as we expect
  more customer onboardings than new processing pipelines.

**3) DAG Factory pattern with template macros**
- GPT suggested a workaround using a DAG Factory pattern and template macros.
- I haven’t explored this yet, and it doesn’t seem worth the effort if AIP-76
  is expected by September - I'd prefer (1) or (2) as interim solutions.

**4) Start building with Dagster**
- Dagster supports what we need today out of the box.
- We could start on Dagster and decide later whether to switch to Airflow.
- The main trade-off is its smaller ecosystem of prebuilt connectors.
- We'd likely need to manage some lower-level integrations (e.g., SQS, Kafka,
  various DBs/datastores) manually via Python libraries.
- Switching later would involve platform migration and retraining engineers who
  initially built on Dagster.

**5) Adopt Dagster as a long-term platform**
- There’s some risk that future requirements may not be fully supported by Dagster.
- That said, the same uncertainty applies to Airflow - we're simply betting
  more on its maturity and its backing within the Apache ecosystem.

----

#### **AIRFLOW VS. DAGSTER PROJECT BACKING**
- Astronomer drives over 60% of Airflow’s development - while that introduces
  some vendor risk, they recently raised a Series D round
- Dagster is backed by Elementl, with a $33M Series B from Sequoia, Index
  Ventures, and 8VC in 2023 - so, similar to Astronomer but smaller in scale

----

#### **AIRFLOW VS. DAGSTER FEATURE DISPARITY**

- Airflow lacks dynamic data partitioning across DAGs, which is a super-useful
  feature for a multi-tenant environment. There are workarounds, though.
  Dagster has dynamic data partitioning built in.
- Airflow delivered DAG versioning in the v3 release, while Dagster has this in
  development, calling it being "at a beta stage". We aren't sure whether this
  feature is important/relevant for our use-case.
- Dagster has a lineage graph for all the assets within the system in a single
  plane, making it easy to observe the situation with all the pipelines at a
  glance. Airflow has graphs only within the context of a DAG being looked at,
  showing some extra details on upstream and downstream dependencies, but no
  single place to view all at once.


#### **AIRFLOW VS. DAGSTER FEATURES DIFF**

**1) Real-time processing**
- Airflow has better built-in support for real-time processing (SQS, Kafka, ...)
  - _"Apache Airflow can connect to Apache Kafka - Airflow can be used to orchestrate
    data pipelines that include Kafka as a data source or destination. This is
    typically achieved by utilizing the Airflow provider for Kafka, which allows
    Airflow to interact with Kafka brokers and process data streams."_
  - _"Dagster can connect to Apache Kafka, though it's not a built-in, native
    integration. You can use Dagster's sensors to pull data from Kafka topics.
    You can also consider using Dagster Pipes as a workaround for consuming Kafka
    data in some scenarios."_
- Dagster doesn't currently have an integration for consuming Kafka data but it
  has been requested in [#11857](https://github.com/dagster-io/dagster/issues/11857).
  You could consider using [Dagster Pipes](https://docs.dagster.io/guides/build/external-pipelines)
  as an approximate solution, depending on your use case.
  [[source](https://github.com/dagster-io/dagster/discussions/21428)]
- WINNER: Airflow 3


**2) DAG Versioning**
- Airflow 3 has implemented DAG versioning, while Dagster has this in development,
  [feature is considered to be in a beta stage](https://docs.dagster.io/guides/build/assets/asset-versioning-and-caching)
  - Airflow 3 has only DAG versioning at a code-level, if I understood it correctly
    [[source](https://www.astronomer.io/docs/learn/airflow-dag-versioning/#:~:text=Airflow%203%20introduces%20two%20new,change%20since%20the%20last%20run.)]
  - Dagster aims at both code- and data-versioning (skipping runs if input data and
    code have not changed since the last run) [[source](https://docs.dagster.io/guides/build/assets/asset-versioning-and-caching)]
- _Note: This doesn't really affect us at this stage, there won't be deploys
  overnight when our batch ingestion will be running; it may affect us with
  real-time attendance processing once we'll have that, if those jobs will
  be complex multi-step DAGs._
- Winner: Airflow 3 _(with Dagster close, having the feature "in beta")_

**3) UI**
- Dagster offers a lineage/graph view for all the jobs and assets in the system,
  showing full dependency trees
- Airflow ofers a lineage view only in context of a selected DAG/asset, showing
  its up- and down-stream dependencies, but without full inside DAG details until
  switched to that dependency
- Airflow currently has some UI bugs, not showing tasks inside a DAG if a DAG
  version with syntax errors have been executed, and then fixed, it confuses
  Airflow completely. It is minor, probably will get fixed soon, but it's there.
- WINNER: Dagster _(although, this might as well be a personal preference)_

**4) Logs & Metrics**
- For shipping logs only, as both ran in containers log out to the _stdout_, a
  fluentd/fluentbit sidecar can be deployed for shipping logs to any of the
  fluent protocol enabled log aggregators, Datadog included
- For Datadog, apparently there are datadog agents for both Airflow and Dagster
  - _"To get Airflow logs into Datadog, you can leverage the Datadog Agent and its
    integration with Airflow. This involves configuring the Datadog Agent to collect
    logs from Airflow, either through the Airflow StatsD plugin or by configuring
    the Agent to collect logs directly from the Airflow logs folder. You can also
    use the Datadog Forwarder for AWS environments to collect logs from various
    AWS services, including those related to Airflow."_
  - _"To send Dagster logs to Datadog, you'll need to use the dagster-datadog
    library and configure your Datadog Agent to collect and forward logs. This
    involves installing the necessary packages, setting up the Datadog Agent, and
    integrating the library into your Dagster pipelines."_
- WINNER: _a tie_

----

#### **AIRFLOW VS. DAGSTER HOSTING**

1) Both, Airflow and Dagster, offer Docker Compose configuration, as well as
   the vendor-provided Helm Charts for easy saetup in a Docker Compose or a
   Kubernetes cluster for self-hosting, including ECS and EKS inside AWS.

2) Airflow will eventually be offered as Amazon Managed Workflows for Apache
   Airflow (MWAA) service(\*). Dagster doesn't have such an offer inside AWS.

3) Both, Airflow and Dagster, offer their own hosted cloud solutions (Dagster+
   and Astro, respectively) where the platform management is offloaded to their
   teams(\*\*).

(\*) NOTE: In a meeting with Astronomer, they told us that ~70% of their customers
are the runaways from MWAA service, due to the level of AWS support being the "add
more hardware" approahc only, whereas Astronomer can do some extra optimizations
inside their cloud to cut hardware cost. With that in mind, the benefits of MWAA
are limited to managed hardware configuration, while potential optimizations are
on our platform team, to a degree AWS makes it possible, if at all.

(\*\*) Astronomer confirmed their ability to be SOC-2 compliant, by hosting us
on a separate set of hardware, should we choose to go with their cloud. We
haven't explored Dagster's offering.

----

### **SUMMARY**

It's not a clear-cut winner here, with either tool there are trade-offs we need
to accept, at least temporarily. Dagster will probably keep winning at UI-level
presentation, while Airflow will probably stay ahead with built-in real-time
processing support, where with Dagster it's doable by leveraging workarounds
using sensors.

_Note: Airflow implementation of the built-in support **could** also be sensors
behind the scenes, so performance-wise it could be the same, with Airflow
providing a declarative way of setting it up)_.

#### CONCLUSION

Even if we choose one tool, and it turns out we'd need to switch, having that
fact in mind from start allows us to write the code as separate Python modules,
where DAGs serve only to call into those processing functions. That way, even
the switch wouldn't be too big of an undertaking. An option remains to keep
the prototyping going on in both tools in parallel, if it turns out it's not
too big of an overhead, which it seems the case.

I am aiming at achieving that.
