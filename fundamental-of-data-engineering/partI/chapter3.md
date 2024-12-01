### chapter 3. Designing Good Data Architecture
- *Data architecture* is a subset of *enterprise architecture*, which Enterprise architecture has many subsets, including *business*, *technical*, *application*,
and *data*
- A data architecture describes how data is managed, from *collection* through to *transformation*, *distribution* and *consumption*.
- *Enterprise architecture* is the **design** of systems to support *change* in the enterprise,
- *Data modeling* is the process of creating a visual representation of either a whole information system or parts of it to communicate connections between data points and structures. The goal of data modeling is to illustrate the types of data used and stored within the system, the relationships among these data types, the ways the data can be grouped and organized and its formats and attributes.
achieved by *flexible* and *reversible* decisions reached through careful *evaluation* of
trade-offs. so the keywords of enterprise arch are --> *change*, *alignment*, *organization*, *opportunities*, *problem-solving*, and *migration*.
- Managing such small actions is at the heart of
change management. it also relates to reversible descisions, where we can manage the changes
and chunk it to reversible small actions, so we can figure out the whole problem and manage the whole
change in reversible manner.
- Data engineering architecture is the systems and frameworks that make up the key sections of the data engineering lifecycle.
- **Operational architecture** encompasses the functional requirements of what needs to happen related to people, processes, and technology. For
example, what business processes does the data serve? How does the organization
manage data quality? What is the latency requirement from when the data is produced to when it becomes available to query?
- **Technical architecture** outlines how data
is *ingested*, *stored*, *transformed*, and *served* along the data engineering lifecycle. For
instance, how will you move 10 TB of data every hour from a source database to your
data lake?
- In short, operational architecture describes *what* needs to be done, and
technical architecture details *how* it will happen.
- *Architecture* represents the *significant* design decisions
that shape a system, where significant is measured by cost of *change*.
##### Principles of Good Data Architecture: "architecture principals"
- The AWS Well-Architected Framework consists of six pillars:
    - Operational Excellence Pillar: The operational excellence pillar focuses on running and monitoring systems, and continually improving processes and procedures. Key topics include automating changes, responding to events, and defining standards to manage daily operations.
    - Security Pillar: The security pillar focuses on protecting information and systems. Key topics include confidentiality and integrity of data, managing user permissions, and establishing controls to detect security events.
    - Reliability Pillar: The reliability pillar focuses on workloads performing their intended functions and how to recover quickly from failure to meet demands. Key topics include distributed system design, recovery planning, and adapting to changing requirements.
    - Performance Efficiency Pillar: The performance efficiency pillar focuses on structured and streamlined allocation of IT and computing resources. Key topics include selecting resource types and sizes optimized for workload requirements, monitoring performance, and maintaining efficiency as business needs evolve.
    - Cost Optimization Pillar: The cost optimization pillar focuses on avoiding unnecessary costs. Key topics include understanding spending over time and controlling fund allocation, selecting resources of the right type and quantity, and scaling to meet business needs without overspending.
    - Sustainability Pillar: The sustainability pillar focuses on minimizing the environmental impacts of running cloud workloads. Key topics include a shared responsibility model for sustainability, understanding impact, and maximizing utilization to minimize required resources and reduce downstream impacts. 
- One of the primary jobs of a data engineer is to choose common components and
practices that can be used widely across an organization. When architects choose
well and lead effectively, common components become a fabric facilitating team
collaboration and breaking down silos. Common components enable agility within
and across teams in conjunction with shared knowledge and skills. Common components include* object storage*, *version-control systems*,
*observability*, *monitoring* and *orchestration systems*, and *processing engines*.
- *Object storage*, often referred to as object-based storage, is a data storage architecture ideal for storing, archiving, backing up and managing high volumes of **static unstructured data**—reliably, efficiently and affordably. Objects (data) in an object-storage system are accessed via Application Programming Interfaces (*APIs*).
- **Serverless and Function as a Service (FaaS)**: Serverless code is invoked when an event occurs—such as when consuming from a Kafka topic. The primary concern is with the development of the application and not on the deployment, hosting, and infrastructure. These variables must still be considered to ensure sufficient processing capabilities but are minimal in comparison to traditional on-premises and self-hosted applications. Function as a Service (FaaS) solutions enable you to write code contained within a single function and register it as a listener on a specific Kafka topic. Upon consuming a new event, the FaaS provider starts up a function execution to process it and with any other events that may arrive during the function's execution window. FaaS providers traditionally pair this execution model with a “pay-as-you-go” billing model, making it even more attractive to workloads that run only intermittently.
- an architect’s job is to
develop deep knowledge of the *baseline architecture* (current state), develop a *target architecture*, and map out a *sequencing plan* to determine priorities and the order of
architecture changes.
- 