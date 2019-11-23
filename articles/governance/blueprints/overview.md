---
title: Overzicht van Azure Blueprints
description: Understand how the Azure Blueprints service enables you to create, define, and deploy artifacts in your Azure environment.
ms.date: 11/21/2019
ms.topic: overview
ms.openlocfilehash: 07d84d658d88e977cd73176861e5c5e080c02857
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74321768"
---
# <a name="what-is-azure-blueprints"></a>Wat is Azure Blueprints?

Net zoals een blauwdruk het een ingenieur of architect mogelijk maakt om de ontwerpparameters voor een project te schetsen, stelt Azure Blueprints cloudarchitecten en centrale IT-groepen in staat om een ​​herhaalbare set Azure-resources te definiëren die voldoet aan de normen, patronen en vereisten van een organisatie en deze implementeert. Met Azure Blueprints kunnen ontwikkelteams snel nieuwe omgevingen bouwen en instellen in het volste vertrouwen dat ze voldoen aan de vereisten van de organisatie, met een set ingebouwde componenten, zoals netwerken, om de ontwikkeling en levering te versnellen.

Blauwdrukken zijn een declaratieve manier om de implementatie van diverse resourcesjablonen en andere artefacten te orkestreren, zoals:

- Roltoewijzingen
- Beleidstoewijzingen
- Azure Resource Manager-sjablonen
- Resourcegroepen

De Azure Blueprints-service wordt gesteund door de wereldwijd gedistribueerde [Azure Cosmos DB](../../cosmos-db/introduction.md).
Blauwdrukobjecten worden naar meerdere Azure-regio's gerepliceerd. Deze replicatie biedt lage latentie, hoge beschikbaarheid en consistente toegang tot uw blauwdrukobjecten, ongeacht in welke regio uw resources worden geïmplementeerd.

## <a name="how-its-different-from-resource-manager-templates"></a>Waarin verschilt het van Resource Manager-sjablonen?

De service is ontworpen om u te helpen met de _setup van de omgeving_. Deze setup bestaat vaak uit een set resourcegroepen, beleidsmaatregelen, roltoewijzingen en Resource Manager-sjabloonimplementaties. Een blauwdruk is een pakket waarmee al deze _artefact_typen bij elkaar worden gebracht en u in staat wordt gesteld om dat pakket samen te stellen en versies ervan te beheren – ook via een CI/CD-pijplijn. Uiteindelijk wordt elke blauwdruk toegewezen aan een abonnement in één bewerking die kan worden gecontroleerd en bijgehouden.

Bijna alles dat u voor implementatie in Blueprints wilt opnemen, kan worden bereikt met een Resource Manager-sjabloon. Een Resource Manager-sjabloon is echter een document dat niet in Azure zelf bestaat. Elke sjabloon wordt lokaal of in broncodebeheer opgeslagen. De sjabloon wordt gebruikt voor de implementatie van een of meer Azure-resources, maar zodra die resources zijn geïmplementeerd, is er geen actieve verbinding of relatie meer met de sjabloon.

Met Blueprints blijft de relatie tussen de blauwdrukdefinitie (wat _moet worden_ geïmplementeerd) en de blauwdruktoewijzing (wat _is_ geïmplementeerd) behouden. Deze verbinding ondersteunt verbeterde tracering en controle van implementaties. Met blauwdrukken kunt u bovendien in één keer een upgrade uitvoeren van meerdere abonnementen die zijn onderworpen aan dezelfde blauwdruk.

U hoeft niet te kiezen tussen een Resource Manager-sjabloon en een blauwdruk. Elke blauwdruk kan bestaan uit nul of meer Resource Manager-sjabloon_artefacten_. Deze ondersteuning betekent dat eerdere inspanningen om een ​​bibliotheek met Resource Manager-sjablonen te ontwikkelen en te onderhouden, in Blueprints kunnen worden hergebruikt.

## <a name="how-its-different-from-azure-policy"></a>Wat is het verschil met Azure Policy?

Een blauwdruk is een pakket of container voor het samenstellen van focusspecifieke sets van standaarden, patronen en vereisten met betrekking tot de implementatie van Azure-cloudservices, beveiliging en ontwerp, die kunnen worden hergebruikt om consistentie en naleving te behouden.

Een [beleid](../policy/overview.md) is een systeem voor standaard toestaan en expliciet weigeren dat is gericht op resource-eigenschappen tijdens implementatie en voor reeds bestaande resources. Het ondersteunt cloudgovernance door te valideren dat resources binnen een abonnement voldoen aan vereisten en normen.

Door een beleid in een blauwdruk op te nemen, kunnen tijdens de toewijzing van de blauwdruk het juiste patroon of ontwerp worden gemaakt. Door een beleid op te nemen, wordt ervoor gezorgd dat alleen goedgekeurde of verwachte wijzigingen kunnen worden aangebracht in de omgeving, om continue naleving van de intentie van de blauwdruk te beschermen.

