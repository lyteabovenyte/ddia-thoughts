### chapter 2. The Data Engineering Lifecycle

- small serivce/database pattern in microservice design rather than monolith design.
- For stateful systems (e.g., a database tracking customer account information), is
data provided as *periodic snapshots* or update events from *change data capture*
(**CDC**)?
- schemaless and fixed schema:
    - Schemaless doesn't mean the absence of schema. Rather, it means that the application
defines the schema as data is written, whether to a message queue, a flat file, a blob, or
a document database such as MongoDB. A more traditional model built on relational
database storage uses a fixed schema enforced in the database, to which application
writes must conform.
- Are you capturing **metadata about schema evolution**, data flows, data lineage,
and so forth? Metadata has a significant impact on the utility of data. Metadata represents an investment in the future, dramatically enhancing discoverability and institutional knowledge to streamline future projects and architecture
changes.
- two different kind of data ingestion formats: **batch** versus **streaming** and **push** versus **pull**.
- CDC with *pull* method on *Binary log* --> timestamp-based CDC: an ingestion system queries the source database
and pulls the rows that have changed since the previous update
- Access controls are critical but not particularly complicated.
Access is managed using a handful of roles and access tiers.
- Apply tenant- or data-level
security within your storage and anywhere there’s a possibility of data leakage.
- (**multitenancy**)any current storage and analytics systems support multitenancy in various ways.
Data engineers may choose to house data for many customers in common tables to
allow a unified view for internal analytics and ML. This data is presented externally
to individual customers through logical views with appropriately defined controls and
filters. It is incumbent on data engineers to understand the minutiae of multitenancy
in the systems they deploy to ensure absolute data security and isolation.
- maintaining feature history and versions and data engineers are part of the core support team for feature
stores to support ML engineering.
- reverse ETL allows us to take analytics, scored models, etc., and feed these back into
production systems or SaaS platforms.
- The jury is out on whether the term reverse ETL will stick. And the practice may
evolve. Some engineers claim that we can eliminate reverse ETL by handling data
transformations in an event stream and sending those events back to source systems
as needed. Realizing widespread adoption of this pattern across businesses is another
matter. The gist is that transformed data will need to be returned to source systems
in some manner, ideally with the correct lineage and business process associated with
the source system.
- Data engineers must understand both data and access security, exercising the principle of least privilege
- Data security is also about *timing* —providing data access to exactly the people and
systems that need to access it and only for the duration necessary to perform their
work.
- Data should be protected from unwanted visibility, both in flight and at rest, by
using **encryption, tokenization, data masking, obfuscation, and simple, robust access controls**
- Data engineers must be competent security administrators, as security falls in their
domain. A data engineer should understand security best practices for the cloud and
on prem. Knowledge of user and **identity access management (IAM) roles, policies, groups, network security, password policies, and encryption** are good places to start.
- Data governance, including discoverability and accountability •
• Data modeling and design •
• Data lineage •
• Storage and operations •
• Data integration and interoperability •
• Data lifecycle management •
• Data systems for advanced analytics and ML •
• Ethics and privacy
- the core categories of data governance are discoverability, security, and accountability. Within these core categories are subcategories, such as data quality, metadata, and privacy. 
- **Metadata** is “data about data,” and it underpins every section of the data
engineering lifecycle. Metadata is exactly the data needed to make data discoverable
and governable. Metadata is the information that describes the attributes and properties of your objects, such as name, size, type, creation date, owner, tags, and permissions.
Metadata helps you organize, search, and manage your objects more efficiently and securely. 
For example, you can use metadata to filter and sort your objects by category, 
date, or size; to apply encryption, compression, or lifecycle policies;
or to control access and authentication.
- DMBOK identifies four main categories of metadata that are useful to data engineers:
    - Business metadata: Business metadata provides a data engineer with the right context and definitions to properly use data.
    - Technical metadata: describes the data created and used by systems across the data engineering lifecycle. It includes the data model and schema, data lineage, field mappings, and pipeline workflows.
        - pipeline metadata
        - data lineage
        - schema metadata
    - Operational metadata: Operational metadata describes the operational results of various systems and
    includes statistics about processes, job IDs, application runtime logs, data used in
    a process, and error logs. A data engineer uses operational metadata to determine
    whether a process succeeded or failed and the data involved in the process.
    - Reference metadata: Reference metadata is data used to classify other data. This is also referred to as **lookup**
    data. Standard examples of reference data are internal codes, geographic codes, units
    of measurement, and internal calendar standards.
