---
title: Logische apps migreren tussen abonnementen, resourcegroepen of regio's
description: Logische apps of integratieaccounts migreren naar andere Azure-abonnementen, resourcegroepen of locaties (regio's)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 065bbc62d65d7e91728b10cd9f95b2e73ea03abc
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878728"
---
# <a name="move-logic-app-resources-to-other-azure-resource-groups-regions-or-subscriptions"></a>Logische app-bronnen verplaatsen naar andere Azure-brongroepen, -regio's of -abonnementen

Als u uw logische app of gerelateerde resources wilt migreren naar een andere Azure-brongroep, -regio of -abonnement, u deze taken uitvoeren, zoals de Azure-portal, Azure PowerShell, Azure CLI en REST API. Voordat u resources verplaatst, controleert u de volgende overwegingen: 

* U alleen [specifieke logische app-brontypen](../azure-resource-manager/management/move-support-resources.md#microsoftlogic) verplaatsen tussen Azure-brongroepen of -abonnementen.

* Controleer de [limieten](../logic-apps/logic-apps-limits-and-config.md) voor het aantal logische app-resources dat u hebben in uw Azure-abonnement en in elke Azure-regio. Deze limieten zijn van invloed op de vraag of u specifieke resourcetypen verplaatsen wanneer de regio hetzelfde blijft voor abonnementen of resourcegroepen. U bijvoorbeeld slechts één gratis laagintegratieaccount hebben voor elke Azure-regio in elk Azure-abonnement.

* Wanneer u resources verplaatst, maakt Azure nieuwe bron-id's. Zorg er dus voor dat u de nieuwe id's gebruikt en werk alle scripts of hulpprogramma's bij die zijn gekoppeld aan de verplaatste resources.

* Nadat u logische apps hebt gemigreerd tussen abonnementen, resourcegroepen of regio's, moet u alle verbindingen die Open Authentication (OAuth) vereisen opnieuw maken of opnieuw autoriseren.

* U een [ise -integratieserviceomgeving](connect-virtual-network-vnet-isolated-environment-overview.md) alleen verplaatsen naar een andere resourcegroep die in dezelfde Azure-regio of Azure-abonnement bestaat. U een ISE niet verplaatsen naar een resourcegroep die bestaat in een andere Azure-regio of Azure-abonnement. Ook moet u na een dergelijke verplaatsing alle verwijzingen naar de ISE bijwerken in uw logica-app-werkstromen, integratieaccounts, verbindingen, enzovoort.

## <a name="prerequisites"></a>Vereisten

* Hetzelfde Azure-abonnement dat is gebruikt om de logische app of het integratieaccount te maken dat u wilt verplaatsen

* Machtigingen voor resource-eigenaren om de gewenste resources te verplaatsen en in te stellen. Meer informatie over [op rollen gebaseerd toegangscontrole (RBAC)](../role-based-access-control/built-in-roles.md#owner).

<a name="move-subscription"></a>

## <a name="move-resources-between-subscriptions"></a>Resources verplaatsen van het ene naar het andere abonnement

Als u een bron, zoals een logische app of integratieaccount, wilt verplaatsen naar een ander Azure-abonnement, u de Azure-portal, Azure PowerShell, Azure CLI of REST API gebruiken. Deze stappen hebben betrekking op de Azure-portal, die u gebruiken wanneer de regio van de resource hetzelfde blijft. Zie Resources verplaatsen naar [een nieuwe resourcegroep of -abonnement voor](../azure-resource-manager/management/move-resource-group-and-subscription.md)andere stappen en algemene voorbereiding.

1. Zoek en selecteer in de [Azure-portal](https://portal.azure.com)de logische app-bron die u wilt verplaatsen.

1. Selecteer op de pagina **Overzicht** van de bron naast **Abonnement**de koppeling **Wijzigen.**

1. Selecteer op de pagina **Resources verplaatsen** de bron van de logische app en de bijbehorende bronnen die u wilt verplaatsen.

1. Selecteer **in** de lijst Abonnement het bestemmingsabonnement.

1. Selecteer in de lijst **Resourcegroep** de groep doelbron. Als u een andere resourcegroep wilt maken, selecteert u **Een nieuwe groep maken**.

1. Als u wilt bevestigen dat scripts of hulpprogramma's die zijn gekoppeld aan de verplaatste resources niet werken totdat u ze hebt bijgewerkt met de nieuwe bron-id's, selecteert u het bevestigingsvak en selecteert u **OK**.

<a name="move-resource-group"></a>

## <a name="move-resources-between-resource-groups"></a>Resources verplaatsen tussen resourcegroepen

Als u een bron, zoals een logic-app, integratieaccount of [integratieserviceomgeving (ISE),](connect-virtual-network-vnet-isolated-environment-overview.md)wilt verplaatsen naar een andere Azure-brongroep, u de Azure-portal, Azure PowerShell, Azure CLI of REST API gebruiken. Deze stappen hebben betrekking op de Azure-portal, die u gebruiken wanneer de regio van de resource hetzelfde blijft. Zie Resources verplaatsen naar [een nieuwe resourcegroep of -abonnement voor](../azure-resource-manager/management/move-resource-group-and-subscription.md)andere stappen en algemene voorbereiding.

Voordat u resources daadwerkelijk tussen groepen verplaatst, u testen of u uw resource naar een andere groep verplaatsen. Zie [Uw verhuizing valideren](../azure-resource-manager/management/move-resource-group-and-subscription.md#validate-move)voor meer informatie .

1. Zoek en selecteer in de [Azure-portal](https://portal.azure.com)de logische app-bron die u wilt verplaatsen.

1. Selecteer op de pagina **Overzicht** van de resource naast **de groep Resource**de koppeling **Wijzigen.**

1. Selecteer op de pagina **Resources verplaatsen** de bron van de logische app en de bijbehorende bronnen die u wilt verplaatsen.

1. Selecteer in de lijst **Resourcegroep** de groep doelbron. Als u een andere resourcegroep wilt maken, selecteert u **Een nieuwe groep maken**.

1. Als u wilt bevestigen dat scripts of hulpprogramma's die zijn gekoppeld aan de verplaatste resources niet werken totdat u ze hebt bijgewerkt met de nieuwe bron-id's, selecteert u het bevestigingsvak en selecteert u **OK**.

<a name="move-location"></a>

## <a name="move-resources-between-regions"></a>Resources verplaatsen tussen regio's

Wanneer u een logische app naar een andere regio wilt verplaatsen, zijn uw opties afhankelijk van de manier waarop u uw logische app hebt gemaakt. Op basis van de optie die u kiest, moet u de verbindingen in uw logische app opnieuw maken of opnieuw autoriseren.

* Maak in de Azure-portal de logische app opnieuw in het nieuwe gebied en configureer de werkstroominstellingen opnieuw. Om tijd te besparen, u de onderliggende werkstroomdefinitie en -verbindingen kopiëren van de bron-app naar de doel-app. Als u de 'code' achter een logische app wilt weergeven, selecteert u op de werkbalk Logic App Designer de **codeweergave**.

* Door Visual Studio en de Azure Logic Apps Tools for Visual Studio te gebruiken, u [uw logische app openen en downloaden](../logic-apps/manage-logic-apps-with-visual-studio.md) vanuit de Azure-portal als azure resource [manager-sjabloon.](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) Deze sjabloon is grotendeels klaar voor implementatie en bevat de brondefinities voor uw logische app, inclusief de werkstroom zelf en verbindingen. De sjabloon declareert ook parameters voor de waarden die moeten worden gebruikt bij implementatie. Op die manier u gemakkelijker wijzigen waar en hoe u de logische app implementeert, op basis van uw behoeften. Als u de locatie en andere benodigde informatie voor implementatie wilt opgeven, u een afzonderlijk parametersbestand gebruiken.

* Als u uw logische app hebt gemaakt en geïmplementeerd met behulp van hulpprogramma's voor continue integratie (CI) en cd's voor continue levering, zoals Azure Pipelines in Azure DevOps, u uw app met behulp van deze hulpprogramma's naar een andere regio implementeren.

Zie de volgende onderwerpen voor meer informatie over implementatiesjablonen voor logische apps:

* [Overzicht: Implementatie voor Azure Logic Apps automatiseren met Azure Resource Manager-sjablonen](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Uw logische app zoeken, openen en downloaden vanuit de Azure-portal naar Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Azure Resource Manager-sjablonen maken voor Azure Logic Apps](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Azure Resource Manager-sjablonen implementeren voor Azure Logic Apps](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)

### <a name="related-resources"></a>Gerelateerde resources

Sommige Azure-bronnen, zoals on-premises gegevensgatewaybronnen in Azure, kunnen bestaan in een regio die verschilt van de logische apps die deze bronnen gebruiken. Andere Azure-bronnen, zoals gekoppelde integratieaccounts, moeten echter in dezelfde regio bestaan als uw logische apps. Zorg er op basis van uw scenario voor dat uw logische apps toegang hebben tot de bronnen waarvan uw apps verwachten dat ze in dezelfde regio bestaan.

Als u bijvoorbeeld een logische app wilt koppelen aan een integratieaccount, moeten beide bronnen in dezelfde regio bestaan. In scenario's zoals disaster recovery wilt u meestal integratieaccounts met dezelfde configuratie en artefacten. In andere scenario's hebt u mogelijk integratieaccounts nodig met verschillende configuraties en artefacten.

Aangepaste connectors in Azure Logic Apps zijn zichtbaar voor de auteurs en gebruikers van de connectors die hetzelfde Azure-abonnement en dezelfde Azure Active Directory-tenant hebben. Deze connectors zijn beschikbaar in hetzelfde gebied waar logische apps worden geïmplementeerd. Zie voor meer informatie [Share custom connectors in your organization](https://docs.microsoft.com/connectors/custom-connectors/share) (Aangepaste connectors delen in uw organisatie).

De sjabloon die u van Visual Studio krijgt, bevat alleen de brondefinities voor uw logische app en de bijbehorende verbindingen. Als uw logica-app dus andere bronnen gebruikt, bijvoorbeeld een integratieaccount en B2B-artefacten, zoals partners, overeenkomsten en schema's, moet u de sjabloon van dat integratieaccount exporteren met behulp van de Azure-portal. Deze sjabloon bevat de brondefinities voor zowel het integratieaccount als de artefacten. De sjabloon is echter niet volledig geparameteriseerd. U moet dus handmatig de waarden parameteriseren die u wilt gebruiken voor implementatie.

### <a name="export-templates-for-integration-accounts"></a>Sjablonen exporteren voor integratieaccounts

1. Zoek en open uw integratieaccount in de [Azure-portal.](https://portal.azure.com)

1. Selecteer in het menu van uw integratieaccount onder **Instellingen**de optie **Sjabloon Exporteren**.

1. Selecteer op de werkbalk **Downloaden**en sla de sjabloon op.

1. Open en bewerk de sjabloon om de benodigde waarden voor implementatie te parameteriseren.

## <a name="next-steps"></a>Volgende stappen

[Azure-bronnen verplaatsen naar nieuwe brongroepen of -abonnementen](../azure-resource-manager/management/move-resource-group-and-subscription.md)
