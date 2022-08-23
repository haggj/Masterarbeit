
0. Abstract
1. Introduction (4)
    - Current architecture
    - Implications and problems
2. Terms and Definitions (4)
2. Requirements (5)
    - Functional requirements
    - Security requirements
    - Non-functional requirements
3. Survey of potential solutions (12)
    - External encryption
    - Mutual encryption
    - Key server
    - Broadcast encryption
    - Hybrid encryption
4. Approach (10)
5. Evaluation (7)
6. Conclusion (3)


# 0. Abstract

# 1. Introduction
    Describes the problem statement, illustrates why this is a problem and describes the contribution the thesis makes in solving this problem. Optionally, it can give a short description (1-3 sentences each) of the remaining chapters. Good introductions are concise, typically no longer than 4 pages.
    The introduction reveals the full (but summarized) results of your work


# 2. Terms and Definitions
    Defines the fundamental concepts your thesis builds on. Your thesis implements a new type of parser generator and uses the term non-terminal symbol a lot? Here is where you define what you mean by it. The key to this chapter is to keep it very, very short. Whenever you can, don’t reinvent a description for an established concept, but reference a text book or paper instead.

# 3. Requirements

Following: https://www.uni-due.de/imperia/md/content/swe/papers/2010a-comparison-of-security-requirements-engineering-methods.pdf


There are three major goals in the context of this thesis:

1. Encrypt log data, s.t. overseer can not access logs (E2E encryption, confidentiality -> security goal)  
   Defending against `hontest-but-curions server`:
      - attacker follows protocol of overseer to store and load data
      - attacker attempts to analyze all legitimately received information (e.g. analyzes logs and policies stored in the database)
      - can collude with others (e.g. a malicious data owner)
      - passive attacker
      - similar to section 12.2 in https://sci-hub.ru/https://doi.org/10.1016/B978-0-12-805394-2.00012-X

2. Data owner needs to be able to share and revoke log data with others (-> functional goal)

3. Defend against a `malicious data owner` (integrity -> security goal):
      - attacker does not follow the protocol and tries to manipulate data logs (integrity and authenticity)
      - attacker tries to share manipulated logs with others (e.g. because attacker wants to discredit others)
      - active attacker
      - similar to section 3.1 https://mediatum.ub.tum.de/doc/1615228/7emnvw4k3nyma9pbthb37cnc4.ease2021-29.pdf


## Functional Requirements

    what the system does (p.11)


1. Define stakeholder goals

Already Implemented:
  - logs are created upon accessing private data
  - data owner can access their logs
  - data owner can restrict data consumers from accessing their private data

This thesis:
  - data owner wants to share logs with others
  - data owner wants to revoke access to logs

2. Goals into stakeholder requirements
  - Once a log was created, only the owner can access the content of the log
  - The owner of a log can share the logs with other users within the system, s.t. after sharing a defined set of users can access the logs
  - The owner of a log can revoke the access to the logs, s.t. after revocation only the defined set of users can access the logs
3. Stakeholder requirements into functional system requirements



## Non-Functional Requirements

    global requirements on its development or operational costs, performance, reliability, maintainability, portability, robustness and the like (p.11)

1. Define stakeholder goals
2. Goals into stakeholder requirements
3. Stakeholder requirements into non-functional system requirements

- Trust
    - Avoid trusted entities -> Do not introduce additional trusted entities

- Maintainability
    - Simplicity of design -> ?
    - Documentation -> Create a documentation of the implemented features

