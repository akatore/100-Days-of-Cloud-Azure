For this task, create an SSH key pair with the following requirements:

- The name of the SSH key pair should be `devops-kp`.
- The key pair `type` must be `rsa`.

```bash
~ ✖ ssh-keygen -t rsa -b 4096 -f ~/.ssh/devops-kp
Generating public/private rsa key pair.
/root/.ssh/devops-kp already exists.
Overwrite (y/n)? y
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /root/.ssh/devops-kp
Your public key has been saved in /root/.ssh/devops-kp.pub
The key fingerprint is:
SHA256:6+HUDiMTUX0Dv19CF7C/awaP6svF0pOszvqyPfTrKts root@azure-client
The key's randomart image is:
+---[RSA 4096]----+
|        .... ... |
|       .  ..o . .|
|      .    ..o. .|
|       .     o.. |
|      . S   . ...|
|       . o .+o.o.|
|      o * o..B=. |
|       * B=.+o.+.|
|        o+@E*o+. |
+----[SHA256]-----+

```

```bash
~ ✖ cat ~/.ssh/devops-kp.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQC4qvv7gBkyQpM0E0OLf+maW75gKN2PE4Wzf5wT7SwveOespXts74jwIS9aZCrgL/XFir/YtpQgnTv/1ORatIFziSPqj4RQnOUqVNQ9ZvoBJWfYEPPVpow3ne7D5PZoO2AkDbMmYB/tRjHOia5QYydbz0d5FsUt8TSQGtNgcZeh/hJG9/82/xl0usMQeZ83iOmRvhe0FtKlOqLPENq9Absx0JaqRD+2dml1XtruAg6nCGaQDDtW94oQnYdrbDjJgA0JXkSYG8d+oh268uegIwCRj1ZWAxexV+zWZJc37LvKoLKetwMfCjBrwbTg4xbRmOO791XUCBsASbpP9MKlzTKEsZfsijXFUEkd4ZUaVFHZ2xW19EYGChSMbmSQ9ZitZyFqZFu3sO/tChn8Uauo+2taewhV8usUCBQ4aRs/eN2p3SW3/mMkB6XKGBuiU7bt6e1Mi3k+N8w82z+O9GsWuCiLfaOfZy0bjjXCzyOiAu8oEXLORORw+g0Ylxh4IwE6lr2Io+zewMRAaTiphnX56fCeEF5XTsPgUf1UaMAaC5Rj/phfAU+pd8RHdCj86iyl5bNq9fg4xZz61dq5r/Wb1def3xLye7J3Xca+PyZ6v3oEgVu5+rqPKROcd/ztkI6jmASIftVJZ4La8qwH0f/BQHwGQF7g8K0OnfkFiNzwv+T2Xw== root@azure-client
```

```json
~ ➜  az sshkey create \
  --name devops-kp \
  --resource-group kml_rg_main-00671abe58424165 \
  --public-key "$(cat ~/.ssh/devops-kp.pub)"
**{
  "id": "/subscriptions/f0c3bcdd-5ce2-4fa0-8cf3-41559747512b/resourceGroups/KML_RG_MAIN-00671ABE58424165/providers/Microsoft.Compute/sshPublicKeys/devops-kp",
  "location": "westus",
  "name": "devops-kp",
  "publicKey": "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQC4qvv7gBkyQpM0E0OLf+maW75gKN2PE4Wzf5wT7SwveOespXts74jwIS9aZCrgL/XFir/YtpQgnTv/1ORatIFziSPqj4RQnOUqVNQ9ZvoBJWfYEPPVpow3ne7D5PZoO2AkDbMmYB/tRjHOia5QYydbz0d5FsUt8TSQGtNgcZeh/hJG9/82/xl0usMQeZ83iOmRvhe0FtKlOqLPENq9Absx0JaqRD+2dml1XtruAg6nCGaQDDtW94oQnYdrbDjJgA0JXkSYG8d+oh268uegIwCRj1ZWAxexV+zWZJc37LvKoLKetwMfCjBrwbTg4xbRmOO791XUCBsASbpP9MKlzTKEsZfsijXFUEkd4ZUaVFHZ2xW19EYGChSMbmSQ9ZitZyFqZFu3sO/tChn8Uauo+2taewhV8usUCBQ4aRs/eN2p3SW3/mMkB6XKGBuiU7bt6e1Mi3k+N8w82z+O9GsWuCiLfaOfZy0bjjXCzyOiAu8oEXLORORw+g0Ylxh4IwE6lr2Io+zewMRAaTiphnX56fCeEF5XTsPgUf1UaMAaC5Rj/phfAU+pd8RHdCj86iyl5bNq9fg4xZz61dq5r/Wb1def3xLye7J3Xca+PyZ6v3oEgVu5+rqPKROcd/ztkI6jmASIftVJZ4La8qwH0f/BQHwGQF7g8K0OnfkFiNzwv+T2Xw== root@azure-client",
  "resourceGroup": "KML_RG_MAIN-00671ABE58424165",
  "tags": null,
  "type": null
}**

```

![alt text](image.png)