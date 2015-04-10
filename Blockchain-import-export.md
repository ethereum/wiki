## C++
### Import:
```
eth --import <filename>
```
Formats supported: binary

### Export:
```
eth --export Myfile --format binary --from 45 --to latest
```
Formats supported: hex (newlines separating), binary or JSON
`--from` and `--to` also support blockhashes

## Go
### Import
```
geth import <filename>
```
Formats supported: binary
### Export
```
geth export <filename>
```
Formats supported: binary
## Python