## Creating a new Proxmox Template

Templates are used for provisioning new VMs in Proxmox. These steps walk through creating a new template that also utilizes cloudinit to add configuration to new VMs created from the template.

### 1. Download and Prepare Image

First, visit [Ubuntu Cloud Images](https://cloud-images.ubuntu.com/) and navigate to your desired Ubuntu version. Look for the `amd64.img` file in the `current` directory. For example:
- Latest LTS: Navigate to `/noble/current/noble-server-cloudimg-amd64.img` for 24.04
- Latest Release: Navigate to `/oracular/current/oracular-server-cloudimg-amd64.img` for 24.10

```bash
# Download Ubuntu cloud image (replace URL with your chosen version)
wget -q https://cloud-images.ubuntu.com/oracular/current/oracular-server-cloudimg-amd64.img

# Resize image (adjust size as needed)
qemu-img resize oracular-server-cloudimg-amd64.img 32G
```

### 2. Set Environment Variables
```bash
export CUSTOM_USER_NAME=bplabroot
export CUSTOM_USER_PASSWORD=password
```

### 3. Create Base VM
```bash
qm create 9999 --name "ubuntu-2410-cloudinit-template" --ostype l26 \
    --memory 1024 \
    --agent 1 \
    --bios ovmf --machine q35 --efidisk0 DATA:0,pre-enrolled-keys=0 \
    --cpu host --socket 1 --cores 1 \
    --vga serial0 --serial0 socket \
    --net0 virtio,bridge=vmbr21
```

### 4. Import and Configure Disk
```bash
# Import disk (make sure to use the correct image filename)
qm importdisk 9999 oracular-server-cloudimg-amd64.img DATA

# Configure disk settings
qm set 9999 --scsihw virtio-scsi-pci --virtio0 DATA:vm-9999-disk-1,discard=on
qm set 9999 --boot order=virtio0
qm set 9999 --ide2 DATA:cloudinit
```

### 5. Configure Cloud-Init

First, create vendor.yaml:
```bash
cat << EOF | tee /var/lib/vz/snippets/vendor.yaml
    #cloud-config
    write_files:
    - path: /etc/ssh/sshd_config.d/10-bplabroot.conf
        content: |
        Match User bplabroot
            PasswordAuthentication yes
        permissions: '0644'

    runcmd:
        - systemctl restart ssh
        - apt update
        - apt install -y qemu-guest-agent
        - systemctl start qemu-guest-agent
        - reboot
EOF
```

Enable snippets storage:
1. Navigate to Datacenter → Storage → local
2. Add "snippets" to contents list

Configure cloud-init settings:
```bash
qm set 9999 --cicustom "vendor=local:snippets/vendor.yaml"
qm set 9999 --tags ubuntu-template,24.10,cloudinit
qm set 9999 --ciuser $CUSTOM_USER_NAME --ciupgrade 1
qm set 9999 --cipassword $(openssl passwd -6 $CUSTOM_USER_PASSWORD)
qm set 9999 --ipconfig0 ip=dhcp

# Update cloud-init
qm cloudinit update 9999

# Convert to template
qm template 9999
```
