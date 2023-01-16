---
title: Ecto Sandbox Plug (Hrvatski)
css: [theme.css]

---
<div class="centered" markdown="1">
  <h1>Ecto Sandbox Plug!</h1>
  <h2>Nikola Begedin</h2>
  <h3>Bego Solutions</h3>
</div>

---
## DataCase -> ConnCase
- brzi integracijski testovi
- izolirani

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
## Phoenix E2E testovi
- Hound ili Wallaby
- Također sandbox
- Browser je kontroliran preko processa u nadzornom stablu aplikacije
- Prednosti
  - jednostavan setup
	- sve je i dalje elixir
- Nedostaci
	- javascript++ -> lakoća korištenja--
	- frontend mora biti dio phoenix aplikacije
---
## Što ako je frontend drugi repo?
- Moguće!
---
## Phoenix.Ecto.SQL.Sandbox

> A plug to allow concurrent, transactional acceptance tests with 
> `Ecto.Adapters.SQL.Sandbox`

- `POST /sandbox`
  - izvrši checkout Ecto sandbox procesa
  - vrati sandbox id kao `term_to_binary`
- `DELETE /sandbox { id }`
  - izvrši checkin procesa s navedenim id-em
- Između
  - svaki zahtjev koji u headeru ima sandbox id postaje dio sandbox procesa
---
## Phoenix.Ecto.SQL.Sandbox

**U Praksi**

- imamo frontend app, npr React ili Vue.js
- imamo neki e2e framework, Cypress, Playwright
- u `beforeEach` -> sandbox checkout
- u sve zahtjeve na backend, dodaje se sandbox header
- u `afterEach` -> sandbox checkin

**Rezultat**: test se izvršava u izoliranoj transakciji
---
## Ključna pitanja

**Kako E2E framework modificira mrežne zahtjeve?**

- Cypress -> `cy.intercept` (ili nekad, `cy.server`)
- Playwright -> `page.setExtraHTTPHeaders`
- Testcafe -> `RequestHook`

**Što kad framework to ne može?**

- Potrebna modifikacija koda frontend aplikacije
- generiraj sandbox id
- spremi u cookie, local storage, etc.
- glavni API klijent aplikacije dodaje sandbox id u headere
---
## Što još možemo

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
  <h1>Hvala!</h1>
  <h2>Pitanja?</h2>
</div>