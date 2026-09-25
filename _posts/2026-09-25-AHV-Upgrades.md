---
layout: default
title: "AHV Upgrades"
date: 2026-09-25
---
# On the subject of upgrades
All software that's released as part of AHV is split into packages. These packages are installed and upgraded by a package manager, much like many Linux distributions, and each of these packages has specific expectations of the setup of the installed hypervisor, and scripts that can run before and after upgrade to make sure each package is upgraded correctly. As with all software, the exact state of the hypervisor will be different between deployments. Some may even have unsupported modifications that have been made to the hypervisor made through SSH. This means that these packages and scripts need to be robust against both known and unknown changes that might have been made by administrators while it was in production.

When it comes to upgrades, this means there's an interesting question. Is it safe to upgrade the individual packages? When talking about a small number of updates, you can reason about the interactions between them and the system state much more reliably but when there are large updates to multiple components over the system things do get a lot less predictable in the face of changes administrators have made. This approach is the one AHV historically took in all cases, which we called In-Place upgrades, but during large system changes the interactions between multiple components and admin modifications did impact reliability back in transitions back around the the times of AOS 5.5. 

To help avoid these issues, Nutanix introduced a second mode of upgrades called [reimage-based](https://portal.nutanix.com/page/documents/details?targetId=Life-Cycle-Manager-Guide-v3_4:top-ahv-upgrade-specifications-c.html) to massively increase the reliability of upgades that changed large numbers components of the system back in 2023. Nutanix then selected which mode was going to be used based on how much change was in the underlying hypervisor.

That's all great, but there were some differences between the two upgrade types. In-place upgrades can keep the hypervisor running and it updates itself, whereas reimage-based upgrades will wipe and replace the entire contents of the boot partition which can't be done while the system is running. As a result, in-place upgrades needed one host reboot but reimage-based upgrades needed two. Host reboots can take time. Some systems might only take 30 seconds from power on to when it's booting the operating system, but some take many minutes.

On top of the hardware boot time, the AHV kernel needed to tear down and re-initialise the environment twice, and some differences between AHV and the reimager environments meant there were real opportunities for optimisations. In total, these impacts meant that reimage-based upgrades took longer to execute than in-place upgrades - in some cases up to 20 minutes more per node.

In AOS 7.5 (and the corresponding AHV 11.0), however, we released a number of huge improvements to the way that reimage-based upgrades work.

First we avoided both reboots entirely. The AHV kernel has a mechanism that lets you load and boot into a new kernel directly from the currently running operating system, skipping the BIOS or UEFI hardware reset phase. This not only makes the switch from the AHV hypervisor environment into the reimager environment happen in mere seconds, but it also avoids the hardware reinitialisation phases which may have led to upgrade failures. This alone saved around 10 minutes per upgrade

Then we unified the reimager environment with the AHV hypervisor environment, making sure that the hardware was being used in the same ways and the kernels of the AHV hypervisor and reimager didn't need to spend time reconfiguring hardware in ways that would be discarded when switching between environments.

Another optimisation was around the management of services in AHV and the reimager. Often when shutting down any operating system there are pauses, such as waiting for services to close. In a normal host shutdown they may not be felt as much as when doing an upgrade across many nodes, but are really important when upgrading. This shaved another few minutes off the total upgrade time.

All in all, the actual time to upgrade a node when using the reimager has gone down from over 30-35 minutes to 10-12 minutes in upgrade time monitoring statistics - an absolutely huge reduction. Obviously, hardware and VM workloads may cause the observed upgrade time to vary.

Check out more details in [this blog post](https://www.nutanix.com/tech-center/blog/nci-75-performance-ahv-for-g2k-scale#upgrade)
