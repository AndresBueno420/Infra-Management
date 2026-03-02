# Distributed Microservices Architecture: DevOps Edition

Welcome to the infrastructure repository for our distributed microservices application. This project demonstrates a complete end-to-end DevOps lifecycle, transforming 5 separate codebases into a cohesive, highly available system using CI/CD pipelines, Infrastructure as Code (IaC), and container orchestration.

## The Engineering Challenge: Hardware Constraints

Most tutorials assume infinite cloud resources. We engineered this solution under strict, real-world constraints: **A physical host machine with only 8GB of RAM.** Deploying 5 microservices, a message broker, a tracing server, and a load balancer using the traditional "one VM per service" approach would have instantly crashed the host OS. Instead, we designed a **High-Density 2-Node Architecture**. We split the ecosystem by resource consumption, strictly allocating 2GB of RAM to each virtual machine:

* **Node 1 (node-apps | 192.168.56.10):** The lightweight traffic gateway. It hosts HAProxy (Load Balancer), the Vue Frontend, the Go Auth API, and the Node.js TODOs API.
* **Node 2 (node-data | 192.168.56.11):** The heavy-lifting backend. It isolates the Java Spring Boot Users API (with strict JVM memory limits applied via Ansible), the Redis Queue, the Python Log Processor, and the Zipkin Tracing Server.

## The Microservices Ecosystem

This infrastructure coordinates 5 separate repositories. GitHub Actions automatically builds and pushes these to GitHub Container Registry (GHCR) upon every commit to the `main` branch.

* [Infra-Management](https://github.com/andresbueno420/Infra-Management) *(You are here)*
* [Users-API (Java/Spring Boot)](https://github.com/andresbueno420/Users-API)
* [Auth-API (Go)](https://github.com/andresbueno420/Auth-API)
* [TODOs-API (Node.js)](https://github.com/andresbueno420/TODOs-API)
* [Log-Message-Processor (Python)](https://github.com/andresbueno420/Log-Message-Processor)
* [Frontend (Vue.js)](https://github.com/andresbueno420/Frontend)

## Tech Stack & Best Practices Implemented

Rather than listing technologies, here is how we used them to solve actual architectural problems:

* **GitHub Actions & GHCR:** We implemented Docker Buildx with layer caching to speed up builds. We also included a bash injection to force repository names to lowercase, preventing Docker's strict naming convention errors.
* **Vagrant & VirtualBox:** Used for predictable, declarative local environments using private networks to secure the database layer.
* **Ansible:** The core orchestrator. We built a completely generic, idempotent `app_deploy` role that handles everything from port mappings to dynamic environment variable injections using the `omit` filter.
* **HAProxy:** Acting as a true Gateway. We bypassed the Frontend's internal proxy and configured HAProxy to intercept all `/api/*` traffic and route it directly to the backend containers.

---

## How to Spin Up the Environment

Ready to see it in action? Follow these steps to deploy the entire infrastructure from scratch. 

**Prerequisites:** Ensure you have Vagrant, VirtualBox, and Ansible installed. *Linux users (especially Ubuntu): Ensure KVM is disabled (`sudo rmmod kvm_amd` or `kvm_intel`) so VirtualBox can use your CPU's virtualization extensions.*

**Step 1: Make Images Public**
Before running the infrastructure, ensure your GitHub Actions have successfully built the images. Go to your GitHub profile's "Packages" section and ensure all 5 GHCR containers are set to "Public" visibility so the VMs can pull them without authentication.

**Step 2: Provision the Hardware**
Navigate to this repository in your terminal and bring up the virtual machines:
`vagrant up`
Wait for VirtualBox to assign the private IPs (192.168.56.10 and .11).

**Step 3: Orchestrate with Ansible**
Extract the dynamic SSH configuration to avoid key errors, then run the master playbook to install Docker, configure HAProxy, and deploy the containers:
`vagrant ssh-config > vagrant-ssh.config`
`ansible-playbook -i ansible/hosts.ini ansible/site.yml --ssh-common-args="-F vagrant-ssh.config -o StrictHostKeyChecking=no"`

**Step 4: Verify and Explore**
Once Ansible finishes with zero failed tasks, the system is alive. Give the Java container a couple of minutes to fully boot, then visit:
* **The Application:** `http://localhost:8080` (Create an account, log in, and add some TODOs!).
* **HAProxy Dashboard:** `http://localhost:8080/haproxy?stats` (Check the health of your clusters).
* **Zipkin Tracing:** `http://192.168.56.11:9411` (Click "Run Query" and explore the "Dependencies" map to see the microservices talking to each other).



