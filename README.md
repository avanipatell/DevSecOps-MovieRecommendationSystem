# DevSecOps-MovieRecommendationSystem
This guide provides detailed instructions for setting up the DevSecOps project on an EC2 instance using Ubuntu 22.04.

## Phase 1: Initial Setup and Deployment

### Step 1: Launch EC2 Instance (Ubuntu 22.04)

1. **Sign in to AWS Console**: Go to the AWS Management Console and sign in with your credentials.
<img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/1.png" alt="aws console" width="600" height="500">

2. **Navigate to EC2 Dashboard**: Go to the EC2 dashboard by selecting "Services" from the top menu, then selecting "EC2" under the "Compute" section.

3. **Launch Instance**: Click on the "Launch Instance" button.
<img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/2.png" alt="aws console" width="900" height="500">
<img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/3.png" alt="aws console" width="900" height="500">

4. **Choose an Amazon Machine Image (AMI)**:
   - Select "Ubuntu Server 22.04 LTS (HVM), SSD Volume Type" as the AMI.
<img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/3-1.png" alt="aws console" width="900" height="500">

5. **Choose an Instance Type**:
   - Select an instance type based on requirements.I choosed t2.micro at first but as this project involves many tools I needed to change instance type to "t2.large".So,start by choosing "t2.large".
   **Select Key Pair**:
    - Choose an existing key pair or create a new one. This key pair will be used to SSH into your instance.
<img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/4.png" alt="aws console" width="900" height="500">
<img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/5.png" alt="aws console" width="900" height="500">


6. **Configure Network Settings and Security Group**:
   - Create a new security group. Ensure that SSH (port 22) is open to your IP address for remote access and also HTTPS and HTTP to access it on browser. You may also want to open other ports based on this application requirements.
  <img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/6.png" alt="aws console" width="900" height="500">

7. **Add Storage**:
   - Define the size of the root volume (usually defaults are fine).I added 25GB additional volumes as needed.
<img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/6-1.png" alt="aws console" width="900" height="500">

8. **Review and Launch Instance**:
    - Review your instance configuration and click "Launch" if everything looks correct.
    - Click "Launch Instances" to launch your EC2 instance.
<img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/7.png" alt="aws console" width="900" height="500">

9. **Connect to the EC2 Instance**:
  - After launching EC2.Click "Connect" then choose EC2 instance connect.
<img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/8.png" alt="aws console" width="900" height="500">
 
10. **You're now connected to your EC2 instance**:
   - You can proceed with further setup tasks, such as updating packages and cloning your application's code repository.
   ```bash
     sudo apt-get update
     git clone "https://github.com/avanipatell/Movie-Recommendation-System.git"
   ```
   <img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/9.png" alt="aws console" width="900" height="500">
   <img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/9-1.png" alt="aws console" width="900" height="500">
   <img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/10.png" alt="aws console" width="900" height="500">

11. **Setup Docker and Run Application**:
    - Update Packages, Install Docker, and Configure Permissions**:
    - Replace $USER with your system's username.Adjust Docker permissions (sudo chmod 777 /var/run/docker.sock) carefully for security considerations.
   ```bash
   sudo apt-get update && \
   sudo apt-get install docker.io -y && \
   sudo usermod -aG docker $USER && \
   newgrp docker && \
   sudo chmod 777 /var/run/docker.sock
   ```
   <img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/10-1.png" alt="aws console" width="900" height="500">
   <img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/11.png" alt="aws console" width="900" height="500">

12. **Change Security Group before moving ahead open port for application,Jenkins,Solarqube**
    - Add rules for the following ports:
        Application port (e.g.,4000 is for myapp)
        Jenkins port (default is 8080, but can be customized)
        SonarQube port (default is 9000, but can be customized)
        For each rule, specify the port, protocol (typically TCP), and source (either "Anywhere" to allow access from any IP address or a specific IP range).
   <img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/13.png" alt="aws console" width="900" height="500">
   <img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/13-1.png" alt="aws console" width="900" height="500">

13. **Build Docker Image and Run Container:**
   - Replace MovieRecommendationSystem  with the appropriate image name for your application.
   - Adjust port mapping (-p 4000:80) as needed for your application.
   ```bash
   docker build movie-recommendation-system  -t .
   docker run -d --name movie-recommendation-system -p 4000:80 movie-recommendation-system:latest
   ```
   <img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/12.png" alt="aws console" width="900" height="500">
   <img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/14-1.png" alt="aws console" width="900" height="500">
   <img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/14.png" alt="aws console" width="900" height="500">
   <img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/15.png" alt="aws console" width="900" height="500">

14. **Install SonarQube**
- Pull the SonarQube Docker image.
- Create a Docker container for SonarQube.
- Access SonarQube at `http://<your-ec2-public-ip>:9000`.
- Log in with the default credentials (admin/admin).
- Configure SonarQube according to your requirements.
- Set up projects and quality profiles.
- Integrate SonarQube with your CI/CD pipelines for automated code analysis.
```bash
sudo docker pull sonarqube
sudo docker run -d --name sonarqube -p 9000:9000 sonarqube
```
<img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/16.png" alt="aws console" width="900" height="500">
<img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/16-1.png" alt="aws console" width="900" height="500">
<img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/16-2.png" alt="aws console" width="900" height="500">
<img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/16-3.png" alt="aws console" width="900" height="500">
<img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/17-4.png" alt="aws console" width="900" height="500">

15. **Install Trivy**
- Install Trivy either by downloading the binary or using Docker.
- Run Trivy to scan container images for vulnerabilities.
```bash
sudo apt-get install wget apt-transport-https gnupg lsb-release
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy        
```
<img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/17-1.png" alt="aws console" width="900" height="500">
<img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/17-2.png" alt="aws console" width="900" height="500">
<img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/17-3.png" alt="aws console" width="900" height="500">

16. **Install Jenkins**
```bash
sudo apt update
sudo apt install fontconfig openjdk-17-jre
java -version
openjdk version "17.0.8" 2023-07-18
OpenJDK Runtime Environment (build 17.0.8+7-Debian-1deb12u1)
OpenJDK 64-Bit Server VM (build 17.0.8+7-Debian-1deb12u1, mixed mode, sharing)
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
/etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
sudo systemctl start jenkins
sudo systemctl enable jenkins
```
<img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/18-1.png" alt="aws console" width="900" height="500">
<img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/18-2.png" alt="aws console" width="900" height="500">
<img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/18-3.png" alt="aws console" width="900" height="500">
<img src="https://github.com/avanipatell/DevSecOps-MovieRecommendationSystem/blob/main/images/17-1.png" alt="aws console" width="900" height="500">

