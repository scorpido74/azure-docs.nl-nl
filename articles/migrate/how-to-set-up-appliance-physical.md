---
title: Een Azure Migrate-toestel instellen voor fysieke servers
description: Meer informatie over het instellen van een Azure Migrate appliance voor fysieke serverbeoordeling.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: b60a30e5e30ee81cbaca7d5e4691ccedac2462b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598167"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Een toestel instellen voor fysieke servers

In dit artikel wordt beschreven hoe u het Azure Migrate-toestel instelt als u fysieke servers beoordeelt met het hulpprogramma Azure Migrate: Server Assessment.

Het Azure Migrate-toestel is een lichtgewicht toestel dat door Azure Migrate Server Assessment als volgt wordt gebruikt:

- Ontdek on-premises servers.
- Metagegevens en prestatiegegevens voor gedetecteerde servers verzenden naar Azure Migrate Server Assessment.

[Meer informatie](migrate-appliance.md) over het Azure Migrate-toestel.


## <a name="appliance-deployment-steps"></a>Implementatiestappen voor toestel

Ga als u het apparaat instelt:
- Download een zip-bestand met Azure Migrate-installatiescript van de Azure-portal.
- Haal de inhoud uit het ritsbestand. Start de PowerShell-console met beheerdersbevoegdheden.
- Voer het PowerShell-script uit om de webtoepassing voor het toestel te starten.
- Configureer het toestel voor de eerste keer en registreer het met het Azure Migrate-project.

## <a name="download-the-installer-script"></a>Het installatiescript downloaden

Download het zip-bestand voor het apparaat.

