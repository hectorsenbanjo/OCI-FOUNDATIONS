# 🚀 VCN Setup in Oracle Cloud Infrastructure (OCI)

This guide demonstrates how to create a **Virtual Cloud Network (VCN)** in OCI with:
- One **public subnet** (internet accessible)  
- One **private subnet** (internal only)  
- Three **gateways**: Internet Gateway, NAT Gateway, and Service Gateway  

This is a **common 3-tier design** used for production workloads.  

---

## 📌 Architecture Overview

            +--------------------------+
            |      Internet Gateway    |
            +------------+-------------+
                         |
                  +------+------+
                  |  Public Subnet|
                  | 10.0.1.0/24  |
                  | (Bastion/ LB) |
                  +------+------+
                         |
       ------------------------------------------------
                         |
                  +------+------+
                  | Private Subnet|
                  | 10.0.2.0/24  |
                  | (App / DB)   |
                  +------+------+
                         |
    +----------+  +-----------------+
    | NAT GW   |  | Service Gateway |
    +----------+  +-----------------+

- **Public Subnet** → Internet access via Internet Gateway  
- **Private Subnet** → Outbound access via NAT Gateway, service access via Service Gateway  

---

## 🖥️ 1. Create the VCN (OCI Console)

1. Go to **Networking → Virtual Cloud Networks → Start VCN Wizard**  
2. Choose **Create VCN with Internet Connectivity**  
3. Name: `Demo-VCN`  
4. CIDR Block: `10.0.0.0/16`  

---

## 🌐 2. Create Subnets
### Public Subnet
- Name: `Public-Subnet`  
- CIDR: `10.0.1.0/24`  
- Type: **Public** (assigns public IPs)  

### Private Subnet
- Name: `Private-Subnet`  
- CIDR: `10.0.2.0/24`  
- Type: **Private** (no public IPs)  

---

## 🔑 3. Create Gateways
### Internet Gateway
- Name: `Demo-IGW`  
- Attach to `Demo-VCN`  
- Route Rule (Public Subnet RT):  
  - `0.0.0.0/0` → **Internet Gateway**  

### NAT Gateway
- Name: `Demo-NAT`  
- Attach to `Demo-VCN`  
- Route Rule (Private Subnet RT):  
  - `0.0.0.0/0` → **NAT Gateway**  

### Service Gateway
- Name: `Demo-ServiceGW`  
- Attach to `Demo-VCN`  
- Route Rule (Private Subnet RT):  
  - `All <region> Oracle Services` → **Service Gateway**  

---

## 🔒 4. Security Lists
### Public Subnet
- Ingress: `22` (SSH), `80/443` (HTTP/HTTPS)  
- Egress: `All`  

### Private Subnet
- Ingress: VCN-only (`10.0.0.0/16`)  
- Egress: NAT / Service Gateway  

---

## ⚡ Terraform Example

```hcl
resource "oci_core_virtual_network" "demo_vcn" {
  cidr_block     = "10.0.0.0/16"
  compartment_id = var.compartment_id
  display_name   = "Demo-VCN"
}

resource "oci_core_internet_gateway" "igw" {
  compartment_id = var.compartment_id
  display_name   = "Demo-IGW"
  vcn_id         = oci_core_virtual_network.demo_vcn.id
  enabled        = true
}

resource "oci_core_nat_gateway" "nat" {
  compartment_id = var.compartment_id
  display_name   = "Demo-NAT"
  vcn_id         = oci_core_virtual_network.demo_vcn.id
}

resource "oci_core_service_gateway" "service_gw" {
  compartment_id = var.compartment_id
  display_name   = "Demo-ServiceGW"
  vcn_id         = oci_core_virtual_network.demo_vcn.id
  services {
    service_id = data.oci_core_services.all_oci_services.services[0].id
  }
}

🖥 OCI CLI Example

# Create VCN
oci network vcn create \
  --compartment-id <compartment_ocid> \
  --display-name Demo-VCN \
  --cidr-block 10.0.0.0/16

# Create Internet Gateway
oci network internet-gateway create \
  --compartment-id <compartment_ocid> \
  --vcn-id <vcn_ocid> \
  --display-name Demo-IGW \
  --is-enabled true

# Create NAT Gateway
oci network nat-gateway create \
  --compartment-id <compartment_ocid> \
  --vcn-id <vcn_ocid> \
  --display-name Demo-NAT

# Create Service Gateway
oci network service-gateway create \
  --compartment-id <compartment_ocid> \
  --vcn-id <vcn_ocid> \
  --display-name Demo-ServiceGW \
  --services '[{"serviceId":"<service_ocid>"}]'

✅ Summary

You now have a VCN with public + private subnets, connected to:

🌍 Internet Gateway for public resources

🔄 NAT Gateway for private outbound access

🔐 Service Gateway for private Oracle services access

This design is production-ready and forms the foundation for deploying bastion hosts, app servers, and databases in OCI.
