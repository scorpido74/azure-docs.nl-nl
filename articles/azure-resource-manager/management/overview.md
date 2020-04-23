---
title: Overzicht
description: Hierin wordt beschreven hoe u Azure Resource Manager kunt gebruiken voor implementatie, beheer, en beheer van toegang tot resources in Azure.
ms.topic: overview
ms.date: 04/21/2020
ms.openlocfilehash: 253fc2f296fa764a6c22fa1331221df60ca21bb5
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870494"
---
# <a name="what-is-azure-resource-manager"></a>Wat is Azure Resource Manager?

Azure Resource Manager is de implementatie- en beheersservice voor Azure. Het biedt een beheerlaag waarmee u resources in uw Azure-account maken, bijwerken en verwijderen. U gebruikt beheerfuncties, zoals toegangscontrole, vergrendelingen en tags, om uw resources na implementatie te beveiligen en te ordenen.

Zie [Overzicht van](../templates/overview.md)azure resource manager voor meer informatie over Azure Resource Manager-sjablonen .

## <a name="consistent-management-layer"></a>Consistente beheerlaag

Wanneer een gebruiker een verzoek verzendt vanuit een van de Azure-hulpprogramma's, API's of SDK's, ontvangt Resource Manager de aanvraag. Het verifieert en machtigt het verzoek. Resourcemanager stuurt de aanvraag naar de Azure-service, waarbij de gevraagde actie wordt uitgevoerd. Omdat alle aanvragen worden verwerkt via dezelfde API, ziet u consistente resultaten en mogelijkheden in de verschillende hulpprogramma's.

In de volgende afbeelding ziet u de rol die Azure Resource Manager speelt bij het afhandelen van Azure-aanvragen.

![Resource Manager-aanvraagmodel](./media/overview/consistent-management-layer.png)

Alle mogelijkheden die beschikbaar zijn in de Azure-portal zijn ook beschikbaar via Azure PowerShell, Azure CLI, de Azure REST API's en client-SDK's. Functionaliteit die oorspronkelijk wordt uitgegeven via API's, is binnen 180 dagen na de eerste release beschikbaar in de portal.

## <a name="terminology"></a>Terminologie

Als u nog geen ervaring hebt met de Azure Resource Manager, zijn er enkele termen die u mogelijk niet kent.

