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




