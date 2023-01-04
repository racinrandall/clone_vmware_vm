add_disks
=========

This role has two main purposes.  First is to expand the primary (OS) disk within RHEL for VMs that were cloned with a larger OS disk than the original template.  The second purpose is to add any additional disks required for the specific VM while also creating the additional Volume Groups, Logical Volumes and persistently mounting them.

Role Variables
--------------

These are the variables for the is role, their purpose and what their default setting is.  They my be overwritten using host/group vars, or passed in at the time the role is run:

- `fs_type` - This is the type of filesystem to use for all newly created logical volumes | string (*default: **xfs***)  
- `vm_compute_resource` - This is the name of the compute resource as defined in Satellite | string options are {hqwvcsa01|denvcsa01} (*default: **{{ inventory_hostname[:3] }}vcsa01***)  
- `vmware_user` This is the name of the service account for Ansible to use to reach into VMware | string options are {DEN_SAT_SvcAcc_PQD|HQW_SAT_SvcAcc_PQD} (*default: **{{ inventory_hostname[:3] | upper }}_SAT_SvcAcc_PQD***)  
- `vmware_pass` - This is teh password for the `vmware_user` account.  This is set in an Ansible Vault encrypted file. | string  (*default: **current password***)  

The following variables are required to be set as host varialbes on each individual host.  Refrence and existing host in the invetory:
- `additional_disks` Dictionary of additional disk:  
  - `physical_drive` - This is what the new disk will show up as in Linux | string (*set this to: **/dev/sdb***)  
  - `size` - The size to create additional disk.  Due to rounding, make sure this disk is slightly bigger than the sum of all additional mounts you need. | Human readable size, generally in G (*example: **525G***)  
  - `vol_group` - This is the name the new volume group that will be created for the additional disk | string make it something related to purpose of box/software being installed (*example: **oradb** for Oracle DB Server*)  
- `additional_mounts` - List of Dictionary Items that define additional logical volumes and mounts.  Can have multiple of the following set:
  - `mount_point` - this specifies the absolute path location to mount the logical volume.  | string (*example: **/u01***)  
  - `size` - This is the size of the logical volume.  The sum of all logical volumes must be less than the size for the additional disks variable. | Human readable size, generally in G (*example: **100G***)  
  - `vol_group` - This is the volume group for the above additional disk and must match. | string (*example: **oradb***)  

Dependencies
------------

This role requires the community.general collection and is specified in the requirements.yml file.

Playbook
----------------

A Playbook to run this role is included in the root directory of the project.  Additionally this role is called in the site.yml playbook.


Author Information
------------------

Originally created for US Mint by:  
Charles Randall  
Senior Consultant  
Red Hat
