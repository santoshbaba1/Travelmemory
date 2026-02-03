# Travel Memory

# Deployment Architecture Diagram – Explanation

The Travel Memory application is deployed using a scalable and highly available cloud architecture on Amazon Web Services (AWS). The application follows a three-tier architecture consisting of the presentation layer (frontend), application layer (backend), and data layer (database). The diagram illustrates how user requests flow securely through each component.


<img width="983" height="1472" alt="tv1 drawio(2)" src="https://github.com/user-attachments/assets/7db799cc-5bce-4cdf-9fef-13238a979c58" />

# 1. User (Client Layer)
    The user accesses the Travel Memory application through a web browser using a domain name graphtech.live or www.graphtech.live.
    Protocol: HTTPS
    Port: 443
    Role: Initiates requests to view the application and interact with backend APIs.
    All user requests are encrypted using HTTPS to ensure secure communication.

# 2. Cloudflare DNS and Security Layer
    Cloudflare acts as the DNS provider and security gateway for the application.
    Responsibilities:
    Resolves domain names (yourdomain.com and api.yourdomain.com)
    Provides SSL/TLS encryption
    Protects the application from DDoS attacks
    Improves performance through caching and CDN
    Traffic Flow:
            Incoming HTTPS requests from users are forwarded to the AWS Application Load Balancer.
# 3. Application Load Balancer (ALB)
    An AWS Application Load Balancer is used to distribute incoming traffic efficiently across multiple EC2 instances.
    Listener Configuration:
    HTTP (Port 80)
    HTTPS (Port 443)
    Routing Rules:
            Requests with path /api/* are routed to the Backend Target Group
            All other requests (/*) are routed to the Frontend Target Group
    This routing mechanism enables frontend and backend services to operate independently while sharing a single load balancer.
# 4. Frontend Target Group
    The frontend target group contains multiple EC2 instances hosting the React application.
    Configuration:
    Protocol: HTTP
    Port: 80
    Health Check Path: /
    The load balancer continuously monitors the health of frontend instances and routes traffic only to healthy targets.
# 5. Frontend EC2 Instances (Presentation Layer)
    Multiple EC2 instances are deployed to serve the frontend application.
    Components:
         React (build version)
         Nginx web server
    Responsibilities:
         Serve static frontend content
         Handle client-side routing using React
         Respond to user requests routed by the load balancer
         This setup ensures high availability and scalability of the frontend layer.
# 6. Backend Target Group
    The backend target group contains EC2 instances running the Node.js API.
    Configuration:
    Protocol: HTTP
    Port: 80
    Health Check Path: /health
    The health check endpoint ensures that only healthy backend instances receive traffic.
# 7. Backend EC2 Instances (Application Layer)
    Multiple EC2 instances are deployed to run the backend services.
    Components:
           Node.js with Express framework
           PM2 process manager
           Nginx reverse proxy
    Internal Communication:
           Nginx listens on port 80 and forwards requests to Node.js running on port 3000
    Responsibilities:
           Handle API requests
           Authenticate users
           Communicate with the database
           Process business logic
           Backend instances are not publicly exposed, enhancing security.
# 8. MongoDB Atlas (Data Layer)
    MongoDB Atlas is used as a managed NoSQL database service.
    Configuration:
           Protocol: TCP
           Port: 27017
    Responsibilities:
           Store application data such as user information and travel memories
           Provide secure and scalable data storage
           Only backend EC2 instances are allowed to communicate with MongoDB Atlas, ensuring data security.
# 9. End-to-End Traffic Flow Summary
    The user sends an HTTPS request via a web browser.
    Cloudflare resolves the domain name and forwards the request securely.
    The Application Load Balancer receives the request.
    Based on routing rules:
           Frontend requests are sent to frontend EC2 instances.
           API requests are sent to backend EC2 instances.
           Backend services interact with MongoDB Atlas to fetch or store data.
           Responses are sent back to the user through the same secure path.
# 10. Security and Scalability Highlights
    HTTPS encryption using Cloudflare SSL
    Load balancing across multiple EC2 instances
    Backend servers are not publicly accessible
    Secure database connectivity

    Horizontal scalability for both frontend and backend layers
    High availability through health checks and automatic traffic rerouting





`.env` file to work with the backend after creating a database in mongodb: 

```
MONGO_URI='ENTER_YOUR_URL'
PORT=3001
```

Data format to be added: 

```json
{
    "tripName": "Incredible India",
    "startDateOfJourney": "19-03-2022",
    "endDateOfJourney": "27-03-2022",
    "nameOfHotels":"Hotel Namaste, Backpackers Club",
    "placesVisited":"Delhi, Kolkata, Chennai, Mumbai",
    "totalCost": 800000,
    "tripType": "leisure",
    "experience": "Lorem Ipsum, Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum, ",
    "image": "https://t3.ftcdn.net/jpg/03/04/85/26/360_F_304852693_nSOn9KvUgafgvZ6wM0CNaULYUa7xXBkA.jpg",
    "shortDescription":"India is a wonderful country with rich culture and good people.",
    "featured": true
}
```


For frontend, you need to create `.env` file and put the following content (remember to change it based on your requirements):
```bash
REACT_APP_BACKEND_URL=http://localhost:3001
```
