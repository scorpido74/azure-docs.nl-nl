---
title: Uw gegevens visualiseren met behulp van dashboards op basis van Azure Monitor-werkmappen in Azure Sentinel | Microsoft Documenten
description: Gebruik deze zelfstudie om te leren hoe u uw gegevens visualiseert met behulp van dashboards op basis van werkmappen in Azure Sentinel.
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
ms.date: 01/01/2020
ms.author: yelevin
ms.openlocfilehash: b4461ac43e9356536914b345ef28f5de62fc9f82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585217"
---
# <a name="tutorial-visualize-and-monitor-your-data"></a>Zelfstudie: Uw gegevens visualiseren en bewaken



Nadat u [uw gegevensbronnen](quickstart-onboard.md) hebt verbonden met Azure Sentinel, u de gegevens visualiseren en bewaken met behulp van de Azure Sentinel-adoptie van Azure Monitor-werkmappen, die veelzijdigheid biedt bij het maken van aangepaste dashboards. Hoewel de werkmappen anders worden weergegeven in Azure Sentinel, kan het handig zijn om te zien hoe u [interactieve rapporten maken met Azure Monitor-werkmappen.](../azure-monitor/app/usage-workbooks.md) Met Azure Sentinel u aangepaste werkmappen maken voor uw gegevens en wordt ook ingebouwde werkmapsjablonen geleverd waarmee u snel inzicht krijgen in uw gegevens zodra u een gegevensbron aansluit.


Met deze zelfstudie u uw gegevens visualiseren in Azure Sentinel.
> [!div class="checklist"]
> * Ingebouwde werkmappen gebruiken
> * Nieuwe werkmappen maken

## <a name="prerequisites"></a>Vereisten

- U moet ten minste machtigingen voor werkmaplezers of werkmapbijdragen hebben in de brongroep van de Azure Sentinel-werkruimte.

> [!NOTE]
> De werkmappen die u zien in Azure Sentinel, worden opgeslagen in de azure sentinel-werkruimtebrongroep en worden getagd door de werkruimte waarin ze zijn gemaakt.

## <a name="use-built-in-workbooks"></a>Ingebouwde werkmappen gebruiken

1. Ga naar **Werkmappen** en selecteer **Sjablonen** om de volledige lijst met ingebouwde werkmappen van Azure Sentinel weer te geven. Als u wilt zien welke relevant zijn voor de gegevenstypen die u hebt verbonden, wordt in het veld **Vereiste gegevenstypen** in elke werkmap het gegevenstype naast een groen vinkje weergegeven als u al relevante gegevens naar Azure Sentinel streamt.
  ![naar werkmappen gaan](./media/tutorial-monitor-data/access-workbooks.png)
1. Klik **op Werkmap weergeven** om de sjabloon te zien die is gevuld met uw gegevens.
  
1. Als u de werkmap wilt bewerken, selecteert u **Opslaan**en selecteert u de locatie waar u het json-bestand voor de sjabloon wilt opslaan. 

   > [!NOTE]
   > Hiermee wordt een Azure-bron gemaakt op basis van de relevante sjabloon en wordt het Json-bestand van de sjabloon zelf opgeslagen en niet de gegevens.


1. Selecteer **Werkmap weergeven**. Klik vervolgens op de knop **Bewerken** bovenaan. U de werkmap nu bewerken en aanpassen aan uw behoeften. Zie hoe u interactieve rapporten maakt met [Azure Monitor Workbooks](../azure-monitor/app/usage-workbooks.md)voor meer informatie over het aanpassen van de werkmap.
![werkmappen weergeven](./media/tutorial-monitor-data/workbook-graph.png)
1. Nadat u uw wijzigingen hebt aangebracht, u de werkmap opslaan. 

1. U de werkmap ook klonen: Selecteer **Bewerken** en vervolgens **opslaan als**, zorg ervoor dat u deze opslaat met een andere naam, onder dezelfde abonnements- en resourcegroep. Deze werkmappen worden weergegeven onder het tabblad **Mijn werkmappen.**


## <a name="create-new-workbook"></a>Nieuwe werkmap maken

1. Ga naar **Werkmappen** en selecteer **Werkmap toevoegen** om helemaal opnieuw een nieuwe werkmap te maken.
  ![naar werkmappen gaan](./media/tutorial-monitor-data/create-workbook.png)

1. Als u de werkmap wilt bewerken, selecteert u **Bewerken**en voegt u indien nodig tekst, query's en parameters toe. Zie hoe u interactieve rapporten maakt met [Azure Monitor Workbooks](../azure-monitor/app/usage-workbooks.md)voor meer informatie over het aanpassen van de werkmap. 

1. Wanneer u een query maakt, stelt u de **gegevensbron** in op **Logboeken,** wordt het **resourcetype** ingesteld op **Logboekanalyse** en kiest u vervolgens de relevante werkruimte(s). 

1. Nadat u uw werkmap hebt gemaakt, slaat u de werkmap op om ervoor te zorgen dat u deze opslaat onder de abonnements- en brongroep van uw Azure Sentinel-werkruimte.

1. Als u anderen in uw organisatie de werkmap wilt laten gebruiken, selecteert u Onder Opslaan om **Gedeelde rapporten** **te** selecteren. Als u wilt dat deze werkmap alleen voor u beschikbaar is, selecteert u **Mijn rapporten**.

1. Als u wilt schakelen tussen werkmappen in uw](./media/tutorial-monitor-data/switch.png)werkruimte, u **Open** ![Switch-werkmappen openen selecteren in het bovenste deelvenster van een werkmap. Schakel in het venster dat naar rechts opent tussen werkmappen.

   ![Werkmappen schakelen](./media/tutorial-monitor-data/switch-workbooks.png)


## <a name="how-to-delete-workbooks"></a>Werkmappen verwijderen

U werkmappen verwijderen die zijn gemaakt met een Azure Sentinel-sjabloon. 

Als u een aangepaste werkmap wilt verwijderen, selecteert u op de pagina Werkmappen de opgeslagen werkmap die u wilt verwijderen en selecteert u **Verwijderen**. Hiermee wordt de opgeslagen werkmap verwijderd.

> [!NOTE]
> Hiermee worden de resource en eventuele wijzigingen in de sjabloon verwijderd. De oorspronkelijke sjabloon blijft beschikbaar.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u uw gegevens weergeven in Azure Sentinel.

Zie [Geautomatiseerde bedreigingsreacties instellen in Azure Sentinel](tutorial-respond-threats-playbook.md)voor meer informatie over het automatiseren van uw reacties op bedreigingen.
