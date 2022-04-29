# passgen

An app to generate passwords inspired by [XKCD 936](https://xkcd.com/936/).

## Data

It was difficult to find a suitable source of words that would be memorable.
The dictionary here is based another project building passwords in the XKCD 936
style. The dictionary here is based on that one -
[gwordlist](https://media.githubusercontent.com/media/hackerb9/gwordlist).

I removed small words and selected the top `65536` words. I also had a little fun
automatically generating the source from the word list with `awk`.

```bash
$ curl https://media.githubusercontent.com/media/hackerb9/gwordlist/master/frequency-alpha-wn.txt > temp
$ awk '(NR>1)' temp | awk '{if (length($2) > 2) print $2}' | awk '/^[a-z]*$/' | head -n 65536 | awk 'OFS="" BEGIN {print "export default ["} {print "  \"", $0, "\","} END {print "]"}' > src/services/dictionary.ts
```
