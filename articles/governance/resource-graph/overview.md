---
title: Overzicht van Azure Resource Graph
description: Ontdek hoe de Azure Resource Graph-service complexe query's van resources op schaal mogelijk maakt voor abonnementen en tenants.
ms.date: 03/02/2020
ms.topic: overview
ms.openlocfilehash: f5c091f60faedb76e3ca6cd68505c06f51be21b6
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381522"
---
# <a name="what-is-azure-resource-graph"></a>Wat is Azure Resource Graph?

Azure Resource Graph is een service in Azure die is ontworpen om Azure Resource Management uit te breiden door efficiënte en performante bronverkenning te bieden met de mogelijkheid om op schaal te zoeken in een bepaalde set abonnementen, zodat u uw omgeving effectief beheren. Deze query's bieden de volgende mogelijkheden:

- De mogelijkheid om resources op te vragen met geavanceerde opties voor filteren, groeperen en sorteren op resource-eigenschappen.
- Vermogen om iteratief resources te verkennen op basis van governancevereisten.
- De mogelijkheid om de impact van het toepassen van een beleid in een grote cloudomgeving te beoordelen.
- Mogelijkheid om [wijzigingen in resourceeigenschappen (voorbeeld) in detail te beschrijven.](./how-to/get-resource-changes.md)

In deze documentatie komt elke mogelijkheid gedetailleerd aan bod.

> [!NOTE]
> Azure Resource Graph voorziet in de zoekbalk van azure portal, de nieuwe browse 'Alle resources' ervaring en Azure Policy's [Change history](../policy/how-to/determine-non-compliance.md#change-history-preview)
> _visual diff_. Het is ontworpen om klanten te helpen bij het beheren van grootschalige omgevingen.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Hoe Resource Graph Azure Resource Manager aanvult

Azure Resource Manager ondersteunt momenteel query's boven basisresourcevelden, met name - Resourcenaam, ID, Type, Resourcegroep, Abonnement en Locatie. Resource Manager biedt ook faciliteiten voor het aanroepen van individuele resourceproviders voor gedetailleerde eigenschappen één resource tegelijk.

