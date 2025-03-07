# ffmpeg

```bash
# show info
ffmpeg -i input.mp4

# concat
ffmpeg -safe 0 -f concat -i filelist.txt -c copy output.h264

# h264 to mp4
ffmpeg -r 10 -i output.h264 -c copy output.mp4

# check sps
ffmpeg -i file.h264 -c copy -bsf:v trace_headers -f null -
```