- Portability (e.g. to new platforms, languages, ...)
    - Standardized algorithms -> do not user crypto algorithms with are not recommended (https://www.bsi.bund.de/SharedDocs/Downloads/EN/BSI/Publications/TechGuidelines/TG02102/BSI-TR-02102-1.pdf?__blob=publicationFile&v=10) and which are not part of WebCrypto API (https://developer.mozilla.org/en-US/docs/Web/API/SubtleCrypto)
  
- Availability 
    - shared data is available even if the sharing user is not online -> ?

- Usability

    "Usability requirements cannot be directed verified, since they involve subjective behaviors that often have to be collected over time." (https://www.sciencedirect.com/topics/computer-science/usability-requirement)
    Usually we have a trade-off between Security and Usability. In this context the complexity of security is a key-driver to improve usability. By integrating the error-prone cryptography tasks into the application we can increase the usability and thus the acceptance of the toolchain. However, this comes with the cost of increased design and implementation effort.

    - Avoid complexity -> stick to "principle of least effort" and decrease the amount of clicks

      "The “principle of least effort “essentially states that people will do the least amount of work to get something done. This can apply to the amount of thought, time, energy or even key strokes- at the end of the day every human prefers the path of least resistance." (https://dzone.com/articles/the-principle-of-least-effort-an-integral-part-of)

    - Visualization -> UI for shared data, just as for own logs

- Performance
    - Minimize resource utilization -> avoid redundant data within the database


  
## Security Requirements

    We consider security requirements to be a part of the non-functional-requirements (p.11)

1. Define stakeholder security goals ("security concerns towards an asset")
  - CIA triad
  - "too vague to be be verifiable"
  - Example: "For example, the customers of a bank may have the
confidentiality goal that their financial situation remains
confidential"
2. Define security requirements ("concretize security goals")
  - information
  - counter-stakeholder
  - circumstances
  - Example: "In the banking example, a customer’s security requirement states that the exact balance of his or her account
must not become known to arbitrary bank employees or
other customers. Here, the information is the account balance, and the counter-stakeholders are bank employees and
other customers. The circumstances describe, e.g., that an
authorized bank employee who needs to know the balance
for a specific purpose may nevertheless get to know it, or
that the balance must be kept confidential for at least
50 years after the account has been closed"

1. Goals
  - Logs are E2E encrypted (confidentiality+authenticity)
    E2EE (https://eprint.iacr.org/2022/449.pdf): "Unlike standard notions of encryption, we require
that protocols and systems claiming “endness’ must be explicit about what
endpoints are within any particular application, and how users can verify that they are in fact engaging with the appropriate endpoint."

    Good source: https://www.ietf.org/id/draft-knodel-e2ee-definition-05.html#name-end-to-end-principle
  - Logs are integrity protected by the issuer (integrity)

2. Requirements
  - Logs are E2E encrypted (https://en.wikipedia.org/wiki/End-to-end_encryption)
    - information: The content of the logs can only be known by communicating users (encryption endpoints). Encryption endpoints: Monitor and data owner if logs are not shared. Data owner and shared entity if logs are shared. Moreover E2E requires authenticated encryption, because otherwise a third party could inject malicious data (https://eprint.iacr.org/2022/449.pdf)
    - counter-stakeholder: honest-but-curious overseer, 
    - circumstances: sharing of logs -> dynamic encryption endpoints
-  Logs are integrity protected (integrity)
    - information: decrypted log files can not be modified by the owner or by a shared user
    - counter-stakeholder: malicious data owner
    - circumstances: monitor is allowed to initially create logs (-> needs to be trusted because the monitor could potentially create malicious logs)

## Consistent System Requirements

1. collect all functional and non-functional requirements
2. resolve conflicts between them to obtain consistent system requirements 


# 4. Survey of potential solutions
    For me this is chapter is similar to Related Work

    Collects descriptions of existing work that is related to your work. Related, in this sense, means aims to solve the same problem or uses the same approach to solve a different problem. This chapter typically reads like a structured list. Each list item summarizes a piece of work (typically a research paper) briefly and explains the relation to your work. This last part is absolutely crucial: the reader should not have to figure out the relation himself. Is your piece better from some perspective? More generalizable? More performant? Simpler? It is ok if it is not, but I want you to tell me.

**Easy solution:**  
Each entity has a public-key pair.  Each data log is encrypted with the public key, s.t. only data owner can decrypt (resolves goal 1). Goal 2 can be solved by encrypting signed data. After each decryption this signed data need to be verified.

**Problems:**
1. How could data be shared, e.g. how to resolve goal 2 if data logs can only be decrypted by the data owner?
2. How can encrypted data be stored/queried on the server?

## 4.1 External Encryption
Encrypt data log with public key of target and send externally (e.g. via email)

  - #️⃣ Security
    - ❌ encryption is responsibility of user -> error prone 
    - ✅ can be realized using standard crypto 
    - ✅ simplicity
  - ❌ Usability: 
    - ❌ not user-friendly, because decryption, signature checks and visualization needs to be handled externally
    - ❌ encryption is responsibility of user -> error prone
  - ❌ Data quality: 
    - ❌ data changes not reflected in already shared data
    - ❌ redundant data in the system if logs are shared with multiple entities

## 4.2 Mutual Encryption
Data owner re-encrypts the log for the target. The re-encrypted data is sent back to overseer, which saves them in a "sharedLog" table

  - ✅ Security
    - ✅ can be realized using standard crypto 
    - ✅ simplicity
  - ✅ Usability: 
    - ✅ data is handled within the system 
  - ❌ Data quality: 
    - ❌ data changes not reflected in already shared data
    - ❌ redundant data in the system if logs are shared with multiple entities

## 4.3 Key-Server
Encrypt logs with unique symmetric key and store them in database. The symmetric key is stored on a trusted key server, which hands out the key to authorized users: After creation only the owner is allowed to access the key. The owner can add/revoke permissions of others to access the key.

<img src="images/key-server.png" width="500"/>

  - #️⃣ Security
    - ✅  can be realized using standard crypto
    - #️⃣ requires a key server
    - #️⃣ getting more complex because during each decryption key server needs to be requested
  - ✅ Usability: 
    - ✅ data is handled within the system 
  - ✅ Data quality: 
    - ✅ each log is only stored once

## 4.4 Broadcast-Encryption
Re-encrypt data by means of a broadcast encryption scheme. Only a defined set of users can decrypt the data.
<img src="images/broadcast-encryption.png" width="500"/>

  - #️⃣ Security
    - #️⃣ requires a key distribution center (e.g. a trusted entity, could be included into revolori)
    - #️⃣ not standard crypto: requires porting of crypto libraries (at least to JS)
  - ✅ Usability: 
    - ✅ data is handled within the system 
  - ✅ Data quality: 
    - ✅ each log is only stored once

  
  **Challenges**:  
  - How to access private keys of users?
  - How to implement ABE in frontend?
    - critical part: implement bilinear pairing
      - C: https://crypto.stanford.edu/pbc/
      - Python: https://pypi.org/project/charm-crypto/ (using C lib under the hood -> not possible to use this via brython)
      Curve definitions: https://github.com/JHUISI/charm/blob/acb55513b244bfdebbfe715cec7b564c8e850779/charm/toolbox/pairingcurves.py
      - Rust: https://github.com/zcash-hackworks/bn
      - Rust (Fraunhofer): https://github.com/georgbramm/rabe-bn 
    - use web assembly to implement bilinear pairing efficiently in browser:
      1. Follow the reference implementation (e.g. AC17 in python, which relies on pbc)
      2. Outsource heavy computation to wasm (pairing) or use existing library (need to be converted to WASM)
    

**Ideas regarding problem 2**
- if solving problem 1 with a dedicated crypto scheme, chances are high that this crypto scheme does not support queries over ciphertexts
- schemes that allow queries over encrypted data are currently researched (e.g. homomorphic encryption)
- Alternative: Keep a relational database
  - encrypt only logs (e.g. as a json)
  - save id of owner along with encrypted logs
  - save ids of shared owners with encrypted logs (1:n realtion)
  - improvement: provide pseudonyms for user ids, which are managed by revolori

## 4.5 Hybrid Encryption

# Approach
    Outlines the main thing your thesis does. Your thesis describes a novel algorith for X? Your main contribution is a case study that replicates Y? Describe it here.

# Evaluation
    Describes why your approach really solves the problem it claims to solve. You implemented a novel algorithm for X? This chapter describes how you ran it on a dataset and reports the results you measured. You replicated a study? This chapter gives the results and your interpretations.

# Conclusion

    Short summary of the contribution and its implications. The goal is to drive home the result of your thesis. Do not repeat all the stuff you have written in other parts of the thesis in detail. Again, limit this chapter to very few pages. The shorter, the easier it is to keep consistent with the parts it summarizes.

