---
title: "Zelfstudie: Query's beheren in Azure-portal"
description: In deze zelfstudie maakt u een resourcegrafiekquery en deelt u de nieuwe query met anderen in de Azure-portal.
ms.date: 11/21/2019
ms.topic: tutorial
ms.openlocfilehash: 00cb3f95112804c81beb6bce6fc35891e6197e60
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74303941"
---
# <a name="tutorial-create-and-share-an-azure-resource-graph-query-in-the-azure-portal"></a>Zelfstudie: Een Azure Resource Graph-query maken en delen in de Azure-portal

Met Azure Resource Graph Explorer u uw ResourceGraph-query's rechtstreeks opslaan in de Azure-portal. Er zijn twee soorten query's: _Privé_ en _Gedeeld_. Een privéquery wordt opgeslagen in de instellingen van uw Azure-portal. Terwijl een gedeelde query een Resource Manager-bron is die kan worden beheerd met RBAC (Role-based access controls) en kan worden beveiligd met resourcevergrendelingen. Beide typen query's worden in rust versleuteld.

Door query's op te slaan in de Azure-portal, bespaart u de tijd die u anders zou kunnen besteden aan het zoeken naar uw favoriete of veelgebruikte query's. Wanneer u query's deelt, helpt u uw team doelen van consistentie en efficiëntie te realiseren door herhaling.

In deze zelfstudie voltooit u de volgende taken:

