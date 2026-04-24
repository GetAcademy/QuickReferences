# JavaScript – Verktøykasse (Emne 1–2)

## JavaScript

```js
// DOM
document.getElementById('id').innerHTML = 'tekst'

// funksjoner
function vis(){ document.getElementById('id').innerHTML = 'Hei' }
function hils(navn){ return 'Hei ' + navn }
function dobbel(x){ return x * 2 }
const f = (x) => x * 2

// variabler
let x          // deklarasjon
x = 3          // tilordning
let y = 5      // deklarasjon + tilordning
const navn = 'Ada' // må tilordnes med én gang

// operatorer
+ - * / % **            // aritmetiske
= += -= *= /= %=        // tilordning
=== !== == !=           // sammenligning (bruk helst === / !==)
> >= < <=               // større/mindre enn
&& || !                 // logiske
x > 5 ? 'stor' : 'liten' // ternary

// if-setninger
if (x > 0) { /* ... */ }
if (x > 0) { /* ... */ } else if (x < 0) { /* ... */ }
if (x > 0) { /* ... */ } else if (x < 0) { /* ... */ } else { /* ... */ }

// null & undefined
let a = null
let b

// løkker
for (let i = 0; i < 5; i++) { sum += i }
for (const value of xs) { console.log(value) }
while (confirm('Fortsette?')) { tall++ }

// MVC
const model = { navn: 'Ada', lys: 'rødt' }
document.getElementById('id').innerHTML = model.lys
function sett(verdi){
  model.lys = verdi
  view()
}

// tall & Math
parseInt('42'); parseFloat('3.14'); pris.toFixed(2)
Math.round(x); Math.floor(x); Math.ceil(x)
Math.max(a, b); Math.min(a, b)
Math.random()
Math.floor(Math.random() * (max - min + 1)) + min

// string
'abc'.length
'abc'.slice(1, 3)
'abc'.includes('a')
'a'.toUpperCase()
'  a  '.trim()
'a;b'.split(';')
['a', 'b'].join(',')
'7'.padStart(2, '0')
`Hei ${navn}
Velkommen`

// date
const d = new Date()
d.getFullYear(); d.getMonth(); d.getDate()
d.toLocaleDateString('no-NO')
d.toISOString()
new Date(d.getTime() + 24 * 60 * 60 * 1000)

// objekter
const person = { navn: 'Ada', alder: 20 }
const q = {}
q.navn = 'Ada'
const store = {}
store['nøkkel'] = 'verdi'
for (const key in person) {
  console.log(key, person[key])
}

// arrays
const xs = [1, 2, 3]
xs.push(4)
xs.pop()
xs[0]
for (let i = 0; i < xs.length; i++) { /* ... */ }
for (const value of xs) { /* ... */ }
xs.find(fn)
xs.filter(fn)
xs.map(fn)

<!-- DOM / grunnferdighet -->
<button onclick="vis()">Klikk</button>

<!-- MVC input-binding -->
<input value="${model.navn}" oninput="model.navn = this.value">

<!-- DOM-hendelser -->
<button onclick="funksjon()">Klikk</button>
<input oninput="oppdater()">
<select onchange="endre()"></select>