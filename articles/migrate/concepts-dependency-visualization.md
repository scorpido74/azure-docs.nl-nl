---
title: Afhankelijkheids visualisatie in Azure Migrate | Microsoft Docs
description: Hierin wordt een overzicht gegeven van de evaluatie berekeningen in de server Assessment-service in Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: hamusa
ms.openlocfilehash: 33594e09778b9a629645e12357e6bafe561ad35e
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202910"
---
# <a name="dependency-visualization"></a>Visualisatie van afhankelijkheden

Azure Migrate: Server evaluatie evalueert groepen van on-premises machines voor migratie naar Azure. U kunt de functionaliteit voor de visualisatie van afhankelijkheden in Server evaluatie gebruiken om groepen te maken. Dit artikel bevat informatie over deze functie.

> [!NOTE]
> De functie voor visualisatie van afhankelijkheden is niet beschikbaar in Azure Government.

## <a name="overview"></a>Overzicht

Met de functie voor afhankelijkheids visualisatie in Server evaluatie kunt u groepen met hoge betrouw baarheid maken voor migratie-evaluaties. Met behulp van afhankelijkheids visualisatie kunt u netwerk afhankelijkheden van machines weer geven en gerelateerde computers identificeren die samen moeten worden gemigreerd naar Azure. Deze functionaliteit is handig in scenario's waarin u niet volledig op de hoogte bent van de computers die uw toepassing vormen en samen moeten worden gemigreerd naar Azure.

## <a name="before-you-start"></a>Voordat u begint

- Zorg ervoor dat u een Azure Migrate project hebt [gemaakt](how-to-add-tool-first-time.md) .
- Als u al een project hebt gemaakt, moet u ervoor zorgen dat u de Azure Migrate hebt [toegevoegd](how-to-assess.md) : Hulp programma Server Assessment.
- Zorg ervoor dat u uw computers hebt gedetecteerd in Azure Migrate. u kunt dit doen door een Azure Migrate apparaat in te stellen voor [VMware](how-to-set-up-appliance-vmware.md) of [Hyper-V](how-to-set-up-appliance-hyper-v.md). Het apparaat detecteert on-premises machines en stuurt meta gegevens en prestatie gegevens naar Azure Migrate: Server-evaluatie. [Meer informatie](migrate-appliance.md).

## <a name="how-does-it-work"></a>Hoe werkt het?

Azure Migrate gebruikt de [servicetoewijzing](../operations-management-suite/operations-management-suite-service-map.md) oplossing in [Azure monitor logboeken](../log-analytics/log-analytics-overview.md) voor de visualisatie van afhankelijkheden.
- Als u gebruik wilt maken van afhankelijkheids visualisatie, moet u een Log Analytics werk ruimte (nieuw of bestaand) koppelen aan een Azure Migrate-project.
- U kunt alleen een werk ruimte maken of koppelen in hetzelfde abonnement als het Azure Migrate project is gemaakt.
- Een Log Analytics-werk ruimte koppelen aan een project:
    1. Op het tabblad **servers** , in **Azure migrate: Tegel server** beoordeling, klikt u op **overzicht**.
    2. Klik in het **overzicht**op de pijl-omlaag om **essentiële**items uit te vouwen.
    3. Klik in de **OMS-werk ruimte**op **configuratie vereist**.
    4. In **werk ruimte configureren**geeft u op of u een nieuwe werk ruimte wilt maken of een bestaande wilt gebruiken:
    
    ![Werkruimte toevoegen](./media/how-to-create-group-machine-dependencies/workspace.png)

