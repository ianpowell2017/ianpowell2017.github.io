---
title: RaspberryPi NUT server with QNAP integration
author: ianpowell
date: 2021-08-14 13:23:00 +0100
categories: [raspberrypi]
tags: [nut-server]
---

References

https://www.howtoraspberry.com/2020/11/how-to-monitor-ups-with-raspberry-pi

https://www.howtoraspberry.com/2021/05/how-to-configure-an-orderly-shutdown/

https://blog.fosketts.net/2015/06/03/automated-ups-monitoring-for-vsphere-with-nut-and-raspberry-pi-cheap/

https://www.reddit.com/r/homelab/comments/5ssb5h/ups_server_on_raspberry_pi/

https://wiki.ledhed.net/index.php?title=Raspberry_Pi_NUT_Server

[For detailed NUT config and low battery warnings](https://wiki.ipfire.org/addons/nut/detailed)

---

Starting with a Raspberry Pi new image

```sudo raspi-config```

- enable ssh
- change hostname to `ups`

## Install Nut tools

``` shell
sudo apt-get update
sudo apt-get upgrade
sudo apt-get update && sudo apt-get install nut nut-client nut-server
```

Edit `/etc/nut/ups.conf` and add the snippet below to the end of the file

```
[qnapups]
driver = usbhid-ups
port = auto
desc = "CyberPower AVR UPS"
pollinterval = 15

override.battery.charge.low = 30
override.battery.charge.warning = 5
override.battery.runtime.low = 180
```

Edit `/etc/nut/upsd.conf` and add the snippet below to the end of the file

```
MAXAGE 25
```

Edit `/etc/nut/upsd.users` and add the snippet below to the end of the file

```
[admin]
password = 12345
upsmon master
instcmds = ALL
actions = SET
```

Edit `/etc/nut/upsmon.conf` and add the snippet below to the end of the file

```
LISTEN {current-ip-address} 3493

MONITOR qnapups@localhost 1 admin 123456 master
```

Also in the same file above set

```
DEADTIME 25
```

Edit `/etc/nut/nut.conf` and set MODE value

```
MODE=netserver
```

Reboot!

## Install Webserver

```
sudo apt install apache2 nut-cgi
```

Edit `/etc/nut/hosts.conf` and add the snippet below to the end of the file

```
MONITOR qnapups@localhost "Server UPS"
```

```
/etc/nut $ sudo a2enmod cgi
systemctl restart apache2
```

Edit `/etc/nut/upsset.conf` and uncomment (remove #)

```
### I_HAVE_SECURED_MY_CGI_DIRECTORY
```

Browse to

```
http://ups.localdomain/cgi-bin/nut/upsset.cgi
```

```
USR: admin
PWD: 123456
```

```
http://ups.localdomain/cgi-bin/nut/upsstats.cgi
```

### Qnap as UPS Slave to Raspberry Pi NUT server

https://forum.qnap.com/viewtopic.php?t=60166

> your upsname must be named qnapups, and user "admin" with password "123456" must be allowed to connect.The user/password pair can be changed manually on the QNAP. The UPS name can not be changed (at least I was not able to find way how to change it) but you can change the username/password manually in `/etc/config/ups/upsmon.conf`

### To generate data as JSON, exported as JSON object in webpage

*upsstats-single.html file contents*

```
<!-- upsstats template file -->

<!--
	This is upsstats-single.html, a template for monitoring a single
	host.  This mode is selected by adding "host=<host>" to the
	upsstats.cgi URL.

	Such URLs are generated automatically when using the HOSTLINK
	command.

	See upsstats.html(5) for more information on template files.
-->

<!-- change this to TEMPF if you don't like Celsius. -->

@TEMPC@
@UPSSTATSPATH upsstats.cgi@
@UPSIMAGEPATH upsimage.cgi@

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.0 Transitional//EN"
	"http://www.w3.org/TR/REC-html40/loose.dtd">
<html>

<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
@REFRESH@
<title>@HOSTDESC@ : @VAR ups.model@ on @HOST@</title>
<!-- LINK REL="stylesheet" TYPE="text/css" HREF="http://localhost/nut/nut.css" / -->
</head>
<body BGCOLOR="#808080" TEXT="#00FC00" LINK="#0000EE" VLINK="#551A8B">

<table BORDER="1" ALIGN="CENTER" CELLSPACING="0" CELLPADDING="10" BGCOLOR="#000000">

<tr>
<th COLSPAN="20">Network UPS Tools upsstats @VERSION@ - @HOSTDESC@ - @VAR ups.model@ on @HOST@</th>
</tr>

<tr>
<th>@DATE %a %b %d %X %Z %Y@</th>
@IFSUPP ambient.temperature@
<th>Ambient</th>
@ELSE@
@IFSUPP ambient.humidity@
<th>Ambient</th>
@ENDIF@
<th>Battery</th>
<th>Input</th>
<th>Output</th>
<th>Load</th>
</tr>

<tr>

<td BGCOLOR="#000000" VALIGN="TOP">

<table BORDER="0">	<!-- table 2 -->

<tr>
<th ALIGN="RIGHT">UPS Model:</th>
<td>@VAR ups.model@</td>
</tr>

<tr>
<th ALIGN="RIGHT">Status:</th>
<td>@STATUS@</td>
</tr>

@IFSUPP battery.runtime@
<tr>
<th ALIGN="RIGHT">Runtime:</th>
<td>@RUNTIME@</td>
</tr>
@ENDIF@

@IFSUPP ups.temperature@
<tr>
<th ALIGN="RIGHT">UPS temp:</th>
<td>@UPSTEMP@ @DEGREES@</td>
</tr>
@ENDIF@

@IFSUPP battery.voltage@
<tr>
<th ALIGN="RIGHT">Battery: </th>
<td>@VAR battery.voltage@ V@IFSUPP battery.current@, @VAR battery.current@ A</td>
@ENDIF@

<tr>
<th VALIGN="TOP" ALIGN="RIGHT">Input: </th>

<td>

@IFSUPP input.L2-L3.voltage@
@VAR input.L1-L2.voltage@ V<br>
@VAR input.L2-L3.voltage@ V<br>
@VAR input.L3-L1.voltage@ V<br>
</td>
</tr>
@ELSE@
@IFSUPP input.L2-N.voltage@
@VAR input.L1-N.voltage@ V<br>
@VAR input.L2-N.voltage@ V<br>
@VAR input.L3-N.voltage@ V<br>
</td>
</tr>
@ELSE@
@IFSUPP input.voltage@
@VAR input.voltage@ V<br>
</td>
</tr>
@ENDIF@

@IFSUPP input.L2.current@
<tr>
<th>
<td>
@VAR input.L1.current@ A<br>
@VAR input.L2.current@ A<br>
@VAR input.L3.current@ A<br>
</td>
</tr>
@ELSE@
@IFSUPP input.current@
<tr>
<th>
<td>
@VAR input.current@ A
</td>
</tr>
@ENDIF@

@IFSUPP input.frequency@
<tr>
<th>
<td>
@VAR input.frequency@ Hz
</td>
</tr>
@ENDIF@

<tr>
<th VALIGN="TOP" ALIGN="RIGHT">Output: </th>
<td>

@IFSUPP output.L2-L3.voltage@
@VAR output.L1-L2.voltage@ V<br>
@VAR output.L2-L3.voltage@ V<br>
@VAR output.L3-L1.voltage@ V<br>
</td>
</tr>
@ELSE@
@IFSUPP output.L2-N.voltage@
@VAR output.L1-N.voltage@ V<br>
@VAR output.L2-N.voltage@ V<br>
@VAR output.L3-N.voltage@ V<br>
</td>
</tr>
@ELSE@
@IFSUPP output.voltage@
@VAR output.voltage@  V
</td>
</tr>
@ENDIF@

@IFSUPP output.L2.current@
<tr>
<th>
<td>
@VAR output.L1.current@ A<br>
@VAR output.L2.current@ A<br>
@VAR output.L3.current@ A<br>
</td>
</tr>
@ELSE@
@IFSUPP output.current@
<tr>
<th>
<td>
@VAR output.current@ A
</td>
</tr>
@ENDIF@

@IFSUPP output.frequency@
<tr>
<th>
<td>
@VAR output.frequency@ Hz
</td>
</tr>
@ENDIF@

</table>		<!-- table 2 -->
</td>

@IFSUPP ambient.temperature@
@IFSUPP ambient.humidity@
<td ALIGN="CENTER" VALIGN="TOP" BGCOLOR="#000000">
<table BORDER="0"><tr>
<td ALIGN="CENTER">Temperature<br>@IMG ambient.temperature tempmin=0 tempmax=50 width=90@</td>
<td ALIGN="CENTER">Humidity<br>@IMG ambient.humidity width=90@</td>
</tr></table>
</td>
@ELSE@
@IFSUPP ambient.temperature@
<td ALIGN="CENTER" VALIGN="TOP" BGCOLOR="#000000">
<table BORDER="0"><tr>
<td ALIGN="CENTER">Temperature<br>@IMG ambient.temperature tempmin=0 tempmax=50@</td>
</tr></table>
</td>
@ELSE@
@IFSUPP ambient.humidity@
<td ALIGN="CENTER" VALIGN="TOP" BGCOLOR="#000000">
<table BORDER="0"><tr>
<td ALIGN="CENTER">Humidity<br>@IMG ambient.humidity@</td>
</tr></table>
</td>
@ENDIF@

<td ALIGN="CENTER" VALIGN="TOP" BGCOLOR="#000000">
<table BORDER="0"><tr>
@IFSUPP battery.charge@
@IFSUPP battery.voltage@
<td ALIGN="CENTER">Charge<br>@IMG battery.charge width=90@</td>
<td ALIGN="CENTER">Voltage<br>@IMG battery.voltage width=90@</td>
@ELSE@
@IFSUPP battery.charge@
<td ALIGN="CENTER">Charge<br>@IMG battery.charge@</td>
@ELSE@
<td ALIGN="CENTER">Voltage<br>@IMG battery.voltage@</td>
@ENDIF@
</tr></table>
</td>

<td ALIGN="CENTER" VALIGN="TOP" BGCOLOR="#000000">
<table BORDER="0"><tr>
@IFSUPP input.L2-L3.voltage@
<td ALIGN="CENTER">L1-L2<br>@IMG input.L1-L2.voltage width=68@</td>
<td ALIGN="CENTER">L2-L3<br>@IMG input.L2-L3.voltage width=68@</td>
<td ALIGN="CENTER">L3-L1<br>@IMG input.L3-L1.voltage width=68@</td>
@ELSE@
@IFSUPP input.L2-N.voltage@
<td ALIGN="CENTER">L1-N<br>@IMG input.L1-N.voltage width=68@</td>
<td ALIGN="CENTER">L2-N<br>@IMG input.L2-N.voltage width=68@</td>
<td ALIGN="CENTER">L3-N<br>@IMG input.L3-N.voltage width=68@</td>
@ELSE@
<td ALIGN="CENTER"><br>@IMG input.voltage@</td>
@ENDIF@
</tr></table>
</td>

<td ALIGN="CENTER" VALIGN="TOP" BGCOLOR="#000000">
<table BORDER="0"><tr>
@IFSUPP output.L2-L3.voltage@
<td ALIGN="CENTER">L1-L2<br>@IMG output.L1-L2.voltage width=68@</td>
<td ALIGN="CENTER">L2-L3<br>@IMG output.L2-L3.voltage width=68@</td>
<td ALIGN="CENTER">L3-L1<br>@IMG output.L3-L1.voltage width=68@</td>
@ELSE@
@IFSUPP output.L2-N.voltage@
<td ALIGN="CENTER">L1-N<br>@IMG output.L1-N.voltage width=68@</td>
<td ALIGN="CENTER">L2-N<br>@IMG output.L2-N.voltage width=68@</td>
<td ALIGN="CENTER">L3-N<br>@IMG output.L3-N.voltage width=68@</td>
@ELSE@
<td ALIGN="CENTER"><br>@IMG output.voltage@</td>
@ENDIF@
</tr></table>
</td>

<td ALIGN="CENTER" VALIGN="TOP" BGCOLOR="#000000">
<table BORDER="0"><tr>
@IFSUPP output.L2.power.percent@
<td ALIGN="CENTER">L1<br>@IMG output.L1.power.percent width=68@</td>
<td ALIGN="CENTER">L2<br>@IMG output.L2.power.percent width=68@</td>
<td ALIGN="CENTER">L3<br>@IMG output.L3.power.percent width=68@</td>
@ELSE@
@IFSUPP output.L2.realpower.percent@
<td ALIGN="CENTER">L1<br>@IMG output.L1.realpower.percent width=68@</td>
<td ALIGN="CENTER">L2<br>@IMG output.L2.realpower.percent width=68@</td>
<td ALIGN="CENTER">L3<br>@IMG output.L3.realpower.percent width=68@</td>
@ELSE@
<td ALIGN="CENTER"><br>@IMG ups.load@</td>
@ENDIF@
</tr></table>
</td>
</tr>
</table>
<script type="application/json">
{
	"Date": "@DATE %d %m %y %X %Z@",
	"battery" : {
		"chargePercent" : @VAR battery.charge@,
		"voltageDc" : @VAR battery.voltage@
	},
	"input" : {
		"voltage": @VAR input.voltage@
	},
	"output": {
		"voltageAc": @VAR output.voltage@
	},
	"ups": {
		"model": "@VAR ups.model@",
		"loadPercent": @VAR ups.load@,
		"runtime": "@RUNTIME@",
@IFEQ ups.status OL@		"status": "ONLINE"
@ELSE@@IFEQ ups.status OB@		"status": "BATTERY"
@ENDIF@	}
}
</script>
</body>
</html>
```