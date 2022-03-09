# Blockchain-Based Edge Computing IoT Architecture
The blockchain based edge computing architecture consist of three layers given as : IoT representation layer, Middleware, and blockchain.

## A three-layer annotated architecture :
![alt text](https://github.com/bushra2001/A-Blockchain-Based-Edge-Computing-Architecture-for-Internet-of-Things-Systems/blob/main/Screenshots/Pasted%20image%2020220308115123.png)
- The first layer replicates edge network containing different IoT devices, connected via internet. The user interface enhance visualization among IoT devices.
- Second layer facilitates in connection between layer 1 and layer 3, by providing data transport.
- Third layer consist of blockchain, which provides security, immutability, and privacy via decentralized data storage.

## Proposed overall architecture :
![alt_text](https://github.com/bushra2001/A-Blockchain-Based-Edge-Computing-Architecture-for-Internet-of-Things-Systems/blob/main/Screenshots/Pasted%20image%2020220308114253.png)
- For layer 1, EdgeX Foundary , an open source IoT edge computing platform is used.
- For layer 2, Microservices & Apache Kafka is used.
- For layer 3, Hyperledger Blockchain network is used.

## Setting up EdgeX :
### Installing Docker and docker-compose :

```
sudo apt update
sudo apt upgrade -y
sudo apt install apt-transport-https ca-certificates curl software-properties-common -y
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
echo 'deb [arch=amd64] https://download.docker.com/linux/debian buster stable' |sudo tee /etc/apt/sources.list.d/docker.list
sudo apt update
sudo apt remove docker docker-engine docker.io
sudo apt install docker-ce -y
sudo usermod -aG docker ${USER}
sudo apt install docker-compose -y
```

### Installing EdgeX Foundry :

The microservices making up EdgeX Foundry are controlled by a docker-compose file in YAML format. It specifies how each microservice should run, its ports, volumes and dependencies.

```
mkdir geneva
cd geneva
wget https://raw.githubusercontent.com/jonas-werner/EdgeX_Tutorial/master/docker-compose_files/docker-compose_step1.yml
cp docker-compose_step1.yml docker-compose.yml
sudo docker-compose pull
sudo docker image ls
```

![image](https://user-images.githubusercontent.com/61081924/157315348-01585808-5f48-479b-9b14-95e5c9590452.png)

### Adding additional graphical user interfaces:
- **Portainer**
1. Open the docker-compose.yml file. Under the volumes section at the beginning of the file, add an entry for portainer as per the below:
```
volumes:
db-data:
log-data:
consul-config:
consul-data:
> portainer_data:
```
2. Under the “services” section, add the following entry for Portainer:
```
portainer:
image: portainer/portainer
ports:
    - "0.0.0.0:9000:9000"
container_name: portainer
command: -H unix:///var/run/docker.sock
volumes:
    - /var/run/docker.sock:/var/run/docker.sock:z
    - portainer_data:/data
```
***Note: Be careful to match the indentation with the other services entries. If in doubt,
compare to entries above or below to make sure the indentation matches.***

3. Save and exit the editor.
4. Start (or restart) EdgeX Foundry
```
docker-compose up -d 
```
5. The new container image will be downloaded and started
6. Portainer can now be accessed in a browser at: http://<edgex ip>:9000
  ![image](https://user-images.githubusercontent.com/61081924/157474628-fd345585-d07e-4a5f-ab66-fd15091a182d.png)

  
