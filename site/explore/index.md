---
title: Explore
---

# Explore

We generate an SQLite database as part of the indexing process. While the schema of this database is not stable and it should not be considered API, it provides a useful way to gain insight into the Psion software library.

<textarea id="q">select name from sqlite_master;</textarea>
<button id="run">Run</button>
<div id="out"></div>
<script src="https://cdnjs.cloudflare.com/ajax/libs/sql.js/1.10.3/sql-wasm.js"></script>
<script>
(async () => {
  const SQL = await initSqlJs({ locateFile: f => `https://cdnjs.cloudflare.com/ajax/libs/sql.js/1.10.3/${f}` });
  const db  = new SQL.Database(new Uint8Array(await (await fetch('/index.db')).arrayBuffer()));
  const row = (cells, tag) => {
    const tr = document.createElement('tr');
    for (const c of cells) tr.appendChild(document.createElement(tag)).textContent = c ?? 'NULL';
    return tr;
  };
  run.onclick = () => {
    try {
      out.replaceChildren(...db.exec(q.value).map(({ columns, values }) => {
        const t = document.createElement('table');
        t.append(row(columns, 'th'), ...values.map(v => row(v, 'td')));
        return t;
      }));
    } catch (e) { out.textContent = e.message; }
  };
})();
</script>

> [!WARNING]
> The API is currently a work in progress and should not be considered stable.
