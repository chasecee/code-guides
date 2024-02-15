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
