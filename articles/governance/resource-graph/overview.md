---
title: Overzicht van Azure Resource Graph
description: Meer informatie over hoe u met de Azure resource Graph-service complexe query's kunt uitvoeren op resources op schaal in abonnementen en tenants.
ms.date: 10/21/2019
ms.topic: overview
ms.openlocfilehash: 1e84de19d35f0c5f5d7975c8a94d5164100013e4
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76936522"
---
# <a name="what-is-azure-resource-graph"></a>Wat is Azure Resource Graph?

Azure resource Graph is een service in azure die is ontworpen om Azure Resource Management uit te breiden door efficiënte en beste bronnen te verkennen met de mogelijkheid om op schaal te zoeken in een bepaalde set abonnementen, zodat u effectief kunt bepalen wat uw variabelen. Deze query's bieden de volgende mogelijkheden:

- De mogelijkheid om resources op te vragen met geavanceerde opties voor filteren, groeperen en sorteren op resource-eigenschappen.
- De mogelijkheid om resources iteratief te verkennen op basis van beheer vereisten.
- De mogelijkheid om de impact van het toepassen van een beleid in een grote cloudomgeving te beoordelen.
- De mogelijkheid om [wijzigingen in de resource-eigenschappen](./how-to/get-resource-changes.md) (preview-versie) te beschrijven.

In deze documentatie komt elke mogelijkheid gedetailleerd aan bod.

