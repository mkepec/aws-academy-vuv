# 📦 AWS Academy Vodič: Amazon ECR Container Registry Setup

## 📚 Ciljevi učenja
Do kraja ovog vodiča moći ćete:
- ✅ Razumjeti koncepte container registry-ja i namespace-ova
- ✅ Kreirati ECR repository-je za frontend i backend aplikacije
- ✅ Konfigurirati AWS CLI za ECR autentifikaciju
- ✅ Buildati i pushati Docker image-e s lokalnog računala ili EC2
- ✅ Pullati i pokretati image-e iz ECR repository-ja
- ✅ Upravljati container image-ima kroz CLI i console

---

## ⏱️ **Trajanje**: 30 minuta
## 💰 **Utjecaj na budžet**: Minimalan (ECR storage ~$0.10/GB/mjesec)
## 🌍 **Region**: us-east-1 (N. Virginia) - **Obavezno za AWS Academy**

---

## 🔑 Preduvjeti
- ✅ AWS Academy Learner Lab sesija pokrenuta
- ✅ Pristup AWS Console-u preko "AWS" gumba
- ✅ Docker radi lokalno ILI postojeći EC2 instance
- ✅ Vaš projektni repository s radnim Dockerfile-om
- ✅ Osnovno razumijevanje Docker naredbi

**📝 Napomena za EC2 korisnike**: Amazon Linux 2023 koristi `dnf` package manager (ne `yum`). Sve naredbe u ovom vodiču odražavaju trenutnu verziju.

---

## 📋 Brzi pregled koraka

1. **Kreirajte ECR repository-je** za komponente vašeg projekta
2. **Konfigurirajte AWS CLI** s Learner Lab credentials
3. **Odaberite metodu buildanja** (lokalno računalo ili EC2)
4. **Buildajte i pushajte** Docker image-e u ECR
5. **Verificirajte i testirajte** deployment image-a

---

## 🏗️ Dio 1: Razumijevanje Container Registry-ja i Namespace-ova

### **Što je Amazon ECR?**
Amazon Elastic Container Registry (ECR) je AWS-ov **managed Docker registry service** - zamislite ga kao vaš privatni "Docker Hub" u cloudu.

### **Zašto koristiti Namespace-ove?**
**Namespace-ovi** pomažu logički organizirati vaše container image-e:

```
Vaš Account Registry: 123456789012.dkr.ecr.us-east-1.amazonaws.com/
├── my-ecommerce-app/frontend    ← Ovdje stavite ime vašeg projekta!
├── my-ecommerce-app/backend     ← Koristite VAŠE stvarno ime projekta
├── my-ecommerce-app/database    ← Ne "animal-rescue"
└── team-blog-app/web-server     ← Namespace drugačijeg projekta
```

**🚨 Važno**: Zamijenite `animal-rescue` s **imenom vašeg stvarnog projekta** kroz cijeli ovaj vodič!

**Primjeri dobrih imena projekata:**
- `student-portal` (za sustav upravljanja studentima)
- `ecommerce-shop` (za online trgovinu)
- `task-manager` (za todo aplikaciju)
- `social-media-app` (za socijalnu platformu)
- `inventory-system` (za upravljanje skladištem)

**Prednosti:**
- ✅ **Organizacija**: Grupiranje povezanih image-a
- ✅ **Timska suradnja**: Više ljudi može raditi na istom projektu
- ✅ **Upravljanje verzijama**: Tagiranje različitih verzija (latest, v1.0.0, dev)
- ✅ **Sigurnost**: Kontrola pristupa po repository-ju
- ✅ **Budućnost**: Lako dodavanje novih komponenti

---

## 📦 Dio 2: Kreiranje ECR Repository-ja

### **Korak 2.1: Navigacija na ECR servis**

1. **Pokrenite AWS Academy sesiju** i kliknite na "AWS" gumb
2. **Pretražite** "ECR" u AWS Console search baru
3. **Kliknite** "Elastic Container Registry"
4. **Provjerite region**: Provjerite da ste u **us-east-1** (gornji desni kut)

