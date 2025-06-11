# 🌐 AWS Academy Vodič: Deployment statičke web stranice na Amazon S3

## 📚 Ciljevi učenja
Do kraja ovog vodiča moći ćete:
- ✅ Razumjeti koncepte statičkog web hostinga na S3
- ✅ Kreirati i konfigurirati S3 bucket za web hosting
- ✅ Postaviti bucket policy-je za javni pristup
- ✅ Uploadati HTML/CSS datoteke na S3
- ✅ Omogućiti pristup vašoj web stranici s interneta
- ✅ Upravljati troškovima i održavati budžet

---

## ⏱️ **Trajanje**: 25 minuta
## 💰 **Utjecaj na budžet**: Minimalan (S3 storage ~$0.023/GB/mjesec)
## 🌍 **Region**: us-east-1 (N. Virginia) ili us-west-2 (Oregon) - **Obavezno za AWS Academy**

---

## 🔑 Preduvjeti
- ✅ AWS Academy Learner Lab sesija pokrenuta
- ✅ Pristup AWS Console-u preko "AWS" gumba
- ✅ Osnovne HTML i CSS datoteke pripravljene
- ✅ Razumijevanje osnovnih web development koncepata
- ✅ Text editor za kreiranje datoteka (VS Code, Notepad++, itd.)

**📝 Napomena**: S3 statički hosting je idealan za portfolios, dokumentaciju, jednostavne web aplikacije i landing stranice koje ne zahtijevaju server-side logiku.

**⚠️ Važne AWS Academy napomene:**
- **Session Timer**: Vaša sesija ima vremensko ograničenje, ali S3 resursi će biti sačuvani između sesija
- **Budget Monitoring**: Redovito provjeravajte budžet u lab sučelju (podatci se ažuriraju svakih 8-12 sati)
- **LabRole pristup**: S3 servis može koristiti LabRole IAM ulogu za dodatne integracije

---

## 📋 Brzi pregled koraka

1. **Pripremite web datoteke** za vaš statički sajt
2. **Kreirajte S3 bucket** s jedinstvenim imenom
3. **Konfigurirajte statički web hosting** na bucket-u
4. **Postavite bucket policy** za javni pristup
5. **Uploadajte datoteke** i testirajte web stranicu
6. **Optimizirajte performanse** i upravljajte troškovima

---

## 🎨 Dio 1: Priprema web datoteka

### **Korak 1.1: Struktura projekta**

Prije uploadanja na S3, organizirajte vaše datoteke:

```
moj-web-projekt/
├── index.html          ← Glavna stranica (obavezno)
├── error.html          ← 404 stranica (preporučeno)
├── css/
│   └── styles.css      ← CSS stilovi
├── js/
│   └── script.js       ← JavaScript datoteke
├── images/
│   ├── logo.png        ← Slike i grafike
│   └── background.jpg
└── assets/
    └── fonts/          ← Fontovi i ostali resursi
```

### **Korak 1.2: Kreiranje osnovnih datoteka**

**Kreirajte `index.html`:**
```html
<!DOCTYPE html>
<html lang="hr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Moja Statička Web Stranica</title>
    <link rel="stylesheet" href="css/styles.css">
</head>
<body>
    <header>
        <h1>Dobrodošli na moju web stranicu</h1>
        <nav>
            <ul>
                <li><a href="#home">Početna</a></li>
                <li><a href="#about">O meni</a></li>
                <li><a href="#contact">Kontakt</a></li>
            </ul>
        </nav>
    </header>
    
    <main>
        <section id="home">
            <h2>Hosting na Amazon S3</h2>
            <p>Ova stranica je hostana na Amazon S3 servisu!</p>
        </section>
        
        <section id="about">
            <h2>O projektu</h2>
            <p>Ovo je demonstracija statičkog web hostinga koristeći AWS S3.</p>
        </section>
    </main>
    
    <footer>
        <p>&copy; 2024 Moja Web Stranica. Sva prava pridržana.</p>
    </footer>
    
    <script src="js/script.js"></script>
</body>
</html>
```

