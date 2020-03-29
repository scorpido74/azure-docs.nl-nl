---
title: Stappen in een blauwdrukimplementatie
description: Leer de beveiligings- en artefactgerelateerde stappen die de Azure Blueprints-services doorlopen tijdens het maken van een blauwdruktoewijzing.
ms.date: 11/13/2019
ms.topic: conceptual
ms.openlocfilehash: daa7722fa37547929aa21b76b870f70143ae71ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76156621"
---
# <a name="stages-of-a-blueprint-deployment"></a>Stappen in een blauwdrukimplementatie

Wanneer een blauwdruk wordt geïmplementeerd, wordt een reeks acties uitgevoerd door de Azure Blueprints-service om de resources te implementeren die in de blauwdruk zijn gedefinieerd. In dit artikel vindt u informatie over wat elke stap inhoudt.

Blueprint-implementatie wordt geactiveerd door een blauwdruk toe te schrijven aan een abonnement of een bestaande toewijzing bij te [werken.](../how-to/update-existing-assignments.md) Tijdens de implementatie neemt Blueprints de volgende stappen op hoog niveau:

> [!div class="checklist"]
> - Blauwdrukken verleend eigenaar rechten
> - Het object blueprint assignment is gemaakt
> - Optioneel - Blauwdrukken maakt **door het systeem toegewezen beheerde** identiteit
> - De beheerde identiteit implementeert blauwdrukartefacten
> - Blueprint-service en **door het systeem toegewezen beheerde** identiteitsrechten worden ingetrokken

## <a name="blueprints-granted-owner-rights"></a>Blauwdrukken verleend eigenaar rechten

De azure blueprints-serviceprincipal krijgt eigenaarrechten voor het toegewezen abonnement of -abonnementen wanneer een [door het systeem toegewezen beheerde identiteit](../../../active-directory/managed-identities-azure-resources/overview.md) wordt gebruikt. Met de toegekende rol kunnen blauwdrukken de door het **systeem toegewezen beheerde** identiteit maken en later intrekken. Als u een door **de gebruiker toegewezen beheerde** identiteit gebruikt, krijgt de azure blueprints-serviceprincipal geen eigenaarrechten voor het abonnement.

De rechten worden automatisch verleend als de toewijzing via de portal wordt uitgevoerd. Als de toewijzing echter via de REST API wordt uitgevoerd, moet het verlenen van de rechten worden gedaan met een afzonderlijke API-aanroep. De Azure Blueprints `f71766dc-90d9-4b7d-bd9d-4499c4331c3f`AppId is , maar de serviceprincipal verschilt per tenant. Gebruik [Azure Active Directory Graph API](../../../active-directory/develop/active-directory-graph-api.md) en REST endpoint [servicePrincipals](/graph/api/resources/serviceprincipal) om de serviceprincipals op te halen. Geef vervolgens de azure blueprints de _rol Eigenaar_ toe via de [portal,](../../../role-based-access-control/role-assignments-portal.md) [Azure CLI,](../../../role-based-access-control/role-assignments-cli.md)Azure [PowerShell,](../../../role-based-access-control/role-assignments-powershell.md) [REST API](../../../role-based-access-control/role-assignments-rest.md)of een [Resource Manager-sjabloon](../../../role-based-access-control/role-assignments-template.md).

De Blueprints-service implementeert de resources niet rechtstreeks.

## <a name="the-blueprint-assignment-object-is-created"></a>Het object blueprint assignment is gemaakt

Een gebruiker, groep of serviceprincipal kent een blauwdruk toe aan een abonnement. Het toewijzingsobject bestaat op het abonnementsniveau waarop de blauwdruk is toegewezen. Resources die door de implementatie zijn gemaakt, worden niet uitgevoerd in de context van de implementerende entiteit.

Tijdens het maken van de blauwdruktoewijzing wordt het type [beheerde identiteit](../../../active-directory/managed-identities-azure-resources/overview.md) geselecteerd. De standaardinstelling is een **door het systeem toegewezen beheerde** identiteit. Er kan een door **de gebruiker toegewezen beheerde** identiteit worden gekozen. Wanneer u een door **de gebruiker toegewezen beheerde** identiteit gebruikt, moet deze worden gedefinieerd en machtigingen worden verleend voordat de blauwdruktoewijzing wordt gemaakt. Zowel de ingebouwde rollen [Owner als](../../../role-based-access-control/built-in-roles.md#owner) `blueprintAssignment/write` Blueprint [Operator](../../../role-based-access-control/built-in-roles.md#blueprint-operator) hebben de vereiste toestemming om een toewijzing te maken die gebruikmaakt van een door de gebruiker toegewezen **beheerde** identiteit.

## <a name="optional---blueprints-creates-system-assigned-managed-identity"></a>Optioneel - Blauwdrukken maakt door het systeem toegewezen beheerde identiteit

Wanneer [beheerde identiteit met systeemtoegewezen](../../../active-directory/managed-identities-azure-resources/overview.md) wordt geselecteerd tijdens de toewijzing, maakt Blueprints de identiteit en verleent het de beheerde identiteit de [eigenaarrol.](../../../role-based-access-control/built-in-roles.md#owner) Als een [bestaande toewijzing wordt bijgewerkt,](../how-to/update-existing-assignments.md)gebruikt Blueprints de eerder gemaakte beheerde identiteit.

De beheerde identiteit met betrekking tot de blauwdruktoewijzing wordt gebruikt om de resources die in de blauwdruk zijn gedefinieerd, te implementeren of opnieuw te implementeren. Dit ontwerp voorkomt dat opdrachten elkaar per ongeluk storen.
Dit ontwerp ondersteunt ook de [functie voor het vergrendelen](./resource-locking.md) van resources door de beveiliging van elke geïmplementeerde resource vanuit de blauwdruk te beheren.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>De beheerde identiteit implementeert blauwdrukartefacten

De beheerde identiteit activeert vervolgens de implementaties van Resource Manager van de artefacten binnen de blauwdruk in de gedefinieerde [volgorde van de volgorde .](./sequencing-order.md) De volgorde kan worden aangepast om ervoor te zorgen dat artefacten die afhankelijk zijn van andere artefacten in de juiste volgorde worden geïmplementeerd.

Een toegangsfout door een implementatie is vaak het gevolg van het niveau van toegang dat wordt verleend aan de beheerde identiteit. De Blueprints-service beheert de beveiligingslevenscyclus van de **door het systeem toegewezen beheerde** identiteit. De gebruiker is echter verantwoordelijk voor het beheer van de rechten en levenscyclus van een **door de gebruiker toegewezen beheerde** identiteit.

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>Blueprint-service en door het systeem toegewezen beheerde identiteitsrechten worden ingetrokken

Zodra de implementaties zijn voltooid, trekt Blueprints de rechten van de **door het systeem toegewezen beheerde** identiteit uit het abonnement in. Vervolgens trekt de Blueprints-service zijn rechten van het abonnement in. Verwijdering van rechten voorkomt dat Blueprints een vaste eigenaar wordt van een abonnement.

## <a name="next-steps"></a>Volgende stappen

- Begrijpen hoe [statische en dynamische parameters](parameters.md)te gebruiken.
- Leer de volgorde van de [blauwdrukvolgorde](sequencing-order.md)aan te passen.
- Ontdek hoe u gebruik maken van het vergrendelen van [blauwdrukbronnen.](resource-locking.md)
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../how-to/update-existing-assignments.md).
- Los problemen op tijdens de toewijzing van een blauwdruk met [algemene probleemoplossing.](../troubleshoot/general.md)
