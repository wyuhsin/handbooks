# Grep Cheatsheet

## Usage

```bash
# search standard output
grep {{OPTIONS}} {{SEARCH_STRING}}

# search for an exact string in file
grep {{OPTIONS}} {{SEARCH_STRING}} {{PATH_TO_FILE}}

# print lines in myfile.txt containing the string "melon"
grep 'mellon' myfile.txt

```

## Option Examples

```bash
# -i: forget about case sensitivity
grep -i ^DA {{PATH_TO_FILE}}

# -w: search only for the full word
grep -w "of" {{PATH_TO_FILE}}

# -A: display 3 lines after matching string
grep -A 3 'Exception' {{PATH_TO_FILE}}

# -B: display 4 lines before matching string
grep -B 4 'Exception' {{PATH_TO_FILE}}

# -C: display lines around matching string
grep -C 5 'Exception' {{PATH_TO_FILE}}

# -r: recursive search(within subdirs)
grep -r 'info' {{PATH_TO_DIR}}

# -v: return all lines which don't match the pattern
grep -v 'warning' {{PATH_TO_DIR}}

# -e: use regex
grep -e '^al' {{PATH_TO_FILE}}

# -E: extended regex(lines containing jason or jackson)
grep -E 'ja(s|cks)on' {{PATH_TO_FILE}}

# -c: count the numbers of matches
grep -c 'error' {{PATH_TO_FILE}}

# -l: print the name of the file of matches
grep -l 'rebot' {{PATH_TO_DIR/*}}

# -o: only show the matching part of the string
grep -p {{SEARCH_STRING}} {{PATH_TO_FILE}}

# show the line numvers of the matches
grep -n "error" {{PATH_TO_FILE}}

```

## Grep Regular Expressions

### Wildcards

```bash
# any character
.

# optional and can onlu occur once
?

# optional and can occur more than once
*

# required and can occur more than once
+

```

### Quantifiers

```bash
# previous item appears exactly n times
{n}

# previous item appears n tiems or more
{n,}

# previous item appears n times maximum
{,m}

# previous item appears between n and m times
{n,m}

```

### POSIX

```bash
# any lower and upper case letter
[:alpha:]

# any number
[:digit:]

# any lower and upper case letter or digit
[:alnum:]

# any whitespace
[:space:]

```

### Character

```bash
# any lower ad upper case letter
[A-Za-z]

# any numbers
[0-9]

# any lower and upper case letter or digit
[0-9A-Za-z]

```

### Position

```bash
# begining of line
^

# end of line
$

# empty line
^$

# start of word
\\<

# end of word
\\>

```