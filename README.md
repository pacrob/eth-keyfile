# eth-keyfile

[![Join the conversation on Discord](https://img.shields.io/discord/809793915578089484?color=blue&label=chat&logo=discord&logoColor=white)](https://discord.gg/GHryRvPB84) [![Build Status](https://circleci.com/gh/ethereum/eth-keyfile.svg?style=shield)](https://circleci.com/gh/ethereum/eth-keyfile)
[![PyPI version](https://badge.fury.io/py/eth-keyfile.svg)](https://badge.fury.io/py/eth-keyfile)
[![Python versions](https://img.shields.io/pypi/pyversions/eth-keyfile.svg)](https://pypi.python.org/pypi/eth-keyfile)

A library for handling the encrypted keyfiles used to store ethereum private keys

> This library and repository was previously located at https://github.com/pipermerriam/ethereum-keyfile.  It was transferred to the Ethereum foundation github in November 2017 and renamed to `eth-keyfile`.  The PyPi package was also renamed from `ethereum-keyfile` to `eth-keyfile`.

Read more in the documentation below. [View the change log](https://github.com/ethereum/eth-keyfile/blob/main/CHANGELOG.rst).

## Quickstart

```sh
python -m pip install eth-keyfile
```

## Documentation

### `eth_keyfile.load_keyfile(path_or_file_obj) --> keyfile_json`

Takes either a filesystem path represented as a string or a file object and
returns the parsed keyfile json as a python dictionary.

```python
>>> from eth_keyfile import load_keyfile
>>> load_keyfile('path/to-my-keystore/keystore.json')
{
    "crypto" : {
        "cipher" : "aes-128-ctr",
        "cipherparams" : {
            "iv" : "6087dab2f9fdbbfaddc31a909735c1e6"
        },
        "ciphertext" : "5318b4d5bcd28de64ee5559e671353e16f075ecae9f99c7a79a38af5f869aa46",
        "kdf" : "pbkdf2",
        "kdfparams" : {
            "c" : 262144,
            "dklen" : 32,
            "prf" : "hmac-sha256",
            "salt" : "ae3cd4e7013836a3df6bd7241b12db061dbe2c6785853cce422d148a624ce0bd"
        },
        "mac" : "517ead924a9d0dc3124507e3393d175ce3ff7c1e96529c6c555ce9e51205e9b2"
    },
    "id" : "3198bc9c-6672-5ab3-d995-4942343ae5b6",
    "version" : 3
}
```

### `eth_keyfile.create_keyfile_json(private_key, password, kdf="pbkdf2", work_factor=None, salt_size=16) --> keyfile_json`

Takes the following parameters:

- `private_key`: A bytestring of length 32
- `password`: A bytestring which will be the password that can be used to decrypt the resulting keyfile.
- `kdf`: The key derivation function.  Allowed values are `pbkdf2` and `scrypt`.  By default, `pbkdf2` will be used.
- `work_factor`: The work factor which will be used for the given key derivation function.  By default `1000000` will be used for `pbkdf2` and `262144` for `scrypt`.
- `salt_size`: Salt size in bytes.

Returns the keyfile json as a python dictionary.

```python
>>> private_key = b'\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01'
>>> create_keyfile_json(private_key, b'foo')
{
    "address" : "1a642f0e3c3af545e7acbd38b07251b3990914f1",
    "crypto" : {
        "cipher" : "aes-128-ctr",
        "cipherparams" : {
            "iv" : "6087dab2f9fdbbfaddc31a909735c1e6"
        },
        "ciphertext" : "5318b4d5bcd28de64ee5559e671353e16f075ecae9f99c7a79a38af5f869aa46",
        "kdf" : "pbkdf2",
        "kdfparams" : {
            "c" : 262144,
            "dklen" : 32,
            "prf" : "hmac-sha256",
            "salt" : "ae3cd4e7013836a3df6bd7241b12db061dbe2c6785853cce422d148a624ce0bd"
        },
        "mac" : "517ead924a9d0dc3124507e3393d175ce3ff7c1e96529c6c555ce9e51205e9b2"
    },
    "id" : "3198bc9c-6672-5ab3-d995-4942343ae5b6",
    "version" : 3
}
```

### `eth_keyfile.decode_keyfile_json(keyfile_json, password) --> private_key`

Takes the keyfile json as a python dictionary and the password for the keyfile,
returning the decoded private key.

```python
>>> keyfile_json = {
...     "crypto" : {
...         "cipher" : "aes-128-ctr",
...         "cipherparams" : {
...             "iv" : "6087dab2f9fdbbfaddc31a909735c1e6"
...         },
...         "ciphertext" : "5318b4d5bcd28de64ee5559e671353e16f075ecae9f99c7a79a38af5f869aa46",
...         "kdf" : "pbkdf2",
...         "kdfparams" : {
...             "c" : 262144,
...             "dklen" : 32,
...             "prf" : "hmac-sha256",
...             "salt" : "ae3cd4e7013836a3df6bd7241b12db061dbe2c6785853cce422d148a624ce0bd"
...         },
...         "mac" : "517ead924a9d0dc3124507e3393d175ce3ff7c1e96529c6c555ce9e51205e9b2"
...     },
...     "id" : "3198bc9c-6672-5ab3-d995-4942343ae5b6",
...     "version" : 3
... }
>>> decode_keyfile_json(keyfile_json, b'foo')
b'\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01'
```

### `eth_keyfile.extract_key_from_keyfile(path_or_file_obj, password) --> private_key`

Takes a filesystem path represented by a string or a file object and the
password for the keyfile.  Returns the private key as a bytestring.

```python
>>> extract_key_from_keyfile('path/to-my-keystore/keyfile.json', b'foo')
b'\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01\x01'
```

## Developer Setup

If you would like to hack on eth-keyfile, please check out the [Snake Charmers
Tactical Manual](https://github.com/ethereum/snake-charmers-tactical-manual)
for information on how we do:

- Testing
- Pull Requests
- Documentation

We use [pre-commit](https://pre-commit.com/) to maintain consistent code style. Once
installed, it will run automatically with every commit. You can also run it manually
with `make lint`. If you need to make a commit that skips the `pre-commit` checks, you
can do so with `git commit --no-verify`.

### Development Environment Setup

You can set up your dev environment with:

```sh
git clone git@github.com:ethereum/eth-keyfile.git
cd eth-keyfile
virtualenv -p python3 venv
. venv/bin/activate
python -m pip install -e ".[dev]"
pre-commit install
```

### Release setup

To release a new version:

```sh
make release bump=$$VERSION_PART_TO_BUMP$$
```

#### How to bumpversion

The version format for this repo is `{major}.{minor}.{patch}` for stable, and
`{major}.{minor}.{patch}-{stage}.{devnum}` for unstable (`stage` can be alpha or beta).

To issue the next version in line, specify which part to bump,
like `make release bump=minor` or `make release bump=devnum`. This is typically done from the
main branch, except when releasing a beta (in which case the beta is released from main,
and the previous stable branch is released from said branch).

If you are in a beta version, `make release bump=stage` will switch to a stable.

To issue an unstable version when the current version is stable, specify the
new version explicitly, like `make release bump="--new-version 4.0.0-alpha.1 devnum"`
