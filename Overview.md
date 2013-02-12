In most applications there is some amount of data that will be frequently used. Some of this data is transient and can be recalculated, while other data will need to be fetched from the database or a middle tier service. In the Netflix cloud architecture we use caching extensively to offset some of these operations.

# Why use Caching?
The advantages of caching are: 
* Faster response time compared to data being fetched from source/database
* Reduces the number of servers needed to handle the requests

