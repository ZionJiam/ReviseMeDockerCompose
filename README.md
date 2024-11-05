# ReviseMe Application with Docker and NGINX Load Balancer

This project contains multiple services such as Auth Service, Flashcard Service, Flashcard AI Service, and Frontend. It uses Docker and Docker Compose for containerization, and NGINX for load balancing across different service instances.

## Prerequisites
Before you begin, ensure you have the following installed:

- Docker
- Docker Compose

## Running the Application

### Step 1: Clone the Repository
First, clone this repository to your local machine:

```bash
git clone https://github.com/yourusername/yourrepositoryname.git
cd yourrepositoryname
```

### Step 2: NGINX Configuration
Ensure that the `nginx.conf` file is in the root directory of the project. This file configures NGINX to load balance the requests between multiple instances of your services.

Here's an example of the `nginx.conf` file:

```nginx
events {}

http {
    upstream auth_service {
        server auth-service2-1:5002;
        server auth-service2-2:5003;
    }

    upstream flashcard_service {
        server flashcard-service-1:5001;
        server flashcard-service-2:5004;
    }

    upstream flashcard_ai_service {
        server flashcard-ai-service-1:5010;
        server flashcard-ai-service-2:5011;
    }

    server {
        listen 80;

        location /auth/ {
            proxy_pass http://auth_service/;
        }

        location /flashcards/ {
            proxy_pass http://flashcard_service/;
        }

        location /flashcards-ai/ {
            proxy_pass http://flashcard_ai_service/;
        }
    }
}
```

Make sure this configuration is available and correct before moving to the next steps.

### Step 3: Build and Run the Application Using Docker Compose
Run the following command in the root of the project (where the `docker-compose.yml` and `nginx.conf` are located) to build and start all the services:

```bash
docker-compose up -d
```

This will:

1. Build the Docker images for each service.
2. Start the containers for all services, including MongoDB, NGINX, and the backend/frontend services.

### Step 4: Access the Application
Once the services are running, you can access the application at the following URLs:

**Frontend:**
Open your browser and go to:
```
http://localhost:3000
```

**Backend Services (via NGINX):**
The API services can be accessed through NGINX on port 8080:
```
http://localhost:8080/auth/api/users/login
http://localhost:8080/flashcards/...
http://localhost:8080/flashcards-ai/...
```

These endpoints are load-balanced using NGINX across multiple service instances.

### Step 5: Stop the Services
To stop all running services, use the following command:

```bash
docker-compose down
```

This will stop and remove the containers.

### Step 6: Check the Status of Containers
To check the status of all running containers, you can use the following command:

```bash
docker-compose ps
```

### Step 7: View Logs
To view the logs for a specific container, use this command:

```bash
docker-compose logs <service-name>
```

For example, to view the logs for `auth-service2-1`, run:

```bash
docker-compose logs auth-service2-1
```

### Step 8: Rebuild the Services After Changes
If you make changes to the code or configuration, you can rebuild the services with:

```bash
docker-compose build
docker-compose up -d
```

This will rebuild the Docker images and restart the containers with the updated code.

## Troubleshooting

### Port Conflicts
If you encounter port conflicts (e.g., if port 8080 is already in use), modify the `ports` section in `docker-compose.yml`:

```yaml
ports:
  - "8081:80"  # Change to another port like 8081
```

After making changes, run:

```bash
docker-compose up -d
```

## Notes

### MongoDB Data Persistence
MongoDB data is persisted using a Docker volume (`mongo-data`). This ensures that data is retained across container restarts.

### Environment Variables
You can modify any environment variables in the `docker-compose.yml` file or create a `.env` file to manage sensitive configurations.

## Additional Commands

### Pulling Images from Docker Hub
If you only have access to the Docker images (for example, if you're sharing the images with others), they can pull and run the images directly using:

```bash
docker-compose pull
docker-compose up -d
```

### Cleaning Up Docker Resources
To free up space and remove unused containers, images, volumes, and networks, use the following command:

```bash
docker system prune -a
```

This will clean up any resources not currently being used.

### Pushing to Docker Hub
If you need to push individual Docker images to Docker Hub, you can do so by tagging the images and pushing them:

**Tag the Image:**
```bash
docker tag auth-service2-1 yourdockerhubusername/auth-service2-1:latest
```

**Push the Image:**
```bash
docker push yourdockerhubusername/auth-service2-1:latest
```

Follow this process for each service you want to push.

## Final Notes
This README.md contains instructions for cloning, building, and running the ReviseMe application using Docker Compose. Make sure to update any repository links, Docker Hub image names, and other project-specific details as needed.
