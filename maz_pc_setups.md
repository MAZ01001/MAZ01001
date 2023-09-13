# MAZ PC setups

- [PC specs](#pc-specs)
- [Twitch Streaming & Video Recording](#twitch-streaming--video-recording)
  - [Plugins](#plugins)
  - [Output](#output)
    - [Output: Streaming](#output-streaming)
    - [Output: Recording](#output-recording)
    - [Output: Audio](#output-audio)
  - [Audio](#audio)
  - [Video](#video)
  - [Advanced](#advanced)
- [Manual remuxing to mp4](#manual-remuxing-to-mp4)

## PC specs

No overclocking and BeQuiet case + air cooling

| Device     | Name / Description                                                                       |
| ---------- | ---------------------------------------------------------------------------------------- |
| OS         | Windows 10 Pro (x64)                                                                     |
| CPU        | Intel i7-10700K @ 3.8GHz                                                                 |
| GPU        | NVIDIA GeForce GTX 1070 (ASUS ROG OC version)                                            |
| Mainboard  | Asus ROG STRIX Z490-E Gaming                                                             |
| PSU        | BeQuiet 750W ATX                                                                         |
| RAM        | 48GB (2x8GB @ 3200C16 + 2x16GB @ 3000C15) DDR4 @ 2133MHz                                 |
| Storage    | Samsung M.2 SSD 970 EVO for C:/ and WD Black HDD for everything else                     |
| Monitors   | 2 old EIZO FlexScan @ 60Hz vertical (1200px high - 16:10 and 4:3)                        |
| Keyboard   | Full sized Mechanical QWERTZ keyboard from ASUS ROG (GK2000 Horus RGB)                   |
| Mouse      | Mini Wireless Mouse @ 3100P Invisible Optical (powered by 2 AA Batteries)                |
| Speakers   | _the classic_ 2 JBL control one speakers and an amplifier (and a Cinch to AUX converter) |
| Headphones | AKG K-371 closed ear headphones (_rarely use it, but it's great_)                        |

## Twitch Streaming & Video Recording

currently using [OBS Studio](https://obsproject.com/ "Official OBS website") version [28.0.3](https://github.com/obsproject/obs-studio/releases/tag/28.0.3 "Offical release on GitHub").

### Plugins

- [Input Overlay](https://obsproject.com/forum/resources/input-overlay.552/ "Official project page") with [my overlays](https://github.com/MAZ01001/obsGameInputOverlays "Free overlays drawn by me").
- [win-capture-audio](https://obsproject.com/forum/resources/win-capture-audio.1338/) to only capture the audio I need (all other sources are deactivated in settings).

### Output

Output Mode: Advanced

#### Output: Streaming

| Setting              | Value                                   |
| -------------------- | --------------------------------------- |
| Encoder              | NVIDIA NVENC H.264                      |
| Scale output         | 1664x936 (for better bitrate on Twitch) |
| Bitrate              | CBR @ 6000Kbps                          |
| Keyframe Interval    | 2 sec.                                  |
| Preset               | Quality                                 |
| Profile              | main                                    |
| Look-ahead           | OFF                                     |
| Psycho Visual Tuning | ON                                      |
| Max B-frames         | 2                                       |

#### Output: Recording

| Setting              | Value                       |
| -------------------- | --------------------------- |
| Type                 | Standard                    |
| Format               | mkv (see further below why) |
| Encoder              | NVIDIA NVENC HEVC           |
| Scale output         | no scaling (1920x1080)      |
| auto split files     | after 120 min.              |
| Compression          | CQP @ 10                    |
| Keyframe Interval    | 0 (auto)                    |
| Preset               | Quality                     |
| Profile              | main                        |
| Look-ahead           | OFF                         |
| Psycho Visual Tuning | ON                          |
| Max B-frames         | 0                           |

#### Output: Audio

all tracks have bitrate set to 128, nothing else is configured

### Audio

| Setting         | Value       |
| --------------- | ----------- |
| Sample Rate     | 48 kHz      |
| Channles        | Stereo      |
| Decay Rate      | Fast        |
| Peak Meter Type | Sample Peak |

I've also disabled all global audio devices that I don't need.

### Video

| Setting           | Value            |
| ----------------- | ---------------- |
| Resolutions       | 1920x1080 (16:9) |
| Common FPS Values | 60               |

### Advanced

| Setting                              | Value                                              |
| ------------------------------------ | -------------------------------------------------- |
| Process Priority                     | Above Normal (and always run OBS as Administrator) |
| Renderer                             | Direct3D 11                                        |
| Color Format                         | NV12 (8-bit, 4:2:0, 2 planes)                      |
| Color Space                          | sRGB - Limited                                     |
| SDR White Level                      | 300 nits                                           |
| HDR Nominal Peak Level               | 1000 nits                                          |
| automatic remuxing to mp4            | OFF (see below)                                    |
| browser source hardware acceleration | ON                                                 |

## Manual remuxing to mp4

Record to MKV (easier to restore if something happens) then convert to MP4 via [FFmpeg](https://ffmpeg.org/ "Official FFmpeg website") as follows

```shell
# Audio codec already is AAC, so it can be copied to save some time
ffmpeg -i INPUT.mkv -c:a copy -c:v libx264 -crf 12 OUTPUT.mp4
```

then it can be used in any video editing software (delete the MKV version only if you tested the MP4 version for errors first).

_one could also add ` -metadata comment="VIDEO DESCRIPTION" -metadata title="VIDEO TITLE" `, to insert some metadata into the MP4 file_ \
see [FFmpeg wiki](https://ffmpeg.org/ffmpeg-all.html "Official FFmpeg documentation") for some inspiration.
