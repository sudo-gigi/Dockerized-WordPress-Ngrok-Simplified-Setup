# Dockerized WordPress + Ngrok Simplified Setup 🚀🐳  
**Perfect for local development & public sharing!**

> ⚠️ **Security Note**: This simplified configuration is intended for educational/development purposes only. Production environments require proper secret management (e.g., Docker Secrets, HashiCorp Vault).

## 📦 Prerequisites
1. Docker Desktop ([Install Guide](https://docs.docker.com/get-docker/))
2. Ngrok Account ([Free Tier](https://dashboard.ngrok.com/signup))
3. Terminal/CLI access

## 🚀 Quick Start
```bash
# 1. Initialize project repository
mkdir wordpress-ngrok && cd wordpress-ngrok
git init

# 2. Create project files
cat << 'EOF' > docker-compose.yml
version: '3.3'

services:
  db:
    image: mysql:5.7
    restart: always
    volumes:
      - db_data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: ${DB_ROOT_PASSWORD:-changeme}   # 🚨 Rotate this in production!
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: ${DB_PASSWORD:-secret}  # 🚨 should match below!

  wordpress:
    image: wordpress:latest
    restart: always
    ports:
      - "8000:80"
    depends_on:
      - db
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: ${DB_PASSWORD:-secret}  # 🚨 should match above!
      WORDPRESS_DB_NAME: wordpress

  ngrok:
    image: wernight/ngrok
    restart: always
    depends_on:
      - wordpress
    command: ["ngrok", "http", "--log=stdout", "wordpress:80"]
    environment:
      NGROK_AUTHTOKEN: ${NGROK_TOKEN:-your_token_here} # 🔐 Get from ngrok dashboard
    ports:
      - "4040:4040"

volumes:
  db_data:
EOF
```

## 🌐 2. Ngrok Setup

### 2.1 Get Your Free Token
- Sign up at [ngrok.com](https://ngrok.com) (free tier works!)
- Copy your authtoken from dashboard
- Replace `your_ngrok_token_here` in the YAML file

🔍 **Pro Tip:** The token looks like _2AbcDeF...xyz_

## 3. Docker Stack Activation 🚀

## 3.1 Start Containers
```bash
# Build and start containers
docker-compose up -d --build

# List all services in the stack
docker-compose ps
```
## 🔗 4. Access Your Site

### 4.1 Local Access
👉 [http://localhost:8000](http://localhost:8000)  
*(WordPress installer will greet you and you'll have to setup admin on first access)*

### 4.2 Public URL via Ngrok
```bash
docker-compose logs ngrok | grep "url=https://"
```
📢 Copy the `https://` URL to share with anyone!

## 🧹 5. Cleanup When Done
```bash
docker-compose down  # Full teardown with volume removal  
docker volume prune  # Removes DB volume
```

We just:

- 🐳 **Dockerized WordPress**   
- 🌐 **Ngrok-ed our way** → into "localhost" 

**Next time you could add:**  
- 🔒 Environment variables for secrets  
- 📦 Proper volume management  
- 🚦 Healthchecks in Docker  

*(Disclaimer: No developers, Docker containers, or routers were harmed in this tutorial. Ngrok tunnels may contain traces of "Why isn't this working?!" moments.)*




