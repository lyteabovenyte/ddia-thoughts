#### worth researching topics:

#### chapter 1:
the fundamental building block of a data-intensive application.
- allow users to search data by keyword or filter it in various ways. (search indexes)
- send a message to another process to handle it asynchronously (stream processing)

hardware faults:
- disks may be configured on RAID configuration. --> what exactly is RAID configuraion?


##### latency and response time:
- random additional latency could be introduced by a **context switch**? to a background process.
A context switch is the process of storing the state of a process or thread, so that it can be restored and resume execution at a later point.[more on context switching](https://www.techtarget.com/whatis/definition/context-switch)

- **garbage collection**? pause

- calculating response time percentile on an **ongoing basis** for monitoring and metrics?


##### scalibility
- what should we do when in our application, the volume of write are high? how should we design our system to cop that?
- what about read? --> caching on writes and move the load from read to write at early stage
- what about data complexity?
- what about the amount of the data?
- what about the access patterns?


##### operability
- operational model? ( if I do X, Y will happen) on documentation or alongside it


##### complexity
- various possible symptoms of complexity:
    - explosion of the state space**??**

- what is TTD (test driven development) --> surface knowledge is not enough. research more deeply.
Agile community created TTD that is helpful when developing software in a frequently changing environment.

- what is refactoring
#### Query language for data:
- declarative languages often lend themselves to parallel execution. --> how can that be achieved?
- 