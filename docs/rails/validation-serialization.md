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
