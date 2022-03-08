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
