# T-Rex-Tutorial
Here is a quick tutorial to set up T Rex and some demo files to get it up and running! In this tutorial, I will only be going over T-Rex Stateless mode (STL). <br>
(Note: This tutorial was made using Ubuntu 20.04)
<br><br>

- [Table of contents](README.md)
    - [What is T-Rex?](#What-is-T-Rex)
    - [Installing T-Rex](#Installing-T-Rex)
    - [First Time Configuration of Network Interfaces](#First-Time-Configuration-of-Network-Interfaces)
    - [Running T-Rex](#Running-T-Rex)
        - [T-Rex Console](#T-Rex-Console)
        - [Python API](#Python-API)
    - [Conclusion](#That-is-all!)

## What is T-Rex?
T-Rex is a software traffic generator designed by Cisco that simulates real packet traffic across a network. T-Rex is fully configurable by the user. To learn more, visit the [T-Rex](https://trex-tgn.cisco.com) website. 

## Installing T-Rex
First, create a directory for T-Rex.
```sh
mkdir /opt/trex
cd /opt/trex
```

Now, download the latest T-Rex version and extract the tar file (Note: this tutorial was made using v2.89)
```sh 
wget --no-cache https://trex-tgn.cisco.com/trex/release/latest
tar -xzvf latest
```

Change directories into the extracted directory. (Replace X.XX with version)
```sh
cd vX.XX
```

## First Time Configuration of Network Interfaces
T-Rex operates by using the network interfaces on your machine as its ports. To create a virtual ethernet pair, run the following.
```sh
sudo ip link add veth1 type veth peer name veth2
```

To configure using the T-Rex port setup script provided in the installation, simply run the following script and follow the instructions. This script will create a config file `/etc/trex_cfg.yaml`. You can read more on [section 3.2](https://trex-tgn.cisco.com/trex/doc/trex_manual.html#_script_for_creating_config_file) of T-Rex's official documentation
```sh
sudo ./dpdk_setup_ports.py -i
```
However, if you're like me, the script doesn't show any of the network interfaces (maybe because I am on a VM). Instead, you can create a config file manually. (Note: use the text editor of your choice. I am using GEdit)
```sh
sudo touch /etc/trex_cfg.yaml
sudo gedit /etc/trex_cfg.yaml
```
Here is an example of a 2-port configuration. (Note: YAML files are strict with spacing. DO NOT use tabs.)
```
 port_limit    : 2 # Increase if you would like more interfaces. Use even numbers.
  version       : 2
  low_end       : true
  interfaces    : ["veth1", "veth2"]   # list of the interfaces to bind run ./dpdk_nic_bind.py --status
  port_info     :  # set eh mac addr

                 - ip         : 1.1.1.1
                   default_gw : 2.2.2.2
                 - ip         : 2.2.2.2
                   default_gw : 1.1.1.1
```
T-Rex is now configured to map port 0 and port 1 to veth1 and veth2 respectively.

## Running T-Rex
To run T-Rex, simply execute this command.
```sh
sudo ./t-rex-64 -i
```
You should now see the console running T-Rex. Here is what that should look like.
```
-Per port stats table 
      ports |               0 |               1 | 
 -----------------------------------------------------------------------------------------
   opackets |               0 |               0 |
     obytes |               0 |               0 |  
   ipackets |               0 |               0 |  
     ibytes |               0 |               0 | 
    ierrors |               0 |               0 | 
    oerrors |               0 |               0 | 
      Tx Bw |       0.00  bps |       0.00  bps |       0.00  bps |       0.00  bps 

-Global stats enabled 
 Cpu Utilization : 0.0  %
 Platform_factor : 1.0  
 Total-Tx        :       0.00  bps  
 Total-Rx        :       0.00  bps  
 Total-PPS       :       0.00  pps  
 Total-CPS       :       0.00  cps  

 Expected-PPS    :       0.00  pps  
 Expected-CPS    :       0.00  cps  
 Expected-BPS    :       0.00  bps  

 Active-flows    :        0  Clients :        0   Socket-util : 0.0000 %    
 Open-flows      :        0  Servers :        0   Socket :        0 Socket/Clients :  -nan 
 drop-rate       :       0.00  bps   
 current time    : X.X sec  
 test duration   : 0.0 sec  
 ```
 To interface with the T-Rex application, we can either use the T-Rex Console, or the T-Rex Python API.
 ### T-Rex Console
 In order to open the T-Rex Console, simply open another terminal while T-Rex is running, and execute this command.
 ```sh
 ./trex-console
 ```
 Now that you have access to the console, let's try sending simple UDP packets across the ports. To do this, we are simply going to start a pre-configured traffic profile. You can find many other traffic profiles in the `stl` directory. To run the `udp_1pkt_1mac.py` profile, execute this command in the T-Rex console.
 ```sh
start -f stl/udp_1pkt_1mac.py -m 10kpps --port 0
 ```
 This will start a packet stream using the profile defined in `udp_1pk_1mac.py` with a multiplier of 10kpps on port 0 (veth1 in our case). You can also run the given YAML traffic profiles using the same command. Just replace the python script path with the YAML file path. 
 <br><br>
 You can also create your own traffic profiles. For more info on how to configure traffic profiles, visit the T-Rex [Stateless API Reference](https://trex-tgn.cisco.com/trex/doc/cp_stl_docs/api/index.html)
 <br><br>
Now, if you check the T-Rex application, you should see traffic running from port 0 to port 1. You can access other statistics by opening the T-Rex console and running this command. (make sure to enlarge the terminal)
```sh
tui
```

 ### Python API
 The other way to interface with T-Rex is by using the Python API. There are pre-written Python scripts to look at in the `automation/trex_control_plane/interactive/trex/examples` directory. Let's try running `stl_simple_burst.py`.
 ```sh
 python3 automation/trex_control_plane/interactive/trex/examples/stl_simple_burst.py
 ```
 Again, once this python script is running, you can check the T-Rex application and see traffic running between the ports.
 <br><br>
 The Python API has various features that gives you full control of the kind of traffic you are sending over the ports. You can change source and destination MAC addresses, IPv4's, and ports. For more information on how to customize your traffic generator, visit the T-Rex [Python API Documentation](https://trex-tgn.cisco.com/trex/doc/cp_stl_docs/api/client_code.html).

 ## That is all!
 You can now create custom traffic profiles and Python scripts to generate traffic across whatever ports you would like! :)