---
theme: ./mathema-2023
defaults:
  layout: "default-with-footer"
title: Selfhosting / Homeserver 101
occasion: "SENECA 2025"
occasionLogoUrl: "./images/logo-seneca24.png"
company: "MATHEMA GmbH"
presenter: "Patrick Drechsler"
contact: "patrick.drechsler@mathema.de"
info:
  Selfhosting / Homeserver 101
layout: cover
transition: slide-left
mdc: true
download: true

src: ./pages/00-title.md
---

---
layout: image-right
image: "/images/weird-network.png"
---

## Motivation

- Privacy / [Enshittification](https://en.wikipedia.org/wiki/Enshittification)
- Learning:  networks, operations, IaC, etc.
- Saving money?

---

## Disclaimer

- I am new to selfhosting
- This is my learning experience

---
layout: two-cols-header
---

## Cloud or Homeserver?

:: left ::

### 🏠 Homeserver

#### Pros

- full control
- privacy: all data is yours
- no vendor lock-in
- low running costs

#### Cons

- initial hardware investment costs
- maintenance of all infrastructure
- availability
- scaling

:: right ::

### ☁️ Cloud

#### Pros

- PaaS / SaaS solutions
- availability
- scaling
- no hardware investment

#### Cons

- vendor lock-in
- running costs are difficult to predict
- trust issues

---

## Typical Applications

| app | "before" | "after" |
| - | - | - |
| calendar | Google Calendar | ☁️ Nextcloud (Hetzner) |
| file sharing | Google Drive | ☁️ Nextcloud (Hetzner) |
| photos | Google Photos | 🏠 [immich](https://immich.app/) |
| document management | private git | 🏠 [Paperless-NGX](https://docs.paperless-ngx.com/) |
| read-it-later | Pocket (Mozilla) | 🏠 [Readeck](https://readeck.org/) |
| cooking recipes | bookmarks, Chefkoch, etc | 🏠 [Mealie](https://mealie.io/) |

---

## Domain Hosting

```mermaid
flowchart LR

  cloud("internet")
  hoster("Domain-Name hoster")
  hetzner("☁️ Nextcloud (Hetzner)")
  homeserver("🏠 Homeserver")

  cloud -- *.mydomain.com --> hoster
  hoster -- *.wolke.mydomain.com --> hetzner
  hoster -- *.cloud.mydomain.com --> homeserver
```

- I learned something about `CNAME` and `A` entries

---

## Home infrastructure (1/x)

```mermaid
flowchart LR

  cloud("internet")
  router("FritzBox")
  pihole("Pi-hole w/ WireGuard & DNS")
  nas("Synology NAS")
  proxmox("Proxmox")

  cloud --> router
  router --> pihole
  pihole --> proxmox
  pihole --> nas
  proxmox --> nas
```

---

## Home infrastructure (2/x)

```mermaid
flowchart LR

  nas("NAS")
  proxmox("Proxmox")

  proxmox --> nas
```


- But wait, there is a NAS and a Proxmox server?
- Let's talk about hardware

---

## Hardware (1/2): NAS

NAS: with 4 HDDs
  - costs: mainly the HDDs (1000 EUR)
  - space: 8TB
  - Synology's "RAID-5"

<img
  class="absolute bottom-20 right-10 w-100"
  src="/images/nas.png"
/>
---

## Hardware (2/2): Server

- costs: 320 EUR
- CPU: Intel Alder Lake N95
- RAM: **32GB** DDR4 (3200MHz)
- SSD: **1TB** M.2 NVMe PCIe 3.0 M.2 2280 (**Max 2TB**)

<img
  class="absolute bottom-20 right-10 w-125"
  src="/images/homeserver.png"
/>

---

## Access to Homeserver

- VPN / Wireguard
- Public access
- Geoblocking

---

## Why did I choose Proxmox?

Alternatives:

- kubernetes
- nix

Both are currently too complex for me.

I picked a different rabbit hole:

- Proxmox with VMs and
  - with Ansible
  - with Terraform (OpenTofu)
- Proxmox VMs provide natural containment -> safety feature

---

## Overview

```mermaid
flowchart LR

  nas("NAS")
  proxmox("Proxmox")
  vm("VM")
  docker("Docker")
  podman("Podman")
  comment@{ shape: notch-rect, label: "Update, Setup
(i.e. diun/watchtower, Ansible, Terraform)" }
  comment2@{ shape: notch-rect, label: "Backup / Restore
(i.e. restic)"}

  subgraph nas
  subgraph proxmox
  subgraph vm

  subgraph docker
  end

  subgraph podman
  end

  end
  end
  end

  comment --> nas
  comment --> proxmox
  comment --> vm
  comment --> docker
  comment --> podman

  %% comment2 --> nas
  %% comment2 --> proxmox
  %% comment2 --> vm
  %% comment2 --> docker
  %% comment2 --> podman
```

---

## Discussion

---
src: ./pages/99-end.md
---