Een beleid kan worden opgenomen als een van de vele _artefacten_ in de definitie van een blauwdruk. Blauwdrukken ondersteunen ook het gebruik van parameters in beleid en initiatieven.

## <a name="blueprint-definition"></a>Blauwdrukdefinitie

Een blauwdruk bestaat uit _artefacten_. Blauwdrukken ondersteunen momenteel de volgende resources als artefacten:

|Bron  | Hiërarchieopties| Beschrijving  |
|---------|---------|---------|
|Resourcegroepen | Abonnement | Een nieuwe resourcegroep maken voor gebruik door andere artefacten binnen de blauwdruk.  Met deze tijdelijke resourcegroepen kunt u resources precies zo indelen als u ze wilt structureren, en het bereik beperken voor opgenomen beleids- en roltoewijzingsartefacten, en Azure Resource Manager-sjablonen. |
|Azure Resource Manager-sjabloon | Abonnement, resourcegroep | Templates, including nested and linked templates, are used to compose complex environments. Voorbeeldomgevingen: een SharePoint-farm, Azure Automation State Configuration of een Log Analytics-werkruimte. |
|Beleidstoewijzing | Abonnement, resourcegroep | Hiermee kan een beleid of initiatief worden toegewezen aan het abonnement waaraan de blauwdruk wordt toegewezen. Het beleid of initiatief moet binnen het bereik van de locatie van de blauwdrukdefinitie vallen. Als het beleid of initiatief parameters heeft, worden deze parameters toegewezen bij het maken of toewijzen van de blauwdruk. |
|Roltoewijzing | Abonnement, resourcegroep | Voeg een bestaande gebruiker of groep toe aan een ingebouwde rol om ervoor te zorgen dat de juiste mensen altijd over de juiste toegang tot uw bronnen beschikken. Roltoewijzingen kunnen voor het hele abonnement worden gedefinieerd of genest in een specifieke resourcegroep die in de blauwdruk is opgenomen. |

### <a name="blueprint-definition-locations"></a>Locaties van blauwdrukdefinities

Bij het maken van een blauwdrukdefinitie definieert u waar de blauwdruk wordt opgeslagen. Momenteel kunnen blauwdrukken worden opgeslagen in een [beheergroep](../management-groups/overview.md) of abonnement waartoe u **Inzender**-toegang hebt. Als de locatie een beheergroep is, is de blauwdruk beschikbaar voor toewijzing aan elk onderliggend abonnement van die beheergroep.

### <a name="blueprint-parameters"></a>Blauwdrukparameters

Blauwdrukken kunnen parameters doorgeven aan een beleid/initiatief of een Azure Resource Manager-sjabloon.
Tijdens het toevoegen van een van deze _​​artefacten_ aan een blauwdruk, besluit de auteur om een ​​gedefinieerde waarde op te geven voor elke blauwdruktoewijzing, of toe te staan ​​dat elke blauwdruktoewijzing op het moment van de toewijzing een waarde opgeeft.
Dankzij deze flexibiliteit hebt u de optie om een ​​vooraf vastgestelde waarde te definiëren voor elk gebruik van de blauwdruk of om die beslissing te nemen op het moment van toewijzing.

> [!NOTE]
> Een blauwdruk kan zijn eigen parameters hebben, maar deze kunnen op dit moment alleen worden gemaakt als een blauwdruk wordt gegenereerd vanuit de REST API in plaats van via de portal.

Zie [blauwdrukparameters](./concepts/parameters.md) voor meer informatie.

### <a name="blueprint-publishing"></a>Blauwdruk publiceren

Wanneer een blauwdruk wordt gemaakt, bevindt deze zich in de modus **Concept**. Wanneer de blauwdruk klaar is om te worden toegewezen, moet deze worden **Gepubliceerd**. Om een blauwdruk te publiceren moet u een **Versie**-tekenreeks definiëren (letters, cijfers en afbreekstreepjes, maximaal 20 tekens lang), plus eventuele **Wijzigingsnotities**. De **versie** onderscheidt deze blauwdruk van toekomstige gewijzigde versies ervan, en maakt het mogelijk elke versie toe te wijzen. Dit versiebeheer betekent ook dat verschillende **versies** van dezelfde blueprint kunnen worden toegewezen aan hetzelfde abonnement. When additional changes are made to the blueprint, the **Published**
**Version** still exists, as do the **Unpublished changes**. Nadat de wijzigingen voltooid zijn, wordt de bijgewerkte blauwdruk **Gepubliceerd** met een nieuwe en unieke **versie**, en kan nu ook worden toegewezen.

## <a name="blueprint-assignment"></a>Blauwdruktoewijzing