**Kreirajte `css/styles.css`:**
```css
/* Reset i osnovni stilovi */
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Arial', sans-serif;
    line-height: 1.6;
    color: #333;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
}

header {
    background: rgba(255, 255, 255, 0.95);
    padding: 1rem;
    box-shadow: 0 2px 10px rgba(0,0,0,0.1);
}

h1 {
    color: #2c3e50;
    text-align: center;
    margin-bottom: 1rem;
}

nav ul {
    list-style: none;
    display: flex;
    justify-content: center;
    gap: 2rem;
}

nav a {
    color: #3498db;
    text-decoration: none;
    font-weight: bold;
    transition: color 0.3s ease;
}

nav a:hover {
    color: #2980b9;
}

main {
    max-width: 800px;
    margin: 2rem auto;
    padding: 2rem;
    background: rgba(255, 255, 255, 0.9);
    border-radius: 10px;
    box-shadow: 0 5px 20px rgba(0,0,0,0.1);
}

section {
    margin-bottom: 2rem;
}

h2 {
    color: #2c3e50;
    margin-bottom: 1rem;
    border-bottom: 2px solid #3498db;
    padding-bottom: 0.5rem;
}

footer {
    text-align: center;
    padding: 2rem;
    background: rgba(0, 0, 0, 0.8);
    color: white;
    margin-top: 2rem;
}

/* Responsive design */
@media (max-width: 768px) {
    nav ul {
        flex-direction: column;
        gap: 1rem;
    }
    
    main {
        margin: 1rem;
        padding: 1rem;
    }
}
```

**Kreirajte `error.html`:**
```html
<!DOCTYPE html>
<html lang="hr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>404 - Stranica nije pronađena</title>
    <link rel="stylesheet" href="css/styles.css">
    <style>
        .error-container {
            text-align: center;
            padding: 4rem 2rem;
        }
        .error-code {
            font-size: 6rem;
            color: #e74c3c;
            font-weight: bold;
        }
        .back-link {
            display: inline-block;
            margin-top: 2rem;
            padding: 1rem 2rem;
            background: #3498db;
            color: white;
            text-decoration: none;
            border-radius: 5px;
            transition: background 0.3s ease;
        }
        .back-link:hover {
            background: #2980b9;
        }
    </style>
</head>
<body>
    <main>
        <div class="error-container">
            <div class="error-code">404</div>
            <h1>Stranica nije pronađena</h1>
            <p>Žao nam je, ali stranica koju tražite ne postoji.</p>
            <a href="/" class="back-link">Povratak na početnu</a>
        </div>
    </main>
</body>
</html>
```

**Opcionalno - Kreirajte `js/script.js`:**
```javascript
// Jednostavan JavaScript za interaktivnost
document.addEventListener('DOMContentLoaded', function() {
    // Smooth scrolling za navigacijske linkove
    const links = document.querySelectorAll('nav a[href^="#"]');
    
    links.forEach(link => {
        link.addEventListener('click', function(e) {
            e.preventDefault();
            const targetId = this.getAttribute('href').substring(1);
            const targetElement = document.getElementById(targetId);
            
            if (targetElement) {
                targetElement.scrollIntoView({
                    behavior: 'smooth'
                });
            }
        });
    });
    
    // Dodaj animaciju na učitavanje
    const main = document.querySelector('main');
    main.style.opacity = '0';
    main.style.transform = 'translateY(20px)';
    
    setTimeout(() => {
        main.style.transition = 'all 0.6s ease';
        main.style.opacity = '1';
        main.style.transform = 'translateY(0)';
    }, 100);
});
```

---

## 🪣 Dio 2: Kreiranje S3 Bucket-a

### **Korak 2.1: Pokretanje Learner Lab sesije**

1. **Pokretanje sesije:**
   - **Kliknite** "Start Lab" gumb u vašem Learner Lab sučelju
   - **Pričekajte** da se lab status promijeni u "Lab status: ready" 
   - **Napomena**: Session timer počinje brojati - vaša sesija ima vremensko ograničenje

2. **Pristup AWS Console-u:**
   - **Kliknite** "AWS" gumb u Learner Lab sučelju
   - **Ako se nova kartica ne otvori**: Provjerite browser pop-up blocker
     - Kliknite na pop-up ikonu u address bar-u
     - Odaberite "Allow pop-ups" za ovaj sajt

3. **Verifikacija pristupa:**
   - Trebali biste vidjeti AWS Management Console
   - **Provjerite region** u gornjem desnom kutu

