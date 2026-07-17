---
title: "Inside My Homelab: Architecture, Tools, and Real-World Lessons" slug: "inside-my-homelab-architecture-tools-and-real-world-lessons"
seoTitle: "My Homelab Stack: Hardware & Software Explained"
seoDescription: "Explore my homelab stack using Raspberry Pi, MicroK8s, Proxmox, TrueNAS, MetalLB, and NFS for reliable self-hosted services."
datePublished: 2026-03-23T05:00:00.000Z
cuid: cmrokafwb00010al3gdar2kdl
slug: inside-my-homelab-architecture-tools-and-real-world-lessons-slug-inside-my-homelab-architecture-tools-and-real-world-lessons
cover: https://cdn.hashnode.com/uploads/covers/69fb425450ecad45332f1a60/c63b75b4-7de9-49de-aae0-43b3c2893582.jpg
ogImage: https://cdn.hashnode.com/uploads/og-images/69fb425450ecad45332f1a60/7cf87f8f-b5c8-49ce-b716-5d0073c18867.jpg
tags: docker, kubernetes, self-hosting

---

* * *

In the last post, we talked about *why* people self-host. This time, let's get concrete — what does a real homelab actually look like?

This is the stack I built, the choices I made, and the reasoning behind each one — including the mistakes, the workarounds, and the moments where it all clicked.

If you’re starting your first homelab or trying to move beyond a single machine setup, this is what a practical, real-world stack looks like.

* * *

## **Starting With a Simple Question**

Before buying anything, I asked: *what do I actually want this to do?*

The answer came down to three things:

*   Run apps reliably — even if one machine goes down
    
*   Store files safely with redundancy
    
*   Be a platform I can actually build things on top of
    

That last point matters more than it sounds. I didn't want a homelab that just sat there. I wanted one that did real work — automating tasks, routing my DNS, running services I actually use. That shaped every decision that followed.

* * *

## Hardware Choices

### **Raspberry Pis — The Low-Power Workhorses**

Raspberry Pis are small ARM-based computers that sip electricity. They're not powerful, but they're cheap, quiet, and can run 24/7 without guilt.

In my setup, they form part of the MicroK8s cluster — running lighter workloads and making the cluster distributed across physical machines. The moment one Pi goes offline, the cluster notices and reschedules its workloads onto the remaining nodes automatically.

The tradeoff: they're slow for demanding tasks, and you need to be deliberate about image management. Since pulling images over the network every time a pod restarts isn't reliable at home bandwidth, I pre-pull images onto each node and set `imagePullPolicy: IfNotPresent`. It's a small discipline that saves a lot of frustration.

### **The i7 x86 Machine — The Heavy Lifter**

For workloads that need real CPU power — running databases, heavier containers, virtual machines — a used i7 desktop does the job without the cost of new server hardware.

x86 also matters for compatibility. ARM-based Pis occasionally run into images that simply don't have an ARM build. The x86 node is where those workloads land without any workarounds.

### **TP-Link Managed Switch — More Than a Power Strip for Cables**

A managed switch lets you control how devices talk to each other — separating traffic, prioritising certain services, and giving you visibility into your network.

In practice, it's what makes MetalLB (the component that assigns real IP addresses to services in the Kubernetes cluster) work cleanly. Without proper switch configuration, services exposed outside the cluster can behave unpredictably. With it, each service gets a stable IP that the rest of your home network can reach reliably.

### **UPS — The Silent Guardian**

A UPS is a battery between your hardware and the wall. When power flickers or cuts out, your machines keep running long enough to shut down gracefully.

Without it, a sudden outage can corrupt a database mid-write or leave a Kubernetes cluster in a split-brain state — where nodes disagree about what's running because they lost contact during shutdown. It's the least glamorous purchase in this list. It's also the one I'd buy first if I were starting over.

* * *

## Software Stack

### **Ubuntu — The Foundation**

Ubuntu runs on the machines. It's stable, widely supported, and when something breaks — and it will — someone has almost certainly broken the same thing and written about it.

Boring? Yes. That's the point.

### **Proxmox — Running Multiple Systems on One Machine**

Proxmox is a virtualisation platform that lets you run multiple isolated virtual machines on a single physical machine. The i7 runs Proxmox, which then hosts TrueNAS as a VM alongside other workloads — clean separation without needing separate physical hardware for each role.

Think of it like dividing one computer into several logical computers, each doing its own job without interfering with each other.

### **TrueNAS — Purpose-Built Storage**

TrueNAS manages the actual storage — hard drives, file sharing, snapshots, and backups. It uses ZFS, a filesystem designed to silently detect and recover from data corruption.

In the Kubernetes cluster, TrueNAS exposes storage over NFS. Pods that need persistent storage — like Pi-hole's configuration or service data — mount NFS-backed volumes. This means storage survives pod restarts and node failures, because the data lives on TrueNAS, not inside the container.

### **MicroK8s — The Cluster Brain**

MicroK8s is a lightweight version of Kubernetes — the system originally built by Google to manage containers at scale, adapted to run on modest hardware.

Across my Raspberry Pis and the x86 machine, MicroK8s forms a single cluster. When a node goes down, workloads reschedule onto surviving nodes automatically. No manual intervention needed.

But that resilience has a prerequisite: the images those workloads need must already exist on the surviving nodes. That's why image pre-pulling isn't optional — it's what makes automatic rescheduling actually work in practice, rather than just in theory.

MetalLB handles IP allocation, giving each exposed service a real address on the home network — so services get a stable address that DNS can point to reliably.

* * *

## **How It All Fits Together**

```plaintext
[Raspberry Pi] ──┐
[Raspberry Pi] ──┤──► MicroK8s Cluster
[i7 x86]       ──┘       ├── Pi-hole      (DNS for the whole network)
                         └── ...more services

[i7 x86 / Proxmox]──► TrueNAS VM
                         └── NFS storage (used by all cluster pods)

[TP-Link Managed Switch]── ties everything together, enables MetalLB
[UPS]── keeps it all alive through power events
```

The apps run on MicroK8s. Persistent data lives on TrueNAS over NFS. Proxmox keeps the i7 organised. MetalLB assigns real IPs to services. The switch makes the network behave. The UPS absorbs the unexpected.

* * *

Next up: a deeper look at the hardware decisions behind this setup — what I’d keep, what I’d change, and how you can build your own.