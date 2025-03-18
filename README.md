# Bakery Foundation Example for Linux OS (Python 3.9)

This guide provides step-by-step instructions to create a Linux-based machine image (Amazon AMI) compatible with Python 3.9 using HashiCorp Packer.

---

## Prerequisites

### 1. AWS Account Setup
Before using Packer, you need to create an IAM user and set up necessary permissions:

#### **Step 1: Create an IAM User**
1. Log in to your [AWS Management Console](https://aws.amazon.com/console/).
2. Navigate to **IAM (Identity and Access Management)**.
3. Click on **Users** > **Add user**.
4. Provide a username (e.g., `packer-user`) and enable **Programmatic access**.
   <Screenshot>
   ![image](https://github.com/user-attachments/assets/695e5e3f-0653-4780-9dd0-689a1f8f0421)


#### **Step 2: Attach IAM Policies**
1. Attach the following managed policies:
   - `AmazonEC2FullAccess`
   - `AmazonSSMManagedInstanceCore`
   - `AmazonS3ReadOnlyAccess` (if needed)
2. Click **Create user** and download the **Access Key ID** and **Secret Access Key**.

#### **Step 3: Configure AWS CLI**
1. Install AWS CLI if not already installed:
   ```sh
   sudo apt install awscli  # Ubuntu
   brew install awscli      # macOS
   ```
2. Configure AWS credentials:
   ```sh
   aws configure
   ```
   Enter the Access Key ID and Secret Access Key when prompted.
<Screenshot>
![image](https://github.com/user-attachments/assets/52eb0f61-b12a-47c2-9f4e-c77cffb67fbf)

---

## 2. Install Packer & AWSCLI 

Packer is required to create machine images. Install it as follows:

### **Linux/macOS**
```sh
brew install packer  # macOS
sudo apt install packer  # Ubuntu
```

You can also use the WSL:
![image](https://github.com/user-attachments/assets/12f7cb9c-7add-4e72-a1f9-968719b8de1c)

AWS CLI is required for Packer when you're building Amazon Machine Images (AMIs) because:
1️⃣ Authentication with AWS
Packer needs to authenticate with AWS to create and manage EC2 instances. The AWS CLI helps by:

- Storing Access Keys (via aws configure)
- Managing credentials automatically
Without AWS CLI, you'd have to manually set credentials in environment variables or the Packer template.

2️⃣ IAM Role & Permissions Setup
- AWS CLI lets you easily create IAM users, roles, and policies needed for Packer to function.
- You can attach the required AmazonEC2FullAccess policy or define a custom IAM policy.
  
3️⃣ Interacting with AWS Services
Packer relies on AWS CLI to interact with:
✅ EC2 → Launch & configure instances
✅ SSM → Connect to instances (optional)
✅ S3 → Store artifacts (if needed)
✅ IAM → Verify permissions

Verify the installation:
```sh
packer version
```

---

## 3. Define a Packer Template (`bakery.json`)

Create a Packer template file named `bakery.json` to build a machine image with Python 3.9.

```json
{
  "variables": {
    "aws_region": "us-east-1"
  },
  "builders": [
    {
      "type": "amazon-ebs",
      "region": "{{user `aws_region`}}",
      "source_ami": "ami-0c55b159cbfafe1f0",
      "instance_type": "t2.micro",
      "ssh_username": "ubuntu",
      "ami_name": "bakery-foundation-python39-{{timestamp}}"
    }
  ],
  "provisioners": [
    {
      "type": "shell",
      "inline": [
        "sudo apt-get update",
        "sudo apt-get install -y python3.9 python3.9-venv python3.9-dev"
      ]
    }
  ]
}
```

---

## 4. Initialize and Validate the Packer Template

Run the following commands to initialize and validate the bakery template:

```sh
packer init .
packer validate bakery.json
```

If validation is successful, proceed to the next step.

---
![image](https://github.com/user-attachments/assets/3f1bae49-f3bc-4361-87ee-12f344320191)


## 5. Build the Machine Image

Run the following command to create the AMI:

```sh
packer build bakery.json
```

This process may take a few minutes. Once completed, it will output the **AMI ID** of the newly created machine image.

---
![image](https://github.com/user-attachments/assets/64f9526b-8112-43cb-9868-17fc0aa486cc)
![image](https://github.com/user-attachments/assets/0e8b9f8c-9aac-46a5-b694-c6cb5cc96d99)


## 6. Deploy and Use the Baked Image

### **For AWS**
1. Go to **EC2 Console** > **AMIs**.
2. Find the created AMI.
3. Launch an EC2 instance from the AMI.

![image](https://github.com/user-attachments/assets/83ad6b03-d072-4b38-af5c-ef69db480332)



## Conclusion

By following this Bakery Foundation approach, you ensure that all deployments start with a pre-configured Linux image containing Python 3.9. This improves consistency and eliminates configuration drift across different environments.

Would you like an example for Docker-based baking instead? 🚀

