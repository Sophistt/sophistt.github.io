---
title: Internal Netwrok Penetration
date: 2019-05-30 13:39:58
tags: Network
categories: Linux
---

# Dependencies

- A computer in internal network environment
- A server in public network environment(Own a public IP address)
- A personal computer

# Principle

Personal computer --> Public Server --> Interal computer

# Procedure

- Open the 8086 port in of Public server. In **Security Group**, add a *Custom TCP Rule*(Protocol: TCP, Port: 8086)
- Open a terminal in Public server, and import the following commands.
  ```bash 
  sudo vim /etc/ssh/ssh_config
  # Add the following line in it
  GatewayPorts yes
  # Import the following command
  sudo systemctl restart ssh.service
  ```
- Open a terminal in the internal computer and import the following command.
    ```bash
    ssh -i /path/to/publicKey.gem -gfnNTR 0.0.0.0:8086:localhost:22 serverName@serverIP -o ServerAliveInterval=300
    ```
- Access the internal computer in the personal computer through `ssh internalName@serverIP -p 8086`.

# Autossh

- Sometimes ssh service is unstable and the conection will break off, so we can use `autossh` and `expect` to reconnect to server automatically. `autossh` provides a method to reconnect to the server and `expect` is responsible for entering password.
- Install
    ```bash
    sudo apt-get install autossh
    sudo apt-get install expect
    ```
- Create a new script `sshpass.sh` to connect to the server.
    ```bash
    #!/bin/bash
    HOST="serverIP"
    USER="serverName"
    PASS="serverPassword"
    CMD=$@

    VAR=$(expect -c "
    spawn /usr/bin/autossh -M 5555 -gnNTR 0.0.0.0:8086:localhost:22 $USER@$HOST $CMD
    match_max 100000
    expect \"*?assword:*\"
    send -- \"$PASS\r\"
    send -- \"\r\"
    expect eof
    ")

    echo "================="
    echo "$VAR"
    ```
- `-M` means autossh service monitor the connection status on port 5555, if the connection break off, it will reconnect to the server automatically.

# Execute autossh automatically

- In order to run autossh service automatically when the computer boots, we add it in StartUP Service.
- Use `chmod u+x sshpass.sh` to change its authority.
- Use `sudo cp sshpass.sh /etc/init.d` to cp the script to /etc/init.d folder.
- `cd /etc/init.d` and run `sudo update-rc.d sshpass.sh defaults 90` to add this script in StartUP Service.
- If we would like to remove this script from StartUP Service, just run the command `sudo update-rc.d f sshpass.sh remove`

# References

- [ssh内网穿透](https://blog.csdn.net/quantumenergy/article/details/78244003)
- [Ubuntu 16.04设置rc.local开机启动命令/脚本的方法](https://www.linuxidc.com/Linux/2017-09/147166.htm)
