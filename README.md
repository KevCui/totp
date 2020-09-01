# totp

> Yet another minimal TOTP generator.

## Table of Contents

- [Dependency](#dependency)
- [Download](#download)
- [Main Code](#main-code)
- [Example](#example)
- [Credits](#credits)

## Dependency

- [xxd](https://linux.die.net/man/1/xxd)
- [openssl](https://linux.die.net/man/1/openssl)

## Download

```bash
wget https://raw.githubusercontent.com/KevCui/totp/master/totp
chmod +x ./totp
```

## Main Code

```bash
mac=$(printf %016X "$(( ($(date +%s)) / 30))" \
    | xxd -r -p \
    | openssl sha1 -binary -hmac "$(base32 -d <<< "${1^^}")" \
    | xxd -p)
offset="$(( 16#"${mac:39:1}" * 2))"
printf '%06d\n' "$(( (0x${mac:offset:8} & 0x7FFFFFFF) % 1000000 ))"
```

## Example

```bash
~$ ./totp ZYTYYE5FOAGW5ML7LRWUL4WTZLNJAMZS
388841
```

## Credits

This project is heavily inspired by [susam/mintotp](https://github.com/susam/mintotp) and [jakwings/bash-totp](https://github.com/jakwings/bash-totp).
