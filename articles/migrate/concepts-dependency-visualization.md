---
title: Visualisatie van afhankelijkheden in Azure Migrate
description: Hierin wordt een overzicht gegeven van de evaluatie berekeningen in de server Assessment-service in Azure Migrate
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 65a99e230262ae05d34dc8c04e87252c15133fda
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425677"
---
# <a name="dependency-visualization"></a>Visualisatie van afhankelijkheden

In dit artikel wordt de functie voor de visualisatie van afhankelijkheden in Azure Migrate: Server evaluatie beschreven.

De visualisatie van afhankelijkheden helpt u bij het begrijpen van afhankelijkheden tussen computers die u wilt beoordelen en migreren. U gebruikt meestal afhankelijkheids toewijzing als u computers wilt beoordelen met een hoger vertrouwens niveau.

- In Azure Migrate: Server analyse verzamelt u computers in groepen voor evaluatie. Groepen bestaan gewoonlijk uit machines die samen moeten worden gemigreerd en afhankelijkheids visualisatie helpt u bij het controleren van afhankelijkheden van machines, zodat u machines nauw keurig kunt groeperen.
- Met visualisatie kunt u afhankelijke systemen detecteren die samen moeten worden gemigreerd. U kunt nagaan of actieve systemen nog in gebruik zijn of of systemen uit de buiten gebruiks telling kunnen worden genomen in plaats van gemigreerd.
- Met het visualiseren van afhankelijkheden kunt u ervoor zorgen dat er geen onverwachte storingen optreden tijdens de migratie.
- Deze functie is vooral nuttig als u niet volledig op de hoogte bent van machines die deel uitmaken van apps en daarom samen moeten worden gemigreerd naar Azure.


> [!NOTE]
> De functie voor visualisatie van afhankelijkheden is niet beschikbaar in Azure Government.

## <a name="agent-based-and-agentless"></a>Op agent en zonder agent

Er zijn twee opties voor het implementeren van afhankelijkheids visualisatie:

- **Visualisatie van afhankelijkheid van agents**: deze optie is momenteel beschikbaar als preview-versie en is alleen verkrijgbaar voor virtuele VMware-machines. U hoeft geen agents op computers te installeren. 
    - Het werkt door de TCP-verbindings gegevens vast te leggen van de computers waarvoor deze is ingeschakeld. [Meer informatie](how-to-create-group-machine-dependencies-agentless.md).
Nadat de detectie van afhankelijkheden is gestart, verzamelt het apparaat gegevens van machines met een polling-interval van vijf minuten.
    - De volgende gegevens worden verzameld:
        - TCP-verbindingen
        - Namen van processen die actieve verbindingen hebben
        - Namen van geïnstalleerde toepassingen die de bovenstaande processen uitvoeren
        - Nee. van verbindingen die zijn gedetecteerd bij elk polling-interval
