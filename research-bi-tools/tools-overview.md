### Overviews Included

* [Apache Superset](#apache-superset) - A modern, enterprise-ready business intelligence web application
* [Cube](#cube) - Agentic Analytics Platform
* [Holistics](#holistics) - Self-service BI Platform
* [Qlik Sense](#qlik-sense)
* [Metabase](#metabase) - Open source Business Intelligence and Embedded Analytics
* [Omni Analytics](#omni-analytics) - Business intelligence that speaks your language
* [Lightdash](#lightdash) - The BI platform built for dbt
* [Datastripes](#datastripes) - Squeeze insights from your data


#### First Pass Shortlist

* Based on the first pass, the tools that look interesting to explore further are Apache Superset, Holistics, Metabase
* Potentially worth chekcing out are Omni Analytics, if it really delivers on their promises, and is not cost-prohibitive; and Lightdash if we end up leveraging dbt

### A Few Reddit Observations ([Source](https://www.reddit.com/r/BusinessIntelligence/comments/11illde/between_the_three_which_bi_tool_do_you_prefer_and/))

#### Metabase

* <...> I would say start with metabase. It's the simplest to use & get started on. If you hit any limitations then I would look into Superset & Lightdash, as they're a bit more powerful/technical, but also can be harder to use.
* Metabase might be a great fit since it offers a free, open-source version that’s easy to set up and doesn’t require a subscription.

#### Lightdash

* Lightdash is the newest of the bunch. It’s built on a semantic layer (so you pre-define metrics/dimensions as code), similar to Looker, and it’s entirely configured from dbt. It requires the least from your users, they just choose from a selection of metrics. But the data team need to invest in maintaining the semantic layer.
* In my previous company, I worked extensively with Looker, currently setting up the analytics department at a new company Lightdash it has been incredible. It's basically like looker so the transition is going to be easy. You do not pay per user, but instead a fixed service cost and they are really really good at customer service, if you have an issue they resolve it in hours not days. You could also potentially get it for free if you self-host. Only drawback its a bit slow sometimes
* very developer-first and works well if you’re deep into dbt. It’s open-source and has a clean interface, but setup can feel pretty technical unless you’re comfortable in YAML and Git.

#### Holistics

* sits somewhere in the middle. It’s good for teams that want SQL-first workflows + reusable data models, without needing a huge setup. You define metrics in code (kind of like Lightdash) but the UI is friendlier for less-technical users to explore or self-serve after. Works smoothly on Mac too since it’s fully browser-based.

#### Datastripes

* Among open-source options, Superset, Metabase, and Lightdash are solid, but if you’re looking for something lightweight and fast, you might also want to check out Datastripes.

#### Sigma

* cloud-native and spreadsheet-style interface, great if you like Excel/Sheets. It’s powerful but leans more toward enterprise teams, so pricing might still be a barrier (not 100% sure if they have a free tier now).


### Additional Resources

* [2024 Price Comparison Article](https://medium.com/@321K/how-much-do-bi-tools-cost-in-2024-5631a24f3b1d) including Metabase, Omni, Tableau, Looker, and Sigma

-----

## [Apache Superset](https://superset.apache.org/)

Superset can work with even gigantic databases! Superset acts as a thin layer above your underlying databases or data engines, which do all the processing. Superset simply visualizes the results of the query. The key to achieving acceptable performance in Superset is whether your database can execute queries and return results at a speed that is acceptable to your users. If you experience slow performance with Superset, benchmark and tune your data warehouse.

#### General technical requirements

The specs of your Superset installation depend on how many users you have and what their activity is, not on the size of your data. Superset admins in the community have reported 8GB RAM, 2vCPUs as adequate to run a moderately-sized instance. Superset's application metadata does not require a very large database to store it, though the log file grows over time. Note that Superset usage has a tendency to occur in spikes, e.g., if everyone in a meeting loads the same dashboard at once. Monitor your resource usage and increase or decrease as needed.

Installation is possible through Docker Compose, Kubernetes, or PyPI directly on bare-metal. This provides the required flexibility for a streamlined development experience and a seamless production installation.

#### Documentation

* [Intro & Getting Started](https://superset.apache.org/docs/6.0.0/intro/)
* [FAQ](https://superset.apache.org/docs/6.0.0/faq/)
* [Video Overview](https://github-production-user-asset-6210df.s3.amazonaws.com/812905/414943901-b37388f7-a971-409c-96a7-90c4e31322e6.webm?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAVCODYLSA53PQK4ZA%2F20250827%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20250827T115736Z&X-Amz-Expires=300&X-Amz-Signature=ca12e36bad298a66e6772a4a18cb7a53fe28ada9ab298cba1abddadf7c28be92&X-Amz-SignedHeaders=host)
* [API Docs](https://superset.apache.org/docs/api/)

#### Pricing

Licensed under the [Apache License](https://apache.org/licenses/), allowing for a commercial use at no cost.

#### Front End

With its no-code interface for building charts, and an additional web-based SQL editor for advanced querying, on a first glance it seems like it could easily fit into Joy's and Michael's workflow and project startlight requirements.

In addition to the above, Superset [offers an API](https://superset.apache.org/docs/api/) to integrate it programmatically into the custom built software solutions. The most important bit is support for dashboards and charts, which may provide the ready-made visualisations that can easily be integrated into the Project Starlight UI. **This is the functionality which should be explored as a priority to verify the assumption**, which if proven true, could easily make the Superset a platform of choice for us.


#### General Notes & Observations

Superset provides:

* A no-code interface for building charts quickly
* A powerful, web-based SQL Editor for advanced querying
* A lightweight semantic layer for quickly defining custom dimensions and metrics
* A wide array of beautiful visualizations to showcase your data, ranging from simple bar charts to geospatial visualizations
* Out of the box support for nearly any SQL database or data engine

For a database engine to be supported in Superset through the SQLAlchemy connector, it requires having a Python compliant SQLAlchemy dialect as well as a DBAPI driver defined. Database that have limited SQL support may work as well. Beyond the SQLAlchemy connector, it’s also possible, though much more involved, to extend Superset and write your own connector.


Q: Can I join / query multiple tables at one time? \
A: Not in the **Explore** or **Visualization UI**. A Superset SQLAlchemy datasource can only be a single table or a view. When working with tables, the solution would be to create a table that contains all the fields needed for your analysis, most likely through some scheduled batch process. However, if you are using **SQL Lab**, there is no such limitation. You can write SQL queries to join multiple tables as long as your database account has access to the tables.

-----

## [Cube](https://cube.dev/)

Cube is marketed as an AI-powered BI tool. More specifically, as an Agentic Analytics Platform, aiming at communicating a contemporary agentic approach to AI. It's built for the cloud data platform era, and has adopted **code-first worfklow** for everything, from configurations to data models _([source](https://cube.dev/docs/product/introduction))_.

**Cube Core** is the universal semantic layer, in open source under a permissive Apache License 2.0. **Cube Cloud** is the managed platform for Cube Core, developed by Cube Dev as a commercial product that includes everything from Cube Core and provides additional semantic layer features, managed infrastructure and workspace tools.

#### General technical requirements

* **Cube Core** - Docker-based deployment; each Cube cluster should contain at least 2 Cube API instances with at least **3GB of RAM** and **2 CPU cores** allocated for each.
* **Cube Cloud** - a SaaS cloud offering
* runs in browser, whether self-hosed or cloud-based

#### Documentation

* [Cube Documentation](https://cube.dev/docs/product/introduction)
* [Cube Core Deplopyment](https://cube.dev/docs/product/deployment/core)

#### Pricing

Cube is split into an open-source **Cube Core**, and the **Cube Cloud** SaaS offering.

* [Cube Core](https://cube.dev/product/cube-core)
* [Cube Cloud Pricing](https://cube.dev/pricing)
* [Core vs Cloud Feature Comparison Table](https://cube.dev/product/cube-core#compare-cube-core-vs-cube-cloud)

Cube Coud utilizes a SaaS pricing model, with either flexible **On-Demand** monthly billing, or a **Committed** billing invoiced upon contract signing following the payment terms defined in the order form, based on the chosen plan. Pricing is based on the notion of a Cube Consumption Unit (CCU), which _"precisely measures resource consumption used to run Cube Cloud infrastructure and resources within it"_. Its "pay for what you use" pricing strategy is kept in check by [setting budgets](https://cube.dev/docs/product/workspace/budgets).

The whole pricing model is not as clear to understand to project the costs up-front. Budget limitations in on-demand model seem to be limited to notifications only, rather than having an ability to put a hard-stop on spending in case things spiral out of control.

There is also a [free version of Cube Cloud](https://cube.dev/docs/product/deployment/cloud/limits) which includes **two developer** instances and **1k query limit per day**.

#### Front End

The Cube frontend is a web-based data modeling and query builder, aiming to build queries resulting in data analyses and/or reports. Based on a preliminary research, its also offers a reasoning LLM AI-augmented interface that helps in building the reports from the connected data sources. The built reports data is then exposed to the client applications through its REST or GraphQL APIs for further consumption and visualization.

#### General Notes & Observations

For integrations, Cube offers a [REST API](https://cube.dev/docs/product/apis-integrations/rest-api) and a [GraphQL API](https://cube.dev/docs/product/apis-integrations/graphql-api) enabling it to deliver data to the applications. At a first glance, its REST API seem less versatile than the Apache Superset.

Cube feels more like a middleware that connects to provided data sources, and provides data downstream to the visualization tools or custom applications, rather than a full-fledged analytics dashboard. It aims to replace some traditional BI and data analytics tools, but based on our conversations with Joy and Michael, we haven't heard them using any of such traditional tools where moving to a "cloud-era data platform" could yield some relief to their existing workflow.

Overall, where for example Apache Superset allows building full dashboards with data visualizations inside the tool itself, Cube requires building such visualizations dashboards as custom built applications. Pre-built analytics dashboards inside the analytics tool may be useful for internal use.

-----

## [Holistics](https://www.holistics.io/)

Holistics focuses on **self-service BI**. Analysts define business metrics and data logic once, and business users build their own reports and visualizations without writing SQL. Holistics automatically translates these actions into optimized queries. This approach empowers a small data team to serve the entire organization, freeing analysts to focus on complex work while most requests are answered directly through dashboards, reports, or drag-and-drop exploration.

#### General technical requirements

* a fully cloud-based BI platform

#### Documentation

* [Documentation](https://docs.holistics.io/docs)
* [1 min Introduction Video](https://www.youtube.com/watch?v=SpqazdqRThw) | [How Holistics works](https://docs.holistics.io/docs/how-holistics-works) | [Key Concepts](https://docs.holistics.io/docs/key-concepts) | [What makes Holistics different?](https://docs.holistics.io/docs/difference)
* [AMQL](https://docs.holistics.io/as-code/amql) - Analytics Modeling and Querying Language for analytics-as-code
* [Supported Visualizations in Holistics](https://docs.holistics.io/docs/visualizations)
* [Demo Sandbox](https://demo4.holistics.io/demo) - a preconfigured environment for demonstration purposes
* [**From Looker to Holistics**](https://docs.holistics.io/docs/from-others/looker/) - a high level comparison to help smoothen the transition for existing Looker customers to Holistics

#### Pricing

* https://www.holistics.io/pricing/

#### Front End

A browser-based frontend, combining visual analytics and report builders, and more advanced code-based modeling through AMQP and SQL querying. Flexible for a wide range of technical skills.

#### General Notes & Observations

Holistics is focused on a self-service data platform aiming to produce dashboards, charts, and reports, all within the same tool. Analysts have an option to drive their research in a more code-driven way through AMQL, SQL querying, and similar; while less-technical users may create reports and visualizations using drag-and-drop interface, creating dashboars, and analyzing the prepared data for their needs without a need to develop a separate application for that purpose.

Holistics offers [an extensive API](https://docs.holistics.io/api) exposing a lot of Holistic's functionality through the API, including the most important - data sets, reports, dashboards, but also working with data sources programmatically.

Should we need such power, Holistics seems to offer a lot in terms of both programmatic access and visualization capabilities inside the tool itself. It's somewhat similar to Looker, great for self-service, with a code-based semantic layer and a LookML alternative. The UI is friendly for less-technical users to explore or self-serve.

-----

## [Qlik Sense](https://www.qlik.com/us/products/qlik-sense)

**Qlik Cloud Analytics** brings all the power of Qlik analytics solutions in a cloud-based SaaS deployment. **Qlik Sense** is an on-premises solution for highly regulated industries and customers that prefer a client-managed offering.

Apparently, it supports the full range of analytics use cases - from self-service visualization and associative exploration to dashboards, embedded analytics, reporting, mobile, augmented analytics (AI), and more. Unfortunately, their product website is so convoluted that I don't think this is a product worth the effort.

#### General technical requirements

* Unknown - they don't really have transparent documentation where technical requirements are laid out
* [Qlik SenseDocumentation for Administrators](https://help.qlik.com/en-US/sense-admin/May2025/Content/Sense_Helpsites/Home-deployadminister.htm) - a place to start the research from, should we choose to...


#### Documentation

* [Get Started with Qlik Sense](https://help.qlik.com/en-US/onboarding/Content/Onboarding/home.htm) - On-boarding set of video tutorials
* [Qlik SenseDocumentation for Administrators](https://help.qlik.com/en-US/sense-admin/May2025/Content/Sense_Helpsites/Home-deployadminister.htm)
* [Qlik Sense on Windows Help for Developers](https://help.qlik.com/en-US/sense-developer/May2025/Content/Sense_Helpsites/Home-developer.htm)
* [Qlik Cloud Product Documentation](https://help.qlik.com/en-US/cloud-services/Content/Sense_Helpsites/Home.htm)
* [Qlik Training](https://www.qlik.com/us/services/training)

#### Pricing

* **Qlik Sense** pricing is on a **"contact us" basis**; it's also referred to as _Qlik Sense Enterprise_, which hints the direction...
* A [competitor article](https://www.holistics.io/blog/qlik-sense-pricing/) explaining Qlik's pricing model...
* [Qlik Cloud Analytics Plans and Pricing](https://www.qlik.com/us/pricing) - for comparison against Qlik Sense
* [Qlik Talend Cloud Plans and Pricing](https://www.qlik.com/us/pricing/data-integration-products-pricing)
* [AI/ML Product Pricing](https://www.qlik.com/us/pricing/ai-ml-products-pricing)

#### Front End

_Among the whole suite of different products, it's hard to find a proper demo of Qlik Sense. I'm not impressed by their overly complicated website to even spend extra effort digging information out, fighting against the product website to evaluate the product doesn't seem promising._

-----

## [Metabase](https://www.metabase.com/)

Metabase is a “Business intelligence” (BI) platform that provides a bunch of tools to understand and share data. Companies typically use Metabase to give their teams an easy way to query data, or to embed Metabase in their application to let customers explore data on their own. It offers Business Intelligence and Embedded Analytics.

#### General technical requirements

* [**Metabase OpenSource**](https://www.metabase.com/start/oss/) - it's either a Docker-based deployment, or running the `.jar` on bare-metal.
* [**Metabase Cloud**](https://www.metabase.com/cloud/) - a SaaS cloud offering

As Metabase is a Java product, hardware requirements for self-hosting are on a higher side. There are no specific minimal and recommended requirements, but a [load test comparison table](https://www.metabase.com/learn/metabase-basics/administration/administration-and-operation/metabase-in-production#example-load-test) is provided for customers to judge what they might need. To run Metabase in production, application data needs to be stored in a separate production-ready database like Postgres.

#### Documentation

* [Metabase Documentation](https://www.metabase.com/docs/latest/)
* [Metabase Concepts](https://www.metabase.com/learn/metabase-basics/overview/concepts)
* [A list of supported data sources](https://www.metabase.com/docs/latest/databases/connecting#connecting-to-supported-databases) | [Extra Community Drivers](https://www.metabase.com/docs/latest/developers-guide/community-drivers)
* [Looker to Metabase](https://www.metabase.com/learn/cheat-sheets/transition-guides/looker-to-metabase)

#### Pricing

* [Pricing page](https://www.metabase.com/pricing/?use_case=bi) with features comparison table
* Open-source is free for self-hosted installation supporting BI use-cases.
* Cloud pricing has several tiers of BI use, with an extra functionality of "Embedded Analytics" tiers allowing to include Metabase in the company's own products

#### Front End

The overall UI offers questions, dashboards, data collections, models, and metrics. Questions are saved queries with visualizations, charts to be added to dashboards. Models are like DB views, and Metrics define the official way to calculate important numbers.

Metabase offers a point-and-click visual query builder that allows querying data without knowing SQL, and a native query editor for people familiar with SQL (or another database's query language)

Native query editor allows writing complex queries and doing thing that might not be possible in the visual query builder. But, there is one drawback compared to the visual query builder - unlike the questions built with the query builder, people won’t be able to drill through the charts built with native query editor.

-----

## [Omni Analytics](https://omni.co/)


#### General technical requirements

n/a

#### Documentation

* [Documentation](https://docs.omni.co/docs)
* [User Guides](https://docs.omni.co/guides)
* [Omni in Action Video](https://www.youtube.com/watch?v=9GduXHYYGbU&t=1s) | More [Video Demos](https://omni.co/demos)
* [A list of supported data sources](https://docs.omni.co/docs/connections/database) | [Supported Integrations](https://omni.co/integrations)
* [A list of built-in visualizations](https://docs.omni.co/docs/visualization-and-dashboards/visualization-types)
* [Omni APIs](https://docs.omni.co/docs/API) - REST API available; no GraphQL

#### Pricing

* No public pricing, need to contact their Sales
* Even a free trial needs to go through the sales-channel, no publicly available demo available
* A [third party pricing report](https://medium.com/@321K/how-much-do-bi-tools-cost-in-2024-5631a24f3b1d) mentions $2,750/mo with annual commitments

#### Front End

A web-based frontend uses the notion of Workbooks with versioning. Data representation is both tabular and visual (charts). Workbooks can be presented as dashboards, displaying many queries at once with interactive filters. Each workbook query will be tiled onto a unified piece of content.

Data querying is visual through Pivot Table UI, and via SQL. It's also [AI-powered](https://omni.co/embedded-analytics).

[Modeling in Omni](https://docs.omni.co/docs/modeling/overview) is done in a layered approach. The system manages three core modeling layers: _(i)_ a schema model that mirrors the database; _(ii)_ a shared, virtualized data model for global business metrics and definitions; _(iii)_ a workbook model for ad hoc analysis that extends the core data models.

The Omni platform powers [embedded](https://docs.omni.co/docs/embed) dashboards and workbooks for internal use or external consumption. External embedding enables delivering data to users outside of the business, living in other applications. Reporting can be mapped to row level permissions, allowing many users to access identical reports filtered to only their own data.

#### General Notes & Observations

* The whole platform feels sales people driven, with hidden pricing and forcing all communciation through their sales. There are a few [Demos available via YouTube](https://omni.co/demos).
* There are reports of Omni's architecture being more flexible than Metabase, and offering a higher performance due to its caching mechanisms. In addition to that, it has apparently a less steep learning curve in comparison to Looker which we use, making it friendly to non-BI engineers.
* Apparently, a [highly rated](https://www.g2.com/products/omni-analytics-inc-omni-analytics/competitors/alternatives) piece of software among commercial offerings

-----

## [Lightdash](https://www.lightdash.com/)

The BI platform built for dbt.

Lightdash instantly turns dbt projects into a full stack BI platform - a powerful developer workflow lets analysts define key metrics, and enable self-serve for everyone else. Intuitive UI for the whole team to explore - no SQL needed.

Embed analytics wherever you need it - create beautiful customer-facing analytics for your users and embed them natively into your own application. Totally secure and configurable to your users' needs. Desktop and mobile support for embedded analytics.

#### General technical requirements

* Docker or K8s for self-hosting, or a cloud hosted solution option
* browser-based UI

#### Documentation

* [Documentation](https://docs.lightdash.com/introduction) with an introduction 5-min video
* [Video Guides](https://docs.lightdash.com/get-started/exploring-data/intro) | ["Lightdash University" on YouTube](https://www.youtube.com/channel/UCgVZeT_aqyW8ky4x-4xP_uA)
* [Demo app](https://demo.lightdash.com/)
* [SSO Setup](https://docs.lightdash.com/self-host/customize-deployment/use-sso-login-for-self-hosted-lightdash) - includes Okta, Google, One Login, Azure AD, OpenID Connect
* [Supported Chart Types](https://docs.lightdash.com/references/chart-types)
* [Embedding Quickstart](https://docs.lightdash.com/guides/how-to-embed-content) | [Embedding Reference](https://docs.lightdash.com/references/embedding)
* [Integration with dbt](https://docs.lightdash.com/references/dbt-projects)
* [React SDK](https://docs.lightdash.com/references/react-sdk) - Reference documentation for the Lightdash React SDK, used for Embedding
* [API Reference](https://docs.lightdash.com/api-reference/v1/introduction) - REST API offered

#### Pricing

* [Open Source](https://github.com/lightdash/lightdash) - [self-hosted](https://docs.lightdash.com/self-host/self-host-lightdash) via K8s or Docker Compose
* [Cloud Hosted](https://www.lightdash.com/pricing)
* [Self-hosting vs Lightdash Cloud](https://docs.lightdash.com/self-host/lightdash-cloud-vs-self-hosted)

#### Front End

* Offers a visual query builder, and an SQL query editor
* Offers data dashboards for visualizations and arranging multiple charts into a single view, and private/public spaces (query collections) to organize data
* Offers a catalogue of Metrics
* Seems lightweight and responsive, worth checking out their [public Demo](https://demo.lightdash.com/projects/2014e038-ff4b-4761-ae6f-fbf551e7b468/dashboards/665be298-b79e-456f-9ef3-03f9e068a2d0/view)

#### General Notes & Observations

Q: Do I need to use dbt to use Lightdash? \
A: Yes. Lightdash leverages your existing dbt project to build and govern your entire BI platform. Lightdash supports all versions of dbt: dbt Core, dbt Cloud and dbt Enterprise. If you don't have a fully fledged dbt project setup, you can still use Lightdash. Reach out to us to learn more!

Q: Is it really unlimited users? \
Yes. Lightdash was built on the idea that you shouldn't limit access to users having access to data. Being data practitioners and former buyers of BI tools, we know how frustrating it can be to have to deal with seat-based pricing limitations. Therefore, we're taking a different approach by giving teams the freedom to have as many users as they want and set their permissions accordingly.

If we embrace dbt, Lightdash seems like a natural choice for building reports and visualizations on top of it.

-----

## [Datastripes](https://datastripes.com/)

"Drop your CSV → Get instant data story. Instantly turn datasets into interactive, insightful, and visually compelling flows. Just drag, drop, and let your data reveal opportunities."

Q: What is Datastripes? \
A: A Ready‑made data Engine on your browser. Datastripes works like a visual data engine where you build flows using drag-and-drop nodes. It comes with 300+ customizable nodes, including machine learning blocks, data transformations, and advanced visualizations (maps, scatterplots, heatmaps, etc.). Everything runs locally in your browser.

This tool is still in development, and it feels like a lightweight tool for data drag'n'drop to visualize. Not sure if we're after such a tool