1. Klik in **Migration Goals** > **Servers** > **Azure Migrate: Server assessment**op **Ontdekken**.
2. In **Machines** > ontdekken**Worden uw machines gevirtualiseerd?**, klikt u op **Niet gevirtualiseerd/Ander.**
3. Klik **op Downloaden** om het zip-bestand te downloaden.

    ![VM downloaden](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>Beveiliging verifiëren

Controleer of het zip-bestand veilig is, voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.
2. Voer de volgende opdracht uit om de hash voor de VHD te genereren
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gebruiksvoorbeeld: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  Voor de nieuwste toestelversie moet de gegenereerde hash overeenkomen met deze [instellingen.](https://docs.microsoft.com/azure/migrate/tutorial-assess-physical#verify-security)



## <a name="run-the-azure-migrate-installer-script"></a>Het installatiescript Azure Migrate uitvoeren
Het installatiescript doet het volgende:

- Installeert agents en een webtoepassing voor het detecteren en beoordelen van fysieke servers.
- Installeer Windows-rollen, waaronder Windows Activation Service, IIS en PowerShell ISE.
- Download en installeert een IIS herschrijfbare module. [Meer informatie](https://www.microsoft.com/download/details.aspx?id=7435).
- Werkt een registersleutel (HKLM) bij met permanente instellingsdetails voor Azure Migrate.
- Hiermee maakt u de volgende bestanden onder het pad:
    - **Config-bestanden**: %Programdata%\Microsoft Azure\Config
    - **Logboekbestanden**: %Programdata%\Microsoft Azure\Logs

Voer het script als volgt uit:

1. Haal het ritsbestand uit naar een map op de server die het toestel host.
2. Start PowerShell op de bovenstaande server met beheerdersbevoegdheden (verhoogde bevoegdheid).
3. Wijzig de PowerShell-map in de map waar de inhoud is geëxtraheerd uit het gedownloade zip-bestand.
4. Voer het script met de naam **AzureMigrateInstaller.ps1** uit door de volgende opdracht uit te voeren:
    ```
    PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1
    ```
Het script start de webtoepassing van het toestel wanneer deze is voltooid.

In het geval van problemen hebt u toegang tot de scriptlogboeken op C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log voor het oplossen van problemen.

> [!NOTE]
> Voer het azure migrate-installatiescript niet uit op een bestaand Azure Migrate-toestel.

### <a name="verify-appliance-access-to-azure"></a>De toegang tot het toestel tot Azure verifiëren

Controleer of de toestel-VM verbinding kan maken met de vereiste [Azure-URL's.](migrate-appliance.md#url-access)

## <a name="configure-the-appliance"></a>Het toestel configureren

Stel het apparaat voor de eerste keer in.

1. Open een browser op elke machine die verbinding kan maken met de VM en open de URL van de toestelweb-app: **https://*toestelnaam of IP-adres*: 44368**.

   U de app afwisselend vanaf het bureaublad openen door op de snelkoppeling van de app te klikken.
2. Ga als volgt te werk in de web-app > **Vereiste voorwaarden instellen:**
    - **Licentie:** Accepteer de licentievoorwaarden en lees de informatie van derden.
    - **Connectiviteit**: De app controleert of de VM toegang tot internet heeft. Als de VM een proxy gebruikt:
        - Klik **op Proxy-instellingen**en geef het proxyadres http://ProxyIPAddress http://ProxyFQDNen de luisterpoort op in het formulier of .
        - Geef referenties op als de proxy verificatie nodig heeft.
        - Alleen HTTP-proxy wordt ondersteund.
    - **Tijdsynchronisatie**: De tijd wordt geverifieerd. De tijd op het toestel moet synchroon lopen met de internettijd om vm-detectie goed te laten werken.
    - **Updates installeren:** Azure Migrate Server Assessment controleert of het toestel de nieuwste updates heeft geïnstalleerd.

### <a name="register-the-appliance-with-azure-migrate"></a>Het toestel registreren met Azure Migreren

1. Klik **op Aanmelden**. Als deze niet wordt weergegeven, controleert u of u de pop-upblokkering in de browser hebt uitgeschakeld.
2. Meld u op het nieuwe tabblad aan met uw Azure-referenties.
    - Meld u aan met uw gebruikersnaam en wachtwoord.
    - Aanmelden met een pincode wordt niet ondersteund.
3. Ga na het succesvol aanmelden terug naar de web-app.
4. Selecteer het abonnement waarin het Azure Migrate-project is gemaakt. Selecteer vervolgens het project.
5. Geef een naam op voor het toestel. De naam moet alfanumeriek zijn met 14 tekens of minder.
6. Klik **op Registreren**.


## <a name="start-continuous-discovery"></a>Continue detectie starten

Maak verbinding van het toestel naar fysieke servers en start de detectie.

1. Klik **op Referenties toevoegen** om de accountreferenties op te geven die het toestel zal gebruiken om servers te detecteren.  
2. Geef het **besturingssysteem**op, de vriendelijke naam voor de referenties, **gebruikersnaam** en **wachtwoord** en klik op **Toevoegen**.
U elk één set referenties toevoegen voor Windows- en Linux-servers.
4. Klik **op Server toevoegen**en geef servergegevens op: FQDN/IP-adres en een vriendelijke naam van referenties (één vermelding per rij) om verbinding te maken met de server.
3. Klik op **Valideren**. Na validatie wordt de lijst met servers die kunnen worden ontdekt weergegeven.
    - Als de validatie mislukt voor een server, controleert u de fout door boven het pictogram in de kolom **Status** te zweven. Los problemen op en valideer opnieuw.
    - Als u een server wilt verwijderen, selecteert u > **Verwijderen**.
4. Klik na validatie op **Opslaan en start detectie** om het detectieproces te starten.

Dit begint te ontdekken. Het duurt ongeveer 15 minuten voordat metagegevens van gedetecteerde VM's worden weergegeven in de Azure-portal.

## <a name="verify-servers-in-the-portal"></a>Servers in de portal verifiëren

Nadat de detectie is voltooid, u controleren of de servers in de portal worden weergegeven.

1. Open het Azure Migrate-dashboard.
2. Klik in **Azure Migrate - Servers** > **Azure Migrate: Server Assessment** pagina op het pictogram dat het aantal voor **gedetecteerde servers**weergeeft.


## <a name="next-steps"></a>Volgende stappen

Probeer [de beoordeling van fysieke servers](tutorial-assess-physical.md) met Azure Migrate Server Assessment uit.
