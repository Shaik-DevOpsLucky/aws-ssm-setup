# Setting Up Amazon SSM Agent on Ubuntu 24.04 (Snap Installation)

This guide explains how to resolve the **document worker timed out** issue by properly installing and starting the Amazon SSM Agent on Ubuntu 24.04, which is required for AWS Systems Manager functionality.

## 1. Create an IAM role & attach below policies
- AmazonSSMFullAccess
- AmazonSSMManagedInstanceCore

## 2. Attach the Role to EC2-Instance.

---

## 🧾 Prerequisites

- **Ubuntu 24.04 LTS EC2 instance**
- Instance should have an **IAM role attached** with the following AWS managed policy:
  - `AmazonSSMManagedInstanceCore`
- Instance must have **internet access** (via public IP or NAT)

---

## ✅ Step 1: Verify the OS

```bash
cat /etc/os-release
```

Ensure you see:

```ini
PRETTY_NAME="Ubuntu 24.04.2 LTS"
```

---

## ✅ Step 2: Verify Snap Installation of SSM Agent

```bash
snap list amazon-ssm-agent
```

Expected output:

```text
Name              Version     Rev   Tracking        Publisher  Notes
amazon-ssm-agent  3.3.987.0   xxxx  latest/stable   aws✓        classic
```

---

## ✅ Step 3: Start and Enable the SSM Agent

```bash
sudo snap start amazon-ssm-agent
sudo snap enable amazon-ssm-agent
```

---

## ✅ Step 4: Confirm Agent is Running

```bash
sudo snap services amazon-ssm-agent
```

Expected output:

```text
Service                              Startup  Current
amazon-ssm-agent.amazon-ssm-agent    enabled  active
```

---

## ✅ Step 5: Verify in AWS Console

Go to:

- AWS Console → Systems Manager → Managed Nodes
- Find your instance by instance ID or name
- Status should show **“Managed”** and **“Online”**

---

## ✅ Step 6: (Optional) Test with a Run Command

Go to:

- AWS Systems Manager → Run Command
- Choose `AWS-RunShellScript`
- Target your instance
- Command to run:

```bash
echo "Hello from SSM"
```

---

## ✅ Step 7: Logs (for Troubleshooting)

```bash
sudo journalctl -u snap.amazon-ssm-agent.amazon-ssm-agent.service
```
