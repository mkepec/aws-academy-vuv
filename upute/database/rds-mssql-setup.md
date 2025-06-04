# 🗄️ AWS Academy Vodič: RDS MSSQL Database Setup

## 📚 Ciljevi učenja
Do kraja ovog vodiča moći ćete:
- ✅ Kreirati RDS MSSQL database instance u AWS Academy okruženju
- ✅ Konfigurirati security grupe za database pristup
- ✅ Postaviti database credentials i connection string-ove
- ✅ Spojiti backend aplikacije na MSSQL bazu
- ✅ Upravljati backup-ima i maintenance postavkama
- ✅ Optimizirati troškove i performance za student projekte

---

## ⏱️ **Trajanje**: 40 minuta
## 💰 **Utjecaj na budžet**: Umjeren (db.t3.micro ~$15-25/mjesec)
## 🌍 **Region**: us-east-1 (N. Virginia) - **Obavezno za AWS Academy**

---

## 🔑 Preduvjeti
- ✅ AWS Academy Learner Lab sesija pokrenuta
- ✅ Pristup AWS Console-u preko "AWS" gumba
- ✅ Osnovni backend projekt koji zahtijeva bazu podataka
- ✅ Razumijevanje SQL koncepata i connection string-ova
- ✅ (Opcionalno) EC2 instance za testiranje konekcije

**⚠️ AWS Academy ograničenja**: Enhanced monitoring NIJE podržan (mora se onemogućiti)

---

## 📋 Brzi pregled koraka

1. **Planirajte database arhitekturu** i security pristup
2. **Kreirajte DB subnet grupu** za network konfiguraciju
3. **Konfigurirajte security grupe** za database pristup
4. **Kreirajte RDS MSSQL instance** s optimalnim postavkama
5. **Testirajte konekciju** i generirajte connection string-ove
6. **Optimizirajte** za troškove i performance

---

## 🏗️ Dio 1: Razumijevanje Database arhitekture

### **Osnovni vs. Napredni Setup**

**🎓 Osnovni Setup (ovaj vodič):**
```
Internet → Security Group → RDS MSSQL (Public/Default VPC)
                ↑
        Backend aplikacija (EC2 ili lokalno)
```

**🏢 Napredni Setup (buduća tema):**
```
Internet → Public Subnet (EC2 Backend) → Private Subnet (RDS MSSQL)
           │                               │
           └─── Secured connection ────────┘
```

### **Zašto počinjemo s osnovnim setupom?**
- ✅ **Lakše za učenje**: Manje network kompleksnosti
- ✅ **Brža implementacija**: Fokus na database konfiguraciju
- ✅ **Idealno za development**: Student projekti i prototipove
- ✅ **Manje troškova**: Jednostavnija infrastruktura

### **MSSQL specifičnosti u AWS RDS:**
- **Default port**: 1433
- **Authentication**: SQL Server authentication i Windows authentication
- **Backup**: Automatski backup s retention period
- **Scaling**: Vertikalno skaliranje (mijenjanje instance veličine)
- **Encryption**: Podržan at-rest i in-transit

---

## 🔧 Dio 2: Kreiranje DB Subnet grupe

### **Korak 2.1: Navigacija na RDS servis**

1. **Pokrenite AWS Academy sesiju** i kliknite na "AWS" gumb
2. **Pretražite** "RDS" u AWS Console search baru
3. **Kliknite** "RDS" (Relational Database Service)
4. **Provjerite region**: Provjerite da ste u **us-east-1** (gornji desni kut)

### **Korak 2.2: Kreiranje DB Subnet grupe (Opcionalno za osnovni setup)**

**Napomena**: Za osnovni setup možete koristiti default subnet grupu, ali ovo je korisno za razumijevanje.

1. **U lijevom navigation panelu**, kliknite "Subnet groups"
2. **Kliknite** "Create DB subnet group"

