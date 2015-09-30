---
name: Security Issue Process
category: 
---

Draft of steps that should be taken when finding a security issue in Ethereum. Security issue is defined as a problem in scope of [Security-Categorization](https://github.com/ethereum/wiki/wiki/Security-Categorization).

Partly inspired by [OWASP Risk Rating](https://www.owasp.org/index.php/OWASP_Risk_Rating_Methodology)

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
* Client version(s). Single or multiple implementations?
* OS / external library version(s).
* Link to relevant source code.
* How to fix.
* How to test.

### Ownership / Responsibility

* Who is assigned to fix the issue?
* Who will test / review a fix?
* Who takes responsibility for preparing new builds of client software?

### Disclosure

* Who takes on to disclose the issue?
* Communication channels (mail lists, twitter, github).
