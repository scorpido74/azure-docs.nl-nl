---
title: Problemen met de implementatie en detectie van Azure Migrate appliance oplossen
description: Hulp bij het implementeren van het Azure Migrate-toestel en het ontdekken van machines.
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 9fbf55fbe16d958bf10541894159dade26668bef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336727"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Problemen met het Azure Migrate-toestel en de detectie oplossen

Met dit artikel u problemen oplossen bij het implementeren van het [Azure Migrate-toestel](migrate-services-overview.md) en het apparaat gebruiken om on-premises machines te detecteren.


## <a name="whats-supported"></a>Wat wordt er ondersteund?

[Bekijk](migrate-appliance.md) de ondersteuningsvereisten van het apparaat.


## <a name="invalid-ovf-manifest-entry"></a>"Ongeldig OVF manifest entry"

Als u de fout 'Het opgegeven manifestbestand ongeldig is: ongeldig OVF-manifest' ontvangt, gaat u als volgt te werk:

1. Controleer of het OVA-bestand azure migrate appliance correct is gedownload door de hashwaarde te controleren. [Meer informatie](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). Als de hashwaarde niet overeenkomt, downloadt u het OVA-bestand opnieuw en probeert u de implementatie opnieuw.
2. Als de implementatie nog steeds mislukt en u de VMware vSphere-client gebruikt om het OVF-bestand te implementeren, probeert u het te implementeren via de vSphere-webclient. Als de implementatie nog steeds mislukt, probeert u een andere webbrowser te gebruiken.
3. Als u de vSphere-webclient gebruikt en deze probeert te implementeren op vCenter Server 6.5 of 6.7, probeert u de OVA rechtstreeks op de ESXi-host te implementeren:
   - Maak rechtstreeks verbinding met de ESXi-host (in plaats van vCenter Server) met de webclient (https://<*host IP-adres*>/ui).
   - Selecteer**OVF-sjabloon** **Bestand** > implementeren in De**voorraad van** **start.** >  Blader naar de OVA en voltooi de implementatie.
4. Als de implementatie nog steeds mislukt, neemt u contact op met azure Migrate-ondersteuning.

## <a name="cant-connect-to-the-internet"></a>Kan geen verbinding maken met het internet

Dit kan gebeuren als de machine achter een proxy zit.

- Zorg ervoor dat u de autorisatiereferenties opgeeft als de proxy deze nodig heeft.
- Als u een op URL gebaseerde firewallproxy gebruikt om uitgaande connectiviteit te beheren, voegt u [deze URL's](migrate-appliance.md#url-access) toe aan een lijst met toegestane instellingen.
- Als u een onderscheppende proxy gebruikt om verbinding te maken met internet, importeert u het proxycertificaat in de toestel-VM met behulp van [deze stappen.](https://docs.microsoft.com/azure/migrate/concepts-collector)

##  <a name="datetime-synchronization-error"></a>Datum-/tijdsynchronisatiefout

Een fout over datum- en tijdsynchronisatie (802) geeft aan dat de serverklok mogelijk langer dan vijf minuten niet meer synchroon is met de huidige tijd. Wijzig de kloktijd op de verzamelvm om overeen te komen met de huidige tijd:

1. Open een opdrachtprompt voor beheerders op de vm.
2. Voer **w32tm/tz uit**om de tijdzone te controleren.
3. Voer **w32tm /resync**uit om de tijd te synchroniseren.


## <a name="unabletoconnecttoserver"></a>"Kan niet verbinding makenmetserver"

Als u deze verbindingsfout opneemt, u mogelijk geen verbinding maken met vCenter Server *Servername*.com:9443. De foutdetails geven aan dat er `https://\*servername*.com:9443/sdk` geen eindpunt is waarop het bericht kan worden geaccepteerd.

- Controleer of u de nieuwste versie van het apparaat gebruikt. Als u dit niet bent, u het toestel upgraden naar de [nieuwste versie.](https://docs.microsoft.com/azure/migrate/concepts-collector)
- Als het probleem zich nog steeds voordoet in de nieuwste versie, kan het toestel de opgegeven vCenter Server-naam mogelijk niet oplossen of is de opgegeven poort mogelijk verkeerd. Als de poort niet is opgegeven, probeert de verzamelaar standaard verbinding te maken met poortnummer 443.

    1. Ping *Servername*.com van het toestel.
    2. Als stap 1 mislukt, probeert u verbinding te maken met de vCenter-server via het IP-adres.
    3. Identificeer het juiste poortnummer om verbinding te maken met vCenter Server.
    4. Controleer of vCenter Server actief is.


## <a name="error-6005260039-appliance-might-not-be-registered"></a>Fout 60052/60039: Toestel mogelijk niet geregistreerd

- Fout 60052, 'Het toestel is mogelijk niet geregistreerd bij het Azure Migrate-project' treedt op als het Azure-account dat wordt gebruikt om het toestel te registreren onvoldoende machtigingen heeft.
    - Zorg ervoor dat het Azure-gebruikersaccount dat wordt gebruikt om het toestel te registreren ten minste inzendermachtigingen op het abonnement heeft.
    - [Meer informatie](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) over vereiste Azure-rollen en -machtigingen.
- Fout 60039, 'Het toestel is mogelijk niet geregistreerd bij het Azure Migrate-project' kan optreden als de registratie mislukt omdat het Azure Migrate-project dat wordt gebruikt voor het register het toestel niet kan worden gevonden.
    - Controleer in de Azure-portal of het project bestaat in de brongroep.
    - Als het project niet bestaat, maakt u een nieuw Azure Migrate-project in uw resourcegroep en registreert u het toestel opnieuw. [Meer informatie over het](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) maken van een nieuw project.

## <a name="error-6003060031-key-vault-management-operation-failed"></a>Fout 60030/60031: Key Vault-beheerbewerking is mislukt

Als u de fout 60030 of 60031, 'Een Azure Key Vault-beheerbewerking is mislukt' ontvangt, gaat u als volgt te werk:
- Zorg ervoor dat het Azure-gebruikersaccount dat wordt gebruikt om het toestel te registreren ten minste inzendermachtigingen voor het abonnement heeft.
- Controleer of het account toegang heeft tot de sleutelkluis die is opgegeven in het foutbericht en probeer de bewerking opnieuw.
- Als het probleem zich blijft voordoen, neemt u contact op met Microsoft-ondersteuning.
- [Meer informatie](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) over de vereiste Azure-rollen en -machtigingen.

## <a name="error-60028-discovery-couldnt-be-initiated"></a>Fout 60028: Detectie kan niet worden gestart

Fout 60028: "Detectie kon niet worden gestart vanwege een fout. De bewerking is mislukt voor de opgegeven lijst met hosts of clusters" geeft aan dat detectie niet kon worden gestart op de hosts die in de fout worden vermeld vanwege een probleem bij het openen of ophalen van VM-informatie. De rest van de hosts zijn met succes toegevoegd.

- Voeg de hosts die in de fout worden vermeld opnieuw toe met de optie **Host toevoegen.**
- Als er een validatiefout optreedt, controleert u de herstelrichtlijnen om de fouten op te lossen en probeert u de optie **Detectie opslaan en opnieuw starten.**

## <a name="error-60025-azure-ad-operation-failed"></a>Fout 60025: Azure AD-bewerking is mislukt 
Fout 60025: "Een AD-bewerking van Azure is mislukt. De fout is opgetreden tijdens het maken of bijwerken van de Azure AD-toepassing" treedt op wanneer het Azure-gebruikersaccount dat wordt gebruikt om de detectie te starten, verschilt van het account dat wordt gebruikt om het toestel te registreren. Voer een van de volgende handelingen uit:

- Controleer of het gebruikersaccount dat de detectie initieert, hetzelfde is als het account dat wordt gebruikt om het toestel te registreren.
- Geef Azure Active Directory-toepassingstoegangsmachtigingen op aan het gebruikersaccount waarvoor de detectiebewerking mislukt.
- Verwijder de resourcegroep die eerder is gemaakt voor het Azure Migrate-project. Maak een andere resourcegroep om opnieuw te beginnen.
- [Meer informatie](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) over Azure Active Directory-toepassingsmachtigingen.


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>Fout 50004: kan geen verbinding maken met host of cluster

Fout 50004: "Kan geen verbinding maken met een host of cluster omdat de servernaam niet kan worden opgelost. WinRM-foutcode: 0x803381B9" kan optreden als de Azure DNS-service voor het toestel de opgegeven cluster- of hostnaam niet kan oplossen.

- Als u deze fout in het cluster ziet, clustert u FQDN.
- Mogelijk ziet u deze fout ook voor hosts in een cluster. Dit geeft aan dat het toestel verbinding kan maken met het cluster, maar het cluster retourneert hostnamen die geen FQDN's zijn. Als u deze fout wilt oplossen, werkt u het hosts-bestand op het toestel bij door een toewijzing van het IP-adres en de hostnamen toe te voegen:
    1. Open Kladblok als beheerder.
    2. Open het bestand C:\Windows\System32\Drivers\etc\hosts.
    3. Voeg de IP-adres en de hostnaam op een rij toe. Herhaal dit voor elke host of cluster waar u deze fout ziet.
    4. Sla het hosts-bestand op en sluit het bestand.
    5. Controleer of het toestel verbinding kan maken met de hosts via de app voor toestelbeheer. Na 30 minuten ziet u de meest recente informatie voor deze hosts in de Azure-portal.

## <a name="discovered-vms-not-in-portal"></a>Ontdekte VM's niet in portal

Als de detectiestatus 'Detectie in uitvoering' is, maar de VM's nog niet in de portal ziet, wacht dan een paar minuten:
- Het duurt ongeveer 15 minuten voor een VMware VM .
- Het duurt ongeveer twee minuten voor elke toegevoegde host voor Hyper-V VM detectie.

Als u wacht en de status niet verandert, selecteert u **Vernieuwen** op het tabblad **Servers.** Hiermoet het aantal gedetecteerde servers in Azure Migrate: Server Assessment en Azure Migrate: Server Migration weergeven.

Als dit niet werkt en u VMware-servers ontdekt:

- Controleer of het opgegeven vCenter-account machtigingen correct heeft ingesteld, met toegang tot ten minste één vm.
- Azure Migrate kan VMware VM's niet ontdekken als het vCenter-account toegang heeft op vCenter VM-mapniveau. [Meer informatie](set-discovery-scope.md) over scoping discovery.

## <a name="vm-data-not-in-portal"></a>VM-gegevens niet in portal

Als gedetecteerde VM's niet in de portal worden weergegeven of als de VM-gegevens verouderd zijn, wacht u enkele minuten. Het duurt maximaal 30 minuten voordat wijzigingen in gedetecteerde VM-configuratiegegevens in de portal worden weergegeven. Het kan enkele uren duren voordat wijzigingen in toepassingsgegevens worden weergegeven. Als er na deze tijd geen gegevens meer zijn, probeer dan als volgt

1. Selecteer **Overzicht**in **servers** > **azure migrate server-beoordeling**.
2. Selecteer **agentstatus** **onder Beheren**.
3. Selecteer **Agent vernieuwen**.
4. Wacht tot de vernieuwingsbewerking is voltooid. U moet nu actuele informatie zien.

## <a name="deleted-vms-appear-in-portal"></a>Verwijderde VM's worden weergegeven in portal

Als u VM's verwijdert en ze nog steeds in de portal worden weergegeven, wacht u 30 minuten. Als ze nog steeds worden weergegeven, vernieuwen zoals hierboven beschreven.

## <a name="common-app-discovery-errors"></a>Veelvoorkomende fouten bij het ontdekken van apps

Azure Migrate ondersteunt detectie van toepassingen, rollen en functies met Azure Migrate: Server Assessment. App-detectie wordt momenteel alleen voor VMware ondersteund. [Meer informatie](how-to-discover-applications.md) over de vereisten en stappen voor het instellen van app-detectie.

Typische fouten bij het ontdekken van apps worden samengevat in de tabel. 

**Fout** | **Oorzaak** | **Actie**
--- | --- | --- | ---
10000: "Niet in staat om de applicaties geïnstalleerd op de server te ontdekken". | Dit kan gebeuren als het besturingssysteem van de machine niet Windows of Linux is. | Gebruik alleen app-detectie voor Windows/Linux.
10001: "Niet in staat om de geïnstalleerde toepassingen van de server op te halen". | Interne fout - enkele ontbrekende bestanden in het toestel. | Neem contact op met Microsoft Ondersteuning.
10002: "Niet in staat om de geïnstalleerde toepassingen van de server op te halen". | De detectie-agent op het apparaat werkt mogelijk niet goed. | Als het probleem zichzelf niet binnen 24 uur oplost, neemt u contact op met de ondersteuning.
10003 "Unable to retrieve the applications installed the server". | De detectie-agent op het apparaat werkt mogelijk niet goed. | Als het probleem zichzelf niet binnen 24 uur oplost, neemt u contact op met de ondersteuning.
10004: "Niet in staat om geïnstalleerde applicaties te ontdekken voor <Windows / Linux> machines." |  Referenties voor toegang tot <Windows/Linux> machines zijn niet in het toestel opgenomen.| Voeg een referentie toe aan het toestel dat toegang heeft tot de <Windows/Linux> machines.
10005: "Geen toegang tot de on-premises server". | De toegangsreferenties kunnen verkeerd zijn. | Werk de referenties van het toestel bij en zorg ervoor dat u met deze apparaten toegang hebt tot de betreffende machine. 
10006: "Geen toegang tot de on-premises server". | Dit kan gebeuren als het besturingssysteem van de machine niet Windows of Linux is.|  Gebruik alleen app-detectie voor Windows/Linux.
10007: "Kan de opgehaalde metagegevens niet verwerken" | Deze interne fout is opgetreden tijdens het deserialiseren van JSON | Contact opnemen met Microsoft Support voor een oplossing
9000/9001/9002: "Niet in staat om de applicaties te ontdekken die op de server zijn geïnstalleerd". | VMware-hulpprogramma's worden mogelijk niet geïnstalleerd of zijn beschadigd. | Installeer/installeer VMware-gereedschappen op de betreffende machine en controleer of deze wordt uitgevoerd.
9003: Niet in staat om de applicaties te ontdekken die op de server zijn geïnstalleerd". | Dit kan gebeuren als het besturingssysteem van de machine niet Windows of Linux is. | Gebruik alleen app-detectie voor Windows/Linux.
9004: "Niet in staat om de applicaties te ontdekken die op de server zijn geïnstalleerd". | Dit kan gebeuren als de VM is uitgeschakeld. | Controleer voor detectie of de VM is ingeschakeld.
9005: "Niet in staat om de applicaties geïnstalleerd op de VM te ontdekken. | Dit kan gebeuren als het besturingssysteem van de machine niet Windows of Linux is. | Gebruik alleen app-detectie voor Windows/Linux.
9006/9007: "Niet in staat om de geïnstalleerde applicaties op te halen". | De detectie-agent op het apparaat werkt mogelijk niet goed. | Als het probleem zichzelf niet binnen 24 uur oplost, neemt u contact op met de ondersteuning.
9008: "Niet in staat om de geïnstalleerde toepassingen van de server op te halen". | Kan een interne fout zijn.  | Tf het probleem niet zelf op te lossen binnen 24 uur, contact opnemen met ondersteuning.
9009: "Niet in staat om de geïnstalleerde toepassingen van de server op te halen". | Kan optreden als de UAC-instellingen (Windows User Account Control) op de server beperkend zijn en detectie van geïnstalleerde toepassingen voorkomen. | Zoek naar 'Gebruikersaccountbeheer' instellingen op de server en configureer de UAC-instelling op de server op een van de onderste twee niveaus.
9010: "Niet in staat om de geïnstalleerde applicaties van de server op te halen". | Kan een interne fout zijn.  | Tf het probleem niet zelf op te lossen binnen 24 uur, contact opnemen met ondersteuning.
9011: "Bestand om te downloaden van gast is niet te vinden op de gast VM" | Het probleem kan optreden als gevolg van een interne fout. | Het probleem moet automatisch worden opgelost in 24 uur. Als het probleem zich nog steeds voordoet, neemt u contact op met Microsoft Support.
9012: "De inhoud van het resultaatbestand is leeg." | Het probleem kan optreden als gevolg van een interne fout. | Het probleem moet automatisch worden opgelost in 24 uur. Als het probleem zich nog steeds voordoet, neemt u contact op met Microsoft Support.
9013: "Voor elke login bij de VMware VM wordt een nieuw tijdelijk profiel aangemaakt" | Voor elke login in de VM wordt een nieuw tijdelijk profiel aangemaakt | Controleer of de gebruikersnaam in de vm-referenties van de gast in UPN-indeling is.
9015: "Niet in staat om verbinding te maken met VMware VM's vanwege onvoldoende bevoegdheden op vCenter" | Gastbewerkingenrol is niet ingeschakeld op het vCenter-gebruikersaccount | Controleer of de rol Gastbewerkingen is ingeschakeld op het vCenter-gebruikersaccount.
9016: "Niet in staat om verbinding te maken met VMware VM's als de gast operations agent is uit gegevens" | VMware-tools zijn niet goed geïnstalleerd of zijn niet up-to-date. | Zorg ervoor dat de VMware-tools goed zijn geïnstalleerd en up-to-date zijn.
9017: "Bestand met gedetecteerde metagegevens is niet te vinden op de VM." | Het probleem kan optreden als gevolg van een interne fout. | Neem contact op met Microsoft Support voor een oplossing.
9018: "PowerShell is niet geïnstalleerd in de vm's van gasten." | PowerShell is niet beschikbaar in de gast-VM. | Installeer PowerShell in de gast-VM.
9019: "Niet in staat om te ontdekken als gevolg van gast VM operatie mislukkingen" | VMware-gastbewerking is mislukt op de VM. | Controleer of de VM-referenties geldig zijn en dat de gebruikersnaam in de gastVM-referenties in UPN-indeling is opgegeven.
9020: 'Toestemming voor het maken van bestanden wordt geweigerd'. | De rol die is gekoppeld aan de gebruiker of het groepsbeleid beperkt de gebruiker om het bestand in de map te maken | Controleer of de gastgebruiker toestemming heeft gegeven voor het bestand in de map. Zie **Meldingen** in Serverbeoordeling voor de naam van de map.
9021: 'Toestemming voor het maken van bestanden wordt geweigerd in map System Temp Path'. | VMware-toolversie op de VM wordt niet ondersteund | Upgrade de VMware-toolversie boven 10.2.0.
9022: 'Wmi-objecttoegang krijgen wordt geweigerd'. | De rol die is gekoppeld aan de gebruiker of het groepsbeleid beperkt de gebruiker tot wmi-object. | Neem contact op met Microsoft Support.
9023: "SystemRoot-omgevingsvariabele waarde is leeg." | Niet bekend | Neem contact op met Microsoft Support.
9024: "Temp-omgevingsvariabele waarde is leeg." | Niet bekend | Neem contact op met Microsoft Support.
9025: "PowerShell is corrupt in de Gast VM's." | Niet bekend | Installeer PowerShell opnieuw in de gast-VM en controleer of PowerShell kan worden uitgevoerd op de gast-VM.
8084: "Niet in staat om toepassingen <Exception from VMware>te ontdekken als gevolg van VMware fout: " | Het Azure Migrate-toestel gebruikt VMware API's om toepassingen te ontdekken. Dit probleem kan optreden als een uitzondering wordt gegenereerd door vCenter Server terwijl u toepassingen probeert te ontdekken. Het foutbericht van VMware wordt weergegeven in het foutbericht in portal. | Zoek naar het bericht in de [VMware-documentatie](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html)en volg de stappen die u wilt oplossen. Als u niet herstellen, neemt u contact op met microsoft-ondersteuning.



## <a name="next-steps"></a>Volgende stappen
Stel een toestel in voor [VMware,](how-to-set-up-appliance-vmware.md) [Hyper-V](how-to-set-up-appliance-hyper-v.md)of [fysieke servers](how-to-set-up-appliance-physical.md).
