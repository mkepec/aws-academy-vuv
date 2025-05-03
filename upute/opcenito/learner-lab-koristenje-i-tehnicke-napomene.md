# AWS Academy Learner Lab – Korištenje i tehničke napomene

## Opis

Ove proširene upute namijenjene su studentima i nastavnicima koji koriste **AWS Academy Learner Lab** za izvođenje vježbi i projekata u AWS oblaku. Learner Lab omogućuje rad s ograničenim AWS okruženjem uz privremeni račun i definirani budžet.

## 1. Pregled okruženja

Learner Lab nudi izolirano AWS okruženje:

* Kada sesija završi (mjerač vremena dosegne 0:00), podaci i resursi ostaju sačuvani.
* Prilikom ponovnog pokretanja sesije (npr. sljedeći dan), resursi će biti ponovno dostupni.
* **EC2 instance** se automatski zaustavljaju i ponovno pokreću.
* **SageMaker notebook** instance se zaustavljaju, ali ih je potrebno ručno pokrenuti.
* **SageMaker Canvas** ostaje aktivan dok se ručno ne izbriše.

> **Važno:** Pratite svoj laboratorijski budžet. Ako premašite budžet, račun će biti deaktiviran i svi resursi izgubljeni.

## 2. Pokretanje i pristup

1. Odaberite **Start Lab** kako biste pokrenuli sesiju.
2. Kada se indikator u gornjem lijevom kutu promijeni u **zeleno**, kliknite **AWS** za otvaranje konzole u novoj kartici.
3. Ako koristite CLI/SDK, pogledajte niže navedene sekcije.

## 3. Navigacija i korisničko sučelje

* **Readme**: Prikaz uputa za rad.
* **AWS Details**: Prikaz tehničkih detalja o trenutnom računu.
* **Reset**: Brisanje svih resursa. Ne utječe na budžet.
* **Mjerač vremena**: Prikazuje trajanje sesije.

## 4. Ograničenja regije i servisa

### Podržane regije

* `us-east-1` (N. Virginia)
* `us-west-2` (Oregon)

> Pokušaj rada u drugim regijama može rezultirati greškom.

### Ograničenja servisa

* **EC2**: Maks. 9 aktivnih instanci, ukupno do 32 vCPU-a.
* **RDS**: Samo određeni tipovi, bez podrške za PIOPS.
* **IAM**: Nije moguće stvarati korisnike ili grupe (dostupna samo `LabRole`).

> **Savjet:** Prije korištenja servisa provjerite ograničenja kako biste izbjegli greške i prekoračenja.

## 5. Rad s terminalom i programatski pristup

### AWS CloudShell

* Dostupan iz gornje trake u konzoli.
* Unaprijed konfiguriran s AWS CLI i autentikacijom.

### EC2 Instance terminal

* Koristite **EC2 Instance Connect** ili SSH.
* Port 22 mora biti otvoren, a ključ preuzet i spreman (`.pem` ili `.ppk`).

### Pokretanje AWS CLI naredbi

```bash
aws ec2 describe-instances
```

### Python SDK (boto3) u CloudShellu

```python
import boto3
ec2 = boto3.client('ec2', region_name='us-east-1')
ec2.describe_regions()
```

## 6. Upravljanje budžetom

Ako premašite budžet:

* Račun se deaktivira.
* Svi resursi i podaci bit će trajno izgubljeni.

### Preporuke:

* Pokrećite samo nužne instance i servise.
* Isključujte instance kada nisu potrebne.
* Koristite **AWS Pricing Calculator** za planiranje.
* Pronađite aktivne resurse pomoću **Tag Editor** alata.

## 7. Povezivanje na EC2 instance putem SSH-a

### Windows korisnici (PuTTY):

1. Otvorite **PuTTY**.
2. Unesite javnu IP adresu instance.
3. U izborniku SSH > Auth odaberite `.ppk` ključ.
4. Korisničko ime: `ec2-user`

### Mac/Linux korisnici:

```bash
chmod 400 labsuser.pem
ssh -i labsuser.pem ec2-user@<javna-ip-adresa>
```

## 8. Završetak sesije

* Zaustavite instance koje više ne koristite.
* Odaberite **End Lab** za sigurno završavanje rada.

> Napomena: neki servisi (npr. NAT gateway, RDS) mogu ostati aktivni i nakon završetka sesije – obavezno ih ručno obrišite.

---

© 2025 Veleučilište u Virovitici / Amazon Web Services
