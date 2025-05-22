# Devops-Project-Final (Job Application Portal)
Documentation Work of Final Devops Project

## Project Details

- `Title:` Microservice Deployment with Docker

- `Description:` Deploy and manage a set of microservices using docker, ensuring they can scale independently 

- `Wants from students:` Create docker containers for each microservice, configure inter-service communication, and manage scaling

- `Proposed tools:` Docker, Docker Compose, Microservices

## Command Prompt Screenshots
```bash
sudo apt-get install docker-compose-plugin
```
![Screenshot from 2025-05-09 15-10-23](https://github.com/user-attachments/assets/b76ac0fd-1056-48e9-acd0-515b047fa158)
```bash
docker --version
```
![Screenshot from 2025-05-09 15-10-40](https://github.com/user-attachments/assets/74953418-15b1-4a1d-ab75-18f474366200)
```bash
docker compose version
```
![Screenshot from 2025-05-09 15-10-56](https://github.com/user-attachments/assets/2e9db05d-f05c-446f-ba15-6d979227de65)
```bash
cd Downloads
cd project-root
```
![Screenshot from 2025-05-09 15-12-14](https://github.com/user-attachments/assets/22730317-43e4-476d-b398-f7afd2034c35)
```bash
docker compose up --build
```
![Screenshot from 2025-05-09 15-13-07](https://github.com/user-attachments/assets/58c8e4e2-e876-4fe9-8e69-595d1922210e)
![Screenshot from 2025-05-09 15-13-17](https://github.com/user-attachments/assets/9c9db1cc-6cb6-4281-a9fa-3c9f93d58452)
```bash
docker compose down
```
![Screenshot from 2025-05-09 15-58-30](https://github.com/user-attachments/assets/36927486-9380-4bed-96c6-c5ccbaeb25d0)
```bash
docker image prune
```
![Screenshot from 2025-05-09 15-58-46](https://github.com/user-attachments/assets/cf469c3d-7be5-47ae-b0a3-0df8637757b5)
```bash
docker compose up --build --scale frontend=2 --scale auth=2
```
![Screenshot from 2025-05-22 12-58-06](https://github.com/user-attachments/assets/0e3f1e25-ae7c-4872-822a-72d3359fde26)
![Screenshot from 2025-05-22 12-58-24](https://github.com/user-attachments/assets/ce9d456b-8a63-4768-9a43-ecd950a5a5c5)

### `Note` : For using the above scaling command, we need to define `nginx.conf` and modify `docker-compose.yaml`

- `nginx.conf`:
```bash
events {}
  http {
    upstream frontend {
      server frontend:3000;
    }
    upstream auth {
      server auth:5001;
    }
    upstream job {
      server job:5002;
    }
    upstream application {
      server application:5003;
    }
    server {
      listen 80;
      location / {
        proxy_pass http://frontend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
      }
      location /api/auth/ {
        proxy_pass http://auth;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
      }
      location /api/jobs/ {
        proxy_pass http://job;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
      }
      location /api/applications/ {
        proxy_pass http://application;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
      }
    }
  }
```

- `docker-compose.yaml`
```bash
version: '3.8'
services:
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    expose:
      - "3000"
    networks:
      - job-portal-network
    depends_on:
      - auth
      - job
      - application
  auth:
    build:
      context: ./backend/auth
      dockerfile: Dockerfile
    expose:
      - "5001"
    env_file:
      - backend/auth/.env
    networks:
      - job-portal-network
  job:
    build:
      context: ./backend/job
      dockerfile: Dockerfile
    expose:
      - "5002"
    env_file:
      - backend/job/.env
    networks:
      - job-portal-network
  application:
    build:
      context: ./backend/application
      dockerfile: Dockerfile
    expose:
      - "5003"
    env_file:
      - backend/application/.env
    networks:
      - job-portal-network
  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
    depends_on:
      - frontend
      - auth
      - job
      - application
    networks:
      - job-portal-network
networks:
  job-portal-network:
    driver: bridge
```

## Folder Structure

- `project-root/`
  - `frontend/`
    - `public/`
      - index.html
    - `src/`
      - index.js
    - package.json
    - Dockerfile
  - `backend/`
    - `application/`
      - application.js
      - Dockerfile
      - package.json
      - .env
    - `auth/`
      - auth.js
      - Dockerfile
      - package.json
      - .env
    - `job/`
      - job.js
      - Dockerfile
      - package.json
      - .env
  - docker-compose.yml

## Project Files

### `docker-compose.yml`
```groovy
version: '3.8'

services:
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    ports:
      - "3000:3000"
    networks:
      - job-portal-network
    depends_on:
      - auth
      - job
      - application

  auth:
    build:
      context: ./backend/auth
      dockerfile: Dockerfile
    ports:
      - "5001:5001"
    env_file:
      - backend/auth/.env
    networks:
      - job-portal-network

  job:
    build:
      context: ./backend/job
      dockerfile: Dockerfile
    ports:
      - "5002:5002"
    env_file:
      - backend/job/.env
    networks:
      - job-portal-network

  application:
    build:
      context: ./backend/application
      dockerfile: Dockerfile
    ports:
      - "5003:5003"
    env_file:
      - backend/application/.env
    networks:
      - job-portal-network

networks:
  job-portal-network:
    driver: bridge
```

## Output Screenshots

- Login Page

![Screenshot from 2025-05-09 15-53-09](https://github.com/user-attachments/assets/a1a5d43c-802a-403f-a4d1-43c2c3758a10)

- Register Page

![Screenshot from 2025-05-09 15-53-14](https://github.com/user-attachments/assets/a1a5bbf7-f3b4-4949-949c-8be0f29f9281)

- User Page

![Screenshot from 2025-05-09 15-53-48](https://github.com/user-attachments/assets/f23ac344-84bd-4f96-bfaa-79bf34febafa)
![Screenshot from 2025-05-09 15-53-51](https://github.com/user-attachments/assets/47b039f9-2a33-480c-9204-179ee7b0d462)

- Admin Page

![Screenshot from 2025-05-09 15-54-08](https://github.com/user-attachments/assets/8835890b-1449-43ce-93a6-b72059a1a82e)
![Screenshot from 2025-05-09 15-54-15](https://github.com/user-attachments/assets/3fd63a41-0d34-421c-bfe9-82d3e45643d0)


