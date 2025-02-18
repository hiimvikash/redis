# 1. What Problem does it solve ?
![image](https://github.com/user-attachments/assets/c47f568f-2b15-4507-b316-24cc93fe6bbf)

# 2. Installation of Redis.
- `docker run -d --name redis-stack -p 6379:6379 -p 8001:8001 redis/redis-stack:latest`
- Here we are running our redis in docker.
- PORT `6379` is where our actual redis server is running.
- PORT `8001` is where our redis GUI is running.

# 3. Interaction with redis-cli.
- run `docker exec -it 3af54f82e390 redis-cli` to switch to redis-cli.
- to test run `PING` you will get `PONG`.
- Now you're connected with your redis server.

# 4. Redis Strings.
## 1️⃣ What are Redis Strings?
Redis strings store sequences of bytes (text, serialized objects, binary arrays, images, etc.).
They are the simplest value type in Redis and are often used for caching.
Keys in Redis are also strings, so using string values means mapping one string to another.
### What can be stored in Redis Strings?
- ✔ Text ("hello world")
- ✔ Numbers ("100", "3.14")
- ✔ Serialized Objects (JSON, XML, etc.) : `JSON.stringify()` converts a JavaScript object into a string representation.
- ✔ Binary Data (Images, audio, videos, etc.)
## 2️⃣ Basic Redis Commands for Strings
### 🔹 SET & GET – Storing and Retrieving Values

```redis
SET key value
GET key
```
✔ Example:
```redis
SET bike:1 Deimos
GET bike:1
```
📌 Explanation:

- SET assigns a value (Deimos) to a key (bike:1).
- GET retrieves the value associated with the key.
- **⚠️Important:** If the key already exists, SET will replace the existing value.

### 🔹 SET with NX and XX Options
```redis
SET key value NX   # Set only if key does NOT exist
SET key value XX   # Set only if key ALREADY exists
```
✔ Example:
```redis
SET bike:1 "bike" NX  # Won't set if bike:1 already exists
SET bike:1 "bike" XX  # Will only set if bike:1 exists
```
📌 Explanation:

- NX (No eXist) ensures the key is only set if it doesn’t exist.
- XX (eXist) ensures the key is only set if it already exists.
### 🔹 MSET & MGET – Setting and Retrieving Multiple Keys at Once
```redis
MSET key1 value1 key2 value2 key3 value3
MGET key1 key2 key3
```
✔ Example:
```redis
MSET bike:1 "Deimos" bike:2 "Ares" bike:3 "Vanth"
MGET bike:1 bike:2 bike:3
```
📌 Explanation:

- MSET allows setting multiple key-value pairs in one command.
- MGET retrieves multiple values at once, reducing latency.
## 3️⃣ Strings as Counters (Atomic Operations)
### 🔹INCR & INCRBY – Incrementing Counters

```redis
INCR key          # Increments value by 1
INCRBY key N      # Increments value by N
```
✔ Example:

```redis
SET total_crashes 0
INCR total_crashes    # 1
INCRBY total_crashes 10  # 11
```
📌 Explanation:

- INCR converts the string into an integer and increases it.
- INCRBY allows incrementing by any number.
- This operation is atomic, meaning multiple clients won't cause race conditions.
### 🔹 DECR & DECRBY – Decrementing Counters
```redis
DECR key          # Decrements value by 1
DECRBY key N      # Decrements value by N
```
✔ Example:
```redis
DECR stock_count
DECRBY stock_count 5
```
📌 Explanation:
- Works similar to INCR, but decreases values.
## 4️⃣ Advanced String Commands
🔹 GETSET – Updating a Value While Retrieving the Old One
```redis
GETSET key new_value
```
✔ Example:
```redis
SET visitor_count 100
GETSET visitor_count 0   # Returns 100 and sets visitor_count to 0
```
- Useful for resetting counters while capturing the old value.
## 5️⃣ Limits and Performance Considerations
- ✔ Maximum string size: 512 MB
- ✔ Most string operations are O(1) (constant time), making them fast.
- ✔ Be careful with random-access commands like SUBSTR, GETRANGE, SETRANGE, as they can be O(n) and cause performance issues with large strings.




