# Laboratorijska vježba: Pokretanje osnovne EC2 instance u AWS Academy Learner Labu

## Pregled laboratorijske vježbe

Ova vježba vodi studente kroz proces pokretanja osnovne EC2 (Elastic Compute Cloud) instance u AWS Academy Learner Lab okruženju. Kreirana instanca može se koristiti za buduće vježbe poput instalacije softvera, testiranja mrežnih pravila i udaljenog pristupa putem SSH-a.

> **Napomena:** U ovoj vježbi instanca će biti pokrenuta bez dodatnih instalacija (bez User Data skripte). Cilj je pokrenuti „čistu“ virtualnu mašinu s Amazon Linux operativnim sustavom.

### Ciljevi

Nakon ove vježbe moći ćete:

* Pokrenuti EC2 instancu putem AWS konzole
* Odabrati Amazon Machine Image (AMI) i vrstu instance
* Konfigurirati sigurnosnu grupu s osnovnim pravilima pristupa
* Provjeriti status pokrenute instance

### Trajanje: \~30 minuta

---

## Priprema

Prije početka, provjerite jeste li uspješno pristupili AWS konzoli putem AWS Academy Learner Laba. Ako niste, slijedite upute u vodiču **AWS Academy Learner Lab Vodič**.

> **Preporučena regija:** N. Virginia (us-east-1) – ako nije već unaprijed postavljena, odaberite je ručno u gornjem desnom kutu AWS konzole.

---

## Zadatak 1: Pokretanje EC2 instance

### Koraci

1. U AWS konzoli kliknite **Services**, zatim pod **Compute** odaberite **EC2**.
2. Kliknite **Launch instance**.
3. U polju **Name and tags** unesite ime instance, npr. `MojaEC2Instanca`.
4. Pod **Application and OS Images (AMI)**:

   * Odaberite **Amazon Linux 2023 AMI (x86\_64)** – Free tier eligible.
5. Pod **Instance type**:

   * Ostavite zadani tip `t2.micro` (besplatni sloj – Free tier).
6. U sekciji **Key pair (login)**:

   * Odaberite postojeći key pair ako je već kreiran, ili kliknite **Create new key pair**.
   * Unesite naziv (npr. `student-key`) i spremite `.pem` ili `.ppk` datoteku na sigurno mjesto.
7. Kliknite **Edit** pod **Network settings**:

   * Ostavite zadane postavke za VPC i subnet.
   * Osigurajte da je **Auto-assign public IP** postavljeno na **Enable**.
8. Pod **Firewall (security group)**:

   * Odaberite **Create security group**.
   * Unesite naziv grupe (npr. `SSH-pristup`) i opis.
   * Uklonite postojeće pravilo i dodajte novo:

     * **Type:** SSH
     * **Protocol:** TCP
     * **Port Range:** 22
     * **Source:** Anywhere (0.0.0.0/0)

> **Upozorenje:** Pravilo pristupa s bilo koje adrese koristi se samo u edukativne svrhe. U stvarnim okruženjima preporuča se ograničiti pristup na određene IP adrese.

9. U sekciji **Configure storage** ostavite zadane vrijednosti (8 GiB root volumen).
10. Kliknite **Launch instance**.
11. Nakon prikaza poruke o uspješnom pokretanju, kliknite **View all instances**.

---

## Zadatak 2: Provjera statusa instance

1. Na popisu instanci pronađite instancu s nazivom koji ste unijeli.
2. Provjerite da je **Instance state** označen kao `Running`.
3. Provjerite da su **Status checks** prošli oba testa (`2/2 checks passed`).
4. Zabilježite **Public IPv4 address** – trebat će vam za povezivanje u sljedećoj vježbi.

> **Savjet:** Klikom na naziv instance otvorit će se detaljni prikaz s informacijama o instanci, sigurnosnim postavkama, mreži i dodijeljenim IP adresama.

---

## Završetak vježbe

Čestitamo! Uspješno ste pokrenuli EC2 instancu u AWS okruženju.

U sljedećoj vježbi naučit ćete kako se povezati na instancu koristeći SSH alat s vašeg računala.

Ne zaboravite:

* Ako ne planirate odmah nastaviti rad, možete kliknuti **Instance state > Stop instance**.
* Kada završite s radom u Learner Labu, odaberite **End Lab** kako biste zaustavili sve resurse i spriječili potrošnju budž