### **Korak 2.2: Kreiranje Frontend Repository-ja**

1. **Kliknite** narančasti "Create repository" gumb

2. **Konfiguracija repository-ja:**
   - **Visibility settings**: Private ✅ (default)
   - **Repository name**: `VAŠE-IME-PROJEKTA/frontend`
     - **🚨 ZAMIJENITE** `VAŠE-IME-PROJEKTA` s imenom vašeg stvarnog projekta!
     - **Primjeri**: `ecommerce-shop/frontend`, `student-portal/frontend`, `task-manager/frontend`
     - **Zadržite** `/frontend` za ime komponente
   - **Tag immutability**: Disabled (omogućuje prepisivanje tag-ova)
   - **Image scan settings**: ✅ Omogućite "Scan on push"
   - **Encryption settings**: AES-256 (default)

3. **Kliknite** "Create repository"

### **Korak 2.3: Kreiranje Backend Repository-ja**

1. **Kliknite** "Create repository" ponovno
2. **Konfiguracija repository-ja:**
   - **Repository name**: `VAŠE-IME-PROJEKTA/backend`
   - **🚨 Koristite ISTO ime projekta** kao za frontend repository!
   - **Zadržite sve ostale postavke iste**
3. **Kliknite** "Create repository"

### **Korak 2.4: Zabilježite URI-jeve vaših Repository-ja**

Sada biste trebali vidjeti dva repository-ja. **Kopirajte i spremite** ove URI-jeve:

```bash
# Vaši repository-ji (zamijenite 123456789012 s vašim stvarnim AWS account ID)
# Zamijenite VAŠE-IME-PROJEKTA s imenom vašeg stvarnog projekta
Frontend: 123456789012.dkr.ecr.us-east-1.amazonaws.com/VAŠE-IME-PROJEKTA/frontend
Backend:  123456789012.dkr.ecr.us-east-1.amazonaws.com/VAŠE-IME-PROJEKTA/backend

# Primjer s pravim imenom projekta:
Frontend: 123456789012.dkr.ecr.us-east-1.amazonaws.com/ecommerce-shop/frontend
Backend:  123456789012.dkr.ecr.us-east-1.amazonaws.com/ecommerce-shop/backend
```

**✅ Verifikacija**: Trebali biste vidjeti 2 repository-ja u vašem ECR console-u s "0 images" u svakom.

---

## 🔧 Dio 3: AWS CLI konfiguracija za ECR

### **Korak 3.1: Instaliranje AWS CLI (ako je potrebno)**

**Za Windows korisnike:**

**Opcija 1: Download MSI Installer (Preporučeno)**
```bash
# 1. Skinite s: https://awscli.amazonaws.com/AWSCLIV2.msi
# 2. Pokrenite installer
# 3. Otvorite Command Prompt ili PowerShell
# 4. Provjerite: aws --version
```

**Opcija 2: Korištenje Chocolatey-ja**
```bash
# Prvo instalirajte Chocolatey (ako nije instaliran):
# 1. Otvorite PowerShell kao Administrator
# 2. Pokrenite: Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
# 3. Zatvorite i ponovno otvorite PowerShell kao Administrator
# 4. Instalirajte AWS CLI:
choco install awscli

# Provjerite instalaciju
aws --version
```

**Opcija 3: Korištenje WSL-a (Windows Subsystem for Linux)**
```bash
# Ako imate WSL instaliran, koristite Linux instrukcije ispod
# Otvorite WSL terminal i slijedite Linux korake instalacije
```

**Za Mac korisnike:**
```bash
# Korištenjem Homebrew-a (preporučeno)
brew install awscli

# Ili skinite installer:
# https://awscli.amazonaws.com/AWSCLIV2.pkg

# Provjerite instalaciju
aws --version
```

