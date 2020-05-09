
# pyxc - Execute Python code on the command-line shell

pyxc processes its input data with the help of user-defined Python 3 commands. It's like python -c, but it needs less typing.

**Hint: This is a mirror for the project on codeberg: https://codeberg.org/sadmin/pyxc**

## Examples
Assume we have an input file
```bash
$ cat input.txt 
afile
build.sh
script.pdf
song.mp3
song2.mp3
```

### Single-line mode (default)
Process every single line of input with the magic variable **line**

#### Strip extension from files
```bash
$ cat input.txt |pyxc 'line.split(".")[0]'
afile
build
script
song
song2

$ pyxc 'line.split(".")[0]' input.txt  # equivalent
```

#### Append '_blur' to each mp3 file, leaving the extension intact
```bash
$ cat input.txt |pyxc 'line if not line.endswith(".mp3") else line.split(".")[0] + "_blur.mp3"'
afile
build.sh
script.pdf
song_blur.mp3
song2_blur.mp3
```

#### Select the first column
```bash
$ paste input.txt input.txt |pyxc 'line.split()[0]'
afile
build.sh
script.pdf
song.mp3
song2.mp3
```


### Pooled mode (--pooled / -p)
Process the whole input at once with the magic variable **lines**

#### Select only the 4th line
```bash
$ cat input.txt |pyxc --pooled 'lines[3]'
song.mp3
```

#### Get number of lines in a file
```bash
$ cat input.txt |pyxc --pooled 'len(lines)'
5
```

#### Sum up all ordinals of all characters in every line 
```bash
$ cat input.txt |pyxc --pooled 'sum([ord(char) for line in lines for char in line])'
3891
```

#### Append 2 values, then sort input descendingly
```bash
$ cat input.txt |pyxc --pooled '"\n".join(reversed(sorted(lines + ["cron.log", "cron.log.1"])))'
song2.mp3
song.mp3
script.pdf
cron.log.1
cron.log
build.sh
afile
```


### Multiline cmd's
If cmd contains line separators, automatic printing is disabled

#### Grep with trailing context, leaving out each first trailing line
```bash
$ cat input.txt |pyxc -p '\
contextLen = 3
pattern = "build"

for i in range(len(lines)):
    if pattern not in lines[i]:
        continue

    for contextLine in range(i, i + contextLen + 1):
        if contextLine == i + 1:
            continue

        try:
            print(lines[contextLine])
        except IndexError:
            pass'

build.sh
song.mp3
song2.mp3
```


## Installing
```bash
pip install --upgrade git+https://codeberg.org/sadmin/pyxc.git
```

## Contributing
When contributing to this repository, please first discuss the change you wish to make via issue

## License
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details

## Similar projects
There are several projects with a similar goal but different syntax:
* https://pypi.org/project/pypyp/
* https://pypi.org/project/shell-pype/
* https://pypi.org/project/thru/
* https://pypi.org/project/Pyped/
* https://pypi.org/project/python-pype/
* https://pypi.org/project/pypipe/

