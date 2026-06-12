# How to Generate an SSH Key on MacBook (And Actually Use It Like a Pro)

So you've got a MacBook and you need to generate an SSH key. Maybe you're setting up a new server, pushing code to GitHub, or finally decided to stop typing passwords every time you SSH into something. Whatever brought you here — you're in the right place.

This guide walks you through every step, from cracking open Terminal to connecting to a remote server without breaking a sweat. And since an SSH key without a server to connect to is a bit like a key with no door, we'll also cover how to pick a high-performance VPS that's worth the effort.

Let's get into it.

---

## What Exactly Is an SSH Key (And Why Should You Care)?

Think of SSH key authentication like a bouncer at an exclusive club — except instead of checking your ID, the server checks a cryptographic signature. You hold the private key (think: your secret identity card), and the server holds the public key (the bouncer's list). When you knock on the door, the two match up, and you're in — no password required.

Why bother with keys instead of passwords?

- **Passwords can be brute-forced.** Keys are mathematically too large to crack.
- **Keys are faster.** No typing, no remembering, no "forgot my password" moments.
- **They're required by most modern infrastructure.** GitHub, GitLab, AWS, DigitalOcean, DMIT — pretty much any serious service expects you to know how to use them.

The good news: MacBook users have it easy. macOS ships with OpenSSH built right in. No installation needed.

---

## Step-by-Step: How to Generate an SSH Key on MacBook

### Step 1 — Open Terminal

Press `Command + Space`, type **Terminal**, and hit Enter. Or go to **Finder → Applications → Utilities → Terminal**.

You'll get a plain command-line window. This is where all the good stuff happens.

---

### Step 2 — Run the Key Generation Command

Type this into Terminal and press Enter:

bash
ssh-keygen -t ed25519 -C "your_email@example.com"


Replace `your_email@example.com` with your actual email — this is just a label to help identify the key later. It doesn't affect how the key works.

**Why Ed25519?** It's the modern standard. Faster than RSA, more secure, and generates a shorter key that's easier to manage. If you're working with an older system that doesn't support Ed25519, use:

bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"


But for anything built after 2015, Ed25519 is the way to go.

---

### Step 3 — Choose Where to Save the Key

After running the command, Terminal will ask:


Enter file in which to save the key (/Users/yourname/.ssh/id_ed25519):


Just press **Enter** to accept the default location. Your keys will be saved in the hidden `~/.ssh/` folder:

- **Private key**: `~/.ssh/id_ed25519` — keep this secret, never share it
- **Public key**: `~/.ssh/id_ed25519.pub` — this is what you copy to servers

---

### Step 4 — Set a Passphrase (Recommended)

Terminal will ask:


Enter passphrase (empty for no passphrase):


You have two options:

1. **Press Enter twice** to skip — your key works without a passphrase, but if someone gets your Mac, they get your key too.
2. **Type a passphrase** — adds a layer of protection. You'll enter it once during login (or once per session if you add the key to ssh-agent).

For personal development servers, skipping the passphrase is usually fine. For production systems or anything sensitive, use one.

---

### Step 5 — Confirm the Key Was Created

Run this command to view your new public key:

bash
cat ~/.ssh/id_ed25519.pub


You'll see something like:


ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIHgm... your_email@example.com


That long string starting with `ssh-ed25519` is your public key. Copy it — you'll need it shortly.

---

## Add Your Key to the SSH Agent

The SSH agent stores your key in memory so you don't have to enter your passphrase every single time. On macOS, it also integrates with the system Keychain.

Start the agent and add your key:

bash
eval "$(ssh-agent -s)"
ssh-add --apple-use-keychain ~/.ssh/id_ed25519


If you're on an older macOS (before Monterey), use:

bash
ssh-add -K ~/.ssh/id_ed25519


To make this persistent across reboots, create or edit `~/.ssh/config`:

bash
nano ~/.ssh/config


Add this:


Host *
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/id_ed25519


Save with `Ctrl + O`, exit with `Ctrl + X`. Now your key loads automatically every time you start your Mac.

---

## Copy Your Public Key to a Remote Server

Once you have the key, you need to tell your server to trust it. There are two ways:

### Option A — Using ssh-copy-id (Easiest)

bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub username@server_ip


This automatically appends your public key to `~/.ssh/authorized_keys` on the server. Done.

### Option B — Manual Copy

If `ssh-copy-id` isn't available (some minimal Linux installs), log into the server with your password and run:

bash
mkdir -p ~/.ssh
echo "your_public_key_here" >> ~/.ssh/authorized_keys
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys


Paste the public key output from `cat ~/.ssh/id_ed25519.pub` in place of `your_public_key_here`.

---

## Test the Connection

Now try connecting:

bash
ssh username@server_ip


If everything worked, you'll get in without a password prompt. First time connecting, you'll see a message about the server's fingerprint — type `yes` to confirm and add it to your known hosts.

---

## Common Issues and Fixes

**Permission denied (publickey):**
Check that your key is in `~/.ssh/authorized_keys` on the server and that file permissions are correct (`chmod 600 ~/.ssh/authorized_keys`).

**Too many authentication failures:**
You might have too many keys loaded. Specify the key explicitly:
bash
ssh -i ~/.ssh/id_ed25519 username@server_ip


**Key not being used:**
Make sure your `~/.ssh/config` points to the right key file and the agent has it loaded (`ssh-add -l` shows loaded keys).

---

## Best Practices for SSH Key Management

- **Never share your private key.** If it's compromised, generate a new pair immediately.
- **Use different keys for different contexts.** One key for GitHub, one for production servers.
- **Rotate keys periodically.** Once a year is a reasonable cadence for personal projects; more often for team environments.
- **Disable password authentication on your server** once SSH keys are working. Edit `/etc/ssh/sshd_config` and set `PasswordAuthentication no`.
- **Back up your private key securely.** Store it in an encrypted vault (like 1Password or Bitwarden) — losing it means generating a new one and updating every server.

---

## You've Got the Key — Now You Need a Door Worth Opening

Here's the thing: generating an SSH key on your MacBook takes five minutes. The more interesting question is *what you're connecting to*.

If you're spinning up personal projects, hosting apps, running dev environments, or just want a reliable Linux box to tinker with, the server matters as much as the key. A slow or unreliable VPS will kill your workflow faster than any SSH configuration headache.

[DMIT](https://www.dmit.io/aff.php?aff=18446) has built a reputation for high-performance VPS infrastructure with serious network routing — particularly for users who need optimized connectivity between the US and Asia. Their CN2 GIA and CMIN2 routes are the premium tiers of trans-Pacific networking, meaning latency that actually behaves like latency should.

👉 [Explore DMIT VPS Plans](https://www.dmit.io/aff.php?aff=18446)

---

## DMIT VPS Plans Overview

DMIT offers VPS across four locations — **Los Angeles**, **San Jose**, **Hong Kong**, and **Tokyo** — with multiple product tiers per location. Here's a breakdown of their current lineup:

### Los Angeles — Premium Series (CN2 GIA Routing)

| Plan | RAM | CPU | SSD | Bandwidth | Price | Link |
|---|---|---|---|---|---|---|
| LAX.Pro.WEE | 1 GB | 1 vCPU | 20 GB | 500 GB/mo @ 500Mbps | $36.9/yr | 👉 [Order](https://www.dmit.io/aff.php?aff=18446) |
| LAX.Pro.MALIBU | 1 GB | 1 vCPU | 20 GB | 1 TB/mo @ 1Gbps | $49.9/yr | 👉 [Order](https://www.dmit.io/aff.php?aff=18446) |
| LAX.Pro.PalmSpring | 2 GB | 2 vCPU | 40 GB | 2 TB/mo @ 2Gbps | $100/yr | 👉 [Order](https://www.dmit.io/aff.php?aff=18446) |

### Los Angeles — Eyeball Series (CMIN2 Optimized Routing)

DMIT's Eyeball series targets users who want optimized routing to Chinese mainland networks at a lower price point than the full CN2 GIA Premium tier. Plans include TINY and STARTER configurations, with 2–10Gbps port speeds and the promo code `LAX-EB-LAUNCH-NON-MONTHLY-RECURRING-20OFF` for 20% off recurring (quarterly or annual billing).

👉 [View Eyeball Series](https://www.dmit.io/aff.php?aff=18446)

### Hong Kong — Tier 1 Series (International Routing)

| Plan | Starting Price | Routing | Link |
|---|---|---|---|
| HKG.T1 Starter | ~$3/mo | International Tier 1 | 👉 [Order](https://www.dmit.io/aff.php?aff=18446) |
| HKG.T1 Annual | Significant discount with code `HKG-T1-ANNUALLY-45OFF-RECUR` (45% off, recurring) | International Tier 1 | 👉 [Order](https://www.dmit.io/aff.php?aff=18446) |

### Hong Kong & Tokyo — Premium Series (CN2 GIA Routing)

For users needing premium CN2 GIA routing in the Asia-Pacific region. Eligible for `202510_HKG_TYO_PRO_20OFF_RECURRING` — 20% off recurring on quarterly or annual plans.

👉 [View HK & Tokyo Premium Plans](https://www.dmit.io/aff.php?aff=18446)

### San Jose — Unmetered Series

DMIT also offers San Jose-based plans with unmetered bandwidth options. A promo code for this tier is also available — check the current promotions page for the latest.

👉 [Check San Jose Plans](https://www.dmit.io/aff.php?aff=18446)

---

## Active Promo Codes (2026)

| Code | Discount | Applicable Plans |
|---|---|---|
| `LAX-EB-LAUNCH-NON-MONTHLY-RECURRING-20OFF` | 20% off recurring | LAX Eyeball (quarterly+) |
| `HKG-T1-ANNUALLY-45OFF-RECUR` | 45% off recurring + spec upgrade | HKG Tier 1 (annual) |
| `202510_HKG_TYO_PRO_20OFF_RECURRING` | 20% off recurring | HKG & Tokyo Premium (quarterly+) |
| `GIA-Q4-Free-LITE-MINI` | 70% off | LAX Lite MINI (semi-annual) |

DMIT also offers a **3-day money-back guarantee** (up to 30GB usage) and **30-day prorated refunds**, so you're not locked in if the server doesn't work out.

---

## Setting Up Your SSH Key on a DMIT VPS

Once you've spun up a DMIT VPS, the whole workflow comes together fast:

1. **Get your server IP** from the DMIT client area
2. **Copy your public key** to the server:
   bash
   ssh-copy-id -i ~/.ssh/id_ed25519.pub root@your_dmit_server_ip
   
3. **Connect via SSH**:
   bash
   ssh root@your_dmit_server_ip
   
4. **(Optional) Create a config alias** in `~/.ssh/config`:
   
   Host dmit-la
     HostName your_dmit_server_ip
     User root
     IdentityFile ~/.ssh/id_ed25519
   
   Now you can just run `ssh dmit-la` — clean and simple.

---

## Frequently Asked Questions

**Q: Can I have multiple SSH keys on my MacBook?**
Yes. Generate as many as you need with different filenames (e.g., `id_ed25519_github`, `id_ed25519_dmit`). Reference them explicitly in `~/.ssh/config` per host.

**Q: What's the difference between Ed25519 and RSA?**
Ed25519 uses elliptic curve cryptography — it generates shorter keys that are computationally faster and at least as secure as 4096-bit RSA. Use Ed25519 for new keys unless a legacy system requires RSA.

**Q: Do I need to regenerate my SSH key if I get a new Mac?**
No. Transfer your existing `~/.ssh/` folder to the new machine, or generate a new key pair and update authorized_keys on your servers with the new public key. The second approach is cleaner and more secure.

**Q: What if my SSH connection drops randomly?**
Add this to your `~/.ssh/config`:

Host *
  ServerAliveInterval 60
  ServerAliveCountMax 3

This sends a keep-alive packet every 60 seconds, preventing idle timeouts.

---

## Wrapping Up

Generating an SSH key on a MacBook is genuinely one of those five-minute tasks that pays dividends for years. The commands are simple, the setup is stable, and once it's done, you're never typing server passwords again.

The key (pun unavoidable) is pairing good key hygiene with a server that's actually worth connecting to. If you're serious about low-latency, high-performance hosting — especially with any US-to-Asia connectivity needs — DMIT's network routing puts them in a different league from commodity VPS providers.

👉 [Check out DMIT's current plans and promotions](https://www.dmit.io/aff.php?aff=18446)

Generate your key, pick your server, and stop typing passwords. You're already almost there.