Each **Published** **Version** of a blueprint can be assigned (with a max name length of 90 characters) to an existing subscription. In de portal wordt standaard de **versie** van de blauwdruk gebruikt die het laatst is **gepubliceerd**. Als er artefactparameters (of blauwdrukparameters) zijn, worden de parameters tijdens het toewijzingsproces gedefinieerd.

## <a name="permissions-in-azure-blueprints"></a>Machtigingen in Azure Blueprints

Om blauwdrukken te kunnen gebruiken, moet u zijn gemachtigd via [op rollen gebaseerd toegangsbeheer](../../role-based-access-control/overview.md) (RBAC). Om blauwdrukken te maken, moet uw account de volgende machtigingen hebben:

- `Microsoft.Blueprint/blueprints/write` - Een blauwdrukdefinitie maken
- `Microsoft.Blueprint/blueprints/artifacts/write` - Artefacten in een blauwdrukdefinitie maken
- `Microsoft.Blueprint/blueprints/versions/write` - Een blauwdruk publiceren

Om blauwdrukken te verwijderen, moet uw account de volgende machtigingen hebben:

- `Microsoft.Blueprint/blueprints/delete`
- `Microsoft.Blueprint/blueprints/artifacts/delete`
- `Microsoft.Blueprint/blueprints/versions/delete`

> [!NOTE]
> De machtigingen voor de blauwdrukdefinitie moeten worden verleend of overgenomen van het beheergroep- of abonnementbereik waarin deze is opgeslagen.

Als u een blauwdruk wilt toewijzen of de toewijzing ongedaan wilt maken, heeft uw account de volgende machtigingen nodig:

- `Microsoft.Blueprint/blueprintAssignments/write` - Een blauwdruk toewijzen
- `Microsoft.Blueprint/blueprintAssignments/delete` - De toewijzing van een blauwdruk ongedaan maken

> [!NOTE]
> Omdat blauwdruktoewijzingen op een abonnement worden gemaakt, moeten de machtigingen voor het toewijzen van blauwdrukken en het ongedaan maken van toewijzingen worden toegekend op abonnementsbereik of worden overgenomen in een abonnementsbereik.

The following built-in roles are available:

|RBAC Role | Beschrijving |
|-|-|
|[Eigenaar](../../role-based-access-control/built-in-roles.md#owner) | In addition to other permissions, includes all Azure Blueprint related permissions. |
|[Inzender](../../role-based-access-control/built-in-roles.md#contributor) | In addition to other permissions, can create and delete blueprint definitions, but doesn't have blueprint assignment permissions. |
|[Blueprint Contributor](../../role-based-access-control/built-in-roles.md#blueprint-contributor) | Can manage blueprint definitions, but not assign them. |
|[Blueprint Operator](../../role-based-access-control/built-in-roles.md#blueprint-operator) | Can assign existing published blueprints, but can't create new blueprint definitions. Blueprint assignment only works if the assignment is done with a user-assigned managed identity. |

Als deze ingebouwde rollen niet aan uw beveiligingsbehoeften voldoen, kunt u een [aangepaste rol](../../role-based-access-control/custom-roles.md) maken.

> [!NOTE]
> If using a system-assigned managed identity, the service principal for Azure Blueprints requires the **Owner** role on the assigned subscription in order to enable deployment. Als u de portal gebruikt, wordt deze rol automatisch verleend en ingetrokken voor de implementatie. Als u de REST API gebruikt, moet deze rol handmatig worden toegekend, maar wordt deze automatisch ingetrokken nadat de implementatie is voltooid. If using a user-assigned managed identity, only the user creating the blueprint assignment needs the `Microsoft.Blueprint/blueprintAssignments/write` permission, which is included in both the **Owner** and **Blueprint Operator** built-in roles.

## <a name="naming-limits"></a>Naming limits

The following limitations exist for certain fields:

|Object|Veld|Allowed Characters|Met maximaal Lengte|
|-|-|-|-|
|Blueprint|Naam|letters, numbers, hyphens, and periods|48|
|Blueprint|Version|letters, numbers, hyphens, and periods|20|
|Blauwdruktoewijzing|Naam|letters, numbers, hyphens, and periods|90|
|Blueprint artifact|Naam|letters, numbers, hyphens, and periods|48|

## <a name="video-overview"></a>Video-overzicht

Het volgende overzicht van Azure Blueprints is afkomstig van Azure Fridays. Als u de video wilt downloaden, gaat u naar [Azure Fridays - An overview of Azure Blueprints](https://channel9.msdn.com/Shows/Azure-Friday/An-overview-of-Azure-Blueprints) op Channel 9.

> [!VIDEO https://www.youtube.com/embed/cQ9D-d6KkMY]

## <a name="next-steps"></a>Volgende stappen

- [Create a blueprint - Portal](./create-blueprint-portal.md).
- [Create a blueprint - PowerShell](./create-blueprint-powershell.md).
- [Create a blueprint - REST API](./create-blueprint-rest-api.md).