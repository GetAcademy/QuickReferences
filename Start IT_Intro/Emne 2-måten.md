
```html
<!-- index.html -->
<html>
<head>
  <link rel="stylesheet" href="style.css" />
  <script src="js/model.js"></script>                 <!-- 1. Data og tilstand først -->
  <script src="js/common.js"></script>                <!-- 2. Felles hjelpefunksjoner -->
  <script src="js/contactPageController.js"></script> <!-- 3. Controllere -->
  <script src="js/categoryPageController.js"></script>
  <script src="js/contactPageView.js"></script>       <!-- 4. Views -->
  <script src="js/categoryPageView.js"></script>
</head>
<body>
  <div id="app"></div>

  <script>
    updateView();

    function updateView() {
      const page = model.viewState.app.currentPage;

      if (page === 'contacts') updateViewContactsPage();
      else if (page === 'categories') updateViewCategoriesPage();
    }
  </script>
</body>
</html>
```


```js
const model = {
  // App-data: det appen handler om
  contacts: [
    { id: 1, name: 'Ada', phone: '11111111' },
    { id: 2, name: 'Linus', phone: '22222222' },
    { id: 3, name: 'Grace', phone: '33333333' },
  ],

  categories: [
    { id: 1, name: 'Venner' },
    { id: 2, name: 'Jobb' },
    { id: 3, name: 'Familie' },
  ],

  // Relasjon som egen tabell:
  // hvilke kontakter har hvilke kategorier?
  contactCategories: [
    { contactId: 1, categoryId: 2 },
    { contactId: 2, categoryId: 1 },
    { contactId: 2, categoryId: 2 },
    { contactId: 3, categoryId: 3 },
  ],

  // ViewState: det brukeren holder på med akkurat nå
  viewState: {
    app: {
      currentPage: 'contacts',
      loggedInUser: null, // bare hvis vi skal simulere innlogging
    },

    contactsPage: {
      newName: '',
      newPhone: '',
      selectedCategoryId: 0,
      searchText: '',
      sortField: 'name',
      sortDirection: 'asc',
      pageNumber: 1,
      pageSize: 5,
    },

    categoriesPage: {
      newCategoryName: '',
    },
  }
};

// Inputfelt skriver til ViewState
oninput="model.viewState.contactsPage.newName=this.value"
```

```js
// Controller flytter fra ViewState til app-data
function addContact() {
  const page = model.viewState.contactsPage;

  const name = page.newName;
  const phone = page.newPhone;

  const newContact = {
    id: getNextContactId(),
    name: name,
    phone: phone,
  };

  model.contacts.push(newContact);

  page.newName = '';  // nullstill skjema
  page.newPhone = '';

  updateView();
}
```

```txt
inputfelt
  ↓
model.viewState.contactsPage.newName
  ↓
addContact()
  ↓
model.contacts
  ↓
updateView()
```


```js
// contactPageView.js
function updateViewContactsPage() {
  const page = model.viewState.contactsPage;

  document.getElementById('app').innerHTML = /*HTML*/`
    <h1>Kontaktregister</h1>

    <input
      type="text"
      placeholder="Søk"
      value="${page.searchText}"
      oninput="model.viewState.contactsPage.searchText=this.value; updateView()"
    /> <!-- Inputfelt skriver til ViewState -->

    <table>
      <tr>
        <th>Navn</th>
        <th>Telefon</th>
        <th>Kategorier</th>
        <th></th>
      </tr>
      ${createContactsTableRowsHtml()}
    </table>

    <h2>Ny kontakt</h2>
    <input
      placeholder="Navn"
      value="${page.newName}"
      oninput="model.viewState.contactsPage.newName=this.value"
    />
    <input
      placeholder="Telefon"
      value="${page.newPhone}"
      oninput="model.viewState.contactsPage.newPhone=this.value"
    />
    <button onclick="addContact()">Legg til</button>
  `;
}
```

---

## 5. Trekk ut HTML-funksjoner

```js
function createContactsTableRowsHtml() {
  let html = '';

  for (let contact of model.contacts) {
    if (!shouldShowContact(contact)) continue;
    html += createContactRowHtml(contact);
  }

  return html;
}
```

```js
// Ikke komponenter. Bare funksjoner som lager HTML.
function createContactRowHtml(contact) {
  return /*HTML*/`
    <tr>
      <td>${contact.name}</td>
      <td>${contact.phone}</td>
      <td>${createCategoryListHtml(contact.id)}</td>
      <td>
        <button onclick="deleteContact(${contact.id})">x</button>
      </td>
    </tr>
  `;
}
```