3. **Konfiguracija:**
   - **Name**: `VAŠE-IME-PROJEKTA-db-subnet-group`
   - **Description**: `Database subnet group for VAŠE-IME-PROJEKTA`
   - **VPC**: Odaberite default VPC
   - **Availability Zones**: Odaberite **najmanje 2 AZ** (us-east-1a, us-east-1b)
   - **Subnets**: Odaberite po jedan subnet iz svakog AZ

4. **Kliknite** "Create"

**✅ Verifikacija**: Subnet grupa kreirana i pokazuje status "Complete"

---

## 🛡️ Dio 3: Konfiguracija Security grupe

### **Korak 3.1: Kreiranje Database Security grupe**

1. **Navigirajte na EC2 Console** → **Security Groups**
2. **Kliknite** "Create security group"

3. **Basic details:**
   - **Security group name**: `VAŠE-IME-PROJEKTA-db-sg`
   - **Description**: `Security group for MSSQL database access`
   - **VPC**: Odaberite default VPC

### **Korak 3.2: Konfiguracija Inbound rules**

**🚨 Važno**: Konfiguracija ovisi o tome odakle se spajate na bazu!

#### **Opcija A: Pristup s EC2 instance-a (Preporučeno)**
```
Type: MS SQL
Port: 1433
Source: Security grupa vašeg EC2 instance-a
```

#### **Opcija B: Pristup s lokalne mašine (Development)**
```
Type: MS SQL  
Port: 1433
Source: Your IP (0.0.0.0/0 ZA TESTIRANJE SAMO!)
```

#### **Opcija C: Hibridni pristup**
```
Rule 1: MS SQL, Port 1433, Source: EC2 Security Group
Rule 2: MS SQL, Port 1433, Source: Your IP
```

**⚠️ Sigurnosno upozorenje**: 
- `0.0.0.0/0` omogućuje pristup iz cijelog interneta!
- Koristite samo za development i testiranje
- Za produkciju koristite specifične IP adrese ili security grupe

### **Korak 3.3: Outbound rules**
```
Type: All traffic
Port: All
Destination: 0.0.0.0/0
```
(Default - obično nije potrebno mijenjati)

4. **Kliknite** "Create security group"

**✅ Verifikacija**: Security grupa kreirana s pravilnim inbound rules za MSSQL

---

## 🗄️ Dio 4: Kreiranje RDS MSSQL Instance-a

### **Korak 4.1: Pokretanje Database Creation Wizard-a**

1. **U RDS Console-u**, kliknite "Create database"
2. **Database creation method**: Standard create
3. **Engine options**: Microsoft SQL Server

### **Korak 4.2: Engine version i Edition**

**Engine version:**
- **Odaberite najnoviju dostupnu verziju** (obično SQL Server 2019 ili 2022)
- **Edition**: Express ili Standard (Express je besplatan tier)

**💡 Preporuka za studente**: Koristite Express edition za manje projekte

### **Korak 4.3: Templates**

**Odaberite**: Free tier (ako je dostupan) ili Dev/Test

### **Korak 4.4: Settings (Credentials)**

**DB instance identifier**: `VAŠE-IME-PROJEKTA-mssql-db`

**Master username**: `admin` (ili vaše preferirano ime)

**Master password management:**
- **Odaberite**: "Self managed"
- **Master password**: Kreirajte **jaku lozinku** (min 8 znakova)
- **Confirm password**: Ponovite istu lozinku

**🔐 Važno**: **Spremite credentials negdje sigurno!**
```
Database: VAŠE-IME-PROJEKTA-mssql-db
Username: admin
Password: VašaJakaLozinka123!
Endpoint: (dobit ćete nakon kreiranja)
Port: 1433
```

### **Korak 4.5: Instance configuration**

**DB instance class:**
- **Burstable classes**: `db.t3.micro` (najjeftiniji)
- **Za veće projekte**: `db.t3.small` ili `db.t3.medium`

