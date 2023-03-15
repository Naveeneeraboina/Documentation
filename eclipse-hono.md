-------------------------------
showing the status of protocols
-------------------------------
kubectl get pods -n hono

---------------------------------
showing the protocol Ip and Ports
---------------------------------
kubectl get services -n hono


-------------------
checking the tenant
-------------------
curl -sIX GET http://10.104.26.8:28080/v1/tenants/impressico

---------------------------------------
1.Creating a tenant named as impressico
---------------------------------------

curl -i -X POST -H "content-type: application/json" --data-binary '{
  "ext": {
    "messaging-type": "kafka"
  }
}' http://10.104.26.8:28080/v1/tenants/impressico

---------------------------------------
2.creating a device named as glucometer
---------------------------------------

curl -i -X POST http://10.104.26.8:28080/v1/devices/impressico/glucometer

-------------------------------------------
3.set device credentials as auth-id and pwd
-------------------------------------------
curl -i -X PUT -H "content-type: application/json" --data-binary '[{
  "type": "hashed-password",
  "auth-id": "glucometer",
  "secrets": [{
      "pwd-plain": "welcome"
  }]
}]' http://10.104.26.8:28080/v1/credentials/impressico/glucometer

---------------------
Listener cmd(syntax):
---------------------
echo "export APP_OPTIONS='-H ${KAFKA_IP} -P 9094 -u hono -p hono-secret --ca-file ${KAFKA_TRUSTSTORE_PATH} --disable-hostname-verification'" >> hono.env

-------------
Modified cmd:
-------------
APP_OPTIONS=-H 10.104.18.88 -P 9094 -u hono -p hono-secret --ca-file /tmp/truststore.pem --disable-hostname-verification"

------------------
Executing jar file
------------------
java -jar hono-cli-2.0.2-exec.jar app -H 10.104.18.88 -P 9094 -u hono -p hono-secret --ca-file /tmp/truststore.pem --disable-hostname-verification consume --tenant impressico


->when i run this cmd in another terminal,i have got the below error.
eraboinanaveen@IBS-LAP-371:~/Downloads$ java -jar hono-cli-2.0.2-exec.jar app -H 10.104.18.88 -P 9094 -u hono -p hono-secret --ca-file /tmp/truststore.pem --disable-hostname-verification consume --tenant impressico
Connecting to Kafka based messaging infrastructure [10.104.18.88:9094]

2022-09-06 12:07:30,573 ERROR [org.apa.kaf.com.sec.ssl.DefaultSslEngineFactory] (main) Modification time of key store could not be obtained: /tmp/truststore.pem: java.nio.file.NoSuchFileException: /tmp/truststore.pem

--------------------------
steps to solve this error:
--------------------------
i)first check whether the kafka truststore path is there or not in the tmp folder.

eraboinanaveen@IBS-LAP-371:~$ cd /tmp
eraboinanaveen@IBS-LAP-371:/tmp$ ls -1

ii)adding that file in tmp folder:

eraboinanaveen@IBS-LAP-371:/$ kubectl get secrets eclipse-hono-kafka-example-keys --template="{{index .data \"ca.crt\" | base64decode}}" -n hono > /tmp/truststore.pem

iii)eraboinanaveen@IBS-LAP-371:/$ cd /tmp
eraboinanaveen@IBS-LAP-371:/tmp$ ls

---------------------------------------------------------------------
4.after solving the jar file error, run the jar file again(Listener):(Another terminal)
---------------------------------------------------------------------
Starting the example Application:
_________________________________

# in directory where the hono-cli-*-exec.jar file has been downloaded to
java -jar hono-cli-*-exec.jar app ${APP_OPTIONS} consume --tenant ${MY_TENANT}

eraboinanaveen@IBS-LAP-371:~/Downloads$ java -jar hono-cli-2.0.2-exec.jar app -H 10.104.18.88 -P 9094 -u hono -p hono-secret --ca-file /tmp/truststore.pem --disable-hostname-verification consume --tenant impressico

----------------------------------------------
Publishing Telemetry Data to the HTTP Adapter:
----------------------------------------------
curl -i -u glucometer@impressico:welcome -H 'Content-Type: application/json' --data-binary '{"temp": 5}' http://10.100.184.207:8080/telemetry


----------------------------------------------
Publishing Telemetry Data to the MQTT Adapter:
----------------------------------------------
mosquitto_pub -h 10.110.242.198 -u glucometer@impressico -P welcome -t telemetry -m '{"temp": 5}'

--------------------------------------
Publishing Events to the HTTP Adapter:
--------------------------------------
curl -i -u ${MY_DEVICE}@${MY_TENANT}:${MY_PWD} -H 'Content-Type: application/json' --data-binary '{"alarm": "fire"}' http://${HTTP_ADAPTER_IP}:8080/event

curl -i -u glucometer@impressico:welcome -H 'Content-Type: application/json' --data-binary '{"alarm": "fire"}' http://10.100.184.207:8080/event

--------------------------------------
Publishing Events to the MQTT Adapter:
--------------------------------------
mosquitto_pub -h 10.110.242.198 -u glucometer@impressico -P welcome -t event -q 1 -m '{"alarm": "fire"}'

---------------------------------------
Advanced: Sending Commands to a Device:
---------------------------------------
an application can send a command to a device and receive a response with the result of processing the command on the device. 

--------------------
Receiving a Command:
--------------------
i)With the mosquitto_sub command you simulate an MQTT device that receives a command. Create a subscription to the command topic in the terminal for the simulated device:

-------------------------------------------
Create a subscription to the command topic:
-------------------------------------------
mosquitto_sub -v -h ${MQTT_ADAPTER_IP} -u ${MY_DEVICE}@${MY_TENANT} -P ${MY_PWD} -t command///req/#

mosquitto_sub -v -h 10.110.242.198 -u glucometer@impressico -P welcome -t command///req/#


ii)Now that the device is waiting to receive commands, the application can start sending them.

----------------------
Application to Device:
----------------------

# in directory where the hono-cli-*-exec.jar file has been downloaded to
java -jar hono-cli-*-exec.jar app ${APP_OPTIONS} command

java -jar hono-cli-2.0.2-exec.jar app -H 10.104.18.88 -P 9094 -u hono -p hono-secret --ca-file /tmp/truststore.pem --disable-hostname-verification command

--------------------------------------------------------------------------------
enter the following to send a one-way command with a JSON payload to the device:
--------------------------------------------------------------------------------

ow --tenant impressico --device glucometer -n setVolume --payload '{"level": 60}'

--------------------------------
Sending a Response to a Command:(Application to Device)    
--------------------------------

req --tenant impressico --device glucometer -n setBrightness --payload '{"level": 67}'

the application will wait up to 60 seconds for the device’s response.

----------------------
Device to Application:
----------------------

export REQ_ID=2240f7e317b-f286-49f0-840a-3354cf119f35
mosquitto_pub -h 10.110.242.198 -u glucometer@impressico -P welcome -t command///res/${REQ_ID}/200 -m '{"current-level": 30}'

mosquitto_pub -h 10.110.242.198 -u glucometer@impressico -P welcome -t command///res/224139acdf2-4940-47ae-96b8-839ae1c6af5a/200 -m '{"current-level": 67}'
