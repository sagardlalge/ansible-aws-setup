# Ansible Installation and Configuration on AWS

This guide provides step-by-step instructions to set up **Ansible** for managing AWS EC2 instances. We'll configure one master and two worker nodes.

---

## **Step 1: Launch EC2 Instances**
1. Launch 3 EC2 instances:
   - **1 Master**
   - **2 Workers**
2. Choose an appropriate AMI (e.g., Ubuntu 20.04 or 22.04).
3. Configure the security group to allow:
   - **SSH** (port 22) access.
   - **ICMP** for ping testing.
4. Note down the public/private IPs of the instances.

---

## **Step 2: Install Ansible on the Master Node**
1. SSH into the master instance:
   ```bash
   ssh -i <your-key.pem> ubuntu@<master-ip>
   ```
2. Run the following commands to install Ansible:
   ```bash
   sudo apt update
   sudo apt install software-properties-common
   sudo add-apt-repository --yes --update ppa:ansible/ansible
   sudo apt update
   sudo apt install ansible
   ```

---

## **Step 3: Install Python3 on Worker Nodes**
1. SSH into each worker node:
   ```bash
   ssh -i <your-key.pem> ubuntu@<worker-ip>
   ```
2. Run the following commands on each worker node:
   ```bash
   sudo apt-get update
   sudo apt install python3
   python3 --version
   ```

---

## **Step 4: Configure Keyless SSH Access**
1. On the master node, generate an SSH key pair:
   ```bash
   ssh-keygen
   ```
   - Press `Enter` to save the key in the default location (`~/.ssh/id_rsa`).
2. Copy the public key to each worker node:
   ```bash
   cat ~/.ssh/id_rsa.pub
   ```
   - Copy the content of the `id_rsa.pub` file.
3. On each worker node:
   - Open the `authorized_keys` file:
     ```bash
     nano ~/.ssh/authorized_keys
     ```
   - Paste the copied public key into the file and save it.
   - Set proper permissions:
     ```bash
     chmod 600 ~/.ssh/authorized_keys
     ```

---

## **Step 5: Configure Ansible Hosts File**
1. On the master node, open the Ansible hosts file:
   ```bash
   sudo nano /etc/ansible/hosts
   ```
2. Add the worker nodes at the bottom of the file:
   ```ini
   [dev]
   node1 ansible_ssh_host=<worker1-ip>
   node2 ansible_ssh_host=<worker2-ip>
   ```
   - Replace `<worker1-ip>` and `<worker2-ip>` with the actual private IPs of the worker nodes.

---

## **Step 6: Test Ansible Connection**
1. Test connectivity to all nodes:
   ```bash
   ansible -m ping all
   ```
2. Test connectivity to the `dev` group:
   ```bash
   ansible -m ping dev
   ```
3. Test individual nodes:
   ```bash
   ansible -m ping node1
   ansible -m ping node2
   ```

---

## **Expected Output**
On successful configuration, you should see output similar to:
```json
node1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3.10"
    },
    "changed": false,
    "ping": "pong"
}
node2 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3.10"
    },
    "changed": false,
    "ping": "pong"
}
```

---

Your Ansible setup is now ready for managing your AWS instances!
