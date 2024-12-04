#### The Data Warehouse Toolkit by Ralph Kimball & Margy Ross
###### notes on Introduction of the book:
- Our job is to *marshal* an organization’s data and bring it to business users for their decision making
- dimensionally modeled framework
- When you use the conformed dimensions and conformed facts of a set of dimensional models, you have a practical and predictable
framework for incrementally building complex DW/BI systems that are inherently
distributed.
- more on dimensional data modeling:
    - To build a dimensional database, you start with a dimensional data model. The dimensional data model provides a method for making databases simple and understandable. You can conceive of a dimensional database as a database cube of three or four dimensions where users can access a slice of the database along any of its dimensions. To create a dimensional database, you need a model that lets you visualize the data. Another name for the dimensional model is the **star schema**. The database designers use this name because the diagram for this model looks like a star with one central table around which a set of other tables are displayed. The central table is the only table in the schema with multiple joins connecting it to all the other tables. This central table is called the **fact table** and the other tables are called **dimension tables**. The dimension tables all have only a single join that attaches them to the fact table, regardless of the query.
- Concepts such as *conformed dimensions, slowly changing dimensions, heterogeneous products, factless fact tables, and the enterprise data warehouse bus matrix* continue to be discussed in design workshops around the globe.
- *three* fundamental types of fact tables: *transaction*, *periodic snapshot*, and *accumulating snapshot*
- 