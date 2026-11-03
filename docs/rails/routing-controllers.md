# Routing, controllers, and responses

Routes are the public map of an application. Keep them resource-oriented and
make authentication requirements visible from the route boundary.

```ruby
resources :orders, only: %i[index show create] do
  resource :cancelation, only: :create
end
```

The controller should coordinate the request, not become the domain model:

1. authenticate the caller;
2. authorize the requested resource;
3. validate and normalize parameters;
4. call the operation that owns the business rule;
5. render one documented response shape.

Use strong parameters as an input allowlist. Do not pass an entire request
parameter object into a model or service.

## Response discipline

Choose status codes consistently: `200` for a successful read, `201` for a
created resource, `204` for a successful empty response, `401` for missing or
invalid authentication, `403` for authenticated but forbidden access, and
`422` for a validly parsed but invalid operation.

Centralize error serialization so clients do not need a different parser for
each endpoint.