> [!NOTE]
> Met de Azure Portal zoek balk van Azure-resource grafiek, de nieuwe Blader functie voor alle resources en de [wijzigings geschiedenis](../policy/how-to/determine-non-compliance.md#change-history-preview) van de Azure Policy
> _Visual diff_. Het is ontworpen om klanten te helpen grootschalige omgevingen te beheren.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Hoe Resource Graph Azure Resource Manager aanvult

Azure Resource Manager ondersteunt momenteel query's over basis resource velden, met name resource naam, ID, type, resource groep, abonnement en locatie. Resource Manager biedt ook voorzieningen voor het aanroepen van afzonderlijke resource providers voor gedetailleerde eigenschappen per resource per keer.

Met Azure Resource Graph hebt u toegang tot de eigenschappen die de resourceproviders retourneren zonder dat u elke resourceprovider afzonderlijk moet aanroepen. Zoek voor een lijst met ondersteunde resource typen naar een **Ja** in de tabel [resources voor implementatie van volledige modus](../../azure-resource-manager/templates/complete-mode-deletion.md) . Meer resource typen vindt u in de gerelateerde [resource grafiek tabellen](./concepts/query-language.md#resource-graph-tables). Een alternatieve manier om ondersteunde resource typen te bekijken, is via de [schema browser van de Azure resource Graph Explorer](./first-query-portal.md#schema-browser).

Met Azure resource Graph kunt u het volgende doen:

- Toegang krijgen tot de eigenschappen die door resource providers worden geretourneerd zonder dat ze afzonderlijke aanroepen naar elke resource provider hoeven te maken.
- Bekijk de laatste 14 dagen van de wijzigings geschiedenis voor de resource om te zien welke eigenschappen zijn gewijzigd en wanneer. (preview)

## <a name="how-resource-graph-is-kept-current"></a>Hoe de resource grafiek actueel blijft

Wanneer een Azure-resource wordt bijgewerkt, wordt de resource grafiek gewaarschuwd door de Resource Manager van de wijziging.
De resource grafiek werkt vervolgens de data base bij. Resource grafiek is ook een regel matige _volledige scan_. Deze scan zorgt ervoor dat de gegevens van de resource grafiek actueel zijn als er gemiste meldingen of wanneer een bron buiten Resource Manager wordt bijgewerkt.

> [!NOTE]
> Resource grafiek maakt gebruik van een `GET` naar de laatste niet-preview-API van elke resource provider voor het verzamelen van eigenschappen en waarden. Als gevolg hiervan is de verwachte eigenschap mogelijk niet beschikbaar. In sommige gevallen is de gebruikte API-versie onderdrukt om meer actuele of veelgebruikte eigenschappen te bieden in de resultaten. Zie de [API-versie weer geven voor elk](./samples/advanced.md#apiversion) voor beeld van een resource type voor een volledige lijst in uw omgeving.

## <a name="the-query-language"></a>De querytaal

Nu u een beter inzicht hebt in wat Azure resource Graph is, gaan we verder met het samen stellen van query's.

Het is belangrijk te weten dat de querytaal van Azure Resource Graph is gebaseerd op de [querytaal van Kusto](../../data-explorer/data-explorer-overview.md) die wordt gebruikt door Azure Data Explorer.

Voor meer informatie over bewerkingen en functies die kunnen worden gebruikt met Azure Resource Graph raadpleegt u eerst [De querytaal van Resource Graph](./concepts/query-language.md).
Zie [Resources verkennen](./concepts/explore-resources.md) als u de resources wilt bekijken.

## <a name="permissions-in-azure-resource-graph"></a>Machtigingen in Azure Resource Graph

Om Resource Graph te kunnen gebruiken, moet u over de juiste machtigingen beschikken in [Op rollen gebaseerd toegangsbeheer](../../role-based-access-control/overview.md) (RBAC), met minimaal leestoegang tot de resources die u wilt zoeken. Zonder ten minste `read`-machtigingen voor het Azure-object of de objectgroep worden er geen resultaten geretourneerd.

> [!NOTE]
> Resource grafiek maakt gebruik van de abonnementen die beschikbaar zijn voor een principal tijdens het aanmelden. Als u resources wilt zien van een nieuw abonnement dat tijdens een actieve sessie wordt toegevoegd, moet de principal de context vernieuwen. Deze actie wordt automatisch uitgevoerd wanneer u zich afmeldt en weer inschakelt.

Azure CLI en Azure PowerShell gebruiken abonnementen waartoe de gebruiker toegang heeft. Wanneer u REST API rechtstreeks gebruikt, wordt de lijst met abonnementen aangeboden door de gebruiker. Als de gebruiker toegang heeft tot een van de abonnementen in de lijst, worden de query resultaten geretourneerd voor de abonnementen waartoe de gebruiker toegang heeft. Dit gedrag is hetzelfde als bij het aanroepen van [resource groepen-lijst](/rest/api/resources/resourcegroups/list) \- u resource groepen krijgt waartoe u toegang hebt, zonder enige indicatie dat het resultaat gedeeltelijk kan zijn.
Als er geen abonnementen zijn in de lijst abonnementen waarvoor de gebruiker de juiste rechten heeft, is het antwoord een _403_ (verboden).

## <a name="throttling"></a>Beperking

Als gratis service worden query's naar resource grafiek beperkt om de beste ervaring en reactie tijd te bieden voor alle klanten. Als uw organisatie de resource Graph API wil gebruiken voor grootschalige en frequente query's, gebruikt u de feedback van de portal op de pagina van de [resource Graph-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/ResourceGraph).
Geef uw zakelijke case op en schakel het selectie vakje ' micro soft kan uw feedback per e-mail verzenden ' in om contact met u op te nemen.

Resource grafiek beperkt query's op het niveau van de gebruiker. Het service-antwoord bevat de volgende HTTP-headers:

- `x-ms-user-quota-remaining` (int): het resterende resource quotum voor de gebruiker. Deze waarde wordt toegewezen aan het aantal query's.
- `x-ms-user-quota-resets-after` (UU: mm: SS): de tijds duur tot het quotum verbruik van een gebruiker opnieuw wordt ingesteld

Zie de [richt lijnen voor beperkte aanvragen](./concepts/guidance-for-throttled-requests.md)voor meer informatie.

## <a name="running-your-first-query"></a>Uw eerste query uitvoeren

Met Azure resource Graph Explorer, onderdeel van Azure Portal, kunt u resource Graph-query's rechtstreeks in Azure Portal uitvoeren. Maak de resultaten vast als dynamische grafieken om realtime dynamische informatie te bieden aan uw portal-werk stroom. Zie [First query with Azure resource Graph Explorer](first-query-portal.md)(Engelstalig) voor meer informatie.

Resource grafiek ondersteunt Azure CLI, Azure PowerShell, Azure SDK voor .NET en meer. De query is voor elke taal hetzelfde gestructureerd. Meer informatie over het inschakelen van resource grafiek met:

- [Azure Portal en resource grafiek Verkenner](first-query-portal.md) 
- [Azure-CLI](first-query-azurecli.md#add-the-resource-graph-extension)
- [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module)

## <a name="next-steps"></a>Volgende stappen

- Voer uw eerste query uit met behulp van de [Azure Portal](first-query-portal.md).
- Voer uw eerste query uit met [Azure cli](first-query-azurecli.md).
- Voer uw eerste query uit met [Azure PowerShell](first-query-powershell.md).