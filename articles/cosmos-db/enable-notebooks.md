---
title: Notitie blokken in het Azure Cosmos DB-account inschakelen (preview)
description: Met de ingebouwde notitie blokken van Azure Cosmos DB kunt u uw gegevens in de portal analyseren en visualiseren. In dit artikel wordt beschreven hoe u deze functie inschakelt voor Cosmos-accounts.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/22/2019
ms.author: dech
ms.openlocfilehash: dcec310db43baa513b2d574d03f3f35dee3f773b
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768020"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Notebooks inschakelen voor Azure Cosmos DB accounts (preview-versie)

> [!IMPORTANT]
> Ingebouwde notitie blokken voor Azure Cosmos DB zijn momenteel beschikbaar in de volgende Azure-regio's: Australië-oost, VS-Oost, VS-Oost 2, Europa-noord, Zuid-Centraal VS, Zuidoost-Azië, UK-zuid, Europa-west en VS-West 2. Als u notebooks wilt gebruiken, [maakt u een nieuw account met notitie blokken](#enable-notebooks-in-a-new-cosmos-account) of [schakelt u notitie blokken in voor een bestaand account](#enable-notebooks-in-an-existing-cosmos-account) in een van deze regio's.

Met ingebouwde Jupyter-notebooks in Azure Cosmos DB kunt u uw gegevens van de Azure Portal analyseren en visualiseren. In dit artikel wordt beschreven hoe u deze functie voor uw Azure Cosmos DB-account kunt inschakelen.

## <a name="enable-notebooks-in-a-new-cosmos-account"></a>Notitie blokken in een nieuw Cosmos-account inschakelen
1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).
1. Selecteer **Een resource maken** > **Databases** > **Azure Cosmos DB**.
1. Selecteer op de pagina **Azure Cosmos DB account maken** de optie **notebooks**. 
 
    ![Selecteer de optie notebooks in Azure Cosmos DB Blade maken](media/enable-notebooks/create-new-account-with-notebooks.png)
1. Selecteer **Controleren + maken**. U kunt de optie **netwerk** en **Tags** overs Laan. 
1. Controleer de account instellingen en selecteer vervolgens **maken**. Het duurt enkele minuten om het account te maken. Wacht tot de portal pagina **uw implementatie is voltooid**. 

    ![Het deelvenster Meldingen in Azure Portal](media/enable-notebooks/create-new-account-with-notebooks-complete.png)
1. Selecteer **Ga naar resource** om naar de pagina Azure Cosmos DB-account te gaan. 

    ![De pagina Azure Cosmos DB-account](../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png)

1. Ga naar het deel venster **Data Explorer** . Nu ziet u de werk ruimte notitie blokken.

    ![Nieuwe Azure Cosmos DB notitie blokken-werk ruimte](media/enable-notebooks/new-notebooks-workspace.png)

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Notebooks inschakelen in een bestaand Cosmos-account
U kunt ook notebooks inschakelen op bestaande accounts. Deze stap hoeft slechts één keer per account te worden uitgevoerd.

1. Ga naar het **Data Explorer** deel venster in uw Cosmos-account.
1. Selecteer **notebooks inschakelen**.

    ![Een nieuwe werk ruimte voor notitie blokken maken in Data Explorer](media/enable-notebooks/enable-notebooks-workspace.png)
1. Hiermee wordt u gevraagd om een nieuwe werk ruimte voor notitie blokken te maken. Selecteer **volledige installatie.**
1. Uw account is nu ingeschakeld voor het gebruik van notitie blokken.

## <a name="create-and-run-your-first-notebook"></a>Uw eerste notitie blok maken en uitvoeren

Als u wilt controleren of u notebooks kunt gebruiken, selecteert u een van de notitie blokken onder voorbeeld notitieblokken. Hiermee wordt een kopie van het notitie blok opgeslagen in uw werk ruimte en geopend.

In dit voor beeld gebruiken we **GettingStarted. ipynb**. 

![GettingStarted. ipynb-notebook weer geven](media/enable-notebooks/select-getting-started-notebook.png)

Als u het notitie blok wilt uitvoeren:
1. Selecteer de eerste code cel die python-code bevat. 
1. Selecteer **uitvoeren** om de cel uit te voeren. U kunt ook **SHIFT + ENTER** gebruiken om de cel uit te voeren.
1. Vernieuw het resource venster om de data base en de container te zien die zijn gemaakt.

    ![Aan de slag-notebook uitvoeren](media/enable-notebooks/run-first-notebook-cell.png)

U kunt ook **Nieuw notitie blok** selecteren om een nieuw notitie blok te maken of een bestaand notebook bestand (. ipynb) te uploaden door **Upload File** te selecteren in het menu **mijn notitie blokken** . 

![Een nieuw notitie blok maken of uploaden](media/enable-notebooks/create-or-upload-new-notebook.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de voor delen van [Azure Cosmos DB Jupyter-notebooks](cosmosdb-jupyter-notebooks.md)
