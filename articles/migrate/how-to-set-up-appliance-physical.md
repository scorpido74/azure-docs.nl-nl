---
title: Een Azure Migrate apparaat instellen voor fysieke servers
description: Meer informatie over het instellen van een Azure Migrate apparaat voor de evaluatie van fysieke servers.
ms.service: azure-migrate
ms.topic: article
ms.date: 04/15/2020
ms.openlocfilehash: 6d9cc071ad5d81a09a14b12fe2acdf564c2ea6c8
ms.sourcegitcommit: 79508e58c1f5c58554378497150ffd757d183f30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84331777"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Een apparaat instellen voor fysieke servers

In dit artikel wordt beschreven hoe u het Azure Migrate apparaat instelt als u fysieke servers wilt beoordelen met het Azure Migrate: Server Assessment-hulp programma.

Het Azure Migrate apparaat is een licht gewicht apparaat dat door de Azure Migrate server-evaluatie wordt gebruikt om het volgende te doen:

- On-premises servers detecteren.
- Meta gegevens en prestatie gegevens voor gedetecteerde servers verzenden naar Azure Migrate server-evaluatie.

Meer [informatie](migrate-appliance.md) over het Azure migrate apparaat.


## <a name="appliance-deployment-steps"></a>Implementatie stappen voor het apparaat

Als u het apparaat wilt instellen, doet u het volgende:
- Down load een zip-bestand met Azure Migrate-installatie script uit de Azure Portal.
- Pak de inhoud uit het zip-bestand uit. Start de Power shell-console met beheerders bevoegdheden.
- Voer het Power shell-script uit om de web-app voor het toestel te starten.
- Configureer het apparaat voor de eerste keer en registreer het bij het Azure Migrate-project.

## <a name="download-the-installer-script"></a>Het installatie script downloaden

Down load het zip-bestand voor het apparaat.

