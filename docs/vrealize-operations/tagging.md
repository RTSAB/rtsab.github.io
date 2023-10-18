---
layout: default
title: "Tagging and Custom Properties"
parent: "vRealize Operations"
---

## Tagging and Custom Properties

In vRealize Operations (vROps) you can add user-defined attributes to virtual infrastructure objects to provide additional information about those objects. These properties can be used to categorize, tag, or label objects in vROps, and they can be used as criteria for creating custom views, reports, and alerts.

There are two main ways to add these attributes, either you create and maintain them through an adapter like vCenter (vSphere Tags and Custom Attributes), or you create Custom Properties that are maintained in vROPs.

Custom properties in vROps can be created for a wider range of objects, not just vSphere.

### vSphere Tags

vRealize Operation will track when in time a tag is added to a virtual machine. This is important to note when using tags in chargeback as any time before the creation will not be included when generating a bill.

### Auto-population

Custom properties can be populated manually or automatically. You can set the value of a custom property manually by editing an object's properties in vROps. You can also use Custom Group policies to automatically populate custom properties based on information from vCenter Server or other data sources.

