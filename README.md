# memorandom

## Shell
#### remove tarminal color (will cause garbled text) on Mac
```
COMMAND | sed -E "s/"$'\E'"\[([0-9]{1,2}(;[0-9]{1,2})*)?m//g" > FILE_NAME
```

#### rename files sequentially
```
\ls | sort -n | awk '{ ext=$0; gsub(/^.*\./, "", ext); printf "mv %s %03d.%s\n", $0, NR, ext }' | sh
# \ls -tr | awk ... is also useful.
\ls | sort -n | awk -F'.' '{ printf "mv %s %03d.%s\n", $0, NR, $2}' | sh
```

## Vim

#### highlight the pattern during the typing
```
:set is hls[hlsearch]
```

#### extract Amazon's link
```
:%s/\(amazon.co.jp\/\).*\/\(dp\/[0-9A-Za-z]\+\/\).*/\1\2/ge 
```

#### rename all files sequentially
##### sort in order of the filenames
```
:put! =expand('*') | g/^$/d | %s/\(.*\)\.\(.*\)/\='mv ' . submatch(0) . ' ' . printf('%03d', line('.')) . '.' . submatch(2)/ge | noh
:w !sh    # unix
:w !cmd   # windows
```

##### sort in order of the timeline
```
# ! execute shell command(s) in vim and r! put the result on vim
:r! ls -rlat | tr -s ' ' | cut -d ' ' -f 9 | grep -v -e '^\.*$'
:g/^$/d | %s/\(.*\)\.\(.*\)/\='mv ' . submatch(0) . ' ' . printf('%03d', line('.')) . '.' . submatch(2)/ge | noh
```

## Git

#### check the differences and status with a certain branch
```
git diff [TARGET_BRANCH] --name-status
```

#### checkout the differences from a certain branch
```
git checkout [TARGET_BRANCH] -- `git diff --diff-filter=[FILTER] [TARGET_BRANCH] --name-only`
```

#### show the list of branches that are deleted on remote but exist on local
```
git remote prune --dry-run origin
```
and delete them via the following command.
```
git remote prune origin
```

#### show remote branches that are merged/unmerged
```
git branch -v -r --no-merged
git branch -v -r --merged origin/[TARGET_BRANCH]
```

if you want to delete branches that are already merged into the master/main branch, you can delete all of them via the following command.
```
git branch --merged | grep -v -e master | xargs -I{} git branch -d {}
git branch -r --merged origin/master | grep -v -e master  | sed -e 's/origin\///g' | xargs -I{} git branch -d {}
```

## Convert data in command line

#### encrypt with RSA
```
openssl rsautl -encrypt -pubin -oaep -inkey public.pem -in passwd.txt -out passwd.encrypted
```
#### encrypt with base64
```
base64 passwd.encrypted
```
#### encrypt with RSA and base64 in one-liner
```
echo -n "password1" | openssl rsautl -encrypt -pubin -oaep -inkey public.pem | base64
```

## ffmpeg

#### convert a movie file to a gif file
```
ffmpeg -i input.mp4 -vf scale=800:-1 -r 16.667 output.gif
```
##### Optimizing
```
filters="fps=12,scale=640:-1:flags=lanczos"
ffmpeg -i input.mp4 -vf "$filters,palettegen=stats_mode=diff" -y palette.png
ffmpeg -i input.mp4 -i palette.png -lavfi "$filters,paletteuse=dither=bayer:bayer_scale=5:diff_mode=rectangle" -y output.gif

# one liner
i="input.mp4"; p="palette.png"; o="output.gif"; f="fps=12,scale=1080:-1:flags=lanczos" && ffmpeg -i $i -vf "$f,palettegen=stats_mode=diff" -y $p && ffmpeg -i $i -i $p -lavfi "$filters,paletteuse=dither=bayer:bayer_scale=5:diff_mode=rectangle" -y $o

# n times speed (1.5x, 2.0x, 4.0x)
i="input.mp4"; p="palette.png"; o="output.gif"; r=4.0; f="fps=12,scale=640:-1:flags=lanczos,setpts=PTS/$r"; ffmpeg -i $i -vf "$f,palettegen=stats_mode=diff" -af atempo=$r -y $p && ffmpeg -i $i -i $p -lavfi "$f,setpts=PTS/$r,paletteuse=dither=bayer:bayer_scale=5:diff_mode=rectangle" -af atempo=$r -y $o
```
According to [the doc](https://ffmpeg.org/ffmpeg.html), The `-lavfi` option is equivalent to -filter_complex.

See: [Reference1](https://cassidy.codes/blog/2017/04/25/ffmpeg-frames-to-gif-optimization/), [Reference2](https://superuser.com/questions/556029/how-do-i-convert-a-video-to-gif-using-ffmpeg-with-reasonable-quality), [Reference3](https://life.craftz.dog/entry/generating-a-beautiful-gif-from-a-video-with-ffmpeg)

#### trim a specific scene from a movie without any deteriorations
```
ffmpeg --ss [START_TIME e.g. 00:00:30] -i input.mp4 -t [DURATION e.g. 00:01:00 -vcodec copy -acodec copy -async 1 output.mp4
```


## VSCode

#### export installed extensions
```
code --list-extensions | xargs -L 1 echo code --install-extension
```
#### import extensions
```
code --install-extension [EXTENSION_ID]
```