**Za Linux/WSL korisnike:**
```bash
# Korištenjem curl-a
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

# Ili korištenjem package manager-a (Ubuntu/Debian)
sudo apt-get update
sudo apt-get install awscli -y

# Provjerite instalaciju
aws --version
```

**Za EC2 Instance:**
```bash
# AWS CLI je već instaliran na Amazon Linux 2023
aws --version

# Ako je potrebno, instalirajte s:
sudo dnf install awscli -y
```

### **Korak 3.2: Dohvaćanje AWS Academy Credentials**

1. **U vašem Learner Lab sučelju**, kliknite "AWS Details" gumb
2. **Kopirajte credentials** - vidjet ćete nešto ovako:

```bash
[default]
aws_access_key_id=ASIAIOSFODNN7EXAMPLE
aws_secret_access_key=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
aws_session_token=AQoEXAMPLEH4aoAH0gNCAPy...
```

### **Korak 3.3: Konfiguriranje AWS CLI-ja**

**Opcija A: Korištenjem aws configure (Interaktivno)**
```bash
aws configure set aws_access_key_id VAŠ_ACCESS_KEY_ID
aws configure set aws_secret_access_key VAŠ_SECRET_ACCESS_KEY  
aws configure set aws_session_token VAŠ_SESSION_TOKEN
aws configure set region us-east-1
```

**Opcija B: Korištenjem credentials datoteke**

**Za Windows:**
```bash
# Kreirajte AWS direktorij i credentials datoteku
mkdir "%USERPROFILE%\.aws"

# Kreirajte credentials datoteku (koristite Notepad ili text editor)
# Lokacija datoteke: C:\Users\VašeKorisničkoIme\.aws\credentials
# Sadržaj:
[default]
aws_access_key_id=VAŠ_ACCESS_KEY_ID
aws_secret_access_key=VAŠ_SECRET_ACCESS_KEY
aws_session_token=VAŠ_SESSION_TOKEN

# Kreirajte config datoteku
# Lokacija datoteke: C:\Users\VašeKorisničkoIme\.aws\config
# Sadržaj:
[default]
region=us-east-1
output=json
```

**Za Mac/Linux/WSL:**
```bash
# Kreiraj/uredi credentials datoteku
mkdir -p ~/.aws
cat > ~/.aws/credentials << EOF
[default]
aws_access_key_id=VAŠ_ACCESS_KEY_ID
aws_secret_access_key=VAŠ_SECRET_ACCESS_KEY
aws_session_token=VAŠ_SESSION_TOKEN
EOF

# Postavite default region
cat > ~/.aws/config << EOF
[default]
region=us-east-1
output=json
EOF
```

### **Korak 3.4: Testiranje AWS CLI konfiguracije**

```bash
# Testirajte osnovnu konekciju
aws sts get-caller-identity

# Očekivani izlaz (s vašim stvarnim account ID):
# {
#     "UserId": "AROAEXAMPLE...",
#     "Account": "123456789012",
#     "Arn": "arn:aws:sts::123456789012:assumed-role/LabRole/..."
# }

# Testirajte ECR pristup
aws ecr describe-repositories --region us-east-1
```

**✅ Verifikacija**: Trebali biste vidjeti JSON izlaz s vašim ECR repository-jima na listi.

---

## 🚀 Dio 4: Opcije Build-a i Push-a

Imate **dvije opcije** za buildanje i pushanje vaših Docker image-a:

### **Opcija A: Build na lokalnom računalu** 
- ✅ **Prednosti**: Koristite vašu poznatu development okolinu
- ✅ **Prednosti**: Nema EC2 troškova tijekom buildanja
- ❌ **Nedostaci**: Potreban Docker i AWS CLI instalirani lokalno
- ❌ **Nedostaci**: Vrijeme upload-a ovisi o brzini interneta

