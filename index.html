<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Docomo Wallet Explorer</title>
  <style>
    body { font-family: Arial, sans-serif; padding: 2em; }
    .tab-menu { display: flex; gap: 10px; margin-bottom: 1em; flex-wrap: wrap; }
    .tab-menu button {
      padding: 8px 16px;
      font-size: 0.9em;
      cursor: pointer;
      border: 1px solid #ccc;
      background: #f4f4f4;
    }
    .tab-menu button.active {
      background: #d0eaff;
      font-weight: bold;
    }
    .tab-content { display: none; }
    .tab-content.active { display: block; }
    input.searchInput {
      margin-bottom: 1em; width: 100%; padding: 0.5em; font-size: 1em;
    }
    ul.numberList li {
      padding: 6px 0;
      display: flex;
      justify-content: space-between;
      align-items: center;
    }
    .coins { color: green; font-weight: bold; margin-right: 10px; }
    .status { margin-left: 10px; font-style: italic; }
    .wallet-actions button {
      margin-left: 6px;
      padding: 2px 6px;
      font-size: 0.8em;
      cursor: pointer;
    }
    .pagination button {
      margin: 2px; padding: 5px 10px;
      font-size: 0.9em; cursor: pointer;
    }
  </style>
</head>
<body>
  <main>
    <h1>📱 Docomo Wallet Explorer</h1>
    <div class="tab-menu" id="tabButtons"></div>
    <div id="tabContainer"></div>
  </main>

  <script>
    const prefixes = [787, 777, 788, 789, 727, 828, 898];
    const total = 9999;
    const perPage = 200;
    const tabButtonsDiv = document.getElementById('tabButtons');
    const tabContainer = document.getElementById('tabContainer');
    let walletDatas = {};

    function createTab(prefix) {
      const btn = document.createElement('button');
      btn.textContent = prefix;
      btn.dataset.prefix = prefix;
      tabButtonsDiv.appendChild(btn);

      const wrapper = document.createElement('div');
      wrapper.className = 'tab-content';
      wrapper.id = `tab-${prefix}`;
      wrapper.innerHTML = `
        <input type="text" class="searchInput" id="search-${prefix}" placeholder="Search ${prefix} numbers..." />
        <ul class="numberList" id="list-${prefix}"></ul>
        <div class="pagination" id="pagination-${prefix}"></div>
      `;
      tabContainer.appendChild(wrapper);
    }

    function showTab(activePrefix) {
      document.querySelectorAll('.tab-content').forEach(el => el.classList.remove('active'));
      document.querySelectorAll('.tab-menu button').forEach(btn => btn.classList.remove('active'));
      document.getElementById(`tab-${activePrefix}`).classList.add('active');
      [...tabButtonsDiv.children].find(b => b.textContent == activePrefix).classList.add('active');
      renderPage(activePrefix);
    }

    function updateCoins(prefix, phone, delta) {
      const entry = walletDatas[prefix].find(item => item.phone === phone);
      if (!entry) return;
      entry.coins = Math.max(0, entry.coins + delta);
      localStorage.setItem(`walletData_${prefix}`, JSON.stringify(walletDatas[prefix]));
      renderPage(prefix);
    }

    function renderPage(prefix, page = 1) {
      const filterVal = document.getElementById(`search-${prefix}`).value || '';
      const filtered = walletDatas[prefix].filter(item => item.phone.includes(filterVal));
      const list = document.getElementById(`list-${prefix}`);
      list.innerHTML = '';

      const start = (page - 1) * perPage;
      const sliced = filtered.slice(start, start + perPage);

      sliced.forEach(item => {
        const li = document.createElement('li');
        const phone = item.phone;
        li.innerHTML = `
          <span>${phone}</span>
          <span>
            <span class="coins">(${item.coins} coins)</span>
            <span class="status">${item.status}</span>
            <span class="wallet-actions">
              <button data-action="send" data-phone="${phone}" data-prefix="${prefix}">Send 10</button>
              <button data-action="receive" data-phone="${phone}" data-prefix="${prefix}">Receive 10</button>
            </span>
          </span>
        `;
        list.appendChild(li);
      });

      renderPagination(prefix, filtered.length, page);
    }

    function renderPagination(prefix, length, activePage) {
      const container = document.getElementById(`pagination-${prefix}`);
      container.innerHTML = '';
      const pages = Math.ceil(length / perPage);
      for (let i = 1; i <= pages; i++) {
        const btn = document.createElement('button');
        btn.textContent = i;
        if (i === activePage) btn.style.fontWeight = 'bold';
        btn.addEventListener('click', () => renderPage(prefix, i));
        container.appendChild(btn);
      }
    }

    function init() {
      prefixes.forEach(prefix => {
        createTab(prefix);
        const base = Number('671' + prefix + '0000');
        const key = `walletData_${prefix}`;
        const saved = localStorage.getItem(key);

        walletDatas[prefix] = saved ? JSON.parse(saved) : Array.from({ length: total }, (_, i) => ({
          phone: (base + i).toString(),
          coins: 100,
          status: 'active'
        }));

        if (!saved) localStorage.setItem(key, JSON.stringify(walletDatas[prefix]));

        document.getElementById(`search-${prefix}`).addEventListener('input', () => renderPage(prefix, 1));
      });

      tabButtonsDiv.addEventListener('click', e => {
        if (e.target.tagName === 'BUTTON') {
          showTab(e.target.dataset.prefix);
        }
      });

      tabContainer.addEventListener('click', e => {
        if (e.target.tagName === 'BUTTON' && e.target.dataset.action) {
          const { action, phone, prefix } = e.target.dataset;
          updateCoins(prefix, phone, action === 'send' ? -10 : 10);
        }
      });

      showTab(prefixes[0]);
    }

    window.addEventListener('DOMContentLoaded', init);
  </script>
</body>
</html>

