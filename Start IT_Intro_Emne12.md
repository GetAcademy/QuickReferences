# JAVASCRIPT – VERKTØYKASSE (Emne 1–2)

Tre-kolonne-oppsett anbefales ved utskrift. Emoji-ikoner hjelper lesbarheten. Lenker peker til relevante W3Schools-artikler.

---

## 🚀 Grunnferdigheter  ·  [DOM intro](https://www.w3schools.com/js/js_htmldom.asp)
```js
document.getElementById('id').innerHTML = 'tekst' // Endre innhold i DOM
```
```html
<button onclick="vis()">Klikk</button> <!-- Kalle funksjon fra knapp -->
```

---

## 🧩 Funksjoner (vanlig syntaks)  ·  [Functions](https://www.w3schools.com/js/js_functions.asp)
```js
function vis(){ document.getElementById('id').innerHTML = 'Hei' } // Enkel funksjon: endrer HTML
function hils(navn){ return 'Hei ' + navn } // Med parameter: tar inn verdi(er)
function dobbel(x){ return x*2 } // Med returverdi: produserer resultat
```
### ➡️ Lambda-syntaks (arrow)  ·  [Arrow Functions](https://www.w3schools.com/js/js_arrow_function.asp)
```js
const f = (x) => x*2 // Kortform funksjon
```

---

## 📦 Variabler  ·  [let/const/var](https://www.w3schools.com/js/js_let.asp)
```js
let x;       // Deklarasjon
x = 3;       // Tilordning
let y = 5;   // Deklarasjon + tilordning
const navn = 'Ada' // const: må tilordnes med én gang, kan ikke endres
```

---

## ➕ Operatorer  ·  [Operators](https://www.w3schools.com/js/js_operators.asp) · [Comparison](https://www.w3schools.com/js/js_comparisons.asp) · [Logical](https://www.w3schools.com/js/js_booleans.asp)
```js
+ - * / % **          // Aritmetiske  (% = modulus/resten, ** = opphøyd i)
= += -= *= /= %=      // Tilordning
=== !== == !=         // Sammenligning (===/!== anbefales)
> >= < <=             // Større/mindre enn
&& || !               // Logiske
x>5 ? 'stor' : 'liten'// Ternary (kort if)
```

---

## 🔀 If-setninger  ·  [If...Else](https://www.w3schools.com/js/js_if_else.asp)
```js
if(x>0){...} // Kun if – kjører bare hvis sann (0 eller 1 gren totalt)
```
```js
if(x>0){...} else if(x<0){...} // If + else if – nøyaktig 1 gren *kan* velges (0 eller 1 gren totalt)
```
```js
if(x>0){...} else if(x<0){...} else {...} // If + 0, 1 eller mange else if + else – alltid nøyaktig 1 gren totalt
```

---

## 🕳️ Null & Undefined  ·  [Null & Undefined](https://www.w3schools.com/js/js_undefined.asp)
```js
let x = null; // Null = tom/ingen verdi (satt eksplisitt)
let y;        // Undefined = ikke satt verdi (mangler tilordning)
```

---

## 🔁 Løkker  ·  [For Loop](https://www.w3schools.com/js/js_loop_for.asp) · [While Loop](https://www.w3schools.com/js/js_loop_while.asp)
```js
for(let i=0;i<5;i++){ sum+=i }         // For – telleløkke
```
```js
for(const v of xs){ console.log(v) }   // For-of – rett på elementene
```
```js
while(confirm('Fortsette?')){ tall++ } // While – så lenge betingelsen er sann
```

---

## 🧭 Model–View–Controller (enkelt mønster)
```js
const model = {navn:'Ada', lys:'rødt'}               // Model: data
document.getElementById('id').innerHTML = model.lys  // View: viser data
function sett(v){ model.lys=v; view() }              // Controller: endrer data og re-render
```
```html
<input value="${model.navn}" oninput="model.navn=this.value"> <!-- Input-binding: value fra model, lagre med oninput -->
```

---

## 🔢 Tall & Math  ·  [Numbers](https://www.w3schools.com/js/js_numbers.asp) · [Math](https://www.w3schools.com/js/js_math.asp)
```js
parseInt('42'); parseFloat('3.14'); pris.toFixed(2) // Konverter/formatter
```
```js
Math.round(x); Math.floor(x); Math.ceil(x) // Avrunding
```
```js
Math.max(a,b); Math.min(a,b) // Ekstremer
```
```js
Math.random() // 0–1
```
```js
Math.floor(Math.random()*(max-min+1))+min // Tilfeldig heltall [min,max]
```

---

## 📝 Tekst / String  ·  [Strings](https://www.w3schools.com/js/js_strings.asp) · [String Methods](https://www.w3schools.com/js/js_string_methods.asp) · [Template Literals](https://www.w3schools.com/js/js_string_templates.asp)
```js
'abc'.length; 'abc'.slice(1,3); 'abc'.includes('a') // Lengde, del, søk
```
```js
'a'.toUpperCase(); '  a  '.trim() // Endre & trim
```
```js
'a;b'.split(';'); ['a','b'].join(',') // Split/Join
```
```js
'7'.padStart(2,'0') // Padding
```
```js
`Hei ${navn}
Velkommen` // Template literal – flere linjer + ${} innfelling
```

---

## 📅 Dato / Date  ·  [Dates](https://www.w3schools.com/js/js_dates.asp)
```js
const d = new Date(); d.getFullYear(); d.getMonth(); d.getDate() // Nåtid + deler
```
```js
d.toLocaleDateString('no-NO'); d.toISOString() // Formatering (lokal/ISO)
```
```js
new Date(d.getTime()+24*60*60*1000) // Legg til ett døgn (ms)
```

---

## 🧱 Objekter  ·  [Objects](https://www.w3schools.com/js/js_objects.asp)
```js
const p={navn:'Ada',alder:20}; const q={}; q.navn='Ada' // Med verdier / settes senere
```
```js
const store={}; store['nøkkel']='verdi' // Bruk som key/value-store
```
```js
for(const k in p){ console.log(k,p[k]) } // Loope nøkler
```

---

## 📜 Lister (Arrayer)  ·  [Arrays](https://www.w3schools.com/js/js_arrays.asp) · [Array Methods](https://www.w3schools.com/js/js_array_methods.asp)
```js
const xs=[1,2,3]; xs.push(4); xs.pop(); xs[0] // Lage, legge til/fjerne, index
```
```js
for(let i=0;i<xs.length;i++){ /* ... */ } // Loop med index
```
```js
for(const v of xs){ /* ... */ } // Loop med of
```
```js
xs.find(fn); xs.filter(fn); xs.map(fn) // Nyttige metoder
```

---

## 🖱️ DOM-hendelser  ·  [HTML DOM Events](https://www.w3schools.com/js/js_htmldom_events.asp)
```html
<button onclick="funksjon()"> <!-- Klikk -->
<input oninput="oppdater()">   <!-- Input -->
<select onchange="endre()">    <!-- Endring -->
```
