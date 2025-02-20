# Deployment Instructions

## **1️⃣ Executing Terraform Scripts**
Terraform is used to provision the AWS infrastructure.

### **Step 1: Navigate to the Terraform Directory**
```sh
cd terraform
```

### **Step 2: Initialize Terraform**
```sh
terraform init
```
🔹 This sets up Terraform by downloading required providers.

### **Step 3: Apply Terraform Configuration**
```sh
terraform apply -auto-approve
```
🔹 This will create an **EC2 instance**, security groups, and networking configurations in **us-east-1**.

### **Step 4: Retrieve the EC2 Public IP**
Once the infrastructure is provisioned, Terraform outputs the **EC2 public IP**. Use it to access the instance:
```sh
echo "EC2 Public IP: $(terraform output public_ip)"
```

---

## **2️⃣ Running Ansible Playbooks**
Once the EC2 instance is created, configure it using **Ansible**.

### **Step 1: Update Ansible Inventory**
Modify `ansible_hosts` with the correct **EC2 public IP**:
```ini
[development]
<EC2_PUBLIC_IP> ansible_ssh_user=ec2-user ansible_ssh_private_key_file=~/aws/aws_keys/default-ec2.pem
```

### **Step 2: Install Docker on EC2**
```sh
cd ansible
ansible-playbook -i ansible_hosts playbooks/docker-install.yml
```
🔹 This installs **Docker** and required dependencies.

### **Step 3: Deploy the Application**
```sh
ansible-playbook -i ansible_hosts playbooks/docker-run.yml
```
🔹 This runs `docker-compose` on EC2, launching both microservices.

---

## **3️⃣ Accessing the Deployed Application**
Once the deployment is complete, verify the API is accessible.

### **Step 1: SSH into the EC2 Instance**
```sh
ssh -i ~/aws/aws_keys/default-ec2.pem ec2-user@<EC2_PUBLIC_IP>
```

### **Step 2: Check Running Containers**
```sh
docker ps
```
You should see two running containers: `tour-de-france-mvc` and `tour-de-france-db`.

### **Step 3: Test the API**
Run the following command from your local machine or EC2:
```sh
curl http://<EC2_PUBLIC_IP>:3000/tour-de-france/cyclists
```
✅ If successful, this will return JSON data containing cyclists.

---

## **4️⃣ Cleaning Up Resources**
To destroy the provisioned AWS infrastructure, run:
```sh
cd terraform
terraform destroy -auto-approve
```
This removes the EC2 instance and all associated resources.

---

## **Troubleshooting**
| Issue | Solution |
|--------|----------|
| Ansible playbook fails | Ensure `ansible_hosts` contains the correct EC2 IP |
| API returns `Unable to retrieve cyclists` | Check if the database has data (`SELECT * FROM cyclists;`) |
| SSH access denied | Check if you are using the correct AWS key (`chmod 400 default-ec2.pem`) |

---

### **🎉 Deployment Complete!**

