---
date: "2020-12-27"
title: "Use KVM and Terraform to create a VM cluster for your home lab"
tags: ["kvm", "linux", "networking", "k8s"]
draft: false
---

# Background

I have a spare desktop computer that was not getting a lot of use, so I decided to repurpose it as the primary server for running a small Kubernetes cluster in my home lab.

The output of this blog post will be a cluster of 3 KVM virtual machines (VM) networked via a virtual "bridge" that allows the VMs to communicate with each other and be reachable from any machine on your local network.

Although I will be using my home lab for learning Kubernetes (via [K3s](https://k3s.io/)), this guide is suitable for many uses, including home labs and local development.

I use [k3sup](https://github.com/alexellis/k3sup) to setup the K3s cluster, which is similar to `kubeadm` and makes bootstrapping K3s clusters a breeze.

My setup is _very_ minimal and approximates most home computer and network setups:

- residential-grade wifi router and cable modem
- DHCP for IP address assignments
- Desktop computer specs
  - 16 GB memory
  - 4 core, 4GHz AMD Ryzen 3200G processor
  - 500 GB SSD

# Code

See https://github.com/ebcrowder/kvm-terraform for Terraform code that we will use to spin up the VM infrastucture.

# Tools

Here is what we will use to create the infrastructure:

- [KVM](https://www.linux-kvm.org/page/Main_Page) for Linux VMs.
- [Terraform](https://www.terraform.io/) for automating the creation of VMs, the related virtual network and other resources.
- [terraform-provider-libvirtd](https://github.com/dmacvicar/terraform-provider-libvirt) this plugin provides Terraform with the necessary functionality to create KVM infrastructure.
- [CentOS](https://www.centos.org/) will be used for each virtual machine.
- [Fedora Server](https://getfedora.org/en/server/) will be used on the home lab server. Fedora Server ships with a web application [Cockpit](https://cockpit-project.org/) which makes server administration very easy.

All of the following instructions assume a RPM-based Linux distribution (ie Fedora Server), but these tools are available for many distributions, so you should be able to find the comparable tool for your package manager.

## Installation

Unless otherwise mentioned, the following instructions should be completed on the host server via SSH or direct access:

### Terraform and terraform-provider-libvirtd plugin

Terraform maintains a custom respository of Terraform binaries for RPM-based distributions, as seen here: https://www.terraform.io/docs/cli/install/yum.html. Follow the instructions there and install the latest Terraform binary.

Next, follow the instructions at https://github.com/dmacvicar/terraform-provider-libvirt to install the `terraform-provider-libvirtd` plugin. As of this writing, installation involves downloading the library from the GitHub releases page and placing the binary in the `~/.local/share/terraform/plugins/registry.terraform.io/dmacvicar/libvirt/0.6.3/linux_amd64` directory, substituting the `0.6.3` bit with the version of the release you are installing.

### KVM tools

```bash
sudo dnf install bridge-utils libvirt virt-install qemu-kvm qemu-guest-agent virt-top libguestfs-tools
```

Start and enable the `libvirtd` (KVM) daemon:

```bash
sudo systemctl start libvirtd
```

```bash
sudo systemctl enable libvirtd
```

## Bridge network setup

By default, KVM VMs are created with a bridge network called `vibr0`. This networks the VMs together, which allows the VMs to communicate with each other.

However, the `vibr0` network is not connected to the ethernet connection utilized by the VM host server. So, we need to create a separate bridge network to provide ethernet access from the host server to the guest VMs. Do **NOT** delete the `vibr0` network because it is integral to how KVM works.

**Note** - bridge networks only function with ethernet connections. There may be a way to do this via wifi connections, but I have not found a successful path to do so.

To show all connections:

```bash
sudo nmcli c
```

From the output, find your ethernet connection and note the entries under the `DEVICE` and `UUID` columns. This will be the device name we will use to provide internet access to the bridge network.

Now, we will need to delete the existing connection via its UUID. We will replace it with the bridge network:

```bash
sudo nmcli c delete UUID
```

Create the bridge network:

```bash
sudo nmcli c add type bridge autoconnect yes con-name bridge0 ifname bridge0
```

Configure the `bridge0` network to use DHCP:

```bash
sudo nmcli c modify bridge0 ipv4.method auto ipv6.method auto
```

Attach the ethernet interface (substituting the DEVICE entry for your machine) from the first step:

```bash
sudo nmcli c add type ethernet autoconnect yes con-name DEVICE ifname DEVICE master bridge0
```

Activate the bridge network:

```bash
sudo nmcli c up bridge0
```

Verify that the `bridge0` network has been assigned an IP address and that the original ethernet device does not have an IP address:

```bash
sudo ip addr
```

After performing the above, you should have a functioning bridge network.

## Create the VMs

On the host server, clone the GitHub repo referenced at the top of the post and do the following in the local repo directory:

Create an SSH key to use for VM access:

```bash
ssh-keygen -t rsa -b 4096 -f id_rsa -C centos -N "" -q
```

Initialize the Terraform project:

```bash
sudo terraform init
```

Then plan it:

```bash
sudo terraform plan
```

Then apply it:

```bash
sudo terraform apply
```

If the resources are successfully created, Terraform will output the assigned IP addresses of the VMs.

Verify that you are able to SSH into each VM:

```bash
ssh -i id_rsa centos@192.168.xxx.xxx
```

Lastly, test that you are able to reach the VMs by pinging them from another device (other than the host server) on your network:

```bash
ping 192.168.xxx.xxx
```

## Wrap up

That is it! You now have a functioning set of VMs that can communicate across devices on your local network.
