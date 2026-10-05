# Linux Project 01: Network Foundation and Persistent Identity

> **Platform:** Rocky Linux 9 VM in Xen Orchestra\
> **Account:** `root`\
> **Standard:** Keep SELinux enforcing and firewalld enabled. Persistent
> work must survive reboot.

# RHCSA Exam Question #1

Configure the network:

-   Assign hostname and IP addresses for your virtual machines as per
    the following details:
    -   Hostname - `system1.eight.example.com`
    -   IP address - `192.168.55.150`
    -   Netmask - `255.255.255.0`
    -   Gateway - `192.168.55.1`
    -   DNS Name Server - `8.8.8.8`

# Exam Solution

### Step 1: Exam VM Information

There are two (2) Virtual Machines given on the exam.

-   Node 1
-   Node 2

### Step 2: NetworkManager, `nmtui`, `nmcli`, and Repositories

In Red Hat exam environments (such as RHCSA or RHCE), network and
repository configuration issues on Question 1 are a common scenario.
However, there are a few important technical facts to clarify regarding
how `nmtui`, `nmcli`, and repositories work together.

#### `nmtui` relies on NetworkManager

`nmtui` is a text-based user interface for NetworkManager. If the
NetworkManager service is stopped or failing, `nmtui` will not work
properly either.

You generally do not need to install `nmtui` during the exam because it
is normally provided as part of the NetworkManager TUI tooling. If
`nmcli` is not working because NetworkManager itself is not running
correctly, check or restart the service:

``` bash
systemctl status NetworkManager
systemctl restart NetworkManager
```

#### Repository files do not depend on `nmtui`

Package repository configuration under:

``` text
/etc/yum.repos.d/
```

does not depend on `nmtui`.

Repositories need working network connectivity so the system can reach
their configured base URLs. This normally requires:

-   A valid IP address
-   Correct subnet/prefix
-   A default gateway when the repository is on another network
-   Working DNS when repository URLs use hostnames

Once network connectivity is working, `dnf` can communicate with the
configured repository server.

## Verify and Fix Network Connectivity

Check whether the network interface is up and has an IP address:

``` bash
ip addr
nmcli connection show
```

If NetworkManager is unresponsive, check or restart it:

``` bash
systemctl status NetworkManager
systemctl restart NetworkManager
```

> **NOTE:** If `nmcli` commands fail because of command syntax, you can
> use `nmtui` to configure the IP address, subnet mask, gateway, and DNS
> visually. After configuration, verify connectivity to the gateway and
> any required repository server.

## Install the Tools if Needed for Practice in NEXUS LAB

On Red Hat-family systems, `nmcli` is provided by NetworkManager, while
the text UI is provided by the NetworkManager TUI package.

For a practice VM:

``` bash
dnf install NetworkManager NetworkManager-tui -y

systemctl enable --now NetworkManager

nmcli --version
nmtui
```

> During an actual exam, avoid installing packages unless required and
> unless the required repositories are already accessible.

## Important Warning for Remote SSH Sessions

When you activate or deactivate a network connection, NetworkManager
immediately applies the change.

If you deactivate the active connection that your SSH session is using,
your SSH connection can drop immediately.

### Safer Method When Working Remotely

#### Step 1: Edit the Settings in `nmtui`

Launch:

``` bash
nmtui
```

Navigate to:

``` text
Edit a connection
```

Make the required changes to:

-   IP address
-   Prefix/subnet
-   Gateway
-   DNS

Then select:

``` text
OK
```

and:

``` text
Quit
```

> If you are configuring the machine through SSH, avoid unnecessarily
> deactivating the connection through the **Activate a connection**
> menu.

#### Step 2: Apply the Connection with `nmcli`

Reload the NetworkManager connection profiles:

``` bash
nmcli connection reload
```

Then bring the required connection up:

``` bash
nmcli connection up <connection-name>
```

For example:

``` bash
nmcli connection show
```

Identify the connection name and then use it with:

