
## Recap 

| **Level Range**   | **Main Challenge / Concept**                                                | **Key Commands Used**                                       |
| ----------------- | --------------------------------------------------------------------------- | ----------------------------------------------------------- |
| **Level 0 → 1**   | SSH connection & reading dashed filenames (`./-`)                           | `ssh`, `cat ./-`                                            |
| **Level 1 → 2**   | Handling spaces in filenames                                                | `cat "spaces in this filename"`                             |
| **Level 2 → 3**   | Accessing hidden files                                                      | `ls -a`, `cat ./.hidden`                                    |
| **Level 3 → 4**   | Inspecting file types in directories                                        | `file ./*`, `cat`                                           |
| **Level 4 → 5**   | Finding human-readable files among multi-directory junk                     | `file ./*`                                                  |
| **Level 5 → 6**   | Finding files by specific criteria (size, executable bit, owner)            | `find . -type f -size 1033c ! -executable`                  |
| **Level 6 → 7**   | System-wide search by owner, group, and size                                | `find / -user bandit7 -group bandit6 -size 33c 2>/dev/null` |
| **Level 7 → 8**   | Pattern searching in large text files                                       | `grep "millionth" data.txt`                                 |
| **Level 8 → 9**   | Finding unique (non-repeated) lines                                         | `sort data.txt \| uniq -u`                                  |
| **Level 9 → 10**  | Extracting printable strings from binary data                               | `strings data.txt \| grep "="'                              |
| **Level 10 → 11** | Base64 decoding                                                             | `base64 -d data.txt`                                        |
| **Level 11 → 12** | Caesar cipher / ROT13 translation                                           | `tr 'A-Za-z' 'N-ZA-Mn-za-m' < data.txt`                     |
| **Level 12 → 13** | Reverse hex dumping & recursive archive extraction (`gzip`, `bzip2`, `tar`) | `xxd -r`, `file`, `gunzip`, `bunzip2`, `tar -xf`            |

## L13 to L14

- `ssh` - secure shell client used for logging into and executing commands on a remote machine
- `ssh -i` - option flag specifying an identity file (private key) for SSH authentication
- `chmod` - change file mode bits / permissions (e.g., `chmod 600` restricts access to owner read/write only)

pass: aaWecNkG4FhxJQxz07uiwzVP6bJiYS65

## L14 to L15

- `nc` - netcat command-line tool used for reading and writing raw data across network connections (TCP/UDP)
- `telnet` - protocol and command-line tool used to interactively communicate with a remote server/port
- `localhost` - loopback IP address (`127.0.0.1`) representing the local host machine

`pass` - `pbLYuZtTg4MgaqfJx8jbA9gKKGqM68A7`

## L15 to L16

