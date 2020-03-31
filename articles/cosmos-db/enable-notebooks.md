---
title: Notitieblokken inschakelen in het Azure Cosmos DB-account (voorbeeld)
description: Met de ingebouwde notitieblokken van Azure Cosmos DB u uw gegevens vanuit de portal analyseren en visualiseren. In dit artikel wordt beschreven hoe u deze functie voor Cosmos-accounts inschakelen.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/22/2019
ms.author: dech
ms.openlocfilehash: dcec310db43baa513b2d574d03f3f35dee3f773b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76768020"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Notitieblokken inschakelen voor Azure Cosmos DB-accounts (voorbeeld)

> [!IMPORTANT]
> Ingebouwde notebooks voor Azure Cosmos DB zijn momenteel beschikbaar in de volgende Azure-regio's: Australië-Oost, Oost-VS, Oost-VS 2, Noord-Europa, Zuid-Centraal-VS, Zuidoost-Azië, het Verenigd Koninkrijk Zuid, West-Europa en West-VS 2. Als u notitieblokken wilt gebruiken, [maakt u een nieuw account met notitieblokken](#enable-notebooks-in-a-new-cosmos-account) of schakelt u [notitieblokken in een bestaand account](#enable-notebooks-in-an-existing-cosmos-account) in een van deze regio's in.

Met ingebouwde Jupyter-notitieblokken in Azure Cosmos DB u uw gegevens van de Azure-portal analyseren en visualiseren. In dit artikel wordt beschreven hoe u deze functie voor uw Azure Cosmos DB-account kunt inschakelen.

## <a name="enable-notebooks-in-a-new-cosmos-account"></a>Notitieblokken inschakelen in een nieuw Cosmos-account
1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1. Selecteer **Een resourcedatabases** > maken**Azure** > **Cosmos DB**.
1. Selecteer **Notitieblokken**op de pagina **Azure Cosmos DB-account** maken . 
 
    ![Optie Notitieblokken selecteren in Azure Cosmos DB Blade maken](media/enable-notebooks/create-new-account-with-notebooks.png)
1. Selecteer **Controleren + maken**. U de optie **Netwerk** en **tags** overslaan. 
1. Controleer de accountinstellingen en selecteer **Vervolgens Maken**. Het maken van het account duurt enkele minuten. Wacht tot de portalpagina wordt weergegeven **Uw implementatie is voltooid.** 

    ![Het deelvenster Meldingen in Azure Portal](media/enable-notebooks/create-new-account-with-notebooks-complete.png)
1. Selecteer **Ga naar resource om** naar de azure cosmos DB-accountpagina te gaan. 

    ![De pagina Azure Cosmos DB-account](../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png)

1. Navigeer naar het deelvenster **Gegevensverkenner.** U moet nu de werkruimte voor notitieblokken bekijken.

    ![Nieuwe werkruimte Azure Cosmos DB-laptops](media/enable-notebooks/new-notebooks-workspace.png)

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Notitieblokken inschakelen in een bestaand Cosmos-account
U ook notitieblokken inschakelen op bestaande accounts. Deze stap hoeft slechts één keer per account te worden uitgevoerd.

1. Navigeer naar het deelvenster **Gegevensverkenner** in uw Cosmos-account.
1. Selecteer **Notitieblokken inschakelen**.

    ![Een nieuwe werkruimte voor notitieblokken maken in Gegevensverkenner](media/enable-notebooks/enable-notebooks-workspace.png)
1. Dit zal u vragen om een nieuwe werkruimte voor notitieblokken te maken. Selecteer **De installatie voltooien.**
1. Uw account is nu ingeschakeld om notitieblokken te gebruiken!

## <a name="create-and-run-your-first-notebook"></a>Uw eerste notitieblok maken en uitvoeren

Als u wilt controleren of u notitieblokken gebruiken, selecteert u een van de notitieblokken onder Voorbeeldnotitieblokken. Hiermee wordt een kopie van het notitieblok opgeslagen in uw werkruimte en wordt deze geopend.

In dit voorbeeld gebruiken we **GettingStarted.ipynb**. 

![Notitieblok GettingStarted.ipynb weergeven](media/enable-notebooks/select-getting-started-notebook.png)

Ga als beste over het notitieblok:
1. Selecteer de eerste codecel die Python-code bevat. 
1. Selecteer **Uitvoeren** om de cel uit te voeren. U **Shift + Enter** ook gebruiken om de cel uit te voeren.
1. Vernieuw het resourcevenster om de database en container te zien die is gemaakt.

    ![Notitieblok aan de slag uitvoeren](media/enable-notebooks/run-first-notebook-cell.png)

U ook **Nieuw notitieblok** selecteren om een nieuw notitieblok te maken of een bestaand notitieblokbestand (.ipynb) uploaden door **Bestand uploaden** te selecteren in het menu **Mijn notitieblok.** 

![Een nieuw notitieblok maken of uploaden](media/enable-notebooks/create-or-upload-new-notebook.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de voordelen van [Azure Cosmos DB Jupyter-notitieblokken](cosmosdb-jupyter-notebooks.md)
