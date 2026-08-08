# Jenkins Installation & Setup – Ubuntu

## Overview

This document covers the basic installation and setup of Jenkins on an Ubuntu/Linux server, along with commands to explore the Jenkins environment.

## 1. Check Environment

```bash
cat /etc/os-release
uname -a
free -h
df -h
nproc
hostname -I
```

## 2. Install Java

Jenkins requires a supported Java version.

```bash
sudo apt update
sudo apt install -y fontconfig openjdk-21-jre
```

Verify:

```bash
java -version
```

## 3. Install Jenkins

Add the Jenkins repository:

```bash
sudo mkdir -p /etc/apt/keyrings

sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2026.key

echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | \
  sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
```

Install Jenkins:

```bash
sudo apt update
sudo apt install -y jenkins
```

## 4. Start Jenkins

```bash
sudo systemctl enable --now jenkins
```

Check status:

```bash
sudo systemctl status jenkins
```

Jenkins normally runs on port `8080`.

```bash
sudo ss -lntp | grep 8080
```

## 5. Initial Setup

Open Jenkins in a browser:

```text
http://<server-ip>:8080
```

Get the initial administrator password:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Complete the setup wizard, install the suggested plugins, and create the administrator account.

## 6. Explore Jenkins Environment

### Jenkins Home

```bash
sudo ls -la /var/lib/jenkins
```

Important directories:

```text
/var/lib/jenkins/
├── jobs/
├── plugins/
├── users/
├── nodes/
├── secrets/
└── workspace/
```

### Jenkins User

```bash
id jenkins
```

### Jenkins Logs

```bash
sudo journalctl -u jenkins -f
```

### Jenkins Version

```bash
dpkg -l | grep jenkins
```

## 7. Explore from Jenkins UI

After login, explore:

* **Dashboard** – Jobs and builds
* **Manage Jenkins** – System configuration
* **Plugins** – Installed plugins
* **Credentials** – Secrets and authentication
* **Nodes/Agents** – Build execution environments
* **System Information** – Java, OS, and environment details

## 8. Create a Test Pipeline

Create a Pipeline job and use:

```groovy
pipeline {
    agent any

    stages {
        stage('Environment') {
            steps {
                sh '''
                    whoami
                    hostname
                    java -version
                    pwd
                    df -h
                    free -h
                    env | sort
                '''
            }
        }
    }
}
```

Check the **Console Output** to understand the environment in which Jenkins executes builds.

## 9. Useful Commands

| Purpose         | Command                          |
| --------------- | -------------------------------- |
| Jenkins status  | `sudo systemctl status jenkins`  |
| Restart Jenkins | `sudo systemctl restart jenkins` |
| Jenkins logs    | `sudo journalctl -u jenkins -f`  |
| Check port      | `sudo ss -lntp \| grep 8080`     |
| Jenkins home    | `ls -la /var/lib/jenkins`        |
| Java version    | `java -version`                  |
| Jenkins user    | `id jenkins`                     |
| Disk usage      | `df -h`                          |
| Memory          | `free -h`                        |

## References

* [Jenkins Documentation](https://www.jenkins.io/doc/?utm_source=chatgpt.com)
* [Jenkins Linux Installation](https://www.jenkins.io/doc/book/installing/linux/?utm_source=chatgpt.com)