### **Korak 2.2: Navigacija na S3 servis**

1. **Pretražite** "S3" u AWS Console search baru (vrh stranice)
2. **Kliknite** "Amazon S3" iz rezultata pretrage
3. **Dvostruko provjerite region**: Osigurajte da ste u **us-east-1** ili **us-west-2**
   - **VAŽNO**: Svi servisi su ograničeni na ove dvije regije u Learner Lab-u

### **Korak 2.2: Kreiranje novog bucket-a**

1. **Kliknite** narančasti "Create bucket" gumb

2. **Općenite konfiguracije:**
   ```
   Bucket name: moja-web-stranica-2024-vaseime
   (Ime mora biti globalno jedinstveno!)
   
   Primjeri dobrih imena:
   - student-portfolio-2024-marko
   - ecommerce-demo-2024-ana  
   - company-website-2024-petar
   ```

3. **Region postavke:**
   - **AWS Region**: US East (N. Virginia) us-east-1 ✅
   - **Ili**: US West (Oregon) us-west-2 ✅

4. **Object Ownership:**
   - Zadržite **ACLs disabled** (preporučeno)

5. **Block Public Access settings:**
   - ⚠️ **VAŽNO**: **Uklonite kvačicu** s "Block all public access"
   - ✅ **Potvrdite** da razumijete rizike
   - **Razlog**: Potreban javni pristup za web hosting

6. **Bucket Versioning:**
   - **Disable** (zadržite default)

7. **Default encryption:**
   - **Amazon S3 managed keys (SSE-S3)** (zadržite default)

8. **Kliknite** "Create bucket"

### **Korak 2.3: Verifikacija kreiranog bucket-a**

**✅ Uspjeh**: Trebali biste vidjeti vaš novi bucket u S3 konzoli s porukom "Bucket and folder creation succeeded".

---

## ⚙️ Dio 3: Konfiguracija statičkog web hostinga

### **Korak 3.1: Pristup Properties tab-u**

1. **Kliknite** na ime vašeg bucket-a
2. **Navigirajte** na **Properties** tab
3. **Skrolajte dolje** do sekcije "Static website hosting"

### **Korak 3.2: Omogućavanje statičkog hostinga**

1. **Kliknite** "Edit" u Static website hosting sekciji

2. **Konfiguracija hostinga:**
   ```
   Static website hosting: Enable ✅
   Hosting type: Host a static website ✅
   Index document: index.html
   Error document: error.html (opcionalno)
   ```

3. **Kliknite** "Save changes"

### **Korak 3.3: Zapišite Bucket website endpoint**

**Nakon spremanja**, vidjet ćete **Bucket website endpoint**:
```
http://moja-web-stranica-2024-vaseime.s3-website-us-east-1.amazonaws.com
```

**🚨 Važno**: Spremite ovaj URL! Koristit ćete ga za pristup vašoj web stranici.

---

## 🔐 Dio 4: Postavljanje Bucket Policy-ja

### **Korak 4.1: Navigacija na Permissions tab**

1. **Idite** na **Permissions** tab vašeg bucket-a
2. **Skrolajte** do sekcije "Bucket policy"
3. **Kliknite** "Edit"

### **Korak 4.2: Dodavanje public read policy-ja**

