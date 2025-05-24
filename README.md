# Nginx Load Balancer Deployment using Ansible
## Overview
This project sets up a high-availability web infrastructure using Nginx as a load balancer and Ansible for automation. It deploys one load balancer node and two web server nodes on AWS EC2, configured to serve static HTML pages and handle server errors gracefully.


### Infrastructure Layout
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
├── inventory         # Ansible inventory (loadbalancer & webservers)
├── project.yaml      # Unified Playbook for Load Balancer and Web Servers
└── node1.pem         # Private Key
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

### Load Balancer:

* Install Nginx
* Create fallback HTML page
* Configure Nginx as load balancer with upstream web servers
* Enable new config and remove default site
* Restart Nginx

### Key Configuration:
* Uses proxy_pass to send traffic to upstream backend.
* Includes fallback.html for 502/503/504 errors.



### Web Servers:

* Install and start Nginx
* Deploy a simple `index.html` with dynamic hostname info

---

## Security & Access

* SSH access to all instances is handled via private key `node1.pem`
* Ensure the key file has proper permissions (chmod 400)
* Use ubuntu user for all EC2 instances.

  ```bash
  chmod 400 /path/to/node1.pem
  ```
* Default user: `ubuntu`

---

## How to Run

Execute the entire setup with one command:

```bash
ansible-playbook -i inventory project.yaml
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
