# Compress file with password

```
unset HISTFILE
```

```
FILE=<filename> ; 7z a -t7z -m0=lzma2 -mx=9 -mfb=64 -md=32m -ms=on -mhe=on -p'PASSWORD' $FILE.7z $FILE
```

## Multiple files

```
for i in *.pdf; do 7z a -t7z -m0=lzma2 -mx=9 -mfb=64 -md=32m -ms=on -mhe=on -p'PASSWORD' "$i.7z" "$i"; done
```