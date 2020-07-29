---
title: Een Azure Migrate apparaat instellen voor fysieke servers
description: Meer informatie over het instellen van een Azure Migrate apparaat voor de evaluatie van fysieke servers.
ms.service: azure-migrate
ms.topic: article
ms.date: 04/15/2020
ms.openlocfilehash: 6d9cc071ad5d81a09a14b12fe2acdf564c2ea6c8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84331777"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Een apparaat instellen voor fysieke servers

In dit artikel wordt beschreven hoe u het Azure Migrate apparaat instelt als u fysieke servers wilt beoordelen met het Azure Migrate: Server Assessment-hulp programma.

Het Azure Migrate apparaat is een licht gewicht apparaat dat door de Azure Migrate server-evaluatie wordt gebruikt om het volgende te doen:

- On-premises servers detecteren.
- Meta gegevens en prestatie gegevens voor gedetecteerde servers verzenden naar Azure Migrate server-evaluatie.

Meer [informatie](migrate-appliance.md) over het Azure migrate apparaat.


## <a name="appliance-deployment-steps"></a>Implementatie stappen voor het apparaat

Om het apparaat in te stellen, moet u het volgende doen:
- Download een zip-bestand met Azure Migrate-installatiescript uit de Azure-portal.
- Pak de inhoud uit het zip-bestand uit. Start PowerShell-console met beheerdersbevoegdheden.
- Voer het PowerShell-script uit om de webtoepassing voor het apparaat te starten.
- Configureer het apparaat voor het eerst en registreer het bij het Azure Migrate-project.

## <a name="download-the-installer-script"></a>Download het installatiescript

Download het zip-bestand voor het apparaat.

1. In **Migratiedoelen** > **Servers** > **Azure Migrate: Serverevaluatie** klikt u op **Ontdekken**.
2. In **Machines ontdekken** > **Zijn uw machines gevirtualiseerd?** selecteert u **Niet gevirtualiseerd/Anders**.
3. Klik op **Downloaden** om het zip-bestand te downloaden.

    ![VM downloaden](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>Beveiliging controleren

Controleer of het zip-bestand veilig is voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.
2. Gebruik de volgende opdracht om de hash voor het zip-bestand te genereren:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Voorbeeld van gebruik voor openbare cloud: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
    - Voorbeeld van gebruik voor overheidscloud: ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5 ```
3.  Controleer de nieuwste versie van het apparaat en de hash-waarden:
 
    - Voor de openbare cloud:

        **Scenario** | **Downloaden*** | **Hash-waarde**
        --- | --- | ---
        Fysiek (63.1 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2105112) | 0a27adf13cc5755e4b23df0c05732c6ac08d1fe8850567cb57c9906fbc3b85a0

    - Voor Azure Government:

        **Scenario** | **Downloaden*** | **Hash-waarde**
        --- | --- | ---
        Fysiek (63.1 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2120100&clcid=0x409) | 93dfef131026e70acdfad2769cd208ff745ab96a96f013cdf3f9e1e61c9b37e1


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

    - Voor de openbare cloud: ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 ```
    - Voor Azure Government: ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```

    Met het script wordt de webtoepassing voor het apparaat gestart wanneer deze succesvol is voltooid.

Als u problemen ondervindt, kunt u het script Logboeken openen op C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>Timestamp</em>.log voor probleemoplossing.



### <a name="verify-appliance-access-to-azure"></a>Apparaattoegang tot Azure controleren

Zorg ervoor dat de apparaat-VM verbinding kan maken met Azure-URL's voor [openbare](migrate-appliance.md#public-cloud-urls) en [overheids](migrate-appliance.md#government-cloud-urls)clouds.

## <a name="configure-the-appliance"></a>Het apparaat configureren

Het apparaat voor de eerste keer instellen.

1. Open een browser op een computer die verbinding kan maken met de VM en open de URL van de web-app van het apparaat: **https://*apparaatnaam of IP-adres*: 44368**.

   U kunt de app ook openen vanaf het bureaublad door te klikken op de snelkoppeling naar de app.
2. Ga als volgt te werk in de web-app > **Vereisten instellen**:
    - **Licentie**: Accepteer de licentievoorwaarden en lees de informatie van derden.
    - **Connectiviteit**: de app controleert of de virtuele machine toegang heeft tot internet. Als de virtuele machine een proxy gebruikt:
        - Klik op **Proxyinstellingen** en geef het proxyadres en de controlepoort op in het formulier http://ProxyIPAddress of http://ProxyFQDN.
        - Geef referenties op als de proxy verificatie nodig heeft.
        - Alleen HTTP-proxy wordt ondersteund.
    - **Tijdsynchronisatie**: Tijd is geverifieerd. De tijd op het apparaat moet zijn gesynchroniseerd met internettijd voor VM zodat detectie goed werkt.
    - **Updates installeren**: Azure Migrate-serverevaluatie controleert of de meest recente updates zijn geïnstalleerd op het apparaat.

### <a name="register-the-appliance-with-azure-migrate"></a>Het apparaat registreren bij Azure Migrate

1. Klik op **Aanmelden**. Als dit niet wordt weergegeven, controleert u of de pop-upblokkering in de browser is uitgeschakeld.
2. Meld u aan met uw Azure-referenties op het nieuwe tabblad.
    - Meld u aan met uw gebruikersnaam en wachtwoord.
    - Aanmelden met een pincode wordt niet ondersteund.
3. Nadat u zich hebt aangemeld, gaat u terug naar de web-app.
4. Selecteer het abonnement waarin het Azure Migrate-project is gemaakt. Selecteer vervolgens het project.
5. Geef een naam op voor het apparaat. De naam moet alfanumeriek zijn met 14 tekens of minder.
6. Klik op **Registreren**.


## <a name="start-continuous-discovery"></a>Continue detectie starten

Maak verbinding van het apparaat met fysieke servers en start de detectie.

1. Klik op **Referenties toevoegen** om de accountreferenties op te geven die door het apparaat worden gebruikt om servers te detecteren.  
2. Geef het **besturingssysteem**, een beschrijvende naam voor de referenties en de gebruikersnaam en het wachtwoord op. Klik vervolgens op **Toevoegen**.
U kunt voor Windows-en Linux-servers één set referenties toevoegen.
4. Klik op **Server toevoegen** en geef serverdetails-FQDN/IP-adres en beschrijvende naam van referenties (één vermelding per rij) op om verbinding te maken met de server.
3. Klik op **Valideren**. Na validatie wordt de lijst met servers weergegeven die kunnen worden gedetecteerd.
    - Als de validatie mislukt voor een server, raadpleegt u de fout door te klikken op het pictogram in de kolom **Status**. Los problemen op en valideer opnieuw.
    - Als u een server wilt verwijderen, selecteert u > **Verwijderen**.
4. Klik na validatie op **Opslaan en detectie starten** om het detectieproces te starten.

De detectie wordt gestart. Het duurt ongeveer 15 minuten voor de meta gegevens van gedetecteerde Vm's die in de Azure Portal worden weer gegeven.

## <a name="verify-servers-in-the-portal"></a>Verifieer servers in de portal

Nadat de detectie is voltooid, kunt u controleren of de servers worden weer gegeven in de portal.

1. Open het Azure Migrate-dashboard.
2. Klik op de pagina **Azure Migrate - Servers** > **Azure Migrate: Serverevaluatie** op het pictogram dat het aantal voor **Gedetecteerde servers** weergeeft.


## <a name="next-steps"></a>Volgende stappen

Probeer [fysieke servers te evalueren](tutorial-assess-physical.md) met Azure migrate server-evaluatie.
