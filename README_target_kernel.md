
# Introduction

This branch provides kernel-specific backport patches for Linux kernel v6.6 targeting core kernel subsystems that are required for full Intel® Xe Graphics Driver support.

These patches are already merged upstream in newer stable kernels but are not yet present in the target kernel versions (VELinux 6.6). Without these patches, certain xe driver features will have limitations or will not work at all. These patches enable advanced features when used with [releases/main](https://github.com/intel-gpu/xekmd-backports/tree/releases/main) DKMS modules.


# Contains
|   |   | |
|-- |---|-- |
|1. | Patches from stable-kernel based on features.| backport/patches/base | |
|2. | Script to create kernel | backport.sh|
|4. | File containing list of patches to pick-up and apply on top of kernel | series|

Note: 
1. Patches present in base will be removed once merged in target kernel.

# Patches and Features
Below are list of patches to enable the features , based on feature requirement apply them to your existing kernel build to enable Xe features.

| Feature | Kernel Subsystem | Patch Path |
|---------|-----------------|-----------|
| PCIe – RAS AER Error handling | PCI | `backport/patches/base/0001-PCI-AER-Allow-drivers-to-opt-in-to-Bus-Reset-on-Non-.patch` |
| PCIe – RAS AER Error handling | PCI | `backport/patches/base/0001-PCI-ERR-Ensure-error-recoverability-at-all-times.patch` |

# Usage
We download a stable Linux kernel (v6.6) and maintain all  listed in series files.
These patches are organized in the base. When creating a kernel tree, the patches from the series files are applied on top of the downloaded kernel, resulting in a custom kernel tree with all required changes.
backport.sh < options >

||options |description |
|-- |--|--| 
|1. |create-tree| Create kernel tree based on given option <base> (default)|
|2. |delete-tree| Delete the tree|
|3. |reset-tree| Delete the existing tree and re-create it|
|4. |override| Overrides existing tree|

## Examples

### Create tree with out-of-tree (OOT) patches included
```bash
./backport.sh -c
```
This applies all which are present in `backport/patches/base/` directory.

# Debugging

## Building and Testing Custom Kernel

After creating the custom kernel tree using `./backport.sh`, you can build and test your changes:

### 1. Navigate to Kernel Tree and Make Changes
```bash
cd <generated-kernel-tree>
```

### 2. First Build - Create DEB Package
For the initial build, compile and install the complete DEB package:

# Contributing

## Adding Patches

### 1. Create Your Patch in kernel tree
Generate patch files from your generated kernel folder:
```bash
git format-patch -N1 <commit-hash> --zero-commit
```

### 2. Choose Location
Place the patch in base directory:

#### Placement Rules:
- **`backport/patches/base/`** - place all patches ib base directory

### 3. Copy Patch File
```bash
# For base patches
cp <your-patch>.patch backport/patches/base/

```

### 4. Update Series File
Add your created patch to the "series" file in the repo folder.

* For base patches - add to the end of base section "# base"


**Important Notes:**
- **Patch numbering**: All patches must start with `0001-` prefix (not sequential numbering)
- **Patch order matters**: Place patches according to their dependencies in the series file
- **Fix Ordering**:  Fixes should be added in series as per the sequence of landing on upstream kernel.
- **Manual editing**: You can also manually edit the series file to insert patches at specific positions

### 5. Verify
Test that patches apply correctly. This will generate the kernel tree with your changes:
```bash
./backport.sh create-tree [base]
```

**Important:** Ensure there are **no conflicts** during tree generation. If conflicts occur, the patch cannot be merged and must be resolved before submission.

After successful tree generation, verify your changes:

### 6. Submit
- Commit your changes (patch file + series file update)
- Create a pull request with clear description
- Reference upstream commit or mailing list discussion

# License

This work is a subset of the Linux kernel as such we keep the kernel's
Copyright practice. Some files may have their own copyright and in those
cases the license is mentioned in the file.
