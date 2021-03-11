# memorandom

## Command Line

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
