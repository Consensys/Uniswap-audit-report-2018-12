## 1 Issue Overview  

The following table contains all the issues discovered during the audit. The issues are ordered based on their severity. The table also contains the Github status of any discovered issue.

| Chapter | Issue Title  | Issue Status | Severity |
| ------------- | ------------- | ------------- | ------------- |
 | 2.1 | [Liquidity pool can be stolen in some tokens (e.g. ERC-777)](#31-liquidity-pool-can-be-stolen-in-some-tokens-(e-g--erc-777)) |  **Open** |  **Major** | 
 | 2.2 | [Frontrunners can skim ~2.5% from every transaction.](#32-frontrunners-can-skim-~2-5%-from-every-transaction-) |  **Open** |  **Medium** | 
 | 2.3 | [Gaps in test coverage](#33-gaps-in-test-coverage) |  **Open** |  **Minor** | 
 | 2.4 | [Consider using transferFrom() in removeLiquidity() function](#34-consider-using-transferfrom()-in-removeliquidity()-function) |  **Open** |  **Minor** | 
 | 2.5 | [Different 'deadline' behaviour](#35-different-deadline-behaviour) |  **Open** |  **Minor** | 
 | 2.6 | [Redundant checks in factory contract](#36-redundant-checks-in-factory-contract) |  **Open** |  **Minor** | 
 | 2.7 | [The factory contract should use a constructor](#37-the-factory-contract-should-use-a-constructor) |  **Open** |  **Minor** | 




## 2 Issue Detail


### 2.1 Liquidity pool can be stolen in some tokens (e.g. ERC-777)

| Severity  | Status |  Remediation Comment |
| ------------- | ------------- | ------------- |
|  **Major**  |  **Open**   | The issue is currently under review |


**Description**

If token allows making reentrancy on `transferFrom(address from, address to, uint tokens)` function by someone except the recipient, then all the liquidity funds might be stolen. For example, if token calls callback function of `from` address. It's irrelevant if reentrancy is done before or after the balances update.

**Attack**

Let's imagine we have a token that calls a callback function of `from` address on `transferFrom(address from, address to, uint tokens)` and allows `from` address to make a reentrancy.  We will consider the case when reentrancy is made after the token balances are updated. If token balances are updated after the reentrancy (e.g. ERC-777), the algorithm is even easier and requires fewer funds to steal liquidity pool.

In `tokenToTokenInput ` we have the following 2 lines of code 
```
assert self.token.transferFrom(buyer, self, tokens_sold)
tokens_bought: uint256 = Exchange(exchange_addr).ethToTokenTransferInput(min_tokens_bought, deadline, recipient, value=wei_bought)
```
Attacker(`buyer`) can make reentrancy on the first line here. 

1. Assume we have an exchange with a token that worth equally to ETH with liquidity pool equals (100 tokens, 100 ETH)
2. An attacker creates a fake Exchange (it will be the second exchange in `tokenToToken` transfers) that will receive ETH from the first exchange and behave like a normal exchange.
3. The attacker can buy 50 ETH for 100 tokens by using `tokenToTokenInput` function.
4. New liquidity pool should be (200 tokens, 50 ETH) but since the attacker makes reentrancy on `assert self.token.transferFrom(buyer, self, tokens_sold)` it will still be (200 tokens, 100 ETH).
5. While making reentrancy the attacker can buy 49.999 ETH for about 200 tokens using `tokenToEthSwapInput`.
6. After that, the liquidity pool should look like (400 tokens, 0.001 ETH)
7. Now the attacker can buy all the tokens for a very small amount of ETH.

This is not a very accurate algorithm that does not include fees and gas cost, but logic stays the same.

**Remediation**

Add mutex to all functions that make trades in order to prevent reentrancy.

### 2.2 Frontrunners can skim ~2.5% from every transaction.

| Severity  | Status |  Remediation Comment |
| ------------- | ------------- | ------------- |
|  **Medium**  |  **Open**   | The issue is currently under review |


The default client contains a constant called `ALLOWED_SLIPPAGE` which states how much the price is allowed to change. This constant is set to 0.025 in `uniswap-frontend/src/pages/Swap/index.js:367`. This means that the price can go up ~2.5% from what the buyer expects, and the order will still get executed.

Any user on the Ethereum network has the ability to watch for new transactions being sent to the network. When the attacker sees a large victim transaction that they want to front run come in, they can create a similar transaction that would move the market up by no more than 2.5%. They then increase their gas fees to ensure that their order gets executed first. The attacker transaction executes, raising the price of the asset, and then the victim transaction executes at the higher price. The attacker is then free to exit the position immediately, pocketing the difference, having never exposed themselves to any risk.

Sophisicated front-runners will likely call these transactions from their own contract addresses to make sure they end up with the prices they expect, and don't collide with other front-runners.

This is a hard problem in general, and front-running of some form is always to be expected. By reducing the `ALLOWED_SLIPPAGE` (maybe even to 0), you can reduce the amount that front-runners can get for free from every transaction. The slippage value should probabally also be exposed to the user so they can opt in to a 0% slippage value if they choose.

Even with zero slippage, the attacker can still make a large buy order, watch the victims order fail, wait until they make a new order, and then exit their position. Doing this does carry the additional risk that the victim may be unhappy with the new price, which eliminates the attackers opportunity for zero risk skimming.

Bancor uses a front-running mitigation where there was a maximum gas value, so a user using the maximum gas value cannot be front-run by an attacker increasing the gas for their transaction. This approach might be worth looking into.

### 2.3 Gaps in test coverage

| Severity  | Status |  Remediation Comment |
| ------------- | ------------- | ------------- |
|  **Minor**  |  **Open**   | The issue is currently under review |


**Description**

The test suite could be improved in certain areas. Different types of testing parameters and testing methodologies could be used to further extend its coverage.

**Remediation**

Specific areas that were identified as lacking proper coverage:

* Additional ERC20 contracts with edge case parameters set (e.g.: `decimals = 0 | 18 | MAX_UINT8`, `totalSupply = 0 | MAX_UINT256`, ...) as opposed to only testing with the `HAY` token
* Stress tests with some blackbox fuzzing with random amounts and parameters for ETH <-> ERC20 trades and ERC20 <-> ERC20 trades instead of fixed amounts.
* Do unit testing in exchange functions with proper state reset between each test as opposed to doing only end-to-end tests with persisting state.
* In the end-to-end tests more scenarios could be added to test for adverse conditions like big slippage.

### 2.4 Consider using transferFrom() in removeLiquidity() function

| Severity  | Status |  Remediation Comment |
| ------------- | ------------- | ------------- |
|  **Minor**  |  **Open**   | The issue is currently under review |


#### Description

To prevent issues like the BNB issue[1] from coming up in the future, consider using the same transfer function to add and remove liquidity. Hopefully this will ensure that if any non-compliant tokens get added in the future, it will be much more unlikely to get into a state where liquidity can be added, but not removed.

[1] https://twitter.com/UniswapExchange/status/1072286773554876416


### 2.5 Different 'deadline' behaviour

| Severity  | Status |  Remediation Comment |
| ------------- | ------------- | ------------- |
|  **Minor**  |  **Open**   | The issue is currently under review |


**Description**

Many functions in `Exchange` contract have `deadline` as a parameter. This parameter has the same description in every function. But some functions use `>` operator when checking for a deadline
``` 
assert deadline > block.timestamp
```
while other functions use `>=` operator.

**Remediation**

Change all `>` operators to `>=` when working with deadline parameter.
It does not really affect anything but keeps code more beautiful and consistent.


### 2.6 Redundant checks in factory contract

| Severity  | Status |  Remediation Comment |
| ------------- | ------------- | ------------- |
|  **Minor**  |  **Open**   | The issue is currently under review |


**Description**

In `uniswap_factory.vy` there are some redundant assertions:



[contracts/uniswap_factory.vy:L15](https://github.com/Uniswap/contracts-vyper/blob/957f7aba57cec5d87824312dd3dd6484e0220086/contracts/uniswap_factory.vy#L15)

```Solidity
    assert template != ZERO_ADDRESS
```



and 



[contracts/uniswap_factory.vy:L21](https://github.com/Uniswap/contracts-vyper/blob/957f7aba57cec5d87824312dd3dd6484e0220086/contracts/uniswap_factory.vy#L21)

```Solidity
    assert self.exchangeTemplate != ZERO_ADDRESS
```



Due to the fact that the Vyper compiler actually asserts that the code size at the specified exchange address (through the use of `EXTCODESIZE`) is bigger than `0`, here:



[contracts/uniswap_factory.vy:L24](https://github.com/Uniswap/contracts-vyper/blob/master/contracts/uniswap_factory.vy#L24)

```Solidity
    Exchange(exchange).setup(token)
```



This meaning that the zeroth address as the exchange, having no code at all, would make the call to `setup()` fail.

**Remediation**

Remove the two assertions.

### 2.7 The factory contract should use a constructor

| Severity  | Status |  Remediation Comment |
| ------------- | ------------- | ------------- |
|  **Minor**  |  **Open**   | The issue is currently under review |


**Description**

The factory uses a public function for initialization instead of a constructor which might make it a target for griefing attacks.

Since there is no way to actually deploy the contract and call the `initializeFactory()` method within the same transaction without an additional contract designed for the effect (which seems to be inexistent in the current codebase) an attacker could grief deployments of the Uniswap factory by always front-running the initializing transaction after deployment.

Possibly even more worrisome would be a front-running transaction that would underhandedly change the exchange template code to something very similarly benign but that was, for example, an underhandedly backdoored version of the original.

**Remediation**

The easiest solution would be to turn the initialization method into the constructor of said contract.
Another possible remediation would be to introduce a "factory deployer" contract that executes both message calls in a single transaction.



