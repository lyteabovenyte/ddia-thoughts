## chapter 9

### Consistency and Consnsus

***Linearizability***: the illusion of having just one copy of the data, even though they are replicated, by using *compare-and-set* or *linearzable read and write* to ensure that whenever a user saw the new value, there is no chance that others see the stale value. by contrast, linearizibility is a recency guarantee: a read is gurarnteed to see the latest value written.

#### intro:
- 