# Real-Time Location Tracking Architecture – Research

---

## 1. Technology Comparison

### WebSockets

WebSockets establish a **persistent, stateful, full-duplex connection** between the client and the server. After an initial HTTP handshake, communication no longer follows the traditional request–response model. Both client and server can push data to each other at any time.

This bidirectional nature helps solve the problem of **stale data**, as updates are delivered immediately.

However, WebSockets introduce complexity when the backend is **horizontally scaled**:
- Each client remains connected to a single server instance
- If that instance goes down, the connection is lost
- Sharing state across multiple instances requires an additional layer such as Redis Pub/Sub, message queues, or sticky sessions

While powerful, this increases operational overhead and development effort.

**Best suited for:**  
Chat applications, collaborative tools, or systems requiring near-instant updates.

---

### Polling (HTTP-based)

Polling does not rely on persistent connections. Instead, the client periodically requests updates from the server using standard HTTP.

A key advantage in a load-balanced system is resilience:
- Each request can be routed to any healthy server instance
- If one server fails, requests continue without client-side disruption
- This makes polling naturally fault-tolerant in horizontally scaled environments

#### Short Polling

In short polling:
- The client sends a request at fixed intervals (e.g., every 30 seconds)
- The server responds immediately with the latest data
- The connection is closed after each response

**Pros**
- Simple to implement
- Predictable server load
- Works well with load balancers

**Cons**
- Data may be stale depending on polling interval

---

#### Long Polling

In long polling:
- The client sends a request
- The server keeps the request open until new data is available
- After receiving a response, the client immediately sends another request

**Pros**
- Fewer unnecessary responses
- More responsive than short polling

**Cons**
- More complex server logic
- Large number of open connections under load

---

### Server-Sent Events (SSE)

Server-Sent Events establish a **persistent, one-way connection** from server to client.

- The server pushes updates whenever data changes
- The client cannot send data back over the same connection
- Built on standard HTTP and simpler than WebSockets

Limitations include:
- Lack of full-duplex communication
- Limited mobile browser support
- Not ideal when clients frequently send data, such as GPS updates

**Best suited for:**  
Live feeds, notifications, or dashboards where clients mostly consume data.

---

## 2. Recommendation

### Recommended Approach: Short Polling

Based on the analysis, **short polling** is the most practical choice for Unolo’s current requirements.

With approximately **10,000 field employees sending location updates every 30 seconds**, the expected load is: **10000 / 30 = 333 req/sec**


This level of traffic is well within the capabilities of a horizontally scaled Node.js backend handling simple JSON requests.

---

## 3. Why Short Polling Fits Unolo

**Scalability**  
Short polling works seamlessly with load balancers, distributing requests evenly across server instances. No shared in-memory state or session affinity is required.

**Reliability**  
Since connections are short-lived, the system performs well on unstable mobile networks. Server failures do not affect clients beyond a single missed request.

**Battery Efficiency**  
Sending updates every 30 seconds avoids continuous network usage while still providing sufficiently fresh data for field force tracking.

**Cost and Development Time**  
Short polling requires no additional infrastructure such as WebSocket gateways or message brokers. This reduces both operational cost and time-to-market, which is critical for a startup.

---

## 4. Trade-offs

By choosing short polling, Unolo accepts:
- Updates that are not truly real-time
- Potential delays of up to the polling interval (30 seconds)
- Reduced efficiency if update frequency increases significantly

### When This Choice Should Be Reconsidered
- If near-instant updates become a strict requirement
- If update frequency increases to sub-second intervals
- If the system scales far beyond current projections

Short polling also serves as a strong foundation, allowing a future transition to WebSockets without a full architectural rewrite.

---

## 5. High-Level Implementation

### Backend
- REST API endpoint to receive location updates
- Load balancer distributing requests across instances
- Database or cache to store latest employee locations

### Mobile / Frontend
- Mobile app sends location updates every 30 seconds
- Manager dashboard fetches latest locations periodically
- Graceful handling of temporary network failures

### Infrastructure
- Standard HTTP load balancing (e.g., Nginx)
- Horizontal scaling of backend services
- No persistent connection management required

---

## Conclusion

Short polling is not the most sophisticated real-time solution, but it is the most **pragmatic** for Unolo’s current scale and constraints. It prioritizes reliability, simplicity, and cost-effectiveness while leaving room for future evolution as requirements grow.

---

## References

- Geeks For Geeks – WebSockets  
- Geeks For Geeks – Long Polling and Short Polling  
- Medium (Gökhan Ayrancıoğlu) – Server-Sent Events  
- Youtube (Piyush Garg) - Websockets v/s Polling v/s Server Sent Events
