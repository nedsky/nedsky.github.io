# Increasing Disk Space on Ubuntu VM (ESXi 6.5)

Increasing disk space in a VM is a **two-step process**:

1. Expand the virtual disk in **ESXi**
2. Resize the partition and filesystem inside **Ubuntu**

> ⚠️ Your `/dev/sda3` is currently at **99% capacity**. Act quickly to avoid system instability.

---

## Step 1: Expand Disk in ESXi 6.5

1. Log in to the **ESXi 6.5 Web Client**
2. **Shut down** the Ubuntu VM (recommended for safety)
3. Right-click the VM → **Edit Settings**
4. Locate **Hard disk 1**
5. Increase the disk size (example: from **30 GB → 100 GB**)
6. Click **Save**
7. Power the VM back on

---

## Step 2: Expand Partition in Ubuntu

Since this is a VM, the safest and easiest approach is using `growpart` and `resize2fs`.

### 1. Install Required Tools

If not already installed:

```bash
sudo apt update
sudo apt install cloud-guest-utils
```

---

### 2. Identify the Root Partition

From your `df -h` output, the root filesystem is on:

```
/dev/sda3
```

This means:

* Disk: `/dev/sda`
* Partition: `3`

---

### 3. Grow the Partition

Resize the partition to consume the newly added disk space:

```bash
sudo growpart /dev/sda 3
```

> ℹ️ Note the space between `sda` and `3`

---

### 4. Resize the Filesystem

Now expand the ext4 filesystem to fill the resized partition:

```bash
sudo resize2fs /dev/sda3
```

---

## Step 3: Verify the Changes

Run:

```bash
df -h
```

You should now see:

* `/dev/sda3` with the new size
* Significantly lower **Use%**

---

## Troubleshooting: "No space left on device"

If the system is too full to install packages, free space immediately:

### Clear APT Cache

```bash
sudo apt-get clean
```

### Remove Old Logs

```bash
sudo journalctl --vacuum-time=1d
```

---

✅ Once resized, your Ubuntu VM will fully utilize the expanded ESXi disk without data loss.
