---
title: Een query maken en delen in Azure Portal
description: In deze zelf studie leert u hoe u een resource Graph-query kunt maken en deze kunt delen met anderen in de Azure Portal.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: resource-graph
ms.openlocfilehash: 10f93f34923fb2399a4b2053167576ba004ae467
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821681"
---
# <a name="tutorial-create-and-share-an-azure-resource-graph-query-in-azure-portal"></a>Zelf studie: een Azure resource Graph-query maken en delen in Azure Portal

Met Azure resource Graph Explorer kunt u uw resource grafiek query's rechtstreeks in Azure Portal opslaan. Er zijn twee soorten query's: _privé_ en _gedeeld_. Een _persoonlijke_ query wordt opgeslagen in uw Azure Portal-instellingen, maar een _gedeelde_ query is een resource manager-resource die kan worden beheerd met op rollen gebaseerd toegangs beheer (RBAC) en wordt beveiligd met resource vergrendelingen.

Bij het opslaan van query's in Azure Portal wordt uw tijd bespaard op het zoeken naar uw favoriete of veelgebruikte query's. Bij het delen van query's kunt u uw team consistent en herhaalbaar maken. In deze zelf studie voert u de volgende stappen uit:

> [!div class="checklist"]
> - Een _persoonlijke_ query maken en verwijderen
> - Een _gedeelde_ query maken
> - _Gedeelde_ query's detecteren
> - Een _gedeelde_ query verwijderen

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-abonnement nodig om deze zelfstudie te voltooien. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="create-and-delete-a-private-query"></a>Een persoonlijke query maken en verwijderen

_Persoonlijke_ query's zijn alleen toegankelijk of zichtbaar voor het account waarmee ze worden gemaakt. Als ze zijn opgeslagen in de Azure Portal instellingen van een account, kunnen ze alleen worden gemaakt, gebruikt en verwijderd uit Azure Portal. Een _persoonlijke_ query is geen Resource Manager-resource. Maak een nieuwe _persoonlijke_ query door de volgende stappen uit te voeren:

1. Selecteer in het menu Portal de optie alle services of gebruik het zoekvak van Azure boven aan alle pagina's.
   Zoek en selecteer resource Graph Explorer.

