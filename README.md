/loja/
├── index.html
├── style.css
├── app.js
├── manifest.json
├── service-worker.js
└── /images/
    ├── prod1.jpg
    └── prod2.jpg

<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Loja Marina Makeup</title>
  <link rel="stylesheet" href="style.css" />
  <link rel="manifest" href="manifest.json" />
  <meta name="theme-color" content="#ffffff" />
</head>
<body>
  <header>
    <h1>Marina Makeup</h1>
  </header>
  <main id="produtos"></main>
  <footer>
    <p>&copy; 2022 Marina Makeup</p>
  </footer>
  <script src="app.js"></script>
</body>
</html>

body {
  font-family: Arial, sans-serif;
  margin: 0;
  padding: 0;
  background-color: #f9f9f9;
}
header, footer {
  background-color: #333;
  color: #fff;
  text-align: center;
  padding: 1em 0;
}
main {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  padding: 1em;
}
.produto {
  background-color: #fff;
  border: 1px solid #ddd;
  border-radius: 5px;
  margin: 1em;
  padding: 1em;
  width: 250px;
  box-shadow: 0 2px 5px rgba(0,0,0,0.1);
}
.produto img {
  max-width: 100%;
  height: auto;
}
.produto h2 {
  font-size: 1.2em;
  margin: 0.5em 0;
}
.produto p {
  margin: 0.5em 0;
}
.produto a {
  display: inline-block;
  margin-top: 0.5em;
  padding: 0.5em;
  background-color: #25d366;
  color: #fff;
  text-decoration: none;
  border-radius: 5px;
}
.produto a:hover {
  background-color: #128c7e;
}


const produtos = [
  {
    id: 1,
    nome: "Produto 1",
    preco: 49.90,
    descricao: "Descrição do Produto 1",
    imagem: "images/prod1.jpg"
  },
  {
    id: 2,
    nome: "Produto 2",
    preco: 79.90,
    descricao: "Descrição do Produto 2",
    imagem: "images/prod2.jpg"
  }
];

const container = document.getElementById('produtos');

produtos.forEach(p => {
  const div = document.createElement('div');
  div.className = 'produto';
  div.innerHTML = `
    <img src="${p.imagem}" alt="${p.nome}" />
    <h2>${p.nome}</h2>
    <p>${p.descricao}</p>
    <p>R$ ${p.preco.toFixed(2)}</p>
    <a href="https://wa.me/11918351103?text=Olá,%20gostaria%20de%20comprar%20o%20${encodeURIComponent(p.nome)}" target="_blank">
      Comprar via WhatsApp
    </a>
  `;
  container.appendChild(div);
});

// Registrar o service worker
if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('service-worker.js')
    .then(() => console.log('Service Worker registrado com sucesso.'))
    .catch(error => console.log('Erro ao registrar o Service Worker:', error));
}


{
  "name": "Loja Marina Makeup",
  "short_name": "Marina Makeup",
  "start_url": "./index.html",
  "display": "standalone",
  "background_color": "#ffffff",
  "theme_color": "#333333",
  "icons": [
    {
      "src": "images/icon-192.png",
      "sizes": "192x192",
      "type": "image/png"
    },
    {
      "src": "images/icon-512.png",
      "sizes": "512x512",
      "type": "image/png"
    }
  ]
}


const CACHE_NAME = 'loja-cache-v1';
const urlsToCache = [
  './',
  './index.html',
  './style.css',
  './app.js',
  './images/prod1.jpg',
  './images/prod2.jpg',
  './images/icon-192.png',
  './images/icon-512.png'
];

self.addEventListener('install', event => {
  event.waitUntil(
    caches.open(CACHE_NAME)
      .then(cache => {
        return cache.addAll(urlsToCache);
      })
  );
});

self.addEventListener('fetch', event => {
  event.respondWith(
    caches.match(event.request)
      .then(response => {
        return response || fetch(event.request);
      })
  );
});