# Load Balancer Topology with Public & Private Subnets in OCI  

This guide shows how to deploy a **Load Balancer topology in Oracle Cloud Infrastructure (OCI)** with:  
- **VCN** with two subnets: Public & Private  
- **Load Balancer** in the public subnet  
- **Two web servers** in the private subnet (backends to the load balancer)  

---

## 🏗️ Architecture Overview  

              Internet
                 │
     ┌───────────┴───────────┐
     │     Load Balancer     │ (Public Subnet: 10.0.1.0/24)
     │ (Public IP + Listener)│
     └───────────┬───────────┘
                 │
 ┌───────────────┴───────────────┐
 │                               │
WebServer1 (10.0.2.x) WebServer2 (10.0.2.y)
(Private Subnet: 10.0.2.0/24, Backend Servers)

---

## ⚙️ Step 1: Create a VCN  

1. Go to **Networking → Virtual Cloud Networks → Create VCN with Internet Connectivity**.  
2. Set VCN CIDR: `10.0.0.0/16`.  
3. Add subnets:  
   - **Public Subnet** → `10.0.1.0/24`, Regional, Route to **Internet Gateway**  
   - **Private Subnet** → `10.0.2.0/24`, Regional, Route to **NAT Gateway**  

---

## ⚙️ Step 2: Launch Two Web Servers in Private Subnet  

1. Go to **Compute → Instances → Create Instance**.  
2. Launch **WebServer1** and **WebServer2** in the **Private Subnet**.  
3. Install a simple web server:  

```bash
sudo yum install -y httpd
echo "Hello from $(hostname)" | sudo tee /var/www/html/index.html
sudo systemctl enable --now httpd
⚙️ Step 3: Create Load Balancer

Go to Networking → Load Balancers → Create Load Balancer.

Place in the Public Subnet with a Public IP.

Define Backend Set:

Policy: ROUND_ROBIN

Health Check: HTTP, port 80, path /

Add Backend Servers: Private IPs of WebServer1 and WebServer2.

⚙️ Step 4: Create Listener

Protocol: HTTP

Port: 80

Associate with backend set
✅ Step 5: Test

Open in browser:

http://<Load-Balancer-Public-IP>


Refresh → You should alternate between:

"Hello from WebServer1"

"Hello from WebServer2"

🔹 Terraform Example
resource "oci_load_balancer_load_balancer" "lb" {
  shape          = "flexible"
  compartment_id = var.compartment_ocid
  subnet_ids     = [oci_core_subnet.public.id]

  display_name = "example-lb"
}

resource "oci_load_balancer_backend_set" "lb_backendset" {
  name             = "backendset1"
  load_balancer_id = oci_load_balancer_load_balancer.lb.id
  policy           = "ROUND_ROBIN"

  health_checker {
    protocol = "HTTP"
    url_path = "/"
    port     = 80
  }
}

resource "oci_load_balancer_backend" "backend1" {
  load_balancer_id = oci_load_balancer_load_balancer.lb.id
  backendset_name  = oci_load_balancer_backend_set.lb_backendset.name
  ip_address       = oci_core_instance.web1.private_ip
  port             = 80
}

resource "oci_load_balancer_backend" "backend2" {
  load_balancer_id = oci_load_balancer_load_balancer.lb.id
  backendset_name  = oci_load_balancer_backend_set.lb_backendset.name
  ip_address       = oci_core_instance.web2.private_ip
  port             = 80
}

🔹 OCI CLI Example
# Create Load Balancer
oci lb load-balancer create \
  --compartment-id <compartment_ocid> \
  --shape flexible \
  --subnet-ids '["<public_subnet_ocid>"]' \
  --display-name my-lb

# Create Backend Set
oci lb backend-set create \
  --load-balancer-id <lb_ocid> \
  --name backendset1 \
  --policy ROUND_ROBIN \
  --health-checker protocol=HTTP,port=80,url-path="/"

# Add Backends
oci lb backend create \
  --load-balancer-id <lb_ocid> \
  --backend-set-name backendset1 \
  --ip-address <webserver1_private_ip> \
  --port 80

oci lb backend create \
  --load-balancer-id <lb_ocid> \
  --backend-set-name backendset1 \
  --ip-address <webserver2_private_ip> \
  --port 80

🎯 Outcome

You now have a public-facing load balancer distributing traffic across two private web servers in OCI 🚀.
This is the basis for scalable web apps, APIs, and high-availability architectures.