> [!div class="checklist"]
> - Een privéquery maken en verwijderen
> - Een gedeelde query maken
> - Gedeelde query's ontdekken
> - Een gedeelde query verwijderen

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-abonnement nodig om deze zelfstudie te voltooien. Als je nog geen account hebt, maak je een [gratis account](https://azure.microsoft.com/free/) aan voordat je begint.

## <a name="create-and-delete-a-private-query"></a>Een privéquery maken en verwijderen

Privéquery's zijn alleen toegankelijk en zichtbaar voor het account waarmee ze worden gemaakt. Als ze worden opgeslagen in de Azure-portalinstellingen van een account, kunnen ze alleen worden gemaakt, gebruikt en verwijderd vanuit de Azure-portal. Een privéquery is geen resourcemanager-bron. Voer de volgende stappen uit om een nieuwe privéquery te maken:

1. Selecteer in het portalmenu **Alle services** of gebruik het azure-zoekvak boven aan alle pagina's. Zoek naar en selecteer vervolgens **Resource Graph Explorer**.

1. Voer op het tabblad **Query 1** op de pagina Azure Resource Graph Explorer de volgende query in:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

   Selecteer **Query uitvoeren** om de queryresultaten in het onderste deelvenster te bekijken.

   Zie Voorbeelden – Virtuele [machines tellen op OS-type voor](../samples/starter.md#count-virtual-machines-by-os-type)meer informatie over deze query.


1. Selecteer **Opslaan** of **Opslaan als**, voer **VM's tellen op besturingssysteem** in als de naam, laat het type als **privéquery**achter en selecteer **Opslaan** onder aan het **queryvenster Opslaan.** De tabtitel verandert van **Query 1** naar **VM's tellen per besturingssysteem**.

1. Ga weg van Azure Resource Graph Explorer in de Azure-portal en keer er vervolgens naar terug. De opgeslagen query wordt niet meer weergegeven en het tabblad **Query 1** is geretourneerd.

1. Selecteer **Een query openen**. Controleer of het type **privéquery**is . De opgeslagen naam **aantal VM's per besturingssysteem** wordt nu weergegeven in de lijst **Querynaam.** Wanneer u de titelkoppeling van de opgeslagen query selecteert, wordt deze geladen naar een nieuw tabblad met de naam van die query.

   > [!NOTE] 
   > Wanneer een opgeslagen query is geopend en het tabblad de naam wordt weergegeven, wordt de knop **Opslaan** bijgewerkt met eventuele wijzigingen die zijn aangebracht. Als u een nieuwe opgeslagen query wilt maken met deze geopende query, selecteert u **Opslaan als** en gaat u verder alsof u een gloednieuwe query opslaat.

1. Als u de opgeslagen query wilt verwijderen, selecteert u **Een query** opnieuw openen en controleert u of het veld **Type** is ingesteld op **Privéquery**. Selecteer `Count VMs by OS` **Verwijderen** (prullenbakpictogram) in de rij van de opgeslagen query. Selecteer in het bevestigingsdialoogvenster **Ja** om de query te verwijderen.
   Sluit vervolgens het **deelvenster Een query openen.**

## <a name="create-a-shared-query"></a>Een gedeelde query maken

In tegenstelling tot een privéquery is een gedeelde query een resourcemanager-bron. Dit feit betekent dat de query wordt opgeslagen in een resourcegroep, kan worden beheerd en gecontroleerd met RBAC en zelfs kan worden beschermd met resourcevergrendelingen. Als resource kan iedereen die over de juiste machtigingen beschikt deze zien en gebruiken.
Voer de volgende stappen uit om een nieuwe gedeelde query te maken:

1. Selecteer in het portalmenu **Alle services**of gebruik het azure-zoekvak boven aan alle pagina's om te zoeken naar **resourcegrafiekverkenner**en selecteer deze.

1. Voer op het tabblad **Query 1** op de pagina Azure Resource Graph Explorer de volgende query in:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```
    
   Selecteer **Query uitvoeren** om de queryresultaten in het onderste deelvenster te bekijken.

   Zie Voorbeelden – Virtuele [machines tellen op OS-type voor](../samples/starter.md#count-virtual-machines-by-os-type)meer informatie over deze query.

1. Selecteer **Opslaan** of **Opslaan als**.

   
   ![De nieuwe query opslaan met de knop Opslaan](../media/create-share-query/save-shared-query-buttons.png)

1. Voer **in** het deelvenster Query opslaan **vm's per besturingssysteem** voor de naam in.

1. Wijzig het type in **Gedeelde query,** stel de beschrijving in **op Aantal virtuele machines op OS-type**en stel **Abonnement** in om op te geven waar de querybron wordt gemaakt.

1. Laat het selectievakje **Publicatie aan resource-graph-queries resourcegroep** ingeschakeld en de **locatie Resourcegroep** ingesteld op **(US) West Central US**.

1. Selecteer **Opslaan** onder aan het **queryvenster Opslaan.** De tabtitel verandert van **Query 1** naar **VM's tellen per besturingssysteem**. De eerste keer dat de **resourcegroep resourcegroep resourcegroep resourcebronnen** resource voor resource-graphs wordt gebruikt, duurt de opslag langer dan verwacht wanneer de resourcegroep wordt gemaakt.
   
   ![De nieuwe query opslaan als gedeelde query](../media/create-share-query/save-shared-query-window.png)

   > [!NOTE] 
   > U het selectievakje **Brongroep Voor publicatie naar resource-graph-queries** uitschakelen als u de naam van een bestaande resourcegroep wilt opgeven om de gedeelde query in op te slaan. Als u de standaardbenoemde resourcegroep voor query's gebruikt, kunnen gedeelde query's gemakkelijker worden ontdekt. Het maakt ook het doel van die resourcegroep duidelijker. U er echter voor kiezen om een bestaande brongroep te selecteren om veiligheidsredenen op basis van bestaande machtigingen.

1. Ga weg van Azure Resource Graph Explorer in de Azure-portal en keer er vervolgens naar terug. De opgeslagen query wordt niet meer weergegeven en het tabblad **Query 1** is geretourneerd.

1. Selecteer **Een query openen**. Controleer of het type is ingesteld op **Gedeelde query** en de combinatie **van abonnements-** en **resourcegroepgroepovereenkomst** waar u de query hebt opgeslagen. De opgeslagen **VM's voor aantal v's per OS-item** worden nu weergegeven in de lijst **Querynaam.** Selecteer de titelkoppeling van de opgeslagen query om deze te laden in een nieuw tabblad met de naam van die query. Als gedeelde query wordt een pictogram weergegeven op het tabblad naast de titel, waarin wordt aangezien dat het wordt gedeeld.

   ![De gedeelde query weergeven met pictogram](../media/create-share-query/show-saved-shared-query.png)

   > [!NOTE] 
   > Wanneer een opgeslagen query is geopend en het tabblad de naam wordt weergegeven, wordt **deze** bijgewerkt met eventuele wijzigingen die zijn aangebracht. Als u een nieuwe opgeslagen query wilt maken, selecteert u **Opslaan als** en gaat u verder alsof u een gloednieuwe query opslaat.

## <a name="discover-shared-queries"></a>Gedeelde query's ontdekken

Omdat een gedeelde query een resourcemanager-bron is, zijn er verschillende manieren om er een te vinden:

- Selecteer in Resource Graph Explorer de optie **Een query openen** en stel het type in op Gedeelde **query**.
- Ga naar de portalpagina ResourceGraph queries.
- Van de resourcegroep waarin de gedeelde query is opgeslagen.
- Via een query naar Resource Graph.

### <a name="view-resource-graph-queries"></a>Resourcegrafiekquery's weergeven

In de Azure-portal wordt op de pagina Resourcegraph-query's gedeelde query's weergegeven waartoe het ingelogde account toegang heeft. Op deze pagina u filteren op naam, abonnement, resourcegroep en andere eigenschappen van de resourcegrafiekquery. U resourcegrafiekquery's ook taggen, exporteren en verwijderen met behulp van deze interface.

Als u een van de query's selecteert, wordt de querypagina Resourcegrafiek geopend. Net als andere resourcemanagerbronnen biedt deze pagina een interactief overzicht, samen met het activiteitenlogboek, toegangsbeheer en tags. U ook rechtstreeks vanaf deze pagina een bronvergrendeling toepassen.

Ga naar de pagina Resourcegraph-query's in het portalmenu door **Alle services** te selecteren of met het Azure-zoekvak boven aan alle pagina's te gebruiken. Zoeken naar en selecteer **Resource Graph Explorer**.

### <a name="list-resource-groups-resources"></a>Brongroepen resources weergeven

De query Resourcegrafiek wordt weergegeven naast andere bronnen die deel uitmaken van een resourcegroep.
Als u de query Resourcegrafiek selecteert, wordt de pagina voor die query geopend. De opties voor het menu ellips en het snelmenu (geactiveerd door met de rechtermuisknop te klikken) werken hetzelfde als op de querypagina Resourcegraph.

### <a name="query-resource-graph"></a>Querybrongrafiek

U ResourceGraph-query's vinden via een query naar Resource Graph. De volgende resourcegrafiekquerylimieten `Microsoft.ResourceGraph/queries`per type `project` en worden vervolgens gebruikt om alleen de naam, de gewijzigde tijd en de query zelf weer te geven:

```kusto
Resources
| where type == "microsoft.resourcegraph/queries"
| project name, properties.timeModified, properties.query
```

## <a name="delete-a-shared-query"></a>Een gedeelde query verwijderen

Als een gedeelde query niet meer nodig is, verwijdert u deze. Door een gedeelde query te verwijderen, verwijdert u de bijbehorende Resource Manager-bron. Alle dashboards waaraan het resultatendiagram is vastgemaakt, geven nu een foutbericht weer. Wanneer dat foutbericht wordt weergegeven, gebruikt u de knop **Verwijderen uit het dashboard** om uw dashboard op te schonen.

U een gedeelde query verwijderen via de volgende interfaces:
- Pagina Resourcegrafiekquery's
- Pagina Resourcegrafiek-query
- De pagina **Een query openen** in Resource Graph Explorer
- Pagina Resourcegroepen

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze zelfstudie, verwijdert u de privé- en gedeelde query's die u hebt gemaakt als u deze niet meer wilt.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u privé- en gedeelde query's gemaakt. Ga voor meer informatie over de taal van de resourcegrafiek verder naar de pagina met details van de querytaal.

> [!div class="nextstepaction"]
> [Meer informatie over de querytaal](../concepts/query-language.md)