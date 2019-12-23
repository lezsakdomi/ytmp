# ytmp
A lightweight YouTube-based music player

## Setup
1. Install mpv on your OS (`sudo apt-get install mpv`, `sudo pacman -S mpv`, `sudo emerge mpv`).
   Sorry, `mpv` is an external dependency as of now. You can skip this step if
   you are into providing your own https-capable video player (like VLC or
   mplayer) using the `-p` flag
2. Run `yarn global add ytmp`

## CLI
A binary called `ytmp` is provided.
```
Usage:
    ytmp --help
    ytmp [ options ] [ [--play] [--mix] [--radio] ] [--] [string]
    ytmp [ options ] --search [--] [string...]
    ytmp [ options ] --id=id | --id [--] [id]
    ytmp [ options ] --url=url | --url [--] [url]

Behaviour control:
    -x, --play      Play the selected playlist and/or video
                    Used in playlists to exit after playling all the songs.
    -m, --mix       After the playlist or video is over start a
                    non-personalized YouTube Mix
    -r, --radio     At the end, don't exit but start an endless radio instead

    The default behaviour is dependent on the song selection

Song selection:
    -s, --search    Use the positional argument(s) to perform a search and use
                    the first result as the video or playlist
    -i, --id id     Use the given ID or URL (either video, playlist or channel)
                    The value can also be supplied either as positional, after
                    the double-dash
    -u, --url url   And alias for --id. Behaviour may change.
    
    By default if the supplied positional argument(s) is a valid playlist or
    video ID or URL then used so, else a search is performed.

Format options:
    -a, --audio     Audio-only, never ever show video
    -v, --video     Optimize for video.
    -av             Select formats with video but optimize for audio

Developer options:
    -p, --player p Use a custom audio and video player executable. "mpv" by
                   default. A recommended alternative is mplayer or ffplay.
                   Should be able to play http streams.
    --saveinfo [f] Save latest video info, optionally to the given file
    --quiet        Don't log to stdout
    -v, --verbose  Log to stderr
```

## API
```javascript
const ytmp = require(ytmp)

let info = await ytmp(url_or_id_or_searchstring, options)
info = await ytmp.play(url_or_id_or_searchstring_or_info, options)
info = await ytmp.playMix(info || "Developers" || "KMU0tzLwhbE", {audio: true, video: false})
info = ytmp.playRadio(info || "Oxygene COMPLETE" || "PLCFC37B95C9159ACC", {player: "C:\\Program Files\\vlc\\vlc.exe"})
```

### main function

The exported function is intelligent, i.e. does not accept an
_info-object_, but accepts a search string tries to guess what the user wants.

First argument:
- url (`String`)
- id (`String`)
- search string (`String`)

Second argument (optional):
- `Options`

Returns:
- [`Promise<ytdl.videoInfo>`](https://github.com/fent/node-ytdl-core/blob/master/typings/index.d.ts)
  This promise is resolved after the last video is played.
  By default the program tries playing an infinite stream thus the promise never resolves.

### Methods

List of exported methods:
- `ytmp.play`
- `ytmp.playRadio`
- `ytmp.playMix`

First argument:
- url (`String`)
- id (`String`)
- [`Promise<ytdl.videoInfo>`](https://github.com/fent/node-ytdl-core/blob/master/typings/index.d.ts)
- [`ytdl.videoInfo`](https://github.com/fent/node-ytdl-core/blob/master/typings/index.d.ts)

You are encouraged to pass an _info-promise_ because that
makes preloading possible in future versions.

Second argument (optional):
- `Options`

Returns:
Returns:
- [`Promise<ytdl.videoInfo>`](https://github.com/fent/node-ytdl-core/blob/master/typings/index.d.ts)
  This promise is resolved after the last video is played. (In case of playlist or mix)

### Options
Not really documented, but generally speaking the keys could be the arguments (see [CLI usage](#CLI) and/or the source code).

## Buzzwords

### Video
It could be a YouTube video, a YouTube playlist or even a channel.

### Radio
An infinite stream of videos, each one similar to the previous ones.
Similar to the YouTube AutoPlay feature except this is better.

### Mix
A list of recommended videos for a specific video. YouTube does the same
except it customizes the priority of the videos based on the user's habits.
