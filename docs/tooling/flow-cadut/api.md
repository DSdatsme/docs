# Flow Cadence Utilities API Reference

## Imports

### `extractImports(code)`

#### Arguments

| Name   | Type   | Description           |
| ------ | ------ | --------------------- |
| `code` | string | Cadence template code |

#### Returns

| Type     | Description                                      |
| -------- | ------------------------------------------------ |
| `object` | contract name as key and import address as value |

#### Usage

```javascript
import { extractImports } from "@onflow/flow-cadut";

const code = `
  import Message from 0x01
  import Utilities from 0x02
  
  pub fun main(){
    Utilities.log(Message.hello)
  }
`;
const imports = extractImports(code);

console.log(imports);
/*
 *  Line above shall show the following:
 *  {
 *     "Message": "0x01",
 *     "Utilities": "0x02"
 *   }
 * */
```

### `missingImports(code, addressMap)`

Given Cadence code template and addressMap, returns an array of missing contract imports

#### Arguments

| Name         | Type                      | Description                      |
| ------------ | ------------------------- | -------------------------------- |
| `code`       | string                    | Cadence template code            |
| `addressMap` | [AddressMap](#AddressMap) | addressMap for provided template |

#### Returns

| Type    | Description                                                   |
| ------- | ------------------------------------------------------------- |
| `array` | array strings, representing names of missing contract imports |

#### Usage

```javascript
import { missingImports } from "@onflow/flow-cadut";

const code = `
  import Message from 0x01
  import Utilities from 0x02
  
  pub fun main(){
    Utilities.log(Message.hello)
  }
`;

const missing = missingImports(code, {});
console.log({ missing });
```

### `report(list, prefix)`

Reports missing imports via `console.error` with format:

```
[prefix] Missing Imports for contracts: [ Contract_1, Contract_2 ]
```

#### Arguments

| Name     | Type   | Optional | Description                      |
| -------- | ------ | -------- | -------------------------------- |
| `list`   | array  |          | list of missing contract imports |
| `prefix` | string | ✅       | Default: `""`                    |

#### Usage

```javascript
import { missingImports, report } from "@onflow/flow-cadut";
const code = `
      import Message from 0x01
      
      pub fun main(){}
    `;
const list = missingImports(code, {});
report(list);
```

### `reportMissingImports(code, addressMap, prefix)`

Checks and reports missing contracts by matching code and addressMap in format:

```
[prefix] Missing Imports for contracts: [ Contract_1, Contract_2 ]
```

#### Arguments

| Name         | Type                      | Optional | Description                                      |
| ------------ | ------------------------- | -------- | ------------------------------------------------ |
| `code`       | string                    |          | Cadence template code to check                   |
| `addressMap` | [AddressMap](#AddressMap) | ✅       | addressMap for imported contracts. Default: `{}` |
| `prefix`     | string                    | ✅       | message prefix. Default: `""`                    |

#### Usage

```javascript
import { missingImports, report } from "@onflow/flow-cadut";

const code = `
  import Message from 0x01
  
  pub fun main(){}
`;

reportMissingImports(code);
```

### `replaceImportAddresses(code, addressMap)`

Replaces import statements in provided Cadence templates with corresponding values from addressMap

#### Arguments

| Name         | Type                      | Description                                       |
| ------------ | ------------------------- | ------------------------------------------------- |
| `code`       | string                    | Cadence template code                             |
| `addressMap` | [AddressMap](#AddressMap) | AddressMap to use map contract names to addresses |

#### Returns

| Type   | Description                              |
| ------ | ---------------------------------------- |
| string | Updated template with replaced addresses |

#### Usage

```javascript
import { replaceImportAddresses } from "@onflow/flow-cadut";

const code = `
  import Messages from 0x01
  
  pub fun main(){}
`;
const addressMap = {
  Message: "0xf8d6e0586b0a20c7",
};
const replaced = replaceImportAddresses(code, addressMap);
console.log({ replaced });
```

## Arguments

### `mapArgument(type, value)`

Converts provided value to `sdk` argument.

📣 Best usage of this method is with combination of `query`/`mutate` methods from Javascript SDK.

#### Arguments

| Name    | Type   | Description                               |
| ------- | ------ | ----------------------------------------- |
| `type`  | string | Cadence type represented as string        |
| `value` | any    | correspondent value to use for conversion |

#### Returns

| Type                  | Description    |
| --------------------- | -------------- |
| [Argument](#Argument) | `sdk` argument |

#### Usage

```javascript
import { query, config } from "@onflow/fcl";
import { mapArgument } from "@onflow/flow-cadut";

(async () => {
  config().put("accessNode.api", "https://rest-testnet.onflow.org");

  const cadence = `
    pub fun main(message: String): String{
      return message
    }
`;

  // Script expects a single argument of type "String"
  const message = mapArgument("String", "Hello from Cadence!");

  // "args" shall return array of arguments.
  // We will pass "message" value into it
  const args = () => [message];

  const result = await query({ cadence, args });
  console.log(result);
})();
```

### `mapArguments(schema, values)`

Converts provided values to `sdk` arguments.

📣 Best usage of this method is with combination of `query`/`mutate` methods from Javascript SDK.

#### Arguments

| Name     | Type          | Description                                         |
| -------- | ------------- | --------------------------------------------------- |
| `schema` | array[string] | Array of Cadence types represented as string        |
| `values` | array[any]    | array of correspondent values to use for conversion |

#### Returns

| Type                         | Description              |
| ---------------------------- | ------------------------ |
| array[[Argument](#Argument)] | array of `sdk` arguments |

#### Usage

```javascript
import { query, config } from "@onflow/fcl";
import { mapArgument } from "@onflow/flow-cadut";

(async () => {
  config().put("accessNode.api", "https://rest-testnet.onflow.org");

  const cadence = `
    pub fun main(message: String, amount: Int): Int{
      log(message)
      return amount
    }
`;

  // Script expects multiple arguments - "String" and "Int"
  const schema = ["String", "Int"];
  // These are the values we will convert to arguments
  const values = ["Hello from Cadence", 1337];
  // mapArguments will return an array, no extra steps are required
  const args = () => mapArguments(schema, values);

  const result = await query({ cadence, args });
  console.log(result);
})();
```

### `mapValuesToCode(code, values)`

This method will extract argument types from provided Cadence code and then converts
values to corresponding types, preparing them to be passed into `sdk.send`

📣 Best usage of this method is with combination of `query`/`mutate` methods from Javascript SDK.

#### Arguments

| Name     | Type   | Description                |
| -------- | ------ | -------------------------- |
| `code`   | string | Cadence template code      |
| `values` | array  | array of values to process |

#### Returns

| Type  | Description              |
| ----- | ------------------------ |
| array | array of `sdk` arguments |

#### Throws

This method will throw an error if user would fail to provide required amount of arguments

```javascript
import { query, config } from "@onflow/fcl";
import { mapValuesToCode } from "@onflow/flow-cadut";

(async () => {
  config().put("accessNode.api", "https://rest-testnet.onflow.org");

  const cadence = `
    pub fun main(metadata: {String:String}, key: String):String {
      return metadata[key]!
    }
`;

  const result = await query({
    cadence,
    args: () =>
      mapValuesToCode(cadence, [
        { language: "Cadence", languageRating: "Cadence is Awesome 🤟" },
        "languageRating",
      ]),
  });

  console.log(result);
})();
```

## Parser

### `getTemplateInfo(code)`

Parses the code and returns [TemplateInfo](#TemplateInfo)

#### Arguments

| Name   | Type   | Description                      |
| ------ | ------ | -------------------------------- |
| `code` | string | Cadence template code to process |

#### Usage

```javascript
import { getTemplateInfo } from "@onflow/flow-cadut";

const script = `
  pub fun main(message:String):String{
    return 42
  }
`;
const info = getTemplateInfo(script);

/*
 * "info" will contain an object:
 *   {
 *     type: "script",
 *     args: [ 'message:String' ]
 *   }
 */
console.log({ info });
```

### `extractSigners(code)`

Parses the code and returns array of [SignerPair](#SignerPair)

#### Arguments

| Name   | Type   | Description                      |
| ------ | ------ | -------------------------------- |
| `code` | string | Cadence template code to process |

#### Returns

| Type                      | Description                          |
| ------------------------- | ------------------------------------ |
| [SignerPair](#SignerPair) | String representation of signer pair |

#### Usage

```javascript
import { extractSigners } from "@onflow/flow-cadut";

const script = `
  pub fun main(){
    log("nothing to see here :)")
  }
`;
const signers = extractSigners(script);
console.log({ signers });
```

### `extractScriptArguments(code)`

Parses the code and returns array of [ArgumentPair](#ArgumentPair)

#### Arguments

| Name   | Type   | Description                      |
| ------ | ------ | -------------------------------- |
| `code` | string | Cadence template code to process |

#### Returns

| Type                          | Description                            |
| ----------------------------- | -------------------------------------- |
| [ArgumentPair](#ArgumentPair) | String representation of argument pair |

#### Usage

```javascript
import { extractScriptArguments } from "@onflow/flow-cadut";

const script = `
  pub fun main(message: String, metadata: {String:String}){
    log(message)
  }
`;
const args = extractScriptArguments(script);
console.log({ args });
```

### `extractTransactionArguments(code)`

Parses the code and returns array of [ArgumentPair](#ArgumentPair)

#### Arguments

| Name   | Type   | Description                      |
| ------ | ------ | -------------------------------- |
| `code` | string | Cadence template code to process |

#### Returns

| Type                          | Description                            |
| ----------------------------- | -------------------------------------- |
| [ArgumentPair](#ArgumentPair) | String representation of argument pair |

#### Usage

```javascript
import { extractTransactionArguments } from "@onflow/flow-cadut";

const tx = `
  transaction(message: String, metadata: {String:String}){
    prepare(signer:AuthAccount){
      
    }
  }
`;
const args = extractTransactionArguments(tx);
console.log({ args });
```

### `extractContractName(code)`

Parses the code and returns contract name

#### Arguments

| Name   | Type   | Description                      |
| ------ | ------ | -------------------------------- |
| `code` | string | Cadence template code to process |

#### Returns

| Type   | Description                                   |
| ------ | --------------------------------------------- |
| string | name of the contract defined in template code |

#### Usage

```javascript
import { extractContractName } from "@onflow/flow-cadut";

const contract = `
  pub contract HelloWorld{
    init(){}
  }
`;
const name = extractContractName(contract);
console.log({ name });
```

### `splitArgs(pair)`

Splits [ArgumentPair](#ArgumentPair) into array of two items

#### Arguments

| Name   | Type                          | Description                       |
| ------ | ----------------------------- | --------------------------------- |
| `pair` | [ArgumentPair](#ArgumentPair) | argument pair in form of a string |

#### Returns

| Type  | Description                                                  |
| ----- | ------------------------------------------------------------ |
| array | first item is a name, second - string representation of type |

#### Usage

```javascript
import { splitArgs } from "@onflow/flow-cadut";
const simplePair = "message:String";
const metaPair = "metadata: {String:String}";

const simple = splitArgs(simplePair);
const meta = splitArgs(metaPair);

console.log({ simple, meta });
```

### `argType(pair)`

Splits [ArgumentPair](#ArgumentPair) and returns type of the argument

#### Arguments

| Name  | Type                          | Description                       |
| ----- | ----------------------------- | --------------------------------- |
| `pair | [ArgumentPair](#ArgumentPair) | argument pair in form of a string |

#### Returns

| Type   | Description                            |
| ------ | -------------------------------------- |
| string | string representation of argument type |

#### Usage

```javascript
import { argType } from "@onflow/flow-cadut";

const simplePair = "message:String";
const metaPair = "metadata: {String:String}";

const simple = argType(simplePair);
const meta = argType(metaPair);

console.log({ simple, meta });
```

### `getArrayType(type)`

Extracts item type from array type

#### Arguments

| Name   | Type   | Description                         |
| ------ | ------ | ----------------------------------- |
| `type` | string | string representation of Array type |

#### Returns

| Type   | Description                        |
| ------ | ---------------------------------- |
| string | string representation of item type |

#### Usage

```javascript
import { getArrayType } from "@onflow/flow-cadut";

const simpleType = getArrayType("[String]");
const complexType = getArrayType("[{String: String}]");

console.log({ simpleType, complexType });
```

### `getDictionaryTypes(type)`

Extracts key and value types from Dictionary type

#### Arguments

| Name   | Type   | Description                              |
| ------ | ------ | ---------------------------------------- |
| `type` | string | string representation of Dictionary type |

#### Returns

| Type  | Description                                                                   |
| ----- | ----------------------------------------------------------------------------- |
| array | array of strings - first item for the `key` type, second for the `value` type |

#### Usage

```javascript
import { getDictionaryTypes } from "@onflow/flow-cadut";

const type = "{String: UFix64}";
const types = getDictionaryTypes(type);
const [keyType, valueType] = types;

console.log({ keyType, valueType });
```

## Generator

### `processFolder(input, output, options)`

Recursively goes through `input` folder and generates code for found contracts, transactions and scripts.
Write files under `output` path.

#### Arguments

| Name      | Type   | Optional | Description                       |
| --------- | ------ | -------- | --------------------------------- |
| `input`   | string |          | path to the input folder          |
| `output`  | string |          | path to output folder             |
| `options` | object | ✅       | additional options. Default: `{}` |

#### Options

| Name         | Type   | Optional | Description                                    |
| ------------ | ------ | -------- | ---------------------------------------------- |
| `dependency` | string | ✅       | interactions dependency. Default: `flow-cadut` |

#### Usage

```javascript
import path from "path";
import { processFolder } from "@onflow/flow-cadut";

(async () => {
  const input = path.resolve("./cadence");
  const output = path.resolve("./src/generated/localRegistry");

  await processFolder(input, output);
  console.log("✅ Done!");
})();
```

### `processGitRepo(url, output, branch, options)`

Fetches GitHub repository from provided `url` and `branch`. Then generates code for found contracts, transactions and scripts.
Write files under `output` path.

#### Arguments

| Name      | Type   | Optional | Description                       |
| --------- | ------ | -------- | --------------------------------- |
| `url`     | string |          | url to GitHub repo                |
| `output`  | string |          | path to output folder             |
| `branch`  | string | ✅       | branch to use. Default: `main`    |
| `options` | object | ✅       | additional options. Default: `{}` |

#### Options

| Name         | Type   | Optional | Description                                    |
| ------------ | ------ | -------- | ---------------------------------------------- |
| `dependency` | string | ✅       | interactions dependency. Default: `flow-cadut` |

#### Usage

```javascript
import path from "path";
import { processGitRepo } from "@onflow/flow-cadut";

(async () => {
  const url = path.resolve("https://github.com/onflow/flow-core-contracts");
  const output = path.resolve("./src/generated/localRegistry");

  await processGitRepo(url, output);
  console.log("✅ Done!");
})();
```

## Interactions

### `setEnvironment(network, options)`

Sets `flow.network` config value

#### Arguments

| Name      | Type   | Optional | Description                         |
| --------- | ------ | -------- | ----------------------------------- |
| `network` | string | ✅       | network to use. Default: `emulator` |
| `options` | object | ✅       | extra options to adjust config      |

#### Network Variants

| Variants   | Description                                                  |
| ---------- | ------------------------------------------------------------ |
| `emulator` | Emulator instance running locally at "http://localhost:8080" |
| `testnet`  | Testnet access node at https://access-testnet.onflow.org     |
| `mainnet`  | Mainnet access node at "https://access.mainnet.onflow.org"   |

#### Options

| Name               | Type   | Optional | Description                        |
| ------------------ | ------ | -------- | ---------------------------------- |
| `options.port`     | number | ✅       | port for emulator. Default: `8080` |
| `options.endpoint` | string | ✅       | Access API endpoint.               |

> ⚠️ Attention: `endpoint` will override `port` and provided `network`. Don't mix endpoint from different `network` - it might lead to unexpected result.

#### Usage

```javascript
import { setEnvironment } from "@onflow/flow-cadut";

(async () => {
  await setEnvironment("testnet");
})();
```

### `getEnvironment()`

Returns a set of deployed contracts for current environment

#### Returns

| Type   | Description                                                                   |
| ------ | ----------------------------------------------------------------------------- |
| object | [AddressMap](#AddressMap) for known contracts deployed in current environment |

#### Usage

```javascript
import { setEnvironment, getEnvironment } from "@onflow/flow-cadut";

(async () => {
  await setEnvironment("mainnet");
  const addressMap = await getEnvironment();
  console.log({ addressMap });
})();
```

### `hexContract(code)`

Prepares Cadence template code to pass into deployment transaction.
Syntax sugar for `Buffer.from(code, "utf8").toString("hex");`

#### Arguments

| Name   | Type   | Description           |
| ------ | ------ | --------------------- |
| `code` | string | Cadence template code |

#### Returns

| Type   | Description                         |
| ------ | ----------------------------------- |
| string | hex representation of template code |

#### Usage

```javascript
import { hexContract } from "@onflow/flow-cadut";

const code = `
  pub contract HelloWorld{
    init(){}
  }
`;
const hexed = hexContract(code);
```

### `executeScript(args)`

Sends script to the network

#### Arguments

| Name   | Type                                | Description      |
| ------ | ----------------------------------- | ---------------- |
| `args` | [ScriptArguments](#ScriptArguments) | script arguments |

#### Returns

| Type                          | Description                 |
| ----------------------------- | --------------------------- |
| [ScriptResult](#ScriptResult) | Result of script execution. |

##### ScriptArguments

| Name         | Type                                             | Optional | Description                                                 |
| ------------ | ------------------------------------------------ | -------- | ----------------------------------------------------------- |
| `code`       | string                                           |          | Cadence code to execute                                     |
| `args`       | array[InteractionArgument](#InteractionArgument) | ✅       | Optional if script does not expect arguments. Default: `[]` |
| `addressMap` | [AddressMap](#AddressMap)                        | ✅       | address map to use for import replacement. Default: `{}`    |
| `limit`      | number                                           | ✅       | gas limit. Default: `100`                                   |

##### ScriptResult

Script result is represented as a tuple `[result, error]`

| Name     | Type  | Description                                                                   |
| -------- | ----- | ----------------------------------------------------------------------------- |
| `result` | any   | result of script execution. Type of this value depends on script return value |
| `error`  | error | Caught error. This will be `null` if script executed successfully             |

#### Usage

```javascript
import { executeScript } from "@onflow/flow-cadut";

(async () => {
  const code = `
    pub fun main():Int{
      return 42
    }
  `;

  const [result, err] = executeScript({ code });
  console.log({ result });
})();
```

##### Alias

This method is also available under alias `query`

### `sendTransaction`

Sends script to the network

#### Arguments

| Name               | Type                                          | Optional | Description                           |
| ------------------ | --------------------------------------------- | -------- | ------------------------------------- |
| `args`             | [TransactionArguments](#TransactionArguments) |          | transaction arguments                 |
| `waitForExecution` | boolean                                       | ✅       | wait for transaction execution or not |

> If `waitForExecution` flag is set to true, method will not return result until `fcl.tx(hash).onceExecuted()` is resolved

#### Returns

| Type                                    | Description                 |
| --------------------------------------- | --------------------------- |
| [TransactionResult](#TransactionResult) | Result of script execution. |

#### TransactionArguments

| Name         | Type                                                                            | Optional | Description                                                                                                                                                                 |
| ------------ | ------------------------------------------------------------------------------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `code`       | string                                                                          |          | Cadence code to execute                                                                                                                                                     |
| `payer`      | [AuthorizationFunction](../fcl-js/api.md#authorizationfunction) |          | The authorization function that returns a valid [AuthorizationObject](../fcl-js/api.md#authorizationobject) for the payer role.                             |
| `signers`    | [AuthorizationFunction]                                                         | ✅       | an array of [AuthorizationObject](../fcl-js/api.md#authorizationobject) representing transaction authorizers. Default: same as `payer`                      |
| `proposer`   | [AuthorizationFunction](../fcl-js/api.md#authorizationfunction) | ✅       | The authorization function that returns a valid [AuthorizationObject](../fcl-js/api.md#authorizationobject) for the proposer role. Default: same as `payer` |
| `args`       | [Any]                                                                           | ✅       | Optional if transactions does not expect arguments. Default: `[]`                                                                                                           |
| `addressMap` | [AddressMap](#AddressMap)                                                       | ✅       | address map to use for import replacement. Default: `{}`                                                                                                                    |
| `limit`      | number                                                                          | ✅       | gas limit. Default: `100`                                                                                                                                                   |

> When being used in the browser, you can pass built-in `fcl.authz` function to produce the authorization (signatures) for the current user.
> When calling this method from Node.js, you will need to supply your own custom authorization functions.

##### TransactionResult

Transaction result is represented as a tuple `[result, error]`

| Name                                                              | Type  | Description                                                                        |
| ----------------------------------------------------------------- | ----- | ---------------------------------------------------------------------------------- |
| [ResponseObject](../fcl-js/api.md#responseobject) | any   | result of transaction execution. Type of this value depends on script return value |
| `error`                                                           | error | Caught error. This will be `null` if script executed successfully                  |

#### Usage

```javascript
import { authenticate, currentUser, authz, config } from "@onflow/fcl";
import { sendTransaction } from "@onflow/flow-cadut";

config()
  .put("accessNode.api", "https://rest-testnet.onflow.org") // Configure FCL's Access Node
  .put("challenge.handshake", "https://fcl-discovery.onflow.org/testnet/authn") // Configure FCL's Wallet Discovery mechanism
  .put("0xProfile", "0xba1132bc08f82fe2"); // Will let us use `0xProfile` in our Cadence

(async () => {
  currentUser().subscribe(async (user) => {
    const code = `
    transaction {
      prepare(currentUser: AuthAccount) {
        log("hello")
      }
    }`;

    const [result] = await sendTransaction({ code, payer: authz });
    console.log({ result });
  });

  authenticate();
})();
```

##### Alias

This method is also available under alias `mutate`