1. In **migratie doelen**  >  **servers**  >  **Azure migrate: Server evaluatie**, klikt u op **detecteren**.
2. **Discover machines**  >  **Zijn uw machines**in Discover-computers gevirtualiseerde? Klik op **niet gevirtualiseerd/Overig**.
3. Klik op **downloaden** om het zip-bestand te downloaden.

    ![Virtuele machine downloaden](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>Beveiliging controleren

Controleer of het gecomprimeerde bestand is beveiligd, voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.
2. Voer de volgende opdracht uit om de hash voor het zip-bestand te genereren:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Voor beeld van gebruik voor open bare Cloud:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
    - Voor beeld van gebruik voor Government Cloud:```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5 ```
3.  Controleer de nieuwste versie van het apparaat en de hash-waarden:
 
    - Voor de open bare Cloud:

        **Scenario** | **Update*** | **Hash-waarde**
        --- | --- | ---
        Fysiek (63,1 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2105112) | 0a27adf13cc5755e4b23df0c05732c6ac08d1fe8850567cb57c9906fbc3b85a0

    - Voor Azure Government:

        **Scenario** | **Update*** | **Hash-waarde**
        --- | --- | ---
        Fysiek (63,1 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2120100&clcid=0x409) | 93dfef131026e70acdfad2769cd208ff745ab96a96f013cdf3f9e1e61c9b37e1


## <a name="run-the-azure-migrate-installer-script"></a>Het Azure Migrate-installatie script uitvoeren
Het installatie script doet het volgende:

- Installeert agents en een webtoepassing voor detectie en evaluatie van fysieke servers.
- Installeer Windows-rollen, waaronder Windows Activation service, IIS en Power shell ISE.
- Een herschrijf bare module van IIS downloaden en installeren. [Meer informatie](https://www.microsoft.com/download/details.aspx?id=7435).
- Hiermee werkt u een register sleutel (HKLM) bij met permanente instellings gegevens voor Azure Migrate.
- Maakt de volgende bestanden onder het pad:
    - **Configuratie bestanden**:%ProgramData%\Microsoft Azure\Config
    - **Logboek bestanden**:%ProgramData%\Microsoft Azure\Logs

Voer het script als volgt uit:

1. Pak het zip-bestand uit naar een map op de server die als host moet fungeren voor het apparaat.  Zorg ervoor dat u het script niet uitvoert op een machine op een bestaand Azure Migrate apparaat.
2. Start Power shell op de bovenstaande server met beheerders bevoegdheden (met verhoogde bevoegdheden).
3. Wijzig de Power shell-map in de map waarin de inhoud is geëxtraheerd uit het gedownloade zip-bestand.
4. Voer het script met de naam **AzureMigrateInstaller. ps1** uit door de volgende opdracht uit te voeren:

    - Voor de open bare Cloud:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 ```
    - Voor Azure Government:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```

    Met het script wordt de web-app voor het toestel gestart wanneer deze is voltooid.

Als u problemen ondervindt, kunt u de script Logboeken openen op C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>Time Stamp</em>. log voor het oplossen van problemen.



### <a name="verify-appliance-access-to-azure"></a>Toestel toegang tot Azure controleren

Zorg ervoor dat de virtuele machine van het apparaat verbinding kan maken met Azure-Url's voor [open bare](migrate-appliance.md#public-cloud-urls) en [overheids](migrate-appliance.md#government-cloud-urls) Clouds.

## <a name="configure-the-appliance"></a>Het apparaat configureren

Het apparaat voor de eerste keer instellen.

1. Open een browser op een computer die verbinding kan maken met de virtuele machine en open de URL van de Web-App van het apparaat: **https://*-apparaatnaam of IP-adres*: 44368**.

   U kunt de app ook openen vanaf het bureau blad door te klikken op de snelkoppeling naar de app.
2. Ga als volgt te werk in de web-app > vereisten in te **stellen**:
    - **Licentie**: accepteer de licentie voorwaarden en lees de informatie van derden.
    - **Connectiviteit**: de app controleert of de virtuele machine toegang heeft tot internet. Als de virtuele machine gebruikmaakt van een proxy:
        - Klik op **proxy-instellingen**en geef het proxy adres en de luister poort op in het formulier http://ProxyIPAddress of http://ProxyFQDN .
        - Geef referenties op als de proxy verificatie nodig heeft.
        - Alleen HTTP-proxy wordt ondersteund.
    - **Tijd synchronisatie**: tijd wordt gecontroleerd. De tijd op het apparaat moet zijn gesynchroniseerd met internet tijd zodat de machine detectie goed werkt.
    - **Updates installeren**: Azure migrate server beoordeling controleert of de meest recente updates zijn geïnstalleerd op het apparaat.

### <a name="register-the-appliance-with-azure-migrate"></a>Het apparaat registreren bij Azure Migrate

1. Klik op **Aanmelden**. Als deze niet wordt weer gegeven, controleert u of de pop-upblokkering in de browser is uitgeschakeld.
2. Meld u aan met uw Azure-referenties op het tabblad Nieuw.
    - Meld u aan met uw gebruikers naam en wacht woord.
    - Aanmelden met een pincode wordt niet ondersteund.
3. Nadat u zich hebt aangemeld, gaat u terug naar de web-app.
4. Selecteer het abonnement waarin het Azure Migrate-project is gemaakt. Selecteer vervolgens het project.
5. Geef een naam op voor het apparaat. De naam moet alfanumeriek zijn met 14 tekens of minder.
6. Klik op **Registreren**.


## <a name="start-continuous-discovery"></a>Continue detectie starten

Maak verbinding van het apparaat met fysieke servers en start de detectie.

1. Klik op **referenties toevoegen** om de account referenties op te geven die door het apparaat worden gebruikt om servers te detecteren.  
2. Geef het **besturings systeem**, een beschrijvende naam voor de referenties en de gebruikers naam en het wacht woord op. Klik vervolgens op **toevoegen**.
U kunt elk voor Windows-en Linux-servers één set referenties toevoegen.
4. Klik op **server toevoegen**en geef Server Details-FQDN/IP-adres en beschrijvende naam van referenties (één vermelding per rij) op om verbinding te maken met de server.
3. Klik op **Valideren**. Na validatie wordt de lijst met servers weer gegeven die kunnen worden gedetecteerd.
    - Als de validatie mislukt voor een server, controleert u de fout door de muis aanwijzer boven het pictogram in de kolom **status** te bewegen. Los problemen op en valideer opnieuw.
    - Als u een server wilt verwijderen, selecteert u > **verwijderen**.
4. Klik na validatie op **opslaan en start de detectie** om het detectie proces te starten.

De detectie wordt gestart. Het duurt ongeveer 15 minuten voor de meta gegevens van gedetecteerde Vm's die in de Azure Portal worden weer gegeven.

## <a name="verify-servers-in-the-portal"></a>Servers in de portal controleren

Nadat de detectie is voltooid, kunt u controleren of de servers worden weer gegeven in de portal.

1. Open het Azure Migrate dash board.
2. Klik op de pagina **Azure migrate servers**  >  **Azure migrate: Server beoordeling** op het pictogram met het aantal voor **gedetecteerde servers**.


## <a name="next-steps"></a>Volgende stappen

Probeer [fysieke servers te evalueren](tutorial-assess-physical.md) met Azure migrate server-evaluatie.