### **Opcija B: Build na EC2 Instance-u**
- ✅ **Prednosti**: Brz upload u ECR (ista AWS mreža)
- ✅ **Prednosti**: Nije potrebna lokalna Docker instalacija
- ✅ **Prednosti**: Konzistentna build okolina
- ❌ **Nedostaci**: Potreban EC2 instance (pokriven u zasebnom vodiču)
- ❌ **Nedostaci**: EC2 compute troškovi tijekom build-a

---

## 💻 Opcija A: Build i Push s lokalnog računala

### **Korak A.1: Dohvaćanje ECR Login naredbe**

1. **U ECR Console-u**, odaberite vaš `VAŠE-IME-PROJEKTA/frontend` repository
2. **Kliknite** "View push commands" gumb (gornji desni kut)
3. **Kopirajte prvu naredbu** (ECR login):

```bash
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 123456789012.dkr.ecr.us-east-1.amazonaws.com
```

4. **Pokrenite ovu naredbu** u vašem lokalnom terminalu

**Očekivani izlaz:**
```
Login Succeeded
```

### **Korak A.2: Build i Tag Image lokalno**

```bash
# Navigirajte u direktorij vašeg projekta (koji sadrži Dockerfile)
cd /path/to/your/project

# Buildajte vaš Docker image
docker build -t animal-frontend-app .

# Tag za ECR (zamijenite s vašim account ID)
docker tag animal-frontend-app:latest 123456789012.dkr.ecr.us-east-1.amazonaws.com/VAŠE-IME-PROJEKTA/frontend:latest

# Opcionalno: Tag s brojem verzije
docker tag animal-frontend-app:latest 123456789012.dkr.ecr.us-east-1.amazonaws.com/VAŠE-IME-PROJEKTA/frontend:v1.0.0
```

### **Korak A.3: Push u ECR**

```bash
# Push latest tag
docker push 123456789012.dkr.ecr.us-east-1.amazonaws.com/VAŠE-IME-PROJEKTA/frontend:latest

# Push version tag (ako je kreiran)
docker push 123456789012.dkr.ecr.us-east-1.amazonaws.com/VAŠE-IME-PROJEKTA/frontend:v1.0.0
```

### **Korak A.4: Strategija upravljanja verzijama**

**Kada ažurirate kod i trebate novi Docker image:**

```bash
# Opcija 1: Ažuriranje 'latest' tag-a (jednostavno, za development)
docker build -t my-frontend-app .
docker tag my-frontend-app:latest 123456789012.dkr.ecr.us-east-1.amazonaws.com/VAŠE-IME-PROJEKTA/frontend:latest
docker push 123456789012.dkr.ecr.us-east-1.amazonaws.com/VAŠE-IME-PROJEKTA/frontend:latest

# Opcija 2: Korištenje brojeva verzija (preporučeno za produkciju)
docker build -t my-frontend-app .
docker tag my-frontend-app:latest 123456789012.dkr.ecr.us-east-1.amazonaws.com/VAŠE-IME-PROJEKTA/frontend:v1.1.0
docker tag my-frontend-app:latest 123456789012.dkr.ecr.us-east-1.amazonaws.com/VAŠE-IME-PROJEKTA/frontend:latest
docker push 123456789012.dkr.ecr.us-east-1.amazonaws.com/VAŠE-IME-PROJEKTA/frontend:v1.1.0
docker push 123456789012.dkr.ecr.us-east-1.amazonaws.com/VAŠE-IME-PROJEKTA/frontend:latest

# Opcija 3: Korištenje git commit hash-a (za praćenje točne verzije koda)
GIT_HASH=$(git rev-parse --short HEAD)
docker tag my-frontend-app:latest 123456789012.dkr.ecr.us-east-1.amazonaws.com/VAŠE-IME-PROJEKTA/frontend:$GIT_HASH
docker push 123456789012.dkr.ecr.us-east-1.amazonaws.com/VAŠE-IME-PROJEKTA/frontend:$GIT_HASH
```

**Najbolje prakse tagiranja:**
- ✅ **latest**: Uvijek pokazuje na najnoviju verziju
- ✅ **v1.0.0**: Specifična verzija za rollback mogućnost  
- ✅ **dev/staging/prod**: Environment-specifični tag-ovi
- ✅ **commit-hash**: Točno praćenje koda

