# encoding-check
Check the encoding of a text file line by line and find out where you have characters that do not match a given encoding

```
$ encoding-check --help

usage: encoding-check [-h] [-e ENCODING] [-s | -c | -l] [-m] [-w] [-n] [-f N]
                      [-t]
                      FILE [FILE ...]

Show all lines of a FILE containing characters that don't match the selected
ENCODING.

positional arguments:
  FILE                  the file to be examined

optional arguments:
  -h, --help            show this help message and exit
  -e ENCODING, --encoding ENCODING
                        file encoding to test (default 'ascii')
  -s, --summary         only print the summary
  -c, --count           only print the detected line count
  -l, --lines           only print the detected lines
  -m, --only-matching   hide files without matching lines from output
  -w, --no-warnings     hide warnings from output
  -n, --no-numbers      do not show line numbers in output
  -f N, --fit-width N   trim lines to N characters, or terminal width if N=0;
                        non-printable characters like tabs will be removed
  -t, --title           print title line above each file
```