---
title: Problemen met de implementatie en detectie van Azure Migrate-apparaten oplossen
description: Krijg hulp bij het implementeren van het Azure Migrate apparaat en het detecteren van computers.
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 37da62a4eb0f934133d6486872ba319138299614
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048688"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Problemen met het Azure Migrate apparaat en de detectie oplossen

Dit artikel helpt u bij het oplossen van problemen bij het implementeren van het [Azure migrate](migrate-services-overview.md) apparaat en het gebruik van het apparaat om on-premises machines te detecteren.


## <a name="whats-supported"></a>Wat wordt er ondersteund?

[Bekijk](migrate-appliance.md) de vereisten voor het ondersteunings team voor apparaten.


## <a name="invalid-ovf-manifest-entry"></a>"Ongeldige vermelding van OVF-manifest"

Ga als volgt te werk als u de fout melding ' het opgegeven manifest bestand is ongeldig: ongeldige OVF-manifest vermelding ' wordt weer gegeven:

1. Controleer of het bestand van de Azure Migrate apparaat voor apparaten correct is gedownload door de hash-waarde te controleren. [Meer informatie](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). Als de hash-waarde niet overeenkomt, downloadt u het bestand van de eicellen opnieuw en voert u de implementatie opnieuw uit.
2. Als de implementatie nog steeds mislukt, en u de VMware vSphere-client gebruikt om het OVF-bestand te implementeren, probeert u het te implementeren via de vSphere-webclient. Als de implementatie nog steeds mislukt, kunt u proberen een andere webbrowser te gebruiken.
3. Als u de vSphere-webclient gebruikt en deze probeert te implementeren op vCenter Server 6,5 of 6,7, probeert u de eicellen rechtstreeks op de ESXi-host te implementeren:
   - Maak rechtstreeks verbinding met de ESXi-host (in plaats van vCenter Server) met de webclient (https://<*IP-adres*van de host >/UI).
   - Selecteer in de **inventaris**van **start** > **bestand** > **OVF-sjabloon implementeren**. Blader naar de eicellen en voltooi de implementatie.
4. Als de implementatie nog steeds mislukt, neemt u contact op met Azure Migrate ondersteuning.

## <a name="cant-connect-to-the-internet"></a>Kan geen verbinding maken met Internet

Dit kan gebeuren als de apparaatapparaat zich achter een proxy bevindt.

- Zorg ervoor dat u de autorisatie referenties opgeeft als de proxy deze nodig heeft.
- Als u een firewall proxy op basis van een URL gebruikt om de uitgaande connectiviteit te beheren, voegt u [deze url's](migrate-appliance.md#url-access) toe aan een acceptatie lijst.
- Als u een interceptie proxy gebruikt om verbinding te maken met internet, importeert u het proxy certificaat op de apparaat-VM met behulp van de [volgende stappen](https://docs.microsoft.com/azure/migrate/concepts-collector).

##  <a name="datetime-synchronization-error"></a>Fout bij de synchronisatie van datum en tijd

Er is een fout opgetreden bij de synchronisatie van datum en tijd (802) geeft aan dat de klok van de server mogelijk niet meer is gesynchroniseerd met de huidige tijd van meer dan vijf minuten. Wijzig de klok tijd op de Collector-VM zodat deze overeenkomt met de huidige tijd:

1. Open een opdracht prompt met beheerders rechten op de VM.
2. Voer **w32tm/TZ**uit om de tijd zone te controleren.
3. Voer **w32tm/resync**uit om de tijd te synchroniseren.


## <a name="unabletoconnecttoserver"></a>"UnableToConnectToServer"

Als deze verbindings fout optreedt, kunt u mogelijk geen verbinding maken met vCenter Server *servername*. com: 9443. De fout gegevens geven aan dat er geen eind punt luistert op https://*servername*. com: 9443/SDK waarmee het bericht kan worden geaccepteerd.

- Controleer of u de nieuwste versie van het apparaat uitvoert. Als dat niet het geval is, moet u het apparaat bijwerken naar de [nieuwste versie](https://docs.microsoft.com/azure/migrate/concepts-collector).
- Als het probleem zich nog steeds voordoet in de meest recente versie, is het mogelijk dat het apparaat de opgegeven vCenter Server naam niet kan omzetten of dat de opgegeven poort onjuist is. Als de poort niet is opgegeven, probeert de Collector standaard verbinding te maken met poort nummer 443.

    1. Ping *Server naam*. com van het apparaat.
    2. Als stap 1 mislukt, probeert u verbinding te maken met de vCenter-Server met behulp van het IP-adres.
    3. Bepaal het juiste poort nummer om verbinding te maken met vCenter Server.
    4. Controleer of vCenter Server actief is.


## <a name="error-6005260039-appliance-might-not-be-registered"></a>Fout 60052/60039: het apparaat is mogelijk niet geregistreerd

- Fout 60052 ' het apparaat is mogelijk niet geregistreerd voor het Azure Migrate-project ' treedt op als het Azure-account dat wordt gebruikt om het apparaat te registreren, onvoldoende machtigingen heeft.
    - Zorg ervoor dat het Azure-gebruikers account dat wordt gebruikt om het apparaat te registreren, ten minste Inzender machtigingen heeft voor het abonnement.
    - Meer [informatie](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) over vereiste Azure-rollen en-machtigingen.
- Fout 60039 ' het apparaat is mogelijk niet geregistreerd voor het Azure Migrate-project ' kan zich voordoen als de registratie mislukt, omdat het Azure Migrate project dat is gebruikt voor de registratie van het apparaat, niet kan worden gevonden.
    - In de Azure Portal en controleer of het project bestaat in de resource groep.
    - Als het project niet bestaat, maakt u een nieuw Azure Migrate project in de resource groep en registreert u het apparaat opnieuw. [Meer informatie over het](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) maken van een nieuw project.

## <a name="error-6003060031-key-vault-management-operation-failed"></a>Fout 60030/60031: Key Vault beheer bewerking is mislukt

Ga als volgt te werk als de fout 60030 of 60031 wordt weer gegeven: ' een Azure Key Vault beheer bewerking is mislukt '
- Zorg ervoor dat het Azure-gebruikers account dat wordt gebruikt om het apparaat te registreren, ten minste Inzender machtigingen heeft voor het abonnement.
- Zorg ervoor dat het account toegang heeft tot de sleutel kluis die is opgegeven in het fout bericht en voer de bewerking opnieuw uit.
- Als het probleem zich blijft voordoen, neemt u contact op met Microsoft-ondersteuning.
- Meer [informatie](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) over de vereiste Azure-rollen en-machtigingen.

## <a name="error-60028-discovery-couldnt-be-initiated"></a>Fout 60028: detectie kan niet worden gestart

Fout 60028: detectie kan niet worden gestart wegens een fout. De bewerking is mislukt voor de opgegeven lijst van hosts of clusters. Hiermee wordt aangegeven dat de detectie niet kan worden gestart op de hosts die in de fout worden vermeld vanwege een probleem bij het openen of ophalen van VM-gegevens. De rest van de hosts is toegevoegd.

- Voeg de hosts in de fout weer toe met behulp van de optie **host toevoegen** .
- Als er een validatie fout optreedt, raadpleegt u de richt lijnen voor herstel om de fouten op te lossen en probeert u het opnieuw met de optie **detectie opslaan en starten** .

## <a name="error-60025-azure-ad-operation-failed"></a>Fout 60025: de Azure AD-bewerking is mislukt 
Fout 60025: er is een Azure AD-bewerking mislukt. De fout is opgetreden tijdens het maken of bijwerken van de Azure AD-toepassing "treedt op wanneer het Azure-gebruikers account dat wordt gebruikt om de detectie te initiëren, afwijkt van het account dat wordt gebruikt om het apparaat te registreren. Voer een van de volgende handelingen uit:

- Zorg ervoor dat het gebruikers account dat de detectie initieert, hetzelfde is als dat waarmee het apparaat wordt geregistreerd.
- Geef Azure Active Directory machtigingen voor toegang tot de toepassing op voor het gebruikers account waarvoor de detectie bewerking is mislukt.
- Verwijder de resource groep die u eerder hebt gemaakt voor het Azure Migrate project. Maak een andere resource groep om opnieuw te starten.
- Meer [informatie](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) over Azure Active Directory toepassings machtigingen.


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>Fout 50004: kan geen verbinding maken met de host of het cluster

Fout 50004: ' kan geen verbinding maken met een host of cluster omdat de server naam niet kan worden omgezet. WinRM-fout code: 0x803381B9 "kan optreden als de Azure DNS-service voor het apparaat het cluster of de hostnaam die u hebt ingevoerd, niet kan omzetten.

- Als u deze fout op het cluster ziet, wordt de cluster-FQDN.
- U ziet deze fout mogelijk ook voor hosts in een cluster. Dit geeft aan dat het apparaat verbinding kan maken met het cluster, maar dat het cluster hostnamen retourneert die geen FQDN-namen zijn. Om deze fout op te lossen, werkt u het hosts-bestand op het apparaat bij door een toewijzing van het IP-adres en de hostnamen toe te voegen:
    1. Open Klad blok als beheerder.
    2. Open het C:\Windows\System32\Drivers\etc\hosts-bestand.
    3. Voeg het IP-adres en de hostnaam toe aan een rij. Herhaal deze stap voor elke host of elk cluster waar u deze fout ziet.
    4. Opslaan en sluiten van het hosts-bestand.
    5. Controleer of het apparaat verbinding kan maken met de hosts met behulp van de app voor het beheren van het apparaat. Na 30 minuten ziet u de meest recente informatie voor deze hosts in de Azure Portal.

## <a name="discovered-vms-not-in-portal"></a>Gedetecteerde Vm's niet in Portal

Als de detectie status ' detectie wordt uitgevoerd ' is, maar de virtuele machines in de portal nog niet worden weer geven, wacht u een paar minuten:
- Het duurt ongeveer 15 minuten voor een virtuele VMware-machine.
- Het duurt ongeveer twee minuten voor elke toegevoegde host voor de detectie van Hyper-V-VM'S.

Als u een ogen blik geduld en de status niet verandert, selecteert u **vernieuwen** op het tabblad **servers** . Hier ziet u het aantal gedetecteerde servers in Azure Migrate: Server evaluatie en Azure Migrate: Server migratie.

Als dit niet werkt en u de VMware-servers wilt detecteren:

- Controleer of de juiste machtigingen zijn ingesteld voor het vCenter-account dat u hebt opgegeven, met toegang tot ten minste één virtuele machine.
- Azure Migrate kan geen VMware-Vm's detecteren als het vCenter-account toegang heeft ingesteld op het niveau van de vCenter-VM-map. Meer [informatie](tutorial-assess-vmware.md#set-the-scope-of-discovery) over het detecteren van scopes.

## <a name="vm-data-not-in-portal"></a>VM-gegevens niet in Portal

Als gedetecteerde Vm's niet worden weer gegeven in de portal of als de VM-gegevens verouderd zijn, wacht u een paar minuten. Het duurt Maxi maal 30 minuten voordat wijzigingen in gedetecteerde VM-configuratie gegevens worden weer gegeven in de portal. Het kan enkele uren duren voordat wijzigingen in de toepassings gegevens worden weer gegeven. Als er na deze tijd geen gegevens zijn, kunt u als volgt vernieuwen

1. Selecteer in **Servers** > **Azure migrate server-evaluatie**de optie **overzicht**.
2. Selecteer onder **beheren**de optie **status van agent**.
3. Selecteer **agent vernieuwen**.
4. Wacht tot de vernieuwings bewerking is voltooid. Nu worden actuele gegevens weer geven.

## <a name="deleted-vms-appear-in-portal"></a>Verwijderde Vm's worden weer gegeven in de portal

Als u Vm's verwijdert en deze nog steeds worden weer gegeven in de portal, wacht u 30 minuten. Als deze nog steeds worden weer gegeven, moet u vernieuwen zoals hierboven wordt beschreven.

## <a name="common-app-discovery-errors"></a>Veelvoorkomende fouten bij app-detectie

Azure Migrate ondersteunt de detectie van toepassingen, functies en onderdelen met behulp van Azure Migrate: Server Assessment. App-detectie wordt momenteel alleen ondersteund voor VMware. Meer [informatie](how-to-discover-applications.md) over de vereisten en stappen voor het instellen van app-detectie.

Veelvoorkomende fouten bij het detecteren van apps worden in de tabel samenvatten. 

**Optreedt** | **Oorzaak** | **Actie**
--- | --- | --- | ---
10000: de toepassingen die op de server zijn geïnstalleerd, kunnen niet worden gedetecteerd. | Dit kan gebeuren als het besturings systeem van de computer niet Windows of Linux is. | Gebruik app-detectie alleen voor Windows/Linux.
10001: de toepassingen waarop de server is geïnstalleerd, kunnen niet worden opgehaald. | Interne fout: sommige bestanden ontbreken in het apparaat. | Neem contact op met Microsoft Ondersteuning.
10002: de toepassingen waarop de server is geïnstalleerd, kunnen niet worden opgehaald. | De detectie agent op het apparaat werkt mogelijk niet goed. | Neem contact op met de ondersteuning als het probleem zich binnen 24 uur niet kan oplossen.
10003 de toepassingen die de server hebben geïnstalleerd, kunnen niet worden opgehaald. | De detectie agent op het apparaat werkt mogelijk niet goed. | Neem contact op met de ondersteuning als het probleem zich binnen 24 uur niet kan oplossen.
10004: de geïnstalleerde toepassingen voor < Windows/Linux > machines kunnen niet worden gedetecteerd. |  Referenties voor toegang tot < Windows/Linux > machines die niet zijn meegeleverd in het apparaat.| Voeg een referentie toe aan het apparaat dat toegang heeft tot de < Windows/Linux > machines.
10005: kan geen toegang krijgen tot de on-premises server. | De toegangs referenties zijn mogelijk onjuist. | Werk de toestel referenties bij om ervoor te zorgen dat u toegang hebt tot de relevante machine. 
10006: kan geen toegang krijgen tot de on-premises server. | Dit kan gebeuren als het besturings systeem van de computer niet Windows of Linux is.|  Gebruik app-detectie alleen voor Windows/Linux.
9000/9001/9002: de toepassingen die op de server zijn geïnstalleerd, kunnen niet worden gedetecteerd. | VMware-hulpprogram ma's zijn mogelijk niet geïnstalleerd of beschadigd. | Installeer/installeer VMware-hulpprogram ma's op de relevante computer en controleer of deze wordt uitgevoerd.
9003: kan de toepassingen die op de server zijn geïnstalleerd, niet detecteren. | Dit kan gebeuren als het besturings systeem van de computer niet Windows of Linux is. | Gebruik app-detectie alleen voor Windows/Linux.
9004: de toepassingen die op de server zijn geïnstalleerd, kunnen niet worden gedetecteerd. | Dit kan gebeuren als de virtuele machine is uitgeschakeld. | Zorg ervoor dat de virtuele machine is ingeschakeld voor detectie.
9005: de toepassingen die op de virtuele machine zijn geïnstalleerd, kunnen niet worden gedetecteerd. | Dit kan gebeuren als het besturings systeem van de computer niet Windows of Linux is. | Gebruik app-detectie alleen voor Windows/Linux.
9006/9007: de toepassingen waarop de server is geïnstalleerd, kunnen niet worden opgehaald. | De detectie agent op het apparaat werkt mogelijk niet goed. | Neem contact op met de ondersteuning als het probleem zich binnen 24 uur niet kan oplossen.
9008: de toepassingen waarop de server is geïnstalleerd, kunnen niet worden opgehaald. | Dit kan een interne fout zijn.  | Tf het probleem wordt binnen 24 uur niet opgelost. Neem contact op met de ondersteuning.
9009: de toepassingen waarop de server is geïnstalleerd, kunnen niet worden opgehaald. | Kan optreden als de Windows UAC-instellingen (User Account Control) op de server beperkend zijn en voor komen dat geïnstalleerde toepassingen worden gedetecteerd. | Zoek de instellingen voor Gebruikersaccountbeheer op de server en configureer de UAC-instelling op de server op een van de twee lagere niveaus.
9010: de toepassingen waarop de server is geïnstalleerd, kunnen niet worden opgehaald. | Dit kan een interne fout zijn.  | Tf het probleem wordt binnen 24 uur niet opgelost. Neem contact op met de ondersteuning.
8084: ' kan geen toepassingen detecteren vanwege VMware-fout: <Exception from VMware>' | Het Azure Migrate-apparaat gebruikt VMware-Api's om toepassingen te detecteren. Dit probleem kan zich voordoen als er een uitzonde ring wordt gegenereerd door vCenter Server tijdens het detecteren van toepassingen. De fout melding van VMware wordt weer gegeven in het fout bericht dat wordt weer gegeven in de portal. | Zoek het bericht in de [VMware-documentatie](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html)en volg de stappen om dit op te lossen. Neem contact op met micro soft ondersteuning als u deze niet kunt oplossen.
9012: de toepassingen die op de server zijn geïnstalleerd, kunnen niet worden gedetecteerd | Het probleem kan optreden vanwege een interne fout.  | Neem contact op met de ondersteuning als het probleem zich binnen 24 uur niet kan oplossen.
9013: de toepassingen die op de server zijn geïnstalleerd, kunnen niet worden gedetecteerd | Er wordt een nieuw tijdelijk profiel gemaakt telkens wanneer u zich aanmeldt bij de virtuele machine.  | Zorg ervoor dat er geen tijdelijk profiel voor de gast gebruiker is gemaakt.



## <a name="next-steps"></a>Volgende stappen
Stel een apparaat in voor [VMware](how-to-set-up-appliance-vmware.md)-, [Hyper-V-](how-to-set-up-appliance-hyper-v.md)of [fysieke servers](how-to-set-up-appliance-physical.md).
