+++
author = "Guowei Lv"
categories = ["Other"]
description = ""
linktitle = ""
featured = ""
featuredpath = ""
featuredalt = ""
title = "How to Fix Thinkpad X1 Carbon Headphone Clipping Noise"
date = 2020-03-21T00:22:17+02:00
+++

Just got the Thinkpad X1 Carbon Gen 7 as work laptop. Installed manjaro and noticed that there is a clipping noise when using the headphone jack.

How to fix:

1. Install `alsa-tools`
2. Create a script in `/usr/bin`
{{< highlight sh >}}
#!/bin/bash
hda-verb /dev/snd/hwC0D0 0x20 SET_COEF_INDEX 0x67
hda-verb /dev/snd/hwC0D0 0x20 SET_PROC_COEF 0x3000
{{< /highlight >}}

3. Create a new file in `/etc/systemd/system/`

{{< highlight sh >}}
[Unit]
Description=Script

[Service]
ExecStart=/usr/bin/script

[Install]
WantedBy=multi-user.target 
{{< /highlight >}}

4. Run and auto start the service
{{< highlight sh >}}
sudo chmod 755 /usr/bin/script
sudo systemctl enable script.service
{{< /highlight >}}
