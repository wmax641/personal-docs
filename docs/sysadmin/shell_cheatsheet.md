# `bash` / `sh` cheatsheet

## Basic
#### Cut specific column output
`$0` will get the whole line, `$n` will get the n'th column
```bash
ls -l | awk '{$2}'
```

If delimiter is predictable, can also use `cut`
```bash
date | cut -d' ' -f4
```

#### Loop over range
```
for i in {1..255}; do nc -cv 10.100.56.$i 22; done
```

#### Loop over list in file
```bash
cat list.txt | while read line; do echo "$line"; done
```
```bash
while read line; do echo "$line"; done < list.txt
```

## Complex
#### Loop over output
```bash
for i in $(docker images | grep "2 days ago" | awk '{print $3}'); 
  do docker image rm -f $i; 
done
```

