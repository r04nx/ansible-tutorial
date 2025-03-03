# Ansible Tutorial Repository

## Overview

This repository contains a practical tutorial on IT Automation with Ansible. It demonstrates how to use Ansible to configure and manage multiple types of servers including web servers, FTP servers, and security infrastructure.

## Repository Structure

```
ansible-tutorial/
├── docker-compose.yml            # Docker environment configuration
├── inventory                      # Ansible inventory file
├── playbooks/                     # Collection of Ansible playbooks
│   ├── check_internet.yml         # Playbook to verify internet connectivity
│   ├── group_vars/                # Group variables
│   │   └── ftpservers/            # Variables specific to FTP servers
│   │       └── ftp_credentials.yml # FTP user credentials and configuration
│   ├── install_ftp_server.yml     # Playbook to set up FTP servers
│   ├── nginx-test-page.yml        # Playbook to deploy Nginx with a test page
│   ├── security-fixed3.yml        # Playbook for security implementations
│   ├── templates/                 # Jinja2 templates
│   │   └── vsftpd.conf.j2         # Template for FTP server configuration
│   └── undo_ftp.yml               # Playbook to remove FTP server installations
└── roles/                         # Ansible roles
    └── nginx/                     # Nginx web server role
        ├── files/                 # Static files
        │   └── index.html         # Default web page
        ├── handlers/              # Handlers for services
        │   └── main.yml           # Restart handlers for Nginx
        ├── tasks/                 # Tasks to be executed
        │   └── main.yml           # Main tasks for Nginx installation
        ├── templates/             # Templates for configuration
        │   └── nginx.conf.j2      # Nginx configuration template
        └── vars/                  # Variables specific to the role
            └── main.yml           # Nginx variables
```

## Environment Setup

The project uses Docker Compose to create a containerized environment with multiple services:

### Servers

- **Webserver**: Container for hosting web applications with Nginx
- **FTP** and **FTP2**: Containers for FTP server implementation
- **Monitoring**: Container for monitoring services
- **NetworkSecurity**: Container for security tools and configurations

All containers are based on Ubuntu and are configured with SSH access for Ansible to manage them.

## Ansible Components

### Inventory

The inventory file organizes servers into logical groups:

- **webservers**: Web server instances
- **ftpservers**: FTP server instances
- **monitoring**: Monitoring server instances
- **securitytools**: Security infrastructure instances

### Roles

#### Nginx Role

The Nginx role automates the deployment and configuration of web servers:

- **Tasks**: Install Nginx, create website directory, deploy content, configure server
- **Handlers**: Manage service restarts when configuration changes
- **Templates**: Configure server settings through templating
- **Variables**: Define customizable parameters for the web server

### Playbooks

#### Nginx Test Page (nginx-test-page.yml)

Deploys Nginx web server with a test page:
- Installs and configures Nginx through the Nginx role
- Sets up a test page to verify the web server is functioning correctly

#### FTP Server Installation (install_ftp_server.yml)

Automates the setup of vsftpd FTP servers:
- Installs the vsftpd package
- Creates FTP user accounts based on defined variables
- Sets up user directories with appropriate permissions
- Configures the server using a template
- Ensures the service is running and enabled

#### Security Implementation (security-fixed3.yml)

Implements security measures across servers:
- Installs security packages (fail2ban, ufw)
- Configures firewall rules
- Sets up intrusion detection and prevention
- Implements recommended security practices

#### Internet Connectivity Check (check_internet.yml)

Verifies internet connectivity on managed hosts:
- Tests connectivity to external resources
- Reports success or failure

#### FTP Server Removal (undo_ftp.yml)

Removes FTP server installations:
- Stops and disables the vsftpd service
- Removes the package
- Cleans up user accounts and directories

### Variables

#### FTP Credentials (group_vars/ftpservers/ftp_credentials.yml)

Defines FTP user accounts and server configuration:
```yaml
ftp_users:
  - username: ftpuser
    password: "{{ 'SecureFtpPass123' | password_hash('sha512') }}"
    local_root: /var/ftp/ftpuser
    chroot: yes
  - username: readonly
    password: "{{ 'ReadOnlyPass456' | password_hash('sha512') }}"
    local_root: /var/ftp/readonly
    chroot: yes
    write_access: no

ftp_banner: "Welcome to DevOps FTP Server"
anonymous_enable: no
local_enable: yes
write_enable: yes
chroot_local_user: yes
```

### Templates

#### vsftpd Configuration (templates/vsftpd.conf.j2)

Jinja2 template for configuring the FTP server with proper security settings and user access controls.

## Usage Instructions

### Prerequisites

- Docker and Docker Compose installed
- Ansible installed (version 2.9+)
- Basic understanding of YAML syntax

### Setting Up the Environment

1. Clone this repository:
```
git clone <repository-url>
cd ansible-tutorial
```

2. Start the Docker environment:
```
docker-compose up -d
```

3. Verify containers are running:
```
docker ps
```

### Running Ansible Playbooks

#### Deploy Web Server
```
ansible-playbook -i inventory playbooks/nginx-test-page.yml
```

#### Install FTP Servers
```
ansible-playbook -i inventory playbooks/install_ftp_server.yml
```

#### Implement Security Measures
```
ansible-playbook -i inventory playbooks/security-fixed3.yml
```

#### Check Internet Connectivity
```
ansible-playbook -i inventory playbooks/check_internet.yml
```

#### Remove FTP Servers
```
ansible-playbook -i inventory playbooks/undo_ftp.yml
```

## Learning Exercises

1. **Modify the Nginx configuration**:
   - Edit the `roles/nginx/templates/nginx.conf.j2` file
   - Run the Nginx playbook to apply changes
   - Verify changes take effect

2. **Add a new FTP user**:
   - Edit the `playbooks/group_vars/ftpservers/ftp_credentials.yml` file
   - Add a new user entry
   - Run the FTP server playbook
   - Test access with the new user

3. **Create a new role**:
   - Develop a role for another service (e.g., MySQL, PostgreSQL)
   - Create associated playbooks
   - Test deployment

## Troubleshooting

### Common Issues

1. **Connection Failures**:
   - Verify Docker containers are running
   - Check SSH configuration in containers
   - Ensure inventory file has correct hostnames/IPs

2. **Playbook Failures**:
   - Run playbooks with increased verbosity (`-vvv`) for detailed debugging
   - Check for syntax errors in YAML files
   - Verify that templates contain valid syntax

## Best Practices Demonstrated

1. **Role-Based Organization**: Separates functionality into reusable roles
2. **Templating**: Uses Jinja2 templates for configuration management
3. **Variable Management**: Organizes variables by group
4. **Secure Credential Handling**: Demonstrates password hashing
5. **Idempotent Operations**: Playbooks can be run repeatedly without issues

## Conclusion

This tutorial demonstrates how Ansible can simplify and automate IT infrastructure management. By exploring the playbooks and roles in this repository, you'll gain practical experience with configuration management, server deployment, and security implementation using Ansible.
