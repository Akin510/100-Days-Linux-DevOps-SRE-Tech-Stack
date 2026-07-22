# NexusVentures RHCSA Mini-Project Series

This package converts the 20 numbered tasks in the supplied RHCSA 9 sample paper into guided Rocky Linux 9 projects for students using root accounts on Xen Orchestra VMs.

## Lab Standards

- Keep SELinux enforcing and firewalld enabled.
- Validate persistent work after reboot.
- Create a Xen Orchestra snapshot before network, boot, disk, LVM, VDO, SSH, and container changes.
- Use a unique instructor-assigned IP for every student VM.
- Use only instructor-assigned blank disks for destructive storage projects.

## Project Index

1. [Network Foundation and Persistent Identity](NexusVentures_Project_01_network-foundation.md)
2. [Controlled Software Repository Configuration](NexusVentures_Project_02_software-repositories.md)
3. [Secure Apache Service on TCP Port 82](NexusVentures_Project_03_web-port-82-selinux.md)
4. [Identity and Administrative Group Provisioning](NexusVentures_Project_04_users-groups.md)
5. [Secure Collaborative Administration Workspace](NexusVentures_Project_05_collaborative-directory.md)
6. [On-Demand NFS Access with AutoFS](NexusVentures_Project_06_autofs-nfs.md)
7. [Scheduled User Work and Cron Access Control](NexusVentures_Project_07_cron-access-control.md)
8. [Fine-Grained File Access with ACLs](NexusVentures_Project_08_acl-permissions.md)
9. [Reliable Time Synchronization with Chrony](NexusVentures_Project_09_chrony-ntp.md)
10. [Large Configuration File Discovery and Collection](NexusVentures_Project_10_large-file-discovery.md)
11. [Identity, Archive, and Private Permission Defaults](NexusVentures_Project_11_identity-archive-umask.md)
12. [Password Aging and Delegated Administration](NexusVentures_Project_12_password-aging-sudo.md)
13. [Robust Bash File Collection Automation](NexusVentures_Project_13_bash-file-collector.md)
14. [Root Password Recovery through GRUB](NexusVentures_Project_14_root-password-recovery.md)
15. [Swap and LVM Database Storage](NexusVentures_Project_15_swap-lvm-storage.md)
16. [Thin-Provisioned VDO Storage Volume](NexusVentures_Project_16_vdo-storage.md)
17. [Online LVM and Filesystem Expansion](NexusVentures_Project_17_lvm-expansion.md)
18. [Automatic Performance Profile Selection with TuneD](NexusVentures_Project_18_tuned-profile.md)
19. [Rootless Rsyslog Container as a Startup Service](NexusVentures_Project_19_rootless-container-service.md)
20. [Persistent Journal Storage for the Logserver Container](NexusVentures_Project_20_container-persistent-journal.md)

## Dependencies

- Project 03 builds on Projects 01 and 02.
- Projects 05, 07, 08, 11, and 12 use identities from Project 04.
- Project 17 expands the storage created in Project 15.
- Project 20 extends the rootless container from Project 19.

## Submission Standard

For every project, students submit pre-change evidence, commands used, explanations, validation output, reboot evidence where applicable, and rollback steps.
