# Airflow and Dagster Concepts

## Processing Units

To undrestand the concepts, building blocks, and terminology of both Airflow
and Dagster, here's a brief overview. Both tools offer building processing
pipelines by using Assets, and DAGs with Tasks in case of Airflow; or Assets,
Jobs, and Ops (operations) in case of Dagster. On the surface, these elements
seem quite similar, but their composition is a bit different in each of the tools.

### Airflow

In Airflow, DAGs are processing units that consist of Tasks, while Assets are
standalone processing units on the surface. Still, Asset is not a completely
separate unit. Internally, Asset is simply a special-case DAG, composed of
an exactly one Task with one Output.


### Dagster

Dagster has a somewhat different approach, its processing units are Jobs, and
there are two types of jobs - [Op Jobs](https://docs.dagster.io/guides/build/jobs/op-jobs),
and [Asset Jobs](https://docs.dagster.io/guides/build/jobs/asset-jobs). Note
that, in both cases, Jobs are the main unit of execution in Dagster. Both,
Ops Jobs and Asset Jobs, can contain multiple Ops/Assets inside them.

#### Cross-job triggering with Ops Jobs

Dagster does not support job-to-job dependencies directly, you handle this
using automation tools like sensors, schedules, or RunRequest chaining.

- Using a Sensor to trigger the second job after the first is the most robust
  and idiomatic Dagster way.
- If you're orchestrating outside Dagster (e.g., in a CI/CD pipeline or a
  wrapper script), you can trigger job B at the end of job A using
  dagster_graphql or automation.
- Using a `RunRequest` inside an op is a bit of a hack: have the final op in
  `job_a` emit a `RunRequest` to start `job_b`. This requires using
  `dagster._core.definitions.run_request.RunRequest` internally and may not
  be future-proof.


#### Cross-job triggering with Asset Jobs

When having two dependent asset jobs, you can trigger the second asset job
based on the completion of the first asset, but Dagster does not natively
support inter-job dependencies (i.e., one job triggering another) in the
same declarative way as it does for assets within a single job.

However, you can achieve this behavior using one of the following approaches:
- Recommended Use a Sensor or Schedule for Chaining Jobs
- Combine into a Multi-Asset Job
- Experimental: Trigger second job programmatically

#### A Few More Details on Dagster Ops Jobs

- Ops jobs aren't drawin in the lineage graph - I guess one of the reasons why
  the preferred way in Dagster is using Asset Jobs
- With Ops Jobs, there's no support for direct Ops dependencies assignment,
  so triggering of the next Op Job must happen via a Sensor, and is delayed
  till the next Sensor check kicks in
- With Asset Jobs, dependencies can be assigned directly between assets, and
  assets within the same job are triggered instantly; cross-job assets still
  require sensors for triggering the downstream asset when conditions are met.



### Processing Units Registration

Dagster requires an explicit registration of all the Sensors, Jobs, Assets,
Resources, etc. through instantiation of `Definition` objects and registering
them in the Dagster Workspace (`dagster_workspace/workspace.yaml`). Resources
are registered through code and the mentioned Definition objects, for the
resources types having an integration support with Dagster _(as for the rest,
I'd assume the dependencies are added and instantiated like in standard Python
applications, but can't be registered as a Resource in Dagster for easy access)_.

Airflow, on the other hand, scans all the files in the `dags/` directory for
code files, and registers any found DAGs automatically. Resources are defined
via the UI and stored in the metadata database _(although, it seems like they
could be registered through code, I'd need to research that detail a bit more)._
