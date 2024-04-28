---
title: Phoenix Live View
css: [theme.css]

---
<div class="centered" markdown="1">
  <h1>Phoenix Live View - SPA bez JS</h1>
  <h2>Nikola Begedin</h2>
  <h3>Bego Solutions | <a>bego.dev</a>
</div>
Note:

Pozdrav svima.
Ja sam Nikola i imam d.o.o. jer su obrti postali riskantni :)

Posljednjih 7 godina radim s Elixir backendom, a posljednjih 5 s Vue.js na frontendu.

Ovo predavanje će u fokusu imati jedan manji dio jednog web frameworka u elixiru.
---

# Što je što?

## erlang
  - funkcijski jezik
  - Erricson, 1986
  - OTP (open telecom platform)
  - VM
  - Joe Armstrong, Robert Virding, Mike Williams

```erlang
-module(series).
-export([fib/1]).
%% public
fib(0) -> 0;
fib(N) when N < 0 -> err_neg_val;
fib(N) when N < 3 -> 1;
fib(N) -> fib_int(N, 0, 1).
%% private
fib_int(1, _, B) -> B;
fib_int(N, A, B) -> fib_int(N-1, B, A+B).

```
---

# Što je što?

## Elixir

- funkcijski jezik napisan u erlangu + Elixiru
- svi benefiti
- ugodniji ux

```Elixir
defmodule Series do
  def fib(0), do: 0
  def fib(n) when n < 0, do: raise "Cannot pass negative value"
  def fib(n) when n < 3, do: 1
  def fib(n) do
    fib(n, 0, 1)
  end

  defp fib(1, _, b), do: b
  defp fib(n, a, b), do: fib(n-1, b, a+b)
end
```
---
# Što je što?

## Phoenix framework

- THE web framework u elixiru
- html.eex templating jezik
- podrška za komponente


<div class="code-multi-part">

```elixir
## fib_controller.ex
def fib(conn, %{"n" => n}) do
  result = Series.fib(n)
  render(conn, :fib, %{n: n, result: result})
end

## fib_html.ex
attr(:n, :integer, required: true)
attr(:result, :integer, required: true)
def fib(assigns) do
  ~H"""
```
```html
  <UI.card>
    <p data-n={n} data-result={result}>
      The <%= n %>th fibonacci number is <%= result %>
    </p>
  </UI.card>
  """
```
```elixir
end
```
</div>
---
# Što je što

## Phoenix live view

- dodatak na Phoenix Framework
- html preko žice
- interaktivni html preko websocketa
- SPA-like razina interakcije
---
# Kako funkcionira?

<div class="code-multi-part">

```elixir
# items_live.ex
def mount(_params, _session, socket) do
  items = Repo.all(Item)
  socket = assign(socket, :items, items)
  {:ok, socket}
end

def render(assigns) do
  ~H"""
```

```html
  <ul>
    <li :for={item <- @items}>
      <UI.row_layout>
        <a href={~p/items/#{item.id}}><%= item.text %></a>
        <button phx-click="delete" phx-value-id={item.id}>X</button>
      </UI.row_layout>
    </li>
  </ul>
```

```elixir
  """
```
```elixir
end

def handle_event("delete", %{"id" => item_id}, socket) do
  Repo.delete!(Item, id)
  items = Enum.reject(socket.assigns.items, & &1.id === id)
  socket = assign(socket, :items, items)
  {:noreply, socket}
end
```
</div>
---

# Zašto?

- JSON/Protobuf/Whatever API je nepotreban

---

# DEMO: Live Beats

https://livebeats.fly.dev/begedin

---

# DEMO: Zmija

https://livepixel.fly.dev

---

# Problemi?

## Latencija

- svaka interakcija mora ye ws poruka gore i dolje
- riješenje: ipak malo JS-a

---

# Ipak malo JS-a

## Phoenix.LiveView.JS modul

```elixir
def hide_modal(js \\ %JS{}) do
  js
  |> JS.hide(transition: "fade-out", to: "#modal")
  |> JS.hide(transition: "fade-out-scale", to: "#modal-content")
end
```

Razne mogucnosti

```elixir
add_class, remove_class, toggle_class, set_atribute, remove_attribute,
toggle_attribute, show, hide, toggle, transition, dispatch, push, focus
```

Sitne client-side modifikacije HTML-a bez pisanja JS-a, "ručno" slanje evenata u DOM i na server, itd.


---

# Ipak malo JS-a

## Hooks

```html
<div id="infinite-scroll" phx-hook="InfiniteScroll" data-page={@page}>
```

```js
let Hooks = {}

Hooks.InfiniteScroll = {
  page() { return this.el.dataset.page },
  mounted(){
    this.pending = this.page()
    window.addEventListener("scroll", e => {
      if(this.pending == this.page() && scrollAt() > 90){
        this.pending = this.page() + 1
        this.pushEvent("load-more", {})
      }
    })
  },
  updated(){ this.pending = this.page() }
}
let liveSocket = new LiveSocket("/live", Socket, {hooks: Hooks, ...})
```

---

# Ovo nije ništa novo

- danas više ne

| jezik | rješenje | prva verzija |
|---|---|---|
| C# | Blazor | 2018 |
| Elixir | PhoenixLiveView | 2019 |
| PHP | Livewire | 2020 |
| Ruby on rails (i drugi) | Turbo | 2021 |
| React | Next.js + Server Components | 2023 ? |

- jedan od ranijih
- jako lightweight
  - elixir backend
  - ws
  - dom.js
  - malo "ljepila"

---

# Prebaciti se na Elixir zbog ovoga?

- možda za osobne projekte
- moooožda za nove projekte

---
<div class="centered">
  <h1>Hvala!</h1>
  <h2>Pitanja?</h2>
</div>

