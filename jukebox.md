---
layout: docs
title: Setting up jukebox player
permalink: /docs/jukebox/
---
Jukebox might not always work out-of-the-box and may require some additional tweaking. If you get no sound output while trying to play via the Jukebox, you might need to tweak the audio device being picked up by Java sound.

##### Finding device name

You can run the folowing Java program to get a list of all the audio devices in your system:

```java
import java.io.*;
import javax.sound.sampled.*;

public class audioDevList {
    public static void main(String args[]) {
        Mixer.Info[] mixerInfo =
            AudioSystem.getMixerInfo();
            System.out.println("Available mixers:");
            for(int cnt = 0; cnt < mixerInfo.length;cnt++) {
                System.out.println(mixerInfo[cnt].getName());
        }
    }
}
```

Sample output:
```
Available mixers:
Port HDMI [hw:0]
Port PCH [hw:1]
default [default]
HDMI [plughw:0,3]
HDMI [plughw:0,7]
HDMI [plughw:0,8]
HDMI [plughw:0,9]
HDMI [plughw:0,10]
PCH [plughw:1,0]
```

### Using sound.properties

You can then generate a `sound.properties` file accordingly with your devicename:

```
javax.sound.sampled.Clip=#PCH [plughw:1,0]
javax.sound.sampled.Port=#Port PCH [hw:1]
javax.sound.sampled.SourceDataLine=#PCH [plughw:1,0]
javax.sound.sampled.TargetDataLine=#PCH [plughw:1,0]
```

Copy the `sound.properties` file to `/usr/lib/jvm/java-1.8-openjdk/jre/lib/sound.properties`. Change `java-1.8-openjdk` depending on your java installation.

### Using Java parameters

You can pass the devicename as parameter into the launch script/service file:

```
-Djavax.sound.sampled.Clip=#PCH [plughw:1,0]
-Djavax.sound.sampled.Port=#Port PCH [hw:1]
-Djavax.sound.sampled.SourceDataLine=#PCH [plughw:1,0]
-Djavax.sound.sampled.TargetDataLine=#PCH [plughw:1,0]
```

### Using Docker

- Ensure that the docker user (passed through `--user` in the docker run) command has access to the `/dev/snd` device. Typically this can be done on most distros by adding the user to the `audio` group. You can alternatively use the `--group-add` flag to add the `audio` group the the user.
- Pass the `--device /dev/snd` argument for docker run. See the [docker documentation](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities) for more details.
- You can mount a copy of the previous `sound.properties` file to `/usr/lib/jvm/java-1.8-openjdk/jre/lib/sound.properties`
 inside the container.

All of the above might result in an invocation like the following:

```sh
docker run \
    -v /home/airsonic/music:/music \
    -v /home/airsonic/config:/config \
    -v /home/airsonic/podcasts:/podcasts \
    -v /home/airsonic/playlists:/playlists \
    --group-add audio \
    --device /dev/snd \
    -v /home/airsonic/sound.properties:/usr/lib/jvm/java-1.8-openjdk/jre/lib/sound.properties \
    -p 4040:4040 \
    airsonic/airsonic
```

### Use Jukebox with Pulseaudio
The point of this configuration is to force pulseaudio to use mixed ALSA output `alsa_output.dmix` (if it's available in your system). To check what sink is being used use `pactl list sinks`.


- Configure java machine as stated above to get Jukebox working.
- Configure pulseaudio alsa module to use dmix device by default (remember to edit an apropriate `*.pa` file, `/etc/pulse/default.pa` if your pulseaudio instance is being autospawn by clients or `/etc/pulse/system.pa` if you run pulseaudio in system mode):

    ```
       load-module module-alsa-sink device=dmix
       load-module module-alsa-source device=snoop
    ```
- Configure pulseaudio to use dmix output by default.


    ```sh
    set-default-sink asla_output.dmix
    ```

