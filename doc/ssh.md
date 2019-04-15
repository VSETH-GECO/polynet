## Base setup and enable ssh
In this tutorial we will look into how to setup a switch with a base setup that gives it an IP and enables ssh. This way we can further configure the switches using ssh and ansible. The goal is that this will be done automatically by PolyNet


### 1. Setup a Management IP
First, make sure you have performed basic network configurations on your switch. For example, assign default gateway, assign management ip-address, etc. If this is already done, skip to the next step.

In the following example, the management ip address is set as 10.10.1.2 in the 1 VLAN. The default gateway points to the core router, which is 10.10.1.1


    # ip default-gateway 10.10.1.1

    # config t
    (config)# interface vlan 1
    (config-if)# ip address 10.10.1.2 255.255.255.0


### 2. Set hostname and domain-name
Next, make sure the switch has a hostname and domain-name set properly.

    # config t
    (config)# hostname myswitch
    (config)# ip domain-name geco.local

### 3. Generate the RSA Keys
The switch or router should have RSA keys that it will use during the SSH process. So, generate these using crypto command as shown below.


    myswitch(config)# crypto key generate rsa
      The name for the keys will be: myswitch.thegeekstuff.com
      Choose the size of the key modulus in the range of 360 to 2048 for your
        General Purpose Keys. Choosing a key modulus greater than 512 may take
        a few minutes.

    How many bits in the modulus [512]: 1024
      % Generating 1024 bit RSA keys, keys will be non-exportable...[OK]

### 4. Setup the Line VTY configurations

Setup the following line vty configuration parameters, where input transport is set to SSH. Set the login to local, and password to 7.

    # line vty 0 4
    (config-line)# transport input ssh
    (config-line)# login local
    (config-line)# password 7
    (config-line)# exit

If you have not set the console line yet, set it to the following values.

    # line console 0
    (config-line)# logging synchronous
    (config-line)# login local

### 5. Create the username password
If you don’t have an username created already, do it as shown below.

    myswitch# config t
    Enter configuration commands, one per line.  End with CNTL/Z.
    myswitch(config)# username ramesh password mypassword

Note: If you don’t have the enable password setup properly, do it now.

    myswitch# enable secret myenablepassword

Make sure the password-encryption service is turned-on, which will encrypt the password, and when you do “sh run”, you’ll seee only the encrypted password and not clear-text password.

    myswitch# service password-encryption

### 6. Verify SSH access
From the switch, if you do ‘sh ip ssh’, it will confirm that the SSH is enabled on this cisco device.

    myswitch# sh ip ssh
    SSH Enabled - version 1.99
    Authentication timeout: 120 secs; Authentication retries: 3

After the above configurations, login from a remote machine to verify that you can ssh to this cisco switch.