**Zamijenite** `VAŠ-BUCKET-NAME` s vašim stvarnim imenom bucket-a:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::VAŠ-BUCKET-NAME/*"
        }
    ]
}
```

**Primjer s pravim imenom bucket-a:**
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::moja-web-stranica-2024-marko/*"
        }
    ]
}
```

### **Korak 4.3: Spremanje policy-ja**

1. **Paste** policy JSON u text area
2. **Kliknite** "Save changes"
3. **Očekujte** warning poruku o javnom pristupu (to je normalno!)

**✅ Verifikacija**: Trebali biste vidjeti "Publicly accessible" label na vašem bucket-u.

---

## 📤 Dio 5: Upload web datoteka

### **Korak 5.1: Navigacija na Objects tab**

1. **Kliknite** **Objects** tab u vašem bucket-u
2. **Priprema**: Otvorite folder s vašim web datotekama na računalu

### **Korak 5.2: Upload putem konzole (Preporučeno za početnike)**

**Opcija A: Pojedinačni upload datoteka**
```
1. Kliknite "Upload"
2. Kliknite "Add files"
3. Odaberite: index.html, error.html
4. Kliknite "Upload"
5. Ponovite za CSS/JS/slike datoteke
```

**Opcija B: Upload čitavog foldera**
```
1. Kliknite "Upload"  
2. Kliknite "Add folder"
3. Odaberite vaš web projekt folder
4. Kliknite "Upload"
```

### **Korak 5.3: Upload putem AWS CLI (Napredni korisnici)**

**Ako želite koristiti AWS CLI, prvo trebate konfigurirati credentials:**

**Dohvaćanje AWS Academy credentials:**
1. **U Learner Lab sučelju**, kliknite "AWS Details" gumb
2. **Kopirajte credentials** koji će biti prikazani:
   ```
   [default]
   aws_access_key_id=ASIAIOSFODNN7EXAMPLE
   aws_secret_access_key=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
   aws_session_token=AQoEXAMPLEH4aoAH0gNCAPy...
   ```

**Brza CLI konfiguracija:**
```bash
# Postavite credentials (zamijenite s vašim stvarnim vrijednostima)
aws configure set aws_access_key_id VAŠ_ACCESS_KEY_ID
aws configure set aws_secret_access_key VAŠ_SECRET_ACCESS_KEY  
aws configure set aws_session_token VAŠ_SESSION_TOKEN
aws configure set region us-east-1

# Testirajte konfiguraciju
aws sts get-caller-identity
```

**Upload datoteka putem CLI:**
```bash
# Navigirajte u direktorij vašeg projekta
cd /path/to/your/web-project

# Sync sve datoteke u bucket (zamijenite VAŠ-BUCKET-NAME)
aws s3 sync . s3://VAŠ-BUCKET-NAME --region us-east-1

# Ili kopirajte pojedinačne datoteke
aws s3 cp index.html s3://VAŠ-BUCKET-NAME/ --region us-east-1
aws s3 cp css/styles.css s3://VAŠ-BUCKET-NAME/css/ --region us-east-1
```

**📝 Napomena o session credentials**: AWS Academy credentials se mijenjaju s svakom novom sesijom, pa ćete morati ponovno konfigurirati CLI ako restartate lab sesiju.

### **Korak 5.4: Verifikacija upload-a**

**✅ Provjera**: U Objects tab-u trebali biste vidjeti:
- `index.html` ✅
- `error.html` ✅  
- `css/styles.css` ✅
- Ostale datoteke ✅

**Status**: Svi upload-ovi trebaju pokazivati "Upload succeeded".

---

## 🧪 Dio 6: Testiranje web stranice

### **Korak 6.1: Pristup vašoj web stranici**

1. **Kopirajte** bucket website endpoint URL iz Properties tab-a
2. **Otvorite** URL u novom browser tab-u
   ```
   http://vaš-bucket-name.s3-website-us-east-1.amazonaws.com
   ```

### **Korak 6.2: Funkcionalnost testiranja**

**Testirajte sljedeće:**
- ✅ **Glavna stranica**: Učitava li se `index.html`?
- ✅ **CSS stilovi**: Primjenjuju li se stilovi?
- ✅ **Navigacija**: Rade li linkovi?
- ✅ **Slike**: Učitavaju li se images?
- ✅ **JavaScript**: Rade li interaktivni elementi?
- ✅ **404 stranica**: `your-url/nonexistent.html` → prikazuje error.html?

### **Korak 6.3: Testiranje s različitih uređaja**

```
Desktop:   ✅ Chrome, Firefox, Safari, Edge
Mobile:    ✅ Responsive design test
Tablet:    ✅ Mid-size screen test
```

---

## 🔄 Dio 7: Ažuriranje web stranice

### **Korak 7.1: Lokalne promjene**

**Kada želite ažurirati sadržaj:**

1. **Uredite** datoteke lokalno
2. **Testirajte** promjene lokalno (otvorite index.html u browseru)
3. **Spremite** sve promjene

### **Korak 7.2: Re-upload novih verzija**

**Putem konzole:**
```
1. Idite na Objects tab
2. Odaberite datoteku za ažuriranje  
3. Kliknite "Upload"
4. Odaberite novu verziju datoteke
5. Kliknite "Upload" (prepisuje staru verziju)
```

**Putem CLI:**
```bash
# Sync sve promjene odjednom
aws s3 sync . s3://VAŠ-BUCKET-NAME --region us-east-1 --delete

# Ili upload pojedinačne datoteke
aws s3 cp index.html s3://VAŠ-BUCKET-NAME/ --region us-east-1
```

### **Korak 7.3: Cache brisanje**

**Ako ne vidite promjene odmah:**
```
Browser cache: Ctrl+F5 (Windows) ili Cmd+Shift+R (Mac)
Ili: Otvorite u incognito/private browsing mode
```

---

## 🚀 Dio 8: Optimizacija performansi

### **Korak 8.1: Optimizacija datoteka**

**HTML optimizacija:**
```html
<!-- Minimizirajte CSS/JS datoteke -->
<link rel="stylesheet" href="css/styles.min.css">

<!-- Optimizirajte slike -->
<img src="images/logo.webp" alt="Logo" loading="lazy">

<!-- Koristite CDN za libraries -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.6.0/jquery.min.js"></script>
```

**CSS optimizacija:**
```css
/* Koristite efikasne selektore */
.button { /* bolje od div.container .button */ }

