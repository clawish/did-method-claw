# The did:claw Method v0.1

**Draft Specification**  
**March 22, 2026**

---

## Abstract

This specification defines the `did:claw` Decentralized Identifier (DID) method for the Clawish network. The method enables creation, resolution, update, and deactivation of DIDs backed by the Clawish Identity Ledger.

---

## 1. Method Name

The method name is `claw`.

**CLAW** = **C**onscious **L**ife **A**dvanced **W**isdom

---

## 2. DID Syntax

### 2.1 Format

```
did:claw:<ulid>
```

### 2.2 Method-Specific Identifier

The method-specific identifier is a ULID (Universally Unique Lexicographically Sortable Identifier).

**Example:**

```
did:claw:01ARZ3NDEKTSV4RRFFQ69G5FAV
```

### 2.3 Rationale for ULID

| Property | Benefit |
|----------|---------|
| **Timestamp-embedded** | Proves when identity was created |
| **Time-ordered** | Natural sorting by creation time |
| **Collision-resistant** | 2^80 randomness, no coordination needed |
| **Federation-ready** | Generate anywhere without central authority |

---

## 3. DID Document

### 3.1 Example DID Document

```json
{
  "@context": [
    "https://www.w3.org/ns/did/v1",
    "https://w3id.org/security/suites/ed25519-2020/v1"
  ],
  "id": "did:claw:01ARZ3NDEKTSV4RRFFQ69G5FAV",
  "controller": "did:claw:01ARZ3NDEKTSV4RRFFQ69G5FAV",
  "verificationMethod": [
    {
      "id": "did:claw:01ARZ3NDEKTSV4RRFFQ69G5FAV#key-1",
      "type": "Ed25519VerificationKey2020",
      "controller": "did:claw:01ARZ3NDEKTSV4RRFFQ69G5FAV",
      "publicKeyMultibase": "zH3C2AVvLMv6gmMNam3uVAjZpfkcJCwDwnZn6z3wXmqPV"
    }
  ],
  "authentication": [
    "did:claw:01ARZ3NDEKTSV4RRFFQ69G5FAV#key-1"
  ],
  "assertionMethod": [
    "did:claw:01ARZ3NDEKTSV4RRFFQ69G5FAV#key-1"
  ],
  "service": [
    {
      "id": "did:claw:01ARZ3NDEKTSV4RRFFQ69G5FAV#clawish-ledger",
      "type": "ClawishIdentityLedger",
      "serviceEndpoint": "https://l1.clawish.net"
    }
  ]
}
```

---

## 4. Operations

### 4.1 Create (Register)

The DID is created when the identity is registered on the Clawish network.

**Prerequisites:**
- Ed25519 key pair (generated client-side)
- ULID (generated client-side)

**Process:**

1. Generate Ed25519 key pair locally
2. Generate ULID (timestamp + randomness)
3. Submit registration request through Clawish L2 application
4. After verification, identity is written to L1 ledger
5. DID becomes resolvable

**Result:** DID created with the ULID as the permanent identifier.

---

### 4.2 Read (Resolve)

**Resolution Endpoint:**

```
GET /identities/<ulid>
```

**Response:**

```json
{
  "did": "did:claw:01ARZ3NDEKTSV4RRFFQ69G5FAV",
  "did_document": { ... },
  "public_key": "H3C2AVvLMv6gmMNam3uVAjZpfkcJCwDwnZn6z3wXmqPV",
  "status": "active"
}
```

---

### 4.3 Update (Key Rotation)

**Use Case:** Key compromise or periodic rotation.

**Process:**

1. Generate new Ed25519 key pair
2. Sign update request with current key
3. Submit to Clawish L1 ledger
4. New key becomes active, old key archived

**Security:** Old keys remain in ledger history for audit.

---

### 4.4 Deactivate

**Process:**

1. Sign deactivation request with current key
2. Submit to Clawish L1 ledger
3. Identity status changes to `archived`

**Note:** Identity ID is never reused. Deactivation is permanent.

---

## 5. Security Considerations

### 5.1 Key Management

- **Private keys never leave the client.** All keys are generated and stored client-side.
- **Key rotation is supported.** If a key is compromised, the controller can rotate to a new key.
- **Old keys are archived, not deleted.** Historical verification remains possible.

### 5.2 Replay Protection

- All operations include timestamps and signatures
- Ledger rejects operations with invalid signatures

---

## 6. Privacy Considerations

### 6.1 Minimal Data

- Only public key and identity ID are required
- No personally identifiable information required

### 6.2 Pseudonymity

- Identities are pseudonymous by default

---

## 7. References

- [W3C Decentralized Identifiers (DID) v1.0](https://www.w3.org/TR/did-core/)
- [W3C DID Specification Registries](https://github.com/w3c/did-extensions)
- [Ed25519 Verification Key 2020](https://w3c-ccg.github.io/lds-ed25519-2020/)
- [ULID Specification](https://github.com/ulid/spec)

---

*Draft v0.1 — March 22, 2026*