---

## 🖥️ Opcija B: Build i Push s EC2 Instance-a

**Preduvjeti**: Imate pokrenuti EC2 instance (pokriven u zasebnom EC2 vodiču)

### **Korak B.1: Povezivanje na vaš EC2 Instance**

**SSH instrukcije za povezivanje:**

**Za Windows korisnike:**
```bash
# Korištenjem PowerShell-a ili Command Prompt-a
ssh -i labsuser.pem ec2-user@VAŠ_EC2_PUBLIC_IP

# Korištenjem PuTTY-ja:
# 1. Skinite labsuser.ppk iz "AWS Details" 
# 2. Host Name: ec2-user@VAŠ_EC2_PUBLIC_IP
# 3. Connection → SSH → Auth → Browse za labsuser.ppk
# 4. Kliknite Open
```

**Za Mac/Linux/WSL korisnike:**
```bash
# Osigurajte datoteku ključa (prvi put samo)
chmod 400 labsuser.pem

# Povezivanje na instance
ssh -i labsuser.pem ec2-user@VAŠ_EC2_PUBLIC_IP
```

### **Korak B.2: Priprema EC2 okruženja**

**Nakon što se spojite na vaš EC2 instance**, pokrenite:

```bash
# Ažuriranje sistemskih paketa (Amazon Linux 2023 koristi dnf)
sudo dnf update -y

# Instaliranje Docker-a
sudo dnf install docker -y
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -a -G docker ec2-user

# Instaliranje Git-a
sudo dnf install git -y

# Odjavite se i ponovno se prijavite za docker group promjene
exit
# SSH se ponovno spojite koristeći istu naredbu kao gore
```

### **Korak B.3: Konfiguracija AWS CLI na EC2**

**Dobra vijest**: Ako vaš EC2 ima **LabInstanceProfile** priložen, AWS CLI bi trebao raditi automatski!

```bash
# Testirajte rade li credentials
aws sts get-caller-identity

# Ako radi, vidjet ćete informacije o vašem accountu
# Ako ne radi, konfigurirajte ručno koristeći credentials iz "AWS Details"
```

### **Korak B.4: Clone i Build na EC2**

```bash
# Clone vaš repository
git clone VAŠ_REPOSITORY_URL
cd VAŠ_PROJECT_DIRECTORY

# Provjerite da Dockerfile postoji
ls -la Dockerfile

# Login u ECR
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 123456789012.dkr.ecr.us-east-1.amazonaws.com

# Build image
docker build -t my-frontend-app .

# Tag za ECR (zamijenite VAŠE-IME-PROJEKTA s imenom vašeg stvarnog projekta)
docker tag my-frontend-app:latest 123456789012.dkr.ecr.us-east-1.amazonaws.com/VAŠE-IME-PROJEKTA/frontend:latest

# Push u ECR
docker push 123456789012.dkr.ecr.us-east-1.amazonaws.com/VAŠE-IME-PROJEKTA/frontend:latest
```

### **Korak B.5: Rukovanje ažuriranjima koda na EC2**

**Kada ažurirate kod i želite rebuild:**

```bash
# Pullajte najnovije promjene koda
git pull origin main

# Rebuil s novim version tag-om
docker build -t my-frontend-app .

# Tag s brojem verzije i latest
docker tag my-frontend-app:latest 123456789012.dkr.ecr.us-east-1.amazonaws.com/VAŠE-IME-PROJEKTA/frontend:v1.1.0
docker tag my-frontend-app:latest 123456789012.dkr.ecr.us-east-1.amazonaws.com/VAŠE-IME-PROJEKTA/frontend:latest

# Push oba tag-a
docker push 123456789012.dkr.ecr.us-east-1.amazonaws.com/VAŠE-IME-PROJEKTA/frontend:v1.1.0
docker push 123456789012.dkr.ecr.us-east-1.amazonaws.com/VAŠE-IME-PROJEKTA/frontend:latest
```

