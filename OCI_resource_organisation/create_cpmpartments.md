# Creating Compartments in OCI

A compartment in Oracle Cloud Infrastructure (OCI) is a logical container used to organize and isolate cloud resources.

## Steps (Console)

1. Sign in to OCI Console.
2. Go to **Identity & Security > Compartments**.
3. Click **Create Compartment**.
4. Enter:
   - Name: `sandbox`
   - Description: "Compartment for testing and development"
   - Parent Compartment: (select tenancy root)
5. Click **Create Compartment**.

## Steps (OCI CLI)

```bash
oci iam compartment create \
  --compartment-id <tenancy_ocid> \
  --name "sandbox" \
  --description "Compartment for testing and development"
