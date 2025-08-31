# Creating Identity Domains in Oracle Cloud Infrastructure (OCI)

An **Identity Domain** in OCI is a dedicated space for managing users, groups, authentication, and access policies.  
This is useful when you want to isolate environments (e.g., dev, test, production) or give separate customers their own identity management.

---

## Steps (Console)

1. Sign in to the **OCI Console**.
2. Open the navigation menu → **Identity & Security** → **Domains**.
3. Click **Create Domain**.
4. Provide details:
   - **Name**: `sandbox-domain`
   - **Description**: *Identity domain for sandbox testing*
   - **Compartment**: select the `sandbox` compartment
   - **License type**: choose **Free** (or **Premium** for advanced features)
5. Click **Create** and wait a few minutes while OCI provisions the domain.

---

## Steps (OCI CLI)

```bash
oci iam domain create \
  --compartment-id <sandbox_compartment_ocid> \
  --name "sandbox-domain" \
 --description "Identity domain for sandbox testing" \
  --license-type "free"


Replace <sandbox_compartment_ocid> with the OCID of your sandbox compartment.

Notes

Every tenancy has a default identity domain, but you can create more for isolation or multi-tenant setups.

After creating a domain, you can add users, groups, and policies inside it.

Free license is enough for basic IAM; choose Premium if you need features like adaptive security and reporting.

✅ With this setup, you now have an Identity Domain (sandbox-domain) inside your sandbox compartment to manage users and resources securely.
