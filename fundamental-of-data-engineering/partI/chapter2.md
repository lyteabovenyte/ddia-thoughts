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
- 