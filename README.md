# cisco-administrative-roles-lab
Cisco IOS Administrative Roles and Parser Views Configuration (R1-R3 Network Topology)
# Cisco IOS Lab: Administrative Roles (Parser Views) & OSPF

This repository contains the configuration files and verification notes for the Cisco administrative roles lab.

## Topology & Addressing

**Devices:** 3 Routers (R1, R2, R3), 2 Switches (S1, S3), 2 PCs (PC-A, PC-C).

| Device | Interface | IP Address | Subnet Mask | Gateway |
| :--- | :--- | :--- | :--- | :--- |
| **R1** | G0/0/0 | 10.1.1.1 | 255.255.255.252 | N/A |
| **R1** | G0/0/1 | 192.168.1.1 | 255.255.255.0 | N/A |
| **R2** | G0/0/0 | 10.1.1.2 | 255.255.255.252 | N/A |
| **R2** | G0/0/1 | 10.2.2.2 | 255.255.255.252 | N/A |
| **R3** | G0/0/0 | 10.2.2.1 | 255.255.255.252 | N/A |
| **R3** | G0/0/1 | 192.168.3.1 | 255.255.255.0 | N/A |
| **PC-A** | NIC | 192.168.1.3 | 255.255.255.0 | 192.168.1.1 |
| **PC-C** | NIC | 192.168.3.3 | 255.255.255.0 | 192.168.3.1 |

---

## Configuration Summary

### Part 1: Basic Settings & OSPF
- Configured hostnames, disabled domain lookup, and set up interface IP addresses.
- Enabled OSPF process 1 in Area 0 across all routers.
- Set `g0/0/1` as `passive-interface` on R1 and R3 to block unnecessary OSPF Hellos on LAN interfaces.

### Part 2: AAA & Parser Views (R1 and R3)
1. Enabled AAA model (`aaa new-model`) and set enable secret (`cisco12345`).
2. Configured three parser views via Root View:
   - **`admin1`** (`secret admin1pass`): Full access (`show`, `config terminal`, `debug`).
   - **`admin2`** (`secret admin2pass`): Read-only access (`show` commands only).
   - **`tech`** (`secret techpasswd`): Restricted access to `show version`, `show interfaces`, `show ip interface brief`, and `show parser view`.

---

## Lab Answers / Вопросы к лабораторной

### 1. What is missing from the list of admin2 commands that is present in admin1?
- `configure` and `debug` commands. The `admin2` view is read-only.

### 2. Could the tech user run `show ip interface brief`?
- **Yes.** The command was explicitly added using `commands exec include show ip interface brief`.

### 3. Could the tech user run `show ip route`?
- **No.** The `show ip route` command was not added to the `tech` view, so execution is denied.

### 4. Why does `show run` list `show` and `show ip` parent nodes for the `tech` view?
- Cisco IOS requires the command tree hierarchy (`show` -> `show ip` -> `show ip interface`) to be enabled so the CLI parser can evaluate the permitted leaf command (`show ip interface brief`).

---

## Verification
- Verified OSPF neighbors using `show ip ospf neighbor` (State: FULL).
- Verified routing table via `show ip route`.
- Tested end-to-end connectivity using ICMP (`ping 192.168.3.3` from PC-A).

![Ping Test](screenshots/ping-test.png)