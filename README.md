# project-1-2025
Step 1: Setup Your AWS EC2 Instance

Step 2: Install Docker on EC2
sudo apt update && sudo apt install -y docker.io
sudo systemctl start docker
sudo systemctl enable docker
docker --version

Step 3: Clone the Web App Code
git clone https://github.com/heroku/node-js-sample.git
cd node-js-sample

Step 4: Create a Dockerfile
nano Dockerfile

FROM node:18  
WORKDIR /app  
COPY package*.json ./  
RUN npm install  
COPY . .  
CMD ["npm", "start"]  
EXPOSE 5000  

Step 5: Build & Run the Docker Container
docker build -t my-node-app .
docker run -d -p 80:5000 my-node-app

Step 6: Automate Deployment with GitHub Actions

git init
git remote add origin https://github.com/your-username/devops-project-1.git
git branch -M main
git add .
git commit -m "Initial commit"
git push -u origin main

Create a GitHub Actions Workflow:
mkdir -p .github/workflows
nano .github/workflows/deploy.yml

name: Deploy to AWS EC2

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v2

      - name: Deploy to EC2
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.EC2_HOST }}
          username: ubuntu
          key: ${{ secrets.EC2_KEY }}
          script: |
            cd ~/node-js-sample
            git pull origin main
            docker build -t my-node-app .
            docker stop my-node-app || true
            docker rm my-node-app || true
            docker run -d -p 80:5000 my-node-app

Set Up GitHub Secrets

Step 7: Test the CI/CD Pipeline
1. git add .
2. git commit -m "Updated log message"
3. git push origin main
