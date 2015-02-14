### Forward

Transferring funds between third-party accounts, especially those of exchanges, places considerable burden on the user and is error prone, due to the way in which deposits are identified to the client account. This problem was tackled by the existing banking industry through having a common code known as *IBAN*. This code amalgamated the institution and client account along with a error-detection mechanism practically eliminating trivial errors and providing considerable convenience for the user. Unfortunately, this is a heavily regulated and centralised service accessible only to large, well-established institutions. This protocol may be viewed is a decentralised version of it suitable for any institutions containing funds on the Ethereum system.

### IBAN

For a good overview of the IBAN system, please see [Wikipedia's IBAN article](https://en.wikipedia.org/wiki/International_Bank_Account_Number). An IBAN code consists of up to 34 case insensitive alpha-numeric characters. It contains three pieces of information:

- The country code; a top-level identifier for the context of the following (ISO 3166-1 alpha-2);
- The error-detection code; uses the *mod-97-10* checksumming protocol (ISO/IEC 7064:2003);
- The basic bank account number (BBAN); an identifier of the institution, branch and client account, whose composition is dependent on the aforementioned country.

For the UK, the BBAN is composed of:

- Institution identifier, 4-character alphabetical, e.g. `MIDL` (ironically) represents HSBC bank.
- Sort-code (branch identifier within the institution), a 6-digit decimal number, e.g. `402702` would be the Lancaster branch of HSBC.
- Account number (client identifier within the branch), an 8-digit decimal number.

# Proposed Design

Introduce a new IBAN country code: *XE*, formulated as the Ethereum *E* prefixed with the "extended" *X*, as used in non-jurisdictional currencies (e.g. XRP, XCP).

The BBAN for this code will comprise three fields:

- Institution identifier, 4-character alphanumeric (< 21-bit);
- Institution client identifier, 9-character alphanumeric (< 47-bit);
- Asset identifier, 3-character alphanumeric (< 16-bit)

Including the four initial characters, this leads to a final client-account address length of 20 characters, of the form:

```
XE66XREGGAVOFYORKETH
```

Split into:

- `XE` The country code for Ethereum;
- `66` The checksum;
- `XREG` The institution code for the account - in this case, Ethereum's base registry system;
- `GAVOFYORK` The client identifier within the institution - in this case, whatever primary address is associated with the name "GAVOFYORK" in Ethereum's base registry contract;
- `ETH` The asset identifier within the client account - in this case, "ETH" is the only valid asset identifier, since Ethereum's base registry contract supports only this asset.

## Notes

Institution identifiers beginning with X are reserved as special institutions for the given country code.

## Other forms

### URI

General URIs can be formed though the URI scheme name `iban`, followed by the colon character `:`, followed by the 20-character alphanumeric identifier, thus for the example above, we would use:

```
iban:XE66XREGGAVOFYORKETH
```

### QR Code

A QR code may be generated directly from the URI using standard QR encodings. For example, the example above `iban:XE66XREGGAVOFYORKETH` would have the corresponding QR code:

![QR code for iban:XE66XREGGAVOFYORKETH](data:image/gif;base64,R0lGODlhdAB0AJEAAAAAAP///wAAAAAAACH5BAEAAAIALAAAAAB0AHQAAAL/jI+py+0Po5y02ouz3rz7D4biSJbmiabqyrZuCcTyTNc2MuOybt9LDwwCKMIijTc85JTGGKPZJEKLyCoz+psKpVqg1bAEQ59d7yRcQat316Rj7bygIfMzWxzHuwN1BXzPBfjQF/H3xXfXYCiXSNdol4cYufiWSOgYiblH1XZIeJk1udUJuTmq5/mIWorUc6jZ6rM6KylIC9vmSirxp0tLmQDqZ3laGxhb87qbJmuc2ywcTBykfFsKzGldKUq9XF0IPW2mrSju6zyceS2OfOQ9aB7ObVs+j10cndr9q1pvmrwsGzpw9uT9o0em4Lx2wHDpOdcQnsKDDNmxYqSOVz99/wgldkz30R+GaBE1ZhSJMWTCkCUJqnxn8iVIhwOPrStjkd/CgDibaeqZc+C9iTgDAd2pkyLPoxtDMVWalGPFLi9cCjxXlcWUdllfbH12squJrw+bqvj0FCBIiP16+fS4Mu3Jq23jqXUZV27eu2iJmpU2V27fcTWXEv5ZbOXIt2X3FbZAFiVkxrXohr152Oniu1PdkTOaWHPKwZZlSn4M+CBpu54z5DNMEytY1ZRtxvzGcbXfy7cvfjasmzbnlCRez4Q9m63j4n8VI29MWCBz3qeHGumMwnhqqckNyp7sOPJe4VC3z94cfYxHt0jNQ0ev/Pp61t/Ym8beffha+jCF1v8WfVxSLQVX1AaD5RZUfXoZWFd78Tm3H1AcHAhcgv0JNqF3/AnYID2RfeePfVxVGFiJ8r2H2YgocmjiePFp9+KG/umH2of/BViZhdCBCKKKOdII4Y/ldRYcYruZJuKF6kXIkoZAAiikglgwaSR5MMooHVxOFbgiMxPh1xuTNj65DXlg4rVlGT7aFmV+SHbIZZtinedmkHb2V1WMNJLU4Zw+ZvkbOdpl52RruClJnYtq0tllaWvimaaEjMoppHUUxsTUn4Va2idmceppZVA8NgdpmYc2eiOlPULJJp98pQoqm63+Fet8X/rm5ZCRmmkrr2FWKqpgR52K5o7BCtsTsbGo4cMaslOiNmt4zTrrKINHcheoqqFRiWu1UfV65njRpucgrZuaS6Z7wJbbnrh1gtsts4lOWqWm6SL46rxd1jvpqjh6axWpqOZrqJbGtgsvplimqiyrz6mb5MEFm0oclJfO2Jp4Buca5MURrzvxabI+jK22z1b56ZI1Hkvum2kRqSPGZ175spsDsvygp4tKfDO7LfsJdNBCD0100UYfjXTSSi/NdNNOk1AAADs=)