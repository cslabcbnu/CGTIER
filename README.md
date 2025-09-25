# CGTIER: Implementation and Evaluation of Cgroup Function for Active Memory Constraints in Tiered Memory Environment

[![Kernel Version](https://img.shields.io/badge/Linux%20Kernel-6.15.6-blue.svg)](https://www.kernel.org/)

**CGTIER** is a novel Linux kernel module designed for tier-aware memory control and isolation per cgroup in heterogeneous memory systems composed of DRAM, PMEM, CXL, and other memory types.

## 🧐 The Problem

Modern computing environments are increasingly adopting a **tiered memory architecture**, which combines high-speed DRAM with large-capacity PMEM or CXL memory. However, the existing Cgroup memory controller in the Linux kernel has a fundamental limitation: it is not tier-aware.

* **No Tier Distinction:** It treats all memory as a single, flat tier, making fine-grained, per-tier control impossible.
* **Infinite Reclamation Loop:** When memory pages are demoted from a higher tier (e.g., DRAM) to a lower tier (e.g., CXL), the cgroup's page counter does not decrease. This tricks the kernel into believing the memory limit has been reached, triggering an unnecessary and infinite memory reclamation process that causes severe performance degradation.

## ✨ Our Solution: CGTIER

To address these issues, we propose **CGTIER**. By implementing the following core features into the Linux kernel (v6.15.6), CGTIER provides a cgroup memory controller optimized for **tiered memory environments**.

### Key Features

* **🎯 Per-tier Page Counters:** Implements separate page counters for each memory tier, allowing for precise tracking of how much memory each cgroup is using on a per-tier basis.
* **🔧 New Control Interface (`tiered_N_high`):** Introduces a new control file, `memory.tiered_N.high` (where N is the tier ID), to the cgroupfs. This allows users to directly set a soft memory limit for each tier.
* **🧠 Automatic Demotion:** When a cgroup's memory usage on a specific tier reaches the configured `tiered_N_high` limit, excess memory pages are automatically demoted to the next lower tier. This ensures service continuity and prevents performance degradation.

## 🚀 Performance Evaluation

We validated the performance of CGTIER using a variety of benchmarks, including **SHINOBI**, **7-zip**, and **HPL**.

\* **SHINOBI** Benchmark (https://github.com/cslabcbnu/SHINOBI)

* **Precise Control:** CGTIER **accurately** enforced the user-defined per-tier memory limits and successfully demoted excess memory to lower tiers as intended.
* **Minimal Overhead:** The performance impact on CPU-intensive workloads was negligible. CGTIER demonstrated **insignificant overhead** compared to the vanilla Linux kernel.
* **Performance Improvement:** By resolving the unnecessary reclamation and performance degradation issues found in the default kernel, CGTIER improves overall system stability and efficiency.

## 💡 Future Work

CGTIER presents a practical and efficient solution for isolating and limiting memory resources for workloads in **tiered memory environments**. Our future plans involve **integrating CGTIER with container runtimes (e.g., Docker, Kubernetes)** to enable more granular and efficient memory management in cloud and data center environments.