- [learn more about metadata-driven data pipeline](https://www.linkedin.com/pulse/how-metadata-drives-data-pipeline-development-rotimi-r-ademola/)
- **Orchestration** is a central hub that coordinates workflow across various systems. Pipeline metadata captured in orchestration systems provides details of the workflow
schedule, system and data dependencies, configurations, connection details, and much more.
- Data-lineage metadata tracks the origin and changes to data, and its dependencies,
over time. As data flows through the data engineering lifecycle, it evolves through
transformations and combinations with other data. Data lineage provides an audit
trail of data's evolution as it moves through various systems and workflows.
- Schema metadata describes the *structure* of data stored in a system such as a database,
a data warehouse, a data lake, or a filesystem; it is one of the key differentiators
across different storage systems. Object stores, for example, don't manage schema
metadata; instead, this must be managed in a **metastore**. On the other hand, cloud
data warehouses manage schema metadata internally
- [How do you manage metadata and versioning in object storage?](https://www.linkedin.com/advice/0/how-do-you-manage-metadata-versioning-object-storage)
- A data engineer ensures **data quality** across the entire data engineering lifecycle. This
involves performing data-quality tests, and ensuring data conformance to schema
expectations, data completeness, and precision.
  - *Accuracy*: Is the collected data factually correct? Are there duplicate values? Are the
    numeric values accurate?
  - *Completeness*: Are the records complete? Do all required fields contain valid values?
  - *Timeliness*: Are records available in a timely fashion?
- *Master data* is data about business entities such as employees, customers, products,
and locations. Master data management (MDM) is the practice of building consistent entity definitions known as golden records.
Golden records harmonize entity data across an
organization and with its partners. MDM is a business operations process facilitated
by building and deploying technology tools. For example, an MDM team might
determine a standard format for addresses, and then work with data engineers to
build an API to return consistent addresses and a system that uses address data to
match customer records across company divisions.
- To derive business insights from data, through business analytics and data science,
the data must be in a usable form. The process for converting data into a usable
form is known as **data modeling and design**. Data processing frameworks
such as Spark can ingest a whole spectrum of data, from flat structured relational
records to raw unstructured text.
- As data moves through its lifecycle, how do you know what system affected the data
or what the data is composed of as it gets passed around and transformed? **Data lineage** describes 
the recording of an audit trail of data through its lifecycle, tracking
both the systems that process the data and the upstream data it depends on.
- [Data Observability Driven Development | The perfect analogy](https://www.kensu.io/blog/a-guide-to-understanding-data-observability-driven-development)
- *Data integration and interoperability* is the process of integrating data across tools and
processes.
- **integration** happens through general-purpose APIs rather than custom
database connections. For example, a data pipeline might pull data from the Salesforce API, 
store it to Amazon S3, call the *Snowflake API* to load it into a table, call the
API again to run a query, and then export the results to S3 where *Spark* can consume
them.
- Whereas *DevOps* aims to improve the release and quality
of software products, *DataOps* does the same thing for data products.
- DataOps is a collection of technical practices, workflows, cultural norms, and architectural patterns that enable:
    - Rapid innovation and experimentation delivering new insights to customers with increasing velocity
    - Extremely high data quality and very low error rates
    - Collaboration across complex arrays of people, technology, and environments
    - Clear measurement, monitoring, and transparency of results
- in DataOps We suggest first starting with *observability* and *monitoring* to get a
window into the performance of a system, then adding in *automation* and *incident response*
- DataOps has three core technical elements: *automation*, *monitoring and observability*, and *incident response*
- Modern-day infrastructures thrive on distributed environments. **Observability** refers to understanding the current state of a
system or application with cumulative information gathered from its components. Observability often focuses on monitoring the system or application as a whole rather than governing the elements individually. A fully observable environment will have in-depth visibility into your network stack
and will help you keep the network infrastructure in its optimal working condition at all times.
- *Automation* enables reliability and consistency in the DataOps process and allows
data engineers to quickly deploy new product features and improvements to existing
workflows. DataOps automation has a similar framework and workflow to DevOps,
consisting of change management (environment, code, and data version control),
continuous integration/continuous deployment (CI/CD), and configuration as code.
Like DevOps, DataOps practices monitor and maintain the reliability of technology
and systems (data pipelines, orchestration, etc.), with the added dimension of checking 
for data quality, data/model drift, metadata integrity, and more.
- As the organization's data maturity grows, data engineers will typically adopt an
orchestration framework, perhaps Airflow or Dagster. Data engineers are aware that
Airflow presents an operational burden, but the benefits of orchestration eventually
outweigh the complexity. Engineers will gradually migrate their cron jobs to Airflow
jobs. Now, dependencies are checked before jobs run. More transformation jobs can
be packed into a given time because each job can start as soon as upstream data is
ready rather than at a fixed, predetermined time. The data engineering team still has room for operational improvements. A data
scientist eventually deploys a broken DAG, bringing down the Airflow web server
and leaving the data team operationally blind. After enough such headaches, the
data engineering team members realize that they need to stop allowing manual DAG
deployments. In their next phase of operational maturity, they adopt automated
DAG deployment. DAGs are tested before deployment, and monitoring processes
ensure that the new DAGs start running properly. In addition, data engineers block
the deployment of new Python dependencies until installation is validated. After
automation is adopted, the data team is much happier and experiences far fewer
headaches.
- a framework that builds DAGs based on the data lineage specification. --> how should this framework be implemented.
- _data architecture_ is an undercurrent of the data engineering lifecycle.
- _Orchestration_ is the process of coordinating many jobs to run as quickly and efficiently 
as possible on a scheduled cadence. For instance, people often refer to
orchestration tools like Apache Airflow as schedulers. This isn't quite accurate. A
pure scheduler, such as cron, is aware only of time; an orchestration engine builds
in metadata on job dependencies, generally in the form of a directed acyclic graph
- We must point out that orchestration is strictly a batch concept.
- data processing tasks such as joins that we take for granted in the
batch processing world often become more complicated in real time, requiring more
complex software engineering. Engineers must also write code to apply a variety
of **windowing** methods. **Windowing** allows real-time systems to calculate valuable
metrics such as trailing statistics. Engineers have many frameworks to choose from,
including various function platforms (OpenFaaS, AWS Lambda, Google Cloud Functions) 
for handling individual events or dedicated stream processors (Spark, Beam,
Flink, or Pulsar) for analyzing streams to support reporting and real-time actions.
- **Pipelines as code** is the core concept of present-day orchestration systems, which
touch every stage of the data engineering lifecycle. Data engineers use code (typically
Python) to declare data tasks and dependencies among them. The orchestration
engine interprets these instructions to run steps using available resources.

#### chatGPT summary:
Summary of Chapter 2: The Data Engineering Lifecycle

(Fundamentals of Data Engineering by Joe Reis and Matt Housley)

Overview

Chapter 2 introduces the Data Engineering Lifecycle, a framework designed to guide the systematic development of data systems. This lifecycle emphasizes designing scalable, maintainable, and high-quality data pipelines that deliver business value while managing complexity. The authors argue that engineering practices in data systems should be aligned with software development principles, balancing technical rigor and adaptability to business needs.

Key Concepts and Detailed Notes

1. Stages of the Data Engineering Lifecycle

The lifecycle is broken into six stages, each of which builds on the previous one:
	1.	Generation:
	•	The process begins with **data generation**, where data originates from various sources like logs, databases, APIs, IoT devices, or user activity.
	•	Important Considerations:
	•	Understanding the nature of data (structured, semi-structured, unstructured).
	•	Evaluating data fidelity (completeness and granularity).
	•	Identifying data velocity (real-time vs. batch).
	•	Personal Note: Think of generation as the **“raw material”** stage of a pipeline. A strong understanding of source systems is crucial to build robust ingestion systems later.
	2.	Ingestion:
	•	Refers to capturing data from its sources and transporting it to a data platform for processing.
	•	Tools like Kafka, Apache Nifi, AWS Kinesis, and Fivetran play a central role here.
	•	Techniques include batch ingestion (e.g., database snapshots) and streaming ingestion (e.g., event streams).
	•	Challenges: Managing schema evolution, handling data volume, and ensuring minimal latency for real-time systems.
	•	Personal Note: Ingestion pipelines should be **modular** to accommodate future changes, such as switching from batch to streaming ingestion.
	3.	Storage:
	•	Once ingested, data needs to be stored in a way that balances performance, scalability, and cost.
	•	Common storage paradigms:
	•	Data Lakes: Store raw, unprocessed data for flexibility (e.g., S3, Azure Blob Storage).
	•	Data Warehouses: Optimized for structured queries (e.g., Snowflake, BigQuery, Redshift).
	•	Data Lakehouses: A hybrid of lakes and warehouses (e.g., Databricks, Apache Iceberg).
	•	Key Principles:
	•	Partitioning: To enable faster retrieval.
	•	Compression: To save storage costs.
	•	Versioning: To support reproducibility.
	•	Personal Note: The choice of storage should align with the intended use case. Data lakes are more suitable for exploratory workloads, while warehouses are better for OLAP use cases.
	4.	Processing:
	•	Involves transforming raw data into meaningful, structured outputs.
	•	Data processing systems can be categorized as:
	•	Batch Processing: Periodic processing of large datasets (e.g., Apache Spark, Hadoop).
	•	Stream Processing: Real-time processing of data as it arrives (e.g., Apache Flink, Kafka Streams).
	•	Best Practices:
	•	Design idempotent pipelines to handle failures gracefully.
	•	Ensure lineage tracking for debugging and compliance.
	•	Use frameworks like dbt for transformations within warehouses.
	•	Personal Note: Build processing pipelines to be as declarative as possible. Avoid unnecessary complexity by focusing on modular, reusable transformations.
	5.	Serving:
	•	Focused on making processed data available to end-users or systems for decision-making or analysis.
	•	Approaches:
	•	BI Tools and Dashboards: Tableau, PowerBI, Looker for business users.
	•	APIs: Exposing data to applications or external systems.
	•	Search Systems: Elasticsearch, for quick data retrieval.
	•	Key Considerations:
	•	Latency: Is the use case real-time or near-real-time?
	•	Availability: Ensuring fault-tolerant and highly available systems.
	•	Scalability: Ability to handle growing workloads.
	•	Personal Note: Efficient serving layers require pre-computed aggregations or materialized views when querying raw data would be too slow.
	6.	Consumption:
	•	The final stage focuses on how stakeholders use the data.
	•	Common use cases include:
	•	Business Intelligence (BI) reporting.
	•	Machine Learning model training and predictions.
	•	Data-driven application features (e.g., recommendation systems).
	•	The success of this stage depends on ensuring data quality, documentation, and accessibility.
	•	Personal Note: Collaboration with data consumers during design ensures that the delivered solution meets actual business needs.

2. Principles of the Data Engineering Lifecycle

The authors propose several guiding principles:
	1.	Focus on Business Value:
	•	Data engineering is not just about technology; it’s about enabling the business to make informed decisions.
	•	Efforts should prioritize projects that directly impact business outcomes.
	2.	Iterative Development:
	•	Adopt incremental changes to reduce risk.
	•	Use practices like version control and CI/CD pipelines to build, test, and deploy pipelines efficiently.
	3.	End-to-End Ownership:
	•	Data engineers should understand the entire lifecycle to ensure consistency.
	•	Data systems are most effective when they are holistically designed rather than optimized in isolation.
	4.	Automation First:
	•	Automate repetitive tasks such as testing, deployment, and monitoring.
	•	Use infrastructure-as-code tools like Terraform for provisioning.
	5.	Scalability and Flexibility:
	•	Design systems to handle growth in data volume, velocity, and variety.
	•	Avoid overengineering; start small and scale as needed.

3. Common Pitfalls in Data Engineering
	•	Over-optimization:
Spending excessive time building pipelines or systems for hypothetical scenarios that may never arise.
	•	Personal Note: Always balance between “good enough” and “future-proofing.”
	•	Neglecting Data Quality:
Poor data quality impacts every downstream process, leading to mistrust in the system.
	•	Lack of Documentation:
Without proper documentation, onboarding new engineers or troubleshooting becomes unnecessarily complex.
	•	Siloed Workflows:
Teams working in isolation create inefficiencies and misaligned priorities.

My Reflections and Key Takeaways

Chapter 2 establishes a solid foundation by framing data engineering as a cohesive process that integrates technical and business objectives. The lifecycle serves as a blueprint to break down the overwhelming complexity of modern data systems. By emphasizing end-to-end thinking and iterative improvement, the authors encourage engineers to focus on creating practical, scalable solutions.

Final Thought:

The lifecycle is not linear; it’s a feedback loop. Failures and insights from the consumption stage can inform improvements in earlier stages. Continuously refine systems to ensure they stay aligned with evolving business goals.