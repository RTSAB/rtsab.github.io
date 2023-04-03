---
layout: default
title: "VMware Chargeback"
parent: "vRealize Operations"
---

## VMware Chargeback

VMware Chargeback is a cost management and allocation tool that allows you to track and allocate the costs of virtualized infrastructure. It is designed specifically for VMware vSphere environments and provides visibility into resource usage and cost metrics, enabling you to understand how resources are being consumed and billed.

With VMware Chargeback, you can allocate resource costs to specific departments, projects, or business units based on actual resource usage. This enables you to accurately chargeback the costs of virtual infrastructure to the appropriate stakeholders, promoting transparency and accountability in IT spending.

VMware Chargeback is a companion tool to vRealize Operations and is configured during installation to run against either [Cloud Director](/docs/cloud-director) or vCenter.

## Tenants

Tenants is a way to provide separation and provide separate dashboards for business units or customers. They can be created in VMware Chargeback but only edited and deleted in vRealize Operations.

So when you create a tenant in Chargeback:

![Chargeback Tenant](/assets/images/chargeback_tenant.png)

It will show up as a custom group in vRealize Operations:

![Custom Groups](/assets/images/vrops_tenant.png)

if you change the name or delete a tenant in vRealize Operations the change will immediately be visible in Chargeback. 