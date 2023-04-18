---
title: Ecto Sandbox Plug (English)
css: [theme.css]
revealOptions:
  autoPlayMedia: true

---
<div class="centered" markdown="1">
  <h1>Path to Vue3</h1>
  <h2>Nikola Begedin</h2>
  <h3>Bego Solutions | <a>bego.dev</a>
  <h4>working with V7 | <a>v7labs.com</a></h3>
</div>
Note:
Hey everyone.

My name is Nikola.

I own Bego Solutions, which is a sole proprietorship, and my
primary client for the past 4 years has been V7.

I've been working with their engineering team,
building a very complex product together.

For the past few months, we've been migrating the frontend part of it to Vue3,
so that's what I'm gonna talk about.

---

# What this isn't

- a success story (still WIP)
- a brag on how fast we did it (still WIP!)
- a how-to

Note:

So before we start, let's just say what this presentation isn't.

It's not a success story. Not because we failed or have given up, but beceause
were still in the process of migrating.

It's also not a brag of how amazingly fast and well we did it because we've
started new to it, and we had to learn a bunch of things, as well as make
mistakes to get anywhere.

It's also not really a how-to. Our project is our project and yours will
probably work differently.
---

# What this is

- sharing of experience, knowledge and strategy

Note: What this is, however, is us sharing what we've learned, what our strategy
was and how it worked out for us specifically.

I'm not really sure if it's going to be useful, but
hopefully it will be interesting.

---

# What kind of app do we have

- one huge frontend monolith
- parts that look like spa - dataset management, account management
- parts that look like whimsical - workflow editor
- parts that look like photoshop - annotation UI
- ~1000 components total
- 1 major subsystem in the middle of a rewrite
  - 20-30% of components due to be deleted

<iframe height="400" width="700" data-src="https://www.youtube.com/embed/F-DMAN8gbjk?autoplay=1&controls=0&mute=1"></iframe>

Note:

Let's brefly talk about v7's product. It started as a computer vision platform,
but over time, it's becoming much, nuch more.

Architecturally, it's one huge frontend monolith, with several backends

On the frontend, we have parts that are your typical single page application,
a part that look like whimsical, dealing with a lot of custom DOM stuff,
and then a part that looks like a customised version of photoshop - our annotation UI

There are about 1000 components in total, but to be fair, we have one major
subsystem nearing the end of a major rewrite. We have a v1 codebase and a v2
codebase, and the v1 codebase is being phased out, hopefully, by end od July
this year.

So I did a bit of padding here, but not fully. I'll touch on that in one of the
later slides.

---

# What kind of app do we have

- typescript
- vuex
- vue class components
- vuex class
- jest + @vue/test-utils
- storybook



<iframe height="400" width="700" data-src="https://www.youtube.com/embed/F-DMAN8gbjk?autoplay=1&controls=0&mute=1&time=45"></iframe>

Note:

As far as technologies go, we're fully on typescript. We use vuex, and it become
kind of bloated, with a lot of store modules, not amazingly well structured.

Early in the project, we decied class components with vuex class were a good
idea for slightly better typescrip support and a system a few of us were a bit
more familiar with from our backtrounds.

We are using, of course, jest, with vue test utils as our testing framework, and
we rely on storybook as a component catalogue, but not for testing.s

---

# #1 problem

- class components
- more work to migrate
- they don't actually run in vite or vue3 at all yet (but they might soon)

Note:

So from that, our number one problem are the class components.

We actually tried swicthing to vite about a year ago, and failed because they
just don't work out of the box. There are now, as far as I know, release
candidates for both the class components and vuex class libraries, so they may
even end up working before we're done with the migration, but we're still
switching away from those in favor of the standard composition API.

---

# TODO

- migrate all the components we're keeping (and maybe a few more)
- vuex to pinia
- app and storybook to vite
- jest to vitest

Note:

What we actually want do to is, migrate all the components that we're keeping,
so those that aren V1.

We of course also want to switch from vuex to pinia.

Finally, we want to use vite as our build system. That means the app, storybook
and vitest.

---

# Strategy

- single big effort will is bad idea
  - will still take weeks
  - guaranteed bugs
  - too many delays in product work
