# bash cheatsheet

## Basic
#### Cut specific column output
`$0` will get the whole line, `$n` will get the n'th column
```
ls -l | awk '{$2}'
```

If delimiter is predictable, can also use `cut`
```
date | cut -d' ' -f4
```

#### Loop over range
```
for i in {1..255}; do nc -cv 10.100.56.$i 22; done
```

## Complex
#### Loop over output
```
for i in $(docker images | grep "2 days ago" | awk '{print $3}'); 
  do docker image rm -f $i; 
done
```

