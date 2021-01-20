ffmpeg Commands
===============

Merge Multiple Files
--------------------

```bash
mylist.txt:
    file '/path/to/file1.wav'
    file '/path/to/file2.wav'
    file '/path/to/file3.wav'

ffmpeg -f concat -i mylist.txt -c copy output.mp4
```

Extracting Video Clips
----------------------

Extract from 0seconds to 3minutes 45seconds

```bash
ffmpeg -ss 00:00:00.0 -t 00:03:45.0 -i input.MP4 -c copy output.mp4
```

Extract from 2minutes 18seconds to 14minutes 2seconds

```bash
ffmpeg -i input.mp4 -ss 00:02:18.0 -to 00:14:02.0 -c copy output.mp4
```

Lower Resolution
----------------

- 480p is 640:480
- 720p is 1270:720
- 1080p is 1920:1080

```bash
ffmpeg -i input.mp4 -vf scale=640:480 output.mp4
```

Flip or Reverse Video
---------------------

```bash
ffmpeg -i input.mp4 -vf "hflip,vflip,reverse" output.mp4
```

Warning, `reverse` require a ton of ram. It eats up all 32GB that 
I currently have and crashes ffmpeg on a 10minute long clip.

The only technique I've come up with is to clip the file into multiple 
chunks, reverse each chunk, then rejoin them with concat.