---

## 🔍 Dio 5: Verifikacija i upravljanje Image-ima

### **Korak 5.1: Listanje Image-a putem CLI-ja**

```bash
# Listajte sve image-e u frontend repository-ju (zamijenite VAŠE-IME-PROJEKTA)
aws ecr list-images --repository-name VAŠE-IME-PROJEKTA/frontend --region us-east-1

# Dohvatite detaljne informacije o image-u
aws ecr describe-images --repository-name VAŠE-IME-PROJEKTA/frontend --region us-east-1

# Primjer s pravim imenom projekta:
aws ecr list-images --repository-name ecommerce-shop/frontend --region us-east-1
```

### **Korak 5.2: Verifikacija u AWS Console-u**

1. **Idite na ECR Console**
2. **Kliknite** na vaš `VAŠE-IME-PROJEKTA/frontend` repository
3. **Refresh-ajte** stranicu ako je potrebno
4. **Trebali biste vidjeti** vaš uploadani image s:
   - Image URI
   - Image tag-ovi (latest, v1.0.0, itd.)
   - Veličina image-a
   - Datum push-a
   - Rezultati vulnerability scan-a

### **Korak 5.3: Pull i testiranje Image-a**

**S bilo kojeg stroja s Docker-om i ECR pristupom:**

```bash
# Login u ECR
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 123456789012.dkr.ecr.us-east-1.amazonaws.com

# Pull image (zamijenite VAŠE-IME-PROJEKTA)
docker pull 123456789012.dkr.ecr.us-east-1.amazonaws.com/VAŠE-IME-PROJEKTA/frontend:latest

# Pokrenite container (primjer za web aplikaciju)
docker run -d \
    --name my-frontend \
    -p 80:80 \
    123456789012.dkr.ecr.us-east-1.amazonaws.com/VAŠE-IME-PROJEKTA/frontend:latest

# Provjerite radi li
docker ps
```

**⚠️ Važno**: Ako pokretate na EC2, provjerite da je **port 80** otvoren u vašoj security grupi!

---

## 🛡️ Dio 6: Konfiguracija Security grupe (za Docker Run)

**Ako planirate pokretati container-e na EC2**, osigurajte pravilan pristup portovima:

### **Uobičajeni zahtjevi za portove:**

| Tip aplikacije | Port | Protokol | Source |
|-----------------|------|----------|---------|
| **Web Frontend** | 80 | TCP | 0.0.0.0/0 |
| **HTTPS Frontend** | 443 | TCP | 0.0.0.0/0 |
| **Development Server** | 3000 | TCP | 0.0.0.0/0 |
| **API Backend** | 8080 | TCP | 0.0.0.0/0 |
| **Database** | 5432/3306 | TCP | Samo VPC |

### **Brzo ažuriranje Security grupe:**

1. **EC2 Console** → **Security Groups**
2. **Odaberite** security grupu vašeg instance-a  
3. **Edit inbound rules** → **Add rule**
4. **Type**: HTTP, **Port**: 80, **Source**: Anywhere-IPv4
5. **Save rules**

---

## 📊 Dio 7: Najbolje prakse upravljanja Image-ima

### **Strategija tagiranja**

```bash
# Koristite semantic versioning
docker tag my-app:latest ECR_URI:v1.0.0
docker tag my-app:latest ECR_URI:v1.0
docker tag my-app:latest ECR_URI:latest

# Koristite environment tag-ove
docker tag my-app:latest ECR_URI:dev
docker tag my-app:latest ECR_URI:staging  
docker tag my-app:latest ECR_URI:prod
```

### **Upravljanje lifecycle-om Repository-ja**

