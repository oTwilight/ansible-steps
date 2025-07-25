1. Generate SSH Key
Create a secure SSH key pair that will be used to access your EC2 instance:

ssh-keygen -t rsa -b 4096 -f ~/.ssh/ssh-key-servers.pem

Copy the public key content (.pem.pub) and paste it into your terraform-ec2/main.tf under the EC2 key pair resource.

2. Provision EC2 with Terraform
Navigate to the terraform-ec2/ directory:

cd terraform-ec2
terraform init
terraform apply

 After apply, Terraform will output the public IP address of the created EC2 instance.

Want more EC2s?
Edit the count parameter in variables.tf:

3. Configure Inventory
Copy the EC2 public IP from Terraform output and paste it into inventory.ini:

[ubuntu_servers]
ubuntu_server_01 ansible_user=ubuntu ansible_host=<your.server.IP> ansible_python_interpreter=/usr/bin/python3.10

ansible_host # Replace with your instance's IP

[defaults]
inventory = ./hosts.ini #change the name of inventory how you want if it needed
private_key_file = ~/.ssh/ssh-key-servers.pem

4. Test Connection
Check Ansible connectivity with ad-hoc ping:


ansible all -i inventory.ini -m ping
Expected output:

1.2.3.4 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
5. Run the Playbook
Run Ansible to configure the instance:

ansible-playbook main.yml

What Does the Playbook Do?
When you run ansible-playbook main.yml, it performs:

Installs Nginx on the remote EC2.

Replaces the default Nginx config using your custom templates/nginx.conf.

Creates a file ~/example containing CPU info of the server.

Prints a message (e.g., "Welcome to server1") on the command line using host-specific variables (host_vars).

Customization
Need different ports or messages?
Edit values in vars/host_vars/*.yml.

Need to change Nginx settings?
Modify templates/nginx.conf.
