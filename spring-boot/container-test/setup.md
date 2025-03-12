
## **Step-by-Step Instructions for Configuring Docker in WSL and IntelliJ IDEA**

### **1. Install Docker in WSL**
1. **Update the Package List:**
   ```bash
   sudo apt update
   ```

2. **Install Docker:**
   ```bash
   sudo apt install docker.io
   ```

3. **Start and Enable the Docker Daemon:**
   ```bash
   sudo systemctl start docker
   sudo systemctl enable docker
   ```

4. **Verify Docker Installation:**
   ```bash
   docker --version
   ```
   Ensure it outputs the installed Docker version.

5. **Test Docker:**
   Run a test container to confirm Docker is working:
   ```bash
   sudo docker run hello-world
   ```

---

### **2. Allow Non-Root User to Use Docker**
1. Add your user to the `docker` group:
   ```bash
   sudo usermod -aG docker $(whoami)
   ```

2. Restart your WSL session for the changes to take effect:
   ```bash
   exit
   wsl
   ```

3. Verify your user is in the `docker` group:
   ```bash
   groups
   ```
   You should see `docker` in the output.

4. Test Docker without `sudo`:
   ```bash
   docker ps
   ```
   If it works without errors, you're good to go.

---

### **3. Configure IntelliJ IDEA to Use Docker**
1. **Open IntelliJ IDEA:**
   - Launch IntelliJ IDEA and open your project.

2. **Set Up Docker Integration:**
   - Go to `File > Settings > Build, Execution, Deployment > Docker`.
   - Click the `+` icon to add a new Docker configuration.
   - Select **Unix Socket** as the connection type.
   - Set the Docker socket path to:
     ```plaintext
     unix:///var/run/docker.sock
     ```

3. **Test the Connection:**
   - Click **Test Connection** to ensure IntelliJ can communicate with Docker.
   - If the connection is successful, proceed to the next step.

4. **Restart IntelliJ IDEA:**
   - If IntelliJ IDEA was running while you configured Docker in WSL, **restart IntelliJ IDEA** to ensure it picks up the updated environment and permissions.

---

### **4. Troubleshooting**
1. **Permission Errors:**
   - Ensure your user is in the `docker` group:
     ```bash
     groups
     ```
   - Check the Docker socket permissions:
     ```bash
     ls -l /var/run/docker.sock
     ```
     It should look like this:
     ```
     srw-rw---- 1 root docker 0 Mar 10 06:09 /var/run/docker.sock
     ```
     If not, fix it with:
     ```bash
     sudo chown root:docker /var/run/docker.sock
     sudo chmod 660 /var/run/docker.sock
     ```

2. **Docker Daemon Not Running:**
   - Check the status of the Docker daemon:
     ```bash
     sudo systemctl status docker
     ```
   - Start it if necessary:
     ```bash
     sudo systemctl start docker
     ```

3. **IntelliJ Cannot Connect to Docker:**
   - Ensure the Docker socket path is correct (`unix:///var/run/docker.sock`).
   - Verify that Docker is running and accessible from the terminal.

