---
title: Ecto Sandbox Plug (English)
css: [theme.css]

---
<div class="centered" markdown="1">
  <h1>Path to Vue3</h1>
  <h2>Nikola Begedin</h2>
  <h3>V7 (via Bego Solutions)</h3>
</div>

---

# Who am I?

---

# What this isn't

- a success story (still WIP)
- a brag on how fast we did (still WIP!)
- a how-to

---

# What this is

- sharing knowledge and strategy

---

# What kind of app do we have

- one huge frontend monolith
- several core product features
- ~1000 components total
- 1 major subsystem in the middle of a rewrite
  - 20-30% of components due to be deleted

---

# What kind of app do we have

- typescript
- vuex
- vue class components
- vuex class
- jest + @vue/test-utils ()
- storybook

---

# #1 problem

- class components
- more work to migrate
- they don't actually run in vite or vue3 at all yet (being worked on)

---

# TODO

- migrate all the components we're keeping
- vuex to pinia
- storybook to vite
- jest to vitest

---

# Strategy

## Observation

- single big effort will create bugs, issues, delay feature work and won't directly benefit customers
  - indirect benefit is not as easy to justify




## Conclusion

- the process needs to be beneficial, not just the goal
- the process needs to be non-discruptive
  - 1 engineer converts 2 components in a sprint (2 weeks)
    - timeboxed to 4 hours total
    - if it takes more than 2 hours per component, drop it and grab the next one
  - we ramp up as we get experience

---

# Strategy: Selecting components

- align with product - migrate components about to be touched by feature work beforehand
- migrate components interacting with the same vuex module
- migrate components around a specific feature of the product
- create independent islands of migrated components
- build up knowledge, learn about caveats, document, share and educate to speed up

---

# Strategy: Vuex to pinia

- establish a safe way to gradually shift

---

# Strategy: Effects

- every small change is beneficial
  - better type safety
  - less boiler plate
  - reduction of bugs in specific categories
  - overall improvement to quality, clarity and maintainability of code
- we are auditing our codebase as a side-effect
  - finding unused code
  - adding missing documentation (we were a startup)

---

# Execution: Easy parts

- delay migrating that 20-30% components
  - but don't take it off the table
- more than 100 components look like this:

```
@Component({ name: 'my-component' })
export default MyComponent extends Vue {}
```

- find and replace with

```
export default { name: 'MyComponent' }
```

- will later explain why

---

# Execution: Vuex to Pinia

- define a future pinia store now
- have it proxy to vuex

```typescript
const teamsStore = defineStore('teams', () => {
  const vuexStore = useVuexStore()

  // getter proxies to vuex state
  const allTeams = computed(() => vuextStore.state.teams.allTeams)

  // action proxies to vuex mutation
  const setTeams = (teams) => store.commit('teams/SET_TEAMS', teams)

  // actions proxies to vuex action
  const loadTeams = () => store.dispatch('teams/loadTeams')

  return {
    allTeams,
    setTeams,
    loadTeams
  }
})
```
---

# Execution: Vuex to Pinia

- now that we have the store, we can switch component by component as we migrate them
- once vuex has no direct use, move fully to pinia

## Extra benefits

- we can directly type the pinias store (vuex has only indirect typing)
- we can document better
- we can fragment into smaller, more focused stores
- extremely low risk

---

# Execution: Vite

- as we do this, gradually introduce a vite config
- can be used in parallel initially, for faster local development
- can be used for vitest
- can be used for storybook
- can be used for Preview.js

## Identify requirements

- `process.env` -> `import.meta.env`
  - we can make it forwards or backwards compatible via define plugin
- custom loaders
  - eliminate or reimplement (not that hard)
- commonjs usage (`require`, `module.exports`) - eliminate
- test migration recipes

Happily using it!

---

# Execution: Identified problems

- `@vue/test-utils` render anonymous stubs for script setup components
  - add an extra, non-setup block with just the name
  - this causes some editor plugins to not work as expected
    - waiting on bugfixes


```html
<script lang="ts" setup>...</script>
<script lang="ts">export default { name: 'MyComponent' }</script>
```
---

# Execution: Identified problems

- wrapping `$store`, `$router`, `$route` into composables

```typescript
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

---

# Execution: Other things we are doing

- switching to PascalCase - done!
- gradually switching from `wrapper.find` to `wrapper.findComponent`
- prefer using `jest.mock` over mock injection
  - more framework agnostic
  - easy to find and replace for vitest

---

# Execution: Other things we are doing

- simplify mocks, component tests shifting more to unit, less integration
  - faster test suite
- eliminating vue2-only libraries
  - mainly by replacing with `@vueuse`
- started using `vue-tsc` for typechecking
  - we can do isolatedModules in jest - faster
  - we still get full type-checking, even better than plain `tsc`, in CI

---

# Report: Where are we?

- ~ 400 components remaining
  - haven't dropped the v1 subsystem yet, so likely closer to around ~200
- 2/10 vuex modules eliminaed
- several pinia proxies in place
- `process.env` centralized, forward compatible with `import.meta.env`
- component migration expected to be done during summer
- full switch to vite expected early fall
- full switch to vue3 expected mid-fall
- full switch to vitest expected mid-fall
- product work effectively unaffected

---

# Outcome: Nice side-effects

- smaller codebase
  - compositon api is less boilerplate
  - code deduplication
  - tracking down unused code
- less bugs overall
  - some categories of bugs are eliminated
    - using vuex with incorrect payloads
  - actual bugs being found and fixed during migrations
  - tests getting better coverage
- much faster build time than we started
- much faster test suite

---

# Summary: What we learned

- it's a lot of work
  - but you get faster as you get more experienced
- it's benefical as you go, not only at the end

## More practical

- pinia proxies are great for gradual elimination of vuex
- `toRef` is your friend for passing props and store values into composables
- composables don't need to be reused to be justified
  - they have self-documenting aspects
- assertions are more useful than snapshot tests
- `@vueuse` will cover 90% of your needs

---
<div class="centered" markdown="1">
  <h1>Thank you!</h1>
  <h2>Questions?</h2>
</div>