/* Minimizirajte CSS */
/* Uklonite nekorištene stilove */
```

### **Korak 8.2: Najbolje prakse za statičke stranice**

| **Područje** | **Preporučene optimizacije** |
|--------------|----------------------------|
| **Slike** | WebP format, lazy loading, responsive images |
| **CSS** | Minification, kritični CSS inline |
| **JavaScript** | Minification, async/defer loading |
| **HTML** | Semantic markup, meta tags |
| **Pristupačnost** | Alt text, ARIA labels, proper contrast |

---

## 💰 Dio 9: Upravljanje troškovima i AWS Academy budžetom

### **AWS Academy specifičnosti budžeta:**

**⚠️ KRITIČNO UPOZORENJE**: 
- Ako premašite vaš lab budžet, **AWS account će biti deaktiviran**
- **Svi resursi i napredak će biti trajno izgubljeni**
- Budžet informacije se ažuriraju **svakih 8-12 sati**, pa možda nećete vidjeti najnovije troškove odmah

**📊 Monitoring budžeta:**
1. **U Learner Lab sučelju** (vrh stranice) vidjet ćete trenutni budžet status
2. **Redovito provjeravajte** ovaj prikaz tijekom rada
3. **Napomena**: Podaci nisu u realnom vremenu!

### **S3 pricing faktori:**

| **Komponenta** | **Cijena** | **Objašnjenje** |
|----------------|------------|-----------------|
| **Storage** | ~$0.023/GB/mjesec | Veličina vaših datoteka |
| **Requests** | ~$0.0004/1000 GET zahtjeva | Broj posjeta stranici |
| **Data Transfer** | Besplatno do 100GB/mjesec | Download promet |

### **Korak 9.1: Monitoring troškova**

```bash
# Provjerite veličinu bucket-a putem CLI
aws s3 ls s3://VAŠ-BUCKET-NAME --recursive --human-readable --summarize