**Storage:**
- **Storage type**: General Purpose SSD (gp2)
- **Allocated storage**: 20 GB (minimum)
- **Enable storage autoscaling**: ✅ (maksimum 100 GB zbog AWS Academy limita)

### **Korak 4.6: Connectivity**

**Compute resource:**
- **Don't connect to an EC2 compute resource** (za osnovni setup)

**Network type**: IPv4

**VPC**: Default VPC

**DB subnet group**: Default ili vaša kreirana grupa

**Public access**: 
- **Yes** (za osnovni setup s vanjskim pristupom)
- **No** (za produkcijski setup)

**VPC security group:**
- **Choose existing**
- **Odaberite vašu kreiranu security grupu**: `VAŠE-IME-PROJEKTA-db-sg`
- **Uklonite default grupu** ako nije potrebna

**Availability zone**: No preference

**Database port**: 1433 (default)

### **Korak 4.7: Database authentication**

**Database authentication options:**
- **Password authentication** ✅ (jednostavno za studente)

### **Korak 4.8: Additional configuration**

**Database options:**
- **Initial database name**: `VAŠE_IME_PROJEKTA_DB` (bez crtice!)

**DB parameter group**: default

**Option group**: default

**Backup:**
- **Enable automated backups**: ✅
- **Backup retention period**: 7 days
- **Backup window**: No preference

**Monitoring:**
- **Enable Enhanced monitoring**: ❌ **MORA BITI ONEMOGUĆENO** (AWS Academy ograničenje!)
- **Enable Performance Insights**: ❌ (ušteda troškova)

**Log exports**: Ostavite prazno (za početak)

**Maintenance:**
- **Enable auto minor version upgrade**: ✅
- **Maintenance window**: No preference

**Deletion protection**: ❌ (onemogući za lakše brisanje tijekom učenja)

### **Korak 4.9: Kreiranje Database-a**

1. **Pregledajte sve postavke**
2. **Estimated monthly costs**: Provjerite da li se uklapa u budžet
3. **Kliknite** "Create database"

**⏳ Čekanje**: Database kreiranje traje **10-15 minuta**

**✅ Verifikacija**: Status pokazuje "Creating..." zatim "Available"

---

## 🔗 Dio 5: Connection String i testiranje

### **Korak 5.1: Dohvaćanje Database Endpoint-a**

1. **U RDS Console-u**, kliknite na vaš database
2. **U "Connectivity & security" sekciji** kopirajte:
   - **Endpoint**: `vaše-ime-projekta-mssql-db.xxxxxxxxx.us-east-1.rds.amazonaws.com`
   - **Port**: `1433`

### **Korak 5.2: Kreiranje Connection String-ova**

**Za .NET aplikacije:**
```csharp
Server=vaše-ime-projekta-mssql-db.xxxxxxxxx.us-east-1.rds.amazonaws.com,1433;
Database=VAŠE_IME_PROJEKTA_DB;
User Id=admin;
Password=VašaJakaLozinka123!;
Encrypt=true;
TrustServerCertificate=true;
```

**Za Node.js (mssql package):**
```javascript
const config = {
    server: 'vaše-ime-projekta-mssql-db.xxxxxxxxx.us-east-1.rds.amazonaws.com',
    port: 1433,
    database: 'VAŠE_IME_PROJEKTA_DB',
    user: 'admin',
    password: 'VašaJakaLozinka123!',
    options: {
        encrypt: true,
        trustServerCertificate: true
    }
};
```

**Za Python (pyodbc):**
```python
connection_string = (
    "DRIVER={ODBC Driver 17 for SQL Server};"
    "SERVER=vaše-ime-projekta-mssql-db.xxxxxxxxx.us-east-1.rds.amazonaws.com,1433;"
    "DATABASE=VAŠE_IME_PROJEKTA_DB;"
    "UID=admin;"
    "PWD=VašaJakaLozinka123!;"
    "Encrypt=yes;"
    "TrustServerCertificate=yes;"
)
```