```js
function createCategoryListHtml(contactId) {
  let html = '';

  for (let relation of model.contactCategories) {
    if (relation.contactId !== contactId) continue;

    const category = findCategory(relation.categoryId);
    html += `<span class="tag">${category.name}</span> `;
  }

  return html;
}
```

```js
// Når trekker vi ut slike funksjoner?
// - når samme type HTML trengs flere steder
// - når en view-funksjon blir for lang
// - når én rad / ett kort / én meny skal lages
```

---

## 6. Routing: bytte side

```js
function goToPage(page) {
  model.viewState.app.currentPage = page;
  updateView();
}
```

```html
<button onclick="goToPage('contacts')">Kontakter</button>
<button onclick="goToPage('categories')">Kategorier</button>
```

```txt
currentPage = 'contacts'
  → updateViewContactsPage()

currentPage = 'categories'
  → updateViewCategoriesPage()
```

---

## 7. Felles layout og meny

```js
// common.js eller commonView.js
function createPageHtml(contentHtml) {
  return /*HTML*/`
    <div class="page">
      <header>
        <h1>Kontaktregister</h1>
      </header>

      <nav>
        <button onclick="goToPage('contacts')">Kontakter</button>
        <button onclick="goToPage('categories')">Kategorier</button>
      </nav>

      <main>
        ${contentHtml}
      </main>
    </div>
  `;
}
```

</div>

<div class="col">

## 8. Controller: endre data, ikke HTML

```js
// contactPageController.js
function deleteContact(contactId) {
  const index = arrayIndexFromId(model.contacts, contactId);
  if (index === null) return;

  model.contacts.splice(index, 1);
  deleteRelationsForContact(contactId);

  updateView();
}
```

```js
function deleteRelationsForContact(contactId) {
  for (let index = model.contactCategories.length - 1; index >= 0; index--) {
    const relation = model.contactCategories[index];

    if (relation.contactId === contactId) {
      model.contactCategories.splice(index, 1);
    }
  }
}
```

```js
// Controller skal vanligvis:
// 1. hente data fra ViewState / app-data
// 2. endre model
// 3. kalle updateView()
```

---

## 9. Relasjoner i modellen

```js
// Kontakt:
{ id: 2, name: 'Linus', phone: '22222222' }

// Kategori:
{ id: 1, name: 'Venner' }

// Relasjon:
{ contactId: 2, categoryId: 1 }
```

```js
function addCategoryToContact(contactId, categoryId) {
  model.contactCategories.push({
    contactId: contactId,
    categoryId: categoryId,
  });

  updateView();
}
```

```js
function findCategory(categoryId) {
  const index = arrayIndexFromId(model.categories, categoryId);
  return index === null ? null : model.categories[index];
}
```

```js
function findContact(contactId) {
  const index = arrayIndexFromId(model.contacts, contactId);
  return index === null ? null : model.contacts[index];
}
```

---

## 10. Felles hjelpefunksjoner

```js
// common.js
function arrayIndexFromId(array, id) {
  for (let index = 0; index < array.length; index++) {
    if (array[index].id === id) return index;
  }

  return null;
}
```

```js
function getNextContactId() {
  let maxId = 0;

  for (let contact of model.contacts) {
    if (contact.id > maxId) maxId = contact.id;
  }

  return maxId + 1;
}
```

```js
function getNextCategoryId() {
  let maxId = 0;

  for (let category of model.categories) {
    if (category.id > maxId) maxId = category.id;
  }

  return maxId + 1;
}
```

</div>

</div>

---

<div class="sheet three-cols">

<div class="col">

## 11. Generelt view-mønster: filtrer, sorter, sideinndel

```js
function createContactsTableRowsHtml() {
  let contacts = model.contacts;

  // 1. Filtrer hvis siden har søk/filter
  contacts = filterContacts(contacts);

  // 2. Sorter hvis siden har sortering
  contacts = sortContacts(contacts);

  // 3. Ta bare med riktig side hvis vi har paging
  contacts = getContactsPage(contacts);

  // 4. Lag HTML
  let html = '';

  for (let contact of contacts) {
    html += createContactRowHtml(contact);
  }

  return html;
}
```

```txt
model.contacts
  ↓
filterContacts(...)
  ↓
sortContacts(...)
  ↓
getContactsPage(...)
  ↓
createContactRowHtml(...)
```

```js
// Poenget:
// Viewet bestemmer hva som skal vises.
// Hjelpefunksjonene gjør hvert steg lett å lese.
```

