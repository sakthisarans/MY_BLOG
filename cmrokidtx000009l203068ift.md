---
title: "Why I'm Ditching Kubernetes for My Home lab (And What's Replacing It)"
seoTitle: "Why I Ditched Kubernetes for My Homelab (MicroK8s to Docker)"
seoDescription: "Tired of dqlite corruption and cluster babysitting? Here's how I rebuilt my homelab on Docker with TrueNAS, shared Postgres, and two fixed hosts."
datePublished: 2026-07-17T06:39:31.492Z
cuid: cmrokidtx000009l203068ift
slug: why-i-m-ditching-kubernetes-for-my-home-lab-and-what-s-replacing-it
cover: https://cdn.hashnode.com/uploads/covers/69fb425450ecad45332f1a60/d774cb47-acbd-4c0e-b750-14713942366a.png
ogImage: https://cdn.hashnode.com/uploads/og-images/69fb425450ecad45332f1a60/40bae4eb-27a9-4c7b-ad80-3ad7e325b9ec.png
tags: docker, microk8s, self-hosting

---

* * *

# Why I'm Ditching Kubernetes for My Homelab (And What's Replacing It)

If you've followed the MicroK8s build journey series, you know the cluster and I have history — most of it involving dqlite deciding, at 11pm on a random Tuesday, that Raft consensus is more of a suggestion than a rule. I've done node resets, corruption recovery, security audits, Prometheus/Grafana dashboards to watch it all in real time. At some point you have to ask: am I running a homelab, or is the homelab running me?

So I'm making a change. Out goes the Kubernetes cluster. In comes a much simpler, boring-on-purpose architecture built on Docker.

## The problem with K8s at homelab scale

Kubernetes is designed to solve problems I don't actually have. I don't need a scheduler shuffling pods across a fleet of interchangeable nodes to survive machine failure. I have four machines total, I know exactly what runs where, and I want it to *stay* there. Every dqlite corruption I've fought was a symptom of running control-plane consensus across underpowered ARM boards that were never going to give me a real HA story anyway — I was paying the complexity tax of a distributed system without getting the benefit.

For a cluster serving 2-3 users (me, and occasionally a household member checking Immich), that's not a good trade.

## The new architecture

The replacement is deliberately dumb in the best way:

**1\. One TrueNAS box — storage, database, and ingress**

This becomes the backbone. ZFS pools for actual data (Immich photos, Nextcloud files, Firefly III ledgers), a single Postgres instance for anything that needs a relational DB, and `cloudflared` running as the tunnel for exposing services to the internet without opening ports on my router.

**2\. A single shared Postgres/MySQL instance, not one DB per app**

This is the decision I expect to get the most pushback on, so let me make the case for it. Every "best practice" guide says isolate your databases per service. At enterprise scale, sure. At my scale — a handful of self-hosted apps serving myself and maybe two other people — running a separate Postgres container per app (Immich, Firefly III, Nextcloud, Ghost, n8n) means five sets of WAL files, five connection pools, five things to patch and back up, for a workload that peaks at maybe a dozen queries a minute.

One instance, multiple logical databases, separate roles and credentials per app, locked down with `pg_hba.conf` so only my two Docker hosts can connect. ZFS snapshots on the data directory give me point-in-time recovery for free. If a service needs an extension Postgres doesn't ship by default (looking at you, `pgvector` for Immich), that gets handled at the image level, not by spinning up a whole separate instance.

**3\. Two Docker machines running fixed services**

No scheduler, no orchestration layer deciding where things run. I know my RPi5 nodes and my x86 box have different resource profiles, so services get pinned deliberately — anything RAM-hungry (Weaviate, for instance) goes on the x86 machine, lighter services stay on the Pis. Each host runs its own `docker-compose.yml` stacks, reachable over the LAN, talking back to the Postgres instance on TrueNAS.

## What I'm trading away

I want to be honest about the downsides, because "just use Docker" isn't a strictly-better answer:

*   **No self-healing.** If a container dies, it stays dead until something (a `restart: unless-stopped` policy, or me) brings it back. For a homelab, I'm fine with that.
    
*   **No live migration.** If a Docker host goes down, whatever ran on it is down until I fix the host. Kubernetes at least gave me the illusion of resilience here, even if the underlying cluster was the thing usually causing outages in the first place.
    
*   **Single point of failure on the DB.** One Postgres instance means one instance to keep healthy. I'm mitigating this with ZFS snapshots and a straightforward `pg_dump` cron job, but it's a real tradeoff versus per-app isolation.
    

## Why I'm okay with that

Every one of those downsides is a resilience feature I was paying for in operational complexity and never actually needed. The dqlite corruption incidents weren't rare edge cases — they were a recurring tax for capabilities (multi-node scheduling, self-healing, rolling updates) that don't matter when you know exactly which of your four machines is going to run which service, forever, until you decide otherwise.

Simpler infrastructure that I understand completely beats sophisticated infrastructure that occasionally eats itself.