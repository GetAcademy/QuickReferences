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
  contacts: [                                    // App-data: det appen handler om
    { id: 1, name: 'Ada', phone: '11111111' },
    { id: 2, name: 'Linus', phone: '22222222' },
    { id: 3, name: 'Grace', phone: '33333333' },
  ],
  categories: [
    { id: 1, name: 'Venner' },
    { id: 2, name: 'Jobb' },
    { id: 3, name: 'Familie' },
  ],  
  contactCategories: [ // Relasjon som egen tabell: hvilke kontakter har hvilke kategorier
    { contactId: 1, categoryId: 2 },
    { contactId: 2, categoryId: 1 },
    { contactId: 2, categoryId: 2 },
    { contactId: 3, categoryId: 3 },
  ],
  viewState: { // ViewState: det brukeren holder på med akkurat nå
    app: {
      currentPage: 'contacts',
      loggedInUser: null, // bare hvis vi skal simulere innlogging
    },
    contactsPage: {
      newName: '',
      newPhone: '',
      selectedCategoryId: 0,
      searchText: '',
    },
    categoriesPage: {
      newCategoryName: '',
    },
  }
};

function addContact() { // Controller flytter fra ViewState til app-data
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

// contactPageView.js
function updateViewContactsPage() {
  const page = model.viewState.contactsPage;

  let html = `
    <h1>Kontaktregister</h1>
    <input
      type="text"
      placeholder="Søk"
      value="${page.searchText}"
      oninput="model.viewState.contactsPage.searchText=this.value;"
    /> <!-- Inputfelt skriver til ViewState -->
    <table><tr><th>Navn</th><th>Telefon</th><th>Kategorier</th><th></th></tr>
  `;

  for (let contact of model.contacts) {
    if (!shouldShowContact(contact)) continue;
    html += createContactRowHtml(contact);
  }

  html += `
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

  document.getElementById('app').innerHTML = createPageHtml(html);
}
```

---

## 5. Trekk ut HTML-funksjoner

```js
// Ikke komponenter. Bare funksjoner som lager HTML.
function createContactRowHtml(contact) {
  return `
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
// Når bruker vi slike funksjoner?
// - når samme HTML trengs flere steder
// - når en view-funksjon blir for lang
// - når én rad / ett kort / én meny skal lages
```

---

## 6. Felles layout og meny

```js
// common.js eller commonView.js
function createPageHtml(contentHtml) {
  return `
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

```js
function goToPage(page) {
  model.viewState.app.currentPage = page;
  updateView();
}
```

```txt
currentPage = 'contacts'
  → updateViewContactsPage()

currentPage = 'categories'
  → updateViewCategoriesPage()
```

</div>

<div class="col">

## 7. Controller: endre data, ikke HTML

```js
// contactPageController.js
function deleteContact(contactId) {
  for (let i = 0; i < model.contacts.length; i++) {
    if (model.contacts[i].id === contactId) {
      model.contacts.splice(i, 1);
      break;
    }
  }

  deleteRelationsForContact(contactId);
  updateView();
}
```

```js
function deleteRelationsForContact(contactId) {
  for (let i = model.contactCategories.length - 1; i >= 0; i--) {
    const relation = model.contactCategories[i];

    if (relation.contactId === contactId) {
      model.contactCategories.splice(i, 1);
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

## 8. Relasjoner i modellen

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
  for (let category of model.categories) {
    if (category.id === categoryId) return category;
  }
  return null;
}
```

```js
function findContact(contactId) {
  for (let contact of model.contacts) {
    if (contact.id === contactId) return contact;
  }
  return null;
}
```

---

## 9. Filtrering uten filter/map

```js
function shouldShowContact(contact) {
  const searchText = model.viewState.contactsPage.searchText;

  if (searchText === '') return true;

  const name = contact.name.toLowerCase();
  const search = searchText.toLowerCase();

  return name.includes(search);
}
```

```js
// I viewet:
for (let contact of model.contacts) {
  if (!shouldShowContact(contact)) continue;
  html += createContactRowHtml(contact);
}
```

---

## 10. Kategoriside

```js
// categoryPageView.js
function updateViewCategoriesPage() {
  const page = model.viewState.categoriesPage;

  let html = `
    <h1>Kategorier</h1>
    <ul>
  `;

  for (let category of model.categories) {
    html += `
      <li>
        ${category.name}
        <button onclick="deleteCategory(${category.id})">x</button>
      </li>
    `;
  }

  html += `
    </ul>

    <input
      placeholder="Ny kategori"
      value="${page.newCategoryName}"
      oninput="model.viewState.categoriesPage.newCategoryName=this.value"
    />
    <button onclick="addCategory()">Legg til</button>
  `;

  document.getElementById('app').innerHTML = createPageHtml(html);
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

---

## 11. Sjekkliste

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
```

---

## 12. Vanlige regler

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
// Ikke gjør dette til hovedmønster nå:
// - komponenter med egen eventhåndtering
// - addEventListener
// - filter/map hvis ikke alle forstår det
// - avansert DOM-manipulasjon
// - moduler/import/export
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