- the process needs to be
  - beneficial as we go, not just at the end
  - non-discruptive

Note:

So we need a strategy.

To us, it really didn't look like trying to do it all in one big push is a good idea. It would stillt ake weeks, delay a bunch of other things, introduce who knows how many bugs. Just a bad idea overall.

We wanted to do it in a safer way.

The process needs to be beneficial as we go, not just something with an end
goal. It also needs to be as undistruptive as possible.

---

# Strategy: Basic approach

- 1 engineer converts 2 components in a sprint (2 weeks)
  - timeboxed to 2 hours per component
  - if it takes more than 2 hours, drop the ticket and grab the next one
- we ramp up as we get experience
- we revisit dropped tickets as we know more
- all new code is composition API

Note:

We decided, every sprint, which is 2 weeks, one engineer would take 2
components to convert. This is timeboxed to 2 hours per component, so if any
takes longer, put that ticket on hold, try and document the problem and move to
the next one.

As we go, hopefully, we get faster, so it takes less than an hour on average to
convert a component, and we can ramp up.

---


# Strategy: Selecting components

- align with product
  - migrate components about to be touched by feature work
- create migrated islands
  - components interacting with the same vuex module
  - components around a specific area of the product
- mantra: build up knowledge
  - learn about caveats
  - document
  - share and educate
  - speed up everyone

Note:

The next important part of the strategy is how to select components for conversion.

First, we aling with the product. We have planned features on the roadmap, so we
figure out which existing components will be touched during development of those
features and should be converted beforehand.

This actually might end up being a net benefit to the speed of developing that
feature.

Then, we find vuex modules that we want to move to pinia in some ways asap, and
we pick components interacting with that module. This will allow us to, in
parallel, reduce the size of our vuex store and gradually shift to pinia.

Finally, we pick components around a specific feature. We find islands in the
vue2 codebase and migrate them to islands in the vue3 codebase.

As we do this, we build up knowledge, audit our code, learn about caviats, and
this is important DOCUMENT all of this stuff so other engineers benefit and we
all get faster.

---

# Strategy: Effects

- as we go, we get
  - better type safety
  - less boiler plate
  - reduction in bugs count
  - speedup in feature work
  - knowledge of more things we can do
- the codebase gets audited
  - finding unused code
  - adding missing documentation (we were a startup)
  - adding missing tests (we were a startup!)

Note:

The effect of all this is that with every component converted, we will benefit
in some way.

Type safety will be marginally better, for example, by shifting to pinia.

Code will get smaller, due to less boilerplate, easier deduplicating and
just by finding unused code, since this actually is an audit to.

And documentation will get better, because we are no longer a startup and we now
ask for documentation in our code reviews.

---

# Execution: Easy parts

- delay migrating that 20% of V1 components
  - but don't take it off the table
- more than 100 components look like this:

```
@Component({ name: 'my-component' })
export default class MyComponent extends Vue {}
```

- find and replace with

```
export default { name: 'MyComponent' }
```

- will later explain why

Note:

Now on to the executions. There are some easy parts we can do right away.

A bunch of our components have no actual logic, so we can find-and-replace them
with a name export and that's about it. Why a name export and not an empty
script tag?

I'll explain in a few slides.

---

# Execution: Conversions

- `@Prop` -> `defineProps`
- class field -> `ref`
- class getter -> `computed`
- class function -> function
- readonly/static values -> plain values, non-reactive

- vuex
  - `@State` -> `computed`
  - `@Getter` -> `computed` or function
  - `@Mutation` -> function
  - `@Action` -> function
  - preferably a pinia wrapper for all of them

Note:

Generally, a conversion is just a mapping of what becomes what, so we don't
realy need to go in detail there.

The actual component conversion is just a lot of labor, but it's not
highly skilled labor. Distilling the experience of doing that into knowledge is.

---

# Execution: Vuex to Pinia

- define a future pinia store now
- have it proxy to vuex

```typescript
const teamsStore = defineStore('teams', () => {
  const vuexStore = useVuexStore()

  // getter proxies to vuex state
  const allTeams = computed(() => vuexStore.state.team.allTeams)

  // action proxies to vuex mutation
  const setTeams = (teams: Team[]) => store.commit('team/SET_TEAMS', teams)

  // actions proxies to vuex action
  const loadTeams = () => store.dispatch('team/loadTeams')

  return {
    allTeams,
    setTeams,
    loadTeams
  }
})
```