# Očekivani izlaz:
# Total Size: 2.1 MiB
# Total Objects: 15
```

### **Korak 9.2: Session management i perzistentnost**

**Važne činjenice o Learner Lab sesijama:**
- ✅ **S3 bucket-i i datoteke OSTAJU** između sesija
- ✅ **Vaša web stranica OSTAJE dostupna** čak i kada je sesija završena
- ⚠️ **Session timer**: Pratite vremenska ograničenja sesije
- 🔄 **Restart sesije**: Kliknite "Start Lab" za produžavanje prije nego što timer dođe do 0:00

### **Korak 9.3: Optimizacija za budžet**

**Savjeti za uštedu:**
- ✅ **Kompresija slika**: Koristite JPG/WebP umjesto PNG
- ✅ **Minifikacija**: Minificirajte CSS/JS datoteke
- ✅ **Uklanjanje**: Obrišite nekorištene datoteke redovito
- ✅ **Monitoring**: Provjeravajte AWS Budgets u lab sučelju

**Procjena troškova za tipičnu statičku stranicu:**
```
Web stranica: 50 datoteka, ukupno 10MB
Monthly storage: 10MB × $0.023/GB = ~$0.0002/mjesec
Monthly visits: 1000 × $0.0004/1000 = ~$0.0004/mjesec
Ukupno: <$0.01/mjesec 💰
```

**📝 Savjet**: S3 statički hosting je jedan od najjeftinijih načina hostiranja web stranica na AWS-u!

---

## 🐛 Rješavanje problema

### **Uobičajeni problemi i rješenja:**

| **Problem** | **Simptomi** | **Rješenje** |
|-------------|--------------|---------------|
| **403 Forbidden** | Stranica nije dostupna | Provjerite bucket policy i public access |
| **404 Not Found** | Index.html se ne učitava | Provjerite je li index.html uploadat i ispravno imenovan |
| **CSS se ne učitava** | Stranica nema stilove | Provjerite putanje u HTML-u i upload CSS datoteka |
| **Sporije učitavanje** | Stranica se sporo učitava | Optimizirajte slike i minimizirajte datoteke |
| **Website URL ne radi** | Koristi object URL umjesto website endpoint | Koristite s3-website URL, ne s3 object URL |
| **CLI credentials istekli** | AWS CLI naredbe ne uspijevaju | Dohvatite nove credentials iz "AWS Details" u Learner Lab |
| **Session timeout** | "Session expired" poruke | Kliknite "Start Lab" za restart sesije |

### **Debug koraci:**

```bash
# 1. Provjerite bucket policy
aws s3api get-bucket-policy --bucket VAŠ-BUCKET-NAME

# 2. Provjerite public access konfiguraciju  
aws s3api get-public-access-block --bucket VAŠ-BUCKET-NAME

# 3. Provjerite website konfiguraciju
aws s3api get-bucket-website --bucket VAŠ-BUCKET-NAME

# 4. Listajte objekte u bucket-u
aws s3 ls s3://VAŠ-BUCKET-NAME --recursive
```

### **Browser debugging:**

```javascript
// Otvorite Developer Tools (F12) i provjerite:
// 1. Console tab - JavaScript greške
// 2. Network tab - failed requests (404, 403)
// 3. Elements tab - ispravne li su putanje do CSS/JS
```

---

## 🌟 Dio 10: Napredne mogućnosti

### **Korak 10.1: Custom domain (opcionalno)**

**Za produkcijski rad možete dodati custom domain:**
```
1. Registrirajte domain (nije dostupno u Learner Lab)
2. Kreirajte Route 53 hosted zone  
3. Konfigurirajte CNAME record
4. Ažurirajte bucket name da odgovara domeni
```

### **Korak 10.2: HTTPS s CloudFront i LabRole**

**Za sigurnu konekciju i AWS integracije:**
```
1. Kreirajte CloudFront distribution
2. Origin: Vaš S3 website endpoint
3. Redirect HTTP to HTTPS
4. Service role: Koristite LabRole kad je potreban
5. Koristite CloudFront URL umjesto S3 URL-a
```

**LabRole integracije s S3:**
- **Lambda funkcije**: Koristite LabRole za S3 pristup iz Lambda-e
- **API Gateway**: LabRole omogućuje S3 integracije
- **CloudFormation**: Automatski deployment s LabRole permissions

### **Korak 10.3: Backup i verzije**

```bash
# Backup vašeg web sadržaja
aws s3 sync s3://VAŠ-BUCKET-NAME ./backup/ --region us-east-1

# Omogućavanje versioning (za recovery)
aws s3api put-bucket-versioning \
    --bucket VAŠ-BUCKET-NAME \
    --versioning-configuration Status=Enabled

# Lista verzija datoteka (ako je versioning omogućen)
aws s3api list-object-versions --bucket VAŠ-BUCKET-NAME
```

**📝 LabRole napomena**: LabRole IAM uloga u Learner Lab-u automatski ima potrebne S3 permissions za ove operacije.

---

## 🧹 Dio 11: Cleanup instrukcije

**Kada završite s projektom i želite uštedjeti budžet:**

### **Korak 11.1: Brisanje sadržaja bucket-a**

```bash
# Putem CLI (brže za puno datoteka)
aws s3 rm s3://VAŠ-BUCKET-NAME --recursive --region us-east-1