### **Korak 5.3: Testiranje konekcije s SQL Server Management Studio (SSMS)**

**Ako imate SSMS instalirano lokalno:**

1. **Otvorite SSMS**
2. **Connect to Server:**
   - **Server type**: Database Engine
   - **Server name**: `vaš-endpoint,1433`
   - **Authentication**: SQL Server Authentication
   - **Login**: `admin`
   - **Password**: `VašaJakaLozinka123!`
3. **Kliknite** "Connect"

### **Korak 5.4: Testiranje s AWS CLI (ako je potrebno)**

```bash
# Provjera da li je database dostupan
aws rds describe-db-instances --db-instance-identifier VAŠE-IME-PROJEKTA-mssql-db --region us-east-1
```

**✅ Verifikacija**: Konekcija uspješna i možete vidjeti kreiran database

---

## 🔧 Dio 6: Integracija s Backend aplikacijom

### **Korak 6.1: Environment varijable setup**

**Za .NET aplikacije (.env ili appsettings.json):**
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=VAŠ-ENDPOINT,1433;Database=VAŠA_BAZA;User Id=admin;Password=VAŠA-LOZINKA;Encrypt=true;TrustServerCertificate=true;"
  }
}
```

**Za Node.js aplikacije (.env datoteka):**
```bash
DB_HOST=vaš-endpoint
DB_PORT=1433
DB_NAME=VAŠA_BAZA
DB_USER=admin
DB_PASSWORD=VAŠA-LOZINKA
DB_ENCRYPT=true
```

### **Korak 6.2: Docker Environment varijable**

**Ako koristite Docker za backend:**
```dockerfile
# U Dockerfile-u
ENV DB_HOST=vaš-rds-endpoint
ENV DB_PORT=1433
ENV DB_NAME=VAŠA_BAZA

# Ili u docker run naredbi
docker run -e DB_HOST=vaš-endpoint \
           -e DB_PORT=1433 \
           -e DB_NAME=VAŠA_BAZA \
           -e DB_USER=admin \
           -e DB_PASSWORD=VAŠA-LOZINKA \
           vaša-backend-aplikacija
```

### **Korak 6.3: Connection pool konfiguracija**

**Za produkciju je važno konfigurirati connection pooling:**

**.NET Entity Framework:**
```csharp
services.AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(connectionString, sqlOptions =>
    {
        sqlOptions.EnableRetryOnFailure(
            maxRetryCount: 3,
            maxRetryDelay: TimeSpan.FromSeconds(5),
            errorNumbersToAdd: null);
    }));
```

**Node.js:**
```javascript
const pool = new sql.ConnectionPool({
    ...config,
    pool: {
        max: 10,
        min: 0,
        idleTimeoutMillis: 30000
    }
});
```

---

## 📊 Dio 7: Monitoring i održavanje

### **Korak 7.1: CloudWatch Metrics**

**AWS automatski prikuplja metrike:**
- **CPU Utilization**
- **Database Connections**
- **Read/Write IOPS**
- **Free Storage Space**

**Pristup metrikama:**
1. **RDS Console** → **Vaš database** → **Monitoring** tab
2. **CloudWatch Console** → **Metrics** → **RDS**

### **Korak 7.2: Backup verifikacija**

**Provjera automatskih backup-a:**
1. **RDS Console** → **Automated backups**
2. **Trebali biste vidjeti** daily backup-e s 7-day retention

**Kreiranje manual snapshot-a:**
1. **Odaberite vaš database**
2. **Actions** → **Take snapshot**
3. **Snapshot identifier**: `VAŠE-IME-PROJEKTA-manual-backup-DATUM`

### **Korak 7.3: Performance optimizacija**

**Osnovne optimizacije za student projekte:**

```sql
-- Kreiranje indeksa za česte query-je
CREATE INDEX IX_UserId ON Orders(UserId);

