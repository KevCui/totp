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
secret="${1^^}$(printf "%$(( (8-${#1}) % 8 ))s" | tr " " "=")"
key="$(base32 -d <<< "$secret" \
    | xxd -p \
    | tr -cd 0-9A-Fa-f)"
mac=$(printf "%016X" "$(( ($(date +%s)) / 30))" \
    | xxd -r -p \
    | openssl sha1 -binary -mac hmac -macopt "hexkey:$key" \
    | xxd -p)
offset="$(( 16#"${mac:39:1}" * 2))"
printf "%06d\n" "$(( (0x${mac:offset:8} & 0x7FFFFFFF) % 1000000 ))"
```

## Example

- Generate TOTP with secret string:

```bash
~$ ./totp ZYTYYE5FOAGW5ML7LRWUL4WTZLNJAMZS
388841
```

- To better protect secret, combine with [secret-vault](https://github.com/KevCui/secret-vault) to generate TOTP:

```bash
~$ ./totp "$(vault.sh -k totp_secret)"
300597
```

## Credits

This project is heavily inspired by [susam/mintotp](https://github.com/susam/mintotp) and [jakwings/bash-totp](https://github.com/jakwings/bash-totp).
