# Why a bigger connection pool usually makes things worse

When requests start timing out, the first instinct is to raise the pool size. The result is often more
timeouts, not fewer, and the reason is worth understanding.

A database can only execute a limited number of queries at once, and that limit is well below the number
of connections a typical application opens. Connections beyond that limit do not add throughput; they
queue inside the database, holding a connection while doing so. Raising the pool therefore moves the
queue, it does not shorten it.

The damage is subtler than a longer queue. Each waiting connection occupies memory on both the
application and database side, and under load the database spends an increasing share of its time
switching between active queries instead of executing them. Past the point where useful parallelism is
saturated, added connections reduce total throughput.

Because a request holds its connection for its entire lifetime, including the time it spends waiting on
slow upstream calls, the pool is exhausted by slow requests rather than by high request volume. A pool
of 20 connections can serve thousands of requests per second if each request holds its connection for
two milliseconds, and can be exhausted by a handful of concurrent requests if each holds one for ten
seconds.

The practical consequence is that pool size should follow the database's useful parallelism, not the
request rate. Once the pool sits near that number, the lever that improves results is shortening how
long a request holds a connection, by bounding timeouts and moving slow non-database work outside the
connection's lifetime.
