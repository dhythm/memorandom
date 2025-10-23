# memorandom

## Shell
#### remove tarminal color (will cause garbled text) on Mac
```sh
COMMAND | sed -E "s/"$'\E'"\[([0-9]{1,2}(;[0-9]{1,2})*)?m//g" > FILE_NAME
COMMAND | sed -r 's/\x1b\[[0-9;]*m//g' | tee FILE_NAME 
```

#### remove files
```sh
find . -name "*.py" -path "*/migrations/*" ! -name "__init__.py" | xargs rm -f
```

#### concurrently mkdir and touch
```sh
sh -c 'mkdir -p "$(dirname "$0")" && touch "$0"' src/aaa/bbb/ccc.ts
```

#### rename files sequentially
```sh
\ls | sort -n | awk '{ ext=$0; gsub(/^.*\./, "", ext); printf "mv %s %03d.%s\n", $0, NR, ext }' | sh
# \ls -tr | awk ... is also useful.
\ls | sort -n | awk -F'.' '{ printf "mv %s %03d.%s\n", $0, NR, $2}' | sh
\ls | sort -n | awk -F'.' '{ printf "mv %s %03d.%s\n", $0, NR+20, $2}' | sh
```

#### search TEXT recursively in DIR
```sh
grep -rl TEXT DIR
```

#### check directory size
```sh
du -sh ~/Library/*
du -h -d 1 -c -x ~/Library | sort -h
```

## Vim

#### create a new directory of a new file if not exist

```
:!mkdir -p %:h
```

#### highlight the pattern during the typing

```
:set is hls[hlsearch]
```

#### extract Amazon's link

```
:%s/\(amazon.co.jp\/\).*\/\(dp\/[0-9A-Za-z]\+\/\).*/\1\2/ge 
```

#### remove color codes

```
:%s/\%x1b\[[0-9;]*m//g
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

#### insert UUID

insert UUID by ex command.
```
:r !uuidgen|sed 's/.*/    "pk": "&",/'|tr "[A-Z]" "[a-z]"
```

insert UUID to `:substitute`
```
:%s/\(.*\)\t\(.*\)/\='{"pk":"' . system("uuidgen | tr -d '\n' | tr '[A-Z]' '[a-z]'") . '","fields":{"code":"' . submatch(1) . '","name":"' . submatch(2) . '"}},'
```

#### replace case

convert snake_case to camelCase
```
'%s/_\([a-z]\)/\u\1/ge
'<,'>s/_\([a-z]\)/\u\1/ge
```

## Git

#### check the differences and status with a certain branch
```sh
git diff [TARGET_BRANCH] --name-status
```

#### checkout the differences from a certain branch
```sh
git checkout [TARGET_BRANCH] -- `git diff --diff-filter=[FILTER] [TARGET_BRANCH] --name-only`
```

#### show the list of branches that are deleted on remote but exist on local
```sh
git remote prune --dry-run origin
```
and delete them via the following command.
```sh
git remote prune origin
```

#### show remote branches that are merged/unmerged
```sh
git branch -v -r --no-merged
git branch -v -r --merged origin/[TARGET_BRANCH]
```

if you want to delete branches that are already merged into the master/main branch, you can delete all of them via the following command.
```sh
git branch --merged | grep -v -e master | xargs -I{} git branch -d {}
git branch -r --merged origin/master | grep -v -e master  | sed -e 's/origin\///g' | xargs -I{} git branch -d {}
```

#### get/delete all local branched that exist on remote
```sh
for branch in `git branch`; do git branch -r | sed -e 's;origin/;;g' | grep -ow "$branch" | uniq ; done
```

```sh
diff --changed-group-format='%<' --unchanged-group-format='' <(git branch | sed -e 's/^[ \*]*//') <(for branch in `git branch`; do git branch -r | sed -e 's;origin/;;g' | grep -ow "$branch" | uniq ; done) | xargs -I{} git branch -d {}
# or
diff --changed-group-format='%<' --unchanged-group-format='' <(git branch | sed -e 's/^[ \*]*//') <(git branch -r | sed -e 's;^.*origin/;;g') | xargs -I{} git branch -d {}
```
`<(...)` is called process substitution. It converts the output of a command into a file-like object that diff can read from.<br>
(`` `...` `` is called command substitution.)

Unfotunatelly, Apple diff doesn't have `--unchanged-group-format` option.
https://github.com/NixOS/nix/issues/7286
```sh
# git fetch -p && diff --changed-group-format='%<' --unchanged-group-format='' <(git branch | sed -e 's/^[ \*]*//') <(git branch -r | sed -e 's;^.*origin/;;g') | xargs -I{} git branch -D {}
git fetch -p && (diff --changed-group-format='%<' --unchanged-group-format='' <(git branch | sed -e 's/^[ \*]*//') <(git branch -r | sed -e 's;^.*origin/;;g')) | xargs -I{} git branch -D {}
```

## NPM

```sh
npm outdated

