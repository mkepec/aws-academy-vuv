# Povezivanje na EC2 instancu putem PuTTY-a

Ovaj vodič opisuje kako se povezati na svoju AWS EC2 instancu s Windows računala pomoću alata **PuTTY**.

---

## ✅ Preduvjeti

- EC2 instanca mora biti **pokrenuta** i dostupna (status: running)
- `.pem` datoteka preuzeta prilikom stvaranja instance
- Instaliran **PuTTY** i **PuTTYgen**
  - Preuzmi s: [https://www.putty.org](https://www.putty.org)

---

## 🧭 Koraci

### 1. Pretvaranje .pem datoteke u .ppk (PuTTY Private Key)

1. Pokreni **PuTTYgen**
2. Klikni **Load** i odaberi `.pem` datoteku (postavi prikaz *All Files* ako se ne vidi)
3. Klikni **Save private key**
4. Spremi `.ppk` datoteku – koristiš ju za autentikaciju u PuTTY-u

---

### 2. Spajanje na instancu putem PuTTY-a

1. Pokreni **PuTTY**
2. U polje `Host Name (or IP address)` unesi:
   ```
   ec2-user@<tvoja-public-DNS-adresa>
   ```
   Primjer: `ec2-user@ec2-3-123-45-67.eu-central-1.compute.amazonaws.com`
3. U lijevom izborniku idi na:
   ```
   Connection > SSH > Auth
   ```
   i klikni **Browse...** kako bi učitao `.ppk` datoteku
4. Klikni **Open** za otvaranje SSH veze
5. Ako se pojavi sigurnosno upozorenje, potvrdi **Yes**

---

## 🟢 Uspješno povezivanje

Nakon spajanja, trebala bi se pojaviti naredbena linija (terminal) na tvojoj EC2 instanci, spremna za unos Linux naredbi.

Možeš sada:

- provjeriti status instance: `uptime`, `df -h`
- instalirati softver: `sudo yum install ...`
- testirati mrežu: `ping`, `curl`, `wget`

---

🎉 Čestitamo! Spojeni ste na svoju EC2 instancu putem PuTTY-a.

