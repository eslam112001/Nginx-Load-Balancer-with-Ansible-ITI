
```markdown
# Nginx Load Balancer Deployment using Ansible

## Overview
This project sets up a high-availability web infrastructure using **Nginx** as a load balancer and **Ansible** for automation.  
It uses a **single Ansible playbook (`project.yaml`)** to configure one load balancer node and two web server nodes on **AWS EC2**.  
All servers serve static HTML pages and gracefully handle server errors.

---

## Infrastructure Layout

```

```
        ┌────────────┐
        │  Client    │
        └─────┬──────┘
              │
      ┌───────▼────────┐
      │  Load Balancer │  ← Nginx (54.164.54.34)
      └───────┬────────┘
              │
 ┌────────────┴────────────┐
 │                         │
```

┌────▼────┐             ┌──────▼─────┐
│ Web Server 1 │         │ Web Server 2 │
│ 54.227.86.175│         │ 3.80.218.45  │
└──────────────┘         └─────────────┘

```

---

## Project Structure

```

project/
│
├── inventory.ini     # Ansible inventory (loadbalancer & webservers)
├── project.yaml      # Unified Playbook for Load Balancer and Web Servers
└── README.md         # Project Documentation

````

---

## Inventory File (`inventory.ini`)

```ini
[loadbalancer]
lb ansible_host=54.164.54.34 ansible_user=ubuntu ansible_ssh_private_key_file=/home/control-node/ansible-file/node1.pem

[webservers]
web1 ansible_host=54.227.86.175 ansible_user=ubuntu ansible_ssh_private_key_file=/home/control-node/ansible-file/node1.pem
web2 ansible_host=3.80.218.45 ansible_user=ubuntu ansible_ssh_private_key_file=/home/control-node/ansible-file/node1.pem
````

---

## Ansible Playbook (`project.yaml`)

### Summary of Tasks

#### Load Balancer:

* Install Nginx
* Create fallback HTML page
* Configure Nginx as a load balancer with two backend servers
* Enable the new configuration and disable the default site
* Restart Nginx

#### Web Servers:

* Install and start Nginx
* Deploy a simple `index.html` with dynamic hostname info

---

## Security & Access

* All SSH access managed using the private key `node1.pem`
* Make sure permissions are set correctly:

  ```bash
  chmod 400 /path/to/node1.pem
  ```
* Default user: `ubuntu`

---

## How to Run

Execute the entire setup with one command:

```bash
ansible-playbook -i inventory.ini project.yaml
```

---

## Testing

* Visit:
  `http://54.164.54.34`
* Refresh to see output from different backend servers
* Stop one backend manually and check fallback page is served

---

## Dependencies

* Ansible
* AWS EC2 (Ubuntu 20.04 or newer)
* SSH Key (PEM file)

---

## Future Improvements

* Convert playbook into Ansible roles for better modularity
* Add Nginx health checks
* Enable HTTPS with Let's Encrypt
