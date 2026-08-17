# CLOUD STORAGE CREATION (S3) AND LAUNCHING AN (EC2) INSTANCE IN AWS
## NAME: MAHASRI D
## REG NO: 212224220058


## Aim

To launch an Amazon EC2 instance running a simple web server, protect it from accidental termination and stopping, observe it using EC2 and CloudWatch monitoring tools, expose it to the internet by correctly configuring a security group, resize its compute and storage resources on demand, and understand the default service quotas that govern EC2 usage in a region.


## Algorithm

1. **Launch** an Amazon Linux 2023, t2.micro EC2 instance named `Web Server`, in the Lab VPC / PublicSubnet1, with a new security group, default 8 GiB storage, **termination protection enabled**, and a user-data script that installs and starts an Apache (`httpd`) web server.
2. **Monitor** the instance's status checks, CloudWatch metrics, system log, and instance screenshot to confirm it booted and configured correctly.
3. **Update** the security group to allow inbound HTTP (port 80) traffic from anywhere, then verify the web page loads at the instance's public IPv4 address.
4. **Resize** the instance: stop it, change the instance type from t2.micro to t2.small, enable stop protection, increase the EBS root volume from 8 GiB to 10 GiB, then restart it.
5. **Explore** Service Quotas for EC2 to understand the default limits on running on-demand instances per region.
6. **Test** stop protection by attempting to stop the protected instance (expect failure), then disable stop protection and stop the instance successfully.


## Steps

### Step 1 — Launch your Amazon EC2 instance
- Open the EC2 console and confirm the region is **N. Virginia (us-east-1)**.
- Choose **Launch instance**, name it `Web Server`.
- Keep the default **Amazon Linux 2023** AMI and **t2.micro** instance type.
- Select the `vockey` key pair.
- Under Network settings, choose **Lab VPC**, keep **PublicSubnet1**, and create a new security group named `Web Server security group` (remove the default inbound rule).
- Keep the default 8 GiB storage.
- Under Advanced details, set **Termination protection → Enable**.
- Add the following user data script:
```bash
  #!/bin/bash
  dnf install -y httpd
  systemctl enable httpd
  systemctl start httpd
  echo '<html><h1>Hello From Your Web Server!</h1></html>' > /var/www/html/index.html
```
- Launch the instance and wait for **Running** state with **2/2 status checks passed**.

<img width="1919" height="1067" alt="Screenshot 2026-08-16 204314" src="https://github.com/user-attachments/assets/8b199d97-bcab-42c3-b947-05a86ebb29c7" />


### Step 2 — Monitor your instance
- Check the **Status checks** tab (System reachability + Instance reachability).
- Review the **Monitoring** tab for CloudWatch metrics.
- Use Actions → Monitor and troubleshoot → **Get system log** to confirm httpd installed via user data.
- Use Actions → Monitor and troubleshoot → **Get instance screenshot** for a visual health check.

<img width="952" height="921" alt="Screenshot 2026-08-16 204357" src="https://github.com/user-attachments/assets/6b72e0ce-508d-4445-8c0c-d254248cce99" />

<img width="658" height="700" alt="image" src="https://github.com/user-attachments/assets/9fd29e84-a669-4e0d-858b-7efbcac165ca" />


### Step 3 — Update security group & access the web server
- Copy the instance's **Public IPv4 address** and open it in a browser — it fails (no inbound rule for port 80 yet).
- Go to **Security Groups** → `Web Server security group` → **Inbound rules**.
- Add rule: Type `HTTP`, Source `Anywhere-IPv4` → Save.
- Refresh the browser tab — it now shows **"Hello From Your Web Server!"**

<img width="1919" height="1143" alt="Screenshot 2026-08-16 205017" src="https://github.com/user-attachments/assets/f6654974-761f-46a2-8058-358a0291c741" />

<img width="979" height="332" alt="Screenshot 2026-08-16 205431" src="https://github.com/user-attachments/assets/f954e4b8-7008-4c71-9504-e532fe65b798" />


### Step 4 — Resize instance type & EBS volume
- Stop the instance.
- Actions → Instance settings → **Change instance type** → `t2.small` → Apply.
- Actions → Instance settings → **Change stop protection** → Enable → Save.
- Storage tab → select volume → Actions → **Modify volume** → size `10` GiB → Modify → confirm.
- Start the instance again.

<img width="956" height="1117" alt="Screenshot 2026-08-16 211139" src="https://github.com/user-attachments/assets/bd889b10-cfe3-48a0-8b24-07e53f3aad29" />

<img width="718" height="390" alt="image" src="https://github.com/user-attachments/assets/114f9847-170f-4015-830d-13d64c0c7501" />


### Step 5 — Test stop protection
- Try Instance state → **Stop instance** → fails with a `disableApiStop` error, confirming stop protection works.
- Actions → Instance settings → **Change stop protection** → disable → Save.
- Stop the instance again — it now stops successfully.

<img width="1919" height="1016" alt="Screenshot 2026-08-16 213014" src="https://github.com/user-attachments/assets/81739b4d-796f-427a-9e62-63326602345f" />

<img width="1919" height="903" alt="Screenshot 2026-08-16 212436" src="https://github.com/user-attachments/assets/d87e83fa-c278-4bb3-bd93-aa47aec9613e" />




## Result

An Amazon EC2 instance running Amazon Linux 2023 was successfully launched, secured, and made publicly accessible over HTTP via a self-installing Apache web server. Termination and stop protection safeguarded the instance against accidental deletion or shutdown, while still allowing controlled, intentional changes — an instance type upgrade, EBS volume expansion, and a deliberate stop after disabling protection.
