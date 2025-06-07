
# Event-Driven Architecture and RESTful API Design Q&A

## Question 1:
**Considering event-driven architecture, if I have a store API that needs to communicate to an orders API, invoices API, and stocks API through a message queue, should I create one topic for each type of message or one topic from where the 3 APIs will consume the same message? Is there a best practice?**

### Answer:
In an event-driven architecture, deciding whether to use one topic for all events or separate topics for each event type depends on your use case. Here are two approaches:

#### **Separate Topics for Each Type of Event**
- Example Topics:
  - `orders-created`
  - `invoices-generated`
  - `stock-updated`
- **Advantages**:
  - Separation of concerns and clear event ownership.
  - Scalability and loose coupling.
- **Disadvantages**:
  - More management overhead.

#### **Single Topic for All Events**
- Example Topic:
  - `store-events`
- **Advantages**:
  - Simpler initial setup and reduced topic sprawl.
- **Disadvantages**:
  - Consumers must filter for relevant messages.
  - Coupling and potential message overload.

#### **Best Practice**:
Use separate topics for different event types, aligning with domain boundaries and scalability requirements.

### Documentation Sources:
1. **[Best practices for implementing event-driven architectures (AWS)](https://aws.amazon.com/blogs/architecture/best-practices-for-implementing-event-driven-architectures-in-your-organization/)**
2. **[Topic Architecture Best Practices (Solace)](https://docs.solace.com/Messaging/Topic-Architecture-Best-Practices.htm)**

---

## Question 2:
**For the store API, should I have a URL pattern like `https://<hostname>/<entity>/<version>/MethodName`, and use the HTTP verbs and method names? Or `https://<hostname>/<version>/<entity>`, and use only the HTTP verbs? Is there a better approach than these two?**

### Answer:
A RESTful API design typically aligns with resources and HTTP verbs for simplicity and clarity. Here’s a breakdown:

#### **Option 1: URL Pattern with Method Names**
- Example: `https://<hostname>/<entity>/<version>/MethodName`
- **Advantages**:
  - Clear intentions for developers unfamiliar with REST.
- **Disadvantages**:
  - Violates REST principles, tightly couples APIs to methods.

#### **Option 2: URL Pattern with Version and Entity**
- Example: `https://<hostname>/<version>/<entity>`
- **Advantages**:
  - Aligns with REST principles and emphasizes resources over actions.
  - Clean and predictable design.
- **Disadvantages**:
  - Developers need to infer operations from HTTP verbs.

#### **Best Practice**:
Use resource-based URLs with HTTP verbs:
- Example:
  - `GET https://api.store.com/v1/orders`
  - `POST https://api.store.com/v1/orders`
  - `GET https://api.store.com/v1/orders/123`

### Documentation Sources:
1. **[Best Practices for REST API Design (Stack Overflow Blog)](https://stackoverflow.blog/2020/03/02/best-practices-for-rest-api-design/)**
2. **[REST API URI Naming Conventions (RESTfulAPI.net)](https://restfulapi.net/resource-naming/)**
3. **[Pragmatic RESTful API Design (Vinay Sahni)](https://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api)**

---
Work in progress ...