# Ili putem konzole:
# 1. Odaberite sve objekte
# 2. Kliknite "Delete" 
# 3. Potvrdite brisanje
```

### **Korak 11.2: Brisanje bucket-a**

```bash
# Putem CLI
aws s3 rb s3://VAŠ-BUCKET-NAME --region us-east-1

# Ili putem konzole:
# 1. Idite na S3 main page
# 2. Odaberite bucket
# 3. Kliknite "Delete"
# 4. Upišite ime bucket-a za potvrdu
```

**⚠️ Važno**: Bucket mora biti prazan prije brisanja!

### **Korak 11.3: AWS Academy Reset opcija**

**Ako trebate potpuno resetirati svoj AWS account:**

**⚠️ OPREZ - DESTRUKTIVNA AKCIJA**:
1. **U Learner Lab sučelju**, kliknite "Reset" link
2. **Pročitajte upozorenje pažljivo**
3. **Kliknite "Yes"** samo ako stvarno želite **OBRISATI SVE**

**Što Reset radi:**
- ✅ **Briše SVE** što ste kreirali u AWS account-u
- ✅ **Vraća account** u početno stanje
- ❌ **NE resetira** vaš budžet
- ❌ **TRAJNO briše** sve podatke i konfiguracije

**Kad koristiti Reset:**
- Account je "zagušen" s previše resursa
- Želite početi iznova s čistim account-om
- Testirali ste mnoge konfiguracije i trebate reset

**📝 Preporučena alternativa**: Radije ručno obrišite nepotrebne resurse umjesto korištenja Reset opcije.

---

## ✅ Checklist za verifikaciju

**Nakon završetka ovog vodiča, trebali biste imati:**

- [ ] S3 bucket kreiran s jedinstvenim imenom
- [ ] Statički web hosting omogućen
- [ ] Bucket policy postavljen za javni pristup  
- [ ] HTML/CSS/JS datoteke uploadane
- [ ] Website URL dostupan s interneta
- [ ] Index.html se učitava kao glavna stranica
- [ ] Error.html radi za 404 greške
- [ ] CSS stilovi se ispravno primjenjuju
- [ ] JavaScript funkcionalnost radi
- [ ] Mobile responsiveness testiran
- [ ] Troškovi monitoring postavljen

---

## 🔗 Sljedeći koraci

### **Proširivanje vašeg projekta:**

**Frontend framework integracija:**
- **React/Vue/Angular**: Build static production verzije i deploy na S3
- **Jekyll/Hugo**: Static site generator integration

**Backend povezivanje:**
- **API Gateway + Lambda**: Serverless backend funkcionalnost  
- **Contact forme**: SES email integration
- **Database**: DynamoDB za jednostavne podatke

**Performance optimizacija:**
- **CloudFront**: CDN za globalnu distribuciju
- **Route 53**: Custom domain i DNS upravljanje
- **Certificate Manager**: Besplatni SSL certifikati

---

## 🎯 Sažetak

**Što ste postigli:**
- ✅ **Infrastruktura**: S3 bucket konfiguriran za web hosting
- ✅ **Sigurnost**: Pravilno konfigurirani public access permissions
- ✅ **Deployment**: Web stranica dostupna s interneta
- ✅ **Optimizacija**: Najbolje prakse za performanse i troškove
- ✅ **Održavanje**: Vještine za ažuriranje i upravljanje

**Vaš projekt sada ima:**
- Skalabilnu web hosting infrastrukturu
- Pristupačnu web stranicu s bilo gdje na internetu  
- Troškovno efikasno rješenje za statički sadržaj
- Temelj za naprednije AWS integracije
- Profesionalnu deployment pipeline

**Spremni ste za produkcijski web development!** 🚀

---

## 📖 Dodatni resursi

**AWS dokumentacija:**
- [S3 Static Website Hosting](https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteHosting.html)
- [S3 Bucket Policies](https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucket-policies.html)

**Best practices:**
- [Web Performance Optimization](https://web.dev/fast/)
- [Accessibility Guidelines](https://www.w3.org/WAI/WCAG21/quickref/)

**Debugging tools:**
- [Browser Developer Tools](https://developer.chrome.com/docs/devtools/)
- [AWS CLI Reference](https://docs.aws.amazon.com/cli/latest/reference/s3/)
