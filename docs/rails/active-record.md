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

## Associations

Associations express ownership and loading behavior:

```ruby
class Order < ApplicationRecord
  belongs_to :user
  has_many :line_items, dependent: :destroy
end
```

Choose `dependent` behavior deliberately. Destroying a parent may be correct
for private child data but dangerous for audit records or shared objects.
Validate ownership in the query, not only after loading a record.

## Transactions and side effects

Transactions make a group of writes atomic. They do not automatically make
external effects atomic. Commit the database change first, then use an
outbox or a carefully designed job for email, webhooks, and other side
effects. Do not hold a transaction open while calling a remote service.

```ruby
Order.transaction do
  order.update!(status: "paid")
  OutboxEvent.create!(kind: "order_paid", aggregate_id: order.id)
end
```

Always inspect query plans for important paths and preload associations when
the access pattern is known. A loop that performs one query per record is an
N+1 query.

## Common mistakes

- relying on model validation for uniqueness;
- using `find(params[:id])` without scoping to the authorized owner;
- loading an entire table into memory;
- adding callbacks that hide network calls or important writes;
- assuming a transaction protects work done by another service.
