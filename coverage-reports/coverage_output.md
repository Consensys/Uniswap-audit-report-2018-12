```console
$ python -m pytest -v
============================= test session starts ==============================
platform linux -- Python 3.7.2, pytest-4.0.0, py-1.7.0, pluggy-0.8.0 -- /usr/bin/python
cachedir: .pytest_cache
rootdir: /home/daniel/Development/github.com/ConsenSys/Uniswap-audit-internal-2018-12/tests, inifile:
plugins: mock-1.10.0, cov-2.6.0, asyncio-0.9.0
collecting ... collected 21 items

exchange/test_ERC20.py::test_ERC20 PASSED                                [  4%]
exchange/test_eth_to_token.py::test_swap_default PASSED                  [  9%]
exchange/test_eth_to_token.py::test_swap_input PASSED                    [ 14%]
exchange/test_eth_to_token.py::test_transfer_input PASSED                [ 19%]
exchange/test_eth_to_token.py::test_swap_output PASSED                   [ 23%]
exchange/test_eth_to_token.py::test_transfer_output PASSED               [ 28%]
exchange/test_factory.py::test_factory PASSED                            [ 33%]
exchange/test_liquidity_pool.py::test_initial_balances PASSED            [ 38%]
exchange/test_liquidity_pool.py::test_liquidity_pool PASSED              [ 42%]
exchange/test_token_to_eth.py::test_swap_input PASSED                    [ 47%]
exchange/test_token_to_eth.py::test_transfer_input PASSED                [ 52%]
exchange/test_token_to_eth.py::test_swap_output PASSED                   [ 57%]
exchange/test_token_to_eth.py::test_transfer_output PASSED               [ 61%]
exchange/test_token_to_exchange.py::test_swap_input PASSED               [ 66%]
exchange/test_token_to_exchange.py::test_transfer_input PASSED           [ 71%]
exchange/test_token_to_exchange.py::test_swap_output PASSED              [ 76%]
exchange/test_token_to_exchange.py::test_transfer_output PASSED          [ 80%]
exchange/test_token_to_token.py::test_swap_input PASSED                  [ 85%]
exchange/test_token_to_token.py::test_transfer_input PASSED              [ 90%]
exchange/test_token_to_token.py::test_swap_output PASSED                 [ 95%]
exchange/test_token_to_token.py::test_transfer_output PASSED             [100%]

========================== 21 passed in 84.32 seconds ==========================
```