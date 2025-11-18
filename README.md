# Interferences for YOU. CAN. JUST. DO. SHIT.

Collective Misnomer's 9th B-Day

## Ingredients

* Media File:
	* *Interferences 2012 @ IEA*
	* 2-channel 720 x 480 SD video edited into a single 1440 x 480 file
	* export h.264 5mbps
	* Stereo audio
* 3x RPi 3 B+
  * 1x Server
  * 2x Clients
* Network router w 1G ethernet ports
	* D-Link DSR-250

## Pi Setup

1. Flash cards with RaspberryPi Imager
  * enable SSH
  * don't setup wifi
  * username: `interferences`
  * setup network hostnames:
  		* interferences-server
  		* interferences-client-a
  		* interferences-client-b
1. Install dependencies:
  * Update and upgrade: `sudo apt update && sudo apt upgrade -y`
  * vlc: `sudo apt-get intstall vlc`

### vlc commands
* Server side: `cvlc --loop ./Interferences-YCJDS.mp4 --sout="#gather:rtp{sdp=rtsp://:8554/stream}" --network-caching=1500 --sout-all --sout-keep` [source](https://stackoverflow.com/questions/25648337/using-vlc-to-host-a-stream-of-an-infinite-video-loop)
* Client side: `cvlc --no-osd --align=2 --no-autoscale --deinterlace=1 --grayscale --play-and-exit --one-instance rtsp://interferences-server.local:8554/stream` [manual](https://wiki.videolan.org/VLC_command-line_help/)

### Installation: Server

1. From project directory run `rsync -vaPu . interferences-server:~/interferences` to copy files to the server.
1. Create links to the appropriate service file for the server: `sudo ln -s /home/interferences/interferences/interferences-server.service /lib/systemd/system/`
1. Reload the systemd daemon `sudo systemctl daemon-reload`
1. Start the service `sudo systemctl start interferences-server`
1. Enable the service `sudo systemctl enable interferences-server`

### Installation: Client

1. From project directory run `rsync -vaPu . interferences-client-<client_id>:~/interferences` to copy files to the clients where <client_id> matches your host naming convention.
1. Create links to the appropriate service file for the client: `sudo ln -s /home/interferences/interferences/interferences-client@.service /lib/systemd/system/`
1. Reload the systemd daemon `sudo systemctl daemon-reload`
1. Start the service `sudo systemctl start interferences-client@<align_value>` where `<align_value>` is 1 for left and 2 for right. This effectively selects between showing the left side of the composition.
1. Enable the service `sudo systemctl enable interferences-client@<align_value>`