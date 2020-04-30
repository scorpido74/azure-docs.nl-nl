---
title: "Zelf studie: query's beheren in Azure Portal"
description: In deze zelf studie maakt u een resource Graph-query en deelt u de nieuwe query met anderen in de Azure Portal.
ms.date: 11/21/2019
ms.topic: tutorial
ms.openlocfilehash: 00cb3f95112804c81beb6bce6fc35891e6197e60
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74303941"
---
# <a name="tutorial-create-and-share-an-azure-resource-graph-query-in-the-azure-portal"></a>Zelf studie: een Azure resource Graph-query maken en delen in de Azure Portal

Met de Verkenner van Azure resource Explorer kunt u uw resource Graph-query's rechtstreeks in het Azure Portal opslaan. Er zijn twee soorten query's: _privé_ en _gedeeld_. Een persoonlijke query wordt opgeslagen in uw Azure Portal-instellingen. Terwijl een gedeelde query een resource manager-resource is die kan worden beheerd met op rollen gebaseerde toegangs beheer (RBAC) en wordt beveiligd met resource vergrendelingen. Beide typen query's worden op rest versleuteld.

Door query's op te slaan in de Azure Portal, bespaart u de tijd die u op een andere manier kunt best Eden aan uw favoriete of veelgebruikte query's. Wanneer u query's deelt, helpt u uw team de doel stellingen van consistentie en efficiëntie te realiseren door herhalingen.

In deze zelf studie voert u de volgende taken uit:

> [!div class="checklist"]
> - Een persoonlijke query maken en verwijderen
> - Een gedeelde query maken
> - Gedeelde query's detecteren
> - Een gedeelde query verwijderen

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-abonnement nodig om deze zelfstudie te voltooien. Als u er nog geen hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="create-and-delete-a-private-query"></a>Een persoonlijke query maken en verwijderen

Persoonlijke query's zijn toegankelijk en alleen zichtbaar voor het account waarmee ze worden gemaakt. Wanneer ze worden opgeslagen in de Azure Portal-instellingen van een account, kunnen ze alleen worden gemaakt, gebruikt en verwijderd uit de Azure Portal. Een persoonlijke query is geen Resource Manager-resource. Voer de volgende stappen uit om een nieuwe persoonlijke query te maken:

1. Selecteer in het menu Portal **alle services** of gebruik het zoekvak van Azure boven aan alle pagina's. Zoek en selecteer **resource Graph Explorer**.

