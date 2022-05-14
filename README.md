# FRP-With-Ubuntu-Amazon-EC2


### Goal: To gain access to a server in LAN (e.g., home) from anywhere

Note: Ubuntu OpenSSH-Server install and Setup Skipped.

#### 1. Register with Amazon AWS and create an instance of EC2
#### 2. (Server side - EC2 as server) Login to the cloud server and install frp from `https://github.com/fatedier/frp`

  ##### &emsp;&emsp;a. cd to the folder after unzipping it (e.g., cd frps)
  ##### &emsp;&emsp;b. modify `frps.ini` for port listening for proxy. 
        # frps.ini
        [common]
        bind_port = 7000 # 7000 as default value
        
        
Note: need to open this port on AWS console under `Security Groups` tap. 

  ##### &emsp;&emsp;c. run following within the frps folder
        sudo mv frps /usr/bin/.  # frps here is an excutable in linux.
        sudo mkdir /etc/frp
        sudo mv frps.ini /etc/frp/
        sudo mv frps.service /etc/systemd/system/
        sudo systemctl daemon-reload   
        sudo systemctl start frps.service # to run the service in backgroud
        sudo systemctl enable frps.service 
        
        
If everything is done correctly, you will see the prompt saying the server is running.

#### 3. (Client Side - home ubuntu as client) Back to home ubuntu (with OpenSSH enabled) and install frp from `https://github.com/fatedier/frp` again.

  ##### &emsp;&emsp;a. cd to the folder after unzipping it (e.g., cd frpc)
  ##### &emsp;&emsp;b. modify `frpc.ini` for port listening for proxy. 
        # frpc.ini
        [common]
        server_addr = x.x.x.x # EC2 IP address
        server_port = 7000 # server port as set in frps.ini

        [ssh] 
        type = tcp
        local_ip = 127.0.0.1 # LAN IP address
        local_port = 22 # SSH port, consistent with the port used in OpenSSH in home ubuntu
        remote_port = 6000 # port to access EC2 server

  ##### &emsp;&emsp;c. run following within the frpc folder

        sudo mv frpc /usr/bin/.  # frpc here is an excutable in linux.
        sudo mkdir /etc/frp
        sudo mv frpc.ini /etc/frp/
        sudo mv frpc.service /etc/systemd/system/
        sudo systemctl daemon-reload   
        sudo systemctl start frpc.service 
        sudo systemctl enable frpc.service # to run the service in backgroud
        sudo systemctl status frpc.service 

If everything is done correctly, you will see the prompt saying the home ubuntu is connected to the EC2 server.


#### 4. Now connect your home server with another device.
        ssh -p 6000 your_username_in_home_ubuntu@Public.IP.Address.of.EC2.
        
        
        
        
### Notes
1. frps means frp for server (EC2)
2. frpc means frp for client (home ubuntu)
3. Register frps/frpc as services because it will run automatically and save some time after every reboot. 
 
 
 

