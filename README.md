# memorandom

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
:r! ls -rla | tr -s ' ' | cut -d ' ' -f 9 | grep -v -e '^\.*$'
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
ffmpeg -i input.mp4 -vf "fps=12,scale=640:-1:flags=lanczos,palettegen=stats_mode=diff" -y palette.png
ffmpeg -i input.mp4 -i palette.png -lavfi "fps=12,scale=640:-1:flags=lanczos,paletteuse=dither=bayer:bayer_scale=5:diff_mode=rectangle" -y output.gif
```
See: https://cassidy.codes/blog/2017/04/25/ffmpeg-frames-to-gif-optimization/
See: https://superuser.com/questions/556029/how-do-i-convert-a-video-to-gif-using-ffmpeg-with-reasonable-quality
See: https://life.craftz.dog/entry/generating-a-beautiful-gif-from-a-video-with-ffmpeg

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
