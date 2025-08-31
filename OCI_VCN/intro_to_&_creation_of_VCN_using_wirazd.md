🌐 Oracle Cloud Infrastructure – Virtual Cloud Network (VCN)
Introduction

A Virtual Cloud Network (VCN) in Oracle Cloud Infrastructure (OCI) is a customizable, private network that closely resembles a traditional data center network.
It provides secure communication between your cloud resources (Compute, DB, Storage) and external networks like the Internet or on-premises data centers.

Key Components of a VCN:

Subnets: Divide the VCN into smaller networks (Public & Private).

Gateways: Enable connectivity to the Internet, other OCI services, or on-premises.

Internet Gateway → Public internet access.

NAT Gateway → Private subnet outbound access.

Service Gateway → Secure access to Oracle services (e.g., Object Storage).

Dynamic Routing Gateway (DRG) → On-premises connectivity via VPN or FastConnect.

🛠 Creating a VCN using the OCI Wizard

Login to the OCI Console

Go to OCI Console
.

Open Networking Menu

From the left navigation pane, select:
Networking → Virtual Cloud Networks.

Create VCN with Internet Connectivity (Wizard)

Click Start VCN Wizard.

Choose VCN with Internet Connectivity.

Provide the following:

VCN Name: sandbox-vcn

Compartment: sandbox

CIDR Block: 10.0.0.0/16

Wizard Automatically Creates:

VCN with CIDR block

Public Subnet (10.0.1.0/24) with Internet Gateway access

Private Subnet (10.0.2.0/24) with NAT Gateway access

Route Tables & Security Lists preconfigured

Service Gateway for Oracle services access

Review & Create

Verify configuration → Click Create.

Your VCN is provisioned and ready for deployment.

📊 Example Architecture

This architecture shows:

Public Subnet for internet-facing resources.

Private Subnet for secure, backend resources.

Gateways (Internet, NAT, Service, DRG) for connectivity.

On-Premises connectivity via VPN or FastConnect.

✅ With the VCN Wizard, you can quickly deploy a production-ready networking setup without manually configuring each component.