Met Azure Resource Graph hebt u toegang tot de eigenschappen die de resourceproviders retourneren zonder dat u elke resourceprovider afzonderlijk moet aanroepen. Voor een lijst met ondersteunde resourcetypen raadpleegt u de [verwijzing tabel en resourcetype](./reference/supported-tables-resources.md). Een alternatieve manier om ondersteunde resourcetypen te bekijken, is via de [Azure Resource Graph Explorer-schemabrowser.](./first-query-portal.md#schema-browser)

Met Azure Resource Graph u het als nog niet doen:

- Krijg toegang tot de eigenschappen die worden geretourneerd door resourceproviders zonder dat u afzonderlijke gesprekken hoeft te voeren naar elke resourceprovider.
- Bekijk de afgelopen 14 dagen van de wijzigingsgeschiedenis die in de resource is gemaakt om te zien welke eigenschappen zijn gewijzigd en wanneer. (preview)

## <a name="how-resource-graph-is-kept-current"></a>Hoe Resource Graph actueel wordt gehouden

Wanneer een Azure-bron wordt bijgewerkt, wordt Resource Graph door Resource Manager op de hoogte gesteld van de wijziging.
Resource Graph werkt vervolgens de database bij. Resource Graph doet ook een regelmatige _volledige scan_. Deze scan zorgt ervoor dat resourcegrafiekgegevens actueel zijn als er meldingen worden gemist of wanneer een bron buiten Resource Beheer wordt bijgewerkt.

> [!NOTE]
> Resource Graph `GET` gebruikt een naar de nieuwste niet-preview API van elke resourceprovider om eigenschappen en waarden te verzamelen. Als gevolg hiervan is de verwachte woning mogelijk niet beschikbaar. In sommige gevallen is de gebruikte API-versie overschreven om meer huidige of veel gebruikte eigenschappen in de resultaten te bieden. Zie de [API-versie weergeven voor elk voorbeeld van het brontype](./samples/advanced.md#apiversion) voor een volledige lijst in uw omgeving.

## <a name="the-query-language"></a>De querytaal

Nu u een beter begrip hebt van wat Azure Resource Graph is, gaan we eens kijken naar hoe u query's construeren.

Het is belangrijk te weten dat de querytaal van Azure Resource Graph is gebaseerd op de [querytaal van Kusto](/azure/data-explorer/data-explorer-overview) die wordt gebruikt door Azure Data Explorer.

Voor meer informatie over bewerkingen en functies die kunnen worden gebruikt met Azure Resource Graph raadpleegt u eerst [De querytaal van Resource Graph](./concepts/query-language.md).
Zie [Resources verkennen](./concepts/explore-resources.md) als u de resources wilt bekijken.

## <a name="permissions-in-azure-resource-graph"></a>Machtigingen in Azure Resource Graph

Om Resource Graph te kunnen gebruiken, moet u over de juiste machtigingen beschikken in [Op rollen gebaseerd toegangsbeheer](../../role-based-access-control/overview.md) (RBAC), met minimaal leestoegang tot de resources die u wilt zoeken. Zonder ten minste `read`-machtigingen voor het Azure-object of de objectgroep worden er geen resultaten geretourneerd.

> [!NOTE]
> Resource Graph gebruikt de abonnementen die beschikbaar zijn voor een opdrachtgever tijdens het inloggen. Als u resources wilt zien van een nieuw abonnement dat tijdens een actieve sessie is toegevoegd, moet de hoofdsom de context vernieuwen. Deze actie gebeurt automatisch wanneer u zich afmeldt en weer inlogt.

Azure CLI en Azure PowerShell gebruiken abonnementen waartoe de gebruiker toegang heeft. Wanneer de REST API rechtstreeks wordt gebruikt, wordt de abonnementslijst door de gebruiker verstrekt. Als de gebruiker toegang heeft tot een van de abonnementen in de lijst, worden de queryresultaten geretourneerd voor de abonnementen waartoe de gebruiker toegang heeft. Dit gedrag is hetzelfde als bij het aanroepen [van resourcegroepen - Lijst](/rest/api/resources/resourcegroups/list) \- waartoe u toegang hebt tot resourcegroepen zonder enige aanwijzing dat het resultaat gedeeltelijk kan zijn.
Als er geen abonnementen in de abonnementslijst staan waarop de gebruiker de juiste rechten heeft, is het antwoord een _403_ (Verboden).

## <a name="throttling"></a>Beperking

Als gratis service worden query's naar Resource Graph beperkt om de beste ervaring en reactietijd voor alle klanten te bieden. Als uw organisatie de Resource Graph API wil gebruiken voor grootschalige en frequente query's, gebruikt u portal 'Feedback' op de [portalpagina resourcegrafiek.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/ResourceGraph)
Geef uw business case op en schakel het selectievakje 'Microsoft kan u e-mailen over uw feedback' in om het team contact met u op te laten nemen.

Resource Graph geeft query's op gebruikersniveau. Het serviceantwoord bevat de volgende HTTP-koppen:

- `x-ms-user-quota-remaining`(int): het resterende resourcequotum voor de gebruiker. Deze waarde wordt toegewezen aan het aantal query's.
- `x-ms-user-quota-resets-after`(hh:mm:ss): de tijdsduur totdat het quotumverbruik van een gebruiker wordt gereset

Zie [Richtlijnen voor aangezocht voor aangezocht.](./concepts/guidance-for-throttled-requests.md)

## <a name="running-your-first-query"></a>Uw eerste query uitvoeren

Azure Resource Graph Explorer, onderdeel van azure portal, maakt het mogelijk om Resource Graph-query's rechtstreeks in Azure-portal uit te voeren. Maak de resultaten vast als dynamische grafieken om realtime dynamische informatie te verstrekken aan uw portalworkflow. Zie [Eerste query met Azure Resource Graph Explorer voor](first-query-portal.md)meer informatie.

Resource Graph ondersteunt Azure CLI, Azure PowerShell, Azure SDK voor .NET en meer. De query is voor elke taal hetzelfde gestructureerd. Meer informatie over het inschakelen van Resource Graph met:

- [Azure-portal en Resource Graph Explorer](first-query-portal.md) 
- [Azure-CLI](first-query-azurecli.md#add-the-resource-graph-extension)
- [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module)

## <a name="next-steps"></a>Volgende stappen

- Voer uw eerste query uit met de [Azure-portal.](first-query-portal.md)
- Voer uw eerste query uit met [Azure CLI](first-query-azurecli.md).
- Voer uw eerste query uit met [Azure PowerShell](first-query-powershell.md).