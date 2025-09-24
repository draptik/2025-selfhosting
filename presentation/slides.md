---
theme: ./mathema-2023
defaults:
  layout: "default-with-footer"
title: Selfhosting / Homeserver
occasion: "SoCraTes AT"
occasionLogoUrl: "./images/logo_socrates.png"
company: "MATHEMA GmbH"
presenter: "Patrick Drechsler"
contact: "patrick.drechsler@mathema.de"
info:
  Selfhosting / Homeserver
layout: cover
transition: slide-left
mdc: true
download: true

src: ./pages/00-title.md
---

---
layout: image-left
image: "/images/weird-network.png"
---

## Motivation

- Privacy / [Enshittification](https://en.wikipedia.org/wiki/Enshittification)
- Learning:  networks, operations, IaC, etc.
- Saving money?

---
layout: image-right
image: "/images/learning.png"
---

## Disclaimer

- I am new to selfhosting
- This is my learning experience
- slowly reaching status "Enough to be dangerous" 😈

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
- availability / scaling

:: right ::

### ☁️ Cloud

#### Pros

- PaaS / SaaS solutions
- availability / scaling
- no hardware investment
- easier setup

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

## Domain Hosting (current state)

```mermaid
flowchart LR

  cloud("internet")
  hoster("Domain-Name hoster")
  hetzner("☁️ Nextcloud (Hetzner)")
  homeserver("🏠 Homeserver")

  cloud -- *.mydomain.com --> hoster
  hoster -- *.wolke.mydomain.com --> hetzner
  hoster -- (2) *.cloud.mydomain.com --> homeserver
  homeserver -- (1) DynDNS --> hoster

  style hetzner fill:#e6ffe6
  style homeserver fill:#ffe4e1
  style hoster fill:orange
```

I learned something about

- `CNAME` entries
- `A` entries
- wildcards

---

## Domain Hosting (alternative)

Use a Virtual Private Server (VPS) to access Home server

```mermaid
flowchart LR

  cloud("internet")
  hoster("Domain-Name hoster")
  hetzner("☁️ Nextcloud (Hetzner)")
  homeserver("🏠 Homeserver")
  vps("VPS")

  hoster -- *.wolke.mydomain.com --> hetzner
  cloud -- *.mydomain.com --> hoster
  hoster -- *.cloud.mydomain.com --> vps
  vps -- WireGuard --> homeserver
  homeserver -- DynDNS --> hoster

  style vps fill:#ffe4e1
```

- on my "needs more research" list, maybe overkill?
- Safer, but might involve extra costs

---
layout: two-cols-header
---

## Home infrastructure: Bird's eye view

```mermaid
flowchart LR

  %% router@{ pos: "t", h: 50, constraint: "on", img: "/images/logo-fritzbox.png", label: "router<br/>(FritzBox)"  }
  %% pihole@{ pos: "t", h: 50, constraint: "on", img: "/images/logo-pi-hole.png", label: "RPi<br/>Pi-hole w/ DHCP & DNS<br/>Wireguard" }

  cloud("internet")
  router("router<br/>(FritzBox)")
  pihole("RPi<br/>Pi-hole w/ DHCP & DNS<br/>Wireguard")
  nas("NAS")
  proxmox("Proxmox")

  cloud --> router
  router --> pihole
  pihole --> proxmox
  proxmox --> nas

  style router fill:#e6ffe6
  style pihole fill:#ffe4e1
```

:: left ::

### router (FritzBox)

- I try to keep the router as dumb as possible
- disabled DNS & DHCP
- enabled DynDNS
- [Let's Encrypt](https://letsencrypt.org/): port 80/443 is open ⚠️
- wireguard port is open
- configs are easy to backup & restore

:: right ::

### RPi

- Raspberry Pi 2 Model B (!)
- [Pi-hole](https://pi-hole.net/): DNS & DHCP
- WireGuard (via [PiVPN](https://www.pivpn.io/))
- configs are easy to backup & restore

---
layout: image-right
image: "/images/hardware-costs.png"
---

## Home infrastructure

```mermaid
flowchart LR
  nas("NAS")
  proxmox("Proxmox")
  proxmox --- nas
```

Hardware: Let's talk about money...

---

## Hardware (1/2): NAS

My old Synology (from 2014)...

- 4 HDDs
- costs (mainly the HDDs): 1000 EUR
- effective storage capacity: 8TB
- Synology's "RAID-5"

YMMV, but you want something solid & tested.

<img
  class="absolute bottom-20 right-10 w-100"
  src="/images/nas.png"
/>
---

## Hardware (2/2): Server

- costs: 320 EUR
- CPU: Intel Alder Lake N95
- RAM: **32GB**
- SSD: **1TB (max 2TB)**

<img
  class="absolute bottom-20 right-10 w-155"
  src="/images/homeserver.png"
/>

---
layout: image-left
image: "/images/vps-crazy.png"
---

### Accessing Homeserver

- VPN / Wireguard
- Public access
- Geoblocking
- Thoughts?

---
layout: image-right
image: "/images/rabbit-hole.png"
---

## Why Proxmox VMs?

I am not an expert: The more contained my experiments are, the safer I am.

- VMs: **containment** -> safety feature
- builtin **backups**
- VMs can be **orchestrated** with
  - Ansible
  - Terraform

Alternatives (for pros): kubernetes, nix-os

---

## Server: Overview

```mermaid
flowchart LR

  %% Classes
  classDef hidden fill:none,stroke:none;

  %% Placement links first (sm we know their indices)
  proxmox --> spacer
  spacer --> comment2

  %% Hide the 2 placement links + 4 anchor links (indices 0..5)
  linkStyle 0 opacity:0,stroke-width:0px
  linkStyle 1 opacity:0,stroke-width:0px

  comment@{ shape: notch-rect, label: "**Setup, Update/Monitoring**<br/>i.e. diun/watchtower, Ansible, Terraform" }
  comment2@{ shape: notch-rect, label: "**Backup / Restore**<br/>(i.e. restic, Proxmox Backup, Terraform)"}
  spacer(( )):::hidden

  subgraph proxmox ["Proxmox Host"]
    vms@{ shape: procs, label: "Virtual Machines" }
    subgraph vm ["Virtual Machine x"]
      subgraph docker ["Docker or Podman"]
        subgraph app ["Application"]
        end
      end
    end
  end

  %% Styles
  style proxmox fill:#f0f8ff,stroke:#333,stroke-width:2px
  style vm fill:#ffe4e1,stroke:#444,stroke-dasharray: 5 5
  style vms fill:#ffe4e1,stroke:#444,stroke-dasharray: 5 5
  style docker fill:#e6ffe6,stroke:#222
  style spacer fill:none,stroke:none

  %% Left-side links
  comment --> proxmox
  comment --> vm
  comment --> docker
  comment --> app

  %% Right-side links (via spacer)
  comment2 --> proxmox
  comment2 --> vm
  comment2 --> docker
  comment2 --> app
```

---
layout: two-cols-header
---

## Outlook

:: left ::

### TODOs

- Setup VMs w/ Terraform
- Add `fail2ban` to public facing `caddy`
- Research Virtual Private Server (VPS) - esp. costs

:: right ::

### We can have a look at details

- Proxmox (live)
- Ansible scripts
- anything else in my network!

:: bottom ::

Let's talk!

---
layout: image
image: "/images/discussion.png"
---

---
src: ./pages/99-end.md
---
