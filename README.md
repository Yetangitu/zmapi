[ mirror only, development is done on my own git server ]

# zmapi

A simple, shell (bash) based ZoneMinder API CLI which, combined with a camera control (cam) and watchdog (camwatch) can be used to keep a ZoneMinder installation up and running without having to deal with those clunky web interfaces.

The ZoneMinder API CLI script (`zmapi`) is independent of the camera control scripts (`cam` and `camwatch`) and each can be used by itself. The camera-related scripts are specific for (XiongMai-based) cameras using the 'NetSDK' API and will need to be adapted to other camera types.

## Installation

Copy the scripts (`zmapi`, `cam` and `camwatch`) to a directory in the PATH. Either copy and edit the configuration files (`zmapi.conf`, `cam.conf` and `camwatch.conf`) to the `$HOME/.config/zmapi` directory for the user who will executre these scripts. If the configuration files are not present on first run the scripts will create them with default values on the first run and abort, in that case edit the created files before continuing.

Run `zmapi` and `cam` without parameters for an overview of the available commands, alternatively have a look at the source for more insight. The available commands are defined in the API (`zmapi`) or SDK (`cam`) arrays, this is also where new commands can be added if new API endpoints are discovered.

## Examples

### ZoneMinder

Show all monitors:

```$ zmapi monitors
...
...lots of JSON output...
```

Show monitor #1:

```$ zmapi monitor 1
...
...lots of JSON output...
```

Show monitor #1 status:

```$ zmapi status 1
{
  "MonitorId": "1",
  "Status": "Connected",
  "CaptureFPS": "10.00",
  "AnalysisFPS": "10.00",
  "CaptureBandwidth": "234455"
}
```

Show daemon status for monitor #1 (default shows `zmc` daemon status):

```$ zmapi daemonstatus 1
{
  "status": true,
  "statustext": "'zmc -m 1' running since 21/04/09 01:06:46, pid = 12303"
}
```

Show `zma` status for that monitor:

```$ zmapi daemonstatus 1 zma
{
  "status": true,
  "statustext": "'zma -m 1' running since 21/04/09 01:06:52, pid = 12314"
}
```

...etc

### Camera control

Show the time according to camera number 1:

```$ cam 1 time
time CAM1 (192.168.5.10): "2021-04-09T15:02:48+02:00"
```

Set the time on all cameras to the current time:

```$ cam all time $(date -Iseconds)
$ cam all time $(date -Iseconds)
time CAM1 (192.168.5.10): "OK"
time CAM2 (192.168.5.11): "OK"
time CAM3 (192.168.5.12): "OK"
time CAM4 (192.168.5.13): "OK"
time CAM5 (192.168.5.14): "OK"
time CAM6 (192.168.5.15): "OK"
time CAM7 (192.168.5.16): "OK"
```

Reboot camera #7:

```$ cam 7 reboot
reboot CAM7 192.168.5.16): "OK"
```

Show video encoding configuration for camera #1:

```$ cam 1 vencode_main
vencode_main CAM1 (192.168.5.10): {
  "id": 101,
  "enabled": true,
  "videoInputChannelID": 101,
  "codecType": "H.264",
  "h264Profile": "high",
  "freeResolution": false,
  "channelName": "CAM1",
  "bitRateControlType": "VBR",
  "resolution": "1920x1080",
  "constantBitRate": 2048,
  "frameRate": 10,
  "keyFrameInterval": 50,
  "expandChannelNameOverlay": [
    {
      "expandChannelName": "cam1",
      "id": 1,
      "enabled": false,
      "regionX": 0,
      "regionY": 0
    },
    {
      "expandChannelName": "",
      "id": 2,
      "enabled": false,
      "regionX": 0,
      "regionY": 0
    },
    {
      "expandChannelName": "",
      "id": 3,
      "enabled": false,
      "regionX": 0,
      "regionY": 0
    },
    {
      "expandChannelName": "",
      "id": 4,
      "enabled": false,
      "regionX": 0,
      "regionY": 0
    }
  ],
  "channelNameOverlay": {
    "enabled": true,
    "regionX": 0,
    "regionY": 0
  },
  "datetimeOverlay": {
    "enabled": true,
    "regionX": 1,
    "regionY": 1,
    "dateFormat": "YYYY/MM/DD",
    "timeFormat": 24,
    "displayWeek": true,
    "displayChinese": false
  },
  "deviceIDOverlay": {
    "enabled": false,
    "regionX": 0,
    "regionY": 0
  },
  "textOverlays": ""
}
```

Show which video encoding parameters can be changed on camera #1:
``` cam 1 vencode_main -H
Available parameters for vencode_main:

	-F	freeResolution (type b)
	-t	bitRateControlType (type s)
	-r	resolution (type s)
	-p	h264Profile (type s)
	-n	channelName (type s)
	-k	keyFrameInterval (type i)
	-f	frameRate (type i)
	-e	enabled (type b)
	-c	codecType (type s)
	-b	constantBitrate (type i)
	```

Set the channelName (the name shown in the bottom-right of the image) for camera #2 to *WATCHTOWER*:

``` cam 2 vencode_main -n WATCHTOWER
vencode_main CAM2 (192.168.5.11): "OK"
```

...etc

### camwatch

Use the `camwatch` script to reboot cameras which have stopped producing valid output. The script is best run as a cron job, once a minute or less often.