```bash
# Obrišite stare verzije image-a za uštedu troškova (zamijenite VAŠE-IME-PROJEKTA)
aws ecr batch-delete-image \
    --repository-name VAŠE-IME-PROJEKTA/frontend \
    --image-ids imageTag=old-version \
    --region us-east-1

# Listajte veličine image-a za monitoring storage troškova
aws ecr describe-images \
    --repository-name VAŠE-IME-PROJEKTA/frontend \
    --query 'imageDetails[*].[imageTags[0],imageSizeInBytes]' \
    --output table
```

---

## 💰 Budžetska razmatranja

### **ECR troškovi:**
- **Storage**: ~$0.10 po GB po mjesecu
- **Data Transfer**: Besplatan unutar istog regiona
- **Scanning**: Besplatan tier: 100 scan-ova po mjesecu

### **Savjeti za optimizaciju troškova:**
```bash
# Pratite veličine repository-ja
aws ecr describe-repositories --query 'repositories[*].[repositoryName,repositorySizeInBytes]' --output table

# Postavite lifecycle policy-je (opcionalno)
# Automatski brisanje starih image-a nakon X dana
```

---

## 🐛 Rješavanje problema

### **Uobičajeni problemi:**

| Problem | Simptomi | Rješenje |
|---------|----------|----------|
| **Autentifikacija neuspješna** | `docker login` ne uspijeva | Ponovno pokrenite ECR login naredbu |
| **Dozvola odbijena** | Push/pull ne uspijeva | Provjerite je li LabRole priložen EC2 |
| **Pogrešan region** | Repository se ne pronalazi | Provjerite us-east-1 region |
| **Build ne uspijeva** | Docker build greške | Provjerite sintaksu Dockerfile-a |
| **Sesija istekla** | CLI naredbe ne uspijevaju | Dohvatite nove credentials iz "AWS Details" |

### **Debug naredbe:**

```bash
# Provjerite AWS CLI konfiguraciju
aws configure list

# Testirajte ECR konekciju
aws ecr describe-repositories

# Provjerite Docker login status
docker info | grep Registry

# Pogledajte lokalne Docker image-e
docker images | grep ECR_URI
```

---

## ✅ Checklist za verifikaciju

**Nakon završetka ovog vodiča, trebali biste imati:**

- [ ] Dva ECR repository-ja kreirana (frontend/backend)
- [ ] AWS CLI konfiguriran s Learner Lab credentials
- [ ] Uspješno ulogiran u ECR
- [ ] Buildan i tagiran Docker image lokalno ili na EC2
- [ ] Push image u ECR repository
- [ ] Verifikacija da se image pojavljuje u AWS Console-u
- [ ] Uspješno pullan image iz ECR-a
- [ ] (Ako testirate) Container se pokreće i dostupan je

---

## 🔗 Sljedeći koraci

### **Završen ECR setup? Odaberite svoj put:**

**Frontend Deployment:**
- **Sljedeći vodič**: "Frontend Deployment na EC2"
- **Koristit ćete**: Image-e iz ECR repository-ja koje ste upravo kreirali

**Backend Development:**
- **Kreirajte**: Backend Docker image koristeći isti proces
- **Push**: U `VAŠE-IME-PROJEKTA/backend` repository

**Database integracija:**
- **Sljedeći vodič**: "RDS Database Setup" 
- **Spojite**: Backend container-e na managed bazu podataka

---

## 🎯 Sažetak

**Što ste postigli:**
- ✅ **Razumijevanje**: Container registry-ja i namespace organizacije
- ✅ **Infrastruktura**: ECR repository-ji za vaš projekt
- ✅ **Sigurnost**: AWS CLI konfiguracija s Learner Lab credentials
- ✅ **Vještine**: Buildanje i pushanje container image-a
- ✅ **Verifikacija**: Upravljanje image-ima i testiranje

**Vaš projekt sada ima:**
- Privatni container registry u AWS-u
- Organizirano pohranjivanje image-a s namespace-ovima
- Temelj za skalabilnu deployment arhitekturu
- Vještine za upravljanje lifecycle-om container image-a

**Spremni ste za produkcijski deployment!** 🚀
