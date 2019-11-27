---
title: Problemen met Azure Migrate oplossen | Microsoft Docs
description: Hierin wordt een overzicht gegeven van bekende problemen in de Azure Migrate-service en tips voor het oplossen van veelvoorkomende fouten.
author: musa-57
ms.manager: abhemraj
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: hamusa
ms.openlocfilehash: 96c5190988d79885f3a1335b6fd431e028bba8fc
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384066"
---
# <a name="troubleshoot-azure-migrate"></a>Problemen met Azure Migrate oplossen

[Azure migrate](migrate-services-overview.md) biedt een hub van hulpprogram ma's voor evaluatie en migratie, evenals onafhankelijke software leveranciers (ISV)-aanbiedingen van derden. Dit artikel helpt u bij het oplossen van problemen met Azure Migrate, Azure Migrate server-evaluatie en Azure Migrate server migratie.


## <a name="find-a-project"></a>Een project zoeken

Er zijn [twee versies](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) van Azure Migrate.


Als u het Azure Migrate project hebt gemaakt in de huidige versie van Azure Migrate gaat u als volgt te werk:

1. Zoek in het [Azure Portal](https://portal.azure.com)naar **Azure migrate**.
2. Selecteer in het Azure Migrate DashBoard > servers **wijzigen** in de rechter bovenhoek.

    ![Overschakelen naar een bestaand Azure Migrate project](./media/troubleshooting-general/switch-project.png)

3. Selecteer het juiste abonnement en Azure Migrate project.


Als u het project hebt gemaakt in de vorige versie van Azure Migrate, gaat u als volgt te werk:

1. Zoek in het [Azure Portal](https://portal.azure.com)naar **Azure migrate**.
2. Als u in de vorige versie van het dash board van Azure Migrate een project hebt gemaakt, wordt er een banner weer gegeven dat verwijst naar oudere projecten. Selecteer de banner.

    ![Toegang tot bestaande projecten](./media/troubleshooting-general/access-existing-projects.png)

3. Bekijk de lijst met oude projecten.


## <a name="create-additional-projects"></a>Aanvullende projecten maken

Maak als volgt een nieuw Azure Migrate project:

1. Zoek in het [Azure Portal](https://portal.azure.com)naar **Azure migrate**.
2. Selecteer in de rechter bovenhoek op het Azure Migrate dash board > **servers** **wijzigen** .

   ![Azure Migrate project wijzigen](./media/troubleshooting-general/switch-project.png)

3. Als u een nieuw project wilt maken, selecteert u **hier klikken**.

   ![Een tweede Azure Migrate-project maken](./media/troubleshooting-general/create-new-project.png)

## <a name="review-supported-geographies"></a>Ondersteunde geographs controleren

Bekijk ondersteunde geographs voor [VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) en [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

## <a name="delete-projectsworkspaces"></a>Projecten/werk ruimten verwijderen

Houd rekening met het volgende wanneer u Azure Migrate projecten en Log Analytics-werk ruimten verwijdert:

- Wanneer u een Azure Migrate project verwijdert, worden het project *en* de meta gegevens over gedetecteerde machines verwijderd.
- Als u een Log Analytics-werk ruimte hebt gekoppeld aan het hulp programma voor Server evaluatie, wordt de werk ruimte niet automatisch verwijderd.
- Dezelfde Log Analytics-werk ruimte kan voor meerdere scenario's worden gebruikt.
- Als u de Log Analytics-werk ruimte wilt verwijderen, moet u dit hand matig doen.


### <a name="delete-a-project"></a>Een project verwijderen

Een project verwijderen uit de huidige versie van Azure Migrate:

1. Open de Azure-resource groep waarin het project is gemaakt.
2. Selecteer op de pagina resource groep de optie **verborgen typen weer geven**.
3. Selecteer het migratie project dat u wilt verwijderen. Het resource type is micro soft. migrate/migrateprojects en verwijdert het.

Een project verwijderen uit de oudere versie van Azure Migrate:

1. Open de Azure-resource groep waarin het project is gemaakt.
2. Selecteer het migratie project dat u wilt verwijderen. Het resource type is een migratie project en wordt verwijderd.


### <a name="delete-a-workspace"></a>Een werkruimte verwijderen

Blader naar de Log Analytics werkruimte die aan het project is gekoppeld.
* Als u het Azure Migrate project nog niet hebt verwijderd, kunt u de koppeling naar de werk ruimte vinden in **essentials** > **Server Assessment**.
       ![werk ruimte van LA](./media/troubleshooting-general/loganalytics-workspace.png)

     * If you've already deleted the Azure Migrate project, select **Resource Groups** in the left pane of the Azure portal. Locate the workspace in the relevant resources group, and [follow the instructions](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) to delete it.


## <a name="error-requests-must-contain-user-identity-headers"></a>Fout ' aanvragen moeten gebruikers identiteits headers bevatten '

Bij het maken van een project kan deze fout erop wijzen dat u geen toegang hebt tot de Azure Active Directory (Azure AD)-Tenant van de organisatie.

- Wanneer u voor de eerste keer aan een Azure AD-Tenant toevoegt, ontvangt u een e-mail uitnodiging om lid te worden van de Tenant.
- Accepteer de uitnodiging om toe te voegen aan de Tenant.
    - Als u het e-mail bericht niet kunt zien, neemt u contact op met een gebruiker die toegang heeft tot de Tenant en vraagt u [de uitnodiging opnieuw](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users) naar u te verzenden.
    - Open de e-mail uitnodiging en selecteer de koppeling om de uitnodiging te accepteren. Meld u vervolgens af bij de Azure Portal en meld u opnieuw aan. (het vernieuwen van de browser werkt niet.) Vervolgens kunt u beginnen met het maken van het migratie project.


## <a name="error-invalid-ovf-manifest-entry"></a>Fout: er is een ongeldige OVF-manifest vermelding

Ga als volgt te werk als u de fout melding ' het opgegeven manifest bestand is ongeldig: ongeldige OVF-manifest vermelding ' wordt weer gegeven:

1. Controleer of het bestand van de Azure Migrate apparaat voor apparaten correct is gedownload door de hash-waarde te controleren. [Meer informatie](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). Als de hash-waarde niet overeenkomt, downloadt u het bestand van de eicellen opnieuw en voert u de implementatie opnieuw uit.
2. Als de implementatie nog steeds mislukt, en u de VMware vSphere-client gebruikt om het OVF-bestand te implementeren, probeert u het te implementeren via de vSphere-webclient. Als de implementatie nog steeds mislukt, kunt u proberen een andere webbrowser te gebruiken.
3. Als u de vSphere-webclient gebruikt en deze probeert te implementeren op vCenter Server 6,5 of 6,7, probeert u de eicellen rechtstreeks op de ESXi-host te implementeren:
   - Maak rechtstreeks verbinding met de ESXi-host (in plaats van vCenter Server) met de webclient (https://<*IP-adres*van de host >/UI).
   - Selecteer in de **inventaris**van **start** > **bestand** > **OVF-sjabloon implementeren**. Blader naar de eicellen en voltooi de implementatie.
4. Als de implementatie nog steeds mislukt, neemt u contact op met Azure Migrate ondersteuning.

## <a name="appliance-cant-connect-to-the-internet"></a>Het apparaat kan geen verbinding maken met Internet

Dit kan gebeuren als de apparaatapparaat zich achter een proxy bevindt.

- Zorg ervoor dat u de autorisatie referenties opgeeft als de proxy deze nodig heeft.
- Als u een firewall proxy op basis van een URL gebruikt om de uitgaande connectiviteit te beheren, voegt u deze Url's toe aan een acceptatie lijst:

    - [Url's voor VMware-evaluatie](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
    - [Url's voor Hyper-V-evaluatie](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
    - [Url's voor VMware-agentloze migratie](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
    - [URL'S voor migratie op basis van VMware-agents](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
    - [Url's voor Hyper-V-migratie](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

- Als u een interceptie proxy gebruikt om verbinding te maken met internet, importeert u het proxy certificaat op de apparaat-VM met behulp van de [volgende stappen](https://docs.microsoft.com/azure/migrate/concepts-collector).

## <a name="errordatetime-synchronization"></a>Fout: datum/tijd synchronisatie

Er is een fout opgetreden bij de synchronisatie van datum en tijd (802) geeft aan dat de klok van de server mogelijk niet meer is gesynchroniseerd met de huidige tijd van meer dan vijf minuten. Wijzig de klok tijd op de Collector-VM zodat deze overeenkomt met de huidige tijd:

1. Open een opdracht prompt met beheerders rechten op de VM.
2. Voer **w32tm/TZ**uit om de tijd zone te controleren.
3. Voer **w32tm/resync**uit om de tijd te synchroniseren.


## <a name="error-unabletoconnecttoserver"></a>Fout: UnableToConnectToServer

Als deze verbindings fout optreedt, kunt u mogelijk geen verbinding maken met vCenter Server *servername*. com: 9443. De fout gegevens geven aan dat er geen eind punt luistert op https://*servername*. com: 9443/SDK waarmee het bericht kan worden geaccepteerd.

- Controleer of u de nieuwste versie van het apparaat uitvoert. Als dat niet het geval is, moet u het apparaat bijwerken naar de [nieuwste versie](https://docs.microsoft.com/azure/migrate/concepts-collector).
- Als het probleem zich nog steeds voordoet in de meest recente versie, is het mogelijk dat het apparaat de opgegeven vCenter Server naam niet kan omzetten of dat de opgegeven poort onjuist is. Als de poort niet is opgegeven, probeert de Collector standaard verbinding te maken met poort nummer 443.

    1. Ping *Server naam*. com van het apparaat.
    2. Als stap 1 mislukt, probeert u verbinding te maken met de vCenter-Server met behulp van het IP-adres.
    3. Bepaal het juiste poort nummer om verbinding te maken met vCenter Server.
    4. Controleer of vCenter Server actief is.


## <a name="error-appliance-might-not-be-registered"></a>Fout: het apparaat is mogelijk niet geregistreerd

- Fout 60052 ' het apparaat is mogelijk niet geregistreerd voor het Azure Migrate-project ' treedt op als het Azure-account dat wordt gebruikt om het apparaat te registreren, onvoldoende machtigingen heeft.
    - Zorg ervoor dat het Azure-gebruikers account dat wordt gebruikt om het apparaat te registreren, ten minste Inzender machtigingen heeft voor het abonnement.
    - Meer [informatie](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) over vereiste Azure-rollen en-machtigingen.
- Fout 60039 ' het apparaat is mogelijk niet geregistreerd voor het Azure Migrate-project ' kan zich voordoen als de registratie mislukt, omdat het Azure Migrate project dat is gebruikt voor de registratie van het apparaat, niet kan worden gevonden.
    - In de Azure Portal en controleer of het project bestaat in de resource groep.
    - Als het project niet bestaat, maakt u een nieuw Azure Migrate project in de resource groep en registreert u het apparaat opnieuw. [Meer informatie over het](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) maken van een nieuw project.

## <a name="error-key-vault-management-operation-failed"></a>Fout: Key Vault beheer bewerking is mislukt

Ga als volgt te werk als de fout 60030 of 60031 wordt weer gegeven: ' een Azure Key Vault beheer bewerking is mislukt '
- Zorg ervoor dat het Azure-gebruikers account dat wordt gebruikt om het apparaat te registreren, ten minste Inzender machtigingen heeft voor het abonnement.
- Zorg ervoor dat het account toegang heeft tot de sleutel kluis die is opgegeven in het fout bericht en voer de bewerking opnieuw uit.
- Neem contact op met micro soft ondersteuning als het probleem zich blijft voordoen.
- Meer [informatie](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) over de vereiste Azure-rollen en-machtigingen.

## <a name="fix-discovery-couldnt-be-initiated"></a>Fix: detectie kan niet worden gestart

Fout 60028: detectie kan niet worden gestart wegens een fout. De bewerking is mislukt voor de opgegeven lijst van hosts of clusters. Hiermee wordt aangegeven dat de detectie niet kan worden gestart op de hosts die in de fout worden vermeld vanwege een probleem bij het openen of ophalen van VM-gegevens. De rest van de hosts is toegevoegd.

- Voeg de hosts in de fout weer toe met behulp van de optie **host toevoegen** .
- Als er een validatie fout optreedt, raadpleegt u de richt lijnen voor herstel om de fouten op te lossen en probeert u het opnieuw met de optie **detectie opslaan en starten** .

## <a name="fix-azure-ad-operation-failed-60025"></a>Fix: de Azure AD-bewerking is mislukt (60025)

Fout 60025: er is een Azure AD-bewerking mislukt. De fout is opgetreden tijdens het maken of bijwerken van de Azure AD-toepassing "treedt op wanneer het Azure-gebruikers account dat wordt gebruikt om de detectie te initiëren, afwijkt van het account dat wordt gebruikt om het apparaat te registreren. Voer een van de volgende handelingen uit:

- Zorg ervoor dat het gebruikers account dat de detectie initieert, hetzelfde is als dat waarmee het apparaat wordt geregistreerd.
- Geef Azure Active Directory machtigingen voor toegang tot de toepassing op voor het gebruikers account waarvoor de detectie bewerking is mislukt.
- Verwijder de resource groep die u eerder hebt gemaakt voor het Azure Migrate project. Maak een andere resource groep om opnieuw te starten.
- Meer [informatie](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) over Azure Active Directory toepassings machtigingen.


## <a name="discovered-vms-not-in-portal"></a>Gedetecteerde Vm's niet in Portal

Als u de detectie start zodat de **Server evaluatie** en **server migratie** **detectie worden uitgevoerd**, maar de virtuele machines in de portal nog niet worden weer gegeven, let dan op het volgende:

- Nadat de detectie van het apparaat is gestart, duurt het ongeveer 15 minuten voor een VMware-VM-detectie en ongeveer twee minuten voor elke toegevoegde host voor de detectie van Hyper-V-VM'S.
- Als de **detectie** nog steeds wordt weer geven, zelfs na deze wacht tijden, selecteert u **vernieuwen** op het tabblad **servers** . Hier wordt het aantal gedetecteerde servers weer gegeven in **Server evaluatie** en **server migratie**.


## <a name="deleted-vms-in-the-portal"></a>Verwijderde Vm's in de portal

Als u een apparaat hebt geïmplementeerd dat uw on-premises omgeving continu detecteert, maar verwijderde Vm's worden nog steeds weer gegeven in de portal, let dan op het volgende:  

- Het duurt Maxi maal 30 minuten voordat de detectie gegevens die door het apparaat zijn verzameld, worden weer gegeven in de portal.
- Als u na 30 minuten geen actuele informatie ziet, vernieuwt u de gegevens met de volgende stappen:

    1. Selecteer in **Servers** > **Azure migrate server-evaluatie**de optie **overzicht**.
    2. Selecteer onder **beheren**de optie **status van agent**
    3. Selecteer **agent vernieuwen**.
    1. Wacht tot de vernieuwings bewerking is voltooid. Nu worden actuele gegevens weer geven.

## <a name="vm-information-isnt-in-the-portal"></a>VM-informatie staat niet in de portal

- Het duurt Maxi maal 30 minuten voordat de detectie gegevens die door het apparaat zijn verzameld, worden weer gegeven in de portal.
- Als u na 30 minuten geen actuele informatie ziet, vernieuwt u de gegevens met de volgende stappen:

    1. Selecteer in **Servers** > **Azure migrate server-evaluatie**de optie **overzicht**.
    2. Selecteer onder **beheren**de optie **status van agent**
    3. Selecteer **agent vernieuwen**.
    1. Wacht tot de vernieuwings bewerking is voltooid. Nu worden actuele gegevens weer geven.


## <a name="fix-cant-connect-to-host-or-cluster"></a>Fix: kan geen verbinding maken met de host of het cluster

Fout 50004: ' kan geen verbinding maken met een host of cluster omdat de server naam niet kan worden omgezet. WinRM-fout code: 0x803381B9 "kan optreden als de Azure DNS-service voor het apparaat het cluster of de hostnaam die u hebt ingevoerd, niet kan omzetten.

- Als u deze fout op het cluster ziet, wordt de cluster-FQDN.
- U ziet deze fout mogelijk ook voor hosts in een cluster. Dit geeft aan dat het apparaat verbinding kan maken met het cluster, maar dat het cluster hostnamen retourneert die geen FQDN-namen zijn. Om deze fout op te lossen, werkt u het hosts-bestand op het apparaat bij door een toewijzing van het IP-adres en de hostnamen toe te voegen:
    1. Open Klad blok als beheerder.
    2. Open het C:\Windows\System32\Drivers\etc\hosts-bestand.
    3. Voeg het IP-adres en de hostnaam toe aan een rij. Herhaal deze stap voor elke host of elk cluster waar u deze fout ziet.
    4. Opslaan en sluiten van het hosts-bestand.
    5. Controleer of het apparaat verbinding kan maken met de hosts met behulp van de app voor het beheren van het apparaat. Na 30 minuten ziet u de meest recente informatie voor deze hosts in de Azure Portal.

## <a name="application-discovery-issues"></a>Problemen met toepassings detectie

De detectie van toepassingen wordt momenteel alleen ondersteund voor virtuele VMware-machines. Ondersteuning voor virtuele Hyper-V-machines en fysieke servers wordt in de toekomst ingeschakeld.

Voor de detectie van toepassingen moet u VM-referenties opgeven in het apparaat. Als u geen VM-referenties in het apparaat hebt verstrekt, werkt de detectie van toepassingen niet. Meer [informatie](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-vcenter-server-permissions) over de toegangs rechten die nodig zijn voor vCenter Server en voor virtuele VMware-machines. Als u VM-referenties hebt ingevoerd in het apparaat en de detectie van de toepassing mislukt, raadpleegt u de volgende tabel om de oorzaak van de fout en de herstel actie te identificeren:

**Fout code** | **Bericht** | **Mogelijke oorzaak** | **Aanbevolen actie**
--- | --- | --- | ---
10.000 | De toepassingen die op de server zijn geïnstalleerd, kunnen niet worden gedetecteerd. | Dit kan gebeuren als het besturings systeem dat op de-server wordt uitgevoerd, geen Windows of Linux is. | De detectie van geïnstalleerde toepassingen wordt alleen ondersteund voor Windows-en Linux-servers.
10001 | Kan de toepassingen die de server hebben geïnstalleerd, niet ophalen. | Dit wordt veroorzaakt door een interne fout omdat er enkele bestanden ontbreken in het apparaat. | Neem contact op met Microsoft Ondersteuning.
10002 | Kan de toepassingen die de server hebben geïnstalleerd, niet ophalen. | Dit kan gebeuren als de detectie agent in het Azure Migrate apparaat niet goed werkt. | Het probleem moet in 24 uur automatisch worden opgelost. Als het probleem zich blijft voordoen, neemt u contact op met Microsoft Ondersteuning.
10003 | Kan de toepassingen die de server hebben geïnstalleerd, niet ophalen. | Dit kan gebeuren als de detectie agent niet goed werkt. | Het probleem moet in 24 uur automatisch worden opgelost. Als het probleem zich blijft voordoen, neemt u contact op met Microsoft Ondersteuning.
10004 | Geïnstalleerde toepassingen voor < Windows/Linux > machines kunnen niet worden gedetecteerd. |  Referenties voor toegang tot < Windows/Linux > machines zijn niet in het Azure Migrate apparaat geleverd | Voeg een referentie toe aan het Azure Migrate-apparaat dat toegang heeft tot de < Windows/Linux > machines.
10005 | Geen toegang tot de on-premises server. | Dit kan gebeuren als de referenties van de computer voor toegang tot de server onjuist zijn. | Werk de referenties die zijn opgenomen in het apparaat bij en controleer of de server toegankelijk is met behulp van de referentie.
10006 | Geen toegang tot de on-premises server. | Dit kan gebeuren als het besturings systeem dat op de-server wordt uitgevoerd, geen Windows of Linux is. | De detectie van geïnstalleerde toepassingen wordt alleen ondersteund voor Windows-en Linux-servers.
9000 | De toepassingen die op de virtuele machine zijn geïnstalleerd, kunnen niet worden gedetecteerd. | VMware-hulpprogram ma's zijn mogelijk niet geïnstalleerd of beschadigd. | Installeer/installeer VMware-hulpprogram ma's in de VM en controleer of deze wordt uitgevoerd.
9001 | De toepassingen die op de virtuele machine zijn geïnstalleerd, kunnen niet worden gedetecteerd. | VMware-hulpprogram ma's zijn mogelijk niet geïnstalleerd of beschadigd. | Installeer/installeer VMware-hulpprogram ma's in de VM en controleer of deze wordt uitgevoerd.
9002 | De toepassingen die op de virtuele machine zijn geïnstalleerd, kunnen niet worden gedetecteerd. | VMware-hulpprogram ma's zijn mogelijk niet actief. | Installeer/installeer VMware-hulpprogram ma's in de VM en controleer of deze wordt uitgevoerd.
9003 | De toepassingen die op de server zijn geïnstalleerd, kunnen niet worden gedetecteerd. | Dit kan gebeuren als het besturings systeem dat op de-server wordt uitgevoerd, geen Windows of Linux is. | De detectie van geïnstalleerde toepassingen wordt alleen ondersteund voor Windows-en Linux-servers.
9004 | De toepassingen die op de server zijn geïnstalleerd, kunnen niet worden gedetecteerd. | Dit kan gebeuren als de virtuele machine is uitgeschakeld. | Als u geïnstalleerde toepassingen op de server wilt detecteren, moet u ervoor zorgen dat de virtuele machine is ingeschakeld.
9005 | De toepassingen die op de virtuele machine zijn geïnstalleerd, kunnen niet worden gedetecteerd. | Dit kan gebeuren als het besturings systeem dat op de virtuele machine wordt uitgevoerd, geen Windows of Linux is. | De detectie van geïnstalleerde toepassingen wordt alleen ondersteund voor Windows-en Linux-servers.
9006 | Kan de toepassingen die de server hebben geïnstalleerd, niet ophalen. | Dit kan gebeuren als de detectie agent niet goed werkt. | Het probleem moet in 24 uur automatisch worden opgelost. Als het probleem zich blijft voordoen, neemt u contact op met Microsoft Ondersteuning.
9007 | Kan de toepassingen die de server hebben geïnstalleerd, niet ophalen. | Dit kan gebeuren als de detectie agent niet goed werkt. | Het probleem moet in 24 uur automatisch worden opgelost. Als het probleem zich blijft voordoen, neemt u contact op met Microsoft Ondersteuning.
9008 | Kan de toepassingen die de server hebben geïnstalleerd, niet ophalen. | Het probleem kan optreden vanwege een interne fout.  | Het probleem moet in 24 uur automatisch worden opgelost. Als het probleem zich blijft voordoen, neemt u contact op met Microsoft Ondersteuning.
9009 | Kan de toepassingen die de server hebben geïnstalleerd, niet ophalen. | Het probleem kan zich voordoen als de UAC-instellingen (Windows User Account Control) op de server beperkend zijn en de detectie van geïnstalleerde toepassingen niet mogelijk maken. | Zoek de instellingen voor Gebruikersaccountbeheer op de server en configureer de UAC-instelling op de server op een van de twee lagere niveaus.
9010 | Kan de toepassingen die de server hebben geïnstalleerd, niet ophalen. | Het probleem kan optreden vanwege een interne fout.  | Het probleem moet in 24 uur automatisch worden opgelost. Als het probleem zich blijft voordoen, neemt u contact op met Microsoft Ondersteuning.
8084 | Kan geen toepassingen detecteren vanwege VMware-fout: <Exception from VMware> | Het Azure Migrate-apparaat gebruikt VMware-Api's om toepassingen te detecteren. Dit probleem kan optreden vanwege een uitzonde ring die is opgetreden door vCenter Server tijdens het detecteren van toepassingen. De fout melding van VMware wordt weer gegeven in het fout bericht dat wordt weer gegeven in de portal. | Raadpleeg de [documentatie van VMware](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html), zoek naar het fout bericht en volg de stappen voor probleem oplossing in het VMware-artikel om het probleem op te lossen. Neem contact op met Microsoft Ondersteuning als u het probleem nog steeds niet kunt oplossen.


## <a name="fix-assessment-readiness"></a>Gereedheid voor evaluatie oplossen

Los problemen met de voorbereidings voorbereiding op als volgt:

**Name** | **Holpen**
--- | ---
Niet-ondersteund opstart type | Azure biedt geen ondersteuning voor Vm's met een EFI-opstart type. U wordt aangeraden het opstart type te converteren naar BIOS voordat u een migratie uitvoert. <br/><br/>U kunt Azure Migrate server migratie gebruiken om de migratie van dergelijke Vm's te verwerken. Tijdens de migratie wordt het opstart type van de VM naar het BIOS geconverteerd.
Voorwaardelijk ondersteund Windows-besturings systeem | Het besturings systeem heeft de eind datum van de ondersteuning door gegeven en heeft een aangepaste ondersteunings overeenkomst (CSA) nodig voor [ondersteuning in azure](https://aka.ms/WSosstatement). Overweeg om te upgraden voordat u naar Azure migreert.
Niet-ondersteund Windows-besturings systeem | Azure ondersteunt alleen [geselecteerde versies van Windows-besturings systemen](https://aka.ms/WSosstatement). U kunt de machine upgraden voordat u naar Azure migreert.
Voorwaardelijk goedgekeurd Linux-besturings systeem | Azure bevestigt alleen [geselecteerde Linux-besturingssysteem versies](../virtual-machines/linux/endorsed-distros.md). U kunt de machine upgraden voordat u naar Azure migreert.
Niet-goedgekeurd Linux-besturings systeem | De machine kan worden gestart in azure, maar Azure biedt geen ondersteuning voor het besturings systeem. U kunt een upgrade uitvoeren naar een [officiële versie van Linux](../virtual-machines/linux/endorsed-distros.md) voordat u naar Azure migreert.
Onbekend besturings systeem | Het besturings systeem van de virtuele machine is opgegeven als ' andere ' in vCenter Server. Dit gedrag blokkeert Azure Migrate van het controleren van de Azure-gereedheid van de virtuele machine. Zorg ervoor dat het besturings systeem wordt [ondersteund](https://aka.ms/azureoslist) door Azure voordat u de computer migreert.
Niet-ondersteunde bits versie | Vm's met een 32-bits besturings systeem kunnen worden opgestart in azure, maar het wordt aanbevolen dat u een upgrade uitvoert naar 64-bits voordat u naar Azure migreert.
Vereist een micro soft Visual Studio-abonnement | Op de computer wordt een Windows client-besturings systeem uitgevoerd, dat alleen wordt ondersteund via een Visual Studio-abonnement.
Er is geen VM gevonden voor de vereiste opslag prestaties | De opslag prestaties (invoer/uitvoer-bewerkingen per seconde [IOPS] en door Voer) die vereist zijn voor de computer, overschrijden de ondersteuning voor Azure-VM'S. Verminder de opslag vereisten voor de machine vóór de migratie.
Er is geen VM gevonden voor de vereiste netwerk prestaties | De netwerk prestaties (in/uit) die vereist zijn voor de computer, overschrijden de ondersteuning voor Azure-VM'S. Verminder de netwerk vereisten voor de computer.
De virtuele machine is niet gevonden op de opgegeven locatie | Gebruik een andere doel locatie vóór de migratie.
Een of meer niet-geschikte schijven | Een of meer schijven die zijn gekoppeld aan de virtuele machine voldoen niet aan de vereisten van Azure. Één<br/><br/> Azure Migrate: Server analyse biedt momenteel geen ondersteuning voor Ultra-SSD schijven en evalueert de schijven op basis van de schijf limieten voor Premium Managed disks (32 TB).<br/><br/> Zorg ervoor dat de grootte van de schijf < 64 TB (ondersteund door Ultra-SSD schijven) voor elke schijf die aan de VM is gekoppeld.<br/><br/> Als dat niet het geval is, vermindert u de schijf grootte voordat u naar Azure migreert, of gebruikt u meerdere schijven in Azure en [stript u deze samen](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) om hogere opslag limieten te krijgen. Zorg ervoor dat de prestaties (IOPS en door Voer) die nodig zijn voor elke schijf worden ondersteund door door Azure [beheerde virtuele-machine schijven](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits).
Een of meer niet-geschikte netwerk adapters. | Verwijder ongebruikte netwerk adapters van de machine voordat de migratie wordt gebruikt.
Aantal schijven overschrijdt de limiet | Verwijder ongebruikte schijven van de machine vóór de migratie.
De schijf grootte overschrijdt de limiet | Azure Migrate: Server analyse biedt momenteel geen ondersteuning voor Ultra-SSD schijven en controleert de schijven op basis van Premium-schijf limieten (32 TB).<br/><br/> Azure ondersteunt echter schijven met een grootte van Maxi maal 64 TB (ondersteund door Ultra-SSD schijven). Verklein schijven tot minder dan 64 TB vóór de migratie of gebruik meerdere schijven in Azure en [strip deze samen](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) om hogere opslag limieten te krijgen.
De schijf is niet beschikbaar op de opgegeven locatie | Zorg ervoor dat de schijf zich op de doel locatie bevindt voordat u migreert.
De schijf is niet beschikbaar voor de opgegeven redundantie | De schijf moet het opslag type redundantie gebruiken dat is gedefinieerd in de instellingen voor evaluatie (standaard LRS).
Kan de schijf geschiktheid niet bepalen vanwege een interne fout | Probeer een nieuwe evaluatie voor de groep te maken.
VM met vereiste kernen en geheugen niet gevonden | Azure kan geen geschikt VM-type vinden. Verminder het geheugen en het aantal kernen van de on-premises machine voordat u migreert.
Kan de geschiktheid van de virtuele machine niet bepalen vanwege een interne fout | Probeer een nieuwe evaluatie voor de groep te maken.
Kan de geschiktheid van een of meer schijven niet bepalen vanwege een interne fout | Probeer een nieuwe evaluatie voor de groep te maken.
Kan de geschiktheid voor een of meer netwerk adapters niet bepalen vanwege een interne fout | Probeer een nieuwe evaluatie voor de groep te maken.

## <a name="linux-vms-are-conditionally-ready"></a>Linux-Vm's zijn ' voorwaardelijk gereed '

Server Assessment markeert Linux Vm's als ' voorwaardelijk gereed ' vanwege een bekende onderbreking in Server evaluatie.

- De tussen ruimte voor komt dat de secundaire versie van het Linux-besturings systeem dat is geïnstalleerd op de on-premises Vm's kan worden gedetecteerd.
- Voor RHEL 6,10 wordt momenteel alleen RHEL 6 als de versie van het besturings systeem gedetecteerd.
-  Omdat Azure alleen specifieke versies van Linux afschrijft, zijn de virtuele Linux-machines momenteel gemarkeerd als voorwaardelijk gereed in Server evaluatie.
- U kunt bepalen of het Linux-besturings systeem dat wordt uitgevoerd op de on-premises VM, wordt goedgekeurd in azure door [Azure Linux-ondersteuning](https://aka.ms/migrate/selfhost/azureendorseddistros)te controleren.
-  Nadat u de geviseerde distributie hebt gecontroleerd, kunt u deze waarschuwing negeren.

## <a name="azure-skus-exceed-on-premises-sizing"></a>Azure-Sku's overschrijden on-premises grootte

Azure Migrate server-evaluatie kan Azure VM Sku's aanbevelen met meer kernen en geheugen dan de huidige on-premises toewijzing op basis van het type evaluatie:


- De aanbeveling van de VM-SKU is afhankelijk van de evaluatie-eigenschappen.
- Dit wordt beïnvloed door het type beoordeling dat u uitvoert in Server beoordeling: op *basis van prestaties*of *als on-premises*.
- Voor evaluaties op basis van prestaties worden de gebruiks gegevens van de on-premises Vm's (CPU, geheugen, schijf en netwerk gebruik) beschouwd om de juiste doel-VM-SKU voor uw on-premises Vm's te bepalen. Er wordt ook een comfort factor toegevoegd bij het bepalen van effectief gebruik.
- Voor on-premises grootte worden de prestatie gegevens niet in overweging genomen en wordt de doel-SKU aanbevolen op basis van on-premises toewijzing.

Om te laten zien hoe dit kan invloed hebben op aanbevelingen, gaan we een voor beeld doen:

We hebben een on-premises VM met vier kernen en acht GB geheugen, met een CPU-gebruik van 50% en een geheugen gebruik van 50% en een opgegeven comfort factor van 1,3.

-  Als de evaluatie **on-premises**is, wordt een Azure VM-SKU met 4 kernen en 8 GB geheugen aanbevolen.
- Als de evaluatie op prestaties gebaseerd is, op basis van effectief CPU-en geheugen gebruik (50% van 4 kernen * 1,3 = 2,6 kernen en 50% van 8 GB geheugen * 1,3 = 5,3 GB geheugen), de goedkoopste VM-SKU van vier kernen (dichtstbijgelegen ondersteunde kernen) en 8 GB aan geheugen (dichtstbijzijnde ondersteund geheugen grootte) wordt aanbevolen.
- Meer [informatie](concepts-assessment-calculation.md#sizing) over de beoordelings grootte.

## <a name="azure-disk-skus-bigger-than-on-premises"></a>Azure Disk Sku's groter dan on-premises

Azure Migrate server beoordeling kan een grotere schijf aanbevelen op basis van het type evaluatie.
- De schijf grootte in Server analyse is afhankelijk van twee evaluatie-eigenschappen: grootte criteria en opslag type.
- Als de grootte criteria **op basis van prestaties**zijn en het opslag type is ingesteld op **automatisch**, worden de IOPS-en doorvoer waarden van de schijf overwogen bij het identificeren van het doel schijf type (Standard-HDD, Standard-SSD of Premium). Er wordt vervolgens een schijf-SKU van het schijf type aanbevolen en de aanbeveling houdt rekening met de grootte vereisten van de on-premises schijf.
- Als de grootte criteria **op basis van prestaties**zijn en het opslag type **Premium**is, wordt een Premium-schijf-SKU in azure aanbevolen op basis van de IOPS-, doorvoer-en grootte vereisten van de on-premises schijf. Dezelfde logica wordt gebruikt voor het uitvoeren van schijf grootte wanneer de grootte criteria **op locatie** zijn en het opslag type is **Standard-HDD**, **Standard-SSD**of **Premium**.

Als u bijvoorbeeld een on-premises schijf met 32 GB geheugen hebt, maar de geaggregeerde Lees-en schrijf-IOPS voor de schijf 800 IOPS is, raadt server beoordeling een Premium-schijf aan (vanwege de hogere IOPS-vereisten) en wordt vervolgens een schijf-SKU aanbevolen die de r kan ondersteunen IOPS en grootte van ereist. In dit voorbeeld komen we dan uit bij P15 (256 GB, 1100 IOPS). Hoewel de grootte die de on-premises schijf vereist, 32 GB was, raadt server evaluatie een grotere schijf aan vanwege de hoge IOPS-vereiste van de on-premises schijf.

## <a name="fix-percentage-of-utilized-core-or-memory-missing"></a>Fix: percentage gegebruikte kern geheugen of ontbrekend RAM

Server Assessment rapporteert "PercentageOfCoresUtilizedMissing" of "PercentageOfMemoryUtilizedMissing" wanneer het Azure Migrate apparaat geen prestatie gegevens kan verzamelen voor de relevante on-premises Vm's.

- Dit kan gebeuren als de virtuele machines tijdens de evaluatie duur zijn uitgeschakeld. Het apparaat kan geen prestatie gegevens voor een virtuele machine verzamelen wanneer deze is uitgeschakeld.
- Als alleen de geheugen items ontbreken en u probeert virtuele Hyper-V-machines te evalueren, controleert u of u dynamisch geheugen hebt ingeschakeld op deze virtuele machines. Er is alleen een bekend probleem voor virtuele Hyper-V-machines, waarin een Azure Migrate apparaat geen geheugen gebruiks gegevens kan verzamelen voor Vm's waarvoor geen dynamisch geheugen is ingeschakeld.
- Als een van de prestatie meter items ontbreekt, wordt de analyse van Azure Migrate server terugvallen op de toegewezen kernen en het geheugen en wordt een bijbehorende VM-grootte aanbevolen.

## <a name="is-the-vm-os-license-cost-included-in-cost-assessment"></a>Zijn de licentie kosten voor het VM-besturings systeem opgenomen in de kosten beoordeling?

Azure Migrate server-evaluatie beschouwt momenteel alleen de licentie kosten van het besturings systeem voor Windows-computers. De licentie kosten voor Linux-machines worden momenteel niet in overweging genomen.

## <a name="performance-history-and-percentile-use"></a>Prestatie geschiedenis en percentiel gebruik

Deze eigenschappen zijn alleen van toepassing op de grootte van de prestaties in Azure Migrate server-evaluatie.

Met Server Assessment worden doorlopend prestatiegegevens van on-premises machines verzameld, die vervolgens worden gebruikt om SKU's voor VM en schijf aan te bevelen in Azure. Deze prestatie gegevens worden als volgt verzameld door server evaluatie:
- Het Azure Migrate apparaat doorlopend de on-premises omgeving profielen om gegevens in realtime te gebruiken voor VMware-Vm's en om de 30 seconden voor Hyper-V-Vm's.
- Het apparaat rolt de voor beelden van 20 of 30 seconden samen om één gegevens punt voor elke tien minuten te maken. Als u het ene gegevens punt wilt maken, selecteert het apparaat de piek waarde van alle voor beelden van 20 en 30 seconden en verzendt deze vervolgens naar Azure.
- Wanneer u een evaluatie maakt in Server Assessment, op basis van de prestatieduur en de percentielwaarde van de prestatiegeschiedenis, wordt er een waarde vastgesteld die het daadwerkelijke gebruik voorstelt. Als de prestatie geschiedenis bijvoorbeeld één week is en het percentiel gebruik 95e is, sorteert Azure Migrate alle tien minuten bemonsterings punten voor de laatste week in oplopende volg orde en selecteert vervolgens het 95e percentiel als de representatieve waarde.
- De 95e percentiel waarde zorgt ervoor dat u alle uitschieters negeert, die mogelijk worden opgenomen als u het 99e percentiel selecteert.
- Als u het piek gebruik voor de periode wilt kiezen en u geen uitschieters wilt missen, selecteert u het 99e percentiel voor percentiel gebruik.


## <a name="i-cant-find-dependency-visualization-for-azure-government"></a>Ik kan geen afhankelijkheids visualisatie vinden voor Azure Government

Azure Migrate is afhankelijk van Servicetoewijzing voor de visualisatie functionaliteit van de afhankelijkheid. Omdat Servicetoewijzing momenteel niet beschikbaar is in Azure Government, is deze functionaliteit niet beschikbaar in Azure Government.

## <a name="dependencies-dont-show-after-installing-agents"></a>Afhankelijkheden worden niet weer gegeven na de installatie van agents

Nadat u de afhankelijkheids visualisatie agenten hebt geïnstalleerd op on-premises Vm's, duurt Azure Migrate doorgaans 15-30 minuten om de afhankelijkheden in de portal weer te geven. Als u langer dan 30 minuten hebt gewacht, moet u ervoor zorgen dat de micro soft Monitoring Agent (MMA) verbinding kan maken met de Log Analytics-werk ruimte.

Voor Windows-Vm's:
1. Start MMA in het configuratie scherm.
2. Controleer in de **Eigenschappen van micro soft Monitoring Agent** > **Azure log Analytics (OMS)** of de **status** van de werk ruimte groen is.
3. Als de status niet groen is, probeert u de werk ruimte te verwijderen en toe te voegen aan MMA.

      ![Het dialoog venster Eigenschappen van MMA](./media/troubleshooting-general/mma-status.png)

Voor virtuele Linux-machines moet u ervoor zorgen dat de installatie opdrachten voor MMA en de afhankelijkheids agent zijn geslaagd.

## <a name="supported-mma-os"></a>Ondersteund MMA-besturings systeem

 Bekijk de ondersteunde [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)-en [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) -besturings systemen.

## <a name="supported-dependency-agent-os"></a>Ondersteund besturings systeem voor afhankelijkheids agent

Bekijk de ondersteunde [Windows-en Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) -besturings systemen.

## <a name="dependencies-for-more-than-an-hour"></a>Afhankelijkheden voor meer dan een uur

Hoewel u met Azure Migrate kunt terugkeren naar een bepaalde datum in de afgelopen maand, is de maximale duur waarvoor u de afhankelijkheden kunt visualiseren één uur.

U kunt bijvoorbeeld de functie tijd duur in de afhankelijkheids toewijzing gebruiken om afhankelijkheden voor gisteren weer te geven, maar u kunt ze alleen bekijken voor een periode van één uur.

U kunt echter Azure Monitor-Logboeken gebruiken om een langere duur van [de afhankelijkheids gegevens](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) op te vragen.

## <a name="i-cant-visualize-dependencies-for-groups-with-more-than-10-vms"></a>Ik kan afhankelijkheden voor groepen met meer dan 10 virtuele machines niet visualiseren

In Azure Migrate server-evaluatie kunt u [afhankelijkheden voor groepen](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) met Maxi maal tien vm's visualiseren. Voor grotere groepen wordt u aangeraden de Vm's te splitsen in kleinere groepen om afhankelijkheden te visualiseren.

## <a name="machines-show-install-agent-not-view-dependencies"></a>Computers ' agent installeren ' niet weer geven ' afhankelijkheden weer geven '

Na de migratie van computers met afhankelijkheids visualisatie ingeschakeld op Azure, kunnen computers de actie ' agent installeren ' in plaats van ' afhankelijkheden weer geven ' weer geven vanwege het volgende gedrag:


- Na de migratie naar Azure zijn on-premises machines uitgeschakeld en zijn er vergelijk bare Vm's in Azure. Deze machines halen een ander MAC-adres op.
- Computers kunnen ook een ander IP-adres hebben, op basis van het feit of u het on-premises IP-adres hebt behouden of niet.
- Als zowel MAC-als IP-adressen verschillen van on-premises, Azure Migrate de on-premises machines niet koppelen aan gegevens van Servicetoewijzing afhankelijkheid. In dit geval wordt de optie voor het installeren van de agent weer gegeven in plaats van het weer geven van afhankelijkheden.
- Na een test migratie naar Azure blijven on-premises machines ingeschakeld zoals verwacht. Gelijkwaardige machines die in Azure worden ingezet, halen een ander MAC-adres op en kunnen verschillende IP-adressen verkrijgen. Tenzij u uitgaand Azure Monitor logboek verkeer van deze computers blokkeert, Azure Migrate de on-premises machines niet koppelen aan gegevens van Servicetoewijzing afhankelijkheden, waardoor de optie voor het installeren van agents wordt weer gegeven in plaats van afhankelijkheden weer te geven.


## <a name="collect-network-traffic-logs-in-portal"></a>Logboeken voor netwerk verkeer verzamelen in Portal

Verzamel Logboeken als volgt:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
2. Druk op F12 om Ontwikkelhulpprogramma's te starten. Als dat nodig is, schakelt u de instelling **vermeldingen wissen bij navigatie** uit.
3. Selecteer het tabblad **netwerk** en begin met het vastleggen van netwerk verkeer:
   - In Chrome selecteert u **logboek behouden**. De opname moet automatisch worden gestart. Een rode cirkel geeft aan dat verkeer wordt vastgelegd. Als de rode cirkel niet wordt weer gegeven, selecteert u de zwarte cirkel om te starten.
   - In micro soft Edge en Internet Explorer moet de opname automatisch worden gestart. Als dat niet het geval is, selecteert u de groene knop afspelen.
4. Probeer de fout te reproduceren.
5. Nadat u de fout hebt aangetroffen tijdens het opnemen, stoppen met opnemen en opslaan van een kopie van de geregistreerde activiteit:
   - Klik in Chrome met de rechter muisknop en selecteer **Opslaan als har met inhoud**. Met deze actie worden de logboeken gecomprimeerd en geëxporteerd als een. har-bestand.
   - Selecteer in micro soft Edge of Internet Explorer de optie **vastgelegd verkeer exporteren** . Met deze actie wordt het logboek gecomprimeerd en geëxporteerd.
6. Selecteer het tabblad **console** om te controleren of er waarschuwingen of fouten zijn. Het console logboek opslaan:
   - Klik in Chrome met de rechter muisknop op een wille keurige plaats in het console logboek. Selecteer **Opslaan als**, om het logboek te exporteren en uit te voeren.
   - Klik in micro soft Edge of Internet Explorer met de rechter muisknop op de fouten en selecteer **Alles kopiëren**.
7. Ontwikkelhulpprogramma's sluiten.
