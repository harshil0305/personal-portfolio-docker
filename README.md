# Personal Portfolio Website Deployment Using Docker on AWS EC2

A beginner-friendly cloud deployment project that packages a static
HTML/CSS portfolio website into a Docker container and hosts it on an
AWS EC2 instance.

## Project Overview

This project demonstrates how to build a static portfolio website,
containerize it with Docker, and deploy it on an Amazon EC2 virtual
server. Once deployed, the website can be accessed through the EC2
instance's public IPv4 address.

## Objectives

-   Create a static portfolio website using HTML and CSS.
-   Package the website in a Docker image.
-   Run the website in a Docker container.
-   Deploy the container on AWS EC2.
-   Configure network access so visitors can reach the website over
    HTTP.
-   Practice basic Linux, Docker, SSH, and cloud deployment skills.

## Technologies Used

-   **HTML5** --- website structure
-   **CSS3** --- website styling
-   **Docker** --- builds and runs the container
-   **AWS EC2** --- virtual server hosting
-   **Amazon Linux 2023** --- EC2 operating system
-   **SSH and SCP** --- secure server access and file transfer
-   **PowerShell** --- commands from Windows

## Architecture

``` text
Visitor's Browser
       |
       | HTTP (Port 80)
AWS EC2 Public IPv4 Address
       |
EC2 Security Group
       |
Docker Container (Port 80)
       |
Static Portfolio Website (HTML/CSS)
```

## Project Files

``` text
personal-portfolio-docker/
├── index.html
├── style.css
└── Dockerfile
```

> The filenames above should match the files in your own project folder.
> If your CSS file or Dockerfile has a different name, update the
> commands accordingly.

## Prerequisites

-   An AWS account
-   Docker installed on your local computer if you want to test locally
-   An EC2 instance running Amazon Linux 2023
-   An EC2 key pair saved securely as a `.pem` file
-   Security group rules for SSH (port 22) from your own IP and HTTP
    (port 80) for website access

## Run Locally with Docker

Open a terminal in the project directory, where the `Dockerfile` is
located.

Build the image:

``` bash
docker build -t personal-portfolio .
```

Run the container locally:

``` bash
docker run -d --name portfolio-local -p 8080:80 personal-portfolio
```

Open `http://localhost:8081` in your browser.

To inspect or stop the local container:

``` bash
docker ps
docker logs portfolio-local
docker stop portfolio-local
```

If a container named `portfolio-local` already exists, inspect it or
choose a different container name before running another one.

## Deploy on AWS EC2

### 1. Launch the EC2 instance

-   Select Amazon Linux 2023.
-   Select an instance type eligible for your account and budget.
-   Choose your EC2 key pair.
-   Enable a public IPv4 address if you want to access the site directly
    over the internet.

### 2. Configure the security group

Add these inbound rules:

  Type     Port Source        Purpose
  SSH        22 My IP         Secure administrator access
  HTTP       80 `0.0.0.0/0`   Public website access

Only expose the ports you need. Restrict SSH to your own IP address
rather than allowing it from everywhere.

### 3. Connect to EC2 from Windows PowerShell

Update the key path and public IP address if yours are different:

``` powershell
ssh -i "$C:\Users\harsh\Downloads\portfolio-key.pem" ec2-user@3.111.58.241
```

### 4. Install and start Docker on EC2

Run these commands after connecting to the EC2 terminal:

``` bash
sudo dnf update -y
sudo dnf install docker -y
sudo systemctl enable --now docker
sudo systemctl status docker
```

Confirm Docker is installed:

``` bash
sudo docker --version
```

### 5. Copy the project files to EC2

Run this command in a **second Windows PowerShell window**, from the
local project directory. Replace the IP address if needed:

``` powershell
scp -i "$C:\Users\harsh\Downloads\portfolio-key.pem" index.html style.css Dockerfile ec2-user@3.111.58.241:~/personal-portfolio-docker/
```

Create the destination directory on EC2 first if it does not exist:

``` bash
mkdir -p ~/personal-portfolio-docker
```

Then verify the files on EC2:

``` bash
ls -l ~/personal-portfolio-docker
```

### 6. Build the image on EC2

In the EC2 terminal:

``` bash
cd ~/personal-portfolio-docker
sudo docker build -t personal-portfolio .
```

### 7. Run the website container

``` bash
sudo docker run -d \
  --name portfolio-website \
  --restart unless-stopped \
  -p 80:81 \
  personal-portfolio
```

If Docker reports that the container name is already in use, inspect the
existing container instead of deleting it:

``` bash
sudo docker ps -a
```

If the existing container is the one you want to use, start it:

``` bash
sudo docker start portfolio-website
```

### 8. Open the website

Visit the following address in your browser, replacing the placeholder
with your instance's current public IPv4 address:

``` text
http://3.111.58.241
```

For the project instance used during setup, the public IP was
`3.111.58.241`. Public IP addresses can change after an instance is
stopped and started unless a stable address is configured.

## Testing and Verification

Run these commands on EC2:

Check Docker:

``` bash
sudo systemctl is-active docker
sudo docker --version
```

Check the container:

``` bash
sudo docker ps
```

Test the local web server:

``` bash
curl -I http://localhost
```

A response such as `HTTP/1.1 200 OK` indicates that the web server is
responding successfully. Exact headers can vary.

Inspect container logs:

``` bash
sudo docker logs portfolio-website
```

Restart the container to verify it starts again:

``` bash
sudo docker restart portfolio-website
sudo docker ps
```

Finally, refresh the public website in your browser.

## Results

The static portfolio website was successfully deployed in a Docker
container on AWS EC2 and made accessible through the instance's public
IPv4 address. Local HTTP checks, container status, and browser access
can be used to verify the deployment.

-   `screenshots/ec2-instance.png`
-   `screenshots/docker-container.png`
-   `screenshots/portfolio-website.png`

## Security and Cost Notes

-   Never upload your `.pem` private key to GitHub.
-   Restrict SSH access to your own IP address.
-   HTTP traffic is not encrypted. For a production website, configure
    HTTPS with a domain and TLS certificate.
-   Check current AWS pricing and your account's Free Tier eligibility.
    EC2, public IPv4 addresses, storage, and data transfer may incur
    charges.
-   Stop or terminate resources when they are no longer needed, after
    saving any files you want to keep.

## Future Enhancements

-   Add a custom domain name.
-   Configure HTTPS.
-   Set up automatic deployment using GitHub Actions.
-   Improve responsive design for mobile devices.
-   Add a contact form or backend service.
-   Explore Amazon S3 and CloudFront as an alternative for hosting a
    static website.

## Learning Outcomes

-   Creating a static website with HTML and CSS
-   Building and running Docker images and containers
-   Using SSH and SCP to work with a Linux server
-   Installing and managing Docker on Amazon Linux
-   Configuring EC2 security groups and public access
-   Testing and troubleshooting a cloud-hosted website

## Author

**Harshil Suvagiya**
**github.com/harshil0305**  
