# Uniswap Audit


<img height="100px" Hspace="30" Vspace="10" align="right" src="static-content/diligence.png"/> 

<!-- Don't remove this -->
<!--59bcc3ad6775562f845953cf01624225-->
<!-- Don't remove this -->

## 1 Summary

Uniswap is a decentralized exchange hosted on the main Ethereum blockchain. It enables users to trade pairs any ERC20 token for ETH, or for another ERC20 token. It has no native token, and no fees are charged by Uniswap's creators. and thus can be considered a public good. 

From December __ to January __ ConsenSys Diligence conducted a security audit
4 members of our team participated participated in the audit.

To our knowledge, this is the first important ethereum smart contract system written in Vyper, and the first security audit conducted on a Vyper codebase. Previously, the vast majority of contracts having been written in Solidity.  

Vyper has the disadvantage of being a newer language with a smaller community, thus having fewer security analysis tools available. Fortunately, Vyper's design is intended to fa

### 1.1 Audit Dashboard

________________

<img height="120px" Hspace="30" Vspace="10" align="right" src="static-content/dashboard.png"/> 

#### Audit Details
* **Project Name:** Uniswap
* **Client Name:** Uniswap
* **Client Contact:** Hayden Adams
* **Auditors:** John Mardlin, Gonçalo Sá, Dean Pierce, Sergii Kravchenko, Daniel Luca 
* **GitHub :** ConsenSys/Uniswap-audit-internal-2018-12
* **Languages:** Vyper
* **Date:** January 11th 2019

#### Number of issues per severity

<%= issue_overview %>

________________

### 1.2 Audit Goals

The focus of the audit was to verify that the smart contract system is secure, resilient and working according to its specifications. The audit activities can be grouped in the following three categories:  

**Security:** Identifying security related issues within each contract and within the system of contracts.

**Sound Architecture:** Evaluation of the architecture of this system through the lens of established smart contract best practices and general software best practices.

**Code Correctness and Quality:** A full review of the contract source code. The primary areas of focus include:

* Correctness 
* Readability 
* Sections of code with high complexity
* Improving scalability
* Quantity and quality of test coverage

### 1.3 System Overview 

#### Documentation

The following documentation was available to the audit team, and provides the necessary context for this report:

- [White Paper](https://hackmd.io/C-DvwDSfSxuh-Gd4WKE_ig#) 
- [Docs](https://docs.uniswap.io/)
- [Runtime Verifications Formal Specification of Market Maker Model](https://github.com/runtimeverification/verified-smart-contracts/blob/uniswap/uniswap/x-y-k.pdf)

#### Summary of Formal Verification work done by Runtime Verification

We were provided with a [report](https://github.com/runtimeverification/verified-smart-contracts/blob/uniswap/uniswap/x-y-k.pdf) by Runtime Verification (RV) Inc. describing some properties of Uniswap exchange. This report contains a formal specification of the Uniswap exchange model (constant product market maker model) that includes a description of price discovery including fees, tokens trading and adding/removing liquidity.

Since the implementation is restricted to integer arithmetic, the main purpose of the RV report is to analyze the difference between the theoretical model and the implemented model. This report only analyzes rounding errors that are made on the transition to integer arithmetics. It proves that no attack can be made to benefit from rounding errors, and that every rounding error is made in favour of the liquidity pool.

The RV report does not cover all possible attacks on the actual smart contract system, such as frontrunning, reentrancy, etc.

#### Scope

| Contract file name  |                SHA1 Hash                 |
| ------------------- | ---------------------------------------- |
| uniswap_exchange.vy | 9b058dc847040594bcac502effab5bda0de5fa3c |
| uniswap_factory.vy  | 97d49145ec4fc6aa31099cb51c0c2f69b6e487b7 |
|                     |                                          |


### 1.4 Key Observations/Recommendations  


An important consideration in our Recommendations is that the Uniswap system is already live on the main Ethereum network, and holds over $300,000 in its liquidity pools. 

* 
* 

<!-- Positive Observations: Examples 
*  The design of the system is well documented
*  The code contains many helpful comments
*  The library architecture is efficient, and innovative
-->

<!-- Recommendations: Examples

* **Test coverage is incomplete:** Any contract system that is used on the main net should have as a minimum requirement a 100% test coverage.
* **Include negative test cases:** The majority of the tests are positive test cases, meaning that the tests confirm that the system works with an expected sequence of actions and inputs. The test suite should be expanded to include more negative scenarios to ensure that the safe checks within the contract system are working correctly.  
* **Stages/Time periods:**  The stages and various timings should be defined more clearly in separated control functions. Any state changing function that is called should check first against those control functions and check if it is allowed to be executed. 
* **Fix all issues:** It is recommended to fix all the issues listed in the below chapters, at the very least the ones with severity Critical, Major and Medium. All issues have also been created as issues on [Github](LINK).
* 
-->
## 2 Threat Model

### 2.1 Overview 

Uniswap is a decentralized exchange, which, from the start, gives it a large number of potential adversaries with strong incentives to take advantage of the system. We examine the various malicious actors, and the potential impact they may have on the system.

### 2.2 Detail

**Malicious Web Attacker**

Since the front-facing portion of Uniswap is hosted on a website, anyone who gains access to the DNS, hosting, or Cloudflare account could deploy a malicious uniswap frontend that could, among other things, redirect transactions meant for the exchanges to a wallet controlled by the attacker. An attacker may also go after one of the many dependencies pulled in by NPM to inject themselves into the deployment process.

**Malicious Ethereum Attacker**

The contracts are live on mainnet, giving anyone the ability to poke at them. The functionality in these contracts is fairly minimal, and the implementation in vyper has likely mitigated many of the classic implementation errors, so it seems like an attacker is going to have the most luck attacking extra features in the attached ERC20 token.

**Malicious Trader**

Two key areas of attack for malicious traders would be trying to stack rounding issues, and skimming trades via front running. Rounding issues are unlikely to be much of a problem because rounding always favors the liquidity providers, but is likely to a key attack vector for a while. Specifics and potential mitigations are discussed in **3.1**.

**Malicious Miner**

The key advantage that a Malicious Miner has is the ability to front run transactions much more reliably. Hopefully the social incentives for being a fair pool operator will preclude any of the major pools from participating in this sort of activity, but the threat exists.

**Malicious Liquidity Provider**

A large liquidity provider primarily has the advantage when performing rounding attacks. Since rounding errors favor the liquidity provider, someone may temporarily take something like a 95% stake in the liquidity pool, and then attempt to stack rounding issues to drain funds from the liquidity pool. We have so far been unable to figure out a sequence of events that would be favorable to the attacker.

**Malicious Exchange Creator**

Importantly, the person who creates an exchange gets to point at any ERC20 token they like, which could lead to a few types of attacks. An attacker may attempt to impersonate a popular token by naming it similarly, though as long as the default exchanges are statically added to the frontend manually exposure should be limited.

More interestingly, the exchange creator could register a well known legitimate token, but initialize the liquidity pool in such a way that the token can never be used on the platform.

There is also nothing that checks for the legitimacy of any ERC20 that gets inserted through the Factory, though the Exchange itself is created via a template, so the Exchange code can't be tampered with. The ERC20 tokens could be contracts designed to attack uniswap, or particularly vulnerable contracts might be added more prone to attack.


## 3 Issue Overview  

The following table contains all the issues discovered during the audit. The issues are ordered based on their severity. More detailed description on the  levels of severity can be found in Appendix 2. The table also contains the Github status of any discovered issue.

<%= issue_list %>


## 4 Issue Detail  


<%= issues_markdown %>

## 5 Tool based analysis 

The issues from the tool based analysis have been reviewed and the relevant issues have been listed in chapter 3 - Issues. 


### 5.1 Mythril Classic

<img height="120px" align="right" src="static-content/mythril.png"/>

The [Mythril Classic](https://github.com/ConsenSys/mythril-classic) uses concolic analysis to detect various types of issues. The tool was used for automated vulnerability discovery for all audited contracts and libraries. More details on MythX's current vulnerability coverage can be found [here](https://github.com/ConsenSys/mythril-classic/wiki).

The raw output of the Mythril Classic vulnerability scan for each contract:

* [uniswap_exchange.vy](./tool-output/mythril/mythril_output_exchange.md)
* [uniswap_factory.vy](./tool-output/mythril/mythril_output_factory.md)


### 5.2 Harvey Fuzzer

Harvey is a grey box fuzzer designed specifically for the EVM. 

The raw output of Harvey's analysis for each contract:

* [uniswap_exchange.vy](./tool-output/harvey/harvey_output_exchange.md)
* [uniswap_factory.vy](./tool-output/harvey/harvey_output_factory.md)

## 6 Test Coverage Measurement

Testing is implemented using [eth-tester](https://github.com/ethereum/eth-tester). 21 tests are included in the test suite and they all pass.

Specific sections of the code where necessary test coverage is missing are included in chapter [3 - Issues](#3-issue-detail).

It's important to note that "100% test coverage" is not a silver bullet. Our review also included a inspection of the test suite, to ensure that testing included important edge cases.

The state of test coverage at the time of our review can be viewed in [coverage_output.md](./coverage-reports/coverage_output.md).

## Appendix 1 - File Hashes

The SHA1 hashes of the source code files in scope of the audit are listed in the table below.

| Contract file name  |                SHA1 Hash                 |
| ------------------- | ---------------------------------------- |
| uniswap_exchange.vy | 9b058dc847040594bcac502effab5bda0de5fa3c |
| uniswap_factory.vy  | 97d49145ec4fc6aa31099cb51c0c2f69b6e487b7 |
|                     |                                          |

## Appendix 2 - Severity 


### A.2.1 - Minor

Minor issues are generally subjective in nature, or potentially deal with topics like "best practices" or "readability".  Minor issues in general will not indicate an actual problem or bug in code.

The maintainers should use their own judgment as to whether addressing these issues improves the codebase.

### A.2.2 - Medium

Medium issues are generally objective in nature but do not represent actual bugs or security problems.

These issues should be addressed unless there is a clear reason not to.

### A.2.3 - Major

Major issues will be things like bugs or security vulnerabilities.  These issues may not be directly exploitable, or may require a certain condition to arise in order to be exploited.

Left unaddressed these issues are highly likely to cause problems with the operation of the contract or lead to a situation which allows the system to be exploited in some way.

### A.2.4 - Critical

Critical issues are directly exploitable bugs or security vulnerabilities.

Left unaddressed these issues are highly likely or guaranteed to cause major problems or potentially a full failure in the operations of the contract.

## Appendix 3 - Disclosure

ConsenSys Diligence (“CD”) typically receives compensation from one or more clients (the “Clients”) for performing the analysis contained in these reports (the “Reports”). The Reports may be distributed through other means, including via ConsenSys publications and other distributions.

The Reports are not an endorsement or indictment of any particular project or team, and the Reports do not guarantee the security of any particular project. This Report does not consider, and should not be interpreted as considering or having any bearing on, the potential economics of a token, token sale or any other product, service or other asset. Cryptographic tokens are emergent technologies and carry with them high levels of technical risk and uncertainty. No Report provides any warranty or representation to any Third-Party in any respect, including regarding the bugfree nature of code, the business model or proprietors of any such business model, and the legal compliance of any such business. No third party should rely on the Reports in any way, including for the purpose of making any decisions to buy or sell any token, product, service or other asset. Specifically, for the avoidance of doubt, this Report does not constitute investment advice, is not intended to be relied upon as investment advice, is not an endorsement of this project or team, and it is not a guarantee as to the absolute security of the project. CD owes no duty to any Third-Party by virtue of publishing these Reports.

PURPOSE OF REPORTS The Reports and the analysis described therein are created solely for Clients and published with their consent. The scope of our review is limited to a review of Solidity code and only the Solidity code we note as being within the scope of our review within this report. The Solidity language itself remains under development and is subject to unknown risks and flaws. The review does not extend to the compiler layer, or any other areas beyond Solidity that could present security risks. Cryptographic tokens are emergent technologies and carry with them high levels of technical risk and uncertainty.

CD makes the Reports available to parties other than the Clients (i.e., “third parties”) -- on its Github account (https://github.com/ConsenSys). CD hopes that by making these analyses publicly available, it can help the blockchain ecosystem develop technical best practices in this rapidly evolving area of innovation.

LINKS TO OTHER WEB SITES FROM THIS WEB SITE You may, through hypertext or other computer links, gain access to web sites operated by persons other than ConsenSys and CD. Such hyperlinks are provided for your reference and convenience only, and are the exclusive responsibility of such web sites' owners. You agree that ConsenSys and CD are not responsible for the content or operation of such Web sites, and that ConsenSys and CD shall have no liability to you or any other person or entity for the use of third party Web sites. Except as described below, a hyperlink from this web Site to another web site does not imply or mean that ConsenSys and CD endorses the content on that Web site or the operator or operations of that site. You are solely responsible for determining the extent to which you may use any content at any other web sites to which you link from the Reports. ConsenSys and CD assumes no responsibility for the use of third party software on the Web Site and shall have no liability whatsoever to any person or entity for the accuracy or completeness of any outcome generated by such software.

TIMELINESS OF CONTENT The content contained in the Reports is current as of the date appearing on the Report and is subject to change without notice. Unless indicated otherwise, by ConsenSys and CD. 

