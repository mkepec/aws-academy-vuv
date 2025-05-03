# Upute: Pokretanje EC2 instance u AWS Academy Learner Labu

## Opis

Ove upute služe kao praktičan vodič za studente i nastavnike Veleučilišta koji koriste **AWS Academy Learner Lab** za pokretanje vlastitih EC2 instanci. Instanca se koristi kao virtualni poslužitelj u oblaku te može služiti kao zamjena za lokalne virtualne strojeve u laboratorijskim vježbama ili edukacijskim projektima.

> **Napomena:** Ova instanca pokreće se bez dodatnih konfiguracija ili softverskih instalacija. Cilj je imati čistu, aktivnu instancu spremnu za daljnji rad.

## Pristup AWS konzoli

Prije početka slijedite upute iz [uputa za AWS Academy Learner Lab](./opcenito/learner-lab-uputa.md) kako biste pristupili AWS konzoli s privremenim računom.

> **Regija:** Preporučena regija je **N. Virginia (us-east-1)** ako nije unaprijed zadana.

## Koraci za pokretanje EC2 instance

### 1. Otvorite EC2 servis

* U AWS konzoli kliknite **Services** i zatim **EC2**.

### 2. Pokrenite instancu

* Kliknite **Launch instance**.

### 3. Osnovne postavke

* **Name**: Unesite ime instance (npr. `MojaEC2Instanca`).
* **AMI**: Odaberite **Amazon Linux 2023 AMI (x86\_64)**.
* **Instance type**: Ostavite zadani `t2.micro`.

### 4. Ključ za pristup

* Pod **Key pair (login)** odaberite postojeći ključ ili kreirajte novi (npr. `student-key`).
* Preuzmite `.pem` (za Mac/Linux) ili `.ppk` (za Windows PuTTY) datoteku i pohranite je sigurno.

### 5. Mrežne postavke

* Kliknite **Edit** pored **Network settings**.
* Ostavite zadani VPC i subnet.
* Provjerite da je **Auto-assign public IP** omogućeno.
* U dijelu **Firewall (security group)**:

  * Odaberite **Create security group**.
  * Dodajte pravilo za SSH:

    * **Type**: SSH
    * **Port range**: 22
    * **Source**: Anywhere (0.0.0.0/0)

> **Napomena:** Pravilo pristupa iz bilo koje mreže koristi se samo u edukacijske svrhe.

### 6. Pohrana

* Ostavite zadani volumen (8 GiB).

### 7. Pokrenite instancu

* Kliknite **Launch instance**.
* Nakon pokretanja, kliknite **View all instances**.

## Provjera statusa

* Provjerite da je instanca u stanju `Running`.
* U stupcu **Status checks** mora pisati `2/2 checks passed`.
* Zabilježite **Public IPv4 address** – trebat će vam za povezivanje.

## Završetak rada

* Ako ne nastavljate odmah s radom, preporučuje se odabrati **Instance state > Stop instance**.
* Nakon završetka rada u konzoli, ne zaboravite kliknuti **End Lab** u AWS Academy sučelju kako biste zaustavili rad resursa i sačuvali budžet.

---

Ove upute su osmišljene za brzu orijentaciju i početak rada s EC2 servisom. Za detaljnije laboratorijske vježbe koristit će se strukturirani predložak s ciljevima, zadacima i evaluacijom.
