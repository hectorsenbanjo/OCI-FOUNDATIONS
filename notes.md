# OCI Core Concepts – Notes

## Tenancy
- A tenancy is your **root account** in OCI.
- It represents your organization and contains all resources.
- Every user, group, policy, and compartment exists inside a tenancy.

## Compartments
- Logical partitions inside a tenancy.
- Used to organize and isolate resources (e.g., dev, test, prod).
- Access is controlled by **IAM policies**.

## Regions
- A **geographic location** where OCI resources are hosted (e.g., US East, London, Frankfurt).
- Each region contains one or more Availability Domains.

## Availability Domains (ADs)
- Independent, fault-isolated data centers within a region.
- Provide high availability — if one AD fails, others remain up.

## Fault Domains (FDs)
- Subdivisions within an Availability Domain.
- Distribute resources across FDs to reduce impact of hardware failures or maintenance.

---

✅ These concepts form the **foundation of OCI architecture**.  
Understanding them is key before diving into networking, compute, or storage services.
