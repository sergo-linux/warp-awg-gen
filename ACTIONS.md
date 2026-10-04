# Generate a WARP config with GitHub Actions

Open **Actions → Generate WARP config → Run workflow**. The workflow builds and checks the generator, registers a fresh WARP device, and uploads an encrypted configuration as an artifact for one day. It never uploads the registration cache or an unencrypted config.

Before a run, create a recipient key and certificate locally:

```sh
openssl req -x509 -newkey rsa:3072 -nodes -keyout recipient.key -out recipient.pem -days 365 -subj /CN=warp-delivery
chmod 600 recipient.key
openssl base64 -A -in recipient.pem
```

Paste the last command's output into **Base64-encoded PEM certificate for encrypted delivery**. Keep `recipient.key` private. Choose the packet mask (default `quic`) and optionally an endpoint, then run the workflow.

Download and extract the artifact ZIP, then decrypt locally:

```sh
openssl cms -decrypt -binary -inform DER -in warp-awg.conf.cms -recip recipient.pem -inkey recipient.key -out warp-awg.conf
chmod 600 warp-awg.conf
```

Import `warp-awg.conf` into a client compatible with the generator's AWG 3.1 parameters. Successful generation does not verify connectivity from your ISP. Automatic endpoint selection measures latency from the GitHub runner, so a manually chosen endpoint may work better on your network.
