---
title: Problemen met Azure Migrate oplossen | Microsoft Docs
description: Hierin wordt een overzicht gegeven van bekende problemen in de Azure Migrate-service en tips voor het oplossen van veelvoorkomende fouten.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: raynew
ms.openlocfilehash: 7fea6d16c8846909a8ce9bb33aae74ce343018fa
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142327"
---
# <a name="troubleshoot-azure-migrate"></a>Problemen met Azure Migrate oplossen

[Azure migrate](migrate-services-overview.md) biedt een hub van hulpprogram ma's voor evaluatie en migratie, evenals onafhankelijke software leveranciers (ISV)-aanbiedingen van derden. Dit artikel helpt u bij het oplossen van fouten met Azure Migrate, Azure Migrate server-evaluatie en Azure Migrate server migratie.

## <a name="azure-migrate-project-issues"></a>Azure Migrate project problemen

### <a name="i-cant-find-my-existing-azure-migrate-project"></a>Ik kan mijn bestaande Azure Migrate-project niet vinden.

Er zijn [twee versies](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) van Azure Migrate. Afhankelijk van de versie waarin u het project hebt gemaakt, gebruikt u een van de volgende methoden:

* Als u op zoek bent naar een project dat is gemaakt met de vorige versie (oude ervaring) van Azure Migrate, volgt u deze stappen om het project te vinden:

    1. Ga naar het [Azure Portal](https://portal.azure.com)en zoek naar **Azure migrate**.
    2. Op het dash board van Azure Migrate ziet u een banner waarin de toegang tot oudere projecten wordt vermeld. Deze banner wordt alleen weer geven als u een project hebt gemaakt met de oude ervaring. Selecteer de banner.

       ![Toegang tot bestaande projecten](./media/troubleshooting-general/access-existing-projects.png)

    3. U ziet nu de lijst met bestaande projecten die zijn gemaakt met de vorige versie van Azure Migrate.

* Als u op zoek bent naar een project dat is gemaakt met de huidige versie (nieuwe ervaring), voert u de volgende stappen uit om het project te zoeken:

    1. Ga naar het [Azure Portal](https://portal.azure.com)en zoek naar **Azure migrate**.
    2. Ga op het Azure Migrate dash board naar de pagina **servers** in het linkerdeel venster en selecteer **wijzigen** in de rechter bovenhoek.

       ![Overschakelen naar een bestaand Azure Migrate project](./media/troubleshooting-general/switch-project.png)

    3. Selecteer het juiste abonnement en Azure Migrate project.

### <a name="how-do-i-create-a-second-azure-migrate-project"></a>Hoe kan ik een tweede Azure Migrate-project maken?

Volg deze stappen voor het maken van een nieuw Azure Migrate project:

1. Ga naar het [Azure Portal](https://portal.azure.com)en zoek naar **Azure migrate**.
2. Ga op het Azure Migrate dash board naar de pagina **servers** in het linkerdeel venster en selecteer **wijzigen** in de rechter bovenhoek.

   ![Azure Migrate project wijzigen](./media/troubleshooting-general/switch-project.png)

3. Als u een nieuw project wilt maken, selecteert u **hier klikken**.

   ![Een tweede Azure Migrate-project maken](./media/troubleshooting-general/create-new-project.png)

### <a name="which-azure-geographies-does-azure-migrate-support"></a>Welke Azure-geografies Azure Migrate ondersteunen?

Bekijk de lijsten voor [VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) en voor [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

### <a name="how-do-i-delete-azure-migrate-projects-and-associated-log-analytics-workspaces"></a>Azure Migrate projecten en gekoppelde Log Analytics-werk ruimten Hoe kan ik verwijderen?

Wanneer u een Azure Migrate project verwijdert, worden het migratie project *en* de meta gegevens over gedetecteerde computers verwijderd. Als u echter een Log Analytics-werk ruimte hebt gekoppeld aan het hulp programma voor Server evaluatie, wordt de Log Analytics-werk ruimte niet automatisch verwijderd. (Dezelfde Log Analytics-werk ruimte kan worden gebruikt voor meerdere use cases.) Als u ook de Log Analytics-werk ruimte wilt verwijderen, moet u dit hand matig doen:

1. Blader naar de Log Analytics werkruimte die aan het project is gekoppeld.
     * Als u het migratie project nog niet hebt verwijderd, kunt u de koppeling naar de werk ruimte vinden op de pagina overzicht van de server evaluatie in de sectie Essentials.

       ![De werk ruimte LA](./media/troubleshooting-general/loganalytics-workspace.png)

     * Als u het migratie project al hebt verwijderd, selecteert u **resource groepen** in het linkerdeel venster van de Azure Portal. Ga naar de resource groep waarin de werk ruimte is gemaakt en blader ernaar.
2. Volg de instructies in [een Azure log Analytics-werk ruimte verwijderen met de Azure Portal](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace).

### <a name="migration-project-creation-failed-with-a-requests-must-contain-user-identity-headers-error"></a>Het maken van het migratie project is mislukt met een fout bericht ' aanvragen moeten gebruikers identiteiten bevatten '.

Dit probleem kan zich voordoen voor gebruikers die geen toegang hebben tot de Azure Active Directory (Azure AD)-Tenant van de organisatie. Wanneer een gebruiker voor de eerste keer wordt toegevoegd aan een Azure AD-Tenant, ontvangt de gebruiker een e-mail uitnodiging om lid te worden van de Tenant. Gebruikers moeten de uitnodiging accepteren om aan de Tenant te kunnen toevoegen. Als u het e-mail bericht niet kunt zien, neemt u contact op met een gebruiker die al toegang heeft tot de Tenant en vraagt u de uitnodiging opnieuw te verzenden met behulp van de stappen die zijn opgegeven in uitnodigingen [opnieuw verzenden naar gast gebruikers](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users).

Nadat de uitnodigings-e-mail is ontvangen, moet u het e-mail bericht openen en de koppeling selecteren om de uitnodiging te accepteren. Vervolgens moet u zich afmelden bij de Azure Portal en zich opnieuw aanmelden. (Als u de browser vernieuwt, werkt deze niet.) Vervolgens kunt u beginnen met het maken van het migratie project.

## <a name="appliance-issues"></a>Problemen met het apparaat

### <a name="deployment-of-an-azure-migrate-appliance-for-vmware-failed-with-a-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry-error"></a>De implementatie van een Azure Migrate apparaat voor VMware is mislukt met een ' het door gegeven manifest bestand is ongeldig: Ongeldige OVF-manifest vermelding.

1. Controleer of het bestand van de Azure Migrate apparaat voor apparaten correct is gedownload door de hash-waarde te controleren. Zie voor meer informatie [VMware-Vm's voorbereiden voor evaluatie en migratie naar Azure](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). Als de hash-waarde niet overeenkomt, downloadt u het bestand van de eicellen opnieuw en voert u de implementatie opnieuw uit.
2. Als de implementatie nog steeds mislukt en u de VMware vSphere-client gebruikt om het OVF-bestand te implementeren, probeert u het te implementeren via de vSphere-webclient. Als de implementatie nog steeds mislukt, kunt u proberen een andere webbrowser te gebruiken.
3. Als u de vSphere-webclient gebruikt en deze probeert te implementeren op vCenter Server 6,5 of 6,7, probeert u de eicellen rechtstreeks op de ESXi-host te implementeren door de volgende stappen te volgen:
   - Maak rechtstreeks verbinding met de ESXi-host (in plaats van vCenter Server) met behulp van de webclient (https://<*IP-adres*van de host >/UI).
   - Ga naar **huisraad** > **inventaris**.
   - Selecteer de**OVF-sjabloon**voor het implementeren van **bestanden** > . Blader naar de eicellen en voltooi de implementatie.
4. Als de implementatie nog steeds mislukt, neemt u contact op met Azure Migrate ondersteuning.

### <a name="the-appliance-cant-connect-to-the-internet"></a>Het apparaat kan geen verbinding maken met internet.

Dit gedrag kan optreden wanneer de computer die u gebruikt zich achter een proxy bevindt. Zorg ervoor dat u de autorisatie referenties opgeeft als de proxy deze nodig heeft.
Als u een firewall proxy op basis van een URL gebruikt om de uitgaande connectiviteit te beheren, moet u deze vereiste Url's toevoegen aan een acceptatie lijst:

- [Server evaluatie voor VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
- [Server evaluatie voor Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
- [Server migratie voor VMware (zonder agent)](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
- [Server migratie voor VMware (op agent gebaseerd)](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
- [Server migratie voor Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

Als u een interceptie proxy gebruikt om verbinding te maken met internet, moet u het proxy certificaat importeren op de apparaat-VM. U kunt het proxy certificaat importeren door de stappen in [Azure migrate apparaat](https://docs.microsoft.com/azure/migrate/concepts-collector)te volgen.

### <a name="error-802-date-and-time-synchronization-error"></a>Fout 802: Synchronisatie fout voor datum en tijd.

De klok van de server is mogelijk niet gesynchroniseerd met de huidige tijd van meer dan vijf minuten. Wijzig de klok tijd op de Collector-VM zodat deze overeenkomt met de huidige tijd, als volgt:

1. Open een opdracht prompt met beheerders rechten op de VM.
2. Voer **w32tm/TZ**uit om de tijd zone te controleren.
3. Voer **w32tm/resync**uit om de tijd te synchroniseren.


###  <a name="connection-failed-error-unabletoconnecttoserver"></a>De verbinding is mislukt. Fout: UnableToConnectToServer.

Mogelijk kunt u geen verbinding maken met vCenter Server *servername*. com: 9443. De fout gegevens geven aan dat er geen eind punt luistert op https://*servername*. com: 9443/SDK die het bericht kan accepteren.

In dit geval controleert u of u de nieuwste versie van het Collector-apparaat uitvoert. Als dat niet het geval is, moet u het apparaat bijwerken naar de [nieuwste versie](https://docs.microsoft.com/azure/migrate/concepts-collector).

Als het probleem zich nog steeds voordoet in de meest recente versie, kan de Collector machine mogelijk de opgegeven vCenter Server naam niet omzetten of is de opgegeven poort onjuist. Als de poort niet is opgegeven, probeert de Collector standaard verbinding te maken met poort nummer 443.

1. Ping *Server naam*. com van de Collector-computer.
2. Als stap 1 mislukt, probeert u verbinding te maken met de vCenter-Server met behulp van het IP-adres.
3. Bepaal het juiste poortnummer om verbinding te maken met de vCenter-server.
4. Controleer of de vCenter-Server actief is.


### <a name="the-appliance-might-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60052"></a>Het apparaat is mogelijk niet geregistreerd voor het Azure Migrate project (fout-ID: 60052).

Deze fout treedt op als het Azure-account dat wordt gebruikt om het apparaat te registreren, onvoldoende machtigingen heeft. Zorg ervoor dat het Azure-gebruikers account dat wordt gebruikt om het apparaat te registreren, ten minste Inzender machtigingen heeft voor het abonnement. Meer [informatie](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) over de vereiste Azure-rollen en-machtigingen.

### <a name="the-appliance-might-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60039"></a>Het apparaat is mogelijk niet geregistreerd voor het Azure Migrate project (fout-ID: 60039).

Het Azure Migrate project dat is geselecteerd om het apparaat te registreren, is mogelijk niet gevonden. In dit geval mislukt de registratie. Ga naar de Azure Portal en controleer of het project bestaat in de resource groep. Als het project niet bestaat, maakt u een nieuw Azure Migrate project in de resource groep en registreert u het apparaat opnieuw. [Meer informatie](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) over het maken van een nieuw Azure migrate-project.

### <a name="an-azure-key-vault-management-operation-failed-error-id-60030-60031"></a>Een Azure Key Vault-beheer bewerking is mislukt (fout-ID: 60030, 60031).

Zorg ervoor dat het Azure-gebruikers account dat wordt gebruikt om het apparaat te registreren, ten minste Inzender machtigingen heeft voor het abonnement. Zorg er ook voor dat het account toegang heeft tot de sleutel kluis die is opgegeven in het fout bericht en voer de bewerking opnieuw uit. Neem contact op met micro soft ondersteuning als het probleem zich blijft voordoen. Meer [informatie](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) over de vereiste Azure-rollen en-machtigingen.

### <a name="discovery-couldnt-be-initiated-because-of-an-error-the-operation-failed-for-the-specified-list-of-hosts-or-clusters-error-id-60028"></a>De detectie kan vanwege een fout niet worden gestart. De bewerking is mislukt voor de opgegeven lijst met hosts of clusters (fout-ID: 60028).

Detectie kan niet worden gestart op de hosts die in de fout melding worden weer gegeven vanwege een probleem bij het openen of ophalen van VM-gegevens. De rest van de hosts is toegevoegd. Voeg de hosts die in de fout zijn vermeld opnieuw toe met behulp van de optie **host toevoegen** . Als er een validatie fout optreedt, raadpleegt u de richt lijnen voor herstel om de fouten op te lossen en probeert u het opnieuw met de optie **detectie opslaan en starten** .

### <a name="an-azure-ad-operation-failed-the-error-occurred-while-creating-or-updating-the-azure-ad-application-error-id-60025"></a>Een Azure AD-bewerking is mislukt. De fout is opgetreden tijdens het maken of bijwerken van de Azure AD-toepassing (fout-ID: 60025).

Deze fout treedt op wanneer het Azure-gebruikersaccount dat is gebruikt om de detectie te initiëren, verschilt van het account dat is gebruikt om het apparaat te registreren. U kunt een van de volgende handelingen uitvoeren:
1. Zorg ervoor dat het gebruikers account dat de detectie initieert, hetzelfde is als dat waarmee het apparaat wordt geregistreerd.
1. Geef toegangs machtigingen voor AAD-toepassingen op voor het andere gebruikers account waarvoor de detectie bewerking is mislukt.
1. Verwijder de resource groep die u eerder hebt gemaakt voor Azure Migrate project en maak een nieuwe resource groep om opnieuw te starten.

Meer [informatie](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) over Aad-toepassings machtigingen.

## <a name="discovery-issues"></a>Problemen met detectie

### <a name="i-started-discovery-but-dont-see-the-discovered-vms-on-the-azure-portal-the-server-assessment-and-server-migration-tiles-show-a-status-of-discovery-in-progress"></a>Ik heb de detectie gestart, maar de gedetecteerde Vm's worden niet weer geven op de Azure Portal. De tegels voor **Server evaluatie** en **server migratie** geven de status ' detectie wordt uitgevoerd ' weer.
Nadat u de detectie van het apparaat hebt gestart, moet u enige tijd op de gedetecteerde computers laten zien op het Azure Portal. Het duurt ongeveer 15 minuten voor een VMware-detectie en ongeveer 2 minuten per toegevoegde host voor een Hyper-V-detectie. Als de detectie wordt uitgevoerd, zelfs na deze wacht tijden, selecteert u **vernieuwen** op het tabblad **servers** . Hier wordt het aantal gedetecteerde servers weer gegeven in de tegels server evaluatie en server migratie.


### <a name="im-using-an-appliance-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-shown-in-the-portal"></a>Ik gebruik een apparaat dat de on-premises omgeving voortdurend detecteert, maar de virtuele machines die in mijn on-premises omgeving worden verwijderd, worden nog steeds weer gegeven in de portal.

Het duurt Maxi maal 30 minuten voordat de detectie gegevens die door het apparaat zijn verzameld, worden weer gegeven in de portal. Als u na 30 minuten geen actuele informatie ziet, vernieuwt u de gegevens met de volgende stappen:

1. Ga naar **servers** > **Azure migrate server-evaluatie**en selecteer **overzicht**.
2. Selecteer onder **beheren**de optie **status van agent**
3. Selecteer **agent vernieuwen**. U ziet deze optie onder de lijst met agents.
4. Wacht tot de vernieuwings bewerking is voltooid. U ziet nu actuele informatie over uw Vm's.

### <a name="i-dont-see-the-latest-information-on-the-on-premise-vms-on-the-portal"></a>Ik zie de meest recente informatie niet op de on-premises virtuele machines in de portal.

Het duurt Maxi maal 30 minuten voordat de detectie gegevens die door het apparaat zijn verzameld, worden weer gegeven op de portal. Als u na 30 minuten geen actuele informatie ziet, vernieuwt u de gegevens met de volgende stappen:

1. Ga naar **servers** > **Azure migrate server-evaluatie**en selecteer **overzicht**.
2. Selecteer onder **beheren**de optie **status van agent**
3. Selecteer **agent vernieuwen**. U ziet deze optie onder de lijst met agents.
4. Wacht tot de vernieuwings bewerking is voltooid. U ziet nu actuele informatie over uw Vm's.

### <a name="cant-connect-to-a-host-or-cluster-because-the-server-name-cant-be-resolved-winrm-error-code-0x803381b9-error-id-50004"></a>Kan geen verbinding maken met een host of cluster omdat de server naam niet kan worden omgezet. WinRM-fout code: 0x803381B9 (fout-ID: 50004).
Deze fout treedt op als de Azure DNS-service voor het apparaat het cluster of de hostnaam die u hebt ingevoerd, niet kan omzetten. Als u deze fout op het cluster ziet, moet u de Fully Qualified Domain Name (FQDN) van het cluster opgeven.

U ziet deze fout mogelijk ook voor hosts in een cluster. In dit geval kan het apparaat verbinding maken met het cluster, maar retourneert het cluster hostnamen die geen FQDN-namen zijn.

Om deze fout op te lossen, werkt u het hosts-bestand op het apparaat bij door een toewijzing van het IP-adres en de hostnamen toe te voegen:
1. Open Klad blok als Administrator. Open vervolgens het C:\Windows\System32\Drivers\etc\hosts-bestand.
2. Voeg het IP-adres en de hostnaam toe aan een rij. Herhaal deze stap voor elke host of elk cluster waar u deze fout ziet.
3. Opslaan en sluiten van het hosts-bestand.
4. U kunt controleren of het apparaat verbinding kan maken met de hosts met behulp van de app voor het beheren van het apparaat. Na 30 minuten zou u de meest recente informatie voor deze hosts op het Azure Portal moeten kunnen zien.


## <a name="assessment-issues"></a>Evaluatieproblemen

### <a name="azure-readiness-issues"></a>Problemen met Azure-gereedheid

Probleem | Herstel
--- | ---
Niet-ondersteund opstarttype | Azure biedt geen ondersteuning voor Vm's met een EFI-opstart type. U wordt aangeraden het opstart type te converteren naar BIOS voordat u een migratie uitvoert. <br/><br/>U kunt Azure Migrate server migratie gebruiken om de migratie van dergelijke Vm's te verwerken. Tijdens de migratie wordt het opstart type van de VM naar het BIOS geconverteerd.
Voorwaardelijk ondersteund Windows-besturings systeem | Het besturings systeem heeft de end-of-support-datum door gegeven en heeft een aangepaste ondersteunings overeenkomst (CSA) nodig voor [ondersteuning in azure](https://aka.ms/WSosstatement). Overweeg het besturings systeem te upgraden voordat u naar Azure migreert.
Niet-ondersteund Windows-besturingssysteem | Azure ondersteunt alleen [geselecteerde versies van Windows-besturings systemen](https://aka.ms/WSosstatement). Overweeg het besturings systeem van de machine te upgraden voordat u naar Azure migreert.
Voorwaardelijk goedgekeurd Linux-besturingssysteem | Azure bevestigt alleen [geselecteerde Linux-besturingssysteem versies](../virtual-machines/linux/endorsed-distros.md). Overweeg het besturings systeem van de machine te upgraden voordat u naar Azure migreert.
Niet-goedgekeurd Linux-besturingssysteem | De machine kan worden gestart in azure, maar Azure biedt geen ondersteuning voor het besturings systeem. Overweeg het besturings systeem bij te werken naar een [officiële versie van Linux](../virtual-machines/linux/endorsed-distros.md) voordat u naar Azure migreert.
Onbekend besturings systeem | Het besturings systeem van de virtuele machine is opgegeven als ' andere ' in vCenter Server. Dit gedrag blokkeert Azure Migrate van het controleren van de Azure-gereedheid van de virtuele machine. Zorg ervoor dat het besturings systeem van de machine door Azure wordt [ondersteund](https://aka.ms/azureoslist) voordat u de computer migreert.
Niet-ondersteunde hoeveelheid bits van besturingssysteem | Vm's met een 32-bits besturings systeem kunnen worden opgestart in azure, maar we raden u aan het besturings systeem van de virtuele machine bij te werken naar 64-bits voordat u naar Azure migreert.
Vereist een micro soft Visual Studio-abonnement | Op de computer wordt een Windows-client besturingssysteem uitgevoerd dat alleen wordt ondersteund via een Visual Studio-abonnement.
Er is geen VM gevonden voor de vereiste opslag prestaties | De opslag prestaties (invoer/uitvoer-bewerkingen per seconde [IOPS] en door Voer) die vereist zijn voor de computer, overschrijden de ondersteuning voor Azure-VM'S. Verminder de opslag vereisten voor de machine vóór de migratie.
Er is geen VM gevonden voor de vereiste netwerk prestaties | De netwerk prestaties (in/uit) die vereist zijn voor de computer, overschrijden de ondersteuning voor Azure-VM'S. Verminder de netwerk vereisten voor de computer.
De virtuele machine is niet gevonden op de opgegeven locatie | Gebruik een andere doel locatie vóór de migratie.
Een of meer ongeschikte schijven | Een of meer schijven die zijn gekoppeld aan de virtuele machine voldoen niet aan de vereisten van Azure. Azure Migrate: Server analyse biedt momenteel geen ondersteuning voor Ultra-SSD schijven en evalueert de schijven op basis van de schijf limieten voor Premium Managed disks (32 TB).  Voor elke schijf die aan de VM is gekoppeld, moet u ervoor zorgen dat de grootte van de schijf < 64 TB (ondersteund door Ultra-SSD schijven), als dat niet het geval is, vermindert u de schijf grootte voordat u naar Azure migreert of gebruikt u meerdere schijven in Azure en koppelt u [deze samen](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) om hogere opslag limieten te krijgen. Zorg ervoor dat de prestaties (IOPS en door Voer) die nodig zijn voor elke schijf worden ondersteund door door Azure [beheerde virtuele-machine schijven](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits).
Een of meer niet-geschikte netwerk adapters. | Verwijder ongebruikte netwerk adapters van de machine voordat de migratie wordt gebruikt.
Het aantal schijven overschrijdt de limiet | Verwijder ongebruikte schijven van de machine vóór de migratie.
De schijfgrootte overschrijdt de limiet | Azure Migrate: Server analyse biedt momenteel geen ondersteuning voor Ultra-SSD-schijven en evalueert de schijven op basis van Premium-schijf limieten (32 TB). Azure ondersteunt echter schijven met een grootte van Maxi maal 64 TB (ondersteund door Ultra-SSD schijven). Verklein schijven tot minder dan 64 TB vóór de migratie of gebruik meerdere schijven in Azure en [strip deze samen](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) om hogere opslag limieten te krijgen.
De schijf is niet beschikbaar op de opgegeven locatie | Zorg ervoor dat de schijf zich op de doel locatie bevindt voordat u migreert.
De schijf is niet beschikbaar voor de opgegeven redundantie | De schijf moet het opslag type redundantie gebruiken dat is gedefinieerd in de instellingen voor evaluatie (standaard LRS).
Kan de schijf geschiktheid niet bepalen vanwege een interne fout | Probeer een nieuwe evaluatie voor de groep te maken.
VM met vereiste kernen en geheugen niet gevonden | Azure kan geen geschikt VM-type vinden. Verminder het geheugen en het aantal kernen van de on-premises machine voordat u migreert.
Kan de geschiktheid van de virtuele machine niet bepalen vanwege een interne fout | Probeer een nieuwe evaluatie voor de groep te maken.
Kan de geschiktheid van een of meer schijven niet bepalen vanwege een interne fout | Probeer een nieuwe evaluatie voor de groep te maken.
Kan de geschiktheid voor een of meer netwerk adapters niet bepalen vanwege een interne fout | Probeer een nieuwe evaluatie voor de groep te maken.

### <a name="i-cant-specify-enterprise-agreement-ea-as-an-azure-offer-in-the-assessment-properties"></a>Ik kan Enterprise Agreement (EA) niet opgeven als een Azure-aanbieding in de beoordelings eigenschappen.
Azure Migrate server beoordeling biedt momenteel geen ondersteuning voor op prijzen gebaseerd op basis van Enterprise Agreement (EA). U kunt deze beperking omzeilen door **betalen** naar gebruik te gebruiken als de Azure-aanbieding en de eigenschap **Discount** te gebruiken om een aangepaste korting op te geven die u ontvangt. [Lees meer over hoe u een evaluatie kunt aanpassen](https://aka.ms/migrate/selfhelp/eapricing).

### <a name="why-does-server-assessment-mark-my-linux-vms-conditionally-ready"></a>Waarom markeert de server evaluatie mijn Linux-Vm's voorwaardelijk?
Er is een bekende tussen ruimte in de server evaluatie waarmee wordt voor komen dat de secundaire versie van het Linux-besturings systeem dat is geïnstalleerd op de on-premises virtuele machines (bijvoorbeeld voor RHEL 6,10, de huidige server evaluatie alleen RHEL 6 als de versie van het besturings systeem detecteert). Omdat Azure alleen specifieke versies van Linux afschrijft, zijn de virtuele Linux-machines momenteel gemarkeerd als voorwaardelijk gereed in Server evaluatie. U kunt bepalen of het Linux-besturings systeem dat wordt uitgevoerd op de on-premises VM, wordt goedgekeurd in azure door de [Azure Linux](https://aka.ms/migrate/selfhost/azureendorseddistros)-ondersteunings documentatie te bekijken. Nadat u de geviseerde distributie hebt gecontroleerd, kunt u deze waarschuwing negeren.

### <a name="why-does-the-vm-sku-recommended-by-server-assessment-have-more-cores-and-more-memory-than-whats-allocated-on-premises"></a>Waarom heeft de virtuele machine-SKU die door de server bepaling wordt aanbevolen, meer kernen en meer geheugen dan er on-premises worden toegewezen?
De aanbeveling voor VM-SKU in Server Assessment is afhankelijk van de evaluatie-eigenschappen. U kunt twee soorten evaluaties maken in Server evaluatie: Op *basis van prestaties* en *on-premises*. Voor evaluaties op basis van prestaties worden de gebruiks gegevens van de on-premises Vm's (CPU, geheugen, schijf en netwerk gebruik) beschouwd om de juiste doel-VM-SKU voor uw on-premises Vm's te bepalen. Daarnaast wordt de comfort factor gebruikt voor het bepalen van effectief gebruik voor de grootte van de prestaties. Voor on-premises grootte worden de prestatie gegevens niet in overweging genomen en wordt een doel-SKU aanbevolen op basis van wat lokaal is toegewezen.

Stel bijvoorbeeld dat er een on-premises VM is met 4 kernen en 8 GB aan geheugen met een CPU-gebruik van 50% en een geheugen gebruik van 50% en een comfort factor van 1,3 is opgegeven in de evaluatie. Als de grootte criteria van de evaluatie **on-premises**zijn, wordt een Azure VM-SKU met 4 kernen en 8 GB aan geheugen aanbevolen.

Stel echter dat de grootte criteria op basis van prestaties zijn. Op basis van het effectief CPU-en geheugen gebruik (50% van de 4 kernen * 1,3 = 2,6 kernen en 50% van 8 GB geheugen * 1,3 = 5,3 GB geheugen), wordt de goedkoopste VM-SKU van vier kernen (dichtstbijgelegen ondersteunde kernen) en 8 GB aan geheugen (dichtstbijgelegen ondersteunde geheugen grootte) aanbevolen. [Lees meer informatie over hoe Server Assessment de grootte aanpast. ](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing).

### <a name="why-is-the-disk-sku-recommended-by-server-assessment-bigger-than-whats-allocated-on-premises"></a>Waarom wordt de schijf-SKU aanbevolen door server evaluatie die groter is dan wat er on-premises is toegewezen?
De schijf grootte in Server analyse is afhankelijk van twee evaluatie-eigenschappen: grootte criteria en opslag type. Als de grootte criteria **op basis van prestaties** zijn en het opslag type is ingesteld op **automatisch**, worden de IOPS-en doorvoer waarden van de schijf gezien als identificatie van het doel schijf type (Standard-HDD, Standard-SSD of Premium-schijven). Een schijf-SKU van het schijf type wordt vervolgens aanbevolen en deze aanbeveling houdt rekening met de grootte vereisten van de on-premises schijf. Als de grootte criteria **op basis van prestaties**zijn en het opslag type **Premium**is, wordt een Premium-schijf-SKU in azure aanbevolen op basis van de IOPS-, doorvoer-en grootte vereisten van de on-premises schijf. Dezelfde logica wordt gebruikt voor het uitvoeren van schijf grootte wanneer de grootte criteria **op locatie** zijn en het opslag type is **Standard-HDD**, **Standard-SSD**of **Premium**.

Als u bijvoorbeeld een on-premises schijf met 32 GB geheugen hebt, maar de geaggregeerde Lees-en schrijf-IOPS voor de schijf 800 IOPS is, raadt server evaluatie een Premium-schijf type aan (vanwege de hogere IOPS-vereisten) en raden we u aan om een schijf-SKU aan te bieden die ondersteuning biedt voor de vereiste IOPS en grootte. In dit voorbeeld komen we dan uit bij P15 (256 GB, 1100 IOPS). Hoewel de grootte die de on-premises schijf vereist, 32 GB was, raadt server evaluatie een grotere schijf aan vanwege de hoge IOPS-vereiste van de on-premises schijf.

### <a name="why-does-my-assessment-report-indicate-percentageofcoresutilizedmissing-or-percentageofmemoryutilizedmissing-for-some-vms"></a>Waarom duidt mijn beoordelings rapport "PercentageOfCoresUtilizedMissing" of "PercentageOfMemoryUtilizedMissing" aan voor sommige Vm's?
Deze problemen worden gerapporteerd wanneer het Azure Migrate apparaat geen prestatie gegevens voor de on-premises Vm's kan verzamelen. Dit kan gebeuren als de virtuele machines voor de duur zijn uitgeschakeld tijdens het maken van de evaluatie (laatste één dag/één week/één maand). Het apparaat kan geen prestatie gegevens voor een virtuele machine verzamelen wanneer deze is uitgeschakeld. Als alleen de geheugen items ontbreken en u probeert virtuele Hyper-V-machines te evalueren, controleert u of u dynamisch geheugen hebt ingeschakeld op deze virtuele machines. Er is een bekend probleem waarbij een Azure Migrate apparaat geen geheugen gebruiks gegevens kan verzamelen voor Vm's waarvoor geen dynamisch geheugen is ingeschakeld. Dit probleem geldt alleen voor virtuele Hyper-V-machines, niet voor VMware-Vm's. Als een van de prestatie meter items ontbreekt, wordt de analyse van Azure Migrate server terugvallen op de toegewezen kernen en het geheugen en wordt een bijbehorende VM-grootte aanbevolen.

### <a name="is-the-os-license-cost-of-the-vm-included-in-the-compute-cost-estimated-by-server-assessment"></a>Zijn de OS-licentie kosten van de virtuele machine opgenomen in de reken kosten geschat door de server evaluatie?
De server evaluatie heeft momenteel alleen betrekking op de OS-licentie kosten voor Windows-computers. De kosten voor OS-licenties voor Linux-machines worden momenteel niet in overweging genomen.

### <a name="what-impact-do-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Wat is de invloed van de prestatie geschiedenis en het percentiel gebruik op de aanbevelingen voor de grootte?
Deze eigenschappen zijn alleen van toepassing op de grootte van op basis van prestaties. Met Server Assessment worden doorlopend prestatiegegevens van on-premises machines verzameld, die vervolgens worden gebruikt om SKU's voor VM en schijf aan te bevelen in Azure. Deze prestatie gegevens worden als volgt verzameld door server evaluatie:
- Het Azure Migrate apparaat maakt continu een profiel van de on-premises omgeving voor het verzamelen van gegevens over realtime gebruik om de 20 seconden voor VMware-Vm's en elke 30 seconden voor Hyper-V-Vm's.
- Het apparaat rolt de voor beelden van 20 seconden en 30 seconden samen om één gegevens punt voor elke tien minuten te maken. Als u het ene gegevens punt wilt maken, selecteert het apparaat de piek waarde van alle voor beelden van 20 en 30 seconden en verzendt deze vervolgens naar Azure.
- Wanneer u een evaluatie maakt in Server Assessment, op basis van de prestatieduur en de percentielwaarde van de prestatiegeschiedenis, wordt er een waarde vastgesteld die het daadwerkelijke gebruik voorstelt. Als de prestatie geschiedenis bijvoorbeeld één week is en het percentiel gebruik 95e is, sorteert Azure Migrate alle tien minuten bemonsterings punten voor de laatste week in oplopende volg orde en selecteert vervolgens het 95e percentiel als de representatieve waarde.
De 95e percentiel waarde zorgt ervoor dat u alle uitschieters negeert, die mogelijk worden opgenomen als u het 99e percentiel selecteert. Als u het piek gebruik voor de periode wilt kiezen en u geen uitschieters wilt missen, selecteert u het 99e percentiel voor percentiel gebruik.

## <a name="dependency-visualization-issues"></a>Afhankelijkheids problemen bij de visualisatie

### <a name="i-cant-find-the-dependency-visualization-functionality-for-azure-government-projects"></a>Ik kan de functionaliteit voor de visualisatie van afhankelijkheden voor Azure Government projecten niet vinden.

Azure Migrate is afhankelijk van Servicetoewijzing voor de visualisatie functionaliteit van de afhankelijkheid. Omdat Servicetoewijzing momenteel niet beschikbaar is in Azure Government, is deze functionaliteit niet beschikbaar in Azure Government.

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>Ik heb micro soft Monitoring Agent (MMA) en de dependency agent geïnstalleerd op mijn on-premises Vm's, maar de afhankelijkheden worden nu weer gegeven in de Azure Migrate Portal.

Nadat u de agents hebt geïnstalleerd, duurt Azure Migrate doorgaans 15-30 minuten om de afhankelijkheden in de portal weer te geven. Als u langer dan 30 minuten hebt gewacht, controleert u of MMA met de volgende stappen kan communiceren met de OMS-werk ruimte.

Voor Windows VM:
1. Ga naar configuratie scherm en start micro soft monitoring agent.
2. Controleer op het tabblad **Azure log Analytics (OMS)** in het dialoog venster **Eigenschappen van micro soft Monitoring Agent** of de **status** van de werk ruimte groen is.
3. Als de status niet groen is, probeert u de werk ruimte te verwijderen en toe te voegen aan MMA.

      ![Het dialoog venster Eigenschappen van MMA](./media/troubleshooting-general/mma-status.png)

Zorg ervoor dat voor de Linux-VM de installatie opdrachten voor MMA en de afhankelijkheids agent zijn geslaagd.

### <a name="what-operating-systems-does-mma-support"></a>Welke besturings systemen ondersteunt MMA?

 [Hier volgt een lijst met Windows-besturings systemen die worden ondersteund door MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
En [hier volgt een lijst met Linux-besturings systemen die worden ondersteund door MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-operating-systems-does-the-dependency-agent-support"></a>Welke besturings systemen worden ondersteund door de afhankelijkheids agent?

[Hier volgt een lijst met [Windows-en Linux-besturings systemen die Azure monitor voor VM's ondersteunt](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems).

### <a name="i-cant-visualize-dependencies-in-azure-migrate-for-more-than-a-one-hour-duration"></a>Ik kan voor meer dan een duur van één uur geen afhankelijkheden in Azure Migrate visualiseren.
In Azure Migrate kunt u afhankelijkheden tot een duur van één uur visualiseren. Hoewel u met Azure Migrate kunt terugkeren naar een bepaalde datum in de afgelopen maand, is de maximale duur waarvoor u de afhankelijkheden kunt visualiseren één uur.

U kunt bijvoorbeeld de functie tijd duur in de afhankelijkheids toewijzing gebruiken om afhankelijkheden voor gisteren weer te geven, maar u kunt ze slechts gedurende een periode van één uur weer geven. U kunt echter Azure Monitor-Logboeken gebruiken om een langere duur van [de afhankelijkheids gegevens](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) op te vragen.

### <a name="i-cant-visualize-dependencies-for-groups-that-have-more-than-10-vms"></a>Ik kan afhankelijkheden voor groepen met meer dan tien Vm's niet visualiseren.
U kunt [afhankelijkheden voor groepen](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) met Maxi maal tien vm's visualiseren. Als u een groep met meer dan 10 virtuele machines hebt, raden we u aan de groep te splitsen in kleinere groepen en de afhankelijkheden te visualiseren.

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>Ik heb agents geïnstalleerd en de visualisatie voor afhankelijkheden gebruikt om groepen te maken. Na failover worden op de computers de actie ' agent installeren ' weer gegeven in plaats van ' afhankelijkheden weer geven '.
* Nadat een geplande of niet-geplande failover, on-premises machines is uitgeschakeld en gelijkwaardige machines zich in azure bevinden. Deze machines halen een ander MAC-adres op. Ze kunnen een ander IP-adres verkrijgen op basis van het feit of de gebruiker ervoor kiest een on-premises IP-adres te bewaren.

  Als zowel MAC als IP-adressen verschillen, Azure Migrate de on-premises machines niet koppelen aan gegevens van Servicetoewijzing afhankelijkheid. In plaats daarvan wordt de gebruiker gevraagd om agents te installeren in plaats van afhankelijkheden weer te geven.
* Failover na de test, de on-premises machines blijven ingeschakeld zoals verwacht. Gelijkwaardige machines die in Azure worden ingezet, halen een ander MAC-adres op en kunnen verschillende IP-adressen verkrijgen. Tenzij de gebruiker het uitgaande Azure Monitor logboek verkeer van deze computers blokkeert, Azure Migrate de on-premises machines niet koppelen aan een Servicetoewijzing afhankelijkheids gegevens en gebruikers vragen om agents te installeren in plaats van afhankelijkheden weer te geven.

## <a name="collect-azure-portal-logs"></a>Azure Portal-logboeken verzamelen

**Azure Portal logboeken met netwerk verkeer Hoe kan ik verzamelen?**

1. Open de browser, ga naar [de portal en meld u aan](https://portal.azure.com).
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
