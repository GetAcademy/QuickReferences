# Vue 3 – Quick Reference

## 1. Props (data inn)

```ts
type Props = { value: number; label?: string }
const props = defineProps<Props>()
```

---

## 2. Emits (data ut)

```ts
const emit = defineEmits<{
  (e: 'increment'): void
  (e: 'update:value', value: number): void
}>()
```

---

## 3. Reactivity + computed

```ts
// ref -> .value i script
// reactive -> direkte tilgang
const count = ref<number>(0)
const state = reactive({ count: 0, name: '' })
const double = computed(() => count.value * 2)
```

---

## 4. Oppdatere state

```ts
const increment = () => {
  count.value++ // ref -> .value
  state.count++ // reactive -> direkte
}
```

---

## 5. Script setup

```html
<script setup lang="ts"></script>
```

---

## 6. Expressions

```html
{{ count }}
{{ count + 1 }}
{{ isActive ? 'Yes' : 'No' }}
```

---

## 7. Binding

```html
<div :id="id"></div>
<div :class="{ active: isActive }"></div>
<div :class="['btn', isActive && 'active']"></div>
<div :style="{ color: 'red' }"></div>
```

---

## 8. Events

```html
<button @click="increment"></button>
<button @click="emit('increment')"></button>
<button @click="count++"></button>
<button @click="doSomething(id)"></button>
<form @submit.prevent="submit"></form>
<button @click.stop="click"></button>
<input @keyup.enter="save" />
```

---

## 9. v-model

```ts
const text = ref('')
```

```html
<input v-model="text" />
```

---

## 10. Conditionals

```html
<div v-if="isVisible"></div>
<div v-else-if="other"></div>
<div v-else></div>
<div v-show="isVisible"></div>
```

---

## 11. Loops (v-for)

```html
<div v-for="item in items" :key="item.id">{{ item }}</div>
<div v-for="(item, index) in items" :key="index"></div>
<div v-for="(value, keyName) in obj" :key="keyName"></div>
<div v-for="n in 6" :key="n">{{ n }}</div>
```

---

## 12. Korte huskeregler

* props er readonly – ikke muter dem
* emit sender data opp til parent
* ref: bruk .value i script
* computed = avledet state
* v-if = lager/fjerner DOM
* v-show = viser/skjuler med CSS
* v-for: bruk alltid :key
* unngå v-if + v-for på samme element
