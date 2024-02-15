# SSH Auto Login

1. Generate an SSH key pair on your local machine if you haven't already:

```bash
ssh-keygen -t rsa -b 4096
```

2. Copy the public key to your remote server:

```bash
ssh-copy-id user@remote_host
```

Replace `user` with your username and `remote_host` with the server's address. 3. Once the key is copied, you should be able to SSH into the server without a password:

```bash
ssh user@remote_host
```

Make sure your remote server allows SSH key authentication. If it doesn't work, check the server's SSH config file (`/etc/ssh/sshd_config`) and ensure that `PubkeyAuthentication` is set to `yes`.

- # Raspberry Pi Screen Control via Home Assistant README

This guide details the process of setting up a Raspberry Pi to allow Home Assistant to automatically execute scripts to wake and sleep the screen via SSH.

## Detailed Steps

### 1. Creating Scripts on Raspberry Pi

First, create two scripts on your Raspberry Pi: one to wake the screen and one to put it to sleep.

**On Raspberry Pi:**

- Open a terminal and create a directory for scripts if it doesn't exist:

```bash
mkdir -p /home/pi/scripts
```

- Create `wake_screen.sh`:

```bash
echo -e '#!/bin/bash\nexport DISPLAY=:0\nxset dpms force on' > /home/pi/scripts/wake_screen.sh
chmod +x /home/pi/scripts/wake_screen.sh
```

- Create `sleep_screen.sh`:

```bash
echo -e '#!/bin/bash\nexport DISPLAY=:0\nxset dpms force off' > /home/pi/scripts/sleep_screen.sh
chmod +x /home/pi/scripts/sleep_screen.sh
```

### 2. Generating SSH Key Pair on Home Assistant Server

Generate an SSH key pair on the server where Home Assistant is running.

**On Home Assistant Server:**

- Generate a new SSH key pair (replace `email@example.com` with your actual email or identifier):

```bash
ssh-keygen -t rsa -b 4096 -C "email@example.com"
```

- When prompted for a file location, you can specify `/config/ssh/id_rsa` or use the default location.

### 3. Copying Public Key to Raspberry Pi

Copy the public SSH key to your Raspberry Pi to enable passwordless SSH login.

**On Home Assistant Server:**

- Copy the public key using `ssh-copy-id`:

```bash
ssh-copy-id -i /config/ssh/id_rsa.pub pi@192.168.4.23
```

Replace `pi@192.168.4.23` with the username and IP address of your Raspberry Pi.

- If `ssh-copy-id` is not available, manually copy the key:

```bash
cat /config/ssh/id_rsa.pub | ssh pi@192.168.4.23 'mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys'
```

### 4. Configuring Home Assistant

Configure Home Assistant to use the generated private key for executing the wake and sleep screen commands remotely.

**On Home Assistant Server:**

- Edit your `configuration.yaml` file to include the `shell_command` configuration:

```yaml
shell_command:
  wake_screen: "/usr/bin/ssh -i /config/ssh/id_rsa -o StrictHostKeyChecking=no pi@192.168.4.23 'bash /home/pi/scripts/wake_screen.sh'"
  sleep_screen: "/usr/bin/ssh -i /config/ssh/id_rsa -o StrictHostKeyChecking=no pi@192.168.4.23 'bash /home/pi/scripts/sleep_screen.sh'"
```

### 5. Testing the Configuration

Test the commands from Home Assistant to ensure they work correctly.

**On Home Assistant Server:**

- Execute the `wake_screen` and `sleep_screen` commands.
- Verify that the screen wakes and sleeps as expected.
- If there are issues, check the Home Assistant logs for errors.

### Notes

- Ensure the SSH private key (`/config/ssh/id_rsa`) is accessible by Home Assistant.
- Regularly check and maintain the security of your Home Assistant server, especially when using SSH keys without passphrases.
- Consider setting a static IP or using a hostname for your Raspberry Pi to ensure consistent access.

This setup allows you to control your Raspberry Pi's screen power state remotely from Home Assistant, enhancing the automation capabilities of your smart home setup.