Note:

So instead of component conversion, I'm gonna talk about something more
interesting to me, which is our strategy of moving from vuex to pinia safely.

I really like this one.

We've developed this way to wrap a pinia store around a collection of vuex
actions, getters, mutations and state.

Basically, we create a pinia store which, rather than having it's own state, is
a proxy to vuex.

Vuex state and getters are proxied to via plain computeds, which in turn
effectively become pinia getters.

Vuex actions and mutations are proxied by functions, which become pinia actions.

From the outside, it looks like a plain pinia store, fully and nicely typed,
accessed as any other pinia store, but from the inside, it's just a proxy.

---

# Execution: Vuex to Pinia

- now that we have the store, we can switch component by component as we migrate them
- once vuex has no direct use, move fully to pinia

## Extra benefits

- pinia store is fully typed (vuex has only indirect typing)
- we can document better
- we can fragment into smaller, more focused stores
  - doesn't have to be 1 module -> 1 pinia store
- extremely low risk

Note:

Now that we have this store, and it can be perfectly safely introduced to our
codebase as it doesn't actually do anything yet, we can start shifting converted
components from direct vuex access to this.

We can do it one small bit at a time, as safely as we can.

Once this proxied to part of vuex is no longer used directly by any components,
we can drop the vuex part fully and move it directly into the pinia store.

The extra benefit of this approach is that pinia stores are directly and
automatically typed. In many places, vuex does not get this. Instead, you are
required to redeclare the types if you want any semblance of type safety.

We can also improve doucmentation by documenting the new pinia store.

We can also fragment into smaller units, that make more sense to fit together.

And again, this is all extremely low risk.

---

# Execution: Vite

- as we do this, gradually introduce a vite config
- can be used in parallel initially, for faster local development
- can be used for vitest
- can be used for storybook
- can be used for Preview.js

## Identify blockers

- `process.env` -> `import.meta.env`
  - we can make it compatible via define plugin
- commonjs usage (`require`, `module.exports`) - eliminate
- custom loaders
  - eliminate or reimplement (not that hard)

Happily using it!

Note:

As we started doing this, we realized we were getting to milestones where large
app features were fully converted to composition API.

That means, if we had a valid vite configuration, we could actually use it,
at least for those parts.

Maybe in time, we can also start switching some tests over to vitest, and
setup a storybook config to.

So we made this one of our sidegoals.

We figured out what the requirements are. For example, while vue-clie, or rather,
webpack, relies on process.env, vite relies on import.meta.env.

We can facilitate the transition there by first moving all access to process.env
to one module, then either make it forward compatbile using the webpack define
plugin, or backwards compatbile using vite's define

We use custom loaders for some things, so we need to either drop those, or write
vite versions of them, which is not actually that hard.

Vite doesn't support commonjs, and we have a few casses, so we have to eliminate
that.

But really, that's about it, everything else is fine.

So we now hapilly use a vite config alongside our webpack config.

---

# Execution: Problems

- `@vue/test-utils` render anonymous stubs for script setup components
  - add an extra, non-setup block with just the name
  - this causes some editor plugins to not work as expected
    - waiting on bugfixes


```html
<script lang="ts" setup>...</script>
<script lang="ts">export default { name: 'MyComponent' }</script>
```

Note:

Of course, we also found problems. One was with @vue/test-utils.

Any component converted to script setup would render as "anonymous-stub" in
snapshots. To get around that, every such component needs a script with a name
export. This is why we used this approach to quickly converted the "codeless"
components a few slides back.

This then creates problems with our import sorting plugin, so our imports are
currently not perfectly sorted, but there's a fix on the way.

---

# Execution: Problems

- wrapping `$store`, `$router`, `$route` into composables

```typescript
import { getCurrentInstance } from 'vue'

export const useStore = (): Store<RootState> => {
  const vm = getCurrentInstance()
  if (!vm) {
    throw new Error(
      'getCurrentInstance() returned null. ' +
      'Method must be called at the top of a setup function'
    )
  }
  return vm.proxy.$store
}
```

