### Researching on high-level and low-level aspects of database internals and data management systems while reading top papers and books out there
##### I'll put some reference to the papers while reading the books through out the chapters and you'll find fine-tuned markdowns while navigating between book's chapters where I'll add my reasearch results between the notes and some good references to papers and blog posts that helps me better undrestand the concept.

###### ❗️although I'll try my best to extract the best and top-notch ideas of the books alongside the articles and best yt videos on that particular topic, but still highly recommend to read the books itself. cause you know, Authors know what are they doing❗️ (specially ddia ✅)

#### List of Books:

- <span style="color: #f2cf4a; font-family: Babas; font-size: 2em;">Desinging Data Intensive Application by Martin kleppman</span>

Content is broken down into 3 sections and 12 chapters: (1) foundations of data systems, which covers reliable, scalable, and maintainable applications, data models and query languages, storage and retrieval, and encoding and evolution, (2) distributed data, which covers replication, partitioning, transactions, the trouble with distributed systems, and consistency and consensus, and (3) derived data, which covers batch processing, stream processing, and the future of data systems. The latter 6 chapters are weighted more heavily, with chapter 9 on consistency and consensus, and chapter 12 on the future of data systems, the most lengthy with each comprising about 12% of the book.
Some potential readers might be disappointed that this book is all theory, but while the author does not provide any code he discusses practical implementation and specific details when applicable for comparisons within a product category. the last chapter is probably the most abstract simply because it explores ideas about how the tools covered in the prior two chapters might be used in the future to build reliable, scalable, and maintainable applications. Similiary, the chapter on the opposite end of this book sets the stage well for any developer of nontrivial applications with its section on thinking about database systems and the concerns around reliability, scalability, and maintainability.
Kleppman mentions that we typically think of databases, message brokers, caches, etc as residing in very different categories of tooling because each of these has very different access patterns, meaning different performance characteristics and therefore different implementations. So why should all of this tooling not be lumped together under an umbrella term such as 'data systems'? Many products for data storage and processing have emerged in recent years, optimized for a variety of use cases and no longer neatly fitting into traditional categories: the boundaries between categories are simply becoming blurred, and since a single tool can no longer satisfy the data processing and storage needs for many applications, work is broken down into tasks that can be performed efficiently on a single system that is often comprised of different tooling stitched together by application code under the covers. -EG



- <span style="color: #f2cf4a; font-family: Babas; font-size: 2em;">Database Internals by Alex Petrov</span>



###### blog posts and source code links:
- [Free learning platform for better future](https://www.javatpoint.com/dbms-tutorial)
- [Practice SQL with cool challenges](https://www.hackerrank.com/domains/sql)
- 