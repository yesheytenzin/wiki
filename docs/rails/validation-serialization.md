# Validation and serialization

Validation protects an application boundary; database constraints protect the
data under concurrency. Use both when an invariant matters.

```ruby
class Signup
  include ActiveModel::Model

  attr_accessor :email, :password
  validates :email, presence: true, format: URI::MailTo::EMAIL_REGEXP
  validates :password, length: { minimum: 12 }
end
```

Validation should produce actionable errors without exposing internal details.
Normalize values deliberately—for example, lowercase an email only if the
business rule treats addresses case-insensitively.

## Serialization

Serialize an explicit response contract rather than exposing every model
column. Choose fields, associations, timestamps, and pagination metadata
intentionally. Avoid accidental N+1 queries by loading the associations the
serializer needs.

When a response changes, decide whether the change is backward-compatible. A
new optional field is usually safer than changing a field's type or meaning.

## Validation layers

Use the layer that owns the rule:

- **Parsing:** is the input shaped correctly?
- **Application:** is this operation allowed in the current state?
- **Model:** is the object valid for persistence?
- **Database:** can concurrent writers violate the invariant?
- **Provider:** did an external system accept the request?

Do not use a model validation to answer an authorization question. Do not use
application validation as the only protection against a uniqueness race.

## Serialization risks

Explicitly select fields and associations. A serializer that exposes the
entire model can leak internal flags, tokens, audit data, or future columns.
Avoid recursive associations and set a maximum collection size.

For money, timestamps, identifiers, and enum values, define representation
and timezone rules. A client should not have to guess whether an integer means
cents or currency units.

## Error contract

Return machine-readable codes and human-readable messages:

```json
{
  "errors": [
    { "field": "email", "code": "invalid", "message": "is invalid" }
  ]
}
```

Keep codes stable even when wording changes. Do not include SQL, stack traces,
or provider credentials in errors.

## Practice

Design a signup request and response. Test malformed JSON, unknown fields,
duplicate email under concurrency, password policy, unauthorized access, and
serialization of a user who owns private fields.
