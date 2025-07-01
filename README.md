<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Gorakhpur Glass & Arts - Product Dashboard</title>
  <style>
    body {
      font-family: sans-serif;
      background-color: #f4f4f4;
      margin: 0;
      padding: 0;
    }
    header {
      background-color: #007bff;
      color: white;
      padding: 1rem 2rem;
      text-align: center;
    }
    main {
      padding: 2rem;
      max-width: 900px;
      margin: auto;
    }
    label {
      font-weight: bold;
      display: block;
      margin-top: 1rem;
    }
    input, textarea, select {
      width: 100%;
      padding: 0.5rem;
      margin-top: 0.5rem;
      margin-bottom: 1rem;
    }
    button {
      padding: 0.6rem 1.2rem;
      margin-right: 0.5rem;
      background-color: #28a745;
      color: white;
      border: none;
      cursor: pointer;
    }
    .product-card {
      background: white;
      padding: 1rem;
      margin: 1rem 0;
      border-radius: 8px;
      box-shadow: 0 0 10px #ccc;
    }
    .product-card img {
      max-width: 100%;
      border-radius: 8px;
    }
    .product-card .actions button {
      background-color: #dc3545;
    }
    pre {
      background-color: #222;
      color: #0f0;
      padding: 1rem;
      overflow-x: auto;
    }
    #loginPanel {
      position: fixed;
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;
      background: #0009;
      display: flex;
      justify-content: center;
      align-items: center;
      z-index: 9999;
    }
    #loginPanel div {
      background: white;
      padding: 2rem;
      border-radius: 10px;
      box-shadow: 0 0 20px #000;
    }
    .filters {
      display: flex;
      flex-wrap: wrap;
      gap: 1rem;
      margin-bottom: 2rem;
    }
    .filters input, .filters select {
      flex: 1;
      min-width: 200px;
    }
  </style>
</head>
<body>
  <div id="loginPanel">
    <div>
      <h2>🔒 Admin Login</h2>
      <input type="password" id="password" placeholder="Enter password" />
      <button onclick="checkLogin()">Login</button>
    </div>
  </div>

  <header>
    <h1>Gorakhpur Glass & Arts - Product Dashboard</h1>
  </header>

  <main>
    <label>Product Name</label>
    <input type="text" id="name" />

    <label>Image URL</label>
    <input type="text" id="image" />

    <label>Description</label>
    <textarea id="description"></textarea>

    <label>Category</label>
    <input type="text" id="category" />

    <label>WhatsApp Message</label>
    <input type="text" id="whatsapp" />

    <button onclick="addProduct()">Add Product</button>
    <button onclick="clearAll()">Clear All</button>

    <div class="filters">
      <input type="text" id="search" placeholder="Search products..." oninput="renderProducts()" />
      <select id="categoryFilter" onchange="renderProducts()">
        <option value="">All Categories</option>
      </select>
      <select id="sortBy" onchange="renderProducts()">
        <option value="">Sort By</option>
        <option value="name">Name</option>
        <option value="category">Category</option>
      </select>
    </div>

    <h2>Preview</h2>
    <div id="productList"></div>

    <h2>HTML Code to Copy</h2>
    <pre id="output"></pre>
  </main>

  <script>
    const ADMIN_PASSWORD = "glass123";
    function checkLogin() {
      const input = document.getElementById("password").value;
      if (input === ADMIN_PASSWORD) {
        document.getElementById("loginPanel").style.display = "none";
      } else {
        alert("Incorrect password");
      }
    }

    let products = JSON.parse(localStorage.getItem('products') || '[]');

    function updateCategoryFilter() {
      const categories = [...new Set(products.map(p => p.category))];
      const select = document.getElementById('categoryFilter');
      select.innerHTML = '<option value="">All Categories</option>';
      categories.forEach(c => {
        const opt = document.createElement('option');
        opt.value = c;
        opt.textContent = c;
        select.appendChild(opt);
      });
    }

    function renderProducts() {
      const list = document.getElementById('productList');
      const output = document.getElementById('output');
      const search = document.getElementById('search').value.toLowerCase();
      const category = document.getElementById('categoryFilter').value;
      const sortBy = document.getElementById('sortBy').value;

      let filtered = products.filter(p =>
        p.name.toLowerCase().includes(search) &&
        (category === '' || p.category === category)
      );

      if (sortBy) {
        filtered.sort((a, b) => a[sortBy].localeCompare(b[sortBy]));
      }

      list.innerHTML = '';
      let htmlOutput = '';

      filtered.forEach((p, i) => {
        const card = `
<div class="product-card">
  <img src="${p.image}" alt="${p.name}" />
  <h3>${p.name}</h3>
  <p>${p.description}</p>
  <p><strong>Category:</strong> ${p.category}</p>
  <a href="${p.whatsapp}" target="_blank">Get Quote</a>
  <div class="actions">
    <button onclick="deleteProduct(${i})">Delete</button>
  </div>
</div>`;

        list.innerHTML += card;
        htmlOutput += `
<div class="bg-white rounded-xl shadow p-4">
  <img src="${p.image}" alt="${p.name}" class="rounded-lg mb-4">
  <h4 class="text-xl font-bold">${p.name}</h4>
  <p>${p.description}</p>
  <a href="${p.whatsapp}" target="_blank" class="mt-4 inline-block bg-green-500 text-white py-1 px-4 rounded hover:bg-green-600">Get Quote</a>
</div>`;
      });

      output.textContent = htmlOutput.trim();
    }

    function addProduct() {
      const product = {
        name: document.getElementById('name').value,
        image: document.getElementById('image').value,
        description: document.getElementById('description').value,
        category: document.getElementById('category').value,
        whatsapp: 'https://wa.me/919696957914?text=' + encodeURIComponent(document.getElementById('whatsapp').value)
      };
      products.push(product);
      localStorage.setItem('products', JSON.stringify(products));
      updateCategoryFilter();
      renderProducts();
    }

    function deleteProduct(index) {
      products.splice(index, 1);
      localStorage.setItem('products', JSON.stringify(products));
      updateCategoryFilter();
      renderProducts();
    }

    function clearAll() {
      if (confirm('Are you sure you want to delete all products?')) {
        products = [];
        localStorage.removeItem('products');
        updateCategoryFilter();
        renderProducts();
      }
    }

    updateCategoryFilter();
    renderProducts();
  </script>
</body>
</html>
