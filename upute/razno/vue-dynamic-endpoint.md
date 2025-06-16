# 🔧 Dinamičko postavljanje Backend Endpointa za Vue Frontend

## 📚 Cilj
Omogućiti Vue frontend aplikaciji da čita backend URL iz environment varijable prilikom pokretanja Docker containera.

**Problem**: Hardkodiran backend URL ne radi kad se mijenja IP adresa EC2 instance
**Rješenje**: Runtime konfiguracija preko environment varijable

---

## ⏱️ Trajanje: 15 minuta

---

## 🛠️ Koraci implementacije

### **Korak 1: Dodajte public/config.json**

Kreirajte datoteku `public/config.json`:

```json
{
  "API_ENDPOINT": "http://localhost:5001/api/"
}
```

### **Korak 2: Ažurirajte axios konfiguraciju**

U vašoj axios datoteci (npr. `src/services/api.js`), zamijenite:

```javascript
// PRIJE
import axios from 'axios';

const instance = axios.create({
  baseURL: 'http://localhost:5001/api/',
});

export default instance;
```

```javascript
// NAKON
import axios from 'axios';

let API_ENDPOINT = '/api/';

if (window && window.__CONFIG__ && window.__CONFIG__.API_ENDPOINT) {
  API_ENDPOINT = window.__CONFIG__.API_ENDPOINT;
}

console.log('API_ENDPOINT:', API_ENDPOINT);

const instance = axios.create({
  baseURL: API_ENDPOINT,
});

export default instance;
```

### **Korak 3: Ažurirajte main.js**

Modificirajte `src/main.js` da učita konfiguraciju prije pokretanja:

```javascript
// PRIJE
import { createApp } from 'vue';
import App from './App.vue';
import router from './router';

createApp(App).use(router).mount('#app');
```

```javascript
// NAKON
import { createApp } from 'vue';
import App from './App.vue';
import router from './router';

fetch('/config.json')
  .then(res => res.json())
  .then(config => {
    window.__CONFIG__ = config;
    createApp(App).use(router).mount('#app');
  })
  .catch(() => {
    window.__CONFIG__ = { API_ENDPOINT: '/api/' };
    createApp(App).use(router).mount('#app');
  });
```

### **Korak 4: Dodajte entrypoint.sh**

Kreirajte `entrypoint.sh` datoteku:

```bash
#!/bin/sh
if [ -n "$API_ENDPOINT" ]; then
  sed -i "s|\"API_ENDPOINT\": \".*\"|\"API_ENDPOINT\": \"$API_ENDPOINT\"|g" /usr/share/nginx/html/config.json
fi
exec nginx -g 'daemon off;'
```

Učinite je izvršnom:
```bash
chmod +x entrypoint.sh
```

### **Korak 5: Ažurirajte Dockerfile**

Dodajte ove linije u vaš Dockerfile:

```dockerfile
# ...postojeći sadržaj...

FROM nginx:alpine

COPY --from=builder /app/dist /usr/share/nginx/html
COPY public/config.json /usr/share/nginx/html/config.json
COPY entrypoint.sh /entrypoint.sh

RUN chmod +x /entrypoint.sh

EXPOSE 80

ENTRYPOINT ["/entrypoint.sh"]
```

### **Korak 6: Build i pokretanje**

```bash
# Build image
docker build -t my-frontend:dynamic .

# Pokretanje s custom backend URL-om
docker run -e API_ENDPOINT="http://192.168.1.100:5001/api/" -p 8080:80 my-frontend:dynamic
```

---

## ☁️ Na AWS EC2

```bash
# Dohvatite IP adresu instance
INSTANCE_IP=$(curl -s http://169.254.169.254/latest/meta-data/local-ipv4)

# Pokrenite frontend s backend URL-om
docker run -d \
    --name vue-frontend \
    -p 80:80 \
    -e API_ENDPOINT="http://$INSTANCE_IP:5001/api/" \
    my-frontend:dynamic
```

---

## ✅ Verifikacija

1. Otvorite aplikaciju u browseru
2. Otvorite Developer Tools → Console
3. Trebali biste vidjeti: `API_ENDPOINT: http://YOUR_IP:5001/api/`
4. API pozivi idu na pravilan endpoint

---

## 🎯 Rezultat

✅ Isti Docker image radi s različitim backend URL-ovima  
✅ Nema više hardkodiranih IP adresa  
✅ Aplikacija se automatski prilagođava novim EC2 IP adresama
