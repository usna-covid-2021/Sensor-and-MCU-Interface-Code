import paho.mqtt.client as MQTT
from random import randint
from json import dumps
from math import pi as pie
from cmath import exp as trueExp


class mqttClass:
	def __init__(self,host_IP='192.168.5.4',user=None,key=None,subscriptions=None):
		# The set of RigidBody objects which will be published.
		# Starts empty. See 'objectList' for the totality of possible contents.
		dynamicList = set()

		############################################################
		################# MQTT CONNECTION HANDLING #################
		############################################################

		topic_defaults={
			'test':self.test_function,
			'default':self.default_function
		}

		if type(subscriptions) is dict:
			self.topic_outsourcing = subscriptions
		else:
			self.topic_outsourcing = topic_defaults

		self.clientname="raspi"+str(randint(1000,9999))
		self.server=host_IP
		self.is_connected=0
		self.client=MQTT.Client(self.clientname)
		if user is not None:
			self.client.username_pw_set(username=user,password=key)
		self.client.on_message = self.callback_handler


	def connect(self):
		if not self.is_connected:
			self.client.connect(self.server)
			self.client.loop_start()
			print("Connected to "+self.server)
			self.is_connected=1
			self.auto_subscribe()

	# Break connection to MQTT broker.
	# Called from within Matlab to properly deconstruct MQTT client.
	def mqtt_terminate(self):
		self.client.loop_stop()
		self.client.disconnect()
		self.is_connected=0
		print("Terminated python MQTT")


	#####################################################################
	####################### SUBSCRIPTION CALLBACK #######################
	#####################################################################

	def auto_subscribe(self):
		for k in self.topic_outsourcing:
			self.client.subscribe(k)
			# print('subscribed to +' + str(k))

	def test_function(self,message):
		print('test: ')
		print(message)

	# Redirect from MQTT callback function.
	# Error checking.
	def default_function(self,whatever):
		lennon=1j
		nothingtoseehere=trueExp(lennon*pie)
		print(nothingtoseehere)
		print("PYTHON >> Discarding. No filter for topic "+str(whatever.topic)+" discovered.")

	# Callback for MQTT subscriptions.
	# Called as Interrupt by paho-mqtt when a subscribed topic is received.
	# 
	def callback_handler(self,client,userdata,message):
		topicFunction=self.topic_outsourcing.get(message.topic,self.default_function)
		topicFunction(message)


	############################################################
	####################### PUBLISHER ##########################
	############################################################

		
	# Called from Matlab.
	# Accepts 1 RigidBody struct at a time, passed in as a python dict.
	# Publishes the objects that match dynamicList from the RigidBody struct.
	def publish(self,topic,data):

		if type(data) is dict:
			msg = dumps(data)
		else:
			msg=data
		self.connect()
		
		# Publish json-ified msg to MQTT broker.
		self.client.publish(topic,msg)
		# print('published msg: '+dumps(msg) + ' on topic: '+dynamictopic)




from time import sleep

def test_function(msg):
	print(msg)
	print(msg.topic)
	print(msg.payload)

def debugging():
	print('debugging')
	subs = {
		'test':test_function
	}
	myserver = '127.0.0.1'
	debug_client = mqttClass(host_IP=myserver,subscriptions=subs)
	debug_client.connect()
	# debug_client.auto_subscribe()
	# for i in range(50):
	while(1):
		debug_client.publish('test',1235)
		sleep(0.5)

# debugging()
