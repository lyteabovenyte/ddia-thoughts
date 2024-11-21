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
- 