npx npm-check-updates
npx npm-check-updates -u
npx npm-check-updates -u --target minor && npm install

npm audit
npm audit fix --force
```

## Docker

### postgres

#### manage postgres
```sh
docker run —rm -d —net localnetwork_app_net -p 5432:5432 —name postgres -v ~/postgresql_data:/var/lib/postgresql/data -e POSTGRES_PASSWORD=password postgres:13.3

docker exec -it $(docker ps -q -f 'NAME=xxx') psql -U [USERNAME] [DB_NAME]
docker exec -i postgres psql -U postgres -c "CREATE USER dbuser WITH PASSWORD 'password'
docker exec -i postgres psql -U postgres -c "CREATE DATABASE postgresdb"

```

#### copy a remote postgres DB to the local (Need to open port)
```sh
docker run postgres:13.8 pg_dump --data-only postgres://[USERNAME]:[PASSWORD]@[IP_ADDRESS]:[PORT]/[DB_NAME] > dump.sql
cat ./dump.sql | docker exec -i [CONTAINER_ID] psql -U [USERNAME] [DB_NAME]

# one liner
docker run postgres:13.8 pg_dump --data-only postgres://[USERNAME]:[PASSWORD]@[IP_ADDRESS]:[PORT]/[DB_NAME] | docker exec -i [CONTAINER_ID] psql -U [USERNAME] [DB_NAME]
docker run postgres:13.8 pg_dump --data-only postgres://[USERNAME]:[PASSWORD]@[IP_ADDRESS]:[PORT]/[DB_NAME] | docker exec -i $(docker ps -q -f 'NAME=xxx') psql -U [USERNAME] [DB_NAME]
```


#### restore from dump file

```sh
# docker compose
docker compose exec -T [CONTAINER_NAME] pg_restore -cO -d [DB_NAME] -U [USER_NAME] -w < [DUMP_FILE]
```

#### run SQL on docker container by cli

```sh
docker compose exec -T [CONTAINER_NAME] psql -P pager=off -U [USER_NAME] -d [DB_NAME] -A -F, -w < ~/local/sample.sql | sed '$d'
docker compose exec -T [CONTAINER_NAME] psql -P pager=off -U [USER_NAME] -d [DB_NAME] -A -F, -w < ~/local/sample.sql | sed '$d' > sample.csv
```

### MySQL

```sh
docker exec -it [CONTAINER_NAME] mysql -u [USER_NAME] -p[PASSWORD] -N -e "SELECT TABLE_CATALOG, TABLE_SCHEMA,  TABLE_NAME, COLUMN_NAME, COLUMN_DEFAULT, IS_NULLABLE, DATA_TYPE, CHARACTER_MAXIMUM_LENGTH, NUMERIC_PRECISION, NUMERIC_SCALE, DATETIME_PRECISION, CHARACTER_SET_NAME, COLLATION_NAME, COLUMN_TYPE, COLUMN_KEY FROM information_schema.columns where table_schema = '[SCHEMA_NAME]';
```

## Convert data in command line

#### encrypt with RSA
```sh
openssl rsautl -encrypt -pubin -oaep -inkey public.pem -in passwd.txt -out passwd.encrypted
```
#### encrypt with base64
```sh
base64 passwd.encrypted
```
#### encrypt with RSA and base64 in one-liner
```sh
echo -n "password1" | openssl rsautl -encrypt -pubin -oaep -inkey public.pem | base64
```

## ffmpeg

#### convert a movie file to a gif file
```sh
ffmpeg -i input.mp4 -vf scale=800:-1 -r 16.667 output.gif
```

##### Optimizing
```sh
filters="fps=12,scale=640:-1:flags=lanczos"
ffmpeg -i input.mp4 -vf "$filters,palettegen=stats_mode=diff" -y palette.png
ffmpeg -i input.mp4 -i palette.png -lavfi "$filters,paletteuse=dither=bayer:bayer_scale=5:diff_mode=rectangle" -y output.gif

