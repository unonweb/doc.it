# ffmpeg

record, convert and stream audio and video

### conversion

```sh
ffmpeg -i input.mp3 output.ogg
ffmpeg -i input.mp4 output.avi
ffmpeg -i input.mp4 output.webm # Because WebM is a well-defined format, FFmpeg automatically knows what video and audio it can support and will convert the streams to be a valid WebM file.

ffmpeg -i input.mkv output.gif
```

```sh
ffmpeg -i input.mp3 -c:a libvorbis output.ogg # set the audio stream to be Vorbis
ffmpeg -i input.webm -c:v copy -c:a flac output.mkv # copies the video stream from input.webm into output.mkv and encodes the Vorbis audio stream into a FLAC

ffmpeg -i input.webm -c:av copy output.mkv # convert from one container format to another without having to do any additional stream encoding
```

### quality

```sh
ffmpeg -i input.webm -c:a copy -c:v vp9 -b:v 1M output.mkv # change the bitrate (simplest method)
```

### cut / extract

```sh
ffmpeg -ss <start> -to <end> -i <input-file> -c <codec> clip.webm
ffmpeg -ss 00:00:09 -to 00:00:12 -i app-platform.webm -c copy clip.webm
# -ss -to cut from a starting position to an ending position in the clip
# -i input file
# -c <codec> is where you would normally specify an output audio or video codec
ffmpeg -c copy # using 'copy' in lieu of a codec skips reencoding the video at all, which can be much quicker and avoid any loss in quality, as long as you don’t need to target a different output format. 
```

```sh
ffmpeg -i <input-file> -ss <hh:mm:ss> -vframes 1 frame_out.jpg
# -vframes <frames> set the number of frames to extract
```
