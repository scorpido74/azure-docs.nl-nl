---
title: Uw gegevens visualiseren met behulp van Azure Monitor werkmappen in azure Sentinel | Microsoft Docs
description: Gebruik deze zelf studie om te leren hoe u uw gegevens kunt visualiseren met behulp van werkmappen in azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2020
ms.author: yelevin
ms.openlocfilehash: 8d8f1343d92f66dc464ab7064949bbabb813268e
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83798530"
---
# <a name="tutorial-visualize-and-monitor-your-data"></a>Zelf studie: uw gegevens visualiseren en bewaken



Zodra u [uw gegevens bronnen](quickstart-onboard.md)   aan Azure Sentinel hebt gekoppeld, kunt u de gegevens visualiseren en bewaken met behulp van de Azure Sentinel-acceptatie van Azure monitor werkmappen, die veelzijdigheid biedt bij het maken van aangepaste Dash boards. Hoewel de werkmappen anders worden weer gegeven in de Azure-Sentinel, kan het nuttig zijn om te zien hoe u [interactieve rapporten met Azure monitor werkmappen maakt](../azure-monitor/platform/workbooks-overview.md). Met Azure Sentinel kunt u aangepaste werkmappen maken voor uw gegevens en ook met ingebouwde werkmap sjablonen, zodat u snel inzicht kunt krijgen in uw gegevens zodra u verbinding maakt met een gegevens bron.


Deze zelf studie helpt u bij het visualiseren van uw gegevens in azure Sentinel.
> [!div class="checklist"]
> * Ingebouwde werkmappen gebruiken
> * Nieuwe werkmappen maken

## <a name="prerequisites"></a>Vereisten

- U moet mini maal over machtigingen voor de werkmap lezer of werkmap beschikken voor de resource groep van de Azure Sentinel-werk ruimte.

> [!NOTE]
> De werkmappen die u in azure Sentinel kunt zien, worden opgeslagen in de resource groep van de Azure-Sentinel-werk ruimte en worden gelabeld door de werk ruimte waarin ze zijn gemaakt.

## <a name="use-built-in-workbooks"></a>Ingebouwde werkmappen gebruiken

1. Ga naar **werkmappen** en selecteer vervolgens **sjablonen** om de volledige lijst met ingebouwde Azure-Sentinel-werkmappen weer te geven. Om te zien welke relevant zijn voor de gegevens typen die u hebt verbonden, wordt in het veld **vereiste gegevens typen** in elke werkmap het gegevens type weer gegeven naast een groen vinkje als u al relevante gegevens streamt naar Azure Sentinel.
  ![Ga naar werkmappen](./media/tutorial-monitor-data/access-workbooks.png)
1. Klik op **werkmap weer geven** om de sjabloon te bekijken die is ingevuld met uw gegevens.
  
1. Als u de werkmap wilt bewerken, selecteert u **Opslaan**en selecteert u vervolgens de locatie waar u het JSON-bestand voor de sjabloon wilt opslaan. 

   > [!NOTE]
   > Hiermee wordt een Azure-resource gemaakt op basis van de relevante sjabloon en wordt het JSON-sjabloon bestand zelf en niet de gegevens opgeslagen.


1. Selecteer **werkmap weer geven**. Klik vervolgens bovenaan op de knop **bewerken** . U kunt de werkmap nu bewerken en aanpassen op basis van uw behoeften. Zie [interactieve rapporten maken met Azure monitor werkmappen](../azure-monitor/platform/workbooks-overview.md)voor meer informatie over het aanpassen van de werkmap.
![werkmappen weer geven](./media/tutorial-monitor-data/workbook-graph.png)
1. Nadat u de wijzigingen hebt aangebracht, kunt u de werkmap opslaan. 

1. U kunt de werkmap ook klonen: Selecteer **bewerken** en vervolgens **Opslaan als**, en zorg ervoor dat u deze opslaat onder een andere naam, onder hetzelfde abonnement en dezelfde resource groep. Deze werkmappen worden weer gegeven op het tabblad **mijn werkmappen** .


## <a name="create-new-workbook"></a>Nieuwe werkmap maken

1. Ga naar **werkmappen** en selecteer vervolgens **werkmap toevoegen** om een nieuwe werkmap helemaal opnieuw te maken.
  ![Ga naar werkmappen](./media/tutorial-monitor-data/create-workbook.png)

1. Als u de werkmap wilt bewerken, selecteert u **bewerken**en voegt u indien nodig tekst, query's en para meters toe. Zie [interactieve rapporten maken met Azure monitor werkmappen](../azure-monitor/platform/workbooks-overview.md)voor meer informatie over het aanpassen van de werkmap. 

1. Wanneer u een query bouwt, stelt u de **gegevens bron** in op **Logboeken**, wordt het **resource type** ingesteld op **log Analytics** en kiest u vervolgens de relevante werk ruimte (n). 

1. Nadat u de werkmap hebt gemaakt, slaat u de werkmap op en zorgt u ervoor dat u deze opslaat onder het abonnement en de resource groep van uw Azure Sentinel-werk ruimte.

1. Als u wilt dat anderen in uw organisatie de werkmap kunnen gebruiken, klikt u onder **Opslaan om** **gedeelde rapporten**te selecteren. Als u wilt dat deze werkmap alleen voor u beschikbaar is, selecteert u **mijn rapporten**.

1. Als u wilt scha kelen tussen werkmappen in uw werk ruimte, kunt u **Open** ![ Switch-werkmappen selecteren ](./media/tutorial-monitor-data/switch.png) in het bovenste deel venster van een werkmap. In het venster dat aan de rechter kant wordt geopend, schakelt u tussen werkmappen.

   ![Scha kelen tussen werkmappen](./media/tutorial-monitor-data/switch-workbooks.png)


## <a name="how-to-delete-workbooks"></a>Werkmappen verwijderen

U kunt werkmappen verwijderen die zijn gemaakt op basis van een Azure-Sentinel-sjabloon. 

Als u een aangepaste werkmap wilt verwijderen, selecteert u op de pagina werkmappen de opgeslagen werkmap die u wilt verwijderen en selecteert u **verwijderen**. Hiermee wordt de opgeslagen werkmap verwijderd.

> [!NOTE]
> Hiermee verwijdert u de resource en wijzigingen die u hebt aangebracht in de sjabloon. De oorspronkelijke sjabloon blijft beschikbaar.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u uw gegevens in azure Sentinel kunt weer geven.

Zie voor meer informatie over het automatiseren van uw reacties op bedreigingen [automatische bedreigings reacties instellen in azure Sentinel](tutorial-respond-threats-playbook.md).