1. Voer de volgende query in op het tabblad ' query 1 ' op de pagina Azure resource Graph Explorer. Zie voor meer informatie over deze query voor [beelden: aantal virtuele machines per OS-type](../samples/starter.md#count-virtual-machines-by-os-type).
   Selecteer **query uitvoeren** om de query resultaten in het onderste deel venster weer te geven.

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

1. Selecteer **Opslaan** of **Opslaan als**, voer de _naam_ in als ' aantal Vm's per besturings systeem ', _Typ_ "persoonlijke query" en selecteer vervolgens **Opslaan** onder aan het deel venster _query opslaan_ . De titel van het tabblad verandert van ' query 1 ' in ' aantal Vm's per OS '.

1. Blader vanuit Azure resource Graph Explorer naar Azure Portal en ga vervolgens terug naar het. De opgeslagen query wordt niet meer weer gegeven en het tabblad ' query 1 ' is geretourneerd.

1. Selecteer **een query openen**. Controleer of het _type_ ' private query ' is. De opgeslagen ' aantal Vm's per OS ' wordt nu weer gegeven in de lijst _query naam_ . Selecteer de titel koppeling van de opgeslagen query en deze wordt geladen in een nieuw tabblad met de naam van die query's.

   > [!NOTE]
   > Wanneer een opgeslagen query is geopend en op het tabblad de _naam_wordt weer gegeven, wordt deze door de knop **Opslaan** bijgewerkt met de wijzigingen die zijn aangebracht. Als u een nieuwe opgeslagen query wilt maken, gebruikt u **Opslaan als** en volgt u de stappen alsof het een gloed nieuwe opgeslagen query is.

1. Als u de opgeslagen query wilt verwijderen, selecteert u opnieuw **een query openen** en controleert u of het _type_ ' private query ' is. Selecteer in de rij met de query ' aantal Vm's per besturings systeem ' het prullenbak pictogram. Selecteer **Ja** in het bevestigings venster om het verwijderen van de query te volt ooien. Sluit vervolgens het deel venster _een query openen_ .

## <a name="create-a-shared-query"></a>Een gedeelde query maken

In tegens telling tot een _persoonlijke_ query is een _gedeelde_ query een resource manager-resource. Dit betekent dat de query wordt opgeslagen in een resource groep, kan worden beheerd en beheerd met RBAC en zelfs worden beveiligd met resource vergrendelingen. Als resource kan iedereen met de juiste machtigingen deze zien en gebruiken. Maak een nieuwe _gedeelde_ query door de volgende stappen uit te voeren:

1. Selecteer in het menu Portal de optie alle services of gebruik het zoekvak van Azure boven aan alle pagina's.
   Zoek en selecteer resource Graph Explorer.

1. Voer de volgende query in op het tabblad ' query 1 ' op de pagina Azure resource Graph Explorer. Zie voor meer informatie over deze query voor [beelden: aantal virtuele machines per OS-type](../samples/starter.md#count-virtual-machines-by-os-type).
   Selecteer **query uitvoeren** om de query resultaten in het onderste deel venster weer te geven.

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

1. Selecteer **Opslaan** of **Opslaan als**.

   ![Sla de nieuwe query op met behulp van de knop Opslaan](../media/create-share-query/save-shared-query-buttons.png)

1. Voer in het deel venster _query opslaan_ de _naam_ in als ' aantal vm's per besturings systeem ', wijzig het _type_ in ' gedeelde query ', stel _Beschrijving_ in op ' telling van virtuele machines per OS-type ' en selecteer het _abonnement_ waarin de query resource wordt gemaakt. Zorg ervoor dat het selectie vakje publiceren naar resource-Graph-query's resource groep is ingeschakeld en de locatie van de _resource groep_ is ingesteld op (VS) West-Centraal vs. Selecteer vervolgens **Opslaan** onder in het deel venster _query opslaan_ . De titel van het tabblad verandert van ' query 1 ' in ' aantal Vm's per OS '. De eerste keer dat resource-Graph-query's worden gebruikt, wordt het opslaan langer naarmate de resource groep is gemaakt.

   ![De nieuwe query opslaan als een gedeelde query](../media/create-share-query/save-shared-query-window.png)

   > [!NOTE]
   > Indien gewenst, verwijdert u de controle om de naam van een bestaande resource groep op te geven waarin de gedeelde query moet worden opgeslagen. Het gebruik van de standaard benoemde resource groep voor query's maakt het gemakkelijker om _gedeelde_ query's te detecteren. Ook wordt het doel van die resource groep duidelijker. Het selecteren van een bestaande resource groep kan echter om veiligheids redenen worden uitgevoerd op basis van bestaande machtigingen.

1. Blader vanuit Azure resource Graph Explorer naar Azure Portal en ga vervolgens terug naar het. De opgeslagen query wordt niet meer weer gegeven en het tabblad ' query 1 ' is geretourneerd.

1. Selecteer **een query openen**. Controleer of het _type_ ' Shared query ' is en de combi natie van _abonnement_ en _resource groep_ overeenkomt met de locatie waar u de query hebt opgeslagen. De opgeslagen ' aantal Vm's per OS ' wordt nu weer gegeven in de lijst _query naam_ . Selecteer de titel koppeling van de opgeslagen query en deze wordt geladen in een nieuw tabblad met de naam van die query's. Als een _gedeelde_ query wordt een pictogram weer gegeven op het tabblad naast de titel waarmee het wordt aangegeven als gedeeld.

   ![Het pictogram Gedeelde query weer geven](../media/create-share-query/show-saved-shared-query.png)

   > [!NOTE]
   > Wanneer een opgeslagen query is geopend en op het tabblad de _naam_wordt weer gegeven, wordt deze door de knop **Opslaan** bijgewerkt met de wijzigingen die zijn aangebracht. Als u een nieuwe opgeslagen query wilt maken, gebruikt u **Opslaan als** en volgt u de stappen alsof het een gloed nieuwe opgeslagen query is.

## <a name="discover-shared-queries"></a>Gedeelde query's detecteren

Als een _gedeelde_ query is een resource manager-resource, kunnen ze op verschillende manieren worden gevonden:

- Selecteer in resource Graph Explorer **de optie een query openen** en _type_ instellen op ' gedeelde query '
- De pagina Resource grafiek query's Portal
- De resource groep waarin deze is opgeslagen
- Met een query voor de resource grafiek

### <a name="view-resource-graph-queries"></a>Resource Graph-query's weer geven

In Azure Portal geeft de pagina Resource grafiek query's _gedeelde_ query's weer waartoe het aangemelde account toegang heeft. Op deze pagina kunt u filteren op naam, abonnement, resource groep en andere eigenschappen van de resource grafiek query. Resource Graph-query's kunnen ook worden gelabeld, geëxporteerd en verwijderd met deze interface.

Als u een van de query's selecteert, wordt de pagina Resource grafiek query geopend. Net als andere Resource Manager-resources biedt deze pagina een interactief overzicht samen met het activiteiten logboek, Toegangs beheer en tags. Een resource vergrendeling kan ook rechtstreeks vanaf deze pagina worden toegepast.

Ga naar de pagina Resource Graph-query's in het menu van de portal door alle services te selecteren of het zoekvak van Azure boven aan alle pagina's te gebruiken. Zoek en selecteer resource Graph Explorer.

### <a name="list-resource-groups-resources"></a>Resources van resource groepen weer geven

De resource Graph-query wordt weer gegeven naast andere resources die deel uitmaken van een resource groep.
Als u de resource Graph-query selecteert, wordt de pagina voor die query geopend. Het weglatings teken of de opties met de rechter muisknop werken hetzelfde als de query pagina voor de resource grafiek.

### <a name="query-resource-graph"></a>Resource grafiek opvragen

Als Resource Manager-resource kunnen de resource grafiek query's worden gevonden met een query op resource grafiek.
De volgende resource grafiek query limieten op type `Microsoft.ResourceGraph/queries`, en vervolgens gebruikt `project` om alleen de naam, het gewijzigde tijdstip en de query zelf weer te geven:

```kusto
Resources
| where type == "microsoft.resourcegraph/queries"
| project name, properties.timeModified, properties.query
```

## <a name="delete-a-shared-query"></a>Een gedeelde query verwijderen

Als een _gedeelde_ query niet meer nodig is, verwijdert u deze. Als u een _gedeelde_ query verwijdert, wordt de werkelijke resource manager-resource verwijderd. Alle Dash boards die in de resultaten grafiek zijn vastgemaakt, worden nu weer gegeven met een fout bericht. Wanneer dit fout bericht wordt weer gegeven, gebruikt u de knop **verwijderen uit dash board** om uw dash board op te schonen.

Een _gedeelde_ query kan worden verwijderd uit de volgende interfaces:
- Pagina Resource grafiek query's
- Query pagina voor resource grafiek
- Resource grafiek Verkenner een query pagina openen
- Pagina Resource groepen

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze zelf studie, verwijdert u de _persoonlijke_ en _gedeelde_ query's die u hebt gemaakt als u deze niet meer nodig hebt.

## <a name="next-steps"></a>Volgende stappen

- Voer uw eerste query uit met [Azure Portal](../first-query-portal.md)
- Meer informatie over de [querytaal](../concepts/query-language.md)
- [Resources verkennen](../concepts/explore-resources.md)
- Voorbeelden uit [Starter query's](../samples/starter.md) bekijken
- Voorbeelden uit [Geavanceerde query's](../samples/advanced.md) bekijken
- Feedback geven op [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)