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

| Device     | Name / Description                                                         |
| ---------- | -------------------------------------------------------------------------- |
| OS         | Windows 10 Pro (x64)                                                       |
| CPU        | Intel i7-10700K @ 3.8GHz                                                   |
| GPU        | NVIDIA GeForce GTX 1070 (ASUS ROG OC version)                              |
| Mainboard  | Asus ROG STRIX Z490-E Gaming                                               |
| PSU        | BeQuiet 750W ATX                                                           |
| RAM        | 48GB (2x8GB @ 3200C16 + 2x16GB @ 3000C15) DDR4 @ 2133MHz                   |
| Storage    | Samsung M.2 SSD 970 EVO for C:\\ and WD Black HDD for everything else      |
| Monitors   | 2x old EIZO FlexScan @ 60Hz vertical (1200px high - 16:10 and 4:3)         |
| Keyboard   | Full-sized Mechanical QWERTZ keyboard from ASUS ROG (GK2000 Horus RGB)     |
| Mouse      | Mini Wireless Mouse @ 3100P Invisible Optical (powered by 2x AA Batteries) |
| Speakers   | 2x JBL control one speaker and an amplifier (+ a Cinch to AUX converter)   |
| Headphones | AKG K-371 closed ear headphones (_rarely use it, but it's great_)          |

## Twitch Streaming & Video Recording

currently using [OBS Studio](https://obsproject.com/ "Official OBS website") version [30.1.0](https://github.com/obsproject/obs-studio/releases/tag/30.1.0 "Offical release on GitHub") (64 bit).

### Plugins

- [Input Overlay](https://obsproject.com/forum/resources/input-overlay.552/ "Official project page") with [my overlays](https://github.com/MAZ01001/obsGameInputOverlays "Free overlays drawn by me").
- [win-capture-audio](https://obsproject.com/forum/resources/win-capture-audio.1338/) to only capture the audio I need (all other sources are deactivated in settings).

### Output

Output Mode: Advanced

#### Output: Streaming

| Setting              | Value                                             |
| -------------------- | ------------------------------------------------- |
| Encoder              | NVIDIA NVENC H.264 ; CoreAudio AAC                |
| Scale output         | (Bicubic) 1664x936 (for better bitrate on Twitch) |
| Bitrate              | CBR @ 6000Kbps                                    |
| Keyframe Interval    | 2 sec.                                            |
| Preset               | P5 / Slow (Good Quality)                          |
| Tuning               | Low Latency                                       |
| Multipass Mode       | Single Pass                                       |
| Profile              | main                                              |
| Look-ahead           | OFF                                               |
| Psycho Visual Tuning | ON                                                |
| Max B-frames         | 2                                                 |

Also, see the [Twitch VOD Track Guide](https://obsproject.com/kb/twitch-vod-track-guide "OBS project: Twitch VOD Track Guide (2023-07-14)") for how to set up a separate audio track for VOD.

#### Output: Recording

| Setting              | Value                              |
| -------------------- | ---------------------------------- |
| Type                 | Standard                           |
| Format               | mkv (see why further below)        |
| Encoder              | NVIDIA NVENC H.264 ; CoreAudio AAC |
| Scale output         | no scaling (1920x1080)             |
| auto split files     | ON ; split by time ; 120 min.      |
| Compression          | CQP @ 4                            |
| Keyframe Interval    | 0 (auto)                           |
| Preset               | P7 / Slowest (Best Quality)        |
| Tuning               | High Quality                       |
| Multipass Mode       | Single Pass                        |
| Profile              | high                               |
| Look-ahead           | OFF                                |
| Psycho Visual Tuning | ON                                 |
| Max B-frames         | 2                                  |

#### Output: Audio

All tracks have bitrate set to 128; nothing else is configured.

### Audio

| Setting         | Value       |
| --------------- | ----------- |
| Sample Rate     | 48 kHz      |
| Channles        | Stereo      |
| Decay Rate      | Fast        |
| Peak Meter Type | Sample Peak |

I've also deactivated all global audio devices that I don't need.

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
| Color Space                          | sRGB ; Limited                                     |
| SDR White Level                      | 300 nits                                           |
| HDR Nominal Peak Level               | 1000 nits                                          |
| automatic remuxing to mp4            | OFF (see below)                                    |
| automatic reconnecting               | ON ; 2s ; 10x                                      |
| network optimizations                | ON                                                 |
| browser source hardware acceleration | ON                                                 |

## Manual remuxing to mp4

Record to MKV (easy to restore if something happens), then convert to MP4 via [FFmpeg](https://ffmpeg.org/ "Official FFmpeg website") as follows

```PowerShell
# (Audio codec already is AAC, so it can be copied to save some time)
# Using NVIDIA CUDA for faster encoding (with 4 CPU threads ; more is not necessarily faster)
# Compression strength (qp) 4 & Bitrate: average 8Mbits ; min 500Kbits ; max 16Mbits ; buffer 16Mbits for nearly no quality loss
ffmpeg.exe -v level+warning -stats -threads 4 -hwaccel cuda -hwaccel_output_format cuda -i INPUT.mkv -map 0 -c copy -c:v:0 h264_nvenc -preset p7 -tune hq -profile:v:0 high -level:v:0 auto -rc vbr -b:v:0 8M -minrate:v:0 500k -maxrate:v:0 16M -bufsize:v:0 16M -multipass disabled -fps_mode passthrough -b_ref_mode:v:0 disabled -rc-lookahead:v:0 32 -qp 4 OUTPUT.mp4
# Copies every stream except the first video stream, which is compressed and converted to mp4 via NVIDIA h264_nvenc (+CUDA hardware acceleration)
```

Then, it can be used in any video editing software (delete the MKV version only if you tested the MP4 version for errors first).

A simpler, more general command can be found [here](https://github.com/MAZ01001/other-projects/blob/main/ffmpeg.md#convert-mkv-to-mp4 "Description and links to documentation in my useful-FFmpeg-commands list here on GitHub").

_One could also add ` -metadata comment="VIDEO DESCRIPTION" -metadata title="VIDEO TITLE" ` (before the output) to insert some metadata into the MP4 file in one command._

also see my [list of useful FFmpeg commands](https://github.com/MAZ01001/other-projects/blob/main/ffmpeg.md "A list on my GitHub for some useful FFmpeg commands with descriptions") and the [official FFmpeg wiki](https://ffmpeg.org/ffmpeg-all.html "The official FFmpeg documentation") for some inspiration.
