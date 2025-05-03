# Upute: Spajanje na EC2 instancu putem SSH-a

## Opis

Ove upute pokrivaju sve glavne metode spajanja na EC2 instancu u AWS Academy Learner Lab okruženju. Povezivanje je moguće putem SSH klijenta na Windowsu, macOS-u ili Linuxu, kao i putem ugrađenog Instance Connect preglednika.

## 1. Preduvjeti

Prije nego što se pokušate povezati, provjerite sljedeće:

* EC2 instanca je **u stanju Running**
* **Port 22 (SSH)** je otvoren u sigurnosnoj grupi instance
* Imate preuzetu SSH ključ datoteku (`.pem` ili `.ppk`)
* Znate **javnu IPv4 adresu** instance (vidljiva u EC2 konzoli pod opisom instance)
* Korisničko ime za Amazon Linux je `ec2-user`

> **Napomena:** Ako ponovno pokrenete (reboot) instancu, IP adresa ostaje ista. Ali ako ju **ugasite i ponovo pokrenete (stop/start)**, dobit će novu javnu IP adresu.

---

## 2. Windows – Korištenje PowerShell-a ili CMD (OpenSSH)

1. Otvorite **PowerShell** ili **Command Prompt**
2. Navigirajte do mape gdje je spremljen `.pem` ključ:

```powershell
cd Downloads
```

3. Pokrenite SSH naredbu:

```powershell
ssh -i .\naziv-kljuca.pem ec2-user@<javna-ip-adresa>
```

> Ako dobijete grešku o dozvolama, datoteka mora imati ograničen pristup.

### Kako riješiti grešku s dozvolama nad .pem datotekom (Windows)

Ako pri korištenju `.pem` datoteke dobijete grešku poput `Permissions are too open` ili `Unprotected key file`, potrebno je ograničiti pristup toj datoteci tako da je može koristiti samo vaš korisnički račun.

#### Koraci:

1. Desni klik na `.pem` datoteku > **Properties**
2. Otvorite karticu **Security** > kliknite **Advanced**
3. Kliknite **Change** pored polja **Owner**

   * Odaberite lokalno računalo (Locations), unesite svoje korisničko ime, kliknite **Check Names** i zatim **OK**
4. Kliknite **Disable inheritance** > **Remove all inherited permissions**
5. Kliknite **Add** > **Select a principal** > unesite svoje korisničko ime > **Check Names** > **OK**
6. Dodijelite si **Full Control** i potvrdite sve prozore klikom na **OK**

Nakon toga bi datoteka trebala imati ispravna prava pristupa i naredba za spajanje neće više prikazivati greške.

---

## 3. Windows – Korištenje PuTTY-a

### Priprema ključa (samo ako imate `.pem` datoteku):

1. Pokrenite **PuTTYgen**
2. Kliknite **Load** i odaberite `.pem` datoteku
3. Kliknite **Save private key** i spremite `.ppk` datoteku

### Postavljanje PuTTY sesije:

1. Pokrenite **PuTTY** (`putty.exe`)
2. U sekciji **Session**:

   * Host Name: `ec2-user@<javna-ip-adresa>`
   * Port: `22`
   * Connection type: `SSH`
3. U lijevom izborniku:

   * `Connection > SSH > Auth` → Kliknite **Browse** i pronađite `.ppk` datoteku
   * (Opcionalno) `Connection` → Set **Seconds between keepalives** na `30`
4. U **Session** sekciji:

   * Upišite naziv sesije (npr. `vuv-ec2`) i kliknite **Save**
5. Kliknite **Open** za povezivanje
6. Ako vas pita za potvrdu hosta, odaberite **Yes**

> **Napomena:** Svaki put kad promijenite IP adresu instance, ažurirajte Host Name i ponovno spremite sesiju.

---

## 4. macOS / Linux – Terminal

1. Spremite `.pem` datoteku u npr. `Downloads` direktorij
2. U terminalu:

```bash
chmod 400 ~/Downloads/ime_kljuca.pem
```

> Ova naredba postavlja ovlasti nad datotekom tako da je može čitati samo vlasnik – što je sigurnosni zahtjev za SSH ključeve.

3. Povežite se pomoću naredbe:

```bash
ssh -i ~/Downloads/ime_kljuca.pem ec2-user@<javna-ip-adresa>
```

> Ova naredba uspostavlja SSH vezu prema EC2 instanci koristeći privatni ključ i zadano korisničko ime `ec2-user`.

> Pri prvom spajanju unesite `yes` kad se zatraži potvrda autentičnosti.

---

## 5. Instance Connect (preglednik – alternativa bez ključa)

1. U AWS konzoli idite na **EC2 > Instances**
2. Označite vašu instancu i kliknite **Connect**
3. Odaberite **EC2 Instance Connect** i kliknite **Connect**

> Radi bez potrebe za SSH ključem, ali zahtijeva aktivan **public IP** i otvoren port 22.

---

## 6. Česta pitanja i rješavanje problema (troubleshooting)

| Problem                 | Mogući uzrok                      | Rješenje                                                |
| ----------------------- | --------------------------------- | ------------------------------------------------------- |
| „Permission denied“     | Pogrešan ključ, krivi korisnik    | Provjerite da koristite `ec2-user` i odgovarajući ključ |
| „Host not reachable“    | Port 22 nije otvoren              | Provjerite sigurnosnu grupu instance                    |
| Instance se ne vidi     | Nije u Running stanju             | Provjerite status u EC2 konzoli                         |
| `pem` ne radi u Windows | Ne podržava format                | Pretvorite u `ppk` pomoću PuTTYgen ili koristite WSL    |
| Promijenjena IP adresa  | Instanca zaustavljena i pokrenuta | Provjerite novu javnu IP adresu u EC2 konzoli           |

---

## 7. Završna napomena

Ako radite na više računala (npr. u učionici), preporučuje se sigurnosna pohrana vašeg SSH ključa (npr. u OneDrive povezan s vašim VUV Office računom).

Za svaki novi pokušaj povezivanja, provjerite IP adresu instance i ažurirajte postavke SSH klijenta.