-- Optimizacija connection timeout-a
-- U connection string-u dodajte:
-- Connection Timeout=30;Command Timeout=60;
```

---

## 💰 Dio 8: Upravljanje troškovima

### **Troškovi RDS MSSQL:**

| Instance tip | Cijena/sat | Cijena/mjesec | Preporučeno za |
|--------------|------------|---------------|----------------|
| **db.t3.micro** | ~$0.035 | ~$25 | Mali student projekti |
| **db.t3.small** | ~$0.070 | ~$50 | Srednji projekti |
| **db.t3.medium** | ~$0.140 | ~$100 | Veliki timski projekti |

### **Strategije uštede:**

#### **Stop/Start Database (Manual)**
```bash
# Zaustavljanje database-a kad ne koristite
aws rds stop-db-instance --db-instance-identifier VAŠE-IME-PROJEKTA-mssql-db

# Pokretanje database-a kad je potreban
aws rds start-db-instance --db-instance-identifier VAŠE-IME-PROJEKTA-mssql-db
```

#### **Schedulirani Stop/Start s Lambda (Napredni topic)**

**⚠️ Važne napomene o troškovima:**
- **Stopped database**: I dalje se naplaćuje storage
- **Automatski restart**: AWS će automatski pokrenuti bazu nakon 7 dana
- **Backup storage**: Dodatni troškovi za backup veći od 20GB

### **Cost Monitoring:**

```bash
# Provjera trenutnih troškova
aws ce get-cost-and-usage \
    --time-period Start=2025-01-01,End=2025-01-31 \
    --granularity MONTHLY \
    --metrics BlendedCost \
    --group-by Type=DIMENSION,Key=SERVICE
```

---

## 🛡️ Dio 9: Sigurnost i best practices

### **Sigurnosni checklist:**

#### **Network sigurnost:**
- [ ] Security grupa ograničava pristup na potrebne IP-jeve/grupe
- [ ] Database u private subnet-u (napredni setup)
- [ ] SSL/TLS encryption omogućen
- [ ] VPC Network ACL konfiguriran (napredni)

#### **Autentifikacija:**
- [ ] Jaka master password (min 12 znakova)
- [ ] Password rotation policy (produkcija)
- [ ] IAM database authentication (napredni)
- [ ] AWS Secrets Manager za password management (napredni)

#### **Backup i recovery:**
- [ ] Automated backup omogućen
- [ ] Point-in-time recovery testiran
- [ ] Cross-region backup (produkcija)
- [ ] Disaster recovery plan (produkcija)

### **Development vs Production konfiguracija:**

| Postavka | Development | Production |
|----------|-------------|------------|
| **Public access** | Yes | No |
| **Multi-AZ** | No | Yes |
| **Backup retention** | 7 days | 30 days |
| **Deletion protection** | No | Yes |
| **Enhanced monitoring** | No | Yes (izvan Academy) |
| **Encryption** | Optional | Obavezno |

---

## 🐛 Rješavanje problema

### **Uobičajeni problemi:**

| Problem | Simptomi | Rješenje |
|---------|----------|----------|
| **Timeout na konekciju** | Cannot connect to server | Provjeri security grupu i network pristup |
| **Authentication failed** | Login failed for user | Provjeri username/password credentials |
| **Database not found** | Invalid object name | Provjeri database name u connection string-u |
| **Permission denied** | Access denied | Provjeri user permissions i roles |
| **Connection refused** | No connection could be made | Provjeri da li je database running |

### **Debug koraci:**

#### **Network connectivity test:**
```bash
# Test connectivity na port 1433
telnet vaš-rds-endpoint 1433

# ili koristite nmap
nmap -p 1433 vaš-rds-endpoint
```

#### **AWS CLI debug:**
```bash
# Provjeri status database-a
aws rds describe-db-instances \
    --db-instance-identifier VAŠE-IME-PROJEKTA-mssql-db \
    --query 'DBInstances[0].DBInstanceStatus'

