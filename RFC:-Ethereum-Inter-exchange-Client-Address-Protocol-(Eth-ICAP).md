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