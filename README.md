<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Docomo Wallet Explorer</title>
  <style>
    body { font-family: Arial, sans-serif; padding: 2em; }
    .tab-menu { display: flex; gap: 8px; margin-bottom: 1em; flex-wrap: wrap; }
    .tab-menu button {
      padding: 6px 12px;
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
      box-sizing: border-box;
    }
    ul.numberList { list-style: none; padding: 0; }
    ul.numberList li {
      padding: 6px 0;
      border-bottom: 1px solid #eee;
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
    const prefixes = [
      300, 333, 339, 344, 355, 362, 366,
      400, 456, 471, 472, 473, 474, 475, 477, 478, 479, 480, 482, 483, 486, 487, 488, 489,
      562, 563, 564, 565, 588,
      632, 633, 634, 635, 637, 638, 642, 644, 645, 646, 647, 648, 649, 653, 654, 682, 683, 685, 686, 687, 688, 689,
      727, 734, 735, 777, 787, 788, 789, 797,
      828, 864, 868, 888, 898,
      922, 929, 969, 971, 972, 977, 979, 987, 988, 989, 997, 998
    ];
    const total = 10000;
    const perPage = 200;
    const tabButtonsDiv = document.getElementById('tabButtons');
    const tabContainer = document.getElementById('tabContainer');
    let walletDatas = {};

    function ensurePrefixData(prefix) {
      if (walletDatas[prefix]) return;
      const base = Number('671' + prefix + '0000');
      const key = `walletData_${prefix}`;
      const saved = localStorage.getItem(key);

      walletDatas[prefix] = saved ? JSON.parse(saved) : Array.from({ length: total }, (_, i) => ({
        phone: (base + i).toString(),
        coins: 100,
        status: 'active'
      }));

      if (!saved) {
        try {
          localStorage.setItem(key, JSON.stringify(walletDatas[prefix]));
        } catch (e) {
          console.warn(`LocalStorage full; ${prefix} running in memory only.`);
        }
      }
    }

    function createTab(prefix) {
      const btn = document.createElement('button');
      btn.textContent = prefix;
      btn.dataset.prefix = prefix;
      tabButtonsDiv.appendChild(btn);

      const wrapper = document.createElement('div');
      wrapper.className = 'tab-content';
      wrapper.id = `tab-${prefix}`;
      wrapper.innerHTML = `
        <input type="text" class="searchInput" id="search-${prefix}" placeholder="Search 671-${prefix} numbers..." />
        <ul class="numberList" id="list-${prefix}"></ul>
        <div class="pagination" id="pagination-${prefix}"></div>
      `;
      tabContainer.appendChild(wrapper);

      document.getElementById(`search-${prefix}`).addEventListener('input', () => renderPage(prefix, 1));
    }

    function showTab(activePrefix) {
      ensurePrefixData(activePrefix);
      document.querySelectorAll('.tab-content').forEach(el => el.classList.remove('active'));
      document.querySelectorAll('.tab-menu button').forEach(btn => btn.classList.remove('active'));
      document.getElementById(`tab-${activePrefix}`).classList.add('active');
      [...tabButtonsDiv.children].find(b => b.dataset.prefix == activePrefix).classList.add('active');
      renderPage(activePrefix);
    }

    function updateCoins(prefix, phone, delta) {
      const entry = walletDatas[prefix].find(item => item.phone === phone);
      if (!entry) return;
      entry.coins = Math.max(0, entry.coins + delta);
      try {
        localStorage.setItem(`walletData_${prefix}`, JSON.stringify(walletDatas[prefix]));
      } catch (e) {}
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
      prefixes.forEach(prefix => createTab(prefix));

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
