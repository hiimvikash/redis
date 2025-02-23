# 1. What Problem does it solve ?
![image](https://github.com/user-attachments/assets/c47f568f-2b15-4507-b316-24cc93fe6bbf)
![image](https://github.com/user-attachments/assets/c428ae8c-d5a2-46df-864e-ec0b94f75d23)
Distributed caching.


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
## NodeJs perspective for same.
- Connect the client @ `./client.js`

```js
const {Redis} = require('ioredis');
const client = new Redis(6379); // if no port specified then it connects to 6379

// OR THIS :-
const client = new Redis({
  host: process.env.REDIS_HOST || '127.0.0.1',
  port: process.env.REDIS_PORT || 6379,
});

module.exports = {client}
```
- Use the client for specific operation.

```js
const client = require('./client');

const res1 = await client.set("bike:1", "Deimos");
console.log(res1);  // OK
const res2 = await client.get("bike:1");
console.log(res2);  // Deimos
```

- You can also expire the key in particular time period.
```js
await client.expire("bike:1", 10) // expire this particular key in 10s.
```

# 5. Redis List
- [if stuck go here](https://redis.io/docs/latest/develop/data-types/lists/)

## Introduction to Redis Lists

Redis lists are **linked lists** of string values. They are commonly used for:

- Implementing **stacks** and **queues**.
- Building **queue management** for background worker systems.

---

## **Basic Commands**

### **1. LPUSH & RPUSH** (Add elements to a list)
- `LPUSH` adds an element to the **head** (left) of a list.
- `RPUSH` adds an element to the **tail** (right) of a list.

#### Example:
```redis
LPUSH mylist "A"
RPUSH mylist "B"
LRANGE mylist 0 -1
```
**Output:** `["A", "B"]`

```redis
LPUSH mylist "X"
RPUSH mylist "Y"
LRANGE mylist 0 -1
```
**Output:** `["X", "A", "B", "Y"]`

---

### **2. LPOP & RPOP** (Remove elements from a list)
- `LPOP` removes and returns an element from the **head**.
- `RPOP` removes and returns an element from the **tail**.

#### Example:
```redis
LPUSH mylist "A" "B" "C"
LPOP mylist
```
**Output:** `"C"`

```redis
RPUSH mylist "X" "Y" "Z"
RPOP mylist
```
**Output:** `"Z"`

---

### **3. LLEN** (Get the length of a list)
- `LLEN` returns the number of elements in a list.

#### Example:
```redis
RPUSH mylist "A" "B" "C"
LLEN mylist
```
**Output:** `3`

```redis
LPUSH newlist "X" "Y"
LLEN newlist
```
**Output:** `2`

---

### **4. LMOVE** (Move elements between lists atomically)
- `LMOVE` moves elements from **one list to another**.
- It can move from **left to left** or **right to right**.

#### Example:
```redis
LPUSH source "A" "B"
LMOVE source destination LEFT LEFT
```
**Output:** Moves `"B"` from `source` to `destination`.

```redis
RPUSH source "X" "Y"
LMOVE source destination RIGHT RIGHT
```
**Output:** Moves `"Y"` from `source` to `destination`.

---

### **5. LRANGE** (Get a range of elements from a list)
- `LRANGE list start end` extracts a range of elements.
- Negative indexes can be used to count from the end.

#### Example:
```redis
RPUSH mylist "A" "B" "C" "D"
LRANGE mylist 1 2
```
**Output:** `["B", "C"]`

```redis
LRANGE mylist -2 -1
```
**Output:** `["C", "D"]`

---

### **6. LTRIM** (Trim a list to a specific range)
- `LTRIM` keeps only elements within a given range.

#### Example:
```redis
RPUSH mylist "A" "B" "C" "D"
LTRIM mylist 1 2
```
**Output:** `["B", "C"]`

```redis
RPUSH numbers "1" "2" "3" "4"
LTRIM numbers 0 1
```
**Output:** `["1", "2"]`

---

## **Blocking Commands**

### **7. BLPOP & BRPOP** (Blocking pop operations)
- `BLPOP` waits for an element to be available in a list, removing it from the **head** as soon as element is inserted within the provided time frame.
- `BRPOP` does the same from the **tail**.

#### Example:
```redis
BLPOP mylist 10
```
**Waits for 10 seconds** to pop an element from the head.

```redis
BRPOP mylist 5
```
**Waits for 5 seconds** to pop an element from the tail.

---

### **8. BLMOVE** (Blocking move operation)
- `BLMOVE` moves an element between lists, blocking if the source is empty.

#### Example:
```redis
BLMOVE source destination LEFT RIGHT 10
```
**Waits for 10 seconds** to move an element from `source` to `destination`.

```redis
BLMOVE queue processed RIGHT LEFT 5
```
**Waits for 5 seconds** to move an element from `queue` to `processed`.

---

## **How Redis Lists Work**

- **Implemented as linked lists**, ensuring constant-time insertions and deletions.
- **Fast for queue and stack operations** but slower for indexed access.
- **Efficient memory usage** when handling large lists.

---

## **First Steps with Redis Lists**

### **Pushing & Retrieving Elements**
```redis
RPUSH queue "task1" "task2"
LPUSH queue "urgent_task"
LRANGE queue 0 -1
```
**Output:** `["urgent_task", "task1", "task2"]`

---

### **Popping Elements**
```redis
LPOP queue
```
**Output:** `"urgent_task"`

```redis
RPOP queue
```
**Output:** `"task2"`

---

### **Moving Elements Between Lists**
```redis
LPUSH pending "taskA"
LMOVE pending processing LEFT LEFT
```
**Output:** Moves `"taskA"` from `pending` to `processing`.

---

### **Trimming a List**
```redis
RPUSH numbers "1" "2" "3" "4"
LTRIM numbers 0 2
```
**Output:** `["1", "2", "3"]`

---

## **Conclusion**
Redis Lists provide a **fast, flexible** way to manage ordered collections of elements. They are particularly useful for **queues, stacks, and messaging systems**. Mastering the key commands helps optimize **performance and memory usage** in Redis-based applications.

# 6. Redis Set :- [read here](https://redis.io/docs/latest/develop/data-types/sets/)
# 7. Redis Hashmap :- [read here](https://redis.io/docs/latest/develop/data-types/hashes/)
# 8. Redis PriorityQueue :- [read here](https://redis.io/docs/latest/develop/data-types/sorted-sets/)
# 9. Redis Stream :- [read here](https://redis.io/docs/latest/develop/data-types/streams/) : Use to store fast changing data like `driver location`, `sensor catching temperature`
# 10. Redis Geospatial data :- [read here](https://redis.io/docs/latest/develop/data-types/geospatial/) : Helps to find anything near you in the radius of Xkm.
# 11. PubSub
![image](https://github.com/user-attachments/assets/62a11d21-2887-4e11-819c-cbdb09c750bc)


# 12. Implementation of redis in NodeJS

```js
.
.
.

app.get("/", async (req, res)=>{
    const cachedValue = await client.get("todos");
    if(cachedValue) return res.json(JSON.parse(cachedValue));

    const {data} = await axios.get('https://jsonplaceholder.typicode.com/todos');

    await client.set("todos", JSON.stringify(data));
    await client.expire("todos", 30);

    return res.json(data);
})
```