1. Voer op het tabblad **query 1** op de pagina Azure resource Graph Explorer de volgende query in:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

   Selecteer **query uitvoeren** om de query resultaten in het onderste deel venster weer te geven.

   Zie voor meer informatie over deze query voor [beelden: aantal virtuele machines per OS-type](../samples/starter.md#count-virtual-machines-by-os-type).


1. Selecteer **Opslaan** of **Opslaan als**, geef de **virtuele machines van het besturings systeem op** als naam, behoud het type als **persoonlijke query**en selecteer vervolgens **Opslaan** onder aan het deel venster **query opslaan** . De titel van het tabblad verandert van **query 1** in **aantal vm's per besturings systeem**.

1. Ga in de Azure Portal van Azure resource Graph Explorer weg en ga vervolgens terug naar het. U ziet dat de opgeslagen query niet meer wordt weer gegeven en dat het tabblad **query 1** wordt geretourneerd.

1. Selecteer **een query openen**. Zorg ervoor dat het type een **persoonlijke query**is. De opgeslagen naam- **vm's met een aantal van het besturings systeem** worden nu weer gegeven in de lijst **query naam** . Wanneer u de koppeling titel van de opgeslagen query selecteert, wordt deze in een nieuw tabblad met de naam van die query geladen.

   > [!NOTE] 
   > Wanneer een opgeslagen query is geopend en de naam van het tabblad wordt weer gegeven, wordt deze door de knop **Opslaan** bijgewerkt met de wijzigingen die zijn aangebracht. Als u een nieuwe opgeslagen query wilt maken op basis van deze open query, selecteert u **Opslaan als** en gaat u door alsof u een gloed nieuwe query opslaat.

1. Als u de opgeslagen query wilt verwijderen, selecteert u opnieuw **een query openen** en controleert u of het veld **type** is ingesteld op **privé-query**. Selecteer in de rij van de `Count VMs by OS` opgeslagen query **verwijderen** (pictogram Prullenbak). Selecteer **Ja** in het bevestigings dialoogvenster om het verwijderen van de query te volt ooien.
   Sluit vervolgens het deel venster **een query openen** .

## <a name="create-a-shared-query"></a>Een gedeelde query maken

In tegens telling tot een persoonlijke query is een gedeelde query een resource manager-resource. Dit betekent dat de query wordt opgeslagen in een resource groep, kan worden beheerd en beheerd met RBAC en kan zelfs worden beveiligd met resource vergrendelingen. Als resource kan iedereen met de juiste machtigingen deze zien en gebruiken.
Voer de volgende stappen uit om een nieuwe gedeelde query te maken:

1. Selecteer in het menu Portal **alle services**, of gebruik het zoekvak van Azure boven aan alle pagina's om de **resource Graph Explorer**te zoeken en te selecteren.

1. Voer op het tabblad **query 1** op de pagina Azure resource Graph Explorer de volgende query in:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```
    
   Selecteer **query uitvoeren** om de query resultaten in het onderste deel venster weer te geven.

   Zie voor meer informatie over deze query voor [beelden: aantal virtuele machines per OS-type](../samples/starter.md#count-virtual-machines-by-os-type).

1. Selecteer **Opslaan** of **Opslaan als**.

   
   ![Sla de nieuwe query op met behulp van de knop Opslaan](../media/create-share-query/save-shared-query-buttons.png)

1. In het deel venster **query opslaan** voert u **aantal vm's per besturings systeem** in voor de naam.

1. Wijzig het type in een **gedeelde query**, stel de beschrijving in op het **aantal virtuele machines per OS-type**en stel **abonnement** in om op te geven waar de query bron wordt gemaakt.

1. Zorg ervoor dat het selectie vakje **publiceren naar resource-Graph-query's resource groep** is geselecteerd en de locatie van de **resource groep** is ingesteld op **(VS) West-Centraal VS**.

1. Selecteer **Opslaan** onder in het deel venster **query opslaan** . De titel van het tabblad verandert van **query 1** in **aantal vm's per besturings systeem**. De eerste keer dat de bron groep **resource-Graph-query's** wordt gebruikt, duurt het opslaan langer dan verwacht, omdat de resource groep wordt gemaakt.
   
   ![De nieuwe query opslaan als een gedeelde query](../media/create-share-query/save-shared-query-window.png)

   > [!NOTE] 
   > Als u de naam van een bestaande resource groep wilt opgeven voor het opslaan van de gedeelde query, schakelt u het selectie vakje **publiceren naar resource-Graph-query's-resource groep** uit. Het gebruik van de standaard benoemde resource groep voor query's maakt het gemakkelijker om gedeelde query's te detecteren. Ook wordt het doel van die resource groep duidelijker. U kunt er echter voor kiezen om om veiligheids redenen een bestaande resource groep te selecteren op basis van bestaande machtigingen.

1. Ga in de Azure Portal van Azure resource Graph Explorer weg en ga vervolgens terug naar het. U ziet dat de opgeslagen query niet meer wordt weer gegeven en dat het tabblad **query 1** wordt geretourneerd.

1. Selecteer **een query openen**. Controleer of het type is ingesteld op **gedeelde query** en de combi natie van het **abonnement** en de **resource groep** overeenkomen waar u de query hebt opgeslagen. De opgeslagen **aantallen vm's per besturingssysteem** item worden nu weer gegeven in de lijst **query naam** . Selecteer de titel koppeling van de opgeslagen query om deze in een nieuw tabblad met de naam van de query te laden. Als een gedeelde query wordt een pictogram weer gegeven op het tabblad naast de titel, waarbij het wordt aangegeven als gedeeld.

   ![Het pictogram Gedeelde query weer geven](../media/create-share-query/show-saved-shared-query.png)

   > [!NOTE] 
   > Wanneer een opgeslagen query is geopend en de naam van het tabblad wordt weer gegeven, wordt deze door de knop **Opslaan** bijgewerkt met de wijzigingen die zijn aangebracht. Als u een nieuwe opgeslagen query wilt maken, selecteert u **Opslaan als** en gaat u door alsof u een gloed nieuwe query opslaat.

## <a name="discover-shared-queries"></a>Gedeelde query's detecteren

Omdat een gedeelde query een resource manager-resource is, zijn er verschillende manieren om er een te vinden:

- Selecteer in resource Graph Explorer de optie **een query openen** en stel het type in op **gedeelde query**.
- Via de pagina Resource Graph-query's Portal.
- Van de resource groep waarin de gedeelde query is opgeslagen.
- Via een query naar een resource grafiek.

### <a name="view-resource-graph-queries"></a>Resource Graph-query's weer geven

In de Azure Portal geeft de pagina Resource grafiek query's gedeelde query's weer waartoe het aangemelde account toegang heeft. Op deze pagina kunt u filteren op naam, abonnement, resource groep en andere eigenschappen van de resource grafiek query. U kunt ook resource Graph-query's labelen, exporteren en verwijderen met deze interface.

Als u een van de query's selecteert, wordt de pagina Resource grafiek query geopend. Net als andere Resource Manager-resources biedt deze pagina een interactief overzicht samen met het activiteiten logboek, Toegangs beheer en tags. U kunt ook rechtstreeks vanaf deze pagina een resource vergrendeling Toep assen.

Ga naar de pagina Resource Graph-query's in het menu van de portal door **alle services** te selecteren of door de Azure Search-vak boven aan alle pagina's te gebruiken. Zoek en selecteer **resource Graph Explorer**.

### <a name="list-resource-groups-resources"></a>Resources van resource groepen weer geven

De resource Graph-query wordt weer gegeven naast andere resources die deel uitmaken van een resource groep.
Als u de resource Graph-query selecteert, wordt de pagina voor die query geopend. De opties voor het weglatings teken en snelmenu (geactiveerd door met de rechter muisknop te klikken) werken op dezelfde manier als op de query pagina van de resource grafiek.

### <a name="query-resource-graph"></a>Resource grafiek opvragen

U kunt met behulp van een query naar een resource grafiek zoeken naar resource Graph-query's. De volgende resource grafiek query limieten per `Microsoft.ResourceGraph/queries`type en gebruikt `project` om alleen de naam, het gewijzigde tijdstip en de query zelf weer te geven:

```kusto
Resources
| where type == "microsoft.resourcegraph/queries"
| project name, properties.timeModified, properties.query
```

## <a name="delete-a-shared-query"></a>Een gedeelde query verwijderen

Als een gedeelde query niet meer nodig is, verwijdert u deze. Door een gedeelde query te verwijderen, verwijdert u de bijbehorende resource manager-resource. Alle Dash boards waarnaar de resultaten grafiek is vastgemaakt, worden nu weer gegeven met een fout bericht. Wanneer dit fout bericht wordt weer gegeven, gebruikt u de knop **verwijderen uit dash board** om uw dash board op te schonen.

U kunt een gedeelde query verwijderen via de volgende interfaces:
- Pagina Resource grafiek query's
- Query pagina voor resource grafiek
- De pagina **een query openen** in resource Graph Explorer
- Pagina Resource groepen

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze zelf studie, verwijdert u de persoonlijke en gedeelde query's die u hebt gemaakt als u deze niet meer nodig hebt.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u persoonlijke en gedeelde query's gemaakt. Ga verder naar de pagina met details van de query taal voor meer informatie over de taal van de resource grafiek.

> [!div class="nextstepaction"]
> [Meer informatie over de query taal](../concepts/query-language.md)