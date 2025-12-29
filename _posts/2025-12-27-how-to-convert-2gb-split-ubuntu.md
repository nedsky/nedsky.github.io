# Convert Split VMDK Files into a Single ESXi Disk

To convert multiple 2GB split VMDK files into a **single, native ESXi disk format**, you will use the built-in ESXi utility **`vmkfstools`**.  This usually happens when we migrate from Virtualbox to ESXi.

> ⚠️ **Prerequisite**
> You **must complete snapshot consolidation** (Method 1 or 2 from the previous steps) **before** doing this.
> Converting while snapshots exist **will result in data loss**.

---

## Step 1: Prepare the VM

1. **Shut down** the **Ubuntu Desktop** VM completely

   > Disk conversion cannot be done while the VM is powered on.

2. Open an **SSH session** to the ESXi host

3. Navigate to the VM directory (where you previously ran `ls`):

```bash
cd /vmfs/volumes/<datastore-name>/Ubuntu-Desktop/
```

---

## Step 2: Run the Conversion Command

We will use the `-i` (**import/clone**) option. This creates a **new disk** in the correct format while leaving the original disk files intact as a backup.

### Run the command:

```bash
vmkfstools -i "Ubuntu-22.04.3.vmdk" -d thin "Ubuntu-Single.vmdk"
```

### What this means:

* **Ubuntu-22.04.3.vmdk**
  → The *descriptor* file that references the 2GB split chunks

* **-d thin**
  → Creates a **Thin Provisioned** disk (uses only actual data space)

* **Ubuntu-Single.vmdk**
  → The new, consolidated disk

### Result:

When the progress reaches **100%**, you will see:

* `Ubuntu-Single.vmdk` (descriptor)
* `Ubuntu-Single-flat.vmdk` (actual data)

---

## Step 3: Swap the Disks

Now point the VM to the new disk.

1. In the **ESXi Web Client**, right-click the VM → **Edit Settings**

2. Locate **Hard Disk 1** and click **Remove (X)**

   ⚠️ **CRITICAL:**
   **Do NOT** check **"Delete files from datastore"**
   You are only detaching the disk, not deleting it.

3. Click **Add Hard Disk → Existing Hard Disk**

4. Browse to the VM folder and select:

```
Ubuntu-Single.vmdk
```

5. **Save** the settings

6. **Power On** the VM and verify it boots correctly

---

## Step 4: Cleanup (After Verification)

Once you are **100% sure** the VM runs correctly using the new disk:

1. Return to your SSH session

2. Delete the old split disk files to reclaim space:

```bash
rm Ubuntu-22.04.3-s00*.vmdk
rm Ubuntu-22.04.3.vmdk
```

---

## ✅ Final Result

* Single, clean **ESXi-native VMDK**
* Thin provisioned
* No snapshots
* Easier backups and management

---