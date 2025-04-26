# How-to-Isolate-your-Environment-with-Docker
How to Isolate your Environment with Docker before cloning a repo that is part of job interviews or bug bounties competitions

As warned by Patrick Collins, on Section 1, video 3 of the Smart Contracts DevOps & Blockchain Security Course, to securely analyze and audit potentially malicious code from a GitHub repository, you can use Docker to create an **isolated environment**. This ensures that the code cannot access your host machine's sensitive data, files, or internet browsing history. Below is a step-by-step guide to achieve this:

![image](https://github.com/user-attachments/assets/0578c1c7-d73a-4367-bbc2-4d5f7cb53976)

---

### **Step 1: Install Required Tools**
1. **Install Docker**: Download and install Docker Desktop from [docker.com](https://www.docker.com/).
2. **Install VSCode IDE**: Ensure you have Visual Studio Code installed (Optional, but recommended)
3. **Install VSCode Extensions**:
   - **Remote - Containers**: This allows you to work inside a Docker container from VSCode.

---

### **Step 2: Create a Dockerfile for Isolation**
Create a Dockerfile to define the isolated environment. This file contains the instructions to create a Docker Image. For example:

```dockerfile
# Use a minimal base image
FROM node:20-slim

# Set a working directory inside the container
WORKDIR /interview-app

# Copy only necessary files (you will clone the repo later)
COPY .dockerignore ./

# Install Git to clone repositories
RUN apt-get update && apt-get install -y git && apt-get clean

# Disable internet access by default (optional but this protects your saved passwords and access to web browser wallets and online accounts)
LABEL "network.isolation"="true"

# Default command to keep the container running
CMD ["tail", "-f", "/dev/null"]
```

---

### **Step 3: Create a `.dockerignore` File**
To avoid copying unnecessary files into the container, create a `.dockerignore` file in the same directory as the Dockerfile:

```
node_modules
.git
*.log
.env
```

---

### **Step 4: Build the Docker Image**
Build the Docker image using the `docker build` command:

```bash
docker build -t isolated-env .
```

This creates a Docker image named `isolated-env`.

---

### **Step 5: Run the Docker Container**
Run a container from the image with restricted access:

```bash
docker run -it --name isolated-container --network none isolated-env
```

- `--network none`: Disables internet access for the container.
- `-it`: Opens an interactive terminal inside the container.

---

### **Step 6: Clone the GitHub Repository Inside the Container**
Once inside the container, now you can clone the GitHub repository:

```bash
git clone https://github.com/cyfrin/smmartcontractrepo.git
cd smmartcontractrepo
```

This ensures the repository is isolated within the container!!!

---

### **Step 7: Attach VSCode to the Running Container**
1. Open VSCode (or you may have already be using the terminal inside of VSCode for the previous steps).
2. Install the **Remote - Containers** extension (if not already installed).
3. Press `Ctrl+Shift+P` and select **"Remote-Containers: Attach to Running Container"**.
4. Select the `isolated-container` from the list.
5. VSCode will open the container's file system, allowing you to analyze the code securely.

---

### **Step 8: Analyze the Code**
- Open the cloned repository in VSCode.
- Run any commands or scripts inside the container to ensure they cannot affect your host machine.

---

### **Step 9: Close & Discard Environment**
Once you're done analyzing the source code of your security audit or job interview github repo code :
1. Stop the container:
   ```bash
   docker stop isolated-container
   ```
2. Remove the container:
   ```bash
   docker rm isolated-container
   ```

---

### **Why This Approach is Secure to protect you from potential unexpected malicious code**

This approach ensures that whenever you receive  a source code to analyze as part of a job interview or a bug bounty competition you can safely analyze potentially malicious code without risking your host machine's security, as you will have:

* **Isolation**: The container runs in a sandboxed environment, separate from your host machine.
* **No Internet Access**: The `--network none` option ensures the code cannot access the internet.
* **No Host Access**: The container cannot access your host's files unless explicitly enabled.
* **Temporary Environment**: Once the container is removed, all changes inside it are discarded.






