<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>ShikosamPOS — Ready</title>
<script src="https://cdn.tailwindcss.com"></script>
<script src="https://cdn.jsdelivr.net/npm/chart.js@4.3.0/dist/chart.umd.min.js"></script>
<style>
  body { font-family: Inter, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial; }
  .avatar { width:48px; height:48px; border-radius:9999px; object-fit:cover; }
  .hidden { display:none; }
  .dragging { opacity:0.5; }
  table td input, table td select { width:100%; box-sizing:border-box; }
  .low { background:#facc15; color:#000; } /* yellow */
  .critical { background:#f87171; color:#fff; } /* red */
  .healthy { background:#4ade80; color:#000; } /* green */
</style>
</head>
<body class="bg-gray-100 min-h-screen">

<div id="app" class="flex min-h-screen">
  <!-- Sidebar -->
  <aside class="w-72 bg-gray-900 text-white flex flex-col">
    <div class="p-4 border-b border-gray-800 flex justify-between items-center">
      <div>
        <h1 class="text-xl font-semibold">ShikosamPOS</h1>
        <div class="text-sm opacity-80">Inventory & Location Management</div>
      </div>
      <div class="flex gap-2 items-center">
        <button id="btnTheme" class="text-yellow-400" title="Toggle dark mode">🌙</button>
      </div>
    </div>

    <nav class="flex-1 p-4 space-y-2">
      <button data-view="dashboard" class="view-btn block w-full text-left px-3 py-2 rounded hover:bg-gray-800">🏠 Dashboard</button>
      <button data-view="locations" class="view-btn block w-full text-left px-3 py-2 rounded hover:bg-gray-800">📍 Locations</button>
      <button data-view="shops" class="view-btn block w-full text-left px-3 py-2 rounded hover:bg-gray-800">🏬 Shops</button>
      <button data-view="inventory" class="view-btn block w-full text-left px-3 py-2 rounded hover:bg-gray-800">⚙️ Inventory</button>
      <button data-view="ledger" class="view-btn block w-full text-left px-3 py-2 rounded hover:bg-gray-800">📋 Ledger</button>
      <button data-view="profile" class="view-btn block w-full text-left px-3 py-2 rounded hover:bg-gray-800">👤 Profile</button>
    </nav>

    <div class="p-4 border-t border-gray-800 space-y-3">
      <div class="flex items-center gap-3">
        <img id="sidebarAvatar" class="avatar bg-gray-700" src="" alt="avatar">
        <div>
          <div id="sidebarName" class="font-semibold text-sm">Not logged</div>
          <div id="sidebarRole" class="text-xs opacity-70">—</div>
        </div>
      </div>

      <div class="flex gap-2">
        <button id="btnExport" class="flex-1 bg-blue-600 hover:bg-blue-700 py-1 rounded text-sm">⬇️ Export</button>
        <label class="flex-1 bg-green-600 hover:bg-green-700 py-1 rounded text-center cursor-pointer text-sm">
          ⬆️ Import
          <input id="fileImport" type="file" accept=".json" class="hidden">
        </label>
      </div>
      <button id="btnLogout" class="w-full bg-red-600 hover:bg-red-700 py-2 rounded">🚪 Logout</button>
    </div>
  </aside>

  <!-- Main -->
  <main class="flex-1 p-6 overflow-y-auto">

    <!-- LOGIN -->
    <div id="loginScreen" class="max-w-md mx-auto mt-20 bg-white p-6 rounded shadow">
      <h2 class="text-xl font-semibold mb-3">Sign in</h2>
      <input id="loginUser" placeholder="Username" class="w-full border p-2 rounded mb-2" />
      <input id="loginPass" placeholder="Password" type="password" class="w-full border p-2 rounded mb-4" />
      <div class="flex gap-2">
        <button id="btnLogin" class="flex-1 bg-blue-600 hover:bg-blue-700 text-white py-2 rounded">Login</button>
        <select id="demoUser" class="border p-2 rounded">
          <option value="">Quick demo user</option>
        </select>
      </div>
      <p class="text-sm mt-3 text-gray-600">Demo users: admin / manager / ceo / staff (password: 1234)</p>
    </div>

    <!-- DASHBOARD -->
    <div id="view-dashboard" class="hidden">
      <div class="flex items-center justify-between mb-4">
        <h2 class="text-2xl font-bold">Dashboard</h2>
        <div class="flex gap-2 items-center">
          <label>From <input id="filterFrom" type="date" class="border p-1 rounded"></label>
          <label>To <input id="filterTo" type="date" class="border p-1 rounded"></label>
          <button id="applyDateFilter" class="bg-blue-600 text-white px-3 py-1 rounded">Apply</button>
        </div>
      </div>

      <!-- Alert Panel -->
      <div id="alertPanel" class="mb-4 p-4 rounded shadow hidden bg-yellow-50">
        <h3 class="font-semibold mb-2">⚠️ Stock Alerts</h3>
        <ul id="alertList" class="list-disc list-inside"></ul>
      </div>

      <!-- Cards -->
      <div id="dashboardCards" class="grid grid-cols-3 gap-4 mb-6"></div>

      <!-- Charts -->
      <div class="grid md:grid-cols-2 gap-4">
        <div class="bg-white p-4 rounded shadow"><canvas id="chartByLocation"></canvas></div>
        <div class="bg-white p-4 rounded shadow"><canvas id="chartByCategory"></canvas></div>
        <div class="bg-white p-4 rounded shadow"><canvas id="chartWastage"></canvas></div>
        <div class="bg-white p-4 rounded shadow"><canvas id="chartValueByLocation"></canvas></div>
      </div>

      <!-- Exports -->
      <div class="flex gap-2 mt-4">
        <button id="exportCSV" class="bg-green-600 text-white px-4 py-2 rounded">Export CSV</button>
        <button id="exportJSON" class="bg-blue-600 text-white px-4 py-2 rounded">Export JSON</button>
      </div>
    </div>

    <!-- LOCATIONS -->
    <div id="view-locations" class="hidden">
      <h2 class="text-2xl font-bold mb-4">Locations</h2>
      <div class="bg-white p-4 rounded shadow mb-4 flex gap-2">
        <input id="locName" placeholder="Add new location" class="border p-2 rounded flex-1" />
        <button id="addLoc" class="bg-green-600 text-white px-4 py-2 rounded">Add</button>
      </div>
      <ul id="listLoc" class="space-y-2"></ul>
    </div>

    <!-- SHOPS -->
    <div id="view-shops" class="hidden">
      <h2 class="text-2xl font-bold mb-4">Shops</h2>
      <div class="grid md:grid-cols-2 gap-4">
        <div class="bg-white p-4 rounded shadow">
          <h3 class="font-semibold mb-2">Locations</h3>
          <ul id="locListForShops" class="space-y-2"></ul>
        </div>
        <div class="bg-white p-4 rounded shadow">
          <div class="flex gap-2 mb-3">
            <input id="shopName" placeholder="New shop name" class="border p-2 rounded flex-1" />
            <button id="addShop" class="bg-green-600 text-white px-4 py-2 rounded">Add</button>
          </div>
          <ul id="listShop" class="space-y-2"></ul>
        </div>
      </div>
    </div>

    <!-- INVENTORY / INTAKE -->
    <div id="view-inventory" class="hidden">
      <h2 class="text-2xl font-bold mb-4">Inventory / Intake</h2>

      <div class="bg-white p-4 rounded shadow mb-4 grid md:grid-cols-3 gap-2">
        <input id="itemName" placeholder="Item name" class="border p-2 rounded" />
        <input id="itemQty" type="number" placeholder="Qty" class="border p-2 rounded" />
        <input id="itemUnit" placeholder="Unit (kg/piece/l)" class="border p-2 rounded" />
        <input id="itemPrice" type="number" placeholder="Unit Price" class="border p-2 rounded" />
        <input id="itemCurrency" placeholder="Currency ($)" class="border p-2 rounded" />
        <select id="itemCategory" class="border p-2 rounded">
          <option>Meat</option><option>Poultry</option><option>Dry Goods</option><option>Fruits</option>
          <option>Vegetables</option><option>Dairy</option><option>Beverages</option><option>Pastry</option><option>Others</option>
        </select>
        <select id="itemLocation" class="border p-2 rounded">
          <!-- filled dynamically -->
        </select>
        <input id="itemDate" type="date" class="border p-2 rounded" />
        <div class="flex gap-2">
          <input id="itemLow" type="number" placeholder="Low threshold" class="border p-2 rounded w-1/2" />
          <input id="itemCritical" type="number" placeholder="Critical threshold" class="border p-2 rounded w-1/2" />
        </div>
        <button id="addItem" class="bg-green-600 text-white px-4 py-2 rounded md:col-span-3">Add Item</button>
      </div>

      <ul id="listIntake" class="space-y-2"></ul>
    </div>

    <!-- LEDGER -->
    <div id="view-ledger" class="hidden">
      <div class="flex items-center justify-between mb-4">
        <h2 class="text-2xl font-bold">Location Stock Ledger</h2>
        <div class="flex gap-2">
          <select id="filterLocation" class="border p-2 rounded">
            <option value="all">All Locations</option>
          </select>
          <select id="filterCategory" class="border p-2 rounded">
            <option value="all">All Categories</option>
            <option>Meat</option><option>Poultry</option><option>Dry Goods</option><option>Fruits</option>
            <option>Vegetables</option><option>Dairy</option><option>Beverages</option><option>Pastry</option><option>Others</option>
          </select>
          <button id="applyFilters" class="bg-blue-600 text-white px-3 py-1 rounded">Apply Filters</button>
        </div>
      </div>

      <div class="overflow-x-auto bg-white rounded shadow">
        <table class="min-w-full">
          <thead class="bg-gray-100">
            <tr>
              <th class="p-2 border">Date</th>
              <th class="p-2 border">Location</th>
              <th class="p-2 border">Category</th>
              <th class="p-2 border">Item</th>
              <th class="p-2 border">Qty</th>
              <th class="p-2 border">Issued</th>
              <th class="p-2 border">Wastage</th>
              <th class="p-2 border">Price</th>
              <th class="p-2 border">Value</th>
            </tr>
          </thead>
          <tbody id="ledgerBody"></tbody>
        </table>
      </div>
    </div>

    <!-- PROFILE -->
    <div id="view-profile" class="hidden max-w-xl">
      <h2 class="text-2xl font-bold mb-4">Profile</h2>
      <div class="bg-white p-4 rounded shadow">
        <div class="flex gap-3 mb-4 items-center">
          <img id="profileImg" class="avatar bg-gray-300" src="" alt="profile">
          <div>
            <div id="profName" class="font-semibold text-lg">Admin</div>
            <div id="profRole" class="text-sm opacity-70">admin@example.com</div>
          </div>
        </div>
        <input id="profDisplay" placeholder="Display name" class="border p-2 rounded w-full mb-2" />
        <input id="profFile" type="file" accept="image/*" class="mb-2" />
        <button id="saveProf" class="bg-blue-600 text-white px-4 py-2 rounded">Save</button>
      </div>
    </div>

  </main>
</div>

<script>
/* --------------------------
  Utilities & localStorage
   -------------------------- */
const db = {
  get: k => JSON.parse(localStorage.getItem(k) || '[]'),
  set: (k, v) => localStorage.setItem(k, JSON.stringify(v))
};

function uid(){ return Math.random().toString(36).slice(2,9); }
function todayISO(){ return new Date().toISOString().split('T')[0]; }

/* --------------------------
  Demo users / starter data
   -------------------------- */
if(!localStorage.getItem('users')){
  db.set('users', [
    { username:'admin', password:'1234', role:'Admin', displayName:'Admin User', avatar:'' },
    { username:'manager', password:'1234', role:'Manager', displayName:'Manager', avatar:'' },
    { username:'ceo', password:'1234', role:'CEO', displayName:'CEO', avatar:'' },
    { username:'staff', password:'1234', role:'Staff', displayName:'Staff', avatar:'' }
  ]);
}

if(!localStorage.getItem('locations')){
  db.set('locations', [
    { id: uid(), name: 'City View' },
    { id: uid(), name: 'Breakfast' },
    { id: uid(), name: 'Olow Cafe' },
    { id: uid(), name: 'Kitchen Staff' },
    { id: uid(), name: 'Pastry' },
    { id: uid(), name: 'Laundry' }
  ]);
}

if(!localStorage.getItem('intake')){
  db.set('intake', [
    { id: uid(), name:'Rice', qty:50, unit:'kg', price:5, currency:'$', category:'Dry Goods', location:'City View', date: todayISO(), issued:0, wastage:0, lowThreshold:10, criticalThreshold:3 },
    { id: uid(), name:'Chicken Breast', qty:30, unit:'kg', price:8, currency:'$', category:'Poultry', location:'Breakfast', date: todayISO(), issued:0, wastage:0, lowThreshold:8, criticalThreshold:3 },
    { id: uid(), name:'Milk', qty:20, unit:'liter', price:2, currency:'$', category:'Dairy', location:'Kitchen Staff', date: todayISO(), issued:0, wastage:0, lowThreshold:5, criticalThreshold:2 }
  ]);
}

/* --------------------------
  UI helper references
   -------------------------- */
const loginScreen = document.getElementById('loginScreen');
const currentUser = () => JSON.parse(localStorage.getItem('currentUser') || 'null');
const demoSelect = document.getElementById('demoUser');

/* populate quick demo user dropdown */
function populateDemoUsers(){
  const users = db.get('users');
  demoSelect.innerHTML = '<option value="">Quick demo user</option>';
  users.forEach(u => {
    const opt = document.createElement('option');
    opt.value = u.username;
    opt.textContent = `${u.displayName} (${u.role})`;
    demoSelect.appendChild(opt);
  });
}
populateDemoUsers();

/* --------------------------
  Theme toggle
   -------------------------- */
const btnTheme = document.getElementById('btnTheme');
function setTheme(mode){
  document.documentElement.classList.toggle('dark', mode === 'dark');
  localStorage.setItem('theme', mode);
  btnTheme.textContent = mode === 'dark' ? '☀️' : '🌙';
}
setTheme(localStorage.getItem('theme') || 'light');
btnTheme.onclick = () => setTheme(document.documentElement.classList.contains('dark') ? 'light' : 'dark');

/* --------------------------
  Authentication & role handling
   -------------------------- */
document.getElementById('demoUser').onchange = (e) => {
  const username = e.target.value;
  if(!username) return;
  document.getElementById('loginUser').value = username;
  document.getElementById('loginPass').value = '1234';
};

document.getElementById('btnLogin').onclick = () => {
  const username = document.getElementById('loginUser').value.trim();
  const password = document.getElementById('loginPass').value.trim();
  if(!username || !password){ alert('Enter username and password'); return; }
  const user = db.get('users').find(u => u.username === username && u.password === password);
  if(!user){ alert('Invalid credentials'); return; }
  localStorage.setItem('currentUser', JSON.stringify(user));
  applyUserToUI(user);
  applyRolePermissions(user.role);
  showView('dashboard');
  refreshAll();
};

/* Logout */
document.getElementById('btnLogout').onclick = () => {
  localStorage.removeItem('currentUser');
  location.reload();
};

function applyUserToUI(user){
  document.getElementById('sidebarName').textContent = user.displayName || user.username;
  document.getElementById('sidebarRole').textContent = user.role;
  document.getElementById('profName').textContent = user.displayName || user.username;
  document.getElementById('profRole').textContent = user.role;
  if(user.avatar){
    document.getElementById('sidebarAvatar').src = user.avatar;
    document.getElementById('profileImg').src = user.avatar;
  }
}

/* Role-based permissions */
function applyRolePermissions(role){
  const buttons = document.querySelectorAll('aside nav button[data-view]');
  buttons.forEach(btn => {
    const view = btn.dataset.view;
    btn.disabled = false;
    // Example rules
    if(role === 'Staff'){
      // Staff limited: can't add locations or manage users (we hide heavy views)
      if(['locations','shops'].includes(view)) btn.disabled = true;
    }
    if(role === 'Manager'){
      // Manager limited: no location create
      if(['locations'].includes(view)) btn.disabled = true;
    }
    btn.style.opacity = btn.disabled ? 0.5 : 1;
  });

  // hide import/export for staff
  if(role === 'Staff'){
    document.getElementById('btnExport').style.display = 'none';
    document.getElementById('fileImport').parentElement.style.display = 'none';
  } else {
    document.getElementById('btnExport').style.display = '';
    document.getElementById('fileImport').parentElement.style.display = '';
  }
}

/* If there's an active user, auto apply */
if(currentUser()){
  applyUserToUI(currentUser());
  applyRolePermissions(currentUser().role);
  loginScreen.classList.add('hidden');
  document.getElementById('view-dashboard').classList.remove('hidden');
}

/* --------------------------
  View switching
   -------------------------- */
const views = document.querySelectorAll('[id^="view-"]');
function showView(v){
  views.forEach(x => x.classList.add('hidden'));
  const el = document.getElementById('view-' + v);
  if(el) el.classList.remove('hidden');
}
document.querySelectorAll('aside nav button[data-view]').forEach(b => b.addEventListener('click', () => {
  if(b.disabled) return;
  showView(b.dataset.view);
}));

/* --------------------------
  Locations & Shops
   -------------------------- */
function renderLocations(){
  const locs = db.get('locations');
  const listLoc = document.getElementById('listLoc');
  listLoc.innerHTML = '';
  locs.forEach((l, i) => {
    const li = document.createElement('li');
    li.className = 'bg-white p-3 rounded shadow flex justify-between items-center';
    li.innerHTML = `<div contenteditable="true" onblur="editLocation(${i}, this)">${l.name}</div>
      <div class="flex gap-2">
        <button onclick="delLocation(${i})" class="text-red-600">Delete</button>
      </div>`;
    listLoc.appendChild(li);
  });
  // fill selects
  fillLocationSelects();
}
window.editLocation = (i, el) => {
  const a = db.get('locations');
  a[i].name = el.textContent.trim() || a[i].name;
  db.set('locations', a);
  renderLocations();
};
function delLocation(i){
  if(!confirm('Delete this location?')) return;
  const a = db.get('locations'); a.splice(i,1); db.set('locations', a);
  renderLocations(); refreshAll();
}
document.getElementById('addLoc').onclick = () => {
  const name = document.getElementById('locName').value.trim();
  if(!name) return alert('Enter a name');
  const a = db.get('locations'); a.push({ id: uid(), name }); db.set('locations', a);
  document.getElementById('locName').value = ''; renderLocations(); refreshAll();
};

/* Shops per location (simple) */
let selectedLocIndex = null;
function renderLocForShops(){
  const locs = db.get('locations');
  const ul = document.getElementById('locListForShops'); ul.innerHTML = '';
  locs.forEach((l,i) => {
    const li = document.createElement('li');
    li.className = 'p-2 bg-gray-50 rounded cursor-pointer hover:bg-gray-100';
    li.textContent = l.name;
    li.onclick = () => { selectedLocIndex = i; renderShops(); };
    ul.appendChild(li);
  });
}
function renderShops(){
  const locs = db.get('locations'); const ul = document.getElementById('listShop'); ul.innerHTML = '';
  if(selectedLocIndex == null) return;
  const loc = locs[selectedLocIndex];
  loc.shops = loc.shops || [];
  if(loc.shops.length === 0) ul.innerHTML = '<li class="text-gray-500 p-2">No shops yet</li>';
  loc.shops.forEach((s,i) => {
    const li = document.createElement('li');
    li.className = 'p-2 bg-white rounded shadow flex justify-between';
    li.innerHTML = `<div contenteditable="true" onblur="editShop(${selectedLocIndex},${i},this)">${s.name}</div>
      <button onclick="delShop(${selectedLocIndex},${i})" class="text-red-600">Delete</button>`;
    ul.appendChild(li);
  });
}
window.editShop = (locIdx, shopIdx, el) => {
  const locs = db.get('locations');
  locs[locIdx].shops[shopIdx].name = el.textContent.trim() || locs[locIdx].shops[shopIdx].name;
  db.set('locations', locs);
  renderShops();
};
function delShop(locIdx, shopIdx){
  if(!confirm('Delete this shop?')) return;
  const locs = db.get('locations'); locs[locIdx].shops.splice(shopIdx,1); db.set('locations', locs); renderShops(); refreshAll();
}
document.getElementById('addShop').onclick = () => {
  if(selectedLocIndex === null) return alert('Select location first');
  const name = document.getElementById('shopName').value.trim();
  if(!name) return;
  const locs = db.get('locations'); locs[selectedLocIndex].shops = locs[selectedLocIndex].shops || [];
  locs[selectedLocIndex].shops.push({ name }); db.set('locations', locs);
  document.getElementById('shopName').value = ''; renderShops(); refreshAll();
};

/* fill location selects */
function fillLocationSelects(){
  const locs = db.get('locations');
  // itemLocation
  const selLoc = document.getElementById('itemLocation');
  selLoc.innerHTML = '';
  locs.forEach(l => {
    const opt = document.createElement('option'); opt.value = l.name; opt.textContent = l.name; selLoc.appendChild(opt);
  });
  // filterLocation in ledger
  const filterLoc = document.getElementById('filterLocation');
  const invLoc = document.getElementById('invLoc');
  if(filterLoc){
    const cur = filterLoc.value || 'all';
    filterLoc.innerHTML = '<option value="all">All Locations</option>';
    locs.forEach(l => filterLoc.appendChild(new Option(l.name, l.name)));
    filterLoc.value = cur;
  }
}

/* --------------------------
  Intake / Inventory
   -------------------------- */
function renderIntakeList(){
  const intake = db.get('intake');
  const ul = document.getElementById('listIntake'); ul.innerHTML = '';
  intake.forEach((it, idx) => {
    const li = document.createElement('li');
    li.className = 'bg-white p-3 rounded shadow flex justify-between items-center';
    li.innerHTML = `<div>
        <div class="font-semibold">${it.name} <span class="text-sm text-gray-500">[${it.category}]</span></div>
        <div class="text-sm text-gray-600">${it.qty} ${it.unit} • ${it.currency}${it.price} • ${it.location} • ${it.date}</div>
      </div>
      <div class="flex gap-2">
        <button onclick="quickIssue(${idx})" class="bg-blue-600 text-white px-2 py-1 rounded">Issue</button>
        <button onclick="quickWaste(${idx})" class="bg-orange-500 text-white px-2 py-1 rounded">Waste</button>
        <button onclick="editIntakeInline(${idx})" class="text-gray-700 px-2">Edit</button>
        <button onclick="deleteIntake(${idx})" class="text-red-600 px-2">Delete</button>
      </div>`;
    ul.appendChild(li);
  });
}
document.getElementById('addItem').onclick = () => {
  const name = document.getElementById('itemName').value.trim();
  const qty = Number(document.getElementById('itemQty').value) || 0;
  const unit = document.getElementById('itemUnit').value.trim() || 'pcs';
  const price = Number(document.getElementById('itemPrice').value) || 0;
  const currency = document.getElementById('itemCurrency').value.trim() || '$';
  const category = document.getElementById('itemCategory').value;
  const location = document.getElementById('itemLocation').value;
  const date = document.getElementById('itemDate').value || todayISO();
  const lowThreshold = Number(document.getElementById('itemLow').value) || 5;
  const criticalThreshold = Number(document.getElementById('itemCritical').value) || 2;

  if(!name || qty <= 0) return alert('Enter item name and positive qty');

  const a = db.get('intake');
  a.push({ id: uid(), name, qty, unit, price, currency, category, location, date, issued: 0, wastage: 0, lowThreshold, criticalThreshold });
  db.set('intake', a);

  // reset fields
  document.getElementById('itemName').value='';
  document.getElementById('itemQty').value='';
  document.getElementById('itemUnit').value='';
  document.getElementById('itemPrice').value='';
  document.getElementById('itemCurrency').value='';
  document.getElementById('itemDate').value='';

  renderIntakeList(); refreshAll();
};

function deleteIntake(i){ if(!confirm('Delete item?')) return; const a = db.get('intake'); a.splice(i,1); db.set('intake', a); renderIntakeList(); refreshAll(); }
function editIntakeInline(i){
  const a = db.get('intake'); const it = a[i];
  const newName = prompt('Name', it.name); if(!newName) return;
  it.name = newName;
  it.qty = Number(prompt('Qty', it.qty) || it.qty);
  it.price = Number(prompt('Price', it.price) || it.price);
  it.location = prompt('Location', it.location) || it.location;
  it.category = prompt('Category', it.category) || it.category;
  db.set('intake', a); renderIntakeList(); refreshAll();
}

/* Issue / Waste functions (prompt) */
function quickIssue(i){
  const a = db.get('intake'); const it = a[i];
  const qty = Number(prompt(`Quantity to issue from ${it.name} (available ${it.qty})`, 1) || 0);
  if(qty <= 0 || qty > it.qty) return alert('Invalid qty');
  it.qty -= qty; it.issued = (it.issued || 0) + qty; db.set('intake', a);
  renderIntakeList(); renderLedger(); refreshAll();
}
function quickWaste(i){
  const a = db.get('intake'); const it = a[i];
  const qty = Number(prompt(`Quantity wasted/expired from ${it.name} (available ${it.qty})`, 1) || 0);
  if(qty <= 0 || qty > it.qty) return alert('Invalid qty');
  it.qty -= qty; it.wastage = (it.wastage || 0) + qty; db.set('intake', a);
  renderIntakeList(); renderLedger(); refreshAll();
}

/* quick issue via small decrement/increment used later in ledger */
function issueFromIdx(i, qty){
  const a = db.get('intake'); const it = a[i];
  const take = Math.min(qty, it.qty);
  if(take <= 0) return;
  it.qty -= take; it.issued = (it.issued||0)+take; db.set('intake', a);
}
function wasteFromIdx(i, qty){
  const a = db.get('intake'); const it = a[i];
  const take = Math.min(qty, it.qty);
  if(take <= 0) return;
  it.qty -= take; it.wastage = (it.wastage||0)+take; db.set('intake', a);
}

/* --------------------------
  Alerts & Alert panel (interactive)
   -------------------------- */
function updateAlertPanel(from, to){
  const intake = filterByDateRange(db.get('intake'), from, to);
  const alerts = intake.filter(i => i.qty <= (i.lowThreshold || 5));
  const panel = document.getElementById('alertPanel');
  const list = document.getElementById('alertList');
  list.innerHTML = '';
  if(alerts.length === 0){ panel.classList.add('hidden'); return; }
  alerts.forEach((i) => {
    const li = document.createElement('li');
    li.style.cursor = 'pointer';
    if(i.qty <= (i.criticalThreshold || 2)){
      li.textContent = `${i.name} at ${i.location}: CRITICAL ${i.qty} left`;
      li.className = 'text-red-600 font-bold';
    } else {
      li.textContent = `${i.name} at ${i.location}: Low ${i.qty} left`;
      li.className = 'text-yellow-600';
    }
    li.onclick = () => {
      // filter ledger to this item
      showView('ledger');
      document.getElementById('filterLocation').value = i.location;
      document.getElementById('filterCategory').value = i.category;
      renderLedger();
    };
    list.appendChild(li);
  });
  panel.classList.remove('hidden');
}

/* --------------------------
  Ledger (table) with filters & inline editing & quick buttons
   -------------------------- */
function renderLedger(){
  const intake = db.get('intake');
  const tbody = document.getElementById('ledgerBody'); tbody.innerHTML = '';
  const locFilter = document.getElementById('filterLocation').value || 'all';
  const catFilter = document.getElementById('filterCategory').value || 'all';
  const from = document.getElementById('filterFrom').value || null;
  const to = document.getElementById('filterTo').value || null;

  const filtered = filterByDateRange(intake, from, to)
    .filter(i => (locFilter === 'all' || i.location === locFilter) && (catFilter === 'all' || i.category === catFilter));

  filtered.forEach((i, idx) => {
    const globalIdx = db.get('intake').findIndex(x => x.id === i.id);
    const tr = document.createElement('tr'); tr.className = 'border-b';
    const total = (i.qty * i.price).toFixed(2);
    tr.innerHTML = `
      <td class="p-2 border"><input value="${i.date}" onchange="updateLedgerField('${i.id}','date',this.value)"></td>
      <td class="p-2 border"><select onchange="updateLedgerField('${i.id}','location',this.value)">${locationOptions(i.location)}</select></td>
      <td class="p-2 border"><select onchange="updateLedgerField('${i.id}','category',this.value)">${categoryOptions(i.category)}</select></td>
      <td class="p-2 border"><input value="${escapeHtml(i.name)}" onchange="updateLedgerField('${i.id}','name',this.value)"></td>
      <td class="p-2 border"><input type="number" value="${i.qty}" onchange="updateLedgerField('${i.id}','qty',this.value)"></td>
      <td class="p-2 border flex items-center gap-1">
        <button onclick="quickLedgerAdjust('${i.id}','issued',-1)" class="px-1 bg-gray-200 rounded">-</button>
        <span id="issued-${i.id}">${i.issued||0}</span>
        <button onclick="quickLedgerAdjust('${i.id}','issued',1)" class="px-1 bg-green-200 rounded">+</button>
      </td>
      <td class="p-2 border flex items-center gap-1">
        <button onclick="quickLedgerAdjust('${i.id}','wastage',-1)" class="px-1 bg-gray-200 rounded">-</button>
        <span id="waste-${i.id}">${i.wastage||0}</span>
        <button onclick="quickLedgerAdjust('${i.id}','wastage',1)" class="px-1 bg-red-200 rounded">+</button>
      </td>
      <td class="p-2 border"><input type="number" value="${i.price}" onchange="updateLedgerField('${i.id}','price',this.value)"></td>
      <td class="p-2 border">${i.currency || '$'}${total}</td>
    `;
    tbody.appendChild(tr);

    // color qty cell after append
    setTimeout(()=>{ colorizeRowByQty(i.id); }, 10);
  });
}
function escapeHtml(s){ return (s+'').replace(/&/g, "&amp;").replace(/</g,"&lt;").replace(/>/g,"&gt;"); }
function locationOptions(selected){
  const locs = db.get('locations'); return locs.map(l => `<option ${l.name===selected?'selected':''}>${l.name}</option>`).join('');
}
function categoryOptions(selected){
  const cats = ['Meat','Poultry','Dry Goods','Fruits','Vegetables','Dairy','Beverages','Pastry','Others'];
  return cats.map(c => `<option ${c===selected?'selected':''}>${c}</option>`).join('');
}
function updateLedgerField(id, field, value){
  const a = db.get('intake'); const idx = a.findIndex(x => x.id === id); if(idx === -1) return;
  if(['qty','price','issued','wastage','lowThreshold','criticalThreshold'].includes(field)) value = Number(value) || 0;
  a[idx][field] = value;
  db.set('intake', a);
  renderLedger(); refreshAll();
}
/* quick adjust */
function quickLedgerAdjust(id, field, delta){
  const a = db.get('intake'); const idx = a.findIndex(x => x.id === id); if(idx === -1) return;
  if(field === 'issued'){
    const allowed = Math.max(0, a[idx].qty);
    const newVal = Math.min(Math.max((a[idx].issued||0) + delta, 0), allowed + (a[idx].issued||0));
    // if increasing issued, reduce qty
    if(delta > 0){
      if(a[idx].qty >= delta){ a[idx].qty -= delta; a[idx].issued = (a[idx].issued||0) + delta; }
    } else {
      // decreasing issued returns stock to qty
      const remove = Math.min(Math.abs(delta), a[idx].issued||0);
      a[idx].issued = (a[idx].issued||0) - remove;
      a[idx].qty += remove;
    }
  } else if(field === 'wastage'){
    if(delta > 0){
      if(a[idx].qty >= delta){ a[idx].qty -= delta; a[idx].wastage = (a[idx].wastage||0) + delta; }
    } else {
      // reduce wastage and return to qty
      const remove = Math.min(Math.abs(delta), a[idx].wastage||0);
      a[idx].wastage = (a[idx].wastage||0) - remove;
      a[idx].qty += remove;
    }
  }
  db.set('intake', a);
  renderLedger(); refreshAll();
}

/* colorize qty cell */
function colorizeRowByQty(id){
  const a = db.get('intake'); const it = a.find(x => x.id === id); if(!it) return;
  // find the row
  const issuedSpan = document.getElementById('issued-'+id);
  const wasteSpan = document.getElementById('waste-'+id);
  // find parent row for qty cell is the 5th cell (index 4)
  const tbody = document.getElementById('ledgerBody');
  for(const row of tbody.children){
    if(row.querySelector(`#issued-${id}`)){
      const qtyCell = row.children[4];
      qtyCell.classList.remove('low','critical','healthy');
      const qt = it.qty || 0;
      if(qt <= (it.criticalThreshold || 2)) qtyCell.classList.add('critical');
      else if(qt <= (it.lowThreshold || 5)) qtyCell.classList.add('low');
      else qtyCell.classList.add('healthy');
      // update spans
      if(issuedSpan) issuedSpan.textContent = it.issued || 0;
      if(wasteSpan) wasteSpan.textContent = it.wastage || 0;
    }
  }
}

/* apply filters button */
document.getElementById('applyFilters').onclick = () => renderLedger();

/* --------------------------
  Filtering by date helper
   -------------------------- */
function filterByDateRange(arr, from, to){
  if(!from && !to) return arr;
  const f = from ? new Date(from + 'T00:00:00') : null;
  const t = to ? new Date(to + 'T23:59:59') : null;
  return arr.filter(i => {
    const d = new Date((i.date || todayISO()) + 'T00:00:00');
    if(f && d < f) return false;
    if(t && d > t) return false;
    return true;
  });
}

/* --------------------------
  Charts & Dashboard metrics
   -------------------------- */
let chartByLocation = null, chartByCategory = null, chartWastage = null, chartValueByLocation = null;

function drawCharts(from, to){
  const intake = filterByDateRange(db.get('intake'), from, to);
  const locs = db.get('locations').map(l => l.name);
  // stock by location
  const stockByLoc = locs.map(l => intake.filter(i => i.location === l).reduce((s, it) => s + (it.qty || 0), 0));
  const issuedByLoc = locs.map(l => intake.filter(i => i.location === l).reduce((s, it) => s + (it.issued || 0), 0));
  // category totals
  const categories = [...new Set(intake.map(i => i.category))].sort();
  const stockByCat = categories.map(c => intake.filter(i => i.category === c).reduce((s, it) => s + (it.qty || 0), 0));
  const wastageByCat = categories.map(c => intake.filter(i => i.category === c).reduce((s, it) => s + (it.wastage || 0), 0));
  // value by location
  const valueByLoc = locs.map(l => intake.filter(i => i.location === l).reduce((s, it) => s + ((it.qty || 0) * (it.price || 0)), 0));

  // By Location chart
  if(chartByLocation) chartByLocation.destroy();
  chartByLocation = new Chart(document.getElementById('chartByLocation'), {
    type: 'bar',
    data: { labels: locs, datasets: [{ label: 'Stock on hand', data: stockByLoc, backgroundColor: 'rgba(16,185,129,0.8)' }] },
    options: { responsive: true, plugins: { legend: { display: false } } }
  });

  // By Category chart
  if(chartByCategory) chartByCategory.destroy();
  chartByCategory = new Chart(document.getElementById('chartByCategory'), {
    type: 'bar',
    data: { labels: categories, datasets: [{ label: 'Stock by category', data: stockByCat, backgroundColor: 'rgba(59,130,246,0.8)' }] },
    options: { responsive: true }
  });

  // Wastage pie
  if(chartWastage) chartWastage.destroy();
  chartWastage = new Chart(document.getElementById('chartWastage'), {
    type: 'pie',
    data: { labels: categories, datasets: [{ label: 'Wastage', data: wastageByCat, backgroundColor: categories.map((_,i)=>`hsl(${i*40},70%,60%)`) }]},
    options: { responsive: true, plugins:{legend:{position:'bottom'}} }
  });

  // Value by location
  if(chartValueByLocation) chartValueByLocation.destroy();
  chartValueByLocation = new Chart(document.getElementById('chartValueByLocation'), {
    type: 'bar',
    data: { labels: locs, datasets: [{ label: 'Inventory value', data: valueByLoc, backgroundColor: 'rgba(245,158,11,0.8)' }]},
    options: { responsive: true, plugins:{legend:{display:false}}, scales:{y:{beginAtZero:true}} }
  });
}

/* Dashboard cards per location */
function renderDashboardCards(from, to){
  const container = document.getElementById('dashboardCards');
  container.innerHTML = '';
  const intake = filterByDateRange(db.get('intake'), from, to);
  const locs = db.get('locations').map(l => l.name);
  locs.forEach(loc => {
    const items = intake.filter(i => i.location === loc);
    const totalStock = items.reduce((s,i)=>s+(i.qty||0),0);
    const totalIssued = items.reduce((s,i)=>s+(i.issued||0),0);
    const totalWastage = items.reduce((s,i)=>s+(i.wastage||0),0);
    const div = document.createElement('div');
    div.className = 'bg-white p-4 rounded shadow';
    div.innerHTML = `<div class="font-semibold">${loc}</div>
                     <div class="mt-2">Stock: <strong>${totalStock}</strong></div>
                     <div>Issued: <strong>${totalIssued}</strong></div>
                     <div>Wastage: <strong>${totalWastage}</strong></div>`;
    container.appendChild(div);
  });
}

/* Refresh all: called whenever data changes */
function refreshAll(){
  // populate selects etc
  renderLocations();
  renderLocForShops();
  fillLocationSelects();
  renderIntakeList();
  renderLedger();
  // date range
  const from = document.getElementById('filterFrom').value || null;
  const to = document.getElementById('filterTo').value || null;
  renderDashboardCards(from, to);
  drawCharts(from, to);
  updateAlertPanel(from, to);
}

/* --------------------------
  Export / Import (CSV / JSON)
   -------------------------- */
document.getElementById('exportCSV').onclick = () => {
  const from = document.getElementById('filterFrom').value || null;
  const to = document.getElementById('filterTo').value || null;
  const data = filterByDateRange(db.get('intake'), from, to);
  if(data.length === 0) return alert('No data to export for selected range');
  const headers = ['date','location','category','name','qty','issued','wastage','unit','price','currency','total'];
  const rows = data.map(i => [i.date,i.location,i.category,i.name,i.qty,i.issued,i.wastage,i.unit,i.price,i.currency,((i.qty||0)*(i.price||0)).toFixed(2)]);
  const csv = [headers.join(','), ...rows.map(r => r.map(v => `"${(''+v).replace(/"/g,'""')}"`).join(','))].join('\n');
  const blob = new Blob([csv], {type:'text/csv'}); const a = document.createElement('a');
  a.href = URL.createObjectURL(blob); a.download = `inventory_${from||'start'}_${to||'end'}.csv`; a.click();
};
document.getElementById('exportJSON').onclick = () => {
  const from = document.getElementById('filterFrom').value || null;
  const to = document.getElementById('filterTo').value || null;
  const data = filterByDateRange(db.get('intake'), from, to);
  if(data.length === 0) return alert('No data to export for selected range');
  const blob = new Blob([JSON.stringify(data, null, 2)], {type:'application/json'}); const a = document.createElement('a');
  a.href = URL.createObjectURL(blob); a.download = `inventory_${from||'start'}_${to||'end'}.json`; a.click();
};

/* Import JSON */
document.getElementById('fileImport').onchange = (e) => {
  const f = e.target.files[0]; if(!f) return;
  const reader = new FileReader();
  reader.onload = (ev) => {
    try {
      const data = JSON.parse(ev.target.result);
      if(Array.isArray(data.intake) || Array.isArray(data)){
        // merge or replace? prompt
        if(confirm('Replace existing intake data with imported data? OK = replace, Cancel = merge')){
          if(Array.isArray(data.intake)) db.set('intake', data.intake); else db.set('intake', data);
        } else {
          const existing = db.get('intake'); const toAdd = Array.isArray(data.intake)?data.intake:data;
          db.set('intake', existing.concat(toAdd));
        }
        refreshAll(); alert('Import complete');
      } else {
        alert('Invalid import file (no intake array found)');
      }
    } catch(err){
      alert('Failed to import: ' + err.message);
    }
  };
  reader.readAsText(f);
};

/* quick export button (same as JSON) */
document.getElementById('btnExport').onclick = () => document.getElementById('exportJSON').click();

/* --------------------------
  Profile save (avatar)
   -------------------------- */
document.getElementById('saveProf').onclick = () => {
  const name = document.getElementById('profDisplay').value.trim();
  const file = document.getElementById('profFile').files[0];
  const user = currentUser();
  if(!user) return alert('No user session');
  const users = db.get('users'); const uidx = users.findIndex(x => x.username === user.username);
  if(uidx === -1) return;
  if(name) users[uidx].displayName = name;
  if(file){
    const r = new FileReader();
    r.onload = e => {
      users[uidx].avatar = e.target.result;
      db.set('users', users);
      localStorage.setItem('currentUser', JSON.stringify(users[uidx]));
      applyUserToUI(users[uidx]);
      alert('Profile saved');
    };
    r.readAsDataURL(file);
  } else {
    db.set('users', users);
    localStorage.setItem('currentUser', JSON.stringify(users[uidx]));
    applyUserToUI(users[uidx]);
    alert('Profile saved');
  }
};

/* --------------------------
  Helpers: ledger rendering & utilities that need to be available globally
   -------------------------- */
window.renderLedger = renderLedger;
window.updateLedgerField = updateLedgerField;
window.quickLedgerAdjust = quickLedgerAdjust;

/* --------------------------
  Init
   -------------------------- */
renderLocations();
renderLocForShops();
fillLocationSelects();
renderIntakeList();
renderLedger();
refreshAll();

/* Apply date filter */
document.getElementById('applyDateFilter').onclick = () => { refreshAll(); };

/* When app loads, if no user logged in, show login */
if(!currentUser()){
  showView('loginScreen');
  // login screen visible by default
} else {
  loginScreen.classList.add('hidden');
  showView('dashboard');
}

/* helper to set initial view if logged in */
function showView(name){
  // name may be like 'dashboard' or 'loginScreen' if called from code earlier
  if(name === 'loginScreen'){ views.forEach(x => x.classList.add('hidden')); loginScreen.classList.remove('hidden'); return; }
  loginScreen.classList.add('hidden');
  views.forEach(x => x.classList.add('hidden'));
  const el = document.getElementById('view-' + name);
  if(el) el.classList.remove('hidden');
}

/* color rows initially */
setTimeout(() => { document.querySelectorAll('#ledgerBody tr').forEach(r => {}); }, 400);

</script>
</body>
</html>
