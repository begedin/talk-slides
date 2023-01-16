---
title: Ecto Sandbox Plug (English)
css: [theme.css]

---
<div class="centered" markdown="1">
  <h1>Ecto Sandbox Plug!</h1>
  <h2>Nikola Begedin</h2>
  <h3>Bego Solutions</h3>
</div>

---
## DataCase -> ConnCase
- fast integration tests
- isolated

```elixir
def setup_sandbox(tags) do
  pid = Ecto.Adapters.SQL.Sandbox.start_owner!(
    SampleApp.Repo, shared: not tags[:async]
  )
  on_exit(fn ->
    Ecto.Adapters.SQL.Sandbox.stop_owner(pid)
  end)

  :ok
end
```

---
## Phoenix E2E tests
- Hound or Wallaby
- Also sandbox
- The browser is controlled by a process in the application supervisor
- Advantages
  - simple setup
	- everything is still elixir
- Disadvantages
	- javascript++ -> ease of use--
	- the frontend has to be part of the phoenix app
---
## What if frontend is a separate repo?
- Moguće!
---
## Phoenix.Ecto.SQL.Sandbox

> A plug to allow concurrent, transactional acceptance tests with 
> `Ecto.Adapters.SQL.Sandbox`

- `POST /sandbox`
  - checks out an Ecto sandbox process
  - returns sandbox id using `term_to_binary`
- `DELETE /sandbox { id }`
  - chesk in sandbox process under specified id
- Between
  - each request with a sandbox id in header becomes part of sandbox process
---
## Phoenix.Ecto.SQL.Sandbox

**In practice**

- we have a frontend app, for example React or Vue.js
- we have some e2e framework; Cypress, Playwright
- in `beforeEach` -> sandbox checkout
- all backend requests get a sandbox header
- in `afterEach` -> sandbox checkin

**Result**: the test is executing in a sandboxed transaction
---
## Key questions

**How does the E2E framework modify network requests?**

- Cypress -> `cy.intercept` (or previously, `cy.server`)
- Playwright -> `page.setExtraHTTPHeaders`
- Testcafe -> `RequestHook`

**What to do when the framework can't modify requests?**

- Frontend application code modification is necessary
- generate a sandbox id
- store in browser cookie, local storage, etc.
- application api client reads sandbox id, adds to requests
---
## What else can we do

**E2E ex_machina**

- `POST /api/test/insert`
- `POST /api/test/insert_list`

```typescript
it('lists todos', () => {
  cy.create('user', {}).then(user => {
    cy.create('todo', { user, title: 'Buy Milk' });
    cy.create('todo', { user, title: 'Write Homework' });
    cy.login(user);
  });

  cy.url().should('not.contain', 'login');

  cy.contains('Buy Milk');
  cy.contains('Write Homework');
});
```
---
<div class="centered" markdown="1">
  <h1>DEMO!</h1>
</div>
---
<div class="centered" markdown="1">
  <h1>Thank you!</h1>
  <h2>Questions?</h2>
</div>