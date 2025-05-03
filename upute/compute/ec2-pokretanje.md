# Upute: Pokretanje EC2 instance u AWS Academy Learner Labu

## Opis

Ove upute namijenjene su studentima i nastavnicima Veleučilišta koji koriste **AWS Academy Learner Lab** za pokretanje vlastite EC2 instance. Pokrenuta instanca može se koristiti za testiranje, rad u terminalu i buduće laboratorijske vježbe.

## 1. Pristup AWS konzoli

Pristupite AWS konzoli putem Learner Laba. Ako vam je ovo prvi put, slijedite [upute za pristup](./opcenito/learner-lab-pregled-i-pristup.md).

> **Preporučena regija:** N. Virginia (`us-east-1`) ako nije već unaprijed postavljena.

## 2. Pokretanje nove EC2 instance

### Koraci:

1. U AWS konzoli kliknite **Services**, zatim **EC2**.
2. Na EC2 nadzornoj ploči kliknite **Launch instance**.
3. Unesite naziv instance, npr. `vuv-lab-ec2-1`.

> **Napomena:** Možete odabrati bilo koji naziv koji će vam kasnije pomoći da prepoznate instancu. Preporučuje se koristiti jasan i strukturiran naziv, npr. uključujući skraćenicu ustanove (npr. `vuv`), vrstu instance (`lab`, `web`, `test`), te broj ili godinu.

4. U dijelu **Application and OS Images (AMI)**:

   * Ostavite zadani izbor: **Amazon Linux 2023 AMI** (oznaka "Free tier eligible" se također prikazuje).

> **Napomena:** Amazon Linux koristi korisničko ime `ec2-user`. To će vam trebati kod povezivanja putem SSH-a.

5. U dijelu **Instance type**:

   * Ostavite zadani tip: `t2.micro` (Free tier eligible).

6. U dijelu **Key pair (login)**:

   * Ako vam je ovo prva instanca, odaberite **Create new key pair**.

     * Unesite naziv (npr. `student-key`).
     * Ostavite zadani **Key pair type**: `RSA`.
     * Odaberite **Private key file format**: `ppk` ako koristite Windows računala u laboratoriju, jer se koristi alat PuTTY.
       Ako ostavite zadani format `pem`, kasnije možete pomoću alata **PuTTYgen** pretvoriti `.pem` datoteku u `.ppk` format.
     * Kliknite **Create key pair** i spremite datoteku na sigurno mjesto – potrebna je za povezivanje na instancu.

> **Važno:** Ako u budućim vježbama budete koristili drugo računalo (npr. u laboratoriju), a nemate spremljen ključ, nećete se moći povezati na već pokrenutu instancu i morat ćete pokrenuti novu.
> Preporučuje se pohraniti `.pem` ili `.ppk` datoteku na trajno dostupno i sigurno mjesto, npr. u vlastiti **OneDrive** račun povezan s vašim VUV Office 365 računom.

* Ako već imate ključ koji ste ranije preuzeli, odaberite **Choose existing key pair** i izaberite ga s popisa.

7. U dijelu **Network settings**:

   * Odaberite opciju **Create security group**.
   * Unesite naziv grupe, npr. `vuv-lab-access`, te kratak opis (npr. "Sigurnosna pravila za laboratorijske instance").
   * Provjerite da je dodano pravilo za **SSH** (port 22, Anywhere – 0.0.0.0/0).

> **Napomena:** Kreiranjem vlastite sigurnosne grupe olakšat ćete kasnije dodavanje dodatnih pravila, npr. otvaranje porta 80 za web server. Sve sigurnosne grupe možete pronaći i uređivati putem **EC2 > Network & Security > Security Groups**.\*\* Ove vrijednosti su zadane u Learner Lab okruženju i dovoljne za potrebe rada u laboratoriju.

8. U dijelu **Configure storage**:

   * Ostavite zadanu vrijednost od **8 GiB**.

9. Kliknite **Launch instance**.

10. Nakon potvrde da je instanca uspješno pokrenuta, kliknite **View all instances**.

## 3. Provjera statusa instance

* Na popisu instanci provjerite da vaša instanca ima status **Running**.
* U stupcu **Status checks** pričekajte oznaku **2/2 checks passed**.
* Zabilježite **Public IPv4 address** – trebat će vam za povezivanje u sljedećim koracima.

## 4. Upravljanje instancom

U prikazu instance kliknite gumb **Instance state** gdje možete:

* **Start** – pokrenuti instancu ako je zaustavljena
* **Stop** – privremeno zaustaviti instancu (resursi se ne brišu)
* **Terminate** – trajno obrisati instancu i sve njene podatke

> **Preporuka:** Kada završite s radom, koristite **Stop** ako ćete se ponovno povezivati kasnije. Koristite **Terminate** samo kada vam instanca više neće trebati.

---

Za povezivanje na EC2 instancu korištenjem SSH-a na Windows, macOS i Linux sustavima, pogledajte:
👉 [Upute za povezivanje na EC2 instancu putem SSH-a](./ec2-spajanje-ssh.md)
