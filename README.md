# Create KVM VMs automatically using Ansible and custom Ubuntu Image with auto-install 

This Lab demonstrate the creation of KVM VMs using Ansible, and perform the auto installation of the OS using a custom Ubuntu 18.04 image.
# 

## Prerequisites
01 Ubuntu 18.04 custom ISO image (you can build one using Cubic Ubuntu Image Builder).
At least 1 machine with Ansible & KVM pre-installed

## The steps of this tutorial

1. Create Ansible files (playbook, hosts, vars and ansible.cfg)
2. Run the playbook to create and install the Ubuntu Image automatically

### 1. Create Ansible files (playbook, hosts, vars and ansible.cfg)

##### This is the custom ansible.cfg file used in this lab
~~~
[defaults]

inventory= ./hosts
timeout=20
host_key_checking=false
deprecation_warnings=False
vault_password_file = ./.vault_pass
~~~

##### This is the custom inventory file (hosts) used in this lab
~~~
[kvm_host:vars]
ansible_ssh_user={{ host_user }}
ansible_ssh_pass={{ host_user_pass }}
ansible_become_password={{ host_user_pass }}
vm_name=ubuntu-18.04-vm
vm_vcpu=1
vm_memory=1024
vm_media=~/ubuntu-18.04.3/ubuntu-18.04.3-2021.10.03-server-amd64.iso
vm_disk_size=10
vm_os_type=linux
vm_os_variant=ubuntu18.04

[kvm_host]
192.168.122.1
~~~

- In this lab, I used Ansible Vault to encrypt my credentials such as the user's password. 
- In your case, if you would skip this part you can just put your variables there in plain text.

##### This is the configuration used in the playbook vm_manage.yaml 
~~~
---
- name: Get Running Configuration
  hosts: kvm_host
  become: true
  gather_facts: false

  tasks:

    - name: Create A New KVM VM
      shell: "virt-install --name {{ vm_name }} \
                           --vcpus {{ vm_vcpu }} \
                           --memory {{ vm_memory }} \
                           --cdrom {{ vm_media }} \
                           --disk size={{ vm_disk_size }} \
                           --os-type {{ vm_os_type }} \
                           --os-variant {{ vm_os_variant }}"

    # - name: List All VMs
    #   community.libvirt.virt:
    #     command: list_vms
    #   register: all_vms
    
    # - name: Display The KVM VMs
    #   debug:
    #     msg:  "All KVM VMs : {{ all_vms['list_vms'] }}"

~~~

- The file auto-inst.seed is an example of a custom pressed file that you can use when you build your Ubuntu 18.04 image using Cubic.
- For more insformation visit : https://help.ubuntu.com/lts/installation-guide/amd64/apbs04.html

### 2. Run the playbook to create and install the Ubuntu Image automatically

##### To run the vm_manage.yaml just use ansible-playbook command as follow
~~~
ansible-playbook vm_manage.yaml
~~~

- Once you run this command, automatically a new KVM VM will be created in the target system then will pressed the auto installation of Ubuntu 18.04 OS.

### Author
Created by @Arboulahdour

<a href="mailto:ar.boulahdour@outlook.com">E-mail me !</a>
