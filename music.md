# Broadcast music over HTTP

```shell
# Broadcast through VLC
# https://superuser.com/questions/605445/how-to-stream-my-gnu-linux-audio-output-to-android-devices-over-wi-fi
$ cvlc -vvv pulse://$(pactl list | grep "Monitor Source" | awk '{print $3}') --sout '#transcode{acodec=mp3,ab=128,channels=2}:standard{access=http,dst=0.0.0.0:8888/pc.mp3}'
```

Stream is then accessible at http://<SERVER_IP>:8888/pc.mp3

Usecase : 
- Linux server `->(wifi)` smartphone `->(bluetooth)` speaker
