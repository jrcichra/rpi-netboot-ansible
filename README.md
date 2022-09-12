# rpi-netboot-ansible [![CICD](https://github.com/jrcichra/rpi-netboot-ansible/actions/workflows/cicd.yml/badge.svg)](https://github.com/jrcichra/rpi-netboot-ansible/actions/workflows/cicd.yml)

Automate your Pi experience

Inspired by Wendell: https://forum.level1techs.com/t/the-ultimate-home-server-herd-of-netboot-raspberry-pi-sure/181022

This set of playbooks configures your NAS to boot Pis from a network. It makes it simple to add new Pis from official images.

This is an early project that is still in development. Do not use this with production workloads. I have qualified the playbooks and they are able to boot a Pi from the network in their current state. I need to put protections in place that prevent the boot and root filesystems from being overwritten if they already exist. You may want to limit the concurrent systems in `inventory.yml` to one, and comment out ones you've created.

# Dependencies

- DHCP server with Option 66 set to the IP of the NFS/TFTP server (for now this playbook assumes they are the same system)
- The static DHCP IP addresses of the Pis you wish to boot
- [Ansible](https://www.ansible.com/) on the system running the playbook (for now the playbook assumes you are running Ansible on the NFS/TFTP server)
- A pre-configured NFS server (TODO: make a playbook for this). I'm using ZFS's `sharenfs` feature.
- Docker (I've configured the TFTP server to run in a Docker container to avoid changing the host's `dnsmasq` configuration)
- `python3-docker`. Ansible uses this to run the Docker container.

# Usage

- Complete `inventory.yml` with paths as seen from your server and from nfs.
- Complete `inventory.yml` with an array of Pi hostnames, IP addresses, and images (see `group_vars/all` for a list of choices or add your own)
- Download the images required with `ansible-playbook -i inventory.yml download.yml`.
- Build the filesystems for the Pis with `ansible-playbook -i inventory.yml build.yml`.
- Configure the Pi's specific settings (i.e hostname) with `ansible-playbook -i inventory.yml configure.yml`.
- Start the TFTP server in a Docker container with `ansible-playbook -i inventory.yml tftp.yml`.
- Boot the Raspberry Pi (ideally with a monitor attached) and make sure it fully boots

# Troubleshooting

- Connect a display to the Raspberry Pi and make sure it:
  - Gets an IP address
  - Finds the TFTP server
  - Is able to fetch files from the TFTP server
  - Loads the initramfs
  - Able to mount the NFS filesystem
  - Comes up with either a desktop or a login prompt
  - /boot is mounted properly
- Check the Ansible Playbook errors
- Check the TFTP server logs with `docker logs <container-id>`

# TODO

- Combine playbooks into a single top-level playbook to make it easier to run
- ZFS clone support for boot and root filesystems
- Validate playbook does not overwrite filesystems