* **resource** - een beheerbaar item dat beschikbaar is via Azure. Virtuele machines, opslagaccounts, web-apps, databases en virtuele netwerken zijn voorbeelden van resources. Resourcegroepen, abonnementen, beheergroepen en tags zijn ook voorbeelden van resources.
* **resourcegroep** - een container met gerelateerde resources voor een Azure-oplossing. De resourcegroep bevat die resources die u als groep wilt beheren. U bepaalt welke resources deel uitmaken van een resourcegroep op basis van wat voor uw organisatie het meest zinvol is. Zie [Resourcegroepen](#resource-groups).
* **resourceprovider** - een service die zorgt voor Azure-resources. Voorbeeld: een veelgebruikte resourceprovider is Microsoft.Compute, die de resource van de virtuele machine levert. Microsoft.Storage is een andere algemene resourceprovider. Zie [Resourceproviders en -typen](resource-providers-and-types.md).
* **Resource manager-sjabloon** - Een JSON-bestand (JavaScript Object Notation) dat een of meer bronnen definieert die moeten worden geïmplementeerd in een resourcegroep, abonnement, beheergroep of tenant. De sjabloon kan worden gebruikt om de resources consistent en herhaaldelijk te implementeren. Zie [Overzicht van sjabloonimplementatie](../templates/overview.md).
* **declaratieve syntaxis** - een syntaxis waarmee u kunt aangeven 'Dit is wat ik wil maken' zonder hiervoor de nodige reeks programmeeropdrachten te hoeven maken. De sjabloon Resource Manager is een voorbeeld van een declaratieve syntaxis. In het bestand definieert u de eigenschappen voor de infrastructuur te implementeren naar Azure.  Zie [Overzicht van sjabloonimplementatie](../templates/overview.md).

## <a name="the-benefits-of-using-resource-manager"></a>De voordelen van Resource Manager

Met Resource Manager u het als nog niet doen:

* Beheer uw infrastructuur via declaratieve sjablonen in plaats van scripts.

* Implementeer, beheer en controleer alle resources voor uw oplossing als groep, in plaats van deze resources afzonderlijk te verwerken.

* Implementeer uw oplossing gedurende de hele ontwikkelingslevenscyclus en heb er vertrouwen in dat uw resources consistent zijn geïmplementeerd.

* Definieer de afhankelijkheden tussen resources, zodat ze in de juiste volgorde worden geïmplementeerd.

* Pas toegangscontrole toe op alle services omdat RBAC (Role-Based Access Control) native is geïntegreerd in het beheerplatform.

* Tags toepassen op resources om logisch alle bronnen in uw abonnement te ordenen.

* Verhelder de facturering van uw organisatie door de kosten weer te geven voor een groep resources die dezelfde tag delen.

## <a name="understand-scope"></a>Bereik

Azure biedt vier scopeniveaus: [beheergroepen,](../../governance/management-groups/overview.md)abonnementen, [resourcegroepen](#resource-groups)en resources. In de volgende afbeelding ziet u een voorbeeld van deze lagen.

![Managementniveaus](./media/overview/scope-levels.png)

U past beheerinstellingen toe op een of meer bereikniveaus. Het niveau dat u kiest, bepaalt de reikwijdte van de instelling. Lagere niveaus nemen instellingen over van hogere niveaus. Wanneer u bijvoorbeeld een [beleid](../../governance/policy/overview.md) toepast op het abonnement, wordt het beleid toegepast op alle brongroepen en resources in uw abonnement. Wanneer u een beleid toepast op de resourcegroep, wordt dat beleid toegepast op de resourcegroep en al zijn resources. Een andere resourcegroep heeft die beleidstoewijzing echter niet.

U sjablonen implementeren voor tenants, beheergroepen, abonnementen of resourcegroepen.

## <a name="resource-groups"></a>Resourcegroepen

Er zijn een aantal belangrijke factoren waarmee u rekening moet houden bij het definiëren van de resourcegroep:

* Alle resources in uw groep moeten dezelfde levenscyclus hebben. U implementeert ze samen, werkt ze samen bij en verwijdert ze samen. Als een resource, zoals een databaseserver, in een andere implementatiecyclus moet werken, moet deze in een andere resourcegroep worden geplaatst.

* Elke resource kan in slechte één resourcegroep voorkomen.

* Sommige resources kunnen buiten een resourcegroep bestaan. Deze resources worden geïmplementeerd in de [abonnements-,](../templates/deploy-to-subscription.md) [beheergroep](../templates/deploy-to-management-group.md)of [tenant](../templates/deploy-to-tenant.md). Alleen specifieke resourcetypen worden ondersteund bij deze scopes.

* U kunt een resource op elk gewenst moment toevoegen aan of verwijderen uit een resourcegroep.

* U kunt een resource van de ene naar de andere resourcegroep verplaatsen. Zie voor meer informatie [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](move-resource-group-and-subscription.md).

* Een resourcegroep kan resources uit verschillende regio’s bevatten.

* Een resourcegroep kan worden gebruikt voor het bepalen van de mate van toegangsbeheer voor beheertaken.

* Een resource kan communiceren met resources in andere resourcegroepen. Deze interactie is gebruikelijk wanneer er een relatie bestaat tussen de twee resources, maar deze niet dezelfde levenscyclus delen (bijvoorbeeld web-apps die verbinding maken met een database).

Als u een resourcegroep maakt, moet u voor die resourcegroep een locatie opgeven. U vraagt zich misschien af: 'Waarom heeft een resourcegroep een locatie nodig? En als de resources andere locaties kunnen hebben dan de resourcegroep, wat is dan het nut van een locatie voor de resourcegroep?' De resourcegroep slaat metagegevens op over de resources. Wanneer u een locatie voor de resourcegroep opgeeft, geeft u op waar die metagegevens zijn opgeslagen. In verband met nalevingsvereisten moet u er mogelijk voor zorgen dat uw gegevens worden opgeslagen in een bepaalde regio.

Als het gebied van de resourcegroep tijdelijk niet beschikbaar is, u de bronnen in de brongroep niet bijwerken omdat de metagegevens niet beschikbaar zijn. De resources in andere regio's werken nog steeds zoals verwacht, maar u ze niet bijwerken. Zie [Betrouwbare Azure-toepassingen ontwerpen](/azure/architecture/checklist/resiliency-per-service)voor meer informatie over het bouwen van betrouwbare toepassingen.

## <a name="resiliency-of-azure-resource-manager"></a>Tolerantie voor Azure Resource Manager

De Azure Resource Manager-service is ontworpen voor tolerantie en continue beschikbaarheid. Resourcebeheer en beheervlakbewerkingen (aanvragen die naar management.azure.com) in de REST-API worden verzonden, zijn:

* Verdeeld over regio's. Sommige diensten zijn regionaal.

* Verdeeld over beschikbaarheidszones (evenals regio's) op locaties met meerdere beschikbaarheidszones.

* Niet afhankelijk van één logisch datacenter.

* Nooit naar beneden gehaald voor onderhoudsactiviteiten.

Deze tolerantie is van toepassing op services die aanvragen ontvangen via Resource Manager. Key Vault profiteert bijvoorbeeld van deze tolerantie.

## <a name="next-steps"></a>Volgende stappen

* Zie [Resources verplaatsen naar een nieuwe resourcegroep of -abonnement](move-resource-group-and-subscription.md)voor meer informatie over het verplaatsen van resources.

* Zie Tags gebruiken om [uw Azure-bronnen te ordenen](tag-resources.md)voor meer informatie over het taggen van resources.

* Zie [Resources vergrendelen om onverwachte wijzigingen te voorkomen](lock-resources.md)voor meer informatie over het vergrendelen van resources.
