Draft of steps that should be taken when finding a security issue in Ethereum. Security issue is defined as a problem in scope of [Security-Categorization](https://github.com/ethereum/wiki/wiki/Security-Categorization).

This list is inspired by [OWASP Risk Rating](https://www.owasp.org/index.php/OWASP_Risk_Rating_Methodology)

### Documentation

* Add a entry describing the issue at (TODO: github link).
* Estimate likelihood, impact and complexity of fix.
* Affected software version(s).

### Estimate Likelihood

* How likely is it to be uncovered and exploited by an attacker?
* Ease of discovery?
* Ease of exploit?
* Likelihood of detection?

### Estimate Impact

* Blockchain consensus. Potential of blockchain fork?
* Financial damage. Loss of ether?
* Privacy. E.g. revealing who sent a tx or who owns an address.
* Availability. Can it impact availablity of node(s)?

### Technical description

* Protocol version.
* Client version(s). Does it affect a single or multiple implementations?
* Link to relevant source code.
* How to fix.
* How to test.