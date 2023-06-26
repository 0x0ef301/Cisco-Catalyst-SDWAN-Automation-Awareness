# Cisco Catalyst SDWAN Automation Awareness Month - June 2023

## Environment Setup

Python 3.8 or newer is required. This can be verified by pasting the following to a terminal window:
```
% python3 -c "import sys;assert sys.version_info>(3,8)" && echo "ALL GOOD"
```

If 'ALL GOOD' is printed it means Python requirements are met. If not, download and install the latest 3.x version at Python.org (https://www.python.org/downloads/).

Create virtual environment:
```
% python3 -m venv venv
```
    
Activate virtual environment:
```
% source venv/bin/activate
(venv) %
```
- Note that the prompt is updated with the virtual environment name (venv), indicating that the virtual environment is active.
    
Upgrade initial virtual environment packages:
```
(venv) % pip install --upgrade pip setuptools
```

Install packages from requirements.txt:
```
(venv) % pip install --upgrade -r requirements.txt
```

Install Sastre-Pro package:
```
(venv) % pip install --upgrade ./CLI/dist/cisco_sdwan-1.21.2-py3-none-any.whl
```

Install Sastre-Ansible collection:
```
(venv) % ansible-galaxy collection install -f ./Ansible/dist/cisco-sastre-1.0.16.tar.gz
```

## DevNet Sandbox

In order to run the exercises you will need access to a vManage and SDWAN overlay setup. A sandbox can be reserved at [Devnet](https://devnetsandbox.cisco.com/RM/Topology).

In the 'Search Sandbox Labs' box, enter SD-WAN. Then click on 'RESERVE' under Cisco SD-WAN 20.10.

Once the lab is ready, please connect to it using AnyConnect VPN client.


## Sastre CLI

Sastre repository: https://github.com/CiscoDevNet/sastre

Enter the CLI directory and source the environment variables:
```
% cd CLI
% source ./devnet-sandbox.sh
```

In order to simplify the command line parameters needed for Sastre, DevNet sandbox vManage address and credentials were loaded via environment variables defined in devnet-sandbox.sh.

The 'sdwan show devices' command can be used to validate that we can communicate with vManage:
```
% sdwan show devices
+==============================================================================+
| Name          | System IP  | Site ID | Reachability | Type    | Model        |
+==============================================================================+
| dc-cedge01    | 10.10.1.11 | 100     | reachable    | vedge   | vedge-C8000V |
| site1-cedge01 | 10.10.1.13 | 1001    | reachable    | vedge   | vedge-C8000V |
| site2-cedge01 | 10.10.1.15 | 1002    | reachable    | vedge   | vedge-C8000V |
| site3-vedge01 | 10.10.1.17 | 1003    | reachable    | vedge   | vedge-cloud  |
| vbond         | 10.10.1.3  | 101     | reachable    | vbond   | vedge-cloud  |
| vmanage       | 10.10.1.1  | 101     | reachable    | vmanage | vmanage      |
| vsmart        | 10.10.1.5  | 101     | reachable    | vsmart  | vsmart       |
+---------------+------------+---------+--------------+---------+--------------+
```

Displaying information about site1-cedge01 control connections:
```
% sdwan --verbose show realtime control connections --system-ip 10.10.1.13                     
INFO: Show realtime task: vManage URL: "https://10.10.20.90"
INFO: Device selection matched 1 devices
INFO: Retrieving control connections for 1 devices
*** Control connections ***
+===============================================================================================+
| Device        | Peer System IP | Site ID | Peer Type | Local Color     | Remote Color | State |
+===============================================================================================+
| site1-cedge01 | 10.10.1.1      | 101     | vmanage   | mpls            | default      | up    |
| site1-cedge01 | 10.10.1.5      | 101     | vsmart    | mpls            | default      | up    |
| site1-cedge01 | 10.10.1.5      | 101     | vsmart    | public-internet | default      | up    |
+---------------+----------------+---------+-----------+-----------------+--------------+-------+
INFO: Task completed successfully
```

Displaying latency information from site1-cedge1 tunnels:
```
% sdwan --verbose show realtime app-route stats --system-ip 10.10.1.13
INFO: Show realtime task: vManage URL: "https://10.10.20.90"
INFO: Device selection matched 1 devices
INFO: Retrieving application-aware route statistics for 1 devices
*** Application-aware route statistics ***
+========================================================================================================================================+
| Device        | Index | Remote System Ip | Local Color     | Remote Color    | Total Packets | Loss | Average Latency | Average Jitter |
+========================================================================================================================================+
| site1-cedge01 | 0     | 10.10.1.11       | mpls            | mpls            | 365           | 0    | 0               | 0              |
| site1-cedge01 | 0     | 10.10.1.11       | mpls            | public-internet | 364           | 1    | 114             | 4              |
| site1-cedge01 | 0     | 10.10.1.11       | public-internet | mpls            | 365           | 0    | 1               | 1              |
| site1-cedge01 | 0     | 10.10.1.11       | public-internet | public-internet | 368           | 0    | 113             | 4              |
| site1-cedge01 | 0     | 10.10.1.15       | mpls            | mpls            | 367           | 0    | 0               | 0              |
| site1-cedge01 | 0     | 10.10.1.15       | mpls            | public-internet | 365           | 0    | 1               | 0              |
| site1-cedge01 | 0     | 10.10.1.15       | public-internet | mpls            | 367           | 0    | 1               | 1              |
| site1-cedge01 | 0     | 10.10.1.15       | public-internet | public-internet | 366           | 0    | 0               | 0              |
| site1-cedge01 | 0     | 10.10.1.17       | mpls            | mpls            | 367           | 0    | 6               | 8              |
| site1-cedge01 | 0     | 10.10.1.17       | mpls            | public-internet | 364           | 0    | 8               | 9              |
| site1-cedge01 | 0     | 10.10.1.17       | public-internet | mpls            | 365           | 0    | 7               | 9              |
| site1-cedge01 | 0     | 10.10.1.17       | public-internet | public-internet | 367           | 0    | 6               | 9              |
| site1-cedge01 | 1     | 10.10.1.11       | mpls            | mpls            | 366           | 0    | 0               | 0              |
| site1-cedge01 | 1     | 10.10.1.11       | mpls            | public-internet | 367           | 0    | 114             | 4              |
| site1-cedge01 | 1     | 10.10.1.11       | public-internet | mpls            | 364           | 0    | 1               | 0              |
| site1-cedge01 | 1     | 10.10.1.11       | public-internet | public-internet | 368           | 0    | 113             | 4              |
| site1-cedge01 | 1     | 10.10.1.15       | mpls            | mpls            | 367           | 0    | 0               | 0              |
| site1-cedge01 | 1     | 10.10.1.15       | mpls            | public-internet | 368           | 0    | 1               | 1              |
| site1-cedge01 | 1     | 10.10.1.15       | public-internet | mpls            | 368           | 0    | 1               | 0              |
| site1-cedge01 | 1     | 10.10.1.15       | public-internet | public-internet | 367           | 0    | 0               | 1              |
| site1-cedge01 | 1     | 10.10.1.17       | mpls            | mpls            | 365           | 0    | 6               | 9              |
| site1-cedge01 | 1     | 10.10.1.17       | mpls            | public-internet | 367           | 0    | 7               | 8              |
| site1-cedge01 | 1     | 10.10.1.17       | public-internet | mpls            | 367           | 0    | 7               | 8              |
| site1-cedge01 | 1     | 10.10.1.17       | public-internet | public-internet | 367           | 0    | 7               | 9              |
| site1-cedge01 | 2     | 10.10.1.11       | mpls            | mpls            | 367           | 0    | 0               | 0              |
| site1-cedge01 | 2     | 10.10.1.11       | mpls            | public-internet | 367           | 2    | 114             | 4              |
| site1-cedge01 | 2     | 10.10.1.11       | public-internet | mpls            | 369           | 0    | 1               | 0              |
| site1-cedge01 | 2     | 10.10.1.11       | public-internet | public-internet | 367           | 0    | 113             | 4              |
| site1-cedge01 | 2     | 10.10.1.15       | mpls            | mpls            | 367           | 0    | 0               | 0              |
| site1-cedge01 | 2     | 10.10.1.15       | mpls            | public-internet | 367           | 0    | 1               | 1              |
| site1-cedge01 | 2     | 10.10.1.15       | public-internet | mpls            | 368           | 0    | 1               | 0              |
| site1-cedge01 | 2     | 10.10.1.15       | public-internet | public-internet | 366           | 0    | 1               | 1              |
| site1-cedge01 | 2     | 10.10.1.17       | mpls            | mpls            | 367           | 0    | 7               | 9              |
| site1-cedge01 | 2     | 10.10.1.17       | mpls            | public-internet | 367           | 0    | 7               | 9              |
| site1-cedge01 | 2     | 10.10.1.17       | public-internet | mpls            | 367           | 0    | 8               | 9              |
| site1-cedge01 | 2     | 10.10.1.17       | public-internet | public-internet | 367           | 0    | 6               | 8              |
| site1-cedge01 | 3     | 10.10.1.11       | mpls            | mpls            | 374           | 0    | 0               | 0              |
| site1-cedge01 | 3     | 10.10.1.11       | mpls            | public-internet | 373           | 1    | 116             | 4              |
| site1-cedge01 | 3     | 10.10.1.11       | public-internet | mpls            | 371           | 0    | 1               | 0              |
| site1-cedge01 | 3     | 10.10.1.11       | public-internet | public-internet | 372           | 0    | 115             | 4              |
| site1-cedge01 | 3     | 10.10.1.15       | mpls            | mpls            | 371           | 0    | 0               | 0              |
| site1-cedge01 | 3     | 10.10.1.15       | mpls            | public-internet | 374           | 0    | 1               | 0              |
| site1-cedge01 | 3     | 10.10.1.15       | public-internet | mpls            | 373           | 0    | 1               | 0              |
| site1-cedge01 | 3     | 10.10.1.15       | public-internet | public-internet | 370           | 0    | 0               | 0              |
| site1-cedge01 | 3     | 10.10.1.17       | mpls            | mpls            | 373           | 0    | 8               | 10             |
| site1-cedge01 | 3     | 10.10.1.17       | mpls            | public-internet | 372           | 0    | 7               | 8              |
| site1-cedge01 | 3     | 10.10.1.17       | public-internet | mpls            | 372           | 0    | 7               | 9              |
| site1-cedge01 | 3     | 10.10.1.17       | public-internet | public-internet | 373           | 0    | 6               | 8              |
| site1-cedge01 | 4     | 10.10.1.11       | mpls            | mpls            | 377           | 0    | 0               | 0              |
| site1-cedge01 | 4     | 10.10.1.11       | mpls            | public-internet | 377           | 0    | 117             | 4              |
| site1-cedge01 | 4     | 10.10.1.11       | public-internet | mpls            | 376           | 0    | 1               | 0              |
| site1-cedge01 | 4     | 10.10.1.11       | public-internet | public-internet | 375           | 0    | 115             | 4              |
| site1-cedge01 | 4     | 10.10.1.15       | mpls            | mpls            | 374           | 0    | 0               | 0              |
| site1-cedge01 | 4     | 10.10.1.15       | mpls            | public-internet | 377           | 0    | 1               | 0              |
| site1-cedge01 | 4     | 10.10.1.15       | public-internet | mpls            | 376           | 0    | 1               | 1              |
| site1-cedge01 | 4     | 10.10.1.15       | public-internet | public-internet | 375           | 0    | 0               | 1              |
| site1-cedge01 | 4     | 10.10.1.17       | mpls            | mpls            | 377           | 0    | 7               | 9              |
| site1-cedge01 | 4     | 10.10.1.17       | mpls            | public-internet | 377           | 0    | 7               | 9              |
| site1-cedge01 | 4     | 10.10.1.17       | public-internet | mpls            | 375           | 0    | 8               | 9              |
| site1-cedge01 | 4     | 10.10.1.17       | public-internet | public-internet | 377           | 0    | 7               | 10             |
| site1-cedge01 | 5     | 10.10.1.11       | mpls            | mpls            | 377           | 0    | 0               | 0              |
| site1-cedge01 | 5     | 10.10.1.11       | mpls            | public-internet | 378           | 0    | 117             | 3              |
| site1-cedge01 | 5     | 10.10.1.11       | public-internet | mpls            | 377           | 0    | 1               | 0              |
| site1-cedge01 | 5     | 10.10.1.11       | public-internet | public-internet | 377           | 0    | 116             | 4              |
| site1-cedge01 | 5     | 10.10.1.15       | mpls            | mpls            | 376           | 0    | 1               | 1              |
| site1-cedge01 | 5     | 10.10.1.15       | mpls            | public-internet | 377           | 0    | 1               | 1              |
| site1-cedge01 | 5     | 10.10.1.15       | public-internet | mpls            | 377           | 0    | 1               | 1              |
| site1-cedge01 | 5     | 10.10.1.15       | public-internet | public-internet | 375           | 0    | 1               | 1              |
| site1-cedge01 | 5     | 10.10.1.17       | mpls            | mpls            | 377           | 0    | 7               | 10             |
| site1-cedge01 | 5     | 10.10.1.17       | mpls            | public-internet | 376           | 0    | 7               | 9              |
| site1-cedge01 | 5     | 10.10.1.17       | public-internet | mpls            | 379           | 0    | 7               | 9              |
| site1-cedge01 | 5     | 10.10.1.17       | public-internet | public-internet | 376           | 0    | 7               | 8              |
+---------------+-------+------------------+-----------------+-----------------+---------------+------+-----------------+----------------+
INFO: Task completed successfully
```

## Sastre Ansible

Sastre-Ansible repository: https://github.com/CiscoDevNet/sastre-ansible

Enter the Ansible directory:
```
% cd Ansible
```

Execute the playbook:
```
% ansible-playbook -i inventory.yml playbook.yml 
```

## Sastre SDK via Jupyter notebooks

Enter the SDK directory:
```
% cd SDK
```

Start Jupyter Lab:
```
% jupyter lab
```
