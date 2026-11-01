# Learning Log

## Phase 1 - Infrastructure

### Completed
- Created three temporarily (from Free Tier account) AWS EC2 instances.
- Configured hostnames and /etc/hosts.
- Verified private network connectivity.
- Configured SSH key authentication.
- Configured AWS Security Groups.
- Enabled UFW with SSH access.
- Installed Docker Engine and Docker Compose on monitor-01.
- Tested Docker with hello-world.
- Connected monitor-01 to a private GitHub repository.

### Problems Solved
- Docker Compose package was unavailable in Ubuntu APT.
  Solution: Added the official Docker repository.

- SSH between EC2 instances timed out after changing
  security group rules.
  Solution: Added the monitoring server's Security Group
  as an inbound SSH source on the apps servers.

### Notes
- Current EC2 instances are temporary.
- Production migration will use the same server roles.
