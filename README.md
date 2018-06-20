<!--
# Copyright (C) 2017 Resin.io, UNI Passau, FBK.
# All rights reserved. This program and the accompanying materials
# are made available under the terms of the Eclipse Public License 2.0
# which accompanies this distribution, and is available at
# https://www.eclipse.org/legal/epl-2.0/
#
# SPDX-License-Identifier: EPL-2.0
#
# Contributors:
#     Resin.io, UNI Passau, FBK - initial API and implementation
-->

# Modosmart Agile-stack

This is a forked repository from [agile-stack](https://github.com/Agile-IoT/agile-stack) with some tweaks to support the Modosmart home automation sensors

## Overview:

We use a [Raspberry pi](https://www.raspberrypi.org/) as a remote to [docker-compose](https://docs.docker.com/compose/overview/) on our work machine. This allows us to work as we would on our x86 machine and push changes over the local network to the docker engine running on the arm device (the Raspberry pi), which rebuilds/restarts the containers and gives us back the logs.

## What is Agile-iot

The [agile-iot](http://agile-iot.eu/) framework language-agnostic, modular software and hardware gateway framework for the Internet of Things with support for protocol interoperability, device and data management, IoT application execution, trusted data sharing and external Cloud communication.

The full [agile-iot](http://agile-iot.eu/) stack consists of several services defined in the [agile-stack](https://github.com/Agile-IoT/agile-stack) docker-compose.yml file.

Each of those service source code is available in the [agile-iot github channel](https://github.com/Agile-IoT)

## Agile-iot stack

In our tests the following versions are used from agile-stack

| Image/Container   | Version/Tag   |
| -------------     |:-------------:|
| agile-dbus        | v0.1.4        |
| agile-core        | v0.2.17       |
| agile-ble         | v0.1.11       |
| agile-dummy       | v0.3.1        |
| agile-ui          | v2.5.1        |
| agile-osjs        | v0.4.0        |
| agile-nodered     | v0.3.2        |
| agile-security    | v3.7.3        |
| agile-data        | v0.1.3        |
| agile-recommender | v0.3.7        |

To configure the Agile-IoT to work with ModoSmart devices, only one of the preivous mentioned service should be changed which is agile-core inorder to add definition for our custom devices.

We created a fork for the agile-core [here](https://github.com/mohamed-elsabagh/agile-core) where we added our devices to the list of the devices as a Java class.

We added support for two of our sensors which are [Room Sensor](https://github.com/mohamed-elsabagh/agile-core/blob/master/org.eclipse.agail.DeviceFactory/src/main/java/org/eclipse/agail/device/instance/ModosmartRoomSensorDevice.java) and [Window Sensor](https://github.com/mohamed-elsabagh/agile-core/blob/master/org.eclipse.agail.DeviceFactory/src/main/java/org/eclipse/agail/device/instance/ModosmartWindowSensor.java) as well as a [testing sensor device](https://github.com/mohamed-elsabagh/agile-core/blob/master/org.eclipse.agail.DeviceFactory/src/main/java/org/eclipse/agail/device/instance/ModosmartTest.java)

## BLE Sensors

#### Room Sensor

| 128 bit UUID                          | short UUID    | BLE Service name          | BLE Characteristic name |
| :-----------------------------------: |:-------------:|:------------------------: |:------------------------: |
| 0000A000-0000-1000-8000-00805f9b34fb  | 0xA000        |Environmental data service ||
| 0000A001-0000-1000-8000-00805f9b34fb  | 0xA001        |                           |Presence detection|
| 00002A6E-0000-1000-8000-00805f9b34fb  | 0x2A6E        |                           |Temperature|
| 00002A6F-0000-1000-8000-00805f9b34fb  | 0x2A6F        |                           |	Humidity|
| 0000180F-0000-1000-8000-00805f9b34fb  | 0x180F        |	Battery service           ||
| 00002A19-0000-1000-8000-00805f9b34fb  | 0x2A19        |                           |Battery level|
| 0000180A-0000-1000-8000-00805f9b34fb  | 0x180A        |Device Information Service ||
| 00002A26-0000-1000-8000-00805f9b34fb  | 0x2A26        |                           |Firmware Revision String||


#### Window Sensor
| 128 bit UUID                          | short UUID    | BLE Service name          | BLE Characteristic name |
| :-----------------------------------: |:-------------:|:------------------------: |:------------------------: |
| 0000180F-0000-1000-8000-00805f9b34fb  | 0x180F        |Battery service            ||
| 00002A19-0000-1000-8000-00805f9b34fb  | 0x2A19        |                           |Battery level|
| 0000180A-0000-1000-8000-00805f9b34fb  | 0x180A        |Device Information Service||
| 00002A26-0000-1000-8000-00805f9b34fb  | 0x2A26        |                           |	Firmware Revision String||

The previous sensors are defined as [Room Sensor](https://github.com/mohamed-elsabagh/agile-core/blob/master/org.eclipse.agail.DeviceFactory/src/main/java/org/eclipse/agail/device/instance/ModosmartRoomSensorDevice.java) and [Window Sensor](https://github.com/mohamed-elsabagh/agile-core/blob/master/org.eclipse.agail.DeviceFactory/src/main/java/org/eclipse/agail/device/instance/ModosmartWindowSensor.java) as well as a [testing sensor device](https://github.com/mohamed-elsabagh/agile-core/blob/master/org.eclipse.agail.DeviceFactory/src/main/java/org/eclipse/agail/device/instance/ModosmartTest.java) in Java class so it can be compiled locally with the forked version of the [agile-core](https://github.com/mohamed-elsabagh/agile-core)

## Usage:
It's a simple 3 step process (I wont count [requirements](#requirements))
  1. [Setup your resinOS device](#setup-your-resinos-device)
  2. [Start the agile services](#start-the-agile-services)
  3. [Developing](#/developing)

*<b>important</b>: These instructions are for *NIX based systems. Windows will require some minor modifications, a PR for windows instructions will be greatly appreciated.*

### Requirements:
- Software:
  * [docker + docker-compose](https://docs.docker.com/compose/install/)
  * [Node.js + npm](https://nodejs.org/en/)
  * [resin CLI](https://www.npmjs.com/package/resin-cli)
  * [etcher](https://etcher.io/)
- Hardware:
  * Raspberry pi 2 or 3
  * SD card `>= 8gb`
  * Wifi dongle or ethernet cable *optional if you have the pi3*
  * Bluetooth dongle *optional if you have the pi3*

### Setup your resinOS device

* Download resinOS image from [resinos.io](https://resinos.io/#downloads-raspberrypi)

* Install the resin cli:
```
$ npm i -g resin-cli
```

* Configure the image
```
$ sudo resin local configure ~/Downloads/resin.img
? Network SSID super_wifi
? Network Key super secure password
? Do you want to set advanced settings? Yes
? Device Hostname resin
? Do you want to enable persistent logging? no
Done!
```

* Burn the image to an SD card. There are a variety of ways to do this. I suggest using [etcher](https://etcher.io/).

**Windows OS**: Before trying to find the Raspberry Pi device, the host computer network connection must be configured to enable network discovery of other devices. For Windows OS > 7 the "Private" profile should be OK. The "Public" networking profile will not allow network discovery so the resin.local device will not be found. These settings can be found in the Network and Sharing Center -> Change advanced sharing settings

* Boot the device and check if you can connect to it by running:
```
ping resin.local
```

* If ping fails try scanning the local network for the device:
```
sudo resin local scan
```

### Start the agile services
* First clone this repo on host machine:
```
git clone https://github.com/mohamed-elsabagh/agile-stack.git && cd agile-stack
```

* Clone the forked version of [agile-core](https://github.com/mohamed-elsabagh/agile-core), which has support to ModoSmart devices in a folder called apps, instead of using the pre-built image from Agile-IoT docker hub.
```
mkdir apps && cd apps
git clone https://github.com/mohamed-elsabagh/agile-core.git
git submodule init
git submodule update
cd ..
```

* Clone the [ModoSmart Service](https://github.com/mohamed-elsabagh/modosmart-agile) in the apps which will expose the service of modosmart to be consumed
```
git clone https://github.com/mohamed-elsabagh/modosmart-agile.git
```

* Deploy
```
docker-compose up
```

Once all containers are up and running the bluetooth should be disabled by running the next command from pi host OS (resin OS)
```
systemctl stop bluetooth
```
