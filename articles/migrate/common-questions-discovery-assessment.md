---
title: Veelgestelde vragen-detectie, evaluatie en afhankelijkheids analyse in Azure Migrate
description: Krijg antwoorden op veelgestelde vragen over detectie, evaluatie en afhankelijkheids analyse in Azure Migrate.
ms.topic: conceptual
ms.date: 12/29/2019
ms.openlocfilehash: 0132563072ed04a52e4937da7a8df69196f1a91f
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513283"
---
# <a name="common-questions-about-discovery-assessment-and-dependency-analysis"></a>Veelgestelde vragen over detectie, evaluatie en afhankelijkheids analyse

In dit artikel vindt u antwoorden op veelgestelde vragen over detectie, evaluatie en afhankelijkheids analyse in Azure Migrate. Als u na het lezen van dit artikel nog meer query's hebt uitgevoerd, plaatst u deze op het [Azure migrate forum](https://aka.ms/AzureMigrateForum). Als u andere vragen hebt, raadpleegt u de volgende artikelen:

- [Algemene vragen](resources-faq.md) over Azure Migrate.
- [Vragen](common-questions-appliance.md) over het Azure migrate apparaat.


## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>Hoeveel Vm's kan ik vinden met een apparaat?

U kunt Maxi maal 10.000 VMware-Vm's detecteren, tot 5.000 Hyper-V-Vm's en Maxi maal 250 servers met één apparaat. Als u meer computers in uw on-premises omgeving hebt, leest u over het schalen van [Hyper-V](scale-hyper-v-assessment.md), [VMware](scale-vmware-assessment.md) en [fysieke](scale-physical-assessment.md) evaluatie.



## <a name="vm-size-changed-can-i-run-an-assessment-again"></a>VM-grootte gewijzigd. Kan ik een evaluatie opnieuw uitvoeren?

Het Azure Migrate apparaat verzamelt voortdurend informatie over de on-premises omgeving. Maar een evaluatie is een tijdgebonden moment opname van on-premises Vm's. Als u de instellingen van een virtuele machine die u wilt beoordelen, wijzigt, gebruikt u de optie opnieuw berekenen om de evaluatie bij te werken met de meest recente wijzigingen.

### <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>Hoe kan ik virtuele machines detecteren in een multi tenant-omgeving?

- Als uw omgeving wordt gedeeld tussen tenants en u niet wilt dat de Vm's van één Tenant in het abonnement van een andere Tenant worden gedetecteerd, moet u voor VMware vCenter Server referenties maken die alleen toegang hebben tot de Vm's die u wilt detecteren. Gebruik vervolgens deze referenties wanneer u de detectie start in het Azure Migrate apparaat.
- Voor Hyper-V maakt detectie gebruik van referenties voor Hyper-V-hosts. Als Vm's dezelfde Hyper-V-host delen, is er momenteel geen enkele manier om de detectie te scheiden.  


### <a name="do-i-need-vcenter-server-for-vmware-vm-discovery"></a>Heb ik vCenter Server nodig voor de detectie van VMWare-machines?

Ja, Azure Migrate moet vCenter Server om de detectie uit te voeren in een VMware-omgeving. Het biedt geen ondersteuning voor detectie van ESXi-hosts die niet worden beheerd door vCenter Server.


## <a name="whats-the-difference-sizing-options"></a>Wat zijn de opties voor de grootte van het verschil?

Bij een on-premises grootte wordt Azure Migrate geen rekening gehouden met de prestatie gegevens van de virtuele machine voor evaluatie. De VM-grootten worden geëvalueerd op basis van de on-premises configuratie. De grootte van de prestaties is gebaseerd op de gebruiks gegevens.

- Als een on-premises VM bijvoorbeeld 4 kernen en 8 GB aan geheugen heeft met een CPU-gebruik van 50% en 50% geheugen gebruik, gebeurt het volgende:
    - Bij een on-premises grootte wordt een Azure VM-SKU aanbevolen met 4 kernen en 8 GB aan geheugen.
    - Op basis van de prestaties wordt een VM-SKU aanbevolen met 2 kernen en 4 GB geheugen, omdat het gebruiks percentage wordt overwogen.

- De schijf grootte is ook afhankelijk van twee evaluatie-eigenschappen: grootte criteria en opslag type.
    - Als de grootte criteria op basis van prestaties zijn en het opslag type automatisch is, neemt Azure Migrate de IOPS-en doorvoer waarden van de schijf in rekening wanneer het type doel schijf (Standard of Premium) wordt geïdentificeerd.
    - Als de grootte criteria gebaseerd zijn op prestaties en het opslag type Premium is, raadt Azure Migrate een SKU voor Premium-schijven aan op basis van de grootte van de on-premises schijf. Dezelfde logica wordt toegepast op schijf grootte, wanneer de grootte is ingesteld op on-premises en het opslag type Standard of Premium is.

## <a name="does-performance-historyutilization-impact-sizing"></a>Is de prestatie geschiedenis/het gebruik van invloed op de grootte?

Deze eigenschappen zijn alleen van toepassing wanneer de grootte wordt bepaald op basis van prestaties.

- Azure Migrate verzamelt de prestatie geschiedenis van on-premises machines en gebruikt deze om de VM-grootte en het schijf type in azure aan te bevelen.
- Het apparaat maakt continu gebruik van de on-premises omgeving, zodat gegevens in realtime kunnen worden verzameld om de 20 seconden.
- Het apparaat rolt de voor beelden van 20 seconden samen en maakt één gegevens punt om de 15 minuten.
- Voor het maken van het gegevens punt selecteert het apparaat de piek waarde van alle voor beelden van 20 seconden.
- Het apparaat verzendt dit gegevens punt naar Azure.

