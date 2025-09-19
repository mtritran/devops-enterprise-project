# Teleport Server Management

## Overview

This guide shows how to automatically add new servers to Teleport for centralized management and secure access.

## 1. Generate Long-term Token

SSH into the Teleport server and create a long-term token:

```bash
tctl tokens add --type=node --ttl=720h
```

This generates a fixed token valid for 30 days (720 hours), solving the token management problem for automation.

## 2. Add Servers to Teleport

### Manual Method (for testing)

On any server you want to add to Teleport management:

```bash
sudo bash -c "$(curl -fsSL https://teleport.mtritran.click/scripts/<your-token>/install-node.sh)"
```

Replace `<your-token>` with the token generated in step 1.

### Automated Method (recommended for production)

For production environments with hundreds or thousands of servers, use automation tools like Ansible, Puppet, or Chef to deploy this command across your infrastructure.

The automation workflow would be:
1. Generate the long-term token once
2. Replace the token in your automation scripts
3. Deploy the installation command to target servers using your automation tool

## 3. Example: Add Load Balancer to Teleport

Add the Load Balancer server to Teleport management:

SSH into the Load Balancer and run:
```bash
sudo bash -c "$(curl -fsSL https://teleport.mtritran.click/scripts/<your-token>/install-node.sh)"
```

## 4. Verify Server Addition

1. Go to the Teleport Web UI at https://teleport.mtritran.click
2. Navigate to the "Resources" section
3. You should see the newly added server(s) listed

## 5. Benefits of Centralized Server Management

- **Secure Access**: All SSH access goes through Teleport's secure gateway
- **Audit Trail**: Complete logging of all server access and commands
- **Role-based Access**: Control who can access which servers
- **Session Recording**: Record and replay SSH sessions for compliance
- **No Direct SSH**: Eliminate the need for direct SSH keys on servers

## Notes

- The token expires after 720 hours (30 days), after which you'll need to generate a new one
- For production deployments, integrate this process with your existing infrastructure automation
- Consider using infrastructure as code tools to automatically add new servers as they're provisioned