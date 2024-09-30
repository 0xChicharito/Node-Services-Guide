# Script for checking worker

\`**Worker 1-1 ETH**

```bash
curl --location 'https://heads.testnet-1.testnet.allora.network/api/v1/functions/execute'
--header 'Content-Type: application/json'
--data '{ "function_id": "bafybeigpiwl3o73zvvl6dxdqu7zqcub5mhg65jiky2xqb4rdhfmikswzqm", "method": "allora-inference-function.wasm", "parameters": null, "topic": "1", "config": { "env_vars": [ { "name": "BLS_REQUEST_PATH", "value": "/api" }, { "name": "ALLORA_ARG_PARAMS", "value": "ETH" } ], "number_of_nodes": -1, "timeout": 2 } }'
```

**Worker 1-3 BTC**

```bash
curl --location 'https://heads.testnet-1.testnet.allora.network/api/v1/functions/execute'
--header 'Content-Type: application/json'
--data '{ "function_id": "bafybeigpiwl3o73zvvl6dxdqu7zqcub5mhg65jiky2xqb4rdhfmikswzqm", "method": "allora-inference-function.wasm", "parameters": null, "topic": "3", "config": { "env_vars": [ { "name": "BLS_REQUEST_PATH", "value": "/api" }, { "name": "ALLORA_ARG_PARAMS", "value": "BTC" } ], "number_of_nodes": -1, "timeout": 2 } }'
```

**Worker 1 - 5 SOL**

````bash
curl --location 'https://heads.testnet-1.testnet.allora.network/api/v1/functions/execute'
--header 'Content-Type: application/json'
--data '{ "function_id": "bafybeigpiwl3o73zvvl6dxdqu7zqcub5mhg65jiky2xqb4rdhfmikswzqm", "method": "allora-inference-function.wasm", "parameters": null, "topic": "5", "config": { "env_vars": [ { "name": "BLS_REQUEST_PATH", "value": "/api" }, { "name": "ALLORA_ARG_PARAMS", "value": "SOL" } ], "number_of_nodes": -1, "timeout": 2 } }'```
````



**Worker 2-2 ETH**

```bash
curl --location 'https://heads.testnet-1.testnet.allora.network/api/v1/functions/execute'
--header 'Content-Type: application/json'
--data '{ "function_id": "bafybeigpiwl3o73zvvl6dxdqu7zqcub5mhg65jiky2xqb4rdhfmikswzqm", "method": "allora-inference-function.wasm", "parameters": null, "topic": "2", "config": { "env_vars": [ { "name": "BLS_REQUEST_PATH", "value": "/api" }, { "name": "ALLORA_ARG_PARAMS", "value": "ETH" } ], "number_of_nodes": -1, "timeout": 2 } }'
```

**Worker 2-4 BTC**

```bash
curl --location 'https://heads.testnet-1.testnet.allora.network/api/v1/functions/execute'
--header 'Content-Type: application/json'
--data '{ "function_id": "bafybeigpiwl3o73zvvl6dxdqu7zqcub5mhg65jiky2xqb4rdhfmikswzqm", "method": "allora-inference-function.wasm", "parameters": null, "topic": "4", "config": { "env_vars": [ { "name": "BLS_REQUEST_PATH", "value": "/api" }, { "name": "ALLORA_ARG_PARAMS", "value": "BTC" } ], "number_of_nodes": -1, "timeout": 2 } }'
```

**Worker 2 - 6 SOL**

```bash
curl --location 'https://heads.testnet-1.testnet.allora.network/api/v1/functions/execute'
--header 'Content-Type: application/json'
--data '{ "function_id": "bafybeigpiwl3o73zvvl6dxdqu7zqcub5mhg65jiky2xqb4rdhfmikswzqm", "method": "allora-inference-function.wasm", "parameters": null, "topic": "6", "config": { "env_vars": [ { "name": "BLS_REQUEST_PATH", "value": "/api" }, { "name": "ALLORA_ARG_PARAMS", "value": "SOL" } ], "number_of_nodes": -1, "timeout": 2 } }'
```
