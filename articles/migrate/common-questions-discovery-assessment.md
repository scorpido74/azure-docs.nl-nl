---
title: Vragen over detectie-, beoordelings- en afhankelijkheidsanalyse in Azure Migrate
description: Antwoorden op veelgestelde vragen over detectie-, beoordelings- en afhankelijkheidsanalyse in Azure Migrate.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: b4b2a50bc88768d46c82f6bce73447dc901e5dfd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681907"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>Analyse van detectie, beoordeling en afhankelijkheid - Veelvoorkomende vragen

In dit artikel worden veelgestelde vragen beantwoord over detectie-, beoordelings- en afhankelijkheidsanalyse in Azure Migrate. Als u andere vragen hebt, controleert u de volgende bronnen:

- [Algemene vragen](resources-faq.md) over Azure Migreren
- Vragen over het [Azure Migrate-toestel](common-questions-appliance.md)
- Vragen over [servermigratie](common-questions-server-migration.md)
- Vragen beantwoorden in het [Azure Migrate-forum](https://aka.ms/AzureMigrateForum)


## <a name="what-geographies-are-supported-for-discovery-and-assessment-with-azure-migrate"></a>Welke regio's worden ondersteund voor detectie en beoordeling met Azure Migrate?

Bekijk de ondersteunde regio's voor [publieke](migrate-support-matrix.md#supported-geographies-public-cloud) en [overheidswolken.](migrate-support-matrix.md#supported-geographies-azure-government)


## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>Hoeveel VM's kan ik ontdekken met een apparaat?

U tot 10.000 VMware VM's, maximaal 5.000 Hyper-V VM's en maximaal 250 fysieke servers ontdekken met behulp van één toestel. Als u meer machines hebt, leest u over [het schalen van een Hyper-V-beoordeling,](scale-hyper-v-assessment.md)het schalen van een [VMware-beoordeling](scale-vmware-assessment.md)of [het schalen van een fysieke serverbeoordeling](scale-physical-assessment.md).

## <a name="i-cant-see-some-vm-types-in-azure-government"></a>Ik zie sommige VM-typen niet in Azure Government

VM-typen die worden ondersteund voor beoordeling en migratie zijn afhankelijk van de beschikbaarheid op de locatie azure-overheid. U VM-typen in Azure Government [bekijken en vergelijken.](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines)


## <a name="the-size-of-my-vm-changed-can-i-run-an-assessment-again"></a>De grootte van mijn VM veranderd. Kan ik opnieuw een assessment uitvoeren?

Het Azure Migrate-toestel verzamelt voortdurend informatie over de on-premises omgeving.  Een beoordeling is een point-in-time momentopname van on-premises VM's. Als u de instellingen op een vm wijzigt die u wilt beoordelen, gebruikt u de optie herberekenen om de beoordeling bij te werken met de laatste wijzigingen.

## <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>Hoe ontdek ik VM's in een multitenant-omgeving?

- **VMware:** Als een omgeving wordt gedeeld tussen tenants en u de VM's van een tenant niet wilt ontdekken in het abonnement van een andere tenant, maakt u VMware vCenter Server-referenties die alleen toegang hebben tot de VM's die u wilt ontdekken. Gebruik deze referenties vervolgens wanneer u de detectie start in het Azure Migrate-toestel.
- **Hyper-V**: Discovery maakt gebruik van Hyper-V host referenties. Als VM's dezelfde Hyper-V-host delen, is er momenteel geen manier om de detectie te scheiden.  

## <a name="do-i-need-vcenter-server"></a>Heb ik vCenter Server nodig?

Ja, Azure Migrate vereist vCenter Server in een VMware-omgeving om detectie uit te voeren. Azure Migrate biedt geen ondersteuning voor detectie van ESXi-hosts die niet worden beheerd door vCenter Server.

## <a name="what-are-the-sizing-options"></a>Wat zijn de grootteopties?

Met as-on-premises grootte, azure migreren niet vm prestatiegegevens voor beoordeling. Azure Migrate beoordeelt VM-formaten op basis van de on-premises configuratie. Bij prestatiegerichte grootte is de grootte gebaseerd op gebruiksgegevens.

Als een on-premises VM bijvoorbeeld vier cores en 8 GB geheugen heeft bij 50% CPU-gebruik en 50% geheugengebruik:
- As-on-premises grootte raadt een Azure VM SKU aan met vier cores en 8 GB geheugen.
- Op prestaties gebaseerde grootte beveelt een VM SKU aan met twee cores en 4 GB geheugen, omdat het gebruikspercentage wordt overwogen.

Op dezelfde manier is de grootte van de schijf afhankelijk van de groottecriteria en het opslagtype:
- Als de groottecriteria op basis van prestaties zijn gebaseerd en het opslagtype automatisch is, houdt Azure Migrate rekening met de IOPS- en doorvoerwaarden van de schijf wanneer het doelschijftype (Standaard of Premium) wordt geïdentificeerd.
- Als de groottecriteria op basis van prestaties zijn gebaseerd en het opslagtype Premium is, raadt Azure Migreren een SKU voor Premium-schijven aan op basis van de grootte van de on-premises schijf. Dezelfde logica wordt toegepast op schijfgrootte wanneer de grootte als on-premises is en het opslagtype Standaard of Premium is.

## <a name="does-performance-history-and-utilization-affect-sizing"></a>Hebben prestatiegeschiedenis en -gebruik invloed op de grootte?

Ja, prestatiegeschiedenis en -gebruik hebben invloed op de grootte in Azure Migrate.

### <a name="performance-history"></a>Prestatiegeschiedenis

Azure Migrate verzamelt alleen voor prestatiegebaseerde grootte de prestatiegeschiedenis van on-premises machines en gebruikt deze vervolgens om de vm-grootte en het schijftype in Azure aan te bevelen:

1. Het toestel profileert continu de on-premises omgeving om elke 20 seconden realtime gebruiksgegevens te verzamelen.
1. Het apparaat rolt de verzamelde monsters van 20 seconden op en gebruikt ze om de 15 minuten één gegevenspunt te maken.
1. Als u het gegevenspunt wilt maken, selecteert het toestel de piekwaarde uit alle monsters van 20 seconden.
1. Het toestel stuurt het gegevenspunt naar Azure.

### <a name="utilization"></a>Gebruik

Wanneer u een beoordeling maakt in Azure, afhankelijk van de prestatieduur en de percentielwaarde van de prestatiegeschiedenis die is ingesteld, berekent Azure Migrate de effectieve gebruikswaarde en gebruikt deze vervolgens voor de grootte.

Als u bijvoorbeeld de prestatieduur instelt op één dag en de percentielwaarde op 95e percentiel, sorteert Azure Migrate de voorbeeldpunten van 15 minuten die door de verzamelaar voor de afgelopen dag zijn verzonden in oplopende volgorde. Het kiest de 95e percentielwaarde als het effectieve gebruik.

Het gebruik van de 95e percentielwaarde zorgt ervoor dat uitschieters worden genegeerd. Uitschieters kunnen worden opgenomen als uw Azure Migrate het 99e percentiel gebruikt. Als u het piekverbruik voor de periode wilt kiezen zonder uitschieters te missen, stelt u Azure Migreren in om het 99e percentiel te gebruiken.

## <a name="how-are-import-based-assessments-different-from-assessments-with-discovery-source-as-appliance"></a>Hoe verschillen importgebaseerde beoordelingen van assessments met detectiebron als toestel?

Beoordelingen op basis van import zijn beoordelingen die zijn gemaakt met machines die worden geïmporteerd in Azure Migrate met behulp van een CSV-bestand. Slechts vier velden zijn verplicht om te importeren: servernaam, cores, geheugen en besturingssysteem. Hier zijn een aantal dingen op te merken: 
 - De gereedheidscriteria zijn minder streng in importgebaseerde beoordelingen op de parameter boottype. Als het opstarttype niet is opgegeven, wordt ervan uitgegaan dat de machine een BIOS-opstarttype heeft en de machine niet is gemarkeerd als **voorwaardelijk gereed**. In beoordelingen met detectiebron als toestel wordt de gereedheid gemarkeerd als **voorwaardelijk klaar** als het opstarttype ontbreekt. Dit verschil in gereedheidsberekening is omdat gebruikers mogelijk niet alle informatie over de machines hebben in de vroege stadia van migratieplanning wanneer beoordelingen op basis van invoer worden uitgevoerd. 
 - Op prestaties gebaseerde importbeoordelingen gebruiken de gebruikswaarde die door de gebruiker wordt opgegeven voor berekeningen met de juiste maat. Aangezien de gebruikswaarde door de gebruiker wordt opgegeven, worden de **prestatiegeschiedenis** **en de gebruiksopties Percentiel** uitgeschakeld in de beoordelingseigenschappen. Bij beoordelingen met detectiebron als toestel wordt de gekozen percentielwaarde gekozen uit de prestatiegegevens die door het toestel worden verzameld.

## <a name="what-is-dependency-visualization"></a>Wat is afhankelijkheidsvisualisatie?

Afhankelijkheidsvisualisatie kan u helpen groepen VM's te beoordelen om met meer vertrouwen te migreren. Afhankelijkheidsvisualisatie controleert machineafhankelijkheden voordat u een beoordeling uitvoert. Het helpt ervoor te zorgen dat er niets achterblijft en het helpt onverwachte storingen te voorkomen wanneer u migreert naar Azure. Azure Migrate gebruikt de Service Map-oplossing in Azure Monitor om afhankelijkheidsvisualisatie in te schakelen. [Meer informatie](concepts-dependency-visualization.md).

> [!NOTE]
> Afhankelijkheidsanalyse op basis van agenten is niet beschikbaar in Azure Government. U gebruik maken van agentless afhankelijkheidsanalyse

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>Wat is het verschil tussen agent-based en agentless?

De verschillen tussen agentloze visualisatie en agentgebaseerde visualisatie worden samengevat in de tabel.

**Vereiste** | **Zonder agent** | **Agent-based**
--- | --- | ---
Ondersteuning | Deze optie is momenteel in preview en is alleen beschikbaar voor VMware VM's. [Bekijk](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) ondersteunde besturingssystemen. | In het algemeen beschikbaarheid (GA).
Agent | Het is niet nodig om agents te installeren op machines die u wilt controleren. | Agents die moeten worden geïnstalleerd op elke on-premises machine die u wilt analyseren: de [Microsoft Monitoring-agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)en de [afhankelijkheidsagent.](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent) 
Vereisten | [Bekijk](concepts-dependency-visualization.md#agentless-analysis) de vereisten en implementatievereisten. | [Bekijk](concepts-dependency-visualization.md#agent-based-analysis) de vereisten en implementatievereisten.
Log Analytics | Niet vereist. | Azure Migrate gebruikt de [Service Map-oplossing](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) in [Azure Monitor-logboeken](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) voor afhankelijkheidsvisualisatie. [Meer informatie](concepts-dependency-visualization.md#agent-based-analysis).
Hoe werkt het? | Hiermee legt u TCP-verbindingsgegevens vast op machines die zijn ingeschakeld voor afhankelijkheidsvisualisatie. Na ontdekking verzamelt het gegevens met intervallen van vijf minuten. | Servicemap-agents die op een machine zijn geïnstalleerd, verzamelen gegevens over TCP-processen en inkomende/uitgaande verbindingen voor elk proces.
Gegevens | Naam van de sourcemachineserver, proces, naam van de toepassing.<br/><br/> Naam, proces, toepassingsnaam en poort van doelmachineserver. | Naam van de sourcemachineserver, proces, naam van de toepassing.<br/><br/> Naam, proces, toepassingsnaam en poort van doelmachineserver.<br/><br/> Het aantal verbindingen, latentie en gegevensoverdrachtsgegevens wordt verzameld en beschikbaar voor Log Analytics-query's. 
Visualisatie | Afhankelijkheidskaart van één server kan gedurende een uur tot 30 dagen worden bekeken. | Afhankelijkheidskaart van één server.<br/><br/> Kaart kan worden bekeken meer dan een uur alleen.<br/><br/> Afhankelijkheidskaart van een groep servers.<br/><br/> Servers in een groep toevoegen en verwijderen uit de kaartweergave.
Gegevensexport | Kan momenteel niet worden gedownload in tabelindeling. | Gegevens kunnen worden opgevraagd met Log Analytics.

## <a name="do-i-pay-for-dependency-visualization"></a>Betaal ik voor afhankelijkheidsvisualisatie?

Nee. Meer informatie over [azure migrate-prijzen](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>Wat installeer ik voor afhankelijkheidsvisualisatie op basis van agent?

Als u agentgebaseerde afhankelijkheidsvisualisatie wilt gebruiken, downloadt en installeert u agents op elke on-premises machine die u wilt evalueren:

- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)
- [Agent voor afhankelijkheden](../azure-monitor/platform/agents-overview.md#dependency-agent)
- Als u machines hebt die geen internetverbinding hebben, downloadt en installeert u de Log Analytics-gateway erop.

U hebt deze agents alleen nodig als u op agentgebaseerde afhankelijkheidsvisualisatie gebruikt.

## <a name="can-i-use-an-existing-workspace"></a>Kan ik een bestaande werkruimte gebruiken?

Ja, voor op agentgebaseerde afhankelijkheidsafhankelijkheid u een bestaande werkruimte aan het migratieproject koppelen en gebruiken voor afhankelijkheidsvisualisatie. 

## <a name="can-i-export-the-dependency-visualization-report"></a>Kan ik het rapport over de visualisatie van afhankelijkheden exporteren?

Nee, het rapport afhankelijkheidsvisualisatie in agentvisualisatie kan niet worden geëxporteerd. Azure Migrate maakt echter gebruik van Servicemap en u de [API ServiceMap REST](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) gebruiken om de afhankelijkheden in json-indeling op te halen.

## <a name="can-i-automate-agent-installation"></a>Kan ik de installatie van agenten automatiseren?

Voor op agenten gebaseerde afhankelijkheidsvisualisatie:

- Gebruik een [script om de afhankelijkheidsagent te installeren.](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples)
- Gebruik voor MMA [de opdrachtregel of automatisering](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration)of gebruik een [script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).
- Naast scripts u implementatiehulpprogramma's zoals Microsoft Endpoint Configuration Manager en [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) gebruiken om de agents te implementeren.

## <a name="what-operating-systems-does-mma-support"></a>Welke besturingssystemen ondersteunt MMA?

- Bekijk de lijst met [Windows-besturingssystemen die MMA ondersteunt.](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)
- Bekijk de lijst met [Linux-besturingssystemen die MMA ondersteunt.](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>Kan ik afhankelijkheden langer dan een uur visualiseren?

Voor op agenten gebaseerde visualisatie u afhankelijkheden maximaal een uur visualiseren. U teruggaan tot een maand naar een specifieke datum in de geschiedenis, maar de maximale duur voor visualisatie is een uur. U bijvoorbeeld de tijdsduur in de afhankelijkheidskaart gebruiken om afhankelijkheden voor gisteren weer te geven, maar u afhankelijkheden alleen voor een venster van één uur weergeven. U Azure Monitor-logboeken echter gebruiken om [afhankelijkheidsgegevens](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) voor een langere duur op te vragen.

Voor agentloze visualisatie u de afhankelijkheidskaart van één server bekijken vanaf een duur van tussen een uur en 30 dagen.

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-vms"></a>Kan ik afhankelijkheden visualiseren voor groepen van meer dan 10 VM's?

U [afhankelijkheden visualiseren](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) voor groepen met maximaal 10 VM's. Als u een groep hebt met meer dan 10 VM's, raden we u aan de groep op te splitsen in kleinere groepen en de afhankelijkheden te visualiseren.

## <a name="next-steps"></a>Volgende stappen

Lees het [overzicht Azure Migreren](migrate-services-overview.md).
