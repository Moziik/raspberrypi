# raspberrypi
import paho.mqtt.client as mqtt
import time
import json
import requests


def on_connect(client, userdata, flags, rc):  					# ON CONNECT CALLBACK
    print("Connected with result code " + str(rc))

    # Subscribing in on_connect() means that if we lose the connection and
    # reconnect then subscriptions will be renewed.

    client.subscribe([(in_topic1, 1), ("ep_mqtt/topic1", 1)])			# subscribe to listen in_topic1


def on_message(client, userdata, message):					# ON MESSAGE CALLBACK
    print(" Message received")							# message received
  	
    m_decode=str(message.payload.decode("utf8"))				# Decode payload out from message
    print(m_decode)
    m_decode=m_decode[6:]							# remove IOTJS= from payload
 
    jsmes=json.loads(m_decode)							# JSON object jsmes

    sname=jsmes["S_name"]							# pick "S_name" value to sname
    svalue=(jsmes["S_value"])							# pick "S_value" value to svalue

    data={'device_id':'ICT_2021','data': { sname : svalue}}			# build Rest-API JSON String


    	
    print (data)

    headers = {'Content-type': 'application/json', 'Accept': 'text/plain'}	# define http header

    url = 'http://smart-iot-29.project.tamk.cloud/api/smart'			# database url
    print(url)
    r = requests.post(url, data=json.dumps(data), headers=headers)		# send POST request
    print(r.text)
    print(r)


    url = 'https://smart-iot-00.project.tamk.cloud/api/weather'
    print(url)
    r = requests.post(url, data=json.dumps(data), headers=headers)
    print(r.text)
    print(r)
    
#################################################################################

#broker_address = 	"192.168.1.8"  						# Broker address
broker_address = 	"localhost" 						# Broker address
port = 			1883  							# Broker port
in_topic1 =		"ICT1A_out_2021"					# in Topic

# user = 		"yourUser"      					# Connection username
# password = 		"yourPassword"  					# Connection password

######################################  start ##################################

print(" Start MQTT 2021 / Rest API ")

client = mqtt.Client()  							# create new instance

# client.username_pw_set(user, password=password)    				# set username and password

client.on_connect = on_connect  						# attach function to callback
client.on_message = on_message  						# attach function to callback

client.connect(broker_address, port)  						# connect to broker

client.loop_forever()								# stay on loop