# one liner
i="input.mp4"; p="palette.png"; o="output.gif"; f="fps=12,scale=1080:-1:flags=lanczos" && ffmpeg -i $i -vf "$f,palettegen=stats_mode=diff" -y $p && ffmpeg -i $i -i $p -lavfi "$filters,paletteuse=dither=bayer:bayer_scale=5:diff_mode=rectangle" -y $o
i="input.mp4";r=1.0;f="fps=12,scale=640:-1:flags=lanczos,setpts=PTS/$r";p=palette.png; ffmpeg -i $i -vf "$f,palettegen=stats_mode=diff" -af atempo=$r -y $p && ffmpeg -i $i -i $p -lavfi "$f,setpts=PTS/$r,paletteuse=dither=bayer:bayer_scale=5:diff_mode=rectangle" -af atempo=$r -y output.gif

# n times speed (1.5x, 2.0x, 4.0x)
i="input.mp4"; p="palette.png"; o="output.gif"; r=4.0; f="fps=12,scale=640:-1:flags=lanczos,setpts=PTS/$r"; ffmpeg -i $i -vf "$f,palettegen=stats_mode=diff" -af atempo=$r -y $p && ffmpeg -i $i -i $p -lavfi "$f,setpts=PTS/$r,paletteuse=dither=bayer:bayer_scale=5:diff_mode=rectangle" -af atempo=$r -y $o

# acceralate a movie
i=Screen\ Recording\ 2024-09-04\ at\ 10.56.11.mov; r=1.50; o="output.mp4"; ffmpeg -i "$i" -r 16 -filter:v "setpts=PTS/$r,scale=1280:-2,minterpolate='mi_mode=mci:mc_mode=aobmc:vsbmc=1:fps=120'" -filter:a "atempo=$r" -y "$o"
```
According to [the doc](https://ffmpeg.org/ffmpeg.html), The `-lavfi` option is equivalent to -filter_complex.

See: [Reference1](https://cassidy.codes/blog/2017/04/25/ffmpeg-frames-to-gif-optimization/), [Reference2](https://superuser.com/questions/556029/how-do-i-convert-a-video-to-gif-using-ffmpeg-with-reasonable-quality), [Reference3](https://life.craftz.dog/entry/generating-a-beautiful-gif-from-a-video-with-ffmpeg)

#### trim a specific scene from a movie without any deteriorations
```sh
ffmpeg --ss [START_TIME e.g. 00:00:30] -i input.mp4 -t [DURATION e.g. 00:01:00 -vcodec copy -acodec copy -async 1 output.mp4
```

#### convert voice memo to wav file (to handle in Google Speech-to-Text Chirp 3)
```sh
sh -c 'ffmpeg -i "$1" -acodec pcm_s16le -ac 1 -ar 16000 "${1%.m4a}.wav"' _ file.m4a
```

## VSCode

#### export installed extensions
```sh
code --list-extensions | xargs -L 1 echo code --install-extension
```
#### import extensions
```sh
code --install-extension [EXTENSION_ID]
```
