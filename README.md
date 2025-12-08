<html lang="it">
<head>
  <meta charset="UTF-8">
  <title>Calendario Avvento Musicale</title>
  <style>
    body {font-family: Arial, sans-serif; margin: 20px;}
    .grid {display: grid; grid-template-columns: repeat(6, 1fr); gap: 10px;}
    .cell {border: 1px solid #ddd; padding: 8px; text-align:center;}
    img {max-width:100%; height:auto;}
  </style>
</head>
<body>
<h1>Calendario Avvento Musicale – Famiglia</h1>
<div class="grid" id="calendar"></div>

<script>
// URL del CSV pubblicato
const csvUrl = 'https://docs.google.com/spreadsheets/d/e/2PACX-1vRbB8anD_KDj7SJ_2zoUdRQsVSEl5HSoOe20m4PGYB73BqnJ9Kx5HS2dfokbOMeQiIQJJPzRvo1N3xv/pub?gid=0&single=true&output=csv';

// Funzione per convertire CSV → array di oggetti
function parseCSV(text){
  const lines = text.trim().split('\n');
  const headers = lines[0].split(',');
  return lines.slice(1).map(l => {
    const cols = l.split(',');
    const obj = {};
    headers.forEach((h,i)=> obj[h.trim()] = cols[i]?.trim());
    return obj;
  });
}

// Genera QR code usando l'API di goqr.me (gratuita, no key)
function qrImg(url){
  const enc = encodeURIComponent(url);
  return `https://api.qrserver.com/v1/create-qr-code/?size=150x150&data=${enc}`;
}

// Render della griglia
async function render(){
  const resp = await fetch(csvUrl);
  const txt = await resp.text();
  const data = parseCSV(txt);

  const container = document.getElementById('calendar');
  // Ordina per giorno (assumendo colonna "Giorno")
  data.sort((a,b)=> Number(a.Giorno)-Number(b.Giorno));

  data.forEach(row=>{
    const cell = document.createElement('div');
    cell.className='cell';
    cell.innerHTML = `
      <strong>Giorno ${row.Giorno}</strong><br>
      ${row.Titolo} – ${row.Artista}<br>
      <img src="${qrImg(row.URL)}" alt="QR ${row.Giorno}">
    `;
    container.appendChild(cell);
  });
}
render();
</script>
</body>
</html>
