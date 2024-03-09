# Rust based AGE - with SOPS integration

Documents how to use `rage` for encrypting/decrypting file using `SOPS`.

- https://github.com/getsops/sops/issues/1103#issuecomment-1866390434
- https://github.com/FiloSottile/age/
- https://github.com/str4d/rage

## 1. Prepare to encrypt

Generate identity (private) and recipient (public) with password - will ask for it interactively

See also <https://htmlpreview.github.io/?https://github.com/FiloSottile/age/blob/main/doc/age.1.html#RECIPIENTS-AND-IDENTITIES>

```bash
$ rage-keygen | rage -p - > key.enc
Public key: age1lzffdzg65h3edyrwssnu388ny0vkgmswy8047j8fghas9fw4syas67rr9j
```
## 2. Encrypt

Encrypt the text file with sops

```bash
sops -e --age age1lzffdzg65h3edyrwssnu388ny0vkgmswy8047j8fghas9fw4syas67rr9j text-clear.yaml > secret.yaml
```

## 3. Prepare to decrypt

Uses key.enc which was generated in the first step.
This will ask for password again, interactively

```bash
SOPS_AGE_KEY=$(rage -d key.enc)
```

# will also print

```
# created: 2024-03-09T16:00:50+01:00
# public key: age1lzffdzg65h3edyrwssnu388ny0vkgmswy8047j8fghas9fw4syas67rr9j
AGE-SECRET-KEY-1MUAJZM5AEKU0VFDJLX3G8WKQ7FQXL8UNCA86USQLFPEQ2870ZX8Q3MHGAX
```

```
$ echo $SOPS_AGE_KEY
# created: 2024-03-09T16:00:50+01:00 # public key: age1lzffdzg65h3edyrwssnu388ny0vkgmswy8047j8fghas9fw4syas67rr9j AGE-SECRET-KEY-1MUAJZM5AEKU0VFDJLX3G8WKQ7FQXL8UNCA86USQLFPEQ2870ZX8Q3MHGAX
```

## 4. Decrypt

`SOPS` for whatever reason requires `~/.config/sops/age/keys.txt` on my local Linux Mint. 
Setting env variable `SOPS_AGE_KEY_FILE` or `SOPS_AGE_KEY` did not work out out of the box...

```bash
$ cat ~/.config/sops/age/keys.txt
AGE-SECRET-KEY-1MUAJZM5AEKU0VFDJLX3G8WKQ7FQXL8UNCA86USQLFPEQ2870ZX8Q3MHGAX

# Now decrypt secret file - works
sops -d secret.yaml > clear2.yaml
```