``` bash
nmcli connection up "<connection-name>"
```

## Verification

After applying the configuration, verify the network settings:

``` bash
ip addr show
nmcli connection show
```

You can also verify routes:

``` bash
ip route
```

## Remote Session Recovery Tip

If you are working remotely, changing the IP address of the interface
carrying your SSH session can disconnect you regardless of the command
used. Make sure you have console access through Xen Orchestra or another
recovery method before changing management networking.

For a practice environment, you may use:

``` bash
nmcli connection up "<connection-name>" || systemctl restart NetworkManager
```

This can retry NetworkManager service initialization if activation
fails, but it does **not** guarantee that an SSH session will remain
reachable after an incorrect IP, gateway, VLAN, or routing
configuration.

# Identify the Interface and Connection

Before making changes, identify the network device and its
NetworkManager connection profile.

Run:

``` bash
nmcli device status
```

Then:

``` bash
nmcli connection show
```

Check the current IP addresses:

``` bash
ip -brief address
```

Check the routing table:

``` bash
ip route
```

## What Each Command Tells You

  -----------------------------------------------------------------------
  Command                             Purpose
  ----------------------------------- -----------------------------------
  `nmcli device status`               Shows network devices and their
                                      current state

  `nmcli connection show`             Shows NetworkManager connection
                                      profiles

  `ip -brief address`                 Shows interfaces and IP addresses
                                      in a compact format

  `ip route`                          Shows the current routing table and
                                      default gateway

  `systemctl status NetworkManager`   Checks whether NetworkManager is
                                      running

  `nmtui`                             Opens the text-based NetworkManager
                                      configuration interface
  -----------------------------------------------------------------------

# Target Network Configuration

The final persistent configuration for `system1` should be:

  Setting        Required Value
  -------------- -----------------------------
  Hostname       `system1.eight.example.com`
  IPv4 Address   `192.168.55.150`
  Netmask        `255.255.255.0`
  Prefix         `/24`
  Gateway        `192.168.55.1`
  DNS            `8.8.8.8`

Remember:

``` text
255.255.255.0 = /24
```

# Persistence Requirement (NOT REQUIRED ON THE EXAM)

Because this project requires the configuration to survive a reboot,
changes should be made through NetworkManager connection profiles rather
than relying only on temporary `ip` commands.

After completing the network configuration, reboot the practice VM and
verify that:

``` bash
hostname
ip -brief address
ip route
nmcli connection show
```

still show the required persistent settings.

```bash

nmcli device status
nmcli connection show
ip -brief address
ip route
```




---
# Job Interview Activity

I was involved in commissioning a new Linux server that required a Stable network identity. This is required before repositories and services can be deployed. 

## 1. What I did

1. I had to submit a change request for CAB approval.
2. Created a NCD that had list of target servers and IP's. This document was submitted for approval. NCD has following steps:
- Pre-implementation Steps
- Implementation Steps
- Validation and test reboot persistence sometimes.
- Roll Back
2. This change was implemented during a Patching Window between 10:30am and 5:30amthe change.

## 2. Safety and Prerequisites

- Confirm the assigned VM with `hostnamectl` and `ip -brief address`.
- Confirm the account with `whoami`; expected output is `root`.
- Create a Xen Orchestra snapshot before disruptive work.
- Save pre-change evidence under `/root/nexusventures-project01/`.
- Use only a unique IP allocated to you.
- Never perform the activation step without console access.

## 3. Completion Checklist

- [ ] Correct VM confirmed
- [ ] Snapshot created when required
- [ ] Original state recorded
- [ ] Configuration completed
- [ ] Validation passed
- [ ] SELinux remains enforcing
- [ ] firewalld remains enabled
- [ ] Reboot persistence tested when required
- [ ] Evidence collected
- [ ] Rollback understood

## 4. Review Questions

1. What business problem did this project solve?
2. Which command proved the configuration was active?
3. Which command proved it was persistent?
4. What could fail, and how would you roll back?