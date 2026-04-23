---
marp: true
theme: default
paginate: true
size: 16:9
header: 'AWS Bootcamp'
footer: 'IT Fundamentals: Module 01, Computers and Operating Systems'
---

# IT Fundamentals Module 01: Computers and Operating Systems

**Module 01 of 06**
Estimated lecture time: 15 to 20 minutes

<!-- Speaker notes: Welcome students to Module 01 of the IT Fundamentals primer. This module covers how computers work at a hardware level and introduces operating systems. Total lecture time is approximately 15 to 20 minutes. This is the first module, so no prior knowledge is assumed. -->

---

## Learning Objectives

By the end of this module, you will be able to:

- Identify the four main hardware components of a computer (CPU, RAM, storage, network interface) and describe their roles
- Define the purpose of an operating system and distinguish between Windows, macOS, and Linux

<!-- Speaker notes: Two objectives for this module, both at the Remember and Understand levels of Bloom's Taxonomy. Approximately 1 minute on this slide. -->

---

## Prerequisites and Agenda

**Prerequisites:** A computer with internet access, a terminal application

**Agenda:**
1. How computers work (hardware components)
2. Bits, bytes, and data sizes
3. What an operating system does
4. Common operating systems and Linux for cloud
5. The file system (Linux/macOS vs. Windows)

<!-- Speaker notes: No prior IT Fundamentals modules are required. This is the starting point. Approximately 1 minute. -->

---

# How Computers Work

<!-- Speaker notes: Transition slide. This section takes approximately 7 minutes across two slides. Start by asking students how they think a computer processes instructions. -->

---

## Hardware Components

| Component | Purpose | Analogy |
|-----------|---------|---------|
| CPU (Central Processing Unit) | Executes instructions and performs calculations | The brain of the computer |
| RAM (Random Access Memory) | Stores data temporarily while programs run | A desk for current work |
| Storage (SSD or HDD) | Stores data permanently, even when powered off | A filing cabinet |
| Network Interface | Connects to other computers via a network | A mailbox for messages |

- When you run a program, the OS loads it from **storage** into **RAM**, and the **CPU** executes the instructions
- When the program closes, its RAM is freed for other programs

<!-- Speaker notes: Emphasize that cloud servers use the same hardware components. In Module 04 (Compute: EC2), students will choose instance types based on CPU, RAM, and storage needs. Ask students to name the four components before showing the table. Approximately 4 minutes. -->

---

## Bits, Bytes, and Data Sizes

Computers store all data as binary digits (bits): either 0 or 1.

| Unit | Size | Example |
|------|------|---------|
| Bit | 1 or 0 | A single on/off switch |
| Byte | 8 bits | A single character ("A") |
| Kilobyte (KB) | 1,024 bytes | A short text file |
| Megabyte (MB) | 1,024 KB | A high-resolution photo |
| Gigabyte (GB) | 1,024 MB | A feature-length movie |
| Terabyte (TB) | 1,024 GB | A large database or video library |

<!-- Speaker notes: Cloud billing uses these units. Storage is priced per GB, data transfer per GB, and memory is measured in GB. In Module 15 (Cost Optimization), students will analyze data transfer costs using these units. Approximately 3 minutes. -->

---

## Bootcamp Connection: Hardware and Cloud

> **Bootcamp connection:** Understanding CPU, RAM, and storage prepares you for selecting EC2 instance types in Module 04: Compute (EC2). In Module 15: Cost Optimization, you will learn that you pay for compute, storage, and memory, so knowing these components helps you make cost-effective decisions.

- Cloud computing charges are based on storage, data transfer, and memory
- Understanding these units helps you estimate costs and right-size resources

<!-- Speaker notes: This is the bridge between hardware concepts and cloud computing. Every EC2 instance type is defined by its CPU, RAM, and storage configuration. Approximately 2 minutes. -->

---

# Operating Systems

<!-- Speaker notes: Transition slide. This section takes approximately 10 minutes across five slides. Ask students which OS they are currently using before proceeding. -->

---

## What the OS Does

An operating system sits between the hardware and the programs you run:

- **Manages CPU time:** allocates processing power to running programs
- **Manages memory:** decides which programs get how much RAM
- **Manages storage:** organizes files into directories, controls read/write access
- **Manages networking:** handles connections to other computers
- **Provides a user interface:** graphical (GUI) or command line (CLI)

<!-- Speaker notes: Ask students which operating system they are currently using. Most will say Windows or macOS. Explain that servers in the cloud almost always run Linux. Approximately 3 minutes. -->

