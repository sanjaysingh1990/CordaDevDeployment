# CordaDevDeployment Google Cloud
## Step1:
## Network Bootstrapper
Create a directory containing a node config file, ending in “_node.conf”, for each node you want to create. “devMode” must be set to true. Then run the following command:

### java -jar corda-tools-network-bootstrapper-4.0.jar --dir nodes-root-dir-name
[bootstrapper](https://drive.google.com/file/d/15cYBkutOo23xZAJgmEQ_jLMIpxX_qYyP/view?usp=sharing)

![config files](https://i.ibb.co/wKvyKDX/Screenshot-from-2019-07-11-09-58-18.png)  

### Each config file content

```devMode=true
myLegalName="O=PartyA,L=New York,C=US"
p2pAddress="34.66.250.52:10008" //external ip address google cloud
rpcSettings {
    address="10.128.0.7:10009" //internal ip address google cloud
    adminAddress="10.128.0.7:10049" //internal ip address google cloud
}
security {
    authService {
        dataSource {
            type=INMEMORY
            users=[
                {
                    password=test
                    permissions=[
                        ALL
                    ]
                    user=user1
                }
            ]
        }
    }
}
```
### after running network-bootstrap-tool
![config files](https://i.ibb.co/4FYpQGh/Screenshot-from-2019-07-11-10-05-02.png)  


### folder structure
![config files](https://i.ibb.co/b2wWgM7/Screenshot-from-2019-07-11-10-08-21.png)  


### Let's create VM for PartyA at google cloud

![config files](https://i.ibb.co/HNsWJdg/Screenshot-from-2019-07-11-10-10-55.png)  
![config files](https://i.ibb.co/fFJmnbV/Screenshot-from-2019-07-11-10-11-02.png)  
![config files](https://i.ibb.co/wRgnMPp/Screenshot-from-2019-07-11-10-11-31.png)  


### Let's configure the PartyA VM
#### Install 1.8 Open jdk
[here](https://www.geofis.org/en/install/install-on-linux/install-openjdk-8-on-ubuntu-trusty/)


### SSH cloud brower console 
![config files](https://i.ibb.co/PYW9bnL/Screenshot-from-2019-07-11-10-23-48.png)  

### Let's connect VM from ubuntu Terminal

#### Let's first create new priavte and public SSH key
ssh-keygen -t rsa -f ~/.ssh/[KEY_FILENAME] -C [USERNAME]

If you created a key on a Linux or macOS workstation by using the ssh-keygen tool, your key was saved to the following locations:

    Public key file: ~/.ssh/[KEY_FILENAME].pub
    Private key file: ~/.ssh/[KEY_FILENAME]

![config files](https://i.ibb.co/PYW9bnL/Screenshot-from-2019-07-11-10-23-48.png)  
 
#### Linux: Installing and running Corda as a system service and prepare VM for cordapps 
As root/sys admin user - add a system user which will be used to run Corda:

    sudo adduser --system --no-create-home --group corda

Create a directory called /opt/corda and change its ownership to the user you want to use to run Corda:

mkdir /opt/corda; chown corda:corda /opt/corda

#### Ubuntu 16.04 and most current Linux distributions use SystemD



#### SystemD: Create a corda.service file based on the example below and save it in the /etc/systemd/system/ directory

        [Unit]
        Description=Corda Node - Bank of Breakfast Tea
        Requires=network.target

        [Service]
        Type=simple
        User=corda
        WorkingDirectory=/opt/corda
        ExecStart=/usr/bin/java -jar /opt/corda/corda.jar
        Restart=on-failure

        [Install]
        WantedBy=multi-user.target

#### SystemD: Make sure the corda.service file is owned by root with the correct permissions:

        sudo chown root:root /etc/systemd/system/corda.service
        sudo chmod 644 /etc/systemd/system/corda.service



#### SystemD: Create a corda-webserver.service file based on the example below and save it in the /etc/systemd/system/ directory

    [Unit]
    Description=Webserver for Corda Node - Bank of Breakfast Tea
    Requires=network.target

    [Service]
    Type=simple
    User=corda
    WorkingDirectory=/opt/corda
    ExecStart=/usr/bin/java -jar /opt/corda/corda-webserver.jar
    Restart=on-failure

    [Install]
    WantedBy=multi-user.target

#### SystemD: You can now start a node and its webserver and set the services to start on boot by running the following systemctl commands:

        sudo systemctl daemon-reload
        sudo systemctl enable --now corda
        sudo systemctl enable --now corda-webserver


#### Let's copy partyA cotent to remote instance 

![Deploy node to server](https://i.ibb.co/bmt0Fpj/Screenshot-from-2019-07-11-11-04-35.png)


#### required files and folder copied to remote /opt/corda
![Deploy node to server](https://i.ibb.co/YR92fcj/Screenshot-from-2019-07-11-11-08-01.png)

#### Let's remove following db files as it's auto generated by the service
 1. persistence.trace.db
 2. persistence.mv.db



#### After deploying service let's check the service status 
![Deploy node to server](https://i.ibb.co/jv1tqmG/Screenshot-from-2019-07-11-11-19-03.png)


#### Let's check response status with telnet 
![Deploy node to server](https://i.ibb.co/Wc90PLq/Screenshot-from-2019-07-11-11-21-28.png)

#### Let's deploy the client

##### 1- client from local host
![Deploy node to server](https://i.ibb.co/nCgznjN/Screenshot-from-2019-07-11-11-28-47.png)

##### 1- client from instance
1. copy corda-webserver.jar to remote /opt/corda folder

![Deploy node to server](https://i.ibb.co/mhY3d5j/Screenshot-from-2019-07-11-11-31-08.png)

2.once copied let's start the service for web client
  
![Deploy node to server](https://i.ibb.co/Tt3Lw2q/Screenshot-from-2019-07-11-11-40-45.png)


3.on browser it's all done client deployed in instance
  
![Deploy node to server](https://i.ibb.co/Rp4mJ2q/Screenshot-from-2019-07-11-11-42-40.png)

#### In case client is not accessible please add new firewall rule as following

![Deploy node to server](
https://i.ibb.co/rmyxyHZ/Screenshot-from-2019-07-11-11-47-40.png)

![Deploy node to server](
https://i.ibb.co/zhX61RM/Screenshot-from-2019-07-11-11-48-29.png)

![Deploy node to server](
https://i.ibb.co/swYYJmv/Screenshot-from-2019-07-11-11-48-32.png)







