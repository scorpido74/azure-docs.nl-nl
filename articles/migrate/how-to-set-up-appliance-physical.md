---
title: Een Azure Migrate apparaat instellen voor fysieke servers
description: Meer informatie over het instellen van een Azure Migrate apparaat voor de evaluatie van fysieke servers.
ms.service: azure-migrate
ms.topic: article
ms.date: 04/15/2020
ms.openlocfilehash: 1b4e875a81c92f74cd7d2db96cf1c313157297eb
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88923543"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Een apparaat instellen voor fysieke servers

In dit artikel wordt beschreven hoe u het Azure Migrate apparaat instelt als u fysieke servers wilt beoordelen met het Azure Migrate: Server Assessment-hulp programma.

Het Azure Migrate apparaat is een licht gewicht apparaat dat door de Azure Migrate server-evaluatie wordt gebruikt om het volgende te doen:

- On-premises servers detecteren.
- Meta gegevens en prestatie gegevens voor gedetecteerde servers verzenden naar Azure Migrate server-evaluatie.

Meer [informatie](migrate-appliance.md) over het Azure migrate apparaat.


## <a name="appliance-deployment-steps"></a>Implementatie stappen voor het apparaat

Om het apparaat in te stellen, moet u het volgende doen:
- Geef een naam op voor het apparaat en Genereer een Azure Migrate project sleutel in de portal.
- Download een zip-bestand met Azure Migrate-installatiescript uit de Azure-portal.
- Pak de inhoud uit het zip-bestand uit. Start PowerShell-console met beheerdersbevoegdheden.
- Voer het PowerShell-script uit om de webtoepassing voor het apparaat te starten.
- Configureer het apparaat voor de eerste keer en registreer het met het Azure Migrate project met behulp van de Azure Migrate-project sleutel.

### <a name="generate-the-azure-migrate-project-key"></a>De Azure Migrate project sleutel genereren

1. In **Migratiedoelen** > **Servers** > **Azure Migrate: Serverevaluatie** selecteert u **Detecteren**.
2. In **Discover-machines**  >  **zijn uw machines gevirtualiseerd?**, selecteert u **fysiek of ander (AWS, GCP, xen, enzovoort)**.
3. Geef in **1: Azure migrate project sleutel genereren**een naam op voor het Azure migrate apparaat dat u wilt instellen voor de detectie van fysieke of virtuele servers. De naam moet alfanumeriek zijn met 14 tekens of minder.
1. Klik op **sleutel genereren** om te beginnen met het maken van de vereiste Azure-resources. Sluit de pagina computers detecteren niet af tijdens het maken van resources.
1. Nadat het maken van de Azure-resources is voltooid, wordt er een **Azure migrate project sleutel** gegenereerd.
1. Kopieer de sleutel, omdat u deze nodig hebt om de registratie van het apparaat tijdens de configuratie te volt ooien.

### <a name="download-the-installer-script"></a>Download het installatiescript

In **2: down load Azure migrate apparaat**en klik op **downloaden**.

   ![Selecties voor discover-computers](./media/tutorial-assess-physical/servers-discover.png)


   ![Selecties voor sleutel genereren](./media/tutorial-assess-physical/generate-key-physical.png)

### <a name="verify-security"></a>Beveiliging controleren

