
# CPU Affinity Experiment Guide

## Introduction
This guide explains how to perform an experiment to understand and utilize CPU affinity on Linux systems. It involves running CPU-intensive tasks with the `stress-ng` tool and then adjusting their CPU affinity using the `taskset` command to specific cores. This experiment demonstrates how to manage process scheduling on multi-core systems effectively.

## Prerequisites
- A Linux system with multiple CPU cores.
- Installation of `stress-ng` for generating CPU load.
- Understanding of CPU cores and NUMA nodes architecture on your system.

## Installation
### Install `stress-ng`
#### On Ubuntu/Debian:
```bash
sudo apt-get update
sudo apt-get install stress-ng
```

#### On Fedora/CentOS/RHEL:
```bash
sudo yum install stress-ng
```
or
```bash
sudo dnf install stress-ng
```

## Experiment Steps

### 1. Generate CPU Load
Start by generating a CPU load that utilizes specific cores. We will use `stress-ng` to create a workload that stresses two CPU cores:

```bash
stress-ng -c 2 --cpu-method matrixprod 
```
or you can directly assign two cores of your choice liks this:

```bash
taskset -c 3,10 stress-ng -c 2 --cpu-method matrixprod
```

### 2. Check Process IDs
Find the Process IDs (PIDs) of the `stress-ng` processes:

```bash
pgrep stress-ng
```

### 3. Set Initial CPU Affinity
Choose initial cores (e.g., 5 and 13) and set the CPU affinity for the `stress-ng` processes:

```bash
for PID in $(pgrep stress-ng); do
    taskset -p -c 5,13 $PID
done
```

### 4. Monitor and Observe
Monitor the CPU usage using tools like `top` or `htop`. Observe how the specified cores' utilization increases due to the `stress-ng` workload.

### 5. Change CPU Affinity
While the `stress-ng` processes are still running, change their CPU affinity to different cores (e.g., 7 and 19) to observe the impact:

```bash
for PID in $(pgrep stress-ng); do
    taskset -p -c 7,19 $PID
done
```

### 6. Shift Task Between NUMA Nodes
If your system supports NUMA, further experiment by shifting the workload between NUMA nodes. This requires knowledge of which cores belong to which NUMA nodes.

### 7. Conclusion
This experiment helps understand how CPU affinity can be used to control and optimize the scheduling of processes on specific cores, improving performance for CPU-bound tasks.

## Cleanup
After completing the experiment, you can kill the `stress-ng` processes:

```bash
pkill stress-ng
```

## Additional Notes
- Adjust the number of cores and specific core numbers based on your system's configuration and the objectives of your experiment.
- Use `lstopo` or `lscpu` to understand your system's CPU and NUMA node architecture.
