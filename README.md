# Jenkins-log.sh
# Setting Up Jenkins on AWS with GitHub

This guide provides a concise process to set up a Jenkins instance on AWS and integrate it with a GitHub repository.

## Setup Steps

### Step 1: Launch and Prepare EC2 Instance

1. **Launch EC2**:
   - AMI: Amazon Linux 2 or Ubuntu.
   - Instance Type: `t2.micro` (Free Tier eligible).
   - Key Pair: Select or create a key pair.
   - Security Group: Allow ports 22 (SSH), 80 (HTTP), 443 (HTTPS), and 8080 (Jenkins).
   - Storage: At least 10GB.
2. **Note Public IP** of the instance.
3. **SSH into Instance**:
   ```bash
   ssh -i /path/to/your-key.pem ec2-user@<public-ip-address>
   ```

### Step 2: Install and Configure Jenkins

1. **Update System Packages**:
   ```bash
   sudo yum update -y
   ```
   For Ubuntu:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```
2. **Install Java**:
   ```bash
   sudo yum install java-11-openjdk-devel -y
   ```
   For Ubuntu:
   ```bash
   sudo apt install openjdk-11-jdk -y
   ```
3. **Add Jenkins Repository and Install Jenkins**:
   ```bash
   sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
   sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
   sudo yum install jenkins -y
   ```
   For Ubuntu:
   ```bash
   curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
   echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
   sudo apt update
   sudo apt install jenkins -y
   ```
4. **Start Jenkins**:
   ```bash
   sudo systemctl start jenkins
   sudo systemctl enable jenkins
   ```

### Step 3: Access and Configure Jenkins

1. Navigate to `http://<public-ip-address>:8080`.
2. Retrieve Admin Password:
   ```bash
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```
3. Complete the setup wizard and create an admin user.
4. **Integrate GitHub**:
   - Install "Git" and "GitHub Integration" plugins.
   - Add GitHub credentials via **Manage Jenkins > Manage Credentials**.
   - Create a Jenkins job using Git as the source code management tool.
5. **Add GitHub Webhook**:
   - Go to **GitHub Repo > Settings > Webhooks**.
   - URL: `http://<public-ip>:8080/github-webhook/`.
   - Content Type: `application/json`.
   - Enable "GitHub hook trigger for GITScm polling" in Jenkins job.

## Tips

- **Logs**: Check Jenkins logs for issues:
  ```bash
  sudo journalctl -u jenkins
  ```
- **Backup**: Use S3 for Jenkins data backup.
- **HTTPS**: Configure HTTPS using an ALB or Let's Encrypt.