Controleer of het zip-bestand veilig is voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.
2. Gebruik de volgende opdracht om de hash voor het zip-bestand te genereren:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Voorbeeld van gebruik voor openbare cloud: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - Voorbeeld van gebruik voor overheidscloud: ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5 ```
3.  Controleer de nieuwste versie van het apparaat en de [instellingen](./tutorial-assess-physical.md#verify-security)van de hash-waarden.
 

## <a name="run-the-azure-migrate-installer-script"></a>Het Azure Migrate-installatiescript uitvoeren
Het installatiescript doet het volgende:

- Installeert agents en een webtoepassing voor detectie en evaluatie van fysieke servers.
- Installeer Windows-rollen, waaronder Windows-activeringsservice, IIS en PowerShell ISE.
- Een herschrijfbare module van IIS downloaden en installeren. [Meer informatie](https://www.microsoft.com/download/details.aspx?id=7435).
- Hiermee werkt u een registersleutel (HKLM) bij met permanente instellingsgegevens voor Azure Migrate.
- Maakt de volgende bestanden onder het pad:
    - **Configuratiebestanden**: %Programdata%\Microsoft Azure\Config
    - **Logboekbestanden**: %Programdata%\Microsoft Azure\Logs

Voer het script als volgt uit:

1. Pak het zip-bestand uit naar een map op de server die als host moet fungeren voor het apparaat.  Zorg ervoor dat u het script niet uitvoert op een machine op een bestaand Azure Migrate-apparaat.
2. Start PowerShell op de bovenstaande server met beheerdersbevoegdheden (verhoogde bevoegdheden).
3. Wijzig de PowerShell-map in de map waarin de inhoud is geëxtraheerd uit het gedownloade zip-bestand.
4. Voer de volgende opdracht uit om het script uit te voeren met de naam **AzureMigrateInstaller.ps1**:

    - Voor de openbare cloud: 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```
    - Voor Azure Government: 
    
        ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```

    Met het script wordt de webtoepassing voor het apparaat gestart wanneer deze succesvol is voltooid.

Als u problemen ondervindt, kunt u het script Logboeken openen op C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>Timestamp</em>.log voor probleemoplossing.



### <a name="verify-appliance-access-to-azure"></a>Apparaattoegang tot Azure controleren

Zorg ervoor dat de apparaat-VM verbinding kan maken met Azure-URL's voor [openbare](migrate-appliance.md#public-cloud-urls) en [overheids](migrate-appliance.md#government-cloud-urls)clouds.

### <a name="configure-the-appliance"></a>Het apparaat configureren

Het apparaat voor de eerste keer instellen.

1. Open een browser op een computer die verbinding kan maken met het apparaat en open de URL van de web-app van het apparaat: **https://*apparaatnaam of IP-adres*: 44368**.

   U kunt de app ook openen vanaf het bureaublad door te klikken op de snelkoppeling naar de app.
2. Ga akkoord met de **licentie voorwaarden**en lees de informatie van derden.
1. Ga als volgt te werk in de web-app > **Vereisten instellen**:
    - **Connectiviteit**: De app controleert of de server toegang heeft tot internet. Als de server gebruikmaakt van een proxy:
        - Klik op **proxy instellen** en geef het proxy adres op (in de vorm http://ProxyIPAddress of http://ProxyFQDN) en luister poort.
        - Geef referenties op als de proxy verificatie nodig heeft.
        - Alleen HTTP-proxy wordt ondersteund.
        - Als u proxy Details hebt toegevoegd of de proxy en/of de verificatie hebt uitgeschakeld, klikt u op **Opslaan** om de connectiviteits controle opnieuw te activeren.
    - **Tijdsynchronisatie**: Tijd is geverifieerd. De tijd op het apparaat moet zijn gesynchroniseerd met internettijd zodat serverdetectie goed werkt.
    - **Updates installeren**: Azure migrate server beoordeling controleert of de meest recente updates zijn geïnstalleerd op het apparaat. Nadat de controle is voltooid, kunt u op **apparaatdiensten weer geven** klikken om de status en versies van de onderdelen die op het apparaat worden uitgevoerd te bekijken.

### <a name="register-the-appliance-with-azure-migrate"></a>Het apparaat registreren bij Azure Migrate

1. Plak de **Azure migrate project sleutel** die u hebt gekopieerd uit de portal. Als u de sleutel niet hebt, gaat u naar **Server Assessment> Discover> bestaande apparaten beheren**, selecteert u de naam van het apparaat dat u hebt ingevoerd op het moment van sleutel genereren en kopieert u de bijbehorende sleutel.
1. Klik op **Aanmelden**. Er wordt een Azure-aanmeldings prompt geopend in een nieuw browser tabblad. Als deze niet wordt weer gegeven, controleert u of de pop-upblokkering in de browser is uitgeschakeld.
1. Meld u op het nieuwe tabblad aan met de gebruikersnaam en het wachtwoord van Azure.
   
   Aanmelden met een pincode wordt niet ondersteund.
3. Nadat u zich hebt aangemeld, gaat u terug naar de web-app. 
4. Als het Azure-gebruikers account dat wordt gebruikt voor logboek registratie over de juiste [machtigingen](tutorial-prepare-physical.md) beschikt voor de Azure-resources die zijn gemaakt tijdens het genereren van sleutels, wordt de registratie van het apparaat gestart.
1. Nadat het apparaat is geregistreerd, kunt u de registratie details zien door te klikken op **Details weer geven**.


## <a name="start-continuous-discovery"></a>Continue detectie starten

Maak nu verbinding vanaf het apparaat met de fysieke servers die moeten worden gedetecteerd en start de detectie.

1. In **stap 1: referenties opgeven voor de detectie van fysieke of virtuele Windows-servers**, klikt u **op referenties toevoegen** om een beschrijvende naam op te geven voor referenties, **gebruikers naam** en **wacht woord** voor een Windows-of Linux-server toevoegen. Klik op **Opslaan**.
1. Als u meerdere referenties tegelijk wilt toevoegen, klikt u op **meer toevoegen** om de referenties op te slaan en toe te voegen. Er worden meerdere referenties ondersteund voor detectie van fysieke servers.
1. In **stap 2: Geef fysieke of virtuele server details**op, klik op **detectie bron toevoegen** om het **IP-adres/de FQDN** van de server en de beschrijvende naam op te geven voor de referenties om verbinding te maken met de server.
1. U kunt **één item tegelijk toevoegen** of **meerdere items** in één keer toevoegen. Er is ook een optie voor het opgeven van Server Details via het **importeren van CSV**.

    ![Selecties voor het toevoegen van de detectie bron](./media/tutorial-assess-physical/add-discovery-source-physical.png)

    - Als u **één item toevoegen**kiest, kunt u het type besturings systeem kiezen, beschrijvende naam voor referenties opgeven, server **-IP-adres/FQDN** toevoegen en op **Opslaan**klikken.
    - Als u **meerdere items toevoegen**hebt gekozen, kunt u meerdere records tegelijk toevoegen door het **IP-adres/de FQDN** van de server op te geven met de beschrijvende naam voor de referenties in het tekstvak. **Controleer** de toegevoegde records en klik op **Opslaan**.
    - Als u **CSV importeren** _(standaard geselecteerd)_ kiest, kunt u een CSV-sjabloon bestand downloaden, het bestand met het **IP-adres/de FQDN** van de server en de beschrijvende naam voor referenties. Vervolgens importeert u het bestand in het apparaat, **controleert** u de records in het bestand en klikt u op **Opslaan**.

1. Wanneer u op Opslaan klikt, probeert het apparaat de verbinding te valideren met de toegevoegde servers en wordt de **validatie status** weer gegeven in de tabel voor elke server.
    - Als de validatie mislukt voor een server, raadpleegt u de fout door te klikken op **validatie is mislukt** in de kolom Status van de tabel. Los het probleem op en valideer het opnieuw.
    - Als u een server wilt verwijderen, klikt u op **verwijderen**.
1. U kunt de verbinding met servers op elk gewenst moment opnieuw **valideren** voordat de detectie wordt gestart.
1. Klik op **detectie starten**om de detectie van de gevalideerde servers te laten slagen. Nadat de detectie is gestart, kunt u de detectie status controleren op elke server in de tabel.


De detectie wordt gestart. Het duurt ongeveer 2 minuten per server voor de meta gegevens van de gedetecteerde server die in de Azure Portal worden weer gegeven.

## <a name="verify-servers-in-the-portal"></a>Verifieer servers in de portal

Nadat de detectie is voltooid, kunt u controleren of de servers worden weer gegeven in de portal.

1. Open het Azure Migrate-dashboard.
2. Klik op de pagina **Azure Migrate - Servers** > **Azure Migrate: Serverevaluatie** op het pictogram dat het aantal voor **Gedetecteerde servers** weergeeft.


## <a name="next-steps"></a>Volgende stappen

Probeer [fysieke servers te evalueren](tutorial-assess-physical.md) met Azure migrate server-evaluatie.
