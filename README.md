# My Website

### Requirements
- Docker
- Terraform

## Terraform 
Infrastructure configuration management.

### Deploy the infrastructure
```powershell
Set-Location terraform
terraform init
terraform plan -out mysite.plan
terraform apply mysite.plan
```

The IP address will output to the console for use with Ansible.  Wait a few minutes for cloud-init to finish before continuing.
```bash
sudo tail -f /var/log/cloud-init-output.log
```

## Ansible
Host configuration management.

### Building the Ansible Docker image
```powershell
Set-Location ansible
docker build -t ansible:0 ansible/
```

### Copying the required files
```powershell
# Use the private key that was configured for the "Ansible" user account during Terraform. (terraform/cloud-config/docker-host.yml)
Copy-Item -Path $HOME\.ssh\id_ed25519 -Destination .\ansible\keys\id_ed25519                                    
```

### Modify the inventory as required
```powershell
Copy-Item -Path .\hosts\hosts.reference.yml -Destination .\hosts\hosts.yml
```

### Copy Cloudflare Origin CA certificates
Cloudflare:  SSL/TLS > Origin Server (Key format: PEM)

Copy to `ansible/keys/origin.pem`, `ansible/keys/origin.key`

### Run the Ansible container
```powershell
docker run -it --rm `
    -v .\keys:/home/ansible/.ssh `
    -v .\playbooks:/ansible/playbooks `
    -v .\vars:/ansible/vars `
    -v .\certs:/ansible/certs `
    -v .\hosts\hosts.yml:/etc/ansible/hosts `
    ansible:0 /bin/bash
```

### Run the configuration scripts
```powershell
ansible-playbook provision.yml
```

## Web Application
Linkstack application with Nginx reverse proxy.

### Start the web application
```powershell
docker compose -f app/docker-compose.yml up -d --force-recreate
```
