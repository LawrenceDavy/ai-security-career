1. We are going to replace my Github password with an Ed25519 SSH Key.

Ed25519 because it is faster and more secure than the old RSA keys.
ssh-keygen -t ed25519 -C "emailaddress@email.com"

We'll will run a background program to hold our key so we don't have to type the password on every push.
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519

Once we have the local key, we need to give the public half of the key to Github so it can recognise us.
cat ~/.ssh/id_ed25519.pub
The entire output starting with ssh-ed25519 is then added to SSH and GPG keys section in Github settings.

Switch from HTTPS to SSH.
ssh -T git@github.com

Update repo to use SSH.
git remote set-url origin git@github.com:USERNAME/ai-security-career.git

Verify.
git remote -v


2. Configure a Firewall to make my machine invisible to casual scans. Deny everything coming in, allow everything going out using UFW (Uncomplicated Firewall).

Check status.
sudo ufw status
Should say inactive

Set new rules.
Block all incoming traffic by default
sudo ufw default deny incoming

Allow all outgoing traffic (so we can update/browse).
sudo ufw default allow outgoing

Allow SSH.
sudo ufw allow ssh

Turn it on:
sudo ufw enable

Check.
sudo ufw status verbose

Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), deny (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere                  
22/tcp (v6)                ALLOW IN    Anywhere (v6)

Deny (Incoming) blocks all unsolicited connection attempts from external systems to your machine.
Allow (Outgoing) permits your machine to initiate connections to external services (e.g., browsing the web or running system updates).