- requires a component to be mounted to access the composable in tests
  - rely more on `jest.mock`

Note:

Another problem that has a solution that creates a problem is converting all the
vue plugin prototype extensions into composables. So that means $store, $route,
$router, etc.

This was relatively simple to do, like in the example above. Basically,
getCurrentInstance is the trick.

But then we have another problem. If we want to test a composable that calls
one of these composables internally, we have to mount a fake component in the
test.

Or, we just `jest.mock` the useStore module and get around it that way. Which
kind of aligns with what we're doing with tests anyway.

---

# Execution: Other things we are doing

- switching to PascalCase - done!
- gradually switching from `wrapper.find` to `wrapper.findComponent`
- prefer using `jest.mock` over mock injection
  - more framework agnostic
  - easy to find and replace for vitest

Note:

Since we're threating this as an audit of sorts, we are also takign the
opportunity to revise a few things.

We've fully switched to pascal case, for example.

As we fix test that break due to component conversions, we started switching
from wrapper.find(selector) to wrapper.findComponent, for slightly more
robustness in tests.

We also started preferring jest.mock over injecting mocks into shallowMount.
It makes tests a bit more framework agnostic, and a bit easier to mock.

jest.mock is easy to find and replace for vitest, so it's not gonna greatly
slow us down.

---

# Execution: Other things we are doing

- eliminating vue2-only libraries
  - mainly by replacing with `@vueuse`
- started using `vue-tsc` for typechecking
  - we can do isolatedModules in jest - faster
  - we still get full type-checking, even better than plain `tsc`, in CI


Note:

And there's more.

We're eliminating vue2 only libraries, mainly by replacing them with composables
from the excellent vueuse suite of libraries.

We started using isolatedModules in tests to speed them up, then to offset the
loss of typesafety, added vue-tsc as a CI check. This is the typechecker volar
uses and it does a far better job with vue components than the standard tsc.

We've effectively sped up and improved typechecking at the same time.


---

# Results: Where are we?

- ~ 400 components remaining
  - haven't dropped the v1 subsystem yet, so likely closer to around ~200
- 2/10 vuex modules eliminated
- several pinia proxies in place
- tests 4x faster - 5000 tests 10 mins -> 2.5 mins
- build time 3x faster - 90 secs -> 30 secs on first run
- less code
- less bugs
- product work effectively unaffected

Note:

And then it's time to fess up. How far did we get, exactly?

We have about 400 compomnents remaining.
Once we drop v1, we expect to drop about 200.

We've eliminated 2 out of 10 or so vuex modules. We have several pinia proxies
in place to eliminate more.

We eexpect to be done with pure component migration during sommer and then expect
a full switch to vite early fall.

A full switch to vue3 will require a few more things, so it will probably happen
around mid fall.

Vitest and storybook are likely to happen late fall.

ANd we can confidently say that our product/feature work was at minimum unaffected,
potentially sped up.

---

# What we learned

- pinia proxies are great for gradual elimination of vuex
- `toRef` is your friend for passing props and store values into composables
- composables don't need to be reused to be justified
  - they have self-documenting aspects
- assertions are more useful than snapshot tests
- `@vueuse` will cover 90% of your needs

Note:

Practically speaking, we've learned that pinia proxies are amazing.

That the getCurrentInstance function is pure magic for quickly getting
composables out of vue2 plugins.

That composables don't need to be reused ot be justified. Seriously. If a piece
of a slightly larger component can become a unit, pull it out into a composable
and use it as an extra bit of compartmentalisation and documentation.

We've learned htat toRef is a severly underused way to pass a prop, or a pinia
store key to a composable as an observable value.

We've learned htat in tests, at least for our team, snapshots are mostly useless,
but assertions on html content are great.

We've learned that @vueuse will cover 90% of your needs.

And we're still learning.

---

# Estimates

- components late summer
- pinia short time after
- full move to vite early fall
- full move to vue3 mid-fall
- full move to vitest late-fall

---
<div class="centered" markdown="1">
  <h1>Thank you!</h1>
  <h2>Questions?</h2>
</div>

Note:

Thank you! Are there any questions?