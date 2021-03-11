# memorandom

## Vim

#### TBD

## Git

#### check the differences and status with a certain branch
```
git diff [TARGET_BRANCH] --name-status
```

#### checkout the differences from a certain branch
```
git checkout [TARGET_BRANCH] -- `git diff --diff-filter=[FILTER] [TARGET_BRANCH] --name-only`
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
ffmpeg -i input.mp4 -vf scale=800:-1 -r 16.667 output.fig
```
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
