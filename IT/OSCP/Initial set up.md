## Connect to a VM

`ssh -o "UserKnownHostsFile=/dev/null" -o "StrictHostKeyChecking=no" learner@192.168.50.52`

## Verify Integrity of a file

### On Windows

`certutil -hashfile *filename* sha256`

### On MacOS and Linux

`shasum -a 256 /path/to/file`

#### Switching to a one line terminal mode

`CTRL + P`

### VPN Network Interface

```shell
$: ip a
tun0
```
`$: ip a`
`tun 0`

# Connect to VPN

```shell
sudo openvpn universal.ovpn
```
