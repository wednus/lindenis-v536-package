# Tina Busybox Init Overview

## 1. Introduction
Tina uses the **Busybox init** system to boot. The process starts by executing `pseudo_init` (which mounts file systems like `/proc`, `/tmp`, `/sys`, `/etc`, and `/usr`), followed by invoking the `/sbin/init` process. The first startup script called by the init process is `/etc/init.d/rcS`.

---

## 2. Platform Customization
Different platforms have common and unique filesystem requirements. 

- **Base Files**: Located under `tina/package/busybox-init-base-files/files`, these provide the foundational files for all platforms.
- **Platform-Specific Files**: Found under `tina/target/allwinner/XXX/busybox-init-base-files`, these override files from the base directory if they have the same name.

### Example:
- File A: `tina/target/allwinner/r11-R11_pref1/busybox-init-base-files/etc/banner`
- File B: `tina/package/busybox-init-base-files/files/etc/banner`

The final file copied into the filesystem will be **File A**.

---

## 3. `pseudo_init` and `rcS`
`pseudo_init` and `rcS` contain shared platform code to avoid redundancy and facilitate easier maintenance. Customization of these files is **not allowed** for specific platforms. Instead, the versions in `tina/package/busybox-init-base-files/files` must be used.

### Platform-Specific Configurations:
- Add custom configurations to `rc.preboot` or `rc.final` as described in Section 4.

---

## 4. `rcS` Script

### 1. Functional Description
#### (1) Execute `/etc/init.d/rc.preboot`
- **Purpose**: Allows custom user configurations for fast booting.
- **Location**: Create `rc.preboot` under `tina/target/allwinner/XXX/busybox-init-base-files/etc/init.d/`.

#### (2) Configure Print Levels and Hostname

#### (3) Configure System Logs
- The default script is `rc.log` located under `tina/package/busybox-init-base-files/files/etc/init.d/`.
- To use a custom `rc.log`, create it under `tina/target/allwinner/XXX/busybox-init-base-files/etc/init.d/`.
- Enable the default `rc.log` in `make menuconfig`:
  ```
  Base system  --->
   busybox-init-base-files......................... Busybox init base system  --->
    [*]   Use the rc.log
  ```

#### (4) Mount UDISK

#### (5) Load Kernel Modules (`rc.modules`)
- Default script: `rc.modules` under `tina/package/busybox-init-base-files/files/etc/init.d/`.
- To use a custom `rc.modules`, create it in `tina/target/allwinner/XXX/busybox-init-base-files/etc/init.d/`.
- Enable the default `rc.modules` in `make menuconfig`:
  ```
  Base system  --->
   busybox-init-base-files......................... Busybox init base system  --->
    [*]   Use the rc.modules
  ```

#### (6) Start Scripts in `/etc/rc.d`
- `/etc/rc.d` scripts are linked to `/etc/init.d/`. By default, only `adbd` is executed.
- To start additional scripts:
  - Create a `load_script.conf` file under `tina/target/allwinner/XXX/busybox-init-base-files/etc/init.d/`.
  - List the applications to start, one per line (e.g., `adbd`).
  - Refer to `tina/package/busybox-init-base-files/files/etc/init.d/load_script.conf` for examples.
- Enable script auto-loading in `make menuconfig`:
  ```
  Base system  --->
   busybox-init-base-files......................... Busybox init base system  --->
    [*]   Auto load the script in /etc/rc.d
  ```

#### (7) OTA Initialization

#### (8) Execute `/etc/init.d/rc.final`
- Users can create `rc.final` under `tina/package/busybox-init-base-files/files/etc/init.d/` to define final startup applications.

### 2. Difference Between `rc.preboot` and `rc.final`
- **`rc.preboot`** runs earlier, before certain initialization tasks (e.g., mounting UDISK, auto-loading kernel modules, OTA initialization) are completed.
- **`rc.final`** runs after all these tasks have finished.

---

## 5. Writing Application Startup Scripts

### Example: Auto-Starting `smartlinkd`
**File**: `tina/package/allwinner/smartlinkd/files/smartlinkd.init`

#### Method 1: Using Specific Format
Refer to the detailed format guidelines:
- [OpenWRT Procd Init Scripts](https://wiki.openwrt.org/inbox/procd-init-scripts)
- [OpenWRT Init Scripts Reference](https://wiki.openwrt.org/doc/techref/initscripts)

##### (1) Procd-Style Script:
```sh
#!/bin/sh /etc/rc.common
START=98
STOP=98

USE_PROCD=1
PROG=smartlinkd

start_service() {
    procd_open_instance
    procd_set_param command $PROG -d
    procd_close_instance
}

shutdown() {
    echo shutdown
}
```

##### (2) Sys-Style Script:
```sh
#!/bin/sh /etc/rc.common
START=98
STOP=98

PROG=smartlinkd

start() {
  smartlinkd -d &
}
```

- These scripts support both Procd and Busybox init systems.
- If using Busybox init, add `smartlinkd` to `load_script.conf`.

#### Method 2: No Specific Format
Create `rc.preboot` or `rc.final` and add the startup commands for `smartlinkd`.
