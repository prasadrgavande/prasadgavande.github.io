---
layout: post
title: Run Kubernetes Locally - Minikube & Docker on Windows
date: 2026-04-13 15:09:00
description: Tutorial for Minikbue installation
tags: DevOps, Kubernates
categories: devops
featured: false
---
<img class="img-fluid" src="https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/10-GettingStartedWithMinikube/minikube-banner.png?raw=true" />

Minikube is a lightweight tool that lets you run a single‑node Kubernetes cluster locally on your laptop, which is perfect for learning Kubernetes and testing workloads before you deploy to a real cluster.
In this guide, we will walk step by step through installing Minikube on Windows 11 using Docker Desktop as the container runtime and then opening the Kubernetes Dashboard UI.

---

## What Is Minikube (and Why Use It)?

Minikube creates a local Kubernetes cluster on your machine so you can experiment without provisioning cloud infrastructure or a multi‑node setup.
By default it starts a single‑node cluster that behaves like any other Kubernetes cluster, so you can use real `kubectl` commands, manifests, and dashboards for development and learning

Typical use cases:

- Learning core Kubernetes concepts (pods, deployments, services).  
- Developing and testing applications locally before pushing to a remote cluster.
- Practicing administration tasks such as scaling, rolling updates, and monitoring.

---

## Environment Used in This Guide

This post assumes the following environment:

- Windows 11 (64‑bit)  
- Docker Desktop installed and running  
- Minikube installed via Chocolatey  
- `kubectl` (Kubernetes CLI) installed via Chocolatey  
- Kubernetes Dashboard accessed via `minikube dashboard`

You can adapt the steps for Windows 10 as long as the system meets Minikube’s CPU, memory, and virtualization requirements.

---

## Check Prerequisites

Before installing Minikube, verify a few basic requirements.

### Verify virtualization and hypervisor

Minikube needs hardware virtualization (Intel VT‑x / AMD‑V) enabled in BIOS, and Docker Desktop on Windows usually relies on WSL2, which also requires virtualization support.

1. Open **Command Prompt** as **Administrator**.  
2. Run:

   ```cmd
   systeminfo
   ```

3. In the output, look for lines such as:

   - `Virtualization-based security: Status: Running`  
   - `A hypervisor has been detected. Features required for Hyper-V will not be displayed.`  

![Checking virtualization with systeminfo](https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/10-GettingStartedWithMinikube/1-hyper-v-requirement.png?raw=true)

This confirms that virtualization is enabled and a hypervisor is present.

### Install and start Docker Desktop

The Docker driver for Minikube uses an existing Docker installation instead of creating its own VM, which is recommended on Windows.

1. Download Docker Desktop for Windows from the official Docker website and install it (enable WSL2 integration during setup if prompted).  
2. After installation, start **Docker Desktop** and wait until the status shows **Running**.

You will later see Minikube appear as a container inside Docker Desktop once the cluster is up.

---

## Install Chocolatey (Windows Package Manager)

Chocolatey is a popular package manager on Windows that makes it easy to install command‑line tools such as `kubectl` and Minikube.

1. Open **PowerShell** as **Administrator** (right‑click → *Run as administrator*).  
2. Run the following script to install Chocolatey:

   ```powershell
   Set-ExecutionPolicy Bypass -Scope Process -Force;
   [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072;
   iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
   ```

![Running the Chocolatey install script](https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/10-GettingStartedWithMinikube/2-install-choco.png?raw=true)

3. When the script finishes, you should see a message indicating that Chocolatey has been installed successfully.  
4. Close and reopen your **Administrator Command Prompt** or **PowerShell** so the `choco` command is available.

---

## Install `kubectl` Using Chocolatey

`kubectl` is the standard command‑line tool for interacting with Kubernetes clusters.

1. In **Command Prompt** (Administrator), run:

   ```cmd
   choco install kubernetes-cli
   ```

![Installing kubernetes-cli with Chocolatey (download in progress)](https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/10-GettingStartedWithMinikube/3-choco-install-1.png?raw=true)

2. Accept the license and let Chocolatey download and install the `kubernetes-cli` package.  
3. When installation is complete you should see a success message.

![kubernetes-cli installed successfully](https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/10-GettingStartedWithMinikube/5-choco-install-3.png?raw=true)

4. Verify the installation:

   ```cmd
   kubectl version --client
   ```

![Verifying kubectl client version](https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/10-GettingStartedWithMinikube/6-verify-kubectl.png?raw=true)

You should see the client version printed without any errors.

---

## Prepare the Kubernetes Config Folder

Kubernetes stores cluster connection information in a **kubeconfig** file, usually located at `C:\Users\<username>\.kube\config` on Windows. When Minikube starts, it automatically populates this file with the correct cluster and credentials.

1. In **Command Prompt** (Administrator), switch to your user profile:

   ```cmd
   cd %USERPROFILE%
   ```

2. Create the `.kube` directory if it does not exist:

   ```cmd
   mkdir .kube
   ```

![Creating the .kube folder from command line](https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/10-GettingStartedWithMinikube/7-createKubeFolder.png?raw=true)

3. (Optional) Create an empty `config` file so that you can see it in Explorer:

   - Open File Explorer and navigate to `C:\Users\<YourUserName>\.kube`.  
   - Create a new file named `config` (no extension).

