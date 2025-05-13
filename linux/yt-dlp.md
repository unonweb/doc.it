

* https://github.com/yt-dlp/yt-dlp

```sh
pip install -U yt-dlp
```

# CONF

```sh
nano ~/yt-dlp/config
```

# CLI

## audio

```sh
yt-dlp -x
yt-dlp --extract-audio # Convert video files to audio-only files (requires ffmpeg and ffprobe)

yt-dlp --audio-quality QUALITY # Specify ffmpeg audio quality to use when converting the audio with -x. Insert a value between 0 (best) and 10 (worst) for VBR or a specific bitrate like 128K (default 5)

yt-dlp --audio-format FORMAT # Format to convert the audio to when -x is used. (currently supported: best (default), aac, alac, flac, m4a, mp3, opus, vorbis, wav).
```

## playlist

```sh
yt-dlp --no-playlist # Download only the video, if the URL refers to a video and a playlist
yt-dlp --yes-playlist # Download the playlist, if the URL refers to a video and a playlist
```

```sh
yt-dlp --embed-metadata # Embed metadata to the video file.
```

```sh
yt-dlp --print [WHEN:]TEMPLATE # Field name or output template to print to screen; Implies --simulate
```

## output

* https://github.com/yt-dlp/yt-dlp?tab=readme-ov-file#output-template

```sh
yt-dlp -o
yt-dlp --output [TYPES:]TEMPLATE # Output filename template
```

```sh
# Download all playlists of YouTube channel/user keeping each playlist in separate directory:
yt-dlp -o "%(uploader)s/%(playlist)s/%(playlist_index)s - %(title)s.%(ext)s" "https://www.youtube.com/user/TheLinuxFoundation/playlists"
```



## format selection

By default, yt-dlp tries to download the best available quality if you **don't** pass any options. 

This is generally equivalent to using `-f bestvideo*+bestaudio/best`

```sh
yt-dlp -f
yt-dlp --format <format> # Video Format Options
```

