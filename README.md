# passgen

An app to generate passwords inspired by [XKCD 936](https://xkcd.com/936/) and
how long it will take to guess those passwords.

## Data

### Dictionary

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

### Password Guessing

I'm use an estimate of 100,000,000 password guesses a second. I arrived at
that number from reading a [fairly recent post](https://www.hivesystems.io/blog/are-your-passwords-in-the-green)
about how long it takes to guess passwords. I used their hashes per seconds in
various scenarios to get a fair number of password guesses a second.
