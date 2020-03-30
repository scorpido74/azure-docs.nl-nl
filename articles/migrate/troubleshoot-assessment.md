---
title: Problemen met beoordelings- en afhankelijkheidsvisualisatie in Azure Migreren oplossen
description: Hulp bij het beoordelen van probleemoplossing en afhankelijkheidsvisualisatie in Azure Migrate.
ms.service: azure-migrate
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/02/2020
ms.openlocfilehash: 205b52201edb849abab02809b58ff9dc77a32a29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127681"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>Problemen met de evaluatie/afhankelijkheid oplossen

In dit artikel u problemen met beoordelings- en afhankelijkheidsvisualisatie met [Azure Migrate: Serverassessment](migrate-services-overview.md#azure-migrate-server-assessment-tool)oplossen.


## <a name="assessment-readiness-issues"></a>Problemen met de gereedheid van de beoordeling

Los problemen met de beoordelingsgereedheid als volgt op:

**Probleem** | **Fix**
--- | ---
Niet-ondersteund opstarttype | Azure ondersteunt geen VM's met een EFI-opstarttype. We raden u aan het opstarttype om te zetten naar BIOS voordat u een migratie uitvoert. <br/><br/>U Azure Migrate Server Migration gebruiken om de migratie van dergelijke VM's te verwerken. Het zal het opstarttype van de VM tijdens de migratie converteren naar BIOS.
Voorwaardelijk ondersteund Windows-besturingssysteem | Het besturingssysteem heeft de einddatum van de ondersteuning verstreken en heeft een Aangepaste ondersteuningsovereenkomst (CSA) nodig voor [ondersteuning in Azure.](https://aka.ms/WSosstatement) Overweeg upgraden voordat u migreert naar Azure.
Niet-ondersteund Windows-besturingssysteem | Azure ondersteunt alleen [geselecteerde Windows OS-versies.](https://aka.ms/WSosstatement) Overweeg de machine te upgraden voordat u migreert naar Azure.
Voorwaardelijk onderschreven Linux OS | Azure onderschrijft alleen [geselecteerde Linux OS-versies.](../virtual-machines/linux/endorsed-distros.md) Overweeg de machine te upgraden voordat u migreert naar Azure.
Niet-goedgekeurd Linux-besturingssysteem | De machine kan starten in Azure, maar Azure biedt geen ondersteuning voor het besturingssysteem. Overweeg een upgrade naar een [goedgekeurde Linux-versie](../virtual-machines/linux/endorsed-distros.md) voordat u migreert naar Azure.
Onbekend besturingssysteem | Het besturingssysteem van de VM is opgegeven als 'Overige' in vCenter Server. Met dit probleem u azure migreren om de azure-gereedheid van de vm te verifiëren. Zorg ervoor dat het besturingssysteem [wordt ondersteund](https://aka.ms/azureoslist) door Azure voordat u de machine migreert.
Niet-ondersteunde bitversie | VM's met een 32-bits besturingssystemen kunnen worden opgestart in Azure, maar we raden u aan te upgraden naar 64-bits voordat u migreert naar Azure.
Vereist een Microsoft Visual Studio-abonnement | De machine draait een Windows-client besturingssysteem, dat alleen wordt ondersteund door middel van een Visual Studio-abonnement.
VM niet gevonden voor de vereiste opslagprestaties | De opslagprestaties (invoer-/uitvoerbewerkingen per seconde [IOPS] en doorvoer) die nodig zijn voor de machine, zijn groter dan de Azure VM-ondersteuning. Verminder de opslagvereisten voor de machine vóór migratie.
VM niet gevonden voor de vereiste netwerkprestaties | De netwerkprestaties (in/uit) die nodig zijn voor de machine, zijn groter dan de Azure VM-ondersteuning. Verlaag de netwerkvereisten voor de machine.
VM niet gevonden op de opgegeven locatie | Gebruik een andere doellocatie voor migratie.
Een of meer ongeschikte schijven | Een of meer schijven die aan de VM zijn gekoppeld, voldoen niet aan de Azure-vereisten. A<br/><br/> Azure Migrate: Server Assessment ondersteunt momenteel geen Ultra SSD-schijven en beoordeelt de schijven op basis van de schijflimieten voor premium beheerde schijven (32 TB).<br/><br/> Controleer voor elke schijf die aan de VM is gekoppeld of de grootte van de schijf < 64 TB (ondersteund door Ultra SSD-schijven).<br/><br/> Als dit niet het is, verkleint u de schijfgrootte voordat u migreert naar Azure of gebruikt u meerdere schijven in Azure en [streept u ze samen](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) om hogere opslaglimieten te krijgen. Zorg ervoor dat de prestaties (IOPS en doorvoer) die nodig zijn voor elke schijf wordt ondersteund door door Azure [beheerde virtuele machineschijven.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits)
Een of meer ongeschikte netwerkadapters. | Verwijder ongebruikte netwerkadapters uit de machine voordat u overstapt.
Schijfaantal overschrijdt limiet | Verwijder ongebruikte schijven uit de machine voordat u overstapt.
Schijfgrootte overschrijdt limiet | Azure Migrate: Server Assessment ondersteunt momenteel geen Ultra SSD-schijven en beoordeelt de schijven op basis van premium schijflimieten (32 TB).<br/><br/> Azure ondersteunt echter schijven met een grootte van maximaal 64 TB (ondersteund door Ultra SSD-schijven). Krimp schijven tot minder dan 64 TB voor migratie, of gebruik meerdere schijven in Azure en [streep ze samen](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) om hogere opslaglimieten te krijgen.
Schijf niet beschikbaar op de opgegeven locatie | Zorg ervoor dat de schijf zich op uw doellocatie bevindt voordat u migreert.
Schijf niet beschikbaar voor de opgegeven redundantie | De schijf moet het type redundantieopslag gebruiken dat is gedefinieerd in de beoordelingsinstellingen (LRS standaard).
Kan de geschiktheid van de schijf niet bepalen vanwege een interne fout | Probeer een nieuwe beoordeling voor de groep te maken.
VM met vereiste cores en geheugen niet gevonden | Azure kon geen geschikt VM-type vinden. Verminder het geheugen en het aantal kernen van de on-premises machine voordat u migreert.
Kan de geschiktheid van vm's niet bepalen vanwege een interne fout | Probeer een nieuwe beoordeling voor de groep te maken.
Kon niet bepalen geschiktheid voor een of meer schijven als gevolg van een interne fout | Probeer een nieuwe beoordeling voor de groep te maken.
Kan de geschiktheid voor een of meer netwerkadapters niet bepalen vanwege een interne fout | Probeer een nieuwe beoordeling voor de groep te maken.

## <a name="linux-vms-are-conditionally-ready"></a>Linux VM's zijn "voorwaardelijk klaar"

Server Assessment markeert Linux VM's als "Voorwaardelijk klaar" vanwege een bekende kloof in Server Assessment.

- De kloof voorkomt dat het de kleine versie van het Linux-besturingssysteem detecteert dat op de on-premises VM's is geïnstalleerd.
- Voor RHEL 6.10 detecteert serverbeoordeling momenteel bijvoorbeeld alleen RHEL 6 als de OS-versie.
-  Omdat Azure alleen specifieke versies van Linux onderschrijft, zijn de Linux-VM's momenteel gemarkeerd als voorwaardelijk klaar in Serverassessment.
- U bepalen of het Linux-besturingssysteem dat op de on-premises VM wordt uitgevoerd, wordt goedgekeurd in Azure door [azure Linux-ondersteuning](https://aka.ms/migrate/selfhost/azureendorseddistros)te controleren.
-  Nadat u de goedgekeurde distributie hebt geverifieerd, u deze waarschuwing negeren.

## <a name="azure-skus-bigger-than-on-premises"></a>Azure SKU's groter dan on-premises

Azure Migrate Server Assessment kan Azure VM SKU's aanbevelen met meer cores en geheugen dan de huidige on-premises toewijzing op basis van het type beoordeling:


- De VM SKU-aanbeveling is afhankelijk van de beoordelingseigenschappen.
- Dit wordt beïnvloed door het type beoordeling dat u uitvoert in Serverassessment: *Op basis van prestaties*of als *on-premises*.
- Voor prestatiegebaseerde beoordelingen houdt Server Assessment rekening met de gebruiksgegevens van de on-premises VM's (CPU, geheugen, schijf en netwerkgebruik) om de juiste vm-VM's voor uw on-premises VM's te bepalen. Het voegt ook een comfortfactor bij het bepalen van effectief gebruik.
- Voor on-premises dimensionering worden prestatiegegevens niet in aanmerking genomen en wordt de doel-SKU aanbevolen op basis van locatietoewijzing.

Laten we een voorbeeld nemen om aan te geven hoe dit van invloed kan zijn op aanbevelingen:

We hebben een on-premises VM met vier cores en acht GB geheugen, met 50% CPU-gebruik en 50% geheugengebruik en een opgegeven comfortfactor van 1,3.

-  Als de beoordeling **als on-premises**is, wordt een Azure VM SKU met vier cores en 8 GB geheugen aanbevolen.
- Als de beoordeling op prestaties is gebaseerd, gebaseerd op effectief CPU- en geheugengebruik (50% van 4 cores * 1,3 = 2,6 cores en 50% geheugen van 8 GB * 1,3 = 5,3 GB geheugen), de goedkoopste VM SKU van vier kernen (dichtstbijzijnde ondersteunde kerntelling) en acht GB geheugen (dichtstbijzijnde ondersteunde geheugengrootte) wordt aanbevolen.
- [Meer informatie](concepts-assessment-calculation.md#types-of-assessments) over de beoordeling van de beoordeling.

## <a name="azure-disk-skus-bigger-than-on-premises"></a>Azure disk SKU's groter dan on-premises

Azure Migrate Server Assessment kan een grotere schijf aanbevelen op basis van het type beoordeling.
- Schijfgrootte in serverbeoordeling is afhankelijk van twee beoordelingseigenschappen: groottecriteria en opslagtype.
- Als de groottecriteria **op basis van prestaties**zijn en het opslagtype is ingesteld op **Automatisch,** worden de IOPS- en doorvoerwaarden van de schijf in aanmerking genomen bij het identificeren van het doelschijftype (Standard HDD, Standard SSD of Premium). Een schijf-SKU van het schijftype wordt vervolgens aanbevolen en in de aanbeveling wordt rekening gehouden met de groottevereisten van de on-premises schijf.
- Als de groottecriteria **op basis van prestaties**zijn en het opslagtype **Premium**is, wordt een premium schijf SKU in Azure aanbevolen op basis van de IOPS-, doorvoer- en groottevereisten van de on-premises schijf. Dezelfde logica wordt gebruikt om schijfgrootte uit te voeren wanneer de groottecriteria **als on-premises** zijn en het opslagtype **Standaard HDD,** **Standaard SSD**of **Premium**is.

Als u bijvoorbeeld een on-premises schijf met 32 GB geheugen hebt, maar de geaggregeerde lees- en schrijf-IOPS voor de schijf 800 IOPS is, raadt Server Assessment een premium schijf aan (vanwege de hogere IOPS-vereisten) en beveelt vervolgens een schijf-SKU aan die de vereiste IOPS en grootte. In dit voorbeeld komen we dan uit bij P15 (256 GB, 1100 IOPS). Hoewel de vereiste grootte van de on-premises schijf 32 GB bedroeg, raadt Server Assessment een grotere schijf aan vanwege de hoge IOPS-vereiste van de on-premises schijf.

## <a name="utilized-corememory-percentage-missing"></a>Gebruikt kern/geheugenpercentage ontbreekt

Server Assessment rapporteert 'PercentageOfCoresUtilizedMissing' of 'PercentageOfMemoryUtilizedMissing' wanneer het Azure Migrate-toestel geen prestatiegegevens kan verzamelen voor de relevante on-premises VM's.

- Dit kan gebeuren als de VM's zijn uitgeschakeld tijdens de beoordelingsduur. Het toestel kan geen prestatiegegevens voor een virtuele machine verzamelen wanneer deze is uitgeschakeld.
- Als alleen de geheugentellers ontbreken en u hyperv-VM's probeert te beoordelen, controleert u of dynamisch geheugen is ingeschakeld op deze VM's. Er is alleen een bekend probleem voor Hyper-V VM's, waarbij een Azure Migrate-toestel geen geheugengebruiksgegevens kan verzamelen voor VM's waarvoor geen dynamisch geheugen is ingeschakeld.
- Als een van de prestatiemeteritems ontbreekt, valt Azure Migrate Server Assessment terug op de toegewezen cores en het geheugen en wordt een overeenkomstige VM-grootte aanbevolen.
- Als alle prestatiemeteritems ontbreken, moet u ervoor zorgen dat aan de vereisten voor poorttoegang voor beoordeling wordt voldaan. Meer informatie over de vereisten voor poorttoegang voor [VMware,](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#port-access) [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#port-access) en [fysieke](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-physical#port-access) serverbeoordeling.

## <a name="is-the-operating-system-license-included"></a>Is de licentie van het besturingssysteem inbegrepen?

Azure Migrate Server Assessment houdt momenteel alleen rekening met de licentiekosten van het besturingssysteem voor Windows-machines. Licentiekosten voor Linux-machines worden momenteel niet in aanmerking genomen.

## <a name="how-does-performance-based-sizing-work"></a>Hoe werkt prestatiegerichte maatvoering?

Met Server Assessment worden doorlopend prestatiegegevens van on-premises machines verzameld, die vervolgens worden gebruikt om SKU's voor VM en schijf aan te bevelen in Azure. [Ontdek hoe](concepts-assessment-calculation.md#calculate-sizing-performance-based) op prestaties gebaseerde gegevens worden verzameld.


## <a name="dependency-visualization-in-azure-government"></a>Afhankelijkheidsvisualisatie in Azure-overheid

Azure Migreren is afhankelijk van Servicemap voor de functionaliteit voor afhankelijkheidsvisualisatie. Omdat servicetoewijzing momenteel niet beschikbaar is in Azure Government, is deze functionaliteit niet beschikbaar in Azure Government.

## <a name="dependencies-dont-show-after-agent-install"></a>Afhankelijkheden worden niet weergegeven na installatie van de agent

Nadat u de afhankelijkheidsvisualisatieagents op on-premises VM's hebt geïnstalleerd, duurt azure migreren doorgaans 15-30 minuten om de afhankelijkheden in de portal weer te geven. Als u langer dan 30 minuten hebt gewacht, moet u ervoor zorgen dat de Microsoft Monitoring Agent (MMA) verbinding kan maken met de werkruimte Log Analytics.

Voor Windows VM's:
1. Start MMA in het Configuratiescherm.
2. Controleer in de **eigenschappen** > **Azure Log Analytics (OMS)** van de Microsoft Monitoring Agent of de **status** voor de werkruimte groen is.
3. Als de status niet groen is, probeert u de werkruimte te verwijderen en opnieuw aan MMA toe te voegen.

    ![MMA-status](./media/troubleshoot-assessment/mma-properties.png)

Voor Linux VM's moet u ervoor zorgen dat de installatieopdrachten voor MMA en de afhankelijkheidsagent zijn geslaagd.

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

- **MMS-agent**: Bekijk de ondersteunde [Windows-](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)en Linux-besturingssystemen. [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)
- **Afhankelijkheidsagent:** de ondersteunde [Windows- en](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) Linux-besturingssystemen.

## <a name="visualize-dependencies-for--hour"></a>Afhankelijkheden visualiseren voor > uur

Hoewel azure migreren u toestaat om terug te gaan naar een bepaalde datum in de afgelopen maand, is de maximale duur waarvoor u de afhankelijkheden visualiseren een uur.

U bijvoorbeeld de functionaliteit voor tijdduur in de afhankelijkheidskaart gebruiken om afhankelijkheden voor gisteren weer te geven, maar u ze alleen voor een periode van één uur bekijken.

U azure monitor-logboeken echter gebruiken om [de afhankelijkheidsgegevens](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) over een langere duur op te vragen.

## <a name="visualized-dependencies-for--10-machines"></a>Gevisualiseerde afhankelijkheden voor > 10 machines

In Azure Migrate Server Assessment u [afhankelijkheden visualiseren voor groepen](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) met maximaal 10 VM's. Voor grotere groepen raden we u aan de VM's in kleinere groepen op te splitsen om afhankelijkheden te visualiseren.

## <a name="machines-show-install-agent"></a>Machines tonen "Installatieagent"

Nadat machines met afhankelijkheidsvisualisatie zijn ingeschakeld voor Azure, kunnen machines de actie 'Installatieagent' weergeven in plaats van 'Afhankelijkheden weergeven' vanwege het volgende gedrag:


- Na migratie naar Azure worden on-premises machines uitgeschakeld en worden gelijkwaardige VM's in Azure gedraaid. Deze machines krijgen een ander MAC-adres.
- Machines kunnen ook een ander IP-adres hebben, op basis van of u het on-premises IP-adres hebt behouden of niet.
- Als zowel MAC- als IP-adressen verschillen van on-premises, koppelt Azure Migreren de on-premises machines niet aan afhankelijkheidsgegevens van servicekaarten. In dit geval wordt de optie weergegeven om de agent te installeren in plaats van afhankelijkheden te bekijken.
- Na een testmigratie naar Azure blijven on-premises machines ingeschakeld zoals verwacht. Gelijkwaardige machines die in Azure zijn gesponnen, krijgen verschillende MAC-adressen en kunnen verschillende IP-adressen verwerven. Tenzij u uitgaand Azure Monitor-logboekverkeer van deze machines blokkeert, koppelt Azure Migrate de on-premises machines niet aan afhankelijkheidsgegevens van servicekaarten en toont het dus de optie om agents te installeren, in plaats van afhankelijkheden weer te geven.


## <a name="capture-network-traffic"></a>Netwerkverkeer vastleggen

Als volgt logboeken voor netwerkverkeer verzamelen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Druk op F12 om Hulpprogramma's voor ontwikkelaars te starten. Schakel indien nodig de **instellingen voor navigatie wissen** uit.
3. Selecteer het tabblad **Netwerk** en begin met het vastleggen van netwerkverkeer:
   - Selecteer logboek **behouden in**Chrome . De opname moet automatisch worden gestart. Een rode cirkel geeft aan dat het verkeer wordt vastgelegd. Als de rode cirkel niet wordt weergegeven, selecteert u de zwarte cirkel die u wilt starten.
   - In Microsoft Edge en Internet Explorer moet de opname automatisch worden gestart. Als dit niet het zo is, selecteert u de groene afspeelknop.
4. Probeer de fout te reproduceren.
5. Nadat u de fout hebt ondervonden tijdens het opnemen, stopt u met opnemen en slaat u een kopie van de opgenomen activiteit op:
   - Klik in Chrome met de rechtermuisknop en selecteer **Opslaan als HAR met inhoud**. Met deze actie worden de logboeken gecomprimeerd en geëxporteerd als een .har-bestand.
   - Selecteer in Microsoft Edge of Internet Explorer de optie **Vastgelegd verkeer exporteren.** Met deze actie wordt het logboek gecomprimeerd en geëxporteerd.
6. Selecteer het tabblad **Console** om te controleren op waarschuwingen of fouten. Ga als u het consolelogboek opslaat:
   - Klik in Chrome met de rechtermuisknop op een plaats in het consolelogboek. Selecteer **Opslaan als**, om te exporteren en rits het logboek.
   - Klik in Microsoft Edge of Internet Explorer met de rechtermuisknop op de fouten en selecteer **Alles kopiëren**.
7. Ontwikkeltools sluiten.

## <a name="next-steps"></a>Volgende stappen

[Een](how-to-create-assessment.md) beoordeling maken of [aanpassen.](how-to-modify-assessment.md)
