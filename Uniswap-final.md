# Uniswap Audit


<img height="100px" Hspace="30" Vspace="10" align="right" src="static-content/diligence.png"/> 

<!-- Don't remove this -->
* [1 Summary](#1-summary)
  * [1.1 Audit Dashboard](#11-audit-dashboard)
  * [1.2 Audit Goals](#12-audit-goals)
  * [1.3 System Overview](#13-system-overview)
  * [1.4 Key Observations/Recommendations](#14-key-observationsrecommendations)
* [2 Issue Overview](#2-issue-overview)
* [3 Issue Detail](#3-issue-detail)
  * [3.1 Frontrunners can skim ~2.5% from every transaction.](#31-frontrunners-can-skim-25-from-every-transaction)
  * [3.2 The factory contract should use a constructor](#32-the-factory-contract-should-use-a-constructor)
  * [3.3 Consider using transferFrom() in removeLiquidity() function.](#33-consider-using-transferfrom-in-removeliquidity-function)
  * [3.4 Redundant checks in factory contract](#34-redundant-checks-in-factory-contract)
* [4 Threat Model](#4-threat-model)
  * [4.1 Overview](#41-overview)
  * [4.2 Detail](#42-detail)
* [5 Tool based analysis](#5-tool-based-analysis)
  * [5.1 Mythril](#51-mythril)
  * [5.2 Odyssey](#52-odyssey)
* [6 Test Coverage Measurement](#6-test-coverage-measurement)
* [Appendix 1 - File Hashes](#appendix-1---file-hashes)
* [Appendix 2 - Severity](#appendix-2---severity)
  * [A.2.1 - Minor](#a21---minor)
  * [A.2.2 - Medium](#a22---medium)
  * [A.2.3 - Major](#a23---major)
  * [A.2.4 - Critical](#a24---critical)
* [Appendix 3 - Disclosure](#appendix-3---disclosure)

<!-- Don't remove this -->

## 1 Summary

ConsenSys Diligence conducted a security audit on ...

<!-- Provide a headline description of what you have audited and what the goal or purpose of the audited project is. 
Also add any other important context for the audit -->

### 1.1 Audit Dashboard

________________

<img height="120px" Hspace="30" Vspace="10" align="right" src="static-content/dashboard.png"/> 

#### Audit Details
* **Project Name:** Uniswap
* **Client Name:** Uniswap
* **Client Contact:** YYY
* **Auditors:** John Mardlin, Gonçalo Sá, Dean Pierce, Sergii Kravchenko, Daniel Luca 
* **GitHub :** ConsenSys/Uniswap-audit-internal-2018-12
* **Languages:** Vyper
* **Date:** XXX

#### Number of issues per severity


| | <img height="30px"  src="static-content/minor.png"/> | <img height="30px" src="static-content/medium.png"/>  | <img height="30px" src="static-content/major.png"/> | <img height="30px" src="static-content/critical.png"/> | 
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|
| <img height="30px"  src="static-content/open.png"/> | **2**  |  **2**  | **0**  | **0** |
| <img height="30px"  src="static-content/closed.png"/> | **0**  |  **0**  | **0**  | **0** |


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

The following documentation was available to the audit team:

- [White Paper](https://hackmd.io/C-DvwDSfSxuh-Gd4WKE_ig#) 
- [Docs](https://docs.uniswap.io/)
- [Runtime Verifications Formal Specification of Market Maker Model](https://github.com/runtimeverification/verified-smart-contracts/blob/uniswap/uniswap/x-y-k.pdf)

#### Scope

| Contract file name  |                SHA1 Hash                 |
| ------------------- | ---------------------------------------- |
| uniswap_exchange.vy | 9b058dc847040594bcac502effab5bda0de5fa3c |
| uniswap_factory.vy  | 97d49145ec4fc6aa31099cb51c0c2f69b6e487b7 |
|                     |                                          |

#### Design


### 1.4 Key Observations/Recommendations  

<!-- Positive Observations: Examples 
*  The design of the system is well documented
*  The code contains many helpful comments
*  The library architecture is efficient, and innovative
-->

<!-- Recommendations: Examples

* **Test coverage is incomplete:** Any contract system that is used on the main net should have as a minimum requirement a 100% test coverage.
* **Include negative test cases:** The majority of the tests are positive test cases, meaning that the tests confirm that the system works with an expected sequence of actions and inputs. The test suite should be expanded to include more negative scenarios to ensure that the safe checks within the contract system are working correctly.  
* **High Complexity:** The multiple library/contract system is complex in nature. Additional complexity is added by having ... 
* **Stages/Time periods:**  The stages and various timings should be defined more clearly in separated control functions. Any state changing function that is called should check first against those control functions and check if it is allowed to be executed. 
* **Fix all issues:** It is recommended to fix all the issues listed in the below chapters, at the very least the ones with severity Critical, Major and Medium. All issues have also been created as issues on [Github](LINK).
* **Function visibility:** Best practices such as explicitly specifying function visibility should be followed.
* **Improve Documentation:** Inconsistencies exist between the white paper/documentation and implementation.  
* 
-->



## 2 Issue Overview  

The following table contains all the issues discovered during the audit. The issues are ordered based on their severity. More detailed description on the  levels of severity can be found in Appendix 2. The table also contains the Github status of any discovered issue.

| Chapter | Issue Title  | Issue Status | Severity |
| ------------- | ------------- | ------------- | ------------- |
 | 3.1 | [Frontrunners can skim ~2.5% from every transaction.](#31-frontrunners-can-skim-~2-5%-from-every-transaction-) | <img height="30px" src="static-content/open.png"/>| <img height="30px" src="static-content/medium.png"/> | 
 | 3.2 | [The factory contract should use a constructor](#32-the-factory-contract-should-use-a-constructor) | <img height="30px" src="static-content/open.png"/>| <img height="30px" src="static-content/medium.png"/> | 
 | 3.3 | [Consider using transferFrom() in removeLiquidity() function.](#33-consider-using-transferfrom()-in-removeliquidity()-function-) | <img height="30px" src="static-content/open.png"/>| <img height="30px" src="static-content/minor.png"/> | 
 | 3.4 | [Redundant checks in factory contract](#34-redundant-checks-in-factory-contract) | <img height="30px" src="static-content/open.png"/>| <img height="30px" src="static-content/minor.png"/> | 




## 3 Issue Detail  


### 3.1 Frontrunners can skim ~2.5% from every transaction. 

| Severity  | Status | Link | Remediation Comment |
| ------------- | ------------- | ------------- | ------------- |
| <img height="30px" src="static-content/medium.png"/> |  <img height="30px" src="static-content/open.png"/> | [ issues/30](https://github.com/ConsenSys/Uniswap-audit-internal-2018-12/issues/30)| The issue is currently under review |


The default client contains a constant called `ALLOWED_SLIPPAGE` which states how much the price is allowed to change. This constant is set to 0.025 in `uniswap-frontend/src/pages/Swap/index.js:367`. This means that the price can go up ~2.5% from what the buyer expects, and the order will still get executed.

Any user on the Ethereum network has the ability to watch for new transactions being sent to the network. When the attacker sees a large victim transaction that they want to front run come in, they can create a similar transaction that would move the market up by no more than 2.5%. They then increase their gas fees to ensure that their order gets executed first. The attacker transaction executes, raising the price of the asset, and then the victim transaction executes at the higher price. The attacker is then free to exit the position immediately, pocketing the difference, having never exposed themselves to any risk.

Sophisicated front-runners will likely call these transactions from their own contract addresses to make sure they end up with the prices they expect, and don't collide with other front-runners.

This is a hard problem in general, and front-running of some form is always to be expected. By reducing the `ALLOWED_SLIPPAGE` (maybe even to 0), you can reduce the amount that front-runners can get for free from every transaction. The slippage value should probabally also be exposed to the user so they can opt in to a 0% slippage value if they choose.

Even with zero slippage, the attacker can still make a large buy order, watch the victims order fail, wait until they make a new order, and then exit their position. Doing this does carry the additional risk that the victim may be unhappy with the new price, which eliminates the attackers opportunity for zero risk skimming.

Bancor uses a front-running mitigation where there was a maximum gas value, so a user using the maximum gas value cannot be front-run by an attacker increasing the gas for their transaction. This approach might be worth looking into.

### 3.2 The factory contract should use a constructor 

| Severity  | Status | Link | Remediation Comment |
| ------------- | ------------- | ------------- | ------------- |
| <img height="30px" src="static-content/medium.png"/> |  <img height="30px" src="static-content/open.png"/> | [ issues/23](https://github.com/ConsenSys/Uniswap-audit-internal-2018-12/issues/23)| The issue is currently under review |


**Description**

The factory uses a public function for initialization instead of a constructor which might make it a target for griefing attacks.

Since there is no way to actually deploy the contract and call the `initializeFactory()` method within the same transaction without an additional contract designed for the effect (which seems to be inexistent in the current codebase) an attacker could grief deployments of the Uniswap factory by always front-running the initializing transaction after deployment.

Possibly even more worrisome would be a front-running transaction that would underhandedly change the exchange template code to something very similarly benign but that was, for example, an underhandedly backdoored version of the original.

**Remediation**

The easiest solution would be to turn the initialization method into the constructor of said contract.
Another possible remediation would be to introduce a "factory deployer" contract that executes both message calls in a single transaction.


### 3.3 Consider using transferFrom() in removeLiquidity() function. 

| Severity  | Status | Link | Remediation Comment |
| ------------- | ------------- | ------------- | ------------- |
| <img height="30px" src="static-content/minor.png"/> |  <img height="30px" src="static-content/open.png"/> | [ issues/31](https://github.com/ConsenSys/Uniswap-audit-internal-2018-12/issues/31)| The issue is currently under review |


To prevent issues like the BNB issue[1] from coming up in the future, consider using the same transfer function to add and remove liquidity. Hopefully this will ensure that if any non-compliant tokens get added in the future, it will be much more unlikely to get into a state where liquidity can be added, but not removed.

[1] https://twitter.com/UniswapExchange/status/1072286773554876416


### 3.4 Redundant checks in factory contract 

| Severity  | Status | Link | Remediation Comment |
| ------------- | ------------- | ------------- | ------------- |
| <img height="30px" src="static-content/minor.png"/> |  <img height="30px" src="static-content/open.png"/> | [ issues/24](https://github.com/ConsenSys/Uniswap-audit-internal-2018-12/issues/24)| The issue is currently under review |


**Description**

In `uniswap_factory.vy` there are some redundant assertions:



[contracts/uniswap_factory.vy:L14](https://github.com/Uniswap/contracts-vyper/blob/957f7aba57cec5d87824312dd3dd6484e0220086/contracts/uniswap_factory.vy#L15)

```Solidity
    assert template != ZERO_ADDRESS
```



and 



[contracts/uniswap_factory.vy:L20](https://github.com/Uniswap/contracts-vyper/blob/957f7aba57cec5d87824312dd3dd6484e0220086/contracts/uniswap_factory.vy#L21)

```Solidity
    assert self.exchangeTemplate != ZERO_ADDRESS
```



Due to the fact that the Vyper compiler actually asserts that the code size at the specified exchange address (through the use of `EXTCODESIZE`) is bigger than `0`, here:



[contracts/uniswap_factory.vy:L23](https://github.com/Uniswap/contracts-vyper/blob/master/contracts/uniswap_factory.vy#L24)

```Solidity
    Exchange(exchange).setup(token)
```



This meaning that the zeroth address as the exchange, having no code at all, would make the call to `setup()` fail.

**Remediation**

Remove the two assertions.



## 4 Threat Model

The creation of a threat model is beneficial when building smart contract systems as it helps to understand the potential security threats, assess risk, and identify appropriate mitigation strategies. This is especially useful during the design and development of a contract system as it allows to create a more resilient design which is more difficult to change post-development. 

A threat model was created during the audit process in order to analyze the attack surface of the contract system and to focus review and testing efforts on key areas that a malicious actor would likely also attack. It consists of two parts a high level design diagram that help to understand the attack surface and a list of threats that exist for the contract system. 

### 4.1 Overview 


### 4.2 Detail



## 5 Tool based analysis 

The issues from the tool based analysis have been reviewed and the relevant issues have been listed in chapter 3 - Issues. 


### 5.1 Mythril 

<img height="120px" align="right" src="static-content/mythril.png"/>

Mythril is a security analysis tool for Ethereum smart contracts. It uses concolic analysis to detect various types of issues. The tool was used for automated vulnerability discovery for all audited contracts and libraries. More details on Mythril's current vulnerability coverage can be found [here](https://github.com/ConsenSys/mythril/wiki).

The raw output of the Mythril vulnerability scan can for each contract:

* [uniswap_exchange.vy](./tool-output/mythril/mythril_output_exchange.md)
* [uniswap_factory.vy](./tool-output/mythril/mythril_output_factory.md)

### 5.2 Odyssey 

<img height="120px" align="right" src="static-content/odyssey.png"/>

Odyssey is an audit tool that acts as the glue between developers, auditors and tools. It leverages Github as the platform for building software and aligns to the approach that quality needs to be addressed as early as possible in the development life cycle and small iterative security activities spread out through development help to produce a more secure smart contract system.
In its current version Odyssey helps to better communicate audit issues to development teams and to successfully close them.



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

