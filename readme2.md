# MERN Stack Three-Tier Application with Docker and Docker Compose

## Prerequisites
Ensure Docker is running on your system:
```sh
sudo systemctl status docker
```

## Setting Up Docker Network
Create a custom Docker network to enable communication between containers:
```sh
docker network create mern-network
```

## Running Containers Manually
### 1. Frontend
Navigate to the frontend directory and build the image:
```sh
cd frontend
docker build -t mern-frontend .
```
Run the frontend container:
```sh
docker run --name=frontend --network=mern-network -d -p 5173:5173 mern-frontend
```
Check logs and container size:
```sh
docker logs frontend
docker ps -s
```

### 2. Database (MongoDB)
Run the MongoDB container with volume mount for data persistence:
```sh
docker run --network=mern-network --name=mongodb -d -p 27017:27017 -v ~/opt/data:/data/db mongo:latest
```

### 3. Backend
Navigate to the backend directory and build the image:
```sh
cd backend
docker build -t mern-backend .
```
Run the backend container:
```sh
docker run --network=mern-network --name=backend -d -p 5050:5050 mern-backend
```

Now, all three containers are running, and the application should be working.

## Using Docker Compose for Simplified Management
To avoid running multiple commands manually, use Docker Compose to automate and manage the containers.

### `docker-compose.yml`
Create a `docker-compose.yml` file in the root directory with the following content:
```yaml
services:
  backend:
    build: ./backend # Ensure you run Docker Compose from the correct directory
    ports:
      - "5050:5050"
    networks:
      - mern_network
    environment:
      MONGO_URI: mongodb://mongodb:27017/mydatabase  
    depends_on:
      - mongodb

  frontend:
    build: ./frontend
    ports:
      - "5173:5173"  
    networks:
      - mern_network
    environment:
      REACT_APP_API_URL: http://backend:5050

  mongodb:
    image: mongo:latest  
    ports:
      - "27017:27017"  
    networks:
      - mern_network
    volumes:
      - mongo-data:/data/db  

networks:
  mern_network:
    driver: bridge

volumes:
  mongo-data:
    driver: local  # Persist MongoDB data locally
```

### Running Docker Compose
Ensure you are in the directory containing `docker-compose.yml`, then start the containers:
```sh
docker compose up -d
```
Check logs for a specific container:
```sh
docker compose logs container_name
```
List running containers:
```sh
docker ps
```

Now, the application should be running, and Docker Compose will automate and manage the multiple containers efficiently.

### Stopping and Removing Containers
To stop and remove all containers:
```sh
docker rm -f container_name
```

Ensure you're always running Docker Compose from the correct directory for proper execution.

