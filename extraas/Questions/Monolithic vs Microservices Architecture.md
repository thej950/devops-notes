# Monolithic vs Microservices Architecture

## Monolithic Architecture
### Overview:
- Monolithic architecture contains all services related to an application in a single system.  
  *(Example: travel booking, hotel booking, payments, sign-up)*
- When a user sends a request via HTTPS, it goes directly to the application server.
  - The application server contains the database underneath.
  - It interacts with the database and sends data back to the user.
- The entire application is developed using a single programming language and framework.

### Advantages:
- Easy to deploy.
- Low complexity.

### Disadvantages:
- Hard to scale.
- Slow performance.
- **Single point of failure:** If the server crashes, all services go down.
- Slow continuous development.
- Rigid structure.
- Horizontal scaling affects the entire application rather than specific business functionalities.

---

## Microservices Architecture
### Overview:
- Microservices architecture breaks the application into small, independent services.
- These services are loosely coupled.
- Each service interacts with others and has its own autonomous structure.
- Supports multiple programming languages for different functionalities.

### Advantages:
- Loosely coupled.
- Agile and flexible.
- Independent development.
- Fault isolation.
- Mixed technology stack.
- Supports granular scaling.

### Disadvantages:
- High complexity.
- Consistency challenges.
- Requires automation.
- Debugging can be difficult.

### Additional Notes:
- In microservices architecture, when a request is sent to a service:
  - The API Gateway receives the request.
  - The API Gateway routes the request to the appropriate service.
- Each service has its own independent database.