---

## 12. Filtrering uten `.filter()`

```js
function filterContacts(contacts) {
  const page = model.viewState.contactsPage;
  const searchText = page.searchText.toLowerCase();
  const categoryId = page.selectedCategoryId;

  let result = [];

  for (let contact of contacts) {
    if (!matchesSearch(contact, searchText)) continue;
    if (!matchesCategory(contact.id, categoryId)) continue;

    result.push(contact);
  }

  return result;
}
```

```js
function matchesSearch(contact, searchText) {
  if (searchText === '') return true;

  const name = contact.name.toLowerCase();
  const phone = contact.phone.toLowerCase();

  return name.includes(searchText) || phone.includes(searchText);
}
```

```js
function matchesCategory(contactId, categoryId) {
  if (categoryId === 0) return true;

  for (let relation of model.contactCategories) {
    if (relation.contactId === contactId &&
        relation.categoryId === categoryId) {
      return true;
    }
  }

  return false;
}
```

</div>

<div class="col">

## 13. Sortering uten `.sort()` med lambda

```js
function sortContacts(contacts) {
  const page = model.viewState.contactsPage;

  // Kopier arrayet først, så vi ikke endrer model.contacts
  let result = copyArray(contacts);

  result.sort(compareContacts);

  if (page.sortDirection === 'desc') {
    result.reverse();
  }

  return result;
}
```

```js
function compareContacts(a, b) {
  const sortField = model.viewState.contactsPage.sortField;

  let aValue = a[sortField];
  let bValue = b[sortField];

  if (typeof aValue === 'string') aValue = aValue.toLowerCase();
  if (typeof bValue === 'string') bValue = bValue.toLowerCase();

  if (aValue < bValue) return -1;
  if (aValue > bValue) return 1;
  return 0;
}
```

```js
function copyArray(array) {
  let result = [];

  for (let item of array) {
    result.push(item);
  }

  return result;
}
```

```js
function setSort(field) {
  const page = model.viewState.contactsPage;

  if (page.sortField === field) {
    toggleSortDirection();
  } else {
    page.sortField = field;
    page.sortDirection = 'asc';
  }

  updateView();
}
```

```js
function toggleSortDirection() {
  const page = model.viewState.contactsPage;

  if (page.sortDirection === 'asc') page.sortDirection = 'desc';
  else page.sortDirection = 'asc';
}
```

```html
<th onclick="setSort('name')">Navn</th>
<th onclick="setSort('phone')">Telefon</th>
```

---

## 14. Paging uten `.slice()`

```js
function getContactsPage(contacts) {
  const page = model.viewState.contactsPage;

  const startIndex = (page.pageNumber - 1) * page.pageSize;
  const endIndex = startIndex + page.pageSize;

  let result = [];

  for (let index = startIndex; index < endIndex; index++) {
    if (index >= contacts.length) break;
    result.push(contacts[index]);
  }

  return result;
}
```

```js
function nextPage() {
  const page = model.viewState.contactsPage;
  page.pageNumber++;
  updateView();
}
```

```js
function previousPage() {
  const page = model.viewState.contactsPage;

  if (page.pageNumber > 1) {
    page.pageNumber--;
  }

  updateView();
}
```

```html
<button onclick="previousPage()">Forrige</button>
<button onclick="nextPage()">Neste</button>
```

</div>

<div class="col">

## 15. Filtervalg i HTML

```js
function createCategoryFilterHtml() {
  const selectedId = model.viewState.contactsPage.selectedCategoryId;

  let html = `
    <select onchange="setCategoryFilter(parseInt(this.value))">
      <option value="0">Alle kategorier</option>
  `;

  for (let category of model.categories) {
    html += `
      <option
        value="${category.id}"
        ${getSelected(category.id, selectedId)}
      >
        ${category.name}
      </option>
    `;
  }

  html += `</select>`;
  return html;
}
```

```js
function setCategoryFilter(categoryId) {
  const page = model.viewState.contactsPage;

  page.selectedCategoryId = categoryId;
  page.pageNumber = 1; // start på første side etter filterendring

  updateView();
}
```

```js
function getSelected(value, selectedValue) {
  if (value === selectedValue) return 'selected="selected"';
  return '';
}
```

---

## 16. Validering i controller

```js
function addContact() {
  const page = model.viewState.contactsPage;

  if (page.newName === '') {
    page.errorMessage = 'Navn må fylles ut';
    updateView();
    return;
  }

  model.contacts.push({
    id: getNextContactId(),
    name: page.newName,
    phone: page.newPhone,
  });

  page.newName = '';
  page.newPhone = '';
  page.errorMessage = '';

  updateView();
}
```

