# Cybersecurity-NetworkSweep-Lab

## Lab Title

Network Device Sweep & Intrusion Detection Lab – BYOD Threat Mitigation

---

## Objective

The objective of this lab was to design and implement a secure Bash-based network sweep script capable of identifying active devices within a specified subnet. The script was required to log responsive IP addresses while restricting execution privileges exclusively to authorized members of a designated SECURITY group.

This lab simulates a real-world defensive cybersecurity response to unauthorized BYOD (Bring Your Own Device) activity within an enterprise network environment.

---

## Scenario

SecureNet Corp, a mid-sized enterprise, recently experienced a network intrusion caused by an unauthorized BYOD device connecting to the internal corporate network. Although the attack was contained, management required a detection mechanism that could quickly identify active devices within a subnet and allow comparison against the official asset inventory.

This lab recreates that defensive process by implementing a controlled subnet sweep tool combined with strict Linux group-based execution control.

---

## Tools Used

- Kali Linux (Operating System environment)
- Bash Scripting (Script development and automation)
- ping (ICMP-based host detection)
- groupadd (Security group creation)
- usermod (User-to-group assignment)
- chmod (File permission configuration)
- chown (File ownership assignment)
- Linux file permission model (rwx) for execution restriction
---

## Step-by-Step Process

### 1. Security Group Creation

A dedicated group named `security` was created to ensure that only authorized users could execute the network sweep script.

```bash
sudo groupadd security
```
Verification of group creation:
```
getent group security
```
### 2. User Creation

Three users were created to simulate members of a security operations team.
```
sudo adduser alice_sec
sudo adduser bob_sec
sudo adduser carol_sec
```
### 3. Group Assignment

Each user was added to the security group to enforce role-based access control.
```
sudo usermod -aG security alice_sec
sudo usermod -aG security bob_sec
sudo usermod -aG security carol_sec
```

Verification:
```
groups alice_sec
groups bob_sec
groups carol_sec
```
---
### 4. Script Development

The script testsweep.sh was created to accept a subnet argument (e.g., 192.168.64) and iterate through IP addresses from .1 to .254. Each address is pinged once, and responsive hosts are recorded in an output file named sweep_results.txt.
```python
#!/bin/bash

# Check if subnet argument is provided
if [ -z "$1" ]; then
    echo "You forgot the network IP"
    echo "Syntax: ./testsweep.sh 192.168.64"
    exit 1
fi

SUBNET=$1
OUTPUT="sweep_results.txt"

echo "Active IP addresses:" > $OUTPUT

# Ping sweep from .1 to .254
for ip in $(seq 1 254); do
    ping -c 1 -W 1 $SUBNET.$ip &> /dev/null
    if [ $? -eq 0 ]; then
        echo "$SUBNET.$ip" >> $OUTPUT
    fi
done

echo "Sweep complete. Results saved in $OUTPUT"
```
---
### 5. Secure Permission Assignment

To enforce execution restrictions, ownership of the script was assigned to root and the security group. File permissions were configured to 750, ensuring only the owner and group members could execute the script.
```
sudo chown root:security testsweep.sh
sudo chmod 750 testsweep.sh
```

Verification:
```
ls -l testsweep.sh
```

Expected permission structure:
```
-rwxr-x--- 1 root security testsweep.sh
```
This configuration enforces the Principle of Least Privilege by preventing unauthorized users from executing the script.

---

### 6. Script Execution and Output Verification

# The script was executed by a user within the security group:
```
./testsweep.sh 192.168.64
```

Example console output:
```
Active IP addresses:
192.168.64.1
192.168.64.2
192.168.64.128
```
Sweep complete. Results saved in sweep_results.txt

# To verify that results were properly logged:
```
cat sweep_results.txt
```
Example contents of sweep_results.txt:
```
Active IP addresses:
192.168.64.1
192.168.64.2
192.168.64.128
```

# Commands Executed

The following commands were used throughout the lab implementation.

Group Management
```
sudo groupadd security
getent group security
```
User Creation and Assignment
```
sudo adduser alice_sec
sudo adduser bob_sec
sudo adduser carol_sec
```
```
sudo usermod -aG security alice_sec
sudo usermod -aG security bob_sec
sudo usermod -aG security carol_sec
```
```
groups alice_sec
groups bob_sec
groups carol_sec
```
Permission Enforcement
```
sudo chown root:security testsweep.sh
sudo chmod 750 testsweep.sh
ls -l testsweep.sh
```
Script Execution
```
./testsweep.sh 192.168.64
cat sweep_results.txt
```

Note: Screenshots of these command outputs are stored in the screenshots/ directory of this repository.
