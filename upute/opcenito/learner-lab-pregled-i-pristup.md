# AWS Academy Learner Lab
AWS Academy Learner Lab pruža dugotrajno testno okruženje za istraživanje AWS usluga. Vaš nastavnik vam može dodijeliti određeni zadatak, ili možete raditi na vlastitom projektu.

## Uvod  
Ovaj vodič pruža upute za korištenje AWS Academy Learner Lab okruženja. Obuhvaća postavljanje i prijavu na AWS Academy račun, pristup AWS Academy Learner Labu te korištenje samog laboratorija. 

---

## Postavljanje i prijava na AWS Academy račun  
1. Za postavljanje svog AWS Academy računa, otvorite pozivnicu u e-pošti od AWS Academy (notifications@instructure.com) i odaberite **Get Started**.  
   **Napomena:** Ako ne vidite poruku e-pošte, provjerite mape **neželjene pošte**, **spam** ili **promotivne poruke**.  
2. Idite na: [https://www.awsacademy.com/LMS_Login](https://www.awsacademy.com/LMS_Login)  
3. Odaberite **Student Login** i prijavite se pomoću svoje adrese e-pošte i lozinke.  
   **Napomena:** Koristite adresu e-pošte na koju je stigla pozivnica iz AWS Academy.

---

## Pristup AWS Academy Learner Labu  
1. Na nadzornoj ploči LMS-a, odaberite laboratorij (Learner Lab) kojem želite pristupiti.  
   **Napomena:** Ako ste pozvani na više kolegija ili laboratorija, prikazat će vam se više pločica na nadzornoj ploči.  
2. U navigacijskom izborniku odaberite **Modules** (Moduli).  
3. Odaberite poveznicu laboratorija (Learner Lab).

---

## Korištenje AWS Academy Learner Laba  
1. Za pokretanje laboratorija, odaberite **Start Lab**.  
2. Kada se ikona kruga desno od AWS poveznice u gornjem lijevom kutu promijeni u **zelenu**, okruženje je spremno za korištenje.  
   Za pokretanje **AWS Management Console** u novoj kartici, odaberite poveznicu **AWS**.  
   Bit ćete prijavljeni u konzolu s privremenim AWS računom koji možete koristiti dok je aktivan mjerač vremena laboratorijske sesije. Sustav će spremiti vaš rad kada završite sesiju ili kada istekne vrijeme.  
   Alternativno, možete koristiti **terminal** u okviru s uputama za korištenje **AWS Command Line Interface (CLI)** za rad s privremenim računom.

3. Za pregled uputa laboratorija, vratite se u LMS i odaberite **Readme**.  
   Upute objašnjavaju kako pristupiti i koristiti svoj AWS račun, koje su AWS usluge dostupne te koja su ograničenja postavljena na pojedine usluge.

4. Za praćenje potrošnje, pogledajte gornji dio uputa laboratorija.  
   **Napomena:** Informacije se prikazuju putem **AWS Budgets** servisa i mogu kasniti do 8 sati. Prikaz potrošnje je okviran.  
   **Važno:** Ako potrošite cijeli budžet, izgubit ćete sav svoj rad, a sustav će deaktivirati vaš privremeni AWS račun.  
   Vaš budžet od **50 USD** obično je dovoljan za većinu akademskih projekata, pod uvjetom da se pridržavate smjernica potrošnje.

5. Za praćenje preostalog vremena sesije, pogledajte gornji dio uputa laboratorija.  
   **Napomena:** Ako aktivno radite i treba vam više vremena, odaberite ponovno **Start Lab** kako biste resetirali mjerač vremena.

6. Za brisanje svih resursa koje ste konfigurirali i početak s čistim AWS računom, odaberite **Reset**.  
   **Važno:** Svi podaci će biti izgubljeni i ne mogu se povratiti. Ova opcija ne resetira vaš budžet — dosadašnja potrošnja će se i dalje pratiti.

7. Kada završite sa sesijom, odaberite **End Lab**.  
   Svi pokrenuti **Amazon EC2** instance bit će zaustavljene. Ako se vratite i ponovno pokrenete laboratorij, zaustavljene EC2 instance bit će ponovno pokrenute, a ostali resursi koje ste postavili bit će i dalje dostupni.  
   **Važno:** Ako ste stvorili i pokrenuli druge AWS resurse poput **NAT gatewaya** ili **Amazon RDS** baze podataka, ti će resursi nastaviti s radom u pozadini i uzrokovati troškove, čak i izvan vremena vaše laboratorijske sesije.