---

## Common Operating Systems

| OS | Used For | Cloud Relevance |
|----|----------|-----------------|
| Windows | Desktops, enterprise servers | Windows Server runs on EC2 instances |
| macOS | Apple desktops and laptops | Common developer workstation; not a cloud server OS |
| Linux | Servers, cloud instances, containers | The dominant OS in cloud computing |

- Linux is the most important OS for cloud computing
- Most cloud servers, containers, and serverless environments run Linux
- You do not need to be a Linux expert, but you need basic command-line skills

<!-- Speaker notes: Emphasize that Linux dominance in the cloud is why the next module covers the command line. In Module 10 (Containers: ECS), students will see that Docker containers run on Linux. Approximately 3 minutes. -->

---

## The File System: Linux/macOS vs. Windows

**Linux/macOS:**
```
/                    (root directory)
├── home/student/    (your home directory)
├── etc/             (system configuration)
├── var/             (logs, databases)
└── tmp/             (temporary files)
```

**Windows:**
```
C:\                  (root of the C drive)
├── Users\student\   (your home directory)
├── Program Files\   (installed applications)
└── Windows\         (OS files)
```

<!-- Speaker notes: Key differences: Linux uses forward slashes (/) and is case-sensitive (File.txt and file.txt are different). Windows uses backslashes (\) and is case-insensitive. This matters when writing file paths in cloud configurations. Approximately 2 minutes. -->

---

## Bootcamp Connection: Operating Systems and Cloud

> **Bootcamp connection:** Understanding operating systems prepares you for choosing Amazon Machine Images (AMIs) in Module 04: Compute (EC2). In Module 10: Containers (ECS), containers run on Linux, so familiarity with Linux concepts is essential.

- Linux uses forward slashes (`/`) and is case-sensitive
- Windows uses backslashes (`\`) and is case-insensitive
- Cloud configurations (paths, file references) follow Linux conventions

<!-- Speaker notes: When students configure S3 bucket paths, Lambda function handlers, or ECS task definitions, they will use Linux-style paths. Approximately 1 minute. -->

---

## Discussion: Why Does Linux Dominate Cloud Computing?

Think about what makes an operating system suitable for running thousands of servers in a data center.

**What characteristics of Linux make it the preferred choice for cloud infrastructure?**

<!-- Speaker notes: Expected answers include: Linux is open source (no licensing fees at scale), lightweight (can run without a graphical interface), highly customizable, stable for long-running server workloads, and has strong community support. Give students 2 to 3 minutes to discuss, then summarize the key points. Approximately 5 minutes total. -->

---

## Instructor Notes: Common Questions

**Q: Do I need to learn Linux before the bootcamp?**
You do not need to be a Linux expert. You need basic command-line navigation (cd, ls, mkdir, cat). AWS CloudShell provides a Linux terminal in the browser.

**Q: Why do hardware specs matter for cloud computing?**
When you provision cloud resources, you choose instance types based on CPU, RAM, and storage. Understanding these components helps you select the right instance type and avoid overpaying.

<!-- Speaker notes: These are the two most common questions from students in this module. Address them proactively to reduce anxiety about Linux. Approximately 2 minutes. -->

---

## Key Takeaways

- Computers process instructions using CPU, RAM, and storage. Cloud servers use the same hardware components, managed by a cloud provider.
- An operating system manages hardware resources and provides services for applications. Linux is the dominant OS in cloud computing.
- Linux uses forward slashes and is case-sensitive. Cloud configurations follow Linux conventions.
- Understanding hardware and OS concepts prepares you for EC2 instance selection, container deployment, and cost optimization in the bootcamp.

<!-- Speaker notes: Four key takeaways for this module. Reinforce that every topic covered today connects directly to a bootcamp module. Approximately 1 minute. -->

---

## Lab Preview and Questions

**Lab 00: Exploring Your Computer and Operating System**

What you will do:
- Identify your operating system with `uname -a` (or `systeminfo` on Windows)
- Explore the file system hierarchy with `ls /`
- Check your system resources (RAM and storage)

**Duration:** 5 to 8 minutes
**No cloud resources created. Everything runs on your local machine.**

Questions?

<!-- Speaker notes: Walk through the lab structure briefly. Remind students that the lab is fully guided with exact commands and expected output for every step. Students using Windows should use Git Bash for command consistency. Take 2 to 3 minutes for questions before transitioning to the lab. -->
