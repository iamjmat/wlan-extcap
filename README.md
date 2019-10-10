# wifi-extcap
Wireshark extcap interface for remote Wi-Fi captures. It allows you to perform live remote captures on a specific channel and channel width using a Linux device's Wi-Fi adapter.

This extcap interface is basically a wrapper for sshdump that includes an option to choose the channel we want to capture on. It also simplifies the configuration of the extcap interface so that the user doesn't have to deal with remote capture commands, etc.

## Setup
Create the file `/etc/sudoers.d/wifidump` with the following content:
```sh
<username> ALL = (root) NOPASSWD: /sbin/ifconfig, /sbin/iwconfig, /usr/sbin/iw
```

Where `<username>` is the SSH username used for connecting remotely.

> __Note__: This is required so that the extcap interface can put the Wi-Fi adapter into monitor mode and change the channel before starting the capture.

### If you're running Wireshark on macOS:
1. Copy `wifidump` to `/Applications/Wireshark.app/Contents/MacOS/extcap/`
2. Make sure it has execution permissions:

```sh
chmod +x /Applications/Wireshark.app/Contents/MacOS/extcap/wifidump
```

### If you're running Wireshark on Windows:

1. Copy `wifidump` to `C:\Program Files\Wireshark\extcap\`
2. Create a file called `wlanpidump.bat` in the same `C:\Program Files\Wireshark\extcap\` directory with the following content: 

```bat
@echo off
<PATH_TO_PYTHON_INTERPRETER> <PATH_TO_WIFIDUMP> %*
```

Where `<PATH_TO_PYTHON_INTERPRETER>` is the path to the Python executable and `<PATH_TO_WLANPIDUMP>` is the path to the `wifidump` extcap interface script. For example:

```bat
@echo off
"C:\Program Files (x86)\Python37-32\python.exe" "C:\Program Files\Wireshark\extcap\wifidump" %*
```

Now launch Wireshark and verify that __Wi-Fi remote capture__ is listed as an extcap interface:

![Wi-Fi Extcap Interface](../master/images/wifidump-extcap-interface.png "Wi-Fi Extcap Interface")

> __Note__: You will have to repeat the setup of the `wifidump` extcap interface on your computer each time you update Wireshark. The Wireshark installer doesn't preserve 3rd-party extcap interfaces added to the _extcap_ folder.

## Usage

1. Click the _gear_ icon next to "Wi-Fi remote capture" to display the interface options, then choose the channel (and channel width) you want to capture on:

![Wi-Fi Extcap Interface Options](../master/images/wifidump-interface-options.png "Wi-Fi Extcap Interface Options")

> __Note:__ All 802.11 channels are listed, however, the Wi-Fi adapter on the remote device may support only a subset of them. If you choose a channel that is not supported by the Wi-Fi adapter or a channel width that doesn't apply to the selected channel, the capture will fail.
2. Go to the _Server_ tab and enter the remote SSH server address, e.g. 192.168.42.1.

<p align="center">
<img src="../master/images/wifidump-interface-options-server.png" alt="Wi-Fi Extcap Interface Options - Server" height="200px">
</p>

3. Go to the _Authentication_ tab and enter the username and password.

<p align="center">
<img src="../master/images/wifidump-interface-options-auth.png" alt="Wi-Fi Extcap Interface Options - Auth" height="200px">
</p>

> __Note:__ The password is not saved, so to avoid having to enter the password each time you start a capture, I would recommend you setup passwordless SSH authentication.

4. Click the __Start__ button to start the capture.