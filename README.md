# COMBOLITE firmware

Signed firmware images for the COMBOLITE expansion card for the Soviet
Vector-06C computer. Sources live in a separate repository.

## What is here

* `manifest.txt` — what the current release is: version, image hashes, links
  and a signature over all of it. Boards read this file and compare the version
  with their own.
* `pubkey.pem` — the public half of the signing key, so anyone can check the
  signature.
* Images themselves are attached to [releases](../../releases), not committed
  here.

## Checking a release by hand

    curl -sO https://raw.githubusercontent.com/ekundo/combolite-firmware/main/manifest.txt
    head -n -1 manifest.txt > signed.txt
    tail -n 1 manifest.txt | cut -d= -f2 | xxd -r -p > sig.der
    openssl dgst -sha256 -verify pubkey.pem -signature sig.der signed.txt

## How a board uses it

A board checks this manifest once a day and only *reports* what it finds — it
never installs anything on its own. Installing is a button in its web UI. That
is deliberate: a bad build would otherwise reach every board at once, and the
rollback in the bootloader only catches firmware that fails to start, not
firmware that starts and misbehaves.

The board verifies two separate things: the TLS certificate (whom it talked to)
and the signature (what it was given). The second one matters more — a
certificate says nothing about the contents.
