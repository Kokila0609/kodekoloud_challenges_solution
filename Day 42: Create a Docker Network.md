# Docker Network Setup

## Requirement
The Nautilus DevOps team needs to set up several docker environments for different applications. One of the team members has been assigned a ticket where he has been asked to create some docker networks to be used later. Complete the task based on the following ticket description:

*   **Network name**: `beta` on App Server 2 in Stratos DC.
*   **Driver**: `bridge`
*   **Subnet**: `172.28.0.0/24`
*   **IP range**: `172.28.0.0/24`

## Steps

### 1. SSH into App Server 2
```bash
thor@jump-host ~$ ssh steve@stapp02
```

**Output:**
```text
The authenticity of host 'stapp02 (10.244.234.223)' can't be established.
ED25519 key fingerprint is SHA256:4/e4Nm4jwi8QcN8b8MJWfPfi1SG8pBZ0E503jpKBj90.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'stapp02' (ED25519) to the list of known hosts.
steve@stapp02's password: 
```

### 2. Create the Custom Bridge Network
```bash
[steve@stapp02 ~]$ docker network create --driver bridge --subnet=172.28.0.0/24 --ip-range=172.28.0.0/24 beta
```

**Output:**
```text
97ced8c74f2e137f3ea2ed2f1bc046634b037a7c2960c4cec5e2d5f19b3b286a
```

### 3. Verify the Network Creation
```bash
[steve@stapp02 ~]$ docker network ls
```

**Output:**
```text
NETWORK ID     NAME      DRIVER    SCOPE
97ced8c74f2e   beta      bridge    local
c696e63def8b   bridge    bridge    local
51c76c88a5f0   host      host      local
71d1a48dfd06   none      null      local
```
