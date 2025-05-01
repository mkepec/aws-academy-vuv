# Pokretanje EC2 instance putem AWS Learner Lab-a

Ovaj vodič objašnjava kako pokrenuti EC2 instancu (virtualno računalo) u Amazon Web Services (AWS) okruženju, koristeći **Learner Lab** koji je dostupan studentima putem AWS Akademije.

---

## ✅ Preduvjeti

- Aktivna prijava u AWS Akademija kolegij
- Pristup AWS konzoli putem [AWS Academy portala](https://awsacademy.instructure.com/)
- Web preglednik i internet pristup

---

## 🧭 Koraci

### 1. Pristupi AWS konzoli

1. Otvori [AWS Academy portal](https://awsacademy.instructure.com/)
2. Prijavi se i uđi u svoj kolegij
3. Klikni na **Learner Lab**
4. Klikni na **AWS Console** kako bi se otvorila AWS konzola u novom prozoru

---

### 2. Pokreni EC2 instancu

1. U tražilicu (gore u konzoli) upiši `EC2` i klikni na prvi rezultat
2. Klikni **Launch instance**
3. Unesi ime instance (npr. `moja-prva-ec2`)
4. U dijelu “Amazon Machine Image (AMI)” odaberi:
   - **Amazon Linux 2 AMI (HVM), SSD Volume Type**
5. U dijelu “Instance type” odaberi:
   - **t2.micro** (besplatni sloj)
6. U sekciji “Key pair (login)”:
   - Ako već imaš ključ, koristi postojeći
   - Inače klikni **Create new key pair**
     - Unesi naziv (npr. `vuv-student`)
     - Format: **.pem**
     - Klikni **Create key pair** i **spremi** datoteku (`.pem`) na svoje računalo
7. Ostavi ostale postavke po zadanom
8. Klikni **Launch instance**

---

## ✅ Provjera instance

1. Klikni na **View all instances**
2. Pronađi svoju instancu (ime koje si upisao)
3. Provjeri da status kaže **running**
4. Kopiraj **Public IPv4 DNS** – to ćeš koristiti za spajanje putem PuTTY-a

---

➡️ Nastavi s [vodičem za povezivanje na instancu putem PuTTY-a](putty-povezivanje.md)

