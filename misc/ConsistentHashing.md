### Key points - Consistent Hashing

The key to understand Consistent Hashing is to understand the following:

1. **We not only hash the key IDs but also the server/node IDs**. Modulo hashing works well for a fixed number of servers. However in reality servers can go down and become unavailable; so a fixed number of servers cannot be assumed.

2. Server IDs are hashed to a fixed range. The key IDs are also hashed to the same fixed range. Can imagine this range to be like a circle.

3. The key IDs are then routed to the closest server in the clockwise direction (It can be anticlockwise as well, as long as the same direction is used for all the keys).

4. Finally, only a single hashing algorithm need not be used. We can use multiple hashing algorithms to make the servers spread out across the circle. Thus the load can be distributed in a better manner.

---

Ref:

1. [Visual simulation of consistent hashing](https://tech.endeepak.com/blog/2021/09/22/visual-simulation-of-consistent-hashing/)

2. [What is Consistent Hashing and Where is it used?](https://www.youtube.com/watch?v=zaRkONvyGr8)
