---
layout: post
title:  "IoT...for gardening?"
date:   2020-02-13 15:00:00 -0500
categories: iot
---

# Gardening devices

I happened to be browsing amazon for some seeds and soil testing kits because I've sort of been
wanting to do some gardening.  I think I mentioned in an earlier blog that Morehei Ueshiba, the
founder of Aikido, said that all warriors should learn to farm.  There's something about cultivating
life that makes you respect and understand it more.

So, I'm browsing the returned results, and I see some electronic devices that monitored 3 things:

- Moisture
- Sunlight
- Ph levels

There were also some testing kits for potassium and nitrogen, though those were chemically based
testing kits.  But I realized that these electronic devices didn't relay any information.  Though I
haven't looked or done any research at the industrial level, it made me think about how IoT devices
could help improve the efficiency of gardening

## Connected testing kits

One of the first things that could be done is to send data to a user's phone for real time
monitoring.  Instead of going out to the garden, one could always look at their phone and get a real
time update on the conditions of the soil.

This kind of defeats some of the fun of gardening, as people who garden _like_ going outside to
check on their plants, vegetables or fruits.  Still, I think having this convenience would be nice.

## Data collection over time

What might be more useful, is to send data to be collected to be analyzed.  For what times of the
day, and how long is there not enough sunlight?  Are there long stretches where the soil is to dry?
Does the Ph level vary dramatically?

This information could be used to monitor and alert the gardner that perhaps more fertilizer is
needed, or that where they have placed their crop is not good for sunlight levels.  At our house, we
have quite a few trees, plus, our backyard slopes at an angle.  We therefore have lots of shadows
being cast.  Other than cloud cover, it's hard to exactly say what spots on our land get how much
sunlight over the course of the day.

Being in Florida, we also can have surprising amounts of rainfall in a short amount of time.
Perhaps knowing how much moisture is in the soil can help by having better drainage or runoff in
one's garden.

## Defense against animals and pests

Truth be told, one of the reasons I built [khadga.app][-khadga-app] was to be able to recognize deer
and then build a little water gun to shoot water at them.  Yes...a water gun.  We have had quite a
bad problem with deer eating fruits and vegetables and even flowers in the garden.  Imagine a little
camera hooked up to a ground sensor (or it can perhaps pan and scan) looking for deer at night.  The
image recognition software would identify animals or birds (not humans) and control a valve that can
turn on or off a water hose.

I think this would be a totally fun project to do because:

- You need deep learning for the image recognition
- You can use deep learning to track objects
- Microcontroller work to control the servos and actuators that control the camera and valve
- Sensor data either from ground pressure or some other sensory input
- Data tracking, to track how many animals are seen, power levels, etc
- Electronics to hook up solar panels to charge batteries in day, and battery powered at night

## So how could you actually implement this?

There are a couple of technical challenges that would need to be solved

- Deep learning to recognize animals/birds that are eating produce
- Connectivity to a gateway if analytics are used
- Analytics data processing, both in real time on IoT device, and to service
- Power for the IoT devices
- Weatherproofing the devices
- User interface to see real-time streams of sensor data, including camera

### Deep learning for pest recognition

This is probably the most fun part, or at least most technically interesting to me.  I could use
models to learn to identify non-human objects that are eating plants.  It might be trickier to have
it not detect domestic animals like dogs or cats.  It would also need to recognize birds as well as
other mammals that eat vegetables.  So it needs to be able to identify many kinds of animals:

- Raccoons
- Squirrels
- Ravens
- Cardinals
- Deer
- etc

Of course, these models aren't perfect.  Even the best prediction rates seem to be around 98%.  So
that means the occasional human might get soaked, or it might allow some pests to eat some produce.

### Connectivity for analytics

If we have some soil monitor many yards away from the house, how can we connect to it?

Some IoT devices operate in mesh networks.  Basically, the majority of devices work in a low energy
radio transceiver like bluetooth or zigbee.  These short range devices can then hook up to slightly
more beefy Gateways.  The gateway usually has a longer range transceiver using wifi.  By bridging
these gateways together, and eventually to a home router, you can cover longer distances and areas.

If you have a Philipps Hue lighting setup, you have a mesh network of IoT devices in your home.  The
hue bridge is your gateway, and each hue light bulb has a low energy tranceiver (zigbee).  By daisy
chaining your lights, you can extend the distances of them.

There are some limits to these mesh networks like how many devices you can hang off a gateway.  But
this would be how we can extend out connectivity to far away soil monitors.

For a more commercial application, where you have soil test monitors miles away, one can use
cellular radios on the device.  These have higher energy requirements, as they use either 2g or 3g
to communicate at very long ranges with cell towers.

Yet another possibility is to have the IoT gateway push the data out to a publicly accessible
endpoint.  All the major cloud players have IoT services now.  One thing you have to do though is
authenticate your IoT device to the cloud.  You don't want random device `hacker-bot` to start
sending/spamming your pub/sub broker.  Provisioning TLS certs on the devices and then making sure
that the cloud broker has it can be a bit of a challenge.

### Data process analytics

If these devices start sending data in real time, who is going to listen to the data, and what are
they going to do with it?

Some questions that would be nice to be answered were covered earlier:

- How much sunlight in 24hours is this area getting?
- Are there fluctuations in Ph level over some time span?
- Are there fluctuations in moisture over some time span?
- If we know what kind of crop is planted in this area, is the Ph, sunlight, or moisture to low/high
  for it?
- How many pests are trying to eat crops in a given time span?
- What is the temperature range over time?
- Given a weather forecast, determine if an alert to protect the crops should be made (ie freezes)

Assuming that we have the connectivity issues above solved, what will actually do the analytics?
the simple solution would be to have some kind of cloud service crunching the data.  The data could
then be stored in a database, and queries could be performed on it.

Since most of these questions are not real-time monitors (that should be done by the device itself),
we need to store the data somewhere.

### Power for the IoT devices

There's actually 2 devices to consider:

- The soil monitor
- The water gun

For the soil monitor, the power requirements will be very miminal, and probably a smallish cortex m4
will be more than sufficient.  If we use a mesh network, we can use bluetooth or zigbee to connect
devices until it reaches a more beefy wifi gateway device to transmit data.

For the water gun, most of the processing should probably be done on the IoT device itself.  That
would probably mean a beefier microcontroller with TPU support.  This has the disadvantage of having
higher power requirements.  Another option would be to send data over for the image recognition to
be done by something more beefy.  However, streaming data has a power cost too.

I think some research into a middle ground processor...something between a Raspberry Pi and an
Cortex M7 would probably be ok.  If the prediction analysis is going to be done at the edge by the
device itself, then it needs to have a reasonable amount of calculative power.  We also want this to
be relatively cheap.

I would also need to look into solar panels, and charging of lithium ion batteries.

### Weatherproofing

This is where I'm not even really sure where to begin.  Perhaps there are already some 3d printing
solutions out there that allow you to assemble water tight containers.  More likely though, there
will need to be some other kind of molding/assembly process to do this.

### User interface

This is where khadga.app could come into play.  Of course not khadga itself, but probably a fork of
it. I have always wanted khadga to allow any _user_ to communicate.  That can include some IoT
device that connects to it.  MQTT can be transmitted over websockets, so this is a viable approach.

Another option is to stream the data locally to an android device.  I haven't looked at Android
development, but I could use react-native.  I really don't feel like getting back into Java or
learning Kotlin.  This option will only work if your android can connect to the same LAN as the IoT
gateway.  That way the gateway could directly stream info the phone.

If you're on 4g/5g or on some other public wi-fi, this wont work without having the data being sent
to some public endpoint.