Bij het maken van een evaluatie in azure, op basis van de waarde voor prestatie duur en het percentiel van de prestatie geschiedenis, berekent Azure Migrate de juiste gebruiks waarde en wordt deze gebruikt voor het aanpassen van de grootte.

- Als u bijvoorbeeld de duur van de prestaties instelt op één dag en de percentiel waarde op 95 percentiel, sorteert Azure Migrate de voorbeeld punten van 15 minuten die zijn verzonden door de collector voor de afgelopen dag in oplopende volg orde, en wordt de waarde van het 95e percentiel als effectief gekozen gebruik.
- Het gebruik van de 95e percentiel waarde zorgt ervoor dat uitbijters worden genegeerd. Uitschieters kunnen worden opgenomen als u het 99e percentiel gebruikt. Als u het piek gebruik voor de periode wilt kiezen zonder uitbijters te missen, selecteert u het 99e percentiel.

## <a name="what-is-dependency-visualization"></a>Wat is de visualisatie van afhankelijkheden?

Gebruik de visualisatie van afhankelijkheden om groepen Vm's te beoordelen op migratie met een grotere betrouw baarheid. Afhankelijkheids visualisatie: meerdere computer afhankelijkheden controleren voordat u een evaluatie uitvoert. Zo kunt u er zeker van zijn dat er niets achter blijft, waardoor onverwachte storingen worden voor komen wanneer u naar Azure migreert. Azure Migrate maakt gebruik van de Servicetoewijzing oplossing in Azure Monitor om de visualisatie van afhankelijkheden in te scha kelen. [meer informatie](concepts-dependency-visualization.md).

> [!NOTE]
> Visualisatie van afhankelijkheid is niet beschikbaar in Azure Government.

## <a name="do-i-pay-for-dependency-visualization"></a>Moet ik betalen voor de visualisatie van de afhankelijkheid?
Nee. Meer informatie over prijzen voor Azure Migrate vindt u [hier](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="what-do-i-install-for-dependency-visualization"></a>Wat moet ik installeren voor visualisatie van afhankelijkheden?

Als u afhankelijkheids visualisatie wilt gebruiken, moet u agents downloaden en installeren op elke on-premises computer die u wilt evalueren.

U moet de volgende agents op elke computer installeren:
- [Micro soft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).
- [Afhankelijkheids agent](../azure-monitor/platform/agents-overview.md#dependency-agent).
- Als u machines hebt zonder Internet verbinding, moet u Log Analytics gateway op deze computers downloaden en installeren.

U hebt deze agents niet nodig tenzij u gebruikmaakt van afhankelijkheids visualisatie.

## <a name="can-i-use-an-existing-workspace"></a>Kan ik een bestaande werk ruimte gebruiken?

Ja, u kunt een bestaande werk ruimte koppelen aan het migratie project en deze gebruiken voor de visualisatie van afhankelijkheden. [Meer informatie](concepts-dependency-visualization.md#how-does-it-work).

## <a name="can-i-export-the-dependency-visualization-report"></a>Kan ik het visualisatie rapport van de afhankelijkheid exporteren?

Nee, de visualisatie van de afhankelijkheid kan niet worden geëxporteerd. Azure Migrate maakt echter gebruik van Servicetoewijzing, en u kunt de [Servicetoewijzing rest API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) gebruiken om de afhankelijkheden in JSON-indeling op te halen.

## <a name="can-i-automate--mmadependency-agent-installation"></a>Kan ik de installatie van MMA/dependency agent automatiseren?

Gebruik dit [script om de afhankelijkheids agent te installeren](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples). Volg deze [instructies voor het installeren van MMA](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) met behulp van de opdracht regel of de automatisering. Voor MMA gebruikt u [Dit script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).

Naast scripts kunt u ook implementatie hulpprogramma's zoals micro soft endpoint Configuration Manager en [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) gebruiken om de agents te implementeren.


## <a name="what-operating-systems-does-mma-support"></a>Welke besturings systemen ondersteunt MMA?

- Bekijk de lijst met [Windows-besturings systemen die worden ondersteund door MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
- Bekijk de lijst met [Linux-besturings systemen die worden ondersteund door MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

## <a name="can-i-visualize-dependencies-for-more-than-an-hour"></a>Kan ik afhankelijkheden langer dan een uur visualiseren?
Nee. U kunt afhankelijkheden tot een uur visualiseren. U kunt teruggaan naar een bepaalde datum in geschiedenis, tot nu toe per maand, maar de maximale duur voor visualisatie is een uur. U kunt bijvoorbeeld de tijds duur in de afhankelijkheids toewijzing gebruiken om afhankelijkheden voor gisteren weer te geven, maar u kunt afhankelijkheden alleen bekijken voor een venster van één uur. U kunt echter Azure Monitor-Logboeken gebruiken om een langere duur van [afhankelijkheids gegevens](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) op te vragen.

## <a name="can-visualize-dependencies-for-groups-of-more-than-10-vms"></a>Kunt u afhankelijkheden voor groepen van meer dan 10 Vm's visualiseren?
U kunt [afhankelijkheden](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) voor groepen met Maxi maal tien vm's visualiseren. Als u een groep hebt met meer dan tien Vm's, raden we u aan de groep te splitsen in kleinere groepen en vervolgens de afhankelijkheden te visualiseren.




## <a name="next-steps"></a>Volgende stappen
Lees het [Azure migrate overzicht](migrate-services-overview.md).
