# 🖧 Creating a Layer 7 Load Balancer in OCI (Step by Step)

This guide walks through building a **public load balancer** in Oracle Cloud Infrastructure (OCI) with:
- A **VCN** (Virtual Cloud Network)
- **Security rules** allowing HTTP traffic (port 80)
- **Two web servers** in private subnets (acting as backends)
- A **Layer 7 (HTTP) Load Balancer** in the public subnet
- Health checks + listener configuration
- Testing the setup from a browser

---

## ⚙️ Step 1: Create a VCN

1. Go to **Networking → Virtual Cloud Networks → Create VCN**.  
2. Choose **VCN with Internet Connectivity**.  
3. CIDR Block: `10.0.0.0/16`.  
4. Create two subnets:
   - **Public Subnet**: `10.0.1.0/24` (for Load Balancer, attached to Internet Gateway)
   - **Private Subnet**: `10.0.2.0/24` (for web servers, routed via NAT Gateway)

---

## ⚙️ Step 2: Modify Security List

Edit the **VCN Security List** for the public subnet:

- **Ingress Rule**:
  - Source CIDR: `0.0.0.0/0`
  - Protocol: `TCP`
  - Destination Port: `80`
- **Egress Rule**:
  - Destination: `0.0.0.0/0`
  - Protocol: All

This ensures the load balancer can receive traffic from the internet.

---

## ⚙️ Step 3: Create Web Server Instances

1. Go to **Compute → Instances → Create Instance**.  
2. Name: `WebServer1`.  
   - Place it in the **Private Subnet** (`10.0.2.0/24`).  
   - Disable public IP assignment.  
3. Repeat for **WebServer2**.  

---

## ⚙️ Step 4: Add Startup Script (Cloud-Init)

During instance creation, add this **startup script** under **Advanced Options → Cloud-Init**:

```bash
#!/bin/bash
sudo yum install -y httpd
echo "Hello from $(hostname)" | sudo tee /var/www/html/index.html
sudo systemctl enable --now httpd
⚙️ Step 5: Create a Load Balancer

Go to Networking → Load Balancers → Create Load Balancer.

Choose:

Type: Public

Subnet: Public Subnet (10.0.1.0/24)

Shape: Flexible (choose min/max bandwidth)

Assign a Public IP address.

⚙️ Step 6: Add Backend Servers

Create a Backend Set:

Policy: ROUND_ROBIN

Health Check: HTTP, Port 80, URL path /

Add backends:

WebServer1 private IP

WebServer2 private IP

Port: 80

⚙️ Step 7: Configure Listener

Add a Listener:

Name: listener1

Protocol: HTTP (Layer 7)

Port: 80

Associate with the backend set

⚙️ Step 8: Test the Setup

Copy the Load Balancer Public IP.

Open it in a browser:

http://<load-balancer-public-ip>


Refresh multiple times → You should see alternating responses:

Hello from WebServer1

Hello from WebServer2

✅ Result

You now have a Layer 7 Load Balancer distributing traffic across two private web servers in OCI.

Highly available: The LB checks health of backends.

Secure: Web servers are private, only accessible through the LB.

Scalable: You can add more backends anytime.
