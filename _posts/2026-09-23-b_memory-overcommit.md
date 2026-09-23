---
layout: post
title: "AHV Internals Blog: Memory Overcommit"
date: 2026-09-23
---

# New blog post - Memory Overcommit Internals
Memory Overcommit is one of the features of the Nutanix AHV hypervisor that allows you to pack more VMs into a host than the host would normally be able to accommodate just based on its physical size. It’s also a feature familiar to many customers using competing hypervisors who are looking to move to AHV. Providing more CPUs to guests than you have physical cores is commonplace but with overcommit you can do something similar with memory - e.g., potentially fitting 12 VMs each using 10GB into a host with just 100GB physical memory. But, as with everything, nothing comes for free and the use of Memory Overcommit needs to balance the advantages against the potential implications.

Want to know more? Well, you're in luck - check out my deep-dive blog post on [Nutanix's blog site](https://www.nutanix.com/tech-center/blog/ahv-internals-memory-overcommit) for all the details, and keep your eyes open for a more posts on this blog to expand further
