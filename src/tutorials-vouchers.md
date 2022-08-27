# Create vouchers

Vouchers are a nice way to spread your community currency. This tutorial will show you how to create unique QR codes and fund them such that the vouchers can be claimed by anymone using the Encointer Wallet app

We have prepared a [very simple python script](https://github.com/encointer/encointer-node/blob/master/client/voucher.py) for you to generate a number of paperwallets and generate QR codes that are understood by the Encointer Wallet app.

## setup

```
wget https://github.com/encointer/encointer-node/blob/master/client/voucher.py
chmod +x voucher.py

pip install pillow qrcode click secrets base58
```

## generate

```
./voucher.py --network nctr-k --cid u0qj944rhWE --issuer pestalozzi -n 10
```

this will create a batch of 10 vouchers

```
generating QR vouchers for batch_token: RtdpYnvFpkz
generating voucher 0: //RtdpYnvFpkz/Cpe3M7ai85cRhcqUk9aJx7x2YZJqjMini
generating voucher 1: //RtdpYnvFpkz/DVXYfVkDPPTdE7pbEUd7ChveApu5ZdoWm
generating voucher 2: //RtdpYnvFpkz/3RiUvgXSvVKCqVuaL49yANm6WdgXn3Xm9
generating voucher 3: //RtdpYnvFpkz/GaVVAgvL7Bn4iPStxe6UeKHFtwitVm4nJ
generating voucher 4: //RtdpYnvFpkz/NRrpFR2sWxzJjHVs86eW67Th8XCfc2xHS
generating voucher 5: //RtdpYnvFpkz/MgKraRVsE2pTMLL5K9TqnUJ4CJ98R3xb5
generating voucher 6: //RtdpYnvFpkz/7sGZntwV5jKXVMcZfWnGKpBdjCKTTDCP
generating voucher 7: //RtdpYnvFpkz/EnU7qpbWjHhYHAr9tSxEhPto6q4wsL7Vv
generating voucher 8: //RtdpYnvFpkz/BiqKLkevGhF7dLC3XjgAJg7iiYLS8uDQa
generating voucher 9: //RtdpYnvFpkz/7TMLTVU6wtBF7EJoJkg3jpuTCv19ktATM
```
The identifiers of these vouchers are private key seeds. do not share them with anyone!

In your working dir you'll find

```
voucher-RtdpYnvFpkz-3RiUvgXSvVKCqVuaL49yANm6WdgXn3Xm9.png
voucher-RtdpYnvFpkz-7sGZntwV5jKXVMcZfWnGKpBdjCKTTDCP.png
voucher-RtdpYnvFpkz-7TMLTVU6wtBF7EJoJkg3jpuTCv19ktATM.png
voucher-RtdpYnvFpkz-BiqKLkevGhF7dLC3XjgAJg7iiYLS8uDQa.png
voucher-RtdpYnvFpkz-Cpe3M7ai85cRhcqUk9aJx7x2YZJqjMini.png
voucher-RtdpYnvFpkz-DVXYfVkDPPTdE7pbEUd7ChveApu5ZdoWm.png
voucher-RtdpYnvFpkz-EnU7qpbWjHhYHAr9tSxEhPto6q4wsL7Vv.png
voucher-RtdpYnvFpkz-GaVVAgvL7Bn4iPStxe6UeKHFtwitVm4nJ.png
voucher-RtdpYnvFpkz-MgKraRVsE2pTMLL5K9TqnUJ4CJ98R3xb5.png
voucher-RtdpYnvFpkz-NRrpFR2sWxzJjHVs86eW67Th8XCfc2xHS.png
voucher-RtdpYnvFpkz.secrets
```

## funding

You can fund the vouchers one by one using the mobile app. 

1. select the correct community and sending account in your app
2. tap "send" on home screen
3. tap scanner symbol and scan one voucher
4. send funds

## design

You can now design nice vouchers including the QR codes generated. 

## auto-config

One nice features of these vouchers is that they automatically configure the app with the correct network and community upon scanning the QR code. this is especially helpful if using for demos on testnets

