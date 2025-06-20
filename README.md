# DevSecOps Project: Mastering Tools and Practices

This repository documents a comprehensive, step-by-step guide on mastering DevSecOps practices and its associated tools. Prepared by **Shriram Karpoora Sundara Pandian** under the guidance of instructor Shikhar Verma, this document is shared for educational purposes to help others learn DevSecOps and enhance their cybersecurity skills.

## Table of Contents

* [What is DevSecOps?](#what-is-devsecops)
* [Key Tools and Technologies](#key-tools-and-technologies)
* [CI/CD and AWS Overview](#cicd-and-aws-overview)
* [Project Implementation Details](#project-implementation-details)
  * [1. Maven Integration](#1.-maven-integration)
  * [2. SAST (Static Application Security Testing) with SonarQube/SonarCloud](#2.-sast-(static-application-security-testing)-with-sonarqube/sonarcloud)
  * [3. Snyk Integration](#3.-snyk-integration)
  * [4. Building Docker Images with Jenkins](#4.-building-docker-images-with-jenkins)
  * [5. Continuous Deployment (CD) with AWS EKS](#5.-continuous-deployment-(cd)-with-aws-eks)
  * [6. Dynamic Testing with OWASP ZAP](#6.-dynamic-testing-with-owasp-zap)
* [Conclusion and Acknowledgements](#conclusion-and-acknowledgements)

## What is DevSecOps?

DevSecOps integrates security practices into this pipeline, ensuring that security is treated as a shared responsibility throughout the software development lifecycle. By embedding security checks and compliance validations early and frequently, DevSecOps reduces vulnerabilities and enhances application resilience.

## Key Tools and Technologies

This project extensively covers the practical application of the following tools:

* **Jenkins:** Automation server for CI/CD pipeline orchestration.
* **SonarQube/SonarCloud:** Static Application Security Testing (SAST) for code quality and vulnerability analysis.
* **Snyk:** Software Composition Analysis (SCA) for identifying vulnerabilities in third-party libraries and containers, as well as Infrastructure as Code (IaC) security.
* **AWS:** Cloud platform used for hosting EC2 instances, ECR (Elastic Container Registry), and EKS (Elastic Kubernetes Service).
* **ZAP (OWASP Zed Attack Proxy):** Dynamic Application Security Testing (DAST) for finding vulnerabilities in deployed web applications.
* **Docker:** Containerization platform for packaging applications.
* **Kubernetes:** Container orchestration platform (specifically AWS EKS) for managing containerized applications.
* **Maven:** Build automation and project management tool for Java projects.

## CI/CD and AWS Overview

**CI/CD Pipeline:** A CI/CD pipeline (Continuous Integration/Continuous Deployment) is a set of automated processes that enable developers to integrate code changes, test them, and deploy applications seamlessly. It ensures rapid delivery of high-quality software by automating repetitive tasks such as testing, building, and deployment.

**AWS for CI/CD and DevSecOps:** AWS provides an excellent platform for implementing CI/CD pipelines and DevSecOps due to its scalability, comprehensive suite of services, and deep integrations. AWS services like CodePipeline, CodeBuild, and CodeDeploy streamline pipeline automation, while AWS Security Hub, GuardDuty, and IAM offer robust security capabilities. Additionally, AWS's global infrastructure ensures high availability and low-latency deployments, making it a top choice for organizations looking to optimize their development and security workflows.

## Project Implementation Details

This section summarizes the hands-on steps performed in the document.

### 1. Maven Integration

Maven is a build automation and project management tool primarily used in Java-based projects. It simplifies the process of managing project dependencies, building, packaging, and deploying applications. Maven uses an XML file (pom.xml) to describe the project's dependencies, build process, and configuration, enabling consistent and repeatable builds.

**Steps Covered:**
* Kick starting an Amazon EC2 instance (Amazon Linux) and accessing it using Putty or any remote client handler.
* Installing Java 11 and Maven.
* Understanding core Maven commands: `validate`, `compile`, `test`, `package`, `verify`, `install`, `deploy`.
* Cloning a sample Java project and building it with Maven (`maven package`).
* Understanding Maven's dependency management in the `.m2/repository` folder.
* Using `maven clean package` for fresh and clean builds.

### 2. SAST (Static Application Security Testing) with SonarQube/SonarCloud

SonarQube is an open-source platform used for continuous code quality inspection and static code analysis. It helps developers and teams improve code quality and maintainability by detecting code smells, bugs, security vulnerabilities, and technical debt. SonarQube supports a wide range of programming languages and integrates seamlessly with CI/CD pipelines to automate code reviews. We test the static code before it is pushed into deployment.

**Steps Covered:**
* Starting the Jenkins server on AWS using Red Hat Linux with a t2.medium instance.
* Installing Jenkins by adding its repository and then running `dnf install jenkins`.
* Starting Jenkins using `systemctl start jenkins` and ensuring port 8080 is open in AWS inbound rules.
* Unlocking Jenkins by copying the initial admin password from `/var/lib/jenkins/secrets/initialAdminPassword`.
* Installing suggested plugins and creating the first admin user.
* **Maven Integration with Jenkins:**
    * Navigating to `Manage Jenkins > Tools` and adding Maven installations by specifying its home path.
    * Creating a new Jenkins pipeline (e.g., `maven-jenkins-git-pipeline`) and configuring it to check out a Git project with Java code.
    * Adding the `mvn clean package` command to the pipeline script to build the package.
* **SonarCloud Integration:**
    * Logging into SonarCloud and creating an organization and project.
    * Generating a SonarCloud authentication token from `My Account > Security`.
    * Adding this token as a Jenkins credential of type `Secret text`.
    * Updating the Jenkins pipeline script to include a stage for SonarCloud analysis (`mvn clean verify sonar:sonar`).
* **SonarQube (Self-hosted) Integration:**
    * Creating a dedicated Ubuntu 22 EC2 instance for the SonarQube server.
    * Installing Java 11 on the SonarQube server.
    * Downloading and unzipping SonarQube version 9.3.0 to the `/opt` directory.
    * Modifying the `sonar.sh` file to run as the `ubuntu` user and changing SonarQube directory ownership to `ubuntu`.
    * Starting SonarQube using `sh sonar.sh start`.
    * Configuring AWS security group inbound rules for SonarQube (port 9000).
    * Logging into SonarQube (default admin:admin) and changing the password.
    * Installing the SonarQube Scanner plugin in Jenkins.
    * Creating a SonarQube authentication token on the self-hosted server.
    * Adding SonarQube server details and the token as Jenkins credentials.
    * Creating a new Jenkins pipeline and updating the script to include the SonarQube analysis stage pointing to the self-hosted server.

### 3. Snyk Integration

Snyk is a tool to find vulnerabilities and fix them automatically at the code level. Snyk Open Source finds vulnerabilities in third-party libraries. Snyk Container identifies vulnerabilities in Kubernetes and container technologies. Snyk Infrastructure as a Code finds vulnerabilities on AWS, Terraform, and other IaC Platforms.

**Steps Covered:**
* Creating a Snyk account and logging in (e.g., using a GitHub account).
* Confirming that the `pom.xml` file of the project to be analyzed includes the Snyk Maven plugin.
* Obtaining a Snyk authentication token from the Snyk website.
* Adding the Snyk token as a Jenkins credential of type `Secret text` (e.g., with ID `SNYK_TOKEN`).
* Creating a new Jenkins pipeline (e.g., `snyk_maven_pipeline`).
* Updating the Jenkins pipeline script to include a stage for Snyk SCA scan and analysis (`mvn snyk:test -fn`).

### 4. Building Docker Images with Jenkins

Based on the diagram, Docker files are built, and images are deployed to AWS ECR (Elastic Container Registry) for better management. AWS ECR is a fully managed container registry that makes it easy to store, manage, share, and deploy your container images and artifacts anywhere.

**Steps Covered:**
* Creating a private repository in AWS ECR.
* Installing Docker on the Jenkins server VM (RHEL).
* Installing Docker and Amazon ECR Jenkins plugins.
* Updating the Jenkins pipeline script to include stages for building a Docker image (`docker.build registry + ":$BUILD_NUMBER"`) and pushing it to ECR.

### 5. Continuous Deployment (CD) with AWS EKS

Continuous Deployment (CD) is a software development practice where changes to the codebase are automatically deployed to production environments without manual intervention. It is the next step after Continuous Integration (CI) and Continuous Delivery, focusing on delivering software updates directly to end-users as soon as they pass automated testing and quality checks.

**CD Server Setup:**
* Creating a new Ubuntu EC2 instance to act as the CD server.
* Installing Docker and AWS CLI on the CD server.
* Creating an IAM role (e.g., `ec2ecrrole`) with `AmazonEC2ContainerRegistryFullAccess` policy and attaching it to the CD server EC2 instance to allow ECR communication.

**AWS EKS Cluster Creation:**
* Creating an EKS cluster in the AWS console.
* Creating a dedicated IAM role for the EKS cluster (e.g., `eksclusterrole`) with `AmazonEKSClusterPolicy` policy.
* Installing `kubectl` and `eksctl` on a client EC2 instance (e.g., `eks_client`) to interact with the Kubernetes API.
* Configuring `kubectl` to communicate with the EKS cluster (`aws eks update-kubeconfig`).
* Creating an IAM role for EKS Node Groups (e.g., `eksgroup1role`) with `AmazonEKS_CNI_Policy`, `AmazonEKSWorkerNodePolicy`, and `AmazonEC2ContainerRegistryReadOnly` policies.
* Adding a node group to the EKS cluster (worker nodes).

**Application Deployment:**
* Pushing the Docker image from Jenkins to AWS ECR.
* Transferring Kubernetes deployment (`deployment.yaml`) and service (`service.yaml`) files (containing ECR image URI) to the EKS client machine via SSH.
* Creating a Kubernetes secret for ECR authentication (`ecr-key`) on the EKS cluster to allow pulling images from ECR.
* Updating the Jenkins pipeline to include stages for:
    * Transferring `deployment.yaml` and `service.yaml` to the EKS client.
    * Executing `kubectl delete all --all -n securityapp` and `kubectl create -f deployment.yaml -n securityapp`, `kubectl create -f service.yaml -n securityapp` commands remotely on the EKS client to deploy the application.
* Verifying the application is running on the AWS EKS cluster via `kubectl get svc`.
* Accessing the deployed application via its LoadBalancer external IP.

### 6. Dynamic Testing with OWASP ZAP

Dynamic Testing is a software testing methodology that involves executing a program or application to identify defects. It focuses on the behavior of the software during execution, ensuring that it works as intended in a live environment. OWASP ZAP (Zed Attack Proxy) is an open-source web application security testing tool developed by the Open Web Application Security Project (OWASP). It is widely used for dynamic application security testing (DAST) to identify vulnerabilities in web applications.

**Key Features of ZAP:**
* Intercepts and inspects HTTP(S) traffic between the client and server.
* Performs automated scans for security vulnerabilities.
* Includes tools for manual security testing (e.g., fuzzing, forced Browse).
* Integrates with CI/CD pipelines for automated security testing.
* Provides an easy-to-use GUI, API, and command-line interface.

**Steps Covered:**
* Navigating to the EKS client VM and installing Java.
* Installing OWASP ZAP using `wget` and then `chmod +x ZAP.sh` and `./zap.sh -y`.
* Retrieving the dynamically generated application URL from the Kubernetes service.
* Executing a ZAP quick scan from the command line against the deployed application using the dynamically retrieved URL.
* Retrieving the ZAP scan report (`zap_report.html`) from the `/tmp` directory.
* Serving the HTML report using a simple Python HTTP server to view scan results in a web browser.

## Conclusion and Acknowledgements

DevSecOps has so much and a lot to learn and explore, especially playing around with different options on the AWS. This document provides a comprehensive hands-on walkthrough of integrating various security tools and practices into a CI/CD pipeline, showcasing a complete DevSecOps workflow from code build and static analysis to dynamic testing and deployment on Kubernetes. The journey demonstrates the importance of embedding security throughout the software development lifecycle.

Special thanks to Mr. Shikhar Verma for his exceptional guidance and instruction throughout this learning process and for learning and being on the path of DEVSECOPS. Glad you guys learned through this document.