```js
function createErrorHtml() {
  const message = model.viewState.contactsPage.errorMessage;

  if (message === '') return '';

  return `<div class="error">${message}</div>`;
}
```

---

## 17. Kategoriside

```js
// categoryPageView.js
function updateViewCategoriesPage() {
  const page = model.viewState.categoriesPage;

  document.getElementById('app').innerHTML = createPageHtml(/*HTML*/`
    <h1>Kategorier</h1>

    <ul>
      ${createCategoryListItemsHtml()}
    </ul>

    <input
      placeholder="Ny kategori"
      value="${page.newCategoryName}"
      oninput="model.viewState.categoriesPage.newCategoryName=this.value"
    />
    <button onclick="addCategory()">Legg til</button>
  `);
}
```

```js
function createCategoryListItemsHtml() {
  let html = '';

  for (let category of model.categories) {
    html += /*HTML*/`
      <li>
        ${category.name}
        <button onclick="deleteCategory(${category.id})">x</button>
      </li>
    `;
  }

  return html;
}
```

```js
// categoryPageController.js
function addCategory() {
  const page = model.viewState.categoriesPage;

  model.categories.push({
    id: getNextCategoryId(),
    name: page.newCategoryName,
  });

  page.newCategoryName = '';
  updateView();
}
```

</div>

</div>

---

<div class="sheet three-cols">

<div class="col">

## 18. Sjekkliste

```txt
Jeg kan forklare:
[ ] forskjell på app-data og ViewState
[ ] hvorfor updateView() kalles etter endringer
[ ] hvordan currentPage styrer hvilken side som vises
[ ] hvordan inputfelt oppdaterer ViewState
[ ] hvordan controller flytter data fra ViewState til app-data
[ ] hvordan en view-funksjon bygger HTML med template strings
[ ] hvordan en for-løkke lager HTML for en liste
[ ] hvordan relasjoner kan ligge i egen array
[ ] hvorfor vi bruker id i stedet for array-index
[ ] hvorfor vi trekker ut HTML-funksjoner, men ikke lager komponenter
[ ] hvordan filtrering kan gjøres med en egen hjelpefunksjon
[ ] hvordan sortering kan gjøres uten å endre model.contacts direkte
[ ] hvordan paging kan gjøres med pageNumber og pageSize
```

</div>

<div class="col">

## 19. Vanlige regler

```js
// View:
// - leser model
// - lager HTML
// - bør ikke endre app-data

// Controller:
// - endrer model
// - kaller updateView()
// - bør ikke lage mye HTML

// Model:
// - inneholder data og ViewState
// - kjenner ikke til HTML eller DOM
```

```js
// Dette er lov, men bør brukes bevisst:
// - små hjelpefunksjoner som lager HTML
// - små hjelpefunksjoner som finner/filterer/sorterer data
// - inline onclick/oninput/onchange
```

```js
// Ikke gjør dette til hovedmønster nå:
// - komponenter med egen eventhåndtering
// - addEventListener
// - filter/map hvis ikke alle forstår det
// - avansert DOM-manipulasjon
// - moduler/import/export
```

</div>

<div class="col">

## 20. Feilsøking

```js
function addContact() {
  console.log('addContact kjører');
  console.log(model.viewState.contactsPage);

  // resten av koden...
}
```

```txt
Når noe ikke virker, sjekk:
1. Blir controller-funksjonen kalt?
2. Endres riktig sted i model/viewState?
3. Kalles updateView()?
4. Bruker viewet riktig data?
5. Er script-filene lastet i riktig rekkefølge?
```

```js
// Typiske feil:
// - glemt updateView()
// - skrevet model.contact i stedet for model.contacts
// - brukt array.length feil
// - brukt id som index
// - glemt parseInt(this.value)
// - skrevet HTML i controller
```

</div>

</div>

---

## Print-/webstil som kan brukes senere i HTML-versjon

```css
.sheet {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 12px;
}

.col {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

pre {
  font-size: 10px;
  line-height: 1.25;
  margin: 0;
  white-space: pre-wrap;
}

@media print {
  @page {
    size: A4 landscape;
    margin: 8mm;
  }

  body {
    margin: 0;
  }

  .sheet {
    grid-template-columns: repeat(3, 1fr);
  }
}

@media screen and (max-width: 900px) {
  .sheet {
    grid-template-columns: 1fr;
  }
}
```
