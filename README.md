# 🚀 **Ultimate Guide to cgroups v2 in Linux**  

### 🧐 **What are cgroups v2?**  
**Control Groups (cgroups v2)** allow you to **manage CPU, memory, and I/O usage** for processes, preventing any single app from consuming excessive resources.  

---

## 🎯 **Why Use cgroups v2?**  
✅ **Better Performance** – Prevents resource starvation.  
✅ **Fair Resource Sharing** – Ideal for multi-tenant applications.  
✅ **Perfect for Containers & Servers** – Ensures stability for Docker, Kubernetes, and system services.  
✅ **Easy Management** – Unified control over system resources.  

---

## ⚡ **Preferred Method (Tested & Works Best!)**  

### 1️⃣ **Create a cgroup named `sugarcrm` for CPU and Memory control**  
```bash
sudo cgcreate -g cpuset,memory:sugarcrm
```

### 2️⃣ **Set CPU and Memory Limits**  
🔹 **Limit CPU to specific cores (0–14, meaning 15 CPUs)**  
```bash
sudo cgset -r cpuset.cpus="0-14" sugarcrm
```
🔹 **Assign memory nodes (usually 0 for a single socket CPU machine)**  
```bash
sudo cgset -r cpuset.mems="0" sugarcrm
```
🔹 **Limit memory usage to 60GB**  
```bash
sudo cgset -r memory.max=64424509440 sugarcrm
```

### 3️⃣ **Run a Go application inside this cgroup**  
```bash
sudo cgexec -g cpuset,memory:sugarcrm /usr/local/go/bin/go run /var/www/html/sugarcrm/scheduler.go >> /dev/null 2>&1
```
```bash
sudo cgexec -g cpuset,memory:sugarcrm /var/www/html/sugarcrm/bin/scheduler >> /dev/null 2>&1
```
✅ **This ensures the application runs within the defined resource limits!**  

---

## 🚀 **Alternative Ways to Use cgroups v2**  

### 📌 **Check if cgroups v2 is enabled**
```bash
mount | grep cgroup2
```
✅ If you see **cgroup2**, it's enabled!  
❌ If not, enable it in GRUB:
```bash
GRUB_CMDLINE_LINUX="systemd.unified_cgroup_hierarchy=1"
```
Then update and reboot:
```bash
sudo update-grub && sudo reboot
```

### 📁 **Manually Creating and Managing cgroups**  
1️⃣ **Create a cgroup manually**  
```bash
mkdir /sys/fs/cgroup/mygroup
```

2️⃣ **Set Resource Limits**  
💾 **Limit Memory (100MB max):**  
```bash
echo $((100*1024*1024)) > /sys/fs/cgroup/mygroup/memory.max
```
⚡ **Limit CPU Usage (50% of a single core):**  
```bash
echo 50000 > /sys/fs/cgroup/mygroup/cpu.max
```
📥 **Limit Disk I/O Speed (Read/Write 10MB/s):**  
```bash
echo "259:0 10485760" > /sys/fs/cgroup/mygroup/io.max
```

3️⃣ **Assign a Process to the cgroup**  
```bash
echo <PID> > /sys/fs/cgroup/mygroup/cgroup.procs
```

---

## 🎯 **Using cgroups v2 with Systemd (Recommended for Services)**  
🔹 **Example: Limit CPU and memory for a service**  
```bash
sudo systemctl set-property nginx.service MemoryMax=500M CPUQuota=50%
```
🔹 Restart to apply changes:  
```bash
sudo systemctl restart nginx
```

---

## 🎉 **Why cgroups v2 is the Best?**  
🔹 **Unified Control** – No separate hierarchies like cgroups v1.  
🔹 **Better Kernel Support** – Works seamlessly with Linux 4.5+ and newer.  
🔹 **Ideal for Containers & Servers** – Essential for **Docker, Kubernetes, and Web Apps**.  
🔹 **Prevents System Slowdowns** – Ensures fair resource usage across processes.  

---

🔥 **Conclusion:** If you're managing high-performance workloads, multi-tenant SaaS apps, or server workloads, **cgroups v2 is the best way to ensure resource efficiency!**  
