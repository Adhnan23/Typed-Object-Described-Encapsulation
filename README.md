# `.tode` – Typed Object Described Encapsulation

**A compact, schema-aware, type-safe data format for APIs and structured data transport.**

`.tode` is designed to be **human-readable, smaller than JSON, and self-validating** before conversion into JSON. Think of it as **TypeScript for JSON payloads**.

> ⚠️ **Experimental:** Parser, validator, and tooling are **not implemented yet**. Contributions are welcome!

---

## 🔹 Key Features

* **Schema + Data** in one file
* **Typed**: strings, numbers, booleans, dates, enums
* **Containers**: `( )` for objects, `{ }` for arrays
* **Array validation**: exact length `[n]` or variable `[?]`
* **Enums**: inline values `<value1,value2,...>`
* **Optional fields**: `!field` syntax
* **Compact**: smaller than JSON for nested objects
* **Validation before parsing**: detect errors early
* **API-ready**: can be used as `Content-Type: application/tode`

---

## 🔹 Container & Syntax Overview with Examples

### 1️⃣ Objects `( )`

Objects (tuples) define **ordered fields**.

```plaintext
(name, age, email)
```

Example:

```plaintext
(karots, 23, karots@karots.lk)
```

* `name = "karots"`
* `age = 23`
* `email = "karots@karots.lk"`

---

### 2️⃣ Arrays `{ }`

Arrays contain **repeating values**.

```plaintext
{apple, banana, cherry}
```

Equivalent JSON:

```json
["apple", "banana", "cherry"]
```

---

### 3️⃣ Fixed-length array `[n]`

Enforce **exact number of elements**.

```plaintext
features[3]:{login, dashboard, analytics}
```

* Must contain exactly **3 items**.
* `[n]` ensures validation before parsing.

Equivalent JSON:

```json
["login", "dashboard", "analytics"]
```

---

### 4️⃣ Variable-length array `[?]`

Allows **0 or more elements**.

```plaintext
webhooks[?]{(id, url, enabled)}
```

Valid entries:

```plaintext
[ ] # empty
[1]:{(WHK-900, https://example.com/notify, true)}
[2]:{
    (WHK-901, https://example.com/alert, true),
    (WHK-902, https://example.com/deploy, false)
}
```

---

### 5️⃣ Enums `< >`

Restrict values to a fixed set.

```plaintext
theme<dark, light>
```

Valid:

```plaintext
dark
light
```

Invalid:

```plaintext
purple  # ❌ Error
```

Equivalent JSON:

```json
"theme": "dark"
```

---

### 6️⃣ Optional Fields `!field`

Optional fields may be omitted.

Schema:

```plaintext
(name, !age, email)
```

Valid examples:

```plaintext
(karots, !, karots@karots.lk)  # omit optional
(karots, 23, karots@karots.lk) # provide optional
(karots, karots@karots.lk)      # omit optional, order maintained
```

> ⚠️ When multiple optional fields exist, you must use `!` placeholders if needed to maintain order.

---

## 🔹 Full Example: `.tode` vs JSON

### `.tode`

```plaintext
app(
    name,
    version,
    releaseDate,
    features[?],
    config(
        theme<dark, light>,
        autosave,
        refreshInterval,
        allowedFileTypes[?],
        notifications(email, push, sms, webhooks[?](id, url, enabled)),
        security(encryption, 2FA, passwordPolicy(minLength, requireNumbers, requireCharacters, requireSymbols, expirationDays))
    )
):
(
    Project Atlas,
    2.4.1,
    2025-09-30,
    [6]:{real-time analytics, multi-user collaboration, machine learning recommendations, offline mode, role-based task routing, voice commands},
    (dark, true, 30,
       [7]:{pdf, docx, xlsx, png, jpg, csv, json},
       (true, true, true, [1]:{(WHK-900, https://example.com/notify, true)}),
       (AES-256, true, (12, true, true, true, 90))
    )
)
```

---

### Equivalent JSON

```json
{
  "app": {
    "name": "Project Atlas",
    "version": "2.4.1",
    "releaseDate": "2025-09-30",
    "features": [
      "real-time analytics",
      "multi-user collaboration",
      "machine learning recommendations",
      "offline mode",
      "role-based task routing",
      "voice commands"
    ],
    "config": {
      "theme": "dark",
      "autosave": true,
      "refreshInterval": 30,
      "allowedFileTypes": ["pdf", "docx", "xlsx", "png", "jpg", "csv", "json"],
      "notifications": {
        "email": true,
        "push": true,
        "sms": true,
        "webhooks": [
          {
            "id": "WHK-900",
            "url": "https://example.com/notify",
            "enabled": true
          }
        ]
      },
      "security": {
        "encryption": "AES-256",
        "2FA": true,
        "passwordPolicy": {
          "minLength": 12,
          "requireNumbers": true,
          "requireCharacters": true,
          "requireSymbols": true,
          "expirationDays": 90
        }
      }
    }
  }
}
```

---

## 🔹 Comparison

| Aspect          | `.tode`              | JSON                       |
| --------------- | -------------------- | -------------------------- |
| Size            | ~50% smaller         | Larger                     |
| Schema          | Embedded             | External (JSON Schema)     |
| Type Safety     | Strong               | Weak (runtime only)        |
| Arrays          | `[n]` enforced       | Runtime only               |
| Enums           | Inline `< >`         | Requires external schema   |
| Optional Fields | `!field`             | Not natively supported     |
| Readability     | Compact & structured | Verbose for nested objects |
| Validation      | Pre-parsing          | Runtime only               |

---

## 🔹 Current Status

| Feature                                           | Status            |
| ------------------------------------------------- | ----------------- |
| Containers `( )`, `{ }`                           | ✅ Defined         |
| Array length `[n]`, variable `[?]`                | ✅ Defined         |
| Enums `< >`                                       | ✅ Defined         |
| Optional fields `!field`                          | ✅ Defined         |
| Type inference                                    | ✅ Defined         |
| Parser / Validator                                | ❌ Not implemented |
| CLI tooling                                       | ❌ Not implemented |
| IDE support / syntax highlighting                 | ❌ Not implemented |
| API middleware (`Content-Type: application/tode`) | ❌ Not implemented |

> Help is welcome! Anyone can contribute parsers, CLI tools, or middleware.

---

## 🔹 Roadmap

1. **v2.1** – Optional fields fully supported, enums enforced
2. **v3.0** – JTS Parser & Validator (Node.js, Python)
3. **v4.0** – CLI tools: validate, convert `.tode` ↔ JSON
4. **v5.0** – Middleware for APIs and live validation

---

## 🔹 Contribution

We are looking for:

* Parser & validator implementation in JavaScript, Python, Rust, Go
* CLI tooling for `.tode`
* IDE/editor plugins
* Test files, examples, and sample APIs
* Spec improvements and enhancements

> Open an issue or pull request on GitHub!

---

## 🔹 License

MIT License © 2025 Karots
