---
title: Setup Charles proxy for mobile app
date: 2020-10-20 22:25:53
tags:
---

1. Install  https://www.charlesproxy.com/download/latest-release/

2. Access

```shell
sudo chown -R root "/Applications/Charles.app/Contents/Resources"
sudo chmod -R u+s "/Applications/Charles.app/Contents/Resources"
```

3. Help -> Register 

   ```
   * Registered Name: https://zhile.io
   * License Key: 48891cf209c6d32bf4
   ```

4. Help -> SSL Proxying -> Install Chales Root Certificate on Mobile device and Remote Browser.

   Configure your device to use Charles as its HTTP proxy on 192.168.0.102:8888, then browse to chls.pro/ssl to download and install the certificate. Note that on iOS 10 and later you must then go into Settings > General > About > Certificate Trust Settings and enable the Charles certificate to be trusted.

