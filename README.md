# Travel Memory

## Deployment Architecture Diagram – Explanation

The Travel Memory application is deployed using a scalable and highly available cloud architecture on Amazon Web Services (AWS). The application follows a three-tier architecture consisting of the presentation layer (frontend), application layer (backend), and data layer (database). The diagram illustrates how user requests flow securely through each component.


<img width="590" height="884" alt="tv1 drawio(2)" src="https://github.com/user-attachments/assets/7db799cc-5bce-4cdf-9fef-13238a979c58" />

## 1. User (Client Layer)
    The user accesses the Travel Memory application through a web browser using a domain name graphtech.live or www.graphtech.live.
      Protocol: HTTPS
      Port: 443
      Role: Initiates requests to view the application and interact with backend APIs.
    All user requests are encrypted using HTTPS to ensure secure communication.

## 2. Cloudflare DNS and Security Layer
    Cloudflare acts as the DNS provider and security gateway for the application.
      Responsibilities:
        Resolves domain names (graphtech.live and api.graphtech.live)
        Provides SSL/TLS encryption
        Protects the application from DDoS attacks
        Improves performance through caching and CDN
      Traffic Flow:
        Incoming HTTPS requests from users are forwarded to the AWS Application Load Balancer.
## 3. Application Load Balancer (ALB)
    An AWS Application Load Balancer is used to distribute incoming traffic efficiently across multiple EC2 instances.
      Listener Configuration:
        HTTP (Port 80)
        HTTPS (Port 443)
      Routing Rules:
        Requests with path /api/* are routed to the Backend Target Group
        All other requests (/*) are routed to the Frontend Target Group
    This routing mechanism enables frontend and backend services to operate independently while sharing a single load balancer.
## 4. Frontend Target Group
    The frontend target group contains multiple EC2 instances hosting the React application.
      Configuration:
      Protocol: HTTP
      Port: 80
      Health Check Path: /
    The load balancer continuously monitors the health of frontend instances and routes traffic only to healthy targets.
## 5. Frontend EC2 Instances (Presentation Layer)
    Multiple EC2 instances are deployed to serve the frontend application.
      Components:
        React (build version)
        Nginx web server
      Responsibilities:
        Serve static frontend content
        Handle client-side routing using React
        Respond to user requests routed by the load balancer
        This setup ensures high availability and scalability of the frontend layer.
## 6. Backend Target Group
    The backend target group contains EC2 instances running the Node.js API.
      Configuration:
        Protocol: HTTP
        Port: 80
        Health Check Path: /health
      The health check endpoint ensures that only healthy backend instances receive traffic.
## 7. Backend EC2 Instances (Application Layer)
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
## 8. MongoDB Atlas (Data Layer)
    MongoDB Atlas is used as a managed NoSQL database service.
      Configuration:
         Protocol: TCP
         Port: 27017
      Responsibilities:
         Store application data such as user information and travel memories
         Provide secure and scalable data storage
         Only backend EC2 instances are allowed to communicate with MongoDB Atlas, ensuring data security.
## 9. End-to-End Traffic Flow Summary
    The user sends an HTTPS request via a web browser.
    Cloudflare resolves the domain name and forwards the request securely.
    The Application Load Balancer receives the request.
    Based on routing rules:
      Frontend requests are sent to frontend EC2 instances.
      API requests are sent to backend EC2 instances.
      Backend services interact with MongoDB Atlas to fetch or store data.
      Responses are sent back to the user through the same secure path.
## 10. Security and Scalability Highlights
    HTTPS encryption using Cloudflare SSL
    Load balancing across multiple EC2 instances
    Backend servers are not publicly accessible
    Secure database connectivity

    Horizontal scalability for both frontend and backend layers
    High availability through health checks and automatic traffic rerouting

# Application Deployment 
## Tech Stack

    Frontend: React.js
    Backend: Node.js, Express.js
    Database: MongoDB Atlas
    Cloud: AWS EC2, ALB, ASG, ACM
    Web Server: Nginx
    Process Manager: PM2
    Domain & DNS: Cloudflare
    SSL: Encrypt Certbot or AWS ACM

# Backend Deployment on AWS EC2
Step 1: Launch EC2 Instance

    Launch an Ubuntu EC2 instance.
    Name: travel-memory-backend
    
    Create security group
    Name: tv-sg
    Open ports: (22,80,3000,3001)
    Select security group (tv-sg) for instance.
    Connect via SSH.

Step 2: Install Required Packages for backend server

    sudo bash
    sudo apt update -y
    sudo apt install nodejs -y
    sudo apt install npm -y

    nodejs -v
    npm -v
<img width="892" height="335" alt="be ec2" src="https://github.com/user-attachments/assets/31b9c128-2999-4a9a-955d-7a6aa7f26413" />
<img width="1352" height="255" alt="apt-upgrade" src="https://github.com/user-attachments/assets/5684138b-3cc2-4c4f-8448-2e23cb61bf30" />

Step 3: Clone Backend Repository

    sudo bash
    cd ~
    git clone https://github.com/UnpredictablePrashant/TravelMemory.git

Step 4: Create .env File
    
    cd TravelMemory/backend
    nano .env
<img width="1350" height="717" alt="tm tree" src="https://github.com/user-attachments/assets/f011e2be-ce0f-4e72-a774-c63d98570ff0" />

Add:

    PORT=3001
    MONGO_URI=mongodb+srv://santosharma:2TNVQJuGsJvRy0pT@travelmemorydb.0kzvz9q.mongodb.net/travelmemory

    Save and exit.

Step 5: Install Dependencies

    npm install

Step 6: Start Backend Server

    node index.js
    Backend server runs:
    BACKEND_EC2_IP= 13.204.76.181
    Open url in browser http://<BACKEND_EC2_IP>:3001

Test <img width="1348" height="437" alt="BE package" src="https://github.com/user-attachments/assets/d41a675e-bd7f-44ed-9643-6812e9298f46" />
connection and API

    url http://<BACKEND_EC2_IP>:3001/hello
    output:Hello World

# MongoDB Atlas Setup & Compass Connection
Step 1: Log in

    Visit: https://cloud.mongodb.com/
    Log into mongo cloud

Step 2: Create Organization and Project
Step 3: Create Cluster

    Name: travelmemorydb
    Select M0 Free Plan
    Create Deployment
<img width="1363" height="716" alt="mongoDB-1" src="https://github.com/user-attachments/assets/20467afe-0d86-4ec3-97ba-8b41781c54f1" />    
<img width="1363" height="716" alt="mongoDB-1" src="https://github.com/user-attachments/assets/fd06ea82-3cff-43cf-a30b-4380f73ace0f" />

Step 4: Create Database User

    Go to Database Access
    Add new user (username and password)

Step 5: Configure Network Access from anywhere

    Add IP:
    0.0.0.0/0
    For production, restrict to trusted IPs. (allow only spacific IP or network)

Step 6: Connect MongoDB Compass

    Connect to Compass
    Copy connection string.

Example
mongodb+srv://<username>:<password>@cluster0.mongodb.net/?retryWrites=true&w=majority

Step 7: Connect Using Compass

    Open MongoDB Compass
    Paste connection string with username and password
    Connect

# Frontend Deployment on AWS EC2
Step 1: Launch EC2 Instance

    Launch Ubuntu EC2
    Name : travel-memory-frontend
    Select security group (tv-sg), same as per backend instance for this instance.
    Connect via SSH.

Step 2: Install Required Packages for frontend server

    sudo bash
    sudo apt update -y
    sudo apt install nodejs -y
    sudo apt install npm -y

    nodejs -v
    npm -v
<img width="1349" height="178" alt="npm inst" src="https://github.com/user-attachments/assets/89cce7eb-53b0-45a1-aa63-e3c7581e3658" />

Step 3: Clone Frontend Repository

    sudo bash
    cd ~
    git clone https://github.com/UnpredictablePrashant/TravelMemory.git


Step 4: Create .env File
    
    cd TravelMemory/frontend
    nano .env

    Add:
    REACT_APP_BACKEND_URL=http://<BACKEND_EC2_IP>:3001

Step 5: Install Dependencies

    npm install
<img width="1350" height="63" alt="npm insta" src="https://github.com/user-attachments/assets/12390058-84c3-4455-8d06-57ab5cf4d5aa" />

Step 6: Start Frontend Server

    npm start
    Frontend server runs.
    FRONTEND_EC2_IP= 13.201.89.94

    open url in browser http://<FRONTEND_EC2_IP>:3000

    Frontpage of Travel Memory application open

# To avoide Port no in url, reverse proxy server needs to install.

For Reverse Proxy use Nginx server
Frontend (Port 3000 → Port 80)

# Connect via SSH to Frontend Server
    sudo bash
    sudo apt install nginx -y
    nano /etc/nginx/sites-available/default

# Modifiy default nginx file to reverse proxy:

    server {
        listen 80;
        #server_name _;
    
        location / {
            proxy_pass http://<FRONTEND_EC2_IP>:3000;
            proxy_http_version 1.1;
    
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
    
Reload:

    nginx -t
    systemctl reload nginx

    Test:
    http://<FRONTEND_EC2_IP>

# Backend Server reverse proxy

To avoide Port no in url, reverse proxy server needs to install.
    
    For Reverse Proxy use Nginx server
    Backend (Port 3001 → Port 80)
    nano /etc/nginx/sites-available/default

Modifiy nginx default file:

    server {
        listen 80;
        # server_name _;
    
        location / {
            proxy_pass http://<BACKEND_EC2_IP>:3001;
            proxy_http_version 1.1;
    
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }

Reload:

    nginx -t
    systemctl reload nginx

Test:
    
    Open in browser http://<BACKEND_EC2_IP>/hello
    output= Hello World

# Custom Domains interigation with Nginx
## Domains Name Used for Server:

    Frontend: graphtech.live
              www.graphtech.live
    Backend: api.graphtech.live

# cloudflare.com to add DNS Records

    Type 	Name 	Value
    A 	     @ 	    13.201.89.94            # FRONTEND SERVER PUBLIC IP
    A 	    www 	13.201.89.94            # FRONTEND SERVER PUBLIC IP
    A 	    api 	13.204.76.181           # BACKEND SERVER PUBLIC IP

<img width="502" height="411" alt="cert r" src="https://github.com/user-attachments/assets/6d11d8cc-446b-4d73-828b-6a678fcfa05b" />

# Frontend Nginx Configuration
    server {
        listen 80;
        server_name graphtech.live www.graphtech.live;
    
        location / {
            proxy_pass http://localhost:3000;
            proxy_http_version 1.1;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }

# Backend Nginx Configuration
    server {
        listen 80;
        server_name api.graphtech.live;
    
        location / {
            proxy_pass http://localhost:3001;
            proxy_http_version 1.1;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
    
Reload:

    nginx -t
    systemctl reload nginx

# Configure Secure connection on both server

## Enable HTTPS with Certbot for Frontend

    sudo bash
    cd ~
    apt install certbot python3-certbot-nginx -y
    certbot --nginx -d graphtech.live -d www.graphtech.live

Final HTTPS URLs

    https://graphtech.live
    https://www.graphtech.live

# Final frontend Nginx (Reverse Proxy ) Config after SSL
    server {
        listen 80;
        server_name graphtech.live www.graphtech.live;
    
        location / {
            proxy_pass http://localhost:3000;
            proxy_http_version 1.1;
    
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    
        listen 443 ssl;
        ssl_certificate /etc/letsencrypt/live/graphtech.live/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/graphtech.live/privkey.pem;
        include /etc/letsencrypt/options-ssl-nginx.conf;
        ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;
    }
    
    server {
        if ($host = www.graphtech.live) {
            return 301 https://$host$request_uri;
        }
    
        if ($host = graphtech.live) {
            return 301 https://$host$request_uri;
        }

        listen 80;
        server_name graphtech.live www.graphtech.live;
        return 404;
    }
    


## Enable HTTPS with Certbot for Backend Server

    sudo certbot --nginx -d api.graphtech.live
<img width="988" height="231" alt="certbot cmd" src="https://github.com/user-attachments/assets/ad9c9adc-7208-4c57-afe3-477683050399" />

# Final Backend Nginx (Reverse Proxy) Config after SSL

    https://api.graphtech.live

    server {
        listen 80;
        server_name api.graphtech.live;
    
        location / {
            proxy_pass http://localhost:3001;
            proxy_http_version 1.1;
    
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    
        listen 443 ssl;
        ssl_certificate /etc/letsencrypt/live/api.graphtech.live/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/api.graphtech.live/privkey.pem;
        include /etc/letsencrypt/options-ssl-nginx.conf;
        ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;
    
    }
    server {
        if ($host = api.graphtech.live) {
            return 301 https://$host$request_uri;
        }
    

        listen 80;
        server_name api.graphtech.live;
        return 404;
    
    }

# Failover and Load balancer Configuration
    
    Create AMI image of Frontend and Backend Servers
      Frontend : front-ami
      Backend  : back-ami

## Create Template
    Launch Template
    Frontend 
      Name: front-ami-tmp
    Backend 
      Name: back-ami-tmp

<img width="892" height="419" alt="alb 1" src="https://github.com/user-attachments/assets/34da8f21-386c-4ec5-9dd7-0aa583fef729" />

Step 1: Create Target Group for Frontend Server

    Name: TG-forntend
    Target type: Instances
    Protocol: HTTPS
    Port: 443
    Register EC2 instances for Frontend
    Check status healthy
<img width="911" height="413" alt="alb 2" src="https://github.com/user-attachments/assets/da554bdf-b49e-413d-be70-64706f14a125" />

Step 2: Create Target Group for Backend Server

    Name: TG-backend
    Target type: Instances
    Protocol: HTTPS
    Port: 443
    Register EC2 instances for Backend
    Check status healthy

    # Load Balancer Setup Application Load Balancer (ALB)
Step 3: Create ALB

    Name: travel-memory-alb
    Scheme: Internet-facing
    IP Type: IPv4
    Select same AZs as EC2
    Zone: More then one zone select

Step 4: Attach both Target Group to ALB.
   
    1 : Target group (TG-frontend) with ALB (travel-memory-alb)
    2 : Target group (TG-backend) with ALB (travel-memory-alb)

Step 4: Test Load Balancer

    http://tm-alb-32463030.ap-south-1.elb.amazonaws.com

AWS Certificate Manager (ACM)

    Certificate ARN:
    arn:aws:acm:ap-south-1:233245302554:certificate/cc3667a6-3da5-4921-84f9-fd6293be7300

    Domain Secured: www.graphtech.live

<img width="935" height="414" alt="alb cert 1" src="https://github.com/user-attachments/assets/cad8d332-a113-461f-b06e-021cad069c77" />
<img width="822" height="382" alt="alb cert" src="https://github.com/user-attachments/assets/c9989ed0-fc03-4d8f-bd04-003fbb238cec" />

# cloudflare.com to add DNS Records

    Type 	     CNAME         
    Name  : _8225cd4c2e13acd764f43cb0c92d493b.alb.
    Value :	_6ebe4648fd9d4bdcd105665671857019.jkddzztszm.acm-validations.aws              


Step 5: Auto Scaling Group (ASG)

    ASG Name: tm-frontend-asg
    Launch Template: front-ami-tmp
    Min: 1
    Desired: 1
    Max: 2
    
    ASG Name: tm-backend-asg
    Launch Template: front-ami-tmp
    Min: 1
    Desired: 1
    Max: 2
<img width="882" height="415" alt="asg " src="https://github.com/user-attachments/assets/72411fa9-7189-45bf-a607-5306cc34841f" />

<img width="869" height="405" alt="asg" src="https://github.com/user-attachments/assets/09cd7917-d77e-4e74-9ef2-4ea69cc9be44" />

Benefits:

    Auto recovery
    Auto scaling
    Zero-downtime deployments

## Deployment Complete!

# Learnings from Deployment

Deploying the Travel Memory MERN application on AWS provided valuable hands-on experience in real-world DevOps practices. This deployment helped bridge the gap between development and production environments while reinforcing best practices for scalability, security, and reliability.

# 1. Understanding Full-Stack Deployment

    Through this deployment, I learned how a MERN stack application is structured and deployed in a production environment. Separating the frontend (React) and backend (Node.js) into     different servers improved clarity, maintainability, and scalability.

# 2. Importance of Reverse Proxy (Nginx)

    Using Nginx as a reverse proxy was a key learning:
    It allowed backend services running on internal ports (3000 and 3001) to be securely exposed via port 80/443.
    Improved security by preventing direct public access to application ports.
    Simplified integration with the Application Load Balancer.

# 3. Load Balancing and High Availability

    Implementing an Application Load Balancer (ALB) demonstrated how traffic can be distributed across multiple instances:
    Multiple frontend and backend EC2 instances ensured high availability.
    Health checks helped automatically remove unhealthy instances from traffic.
    Listener rules enabled routing to multiple target groups using a single load balancer.

# 4. Health Checks and Monitoring

    One of the most important learnings was the role of health checks:
    Proper health endpoints (/health) are essential for backend services.
    Incorrect ports or paths result in unhealthy target groups.
    Health checks are critical for maintaining reliability in production.

# 5. Domain and DNS Management

    Configuring a custom domain using Cloudflare provided insights into:
    DNS record management (A and CNAME records)
    Using subdomains Using subdomains www.graphtech.live for frontend services and api.graphtech.live for backend services
    SSL/TLS termination and DDoS protection via Cloudflare
    This setup improved both security and user experience.

# 6. Security Best Practices

    The deployment reinforced several security principles:
    Backend servers should not be publicly accessible.
    Sensitive data must be stored in environment variables (.env).
    Database access should be restricted to backend servers only.
    Using HTTPS for all client-server communication is essential.

# 7. Cloud Networking and Access Control

    Working with AWS Security Groups helped in understanding:
    How inbound and outbound traffic is controlled.
    Allowing traffic only from trusted sources (such as ALB to EC2).
    Preventing unnecessary exposure of internal services.

# 8. Scalability and Fault Tolerance

    By creating multiple instances and placing them behind a load balancer, I learned:
    How horizontal scaling improves application resilience.
    How cloud infrastructure can handle traffic spikes.
    How stateless frontend and backend services support scaling.

# 9. Database as a Managed Service

    Using MongoDB Atlas highlighted the advantages of managed databases:
    Reduced operational overhead
    Built-in backups and scalability
    Secure connectivity with cloud applications

# 10. Real-World DevOps Experience

    Overall, this deployment provided practical experience in:
    Cloud infrastructure management
    Production-ready application deployment
    Debugging real-world issues such as unhealthy target groups and CORS errors
    Designing scalable and secure architectures

# Conclusion

    This project strengthened my understanding of DevOps workflows, cloud-native architecture, and production deployments. The hands-on challenges encountered during deployment           significantly improved my troubleshooting skills and confidence in deploying real-world applications.

# Author:
## Santosh Kumar Sharma