- Tijdens het koppelen van een werk ruimte krijgt u de mogelijkheid om een nieuwe werk ruimte te maken of een bestaande te koppelen:
  - Wanneer u een nieuwe werk ruimte maakt, moet u een naam opgeven voor de werk ruimte. U kunt de [regio](https://azure.microsoft.com/global-infrastructure/regions/) kiezen waarin de werk ruimte wordt gemaakt.
  - Wanneer u een bestaande werk ruimte koppelt, kunt u kiezen uit alle beschik bare werk ruimten in hetzelfde abonnement als het migratie project. Houd er rekening mee dat alleen deze werk ruimten worden weer gegeven die zijn gemaakt in een regio waar [servicetoewijzing wordt ondersteund](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites). Als u een werk ruimte wilt koppelen, moet u ervoor zorgen dat u toegang hebt tot de werk ruimte.

  > [!NOTE]
  > Zodra u een werk ruimte aan een project hebt gekoppeld, kunt u deze later niet meer wijzigen.

  > [!NOTE]
  > Azure Migrate biedt momenteel ondersteuning voor het maken van OMS-werk ruimte in VS-Oost, Zuidoost-Azië en Europa-west regio's. Als de werk ruimte wordt gemaakt buiten Azure Migrate in een andere regio, kan deze momenteel niet worden gekoppeld aan een Azure Migrate-project. 

- De gekoppelde werk ruimte is gelabeld met het sleutel **migratie project**en de waarde van de **project naam**, die u kunt gebruiken om te zoeken in de Azure Portal.
- Als u wilt navigeren naar de werk ruimte die is gekoppeld aan het project, gaat u naar de sectie met **essentiële onderdelen** van de pagina **overzicht** van het project en opent u de werk ruimte

    ![Log Analytics werk ruimte navigeren](./media/concepts-dependency-visualization/oms-workspace.png)

Als u visualisatie van afhankelijkheden wilt gebruiken, moet u agents downloaden en installeren op elke on-premises computer die u wilt analyseren.  

- [Micro soft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) moet op elke computer worden geïnstalleerd. Meer [informatie](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-mma) over het installeren van de MMA-agent.
- De [afhankelijkheids agent](../azure-monitor/platform/agents-overview.md#dependency-agent) moet op elke computer worden geïnstalleerd. Meer [informatie](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-dependency-agent) over het installeren van de afhankelijkheids agent.
- En als u VM's zonder internetverbinding hebt, moet u op deze machines Log Analytics-gateway downloaden en installeren.

U hebt deze agents niet nodig op computers die u wilt beoordelen, tenzij u gebruikmaakt van afhankelijkheids visualisatie.

## <a name="do-i-need-to-pay-for-it"></a>Moet ik hiervoor betalen?

De functie voor het visualisatie gebied is gratis beschikbaar. Voor het gebruik van de functie voor het betrekken van afhankelijkheden in Server evaluatie is Servicetoewijzing vereist. u moet een Log Analytics werk ruimte (nieuw of bestaand) koppelen aan het Azure Migrate project. De functionaliteit van de afhankelijkheids visualisatie in Server evaluatie is gratis voor de eerste 180 dagen.

1. Het gebruik van oplossingen met uitzonde ring van Servicetoewijzing in deze Log Analytics werk ruimte worden [standaard log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) kosten in rekening gebracht.
2. Ter ondersteuning van migratie scenario's zonder extra kosten Servicetoewijzing, worden voor de eerste 180 dagen geen kosten in rekening gebracht vanaf de dag van de koppeling van de Log Analytics werk ruimte met het Azure Migrate project. Na 180 dagen zijn de standaard Log Analytics kosten van toepassing.

Wanneer u agents registreert bij de werk ruimte, gebruikt u de ID en de sleutel die is opgegeven door het project op de pagina agent stappen installeren.

Wanneer het Azure Migrate project wordt verwijderd, wordt de werk ruimte samen niet verwijderd. Post de verwijdering van het project, het Servicetoewijzing gebruik is niet gratis en elk knoop punt wordt in rekening gebracht volgens de betaalde laag van Log Analytics werk ruimte.

> [!NOTE]
> De visualisatie functie dependency maakt gebruik van Servicetoewijzing via een Log Analytics-werk ruimte. Sinds 28 februari 2018, met de aankondiging van Azure Migrate algemene Beschik baarheid, is de functie nu gratis beschikbaar. U moet een nieuw project maken om de werk ruimte gratis gebruik te kunnen gebruiken. Bestaande werk ruimten voor algemene Beschik baarheid worden nog steeds in rekening gebracht. Daarom raden we u aan om over te stappen op een nieuw project.

Meer informatie over prijzen voor Azure Migrate vindt u [hier](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="how-do-i-manage-the-workspace"></a>Hoe kan ik de werk ruimte beheren?

U kunt de Log Analytics-werk ruimte gebruiken buiten Azure Migrate. Het wordt niet verwijderd als u het Azure Migrate project verwijdert waarin het is gemaakt. Als u de werk ruimte niet meer nodig hebt, [verwijdert u deze](../azure-monitor/platform/manage-access.md) hand matig.

Verwijder de werk ruimte die is gemaakt door Azure Migrate, tenzij u het Azure Migrate project verwijdert. Als u dat wel doet, werkt de visualisatie functionaliteit voor afhankelijkheden niet zoals verwacht.

## <a name="next-steps"></a>Volgende stappen
- [Machines groeperen met behulp van machine afhankelijkheden](how-to-create-group-machine-dependencies.md)
- Meer [informatie](https://docs.microsoft.com/azure/migrate/resources-faq#what-is-dependency-visualization) over de veelgestelde vragen over de visualisatie van afhankelijkheden.
