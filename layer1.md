# Setting up EdgeX :
## Installing Docker and docker-compose :

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

## Installing EdgeX Foundry :

The microservices making up EdgeX Foundry are controlled by a docker-compose file in YAML format. It specifies how each microservice should run, its ports, volumes and dependencies.

```
mkdir geneva
cd geneva
wget https://github.com/edgexfoundry/developer-scripts/blob/master/releases/geneva/compose-files/docker-compose-geneva-mongo.yml
cp docker-compose-geneva-mongo.yml docker-compose.yml
sudo docker-compose pull
sudo docker image ls
```

![image](https://user-images.githubusercontent.com/61081924/157315348-01585808-5f48-479b-9b14-95e5c9590452.png)

## Adding additional graphical user interfaces:
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

    
## Creating a device :
    
1. Create value descriptors:
    
Open Postman and use the following values:
```
Method: POST
URL:http://<edgex ip>:48080/api/v1/valuedescriptor
Payload settings:Set Body to “raw” and “JSON”
Payload data:
{
"name": "humid",
"description": "Ambient humidity in percent",
"min": "0",
"max": "100",
"type": "Int64",
"uomLabel": "humidity",
"defaultValue": "0",
"formatting": "%s",
"labels": [
"environment",
"humidity"
]
}
```
Tip: ***Use the very excellent feature “Beautify” on the “Body” tab in Postman to fix any
indentation issues with the JSON payload.***
Watch for the return code of “***200 OK*** ” and the ID of the newly created value descriptor.
There is no need to make note of the ID.
![image](https://user-images.githubusercontent.com/61081924/158033624-5eed66e3-17e6-4f13-92ce-f56f620b4136.png)

    
2. Update the body and issue the command again for temperature:
```
Method: POST
URI: http://<edgex ip>:48080/api/v1/valuedescriptor
Payload settings: Set Body to “raw” and “JSON”
Payload data:
{
"name": "temp",
"description": "Ambient temperature in Celsius",
"min": "-50",
"max": "100",
"type": "Int64",
"uomLabel": "temperature",
"defaultValue": "0",
"formatting": "%s",
"labels": [
"environment",
"temperature"
]
}
```
![image](https://user-images.githubusercontent.com/61081924/158033641-7077ce2b-8c5d-4c88-995f-7de5af9a0d8f.png)
    
2. Upload the device profile:
    
Get a copy of the device profile from here: https://github.com/bushra2001/Blockchain-Based-Edge-Computing-IoT-System/blob/main/geneva/sensorClusterDeviceProfile.yaml

In Postman use the following settings:
```
Method: POST
URI: http://<edgex ip>:48081/api/v1/deviceprofile/uploadfile
Payload settings:
This part is a bit tricky:
● Set Body to “form-data”
● Hover over KEY and select “File”
● Select the yaml file: sensorClusterDeviceProfile.yaml
● In the KEY field, enter “file” as key
```
![image](https://user-images.githubusercontent.com/61081924/158033762-2d095fd3-4272-4c6c-8b70-f8f0d1d82540.png)

3. Create the device:
    
Now EdgeX is finally ready to receive the device creation command in Postman as follows:
Two items are particularly important in this JSON body:
● The device service “edgex-device-rest” is used since this is a REST device.
● The profile name “SensorCluster” must match the name in the device profile yaml
file uploaded in the previous step.
Feel free to change values for description, location, labels, etc. if desired.
However, the name (Temp_and_Humidity_sensor_cluster_01) will be referenced
several times later and it’s recommended to keep it at the default for now.
In Postman use the following settings:
```
Method: POST
URI: http://<edgex ip>:48081/api/v1/device
Payload settings: Set Body to “raw” and “JSON”
Payload data:
{
"name": "Temp_and_Humidity_sensor_cluster_01",
"description": "Raspberry Pi sensor cluster",
"adminState": "unlocked",
"operatingState": "enabled",
"protocols": {
"example": {
"host": "dummy",
"port": "1234",
"unitID": "1"
undry: A hands-on tutorial
}
},
"labels": [
"Humidity sensor",
"Temperature sensor",
"DHT11"
],
"location": "Tokyo",
"service": {
"name": "edgex-device-rest"
},
"profile": {
"name": "SensorCluster"
}
}
```
![image](https://user-images.githubusercontent.com/61081924/158034013-a40e6666-fcf1-400b-b43c-8b1fa6d2a89f.png)

4. Send the data:
In Postman use the following settings to send a temperature value:
```
Method: POST
URI: http://<edgex ip>:49986/api/v1/resource/Temp_and_Humidity_sensor_cluster_01/temperature
Payload settings:
Set Body to “raw” and “text”
Payload data:
23​ (any integer value will do)

![image](https://user-images.githubusercontent.com/61081924/158034135-11c7d0da-c1b5-47b6-a1d9-a51c4952b87f.png)
```
5. View the data:

Use Postman to view the data stored in the EdgeX Foundry MOngo DB as follows
In Postman use the following settings to view the temperature value:
```
Method: GET
URI: http://<edgex ip>:48080/api/v1/reading
```
The return data should be similar to the following:
    
![image](https://user-images.githubusercontent.com/61081924/158034257-f6ffed6e-5d72-423d-b1a9-ad785e943884.png)
