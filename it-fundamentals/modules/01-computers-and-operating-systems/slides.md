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

## Hardware Components

| Component | Purpose | Analogy |
|-----------|---------|---------|
| CPU | Executes instructions | The brain |
| RAM | Temporary storage for running programs | A desk for current work |
| Storage (SSD/HDD) | Permanent data storage | A filing cabinet |
| Network Interface | Connects to other computers | A mailbox |

- The CPU processes instructions loaded from storage into RAM
- When a program closes, its RAM is freed for other programs

<!-- Speaker notes: Emphasize that cloud servers use the same hardware components. In Module 04 (Compute: EC2), students will choose instance types based on CPU, RAM, and storage needs. Approximately 4 minutes on this slide. -->

---

## Bits, Bytes, and Data Sizes

| Unit | Size | Example |
|------|------|---------|
| Bit | 1 or 0 | A single on/off switch |
| Byte | 8 bits | One character ("A") |
| Kilobyte (KB) | 1,024 bytes | A short text file |
| Megabyte (MB) | 1,024 KB | A high-resolution photo |
| Gigabyte (GB) | 1,024 MB | A feature-length movie |

- Cloud computing charges are based on storage, data transfer, and memory
- Understanding these units helps you estimate costs

<!-- Speaker notes: Keep this brief. The key takeaway is that cloud billing uses these units. In Module 15 (Cost Optimization), students will analyze data transfer costs. Approximately 3 minutes. -->

---

## Operating Systems: What the OS Does

An operating system manages hardware and provides services for applications:

- Allocates CPU time to running programs
- Manages memory (RAM) across applications
- Organizes files into directories
- Handles network connections
- Provides a user interface (graphical or command line)

<!-- Speaker notes: Ask students which operating system they are currently using. Most will say Windows or macOS. Explain that servers in the cloud almost always run Linux. Approximately 3 minutes. -->

---

## Linux for Cloud Computing

| OS | Primary Use | Cloud Relevance |
|----|-------------|-----------------|
| Windows | Desktops, enterprise servers | Runs on EC2 instances |
| macOS | Developer workstations | Not used as a cloud server OS |
| Linux | Servers, containers, serverless | The dominant OS in the cloud |

- Most cloud servers, containers, and serverless environments run Linux
- You do not need to be a Linux expert, but you need basic command-line skills
- Linux uses forward slashes (`/`) and is case-sensitive

<!-- Speaker notes: Emphasize that Linux dominance in the cloud is why the next module covers the command line. In Module 10 (Containers: ECS), students will see that Docker containers run on Linux. Approximately 4 minutes. -->

---

## Discussion: Why Does Linux Dominate Cloud Computing?

Think about what makes an operating system suitable for running thousands of servers in a data center.

**What characteristics of Linux make it the preferred choice for cloud infrastructure?**

<!-- Speaker notes: Expected answers include: Linux is open source (no licensing fees at scale), lightweight (can run without a graphical interface), highly customizable, stable for long-running server workloads, and has strong community support. Give students 2 to 3 minutes to discuss, then summarize the key points. Approximately 5 minutes total. -->

---

## Key Takeaways

- Computers process instructions using CPU, RAM, and storage. Cloud servers use the same components.
- Linux is the dominant OS in cloud computing. Basic command-line skills are essential.

<!-- Speaker notes: Two key takeaways for this module. Reinforce that every topic covered today connects directly to a bootcamp module. Approximately 1 minute. -->

---

## Lab Preview and Questions

**Lab 00: Exploring Your Computer and Operating System**

What you will do:
- Identify your operating system with `uname -a`
- Explore the file system hierarchy with `ls /`
- Check your system resources (RAM and storage)

**Duration:** 5 to 8 minutes
**No cloud resources created. Everything runs on your local machine.**

Questions?

<!-- Speaker notes: Walk through the lab structure briefly. Remind students that the lab is fully guided with exact commands and expected output for every step. Students using Windows should use Git Bash for command consistency. Take 2 to 3 minutes for questions before transitioning to the lab. -->