# Provjeri security grupe
aws rds describe-db-instances \
    --db-instance-identifier VAŠE-IME-PROJEKTA-mssql-db \
    --query 'DBInstances[0].VpcSecurityGroups'
```

#### **Connection string validation:**
```bash
# Provjeri sintaksu connection string-a
# .NET format provjera:
# Server=endpoint,port;Database=db;User Id=user;Password=pass;

# Provjeri special znakove u password-u
# Escapiraj ili koristite različit password
```

---

## ✅ Checklist za verifikaciju

**Nakon završetka ovog vodiča, trebali biste imati:**

- [ ] RDS MSSQL instance kreiran i status "Available"
- [ ] Security grupa konfigurirana za database pristup
- [ ] Database credentials sigurno pohranjeni
- [ ] Connection string-ovi generirani za vaš tech stack
- [ ] Uspješno testiranje konekcije
- [ ] Environment varijable konfigurirane za backend
- [ ] Automated backup-i omogućeni
- [ ] Monitoring setup u CloudWatch-u
- [ ] Plan za cost management

---

## 🔗 Sljedeći koraci

### **Osnovni RDS setup završen? Odaberite proširenja:**

**Backend integracija:**
- **Povežite vaš backend** s kreiranim database-om
- **Implementirajte Entity Framework/ORM** konfiguraciju
- **Testirajte CRUD operacije** kroz API-je

**Napredna arhitektura:**
- **Sljedeći vodič**: "Napredni Network Setup - Private Subnets"
- **Multi-AZ deployment** za high availability
- **Read replicas** za performance scaling

**Security poboljšanja:**
- **AWS Secrets Manager** integracija
- **VPC Endpoints** za privatnu komunikaciju
- **Database encryption** at-rest i in-transit

**DevOps workflow:**
- **Database migration scripts** s Entity Framework
- **CI/CD pipeline** s database deployment
- **Infrastructure as Code** s CloudFormation

---

## 🎯 Sažetak

**Što ste postigli:**
- ✅ **Database infrastruktura**: Potpuno funkcionalan MSSQL database u AWS-u
- ✅ **Network konfiguracija**: Security grupe i pristup kontrola
- ✅ **Connection management**: String-ovi za različite tech stack-ove
- ✅ **Cost awareness**: Optimizacija troškova za student projekte
- ✅ **Monitoring setup**: Basic observability s CloudWatch-om

**Vaš projekt sada ima:**
- Skalirajuću managed database infrastrukturu
- Sigurnu konfiguraciju s proper access kontrolom
- Foundation za kompleksne backend aplikacije
- Cost-efficient setup za development i testiranje

**Spremni ste za backend aplikaciju s enterprise-grade database-om!** 🚀

### **Napredni setup pregled (buduća tema):**

```
┌─────────────────────────────────────────────────────┐
│                   Production Architecture            │
├─────────────────────────────────────────────────────┤
│                                                     │
│  Internet Gateway                                   │
│         │                                           │
│  ┌─────────────────┐    ┌──────────────────────┐    │
│  │   Public Subnet │    │   Private Subnet     │    │
│  │                 │    │                      │    │
│  │  ┌───────────┐  │    │  ┌─────────────────┐ │    │
│  │  │    EC2    │  │    │  │   RDS MSSQL     │ │    │
│  │  │ (Backend) │  │────┼──│   (Database)    │ │    │
│  │  └───────────┘  │    │  └─────────────────┘ │    │
│  └─────────────────┘    └──────────────────────┘    │
│                                                     │
│  Benefits:                                          │
│  • Database nedostupan izvana                       │
│  • Additional security layer                        │
│  • Production-ready architecture                    │
│  • Compliance requirements                          │
└─────────────────────────────────────────────────────┘
```

**Ovaj osnovni setup omogućuje vam fokus na razvoj aplikacije, dok napredni setup priprema za production deployment!**
