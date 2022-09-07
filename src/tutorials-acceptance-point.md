# Acceptance Point Setup

In order to accept Encointer community currency (CC) at an outlet, the vendor may [set up an account](https://encointer.org/encointer-app/) like anyone else. 
No approval is necessary, anyone can start accepting CC in a matter of minutes.

## security

Make sure you [backup](https://encointer.org/wp-content/uploads/2022/05/ENCOINTER-BackupandRestoreAccount-230422-1443.pdf) your account

### collect-only setup
Once you have created your store account, it is possible to set up the app as a receiving payment terminal only, 
such that employees can only collect payments but not transfer funds. If this is what you want, do the following steps on a new device:

1. Set up at least one default account
2. Profile -> enable developer mode
3. Address Book -> (+) add contact
4. Enter the public address of your store account, like *5EkhjbRksfjG3GjCTveDicmSy2q87eTpNu6fKN8VDAYsKBvk*
5. enter the name of your store, i.e. *SuperLocalStore*
6. tick the *observation* checkbox
7. save
8. Profile -> disable developer Mode
7. Home -> tap logo to select account *SuperLocalStore (Observation)*
8. You should now see your store's balance
9. tap *receive* and enter amount
10. your client can now scan the QR code and send funds

## QR code for self-service checkout

You can prepare and print QR codes for display. This is useful for self-checkout scenarios where 
your customers scan and pay without interaction by your personel. Such QR codes act like an unpersonalized invoice, 
either with a fixed amount or with an undefined amount

The format for QR code payload is:

```
encointer-invoice
v1.0
<ACCOUNT>>
<CID>
<AMOUNT>
<LABEL>
```

So, If we have a fixed-price product for 5.0 Leu, we can print a QR code with the following payload:

```
encointer-invoice
v1.0
5DkVGErvJLPTgec4C73Xk7boEVTKXJYDuCro2kPHkB3XGARh
u0qj944rhWE
5.0
Encointer Association
```

Store the above content in `payload.txt`

On Linux, we can create a QR code using [qrencode](https://linux.die.net/man/1/qrencode). 

Caveat: with the current version we need to make sure there's no newline at the end of the last line. So we just truncate the last byte of the file in the following example command

```
head -c -1 payload.txt | qrencode -o test.png
```

Alternatively, you can use web services that help you generate a QR code, like [this one](https://www.qr-code-generator.com/) (select "Text" mode and make sure there's no line at the end)

Now, you can insert `test.png` into a design of your choice. For Leu we use [this pdf form](https://leu.zuerich/wp-content/uploads/2022/09/Leu_Steller.pdf)


