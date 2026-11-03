# Active Record and transactions

Active Record maps rows to Ruby objects, but the database remains the source
of truth. Put important invariants in database constraints as well as model
validation.

```ruby
class User < ApplicationRecord
  validates :email, presence: true
  validates :email, uniqueness: true
end
```

The validation improves user feedback; a unique index prevents a race:

```ruby
add_index :users, :email, unique: true
```

Transactions make a group of writes atomic. They do not automatically make
external effects atomic. Commit the database change first, then use an
outbox or a carefully designed job for email, webhooks, and other side
effects.

Always inspect query plans for important paths and preload associations when
the access pattern is known. A loop that performs one query per record is an
N+1 query.