- **Visualisatie van afhankelijkheid op basis van een agent**: voor het gebruik van op agents gebaseerde afhankelijkheids visualisatie moet u de volgende agents downloaden en installeren op elke on-premises computer die u wilt analyseren.  
    - [Microsoft Monitoring Agent (MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)) moet op elke computer worden geïnstalleerd. Meer [informatie](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-mma) over het installeren van de MMA-agent.
    - De [afhankelijkheids agent](../azure-monitor/platform/agents-overview.md#dependency-agent) moet op elke computer worden geïnstalleerd. Meer [informatie](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-dependency-agent) over het installeren van de afhankelijkheids agent.
    - En als u VM's zonder internetverbinding hebt, moet u op deze machines Log Analytics-gateway downloaden en installeren.

## <a name="agent-based-requirements"></a>Vereisten op basis van een agent

### <a name="what-do-i-need-to-deploy-dependency-visualization"></a>Wat heb ik nodig om de afhankelijkheids visualisatie te implementeren?

Voordat u een afhankelijkheids visualisatie implementeert, moet er een Azure Migrate project aanwezig zijn, met het Azure Migrate: Server assessment tool is toegevoegd aan het project. U kunt de visualisatie van de afhankelijkheid implementeren nadat u een Azure Migrate apparaat hebt ingesteld om uw on-premises machines te detecteren.

Meer [informatie](how-to-assess.md) over het toevoegen van het hulp programma en het implementeren van een apparaat voor [Hyper-V](how-to-set-up-appliance-hyper-v.md)-, [VMware](how-to-set-up-appliance-vmware.md)-en fysieke servers.


### <a name="how-does-it-work"></a>Hoe werkt het?

Azure Migrate gebruikt de [servicetoewijzing](../operations-management-suite/operations-management-suite-service-map.md) oplossing in [Azure monitor logboeken](../log-analytics/log-analytics-overview.md) voor de visualisatie van afhankelijkheden.

- Als u gebruik wilt maken van afhankelijkheids visualisatie, moet u een Log Analytics werk ruimte (nieuw of bestaand) koppelen aan een Azure Migrate-project.
- De werk ruimte moet zich in hetzelfde abonnement bevinden als waarin u het Azure Migrate project maakt.
- Azure Migrate ondersteunt werk ruimten die zich in het VS-Oost, Zuidoost-Azië en Europa-west regio's bevinden. Werk ruimten in andere regio's kunnen niet worden gekoppeld aan een project. Houd er ook rekening mee dat de werk ruimte zich in een regio moet bevinden waarin [servicetoewijzing wordt ondersteund](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).
- De werk ruimte voor een Azure Migrate project kan niet worden gewijzigd nadat deze is toegevoegd.
- In Log Analytics wordt de werk ruimte die is gekoppeld aan Azure Migrate gelabeld met de sleutel van het migratie project en de project naam.

    ![Log Analytics werk ruimte navigeren](./media/concepts-dependency-visualization/oms-workspace.png)



### <a name="do-i-need-to-pay-for-it"></a>Moet ik hiervoor betalen?

Voor afhankelijkheids visualisatie zijn Servicetoewijzing en een gekoppelde Log Analytics-werk ruimte vereist. 

- De Servicetoewijzing oplossing heeft geen kosten in rekening gebracht voor de eerste 180 dagen. Dit is vanaf de dag dat u de werk ruimte Log Analytics hebt gekoppeld aan het Azure Migrate-project.
- Na 180 dagen zijn de standaard Log Analytics kosten van toepassing.
- Bij het gebruik van een andere oplossing dan Servicetoewijzing in de gekoppelde Log Analytics werk ruimte worden [standaard log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) kosten in rekening gebracht.
- Wanneer het Azure Migrate project wordt verwijderd, wordt de werk ruimte samen niet verwijderd. Na het verwijderen van het project is Servicetoewijzing gebruik niet gratis en worden voor elk knoop punt de kosten in rekening gebracht volgens de betaalde laag van Log Analytics werk ruimte.

Meer informatie over prijzen voor Azure Migrate vindt u [hier](https://azure.microsoft.com/pricing/details/azure-migrate/).

> [!NOTE]
> Als u projecten hebt die u hebt gemaakt vóór Azure Migrate algemene Beschik baarheid in 28 februari 2018, hebt u mogelijk extra Servicetoewijzing kosten in rekening gebracht. Om ervoor te zorgen dat u na 180 dagen alleen betaalt, is het raadzaam om een nieuw project te maken, omdat de bestaande werk ruimten voor algemene Beschik baarheid nog steeds toerekenbaar zijn.



### <a name="how-do-i-manage-the-workspace"></a>Hoe kan ik de werk ruimte beheren?

- Wanneer u agents registreert bij de werk ruimte, gebruikt u de ID en de sleutel van het Azure Migrate project.
- U kunt de Log Analytics-werk ruimte gebruiken buiten Azure Migrate.
- Als u het gekoppelde Azure Migrate project verwijdert, wordt de werk ruimte niet automatisch verwijderd. U moet [deze hand matig verwijderen](../azure-monitor/platform/manage-access.md).
- Verwijder de werk ruimte die is gemaakt door Azure Migrate, tenzij u het Azure Migrate project verwijdert. Als u dat wel doet, werkt de visualisatie functionaliteit voor afhankelijkheden niet zoals verwacht.

## <a name="next-steps"></a>Volgende stappen
- [Machines groeperen met behulp van machine afhankelijkheden](how-to-create-group-machine-dependencies.md)
- Meer [informatie](common-questions-discovery-assessment.md#what-is-dependency-visualization) over de veelgestelde vragen over de visualisatie van afhankelijkheden.


