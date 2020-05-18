```bash
netstat -an | grep '^tcp' | awk '{++s[$NF]} END{for(i in s) print i "\t"s[i]}'
```

