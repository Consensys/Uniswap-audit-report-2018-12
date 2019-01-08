```console
$ docker run -v $(pwd):/tmp mythril/myth:0.19.11 -x -f /tmp/exchange.txt
==== Multiple Calls ====
SWC ID: 113
Type: Informational
Contract: MAIN
Function name: fallback
PC address: 1179
Estimated Gas Usage: 3869 - 7493
Multiple sends are executed in a single transaction. Try to isolate each external call into its own transaction, as external calls can fail accidentally or deliberately.
Consecutive calls:
Call at address: 1317

--------------------

==== Unchecked CALL return value ====
SWC ID: 104
Type: Informational
Contract: MAIN
Function name: fallback
PC address: 1183
Estimated Gas Usage: 4572 - 42196
The return value of an external call is not checked. Note that execution continue even if the called contract throws.
--------------------

==== External call to user-supplied address ====
SWC ID: 107
Type: Warning
Contract: MAIN
Function name: fallback
PC address: 1317
Estimated Gas Usage: 17110 - 95959
The contract executes a function call with high gas to a user-supplied address. Note that the callee can contain arbitrary code and may re-enter any function in this contract. Review the business logic carefully to prevent unanticipated effects on the contract state.
--------------------

==== Unchecked CALL return value ====
SWC ID: 104
Type: Informational
Contract: MAIN
Function name: fallback
PC address: 1321
Estimated Gas Usage: 17813 - 130662
The return value of an external call is not checked. Note that execution continue even if the called contract throws.
--------------------

==== Unchecked CALL return value ====
SWC ID: 104
Type: Informational
Contract: MAIN
Function name: fallback
PC address: 3184
Estimated Gas Usage: 3156 - 42848
The return value of an external call is not checked. Note that execution continue even if the called contract throws.
--------------------

==== Unchecked CALL return value ====
SWC ID: 104
Type: Informational
Contract: MAIN
Function name: fallback
PC address: 4001
Estimated Gas Usage: 3234 - 43112
The return value of an external call is not checked. Note that execution continue even if the called contract throws.
--------------------

==== Unchecked CALL return value ====
SWC ID: 104
Type: Informational
Contract: MAIN
Function name: fallback
PC address: 4889
Estimated Gas Usage: 3341 - 43591
The return value of an external call is not checked. Note that execution continue even if the called contract throws.
--------------------

==== Unchecked CALL return value ====
SWC ID: 104
Type: Informational
Contract: MAIN
Function name: fallback
PC address: 5764
Estimated Gas Usage: 3404 - 43747
The return value of an external call is not checked. Note that execution continue even if the called contract throws.
--------------------
```