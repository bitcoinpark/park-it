## Deploying New VMs

When deploying a new VM from the template, you'll need to customize several parameters. Here's the base command structure with explanations:

```bash
# Clone template
qm clone 9999 470 --name ubuntu-2410-test -full -storage DATA

# Configure networking and resources
qm set 470 --ipconfig0 ip=10.21.0.7/16,gw=10.21.0.1
qm set 470 --nameserver 10.21.0.1 --searchdomain lab.bitcoinpark.com
qm set 470 --net0 model=virtio,bridge=vmbr21
# qm resize 470 virtio0 +8G
qm set 470 --core 4 --memory 16384 --balloon 0

# Start VM
qm start 470
```

### Required Customizations

Before running the commands, adjust these values:

1. **VM ID** (`470`): 
   - Choose an unused VM ID
   - Check existing IDs in Proxmox or ask someone
   - Replace `470` in all commands

2. **VM Name** (`ubuntu-2404-test`):
   - Choose a descriptive name for your VM
   - This name will appear in the Proxmox console
   - Example: `ubuntu-web1`

3. **IP Configuration** (`ip=10.21.0.7/16,gw=10.21.0.1`):
   - Use an available IP from the `10.21.0.1/16` network

4. **Storage** (`qm resize 470 virtio0 +8G`):
   - Base template has a 16GB disk
   - Add additional space with the `+` operator
   - Example: `+8G` results in 24GB total (16GB + 8GB)
   - Comment out this line if default size is sufficient

5. **Resources** (`--core 4 --memory 16384`):
   - Memory is specified in MB (16384 = 16GB)
   - Cores represent virtual CPU cores 
   - Adjust based on workload requirements

Full example with customizations. Copy/paste this into the Proxmox server shell to provision a new VM:
```bash
# Clone template
qm clone 9999 475 --name adam -full -storage DATA

# Configure networking and resources
qm set 475 --ipconfig0 ip=10.21.0.7/16,gw=10.21.0.1
qm set 475 --nameserver 10.21.0.1 --searchdomain lab.bitcoinpark.com
qm set 475 --net0 model=virtio,bridge=vmbr21
# qm resize 475 virtio0 +8G
qm set 475 --core 4 --memory 16384 --balloon 0

# Start VM
qm start 475
```