![Empty kubeconfig file created under .kube](https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/10-GettingStartedWithMinikube/8-createConfig.png?raw=true)

Minikube will later update this `config` file with the cluster details when the cluster starts.

---

## Install Minikube Using Chocolatey

Now you are ready to install Minikube itself.

1. In **Command Prompt** (Administrator), run:

   ```cmd
   choco install minikube
   ```

![Downloading Minikube via Chocolatey](https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/10-GettingStartedWithMinikube/9-installMinikube.png?raw=true)

2. Chocolatey will download the Minikube binary and place it in a directory on your `PATH`.  
3. When the installation completes, you should see a message similar to:

![Minikube installed successfully](https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/10-GettingStartedWithMinikube/10-installMinikube.png?raw=true)

4. Verify the installation:

   ```cmd
   minikube version
   ```

You should see a version string for Minikube, confirming it is installed.

---

## Start Minikube with the Docker Driver

Minikube supports several “drivers” that control where the Kubernetes node actually runs (Docker, Hyper‑V, VirtualBox, etc.). Here we will use the **Docker driver**, which runs Kubernetes inside a Docker container.

> Make sure Docker Desktop is running before this step.

1. In **Command Prompt** (Administrator), start Minikube:

   ```cmd
   minikube start --driver=docker
   ```

   

2. The first start may take a few minutes. Minikube will download the base Kubernetes image, create the Minikube control‑plane node, and configure all the components.

3. At the end, you should see a message similar to:

   > Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default

![Minikube cluster created and kubectl configured](https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/10-GettingStartedWithMinikube/12-minikube-configured.png?raw=true)

This means your local Kubernetes cluster is up and your kubeconfig has been updated.

---

## Verify the Cluster and Check Docker Desktop

### Confirm the Kubernetes node

Run:

```cmd
kubectl get nodes
```

You should see a node named `minikube` with status `Ready`, confirming that the control‑plane node is up and responding.

### Check the Minikube container in Docker Desktop

Open **Docker Desktop** and go to the **Containers** tab. You should see a running container named `minikube` managed by Docker Desktop.
<img src = "https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/10-GettingStartedWithMinikube/12a-Minikube-in-docker.png?raw=true" class="img-fluid" />

Minikube uses this container as the single Kubernetes node in your cluster.

---

## Open the Kubernetes Dashboard

Minikube ships with integrated support for the Kubernetes Dashboard — a web UI that lets you visualize resources, deploy apps, and manage your cluster.

1. In **Command Prompt**, run:

   ```cmd
   minikube dashboard
   ```

<img src="https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/10-GettingStartedWithMinikube/13-minikube-dashboard-cmd.png?raw=true" class="img-fluid" />

2. Minikube will enable the dashboard add‑on if needed, start a local HTTP proxy, and open your default browser to a URL similar to:

   ```text
   http://127.0.0.1:58301/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/
   ```

3. In the Dashboard UI, make sure the namespace is set to `default`. On a fresh cluster, you will see a message like **“There is nothing to display here”** because no workloads are deployed yet.

<img src="https://github.com/prasadrgavande/prasadgavande.github.io/blob/master/assets/img/10-GettingStartedWithMinikube/14-dashboard.png?raw=true" class="img-fluid" />

You can keep this dashboard open while you deploy workloads; it will update as new pods, deployments, and services are created.

> To stop the proxy (but leave the cluster running), press `Ctrl + C` in the window where `minikube dashboard` is running.

---

## Deploy Your First Application 

To actually *see* something in the dashboard, let’s deploy a simple NGINX web server.

1. Create a deployment:

   ```cmd
   kubectl create deployment hello-nginx --image=nginx
   ```

2. Expose the deployment as a service:

   ```cmd
   kubectl expose deployment hello-nginx --type=NodePort --port=80
   ```

3. Check resources:

   ```cmd
   kubectl get pods
   kubectl get svc
   ```

4. Refresh the **Kubernetes Dashboard**. You should now see your deployment, pods, and service in the `default` namespace.

5. To access the service from your browser, you can let Minikube open a tunnel to the NodePort:

   ```cmd
   minikube service hello-nginx
   ```

Minikube will open the service URL in your browser so you can see the default NGINX welcome page.

---

## Managing Your Minikube Cluster

Here are some useful day‑to‑day Minikube commands:

```cmd
minikube status           # Show cluster status
minikube stop             # Gracefully stop the local cluster
minikube start            # Start it again (reuses existing files/images)
minikube delete           # Delete the cluster and all associated data
kubectl config get-contexts
kubectl config use-context minikube
```

These commands let you control the lifecycle of your local Kubernetes cluster without affecting other contexts or remote clusters.

---

## Wrapping Up

You now have:

- Docker Desktop running as your container runtime.  
- Minikube installed via Chocolatey and configured to use the Docker driver.  
- A working local Kubernetes cluster accessible via `kubectl`.  
- The Kubernetes Dashboard UI open in your browser for visual management.  

This is a solid foundation for further Kubernetes learning, such as exploring namespaces, ConfigMaps, Secrets, deployments, and ingress, all from the comfort of your local machine. 
