---
title: Resources toevoegen aan integratieserviceomgevingen
description: Logische apps, integratieaccounts, aangepaste connectors en beheerde connectors toevoegen aan uw integratieserviceomgeving (ISE)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 58d2efd0c61045739930ce36ba317b1aa6a40ce8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79164876"
---
# <a name="add-resources-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Resources toevoegen aan uw integratieserviceomgeving (ISE) in Azure Logic Apps

Nadat u een [integratieserviceomgeving (ISE) hebt gemaakt,](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)voegt u bronnen toe zoals logische apps, integratieaccounts en connectoren, zodat ze toegang hebben tot de bronnen in uw virtuele Azure-netwerk. Beheerde ISE-connectors die beschikbaar komen nadat u uw ISE hebt gemaakt, worden bijvoorbeeld niet automatisch weergegeven in de Logic App Designer. Voordat u deze ISE-connectors gebruiken, moet u deze connectors handmatig [toevoegen en implementeren aan uw ISE,](#add-ise-connectors-environment) zodat ze worden weergegeven in de Logic App Designer.

> [!IMPORTANT]
> Als logische apps en integratieaccounts in een ISE samenwerken, moeten beide *dezelfde ISE* gebruiken als hun locatie.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* De ISE die u hebt gemaakt om uw logische apps uit te voeren. Als u geen ISE hebt, [maakt u eerst een ISE.](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

* Als u resources wilt maken, toevoegen of bijwerken die zijn geïmplementeerd op een ISE, moet u de rol Eigenaar of inzender op die ISE toegewezen krijgen of moet er machtigingen worden overgenomen via het Azure-abonnement of de Azure-brongroep die is gekoppeld aan de ISE. Voor mensen die geen eigenaar, bijdrager of overgenomen machtigingen hebben, kunnen ze de rol Integratieserviceomgeving bijdrager of integratieserviceomgeving-functie toegewezen krijgen. Zie [RBAC (RBAC) voor](../role-based-access-control/overview.md)meer informatie over op rollen gebaseerdtoegangscontrole?

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>Logische apps maken

Voer de volgende stappen uit om logische apps te bouwen die worden uitgevoerd in uw integratieserviceomgeving (ISE):

1. Zoek en open uw ISE, zo niet al geopend. Selecteer in het menu ISE onder **Instellingen**de optie **Logische apps** > **Toevoegen**.

   ![Nieuwe logische app toevoegen aan ISE](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

1. Geef informatie over de logische app die u wilt maken, bijvoorbeeld:

   ![Integratieserviceomgeving selecteren](./media/add-artifacts-integration-service-environment-ise/create-logic-app-integration-service-environment.png)

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Naam** | Ja | De naam voor de logische app om te maken |
   | **Abonnement** | Ja | De naam voor het Azure-abonnement dat moet worden gebruikt |
   | **Resourcegroep** | Ja | De naam voor de Azure-brongroep (nieuw of bestaand) die moet worden gebruikt |
   | **Locatie** | Ja | Selecteer **onder Integratieserviceomgevingen**de ISE die u wilt gebruiken, als deze nog niet is geselecteerd. <p><p> **Belangrijk:** om uw logische apps met een integratieaccount te gebruiken, moeten beide dezelfde ISE gebruiken. |
   ||||

1. Als u gereed bent, selecteert u **Maken**.

1. Ga door [met het maken van uw logische app op de gebruikelijke manier.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

   Zie Isolated versus multi-tenant voor verschillen in de manier waarop triggers en acties werken en hoe ze worden gelabeld wanneer u een ISE gebruikt in vergelijking met de multi-tenant Logic Apps-service, zie [Geïsoleerd versus multi-tenant in het ISE-overzicht.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)

1. Zie [Uw integratieserviceomgeving](../logic-apps/ise-manage-integration-service-environment.md)beheren als u logische apps en API-verbindingen in uw ISE wilt beheren.

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>Integratieaccounts maken

Op basis van de [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) die is geselecteerd bij het maken, bevat uw ISE specifiek gebruik van integratie-account zonder extra kosten. Logische apps die in een integratieserviceomgeving (ISE) bestaan, kunnen alleen verwijzen naar integratieaccounts die in dezelfde ISE bestaan. Als een integratieaccount dus werkt met logische apps in een ISE, moeten zowel het integratieaccount als de logische apps *dezelfde omgeving* gebruiken als hun locatie. Zie [Integratieaccounts met ISE](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment)voor meer informatie over integratieaccounts en ISE's.

Voer de volgende stappen uit om een integratieaccount te maken dat een ISE gebruikt:

1. Zoek en open uw ISE, zo niet al geopend. Selecteer **integratieaccounts** > **Toevoegen**in het menu ISE onder **Instellingen**.

   ![Nieuw integratieaccount toevoegen aan ISE](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

1. Geef informatie over de logische app die u wilt maken, bijvoorbeeld:

   ![Integratieserviceomgeving selecteren](./media/add-artifacts-integration-service-environment-ise/create-integration-account-integration-service-environment.png)

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Naam** | Ja | De naam voor het integratieaccount dat u wilt maken |
   | **Abonnement** | Ja | De naam voor het Azure-abonnement dat u wilt gebruiken |
   | **Resourcegroep** | Ja | De naam voor de Azure-brongroep (nieuw of bestaand) die moet worden gebruikt |
   | **Prijslaag** | Ja | De prijscategorie die u moet gebruiken voor het integratieaccount |
   | **Locatie** | Ja | Selecteer **onder Integratieserviceomgevingen**dezelfde ISE die uw logische apps gebruiken, als deze nog niet zijn geselecteerd. <p><p> **Belangrijk:** om uw integratieaccount met logische apps te gebruiken, moeten beide dezelfde ISE gebruiken. |
   ||||

1. Als u gereed bent, selecteert u **Maken**.

1. [Koppel uw logica-app op de gebruikelijke manier aan uw integratieaccount.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)

1. Ga verder door resources toe te voegen aan uw integratieaccount, zoals [handelspartners](../logic-apps/logic-apps-enterprise-integration-partners.md) en [overeenkomsten.](../logic-apps/logic-apps-enterprise-integration-agreements.md)

1. Zie [Uw integratieserviceomgeving beheren](../logic-apps/ise-manage-integration-service-environment.md)als u integratieaccounts in uw ISE wilt beheren.

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>ISE-connectoren toevoegen

Door Microsoft beheerde connectors die beschikbaar komen nadat u uw ISE hebt gemaakt, worden niet automatisch weergegeven in de connectorkiezer in de Logic App Designer. Voordat u deze ISE-connectors gebruiken, moet u deze connectors handmatig toevoegen en implementeren aan uw ISE, zodat ze worden weergegeven in de Logic App Designer.

1. Selecteer **beheerde connectors**in het menu ISE onder **Instellingen**. Selecteer op de werkbalk **Toevoegen**.

   ![Beheerde connectors weergeven](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. Open de lijst **Connector zoeken** in het deelvenster Een **nieuwe beheerde connector toevoegen.** Selecteer de ISE-connector die u wilt gebruiken, maar nog niet is geïmplementeerd in uw ISE. Selecteer **Maken**.

   ![Selecteer de ISE-connector die u in uw ISE wilt implementeren](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

   Alleen ISE-connectors die in aanmerking komen, maar nog niet zijn geïmplementeerd op uw ISE, worden beschikbaar voor u om te selecteren. Connectors die al in uw ISE zijn geïmplementeerd, lijken niet beschikbaar voor selectie.

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>Aangepaste connectors maken

Als u aangepaste connectors in uw ISE wilt gebruiken, maakt u deze aangepaste connectors rechtstreeks binnen uw ISE.

1. Zoek en open uw ISE, zo niet al geopend. Selecteer in het menu ISE onder **Instellingen**de optie **Aangepaste connectors** > **Toevoegen**.

   ![Een aangepaste connector maken](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. Geef de naam, Azure-abonnement en Azure-brongroep (nieuw of bestaand) op voor uw aangepaste connector.

1. Selecteer in de lijst **Locatie** onder de sectie **Integratieserviceomgevingen** dezelfde ISE die uw logische apps gebruiken en selecteer **Maken,** bijvoorbeeld:

   ![Integratieserviceomgeving selecteren](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-integration-service-environment.png)

1. Selecteer uw nieuwe aangepaste connector en selecteer vervolgens **Bewerken,** bijvoorbeeld:

   ![Aangepaste connector selecteren en bewerken](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. Ga verder door de connector op de gebruikelijke manier te maken vanuit een [OpenAPI-definitie](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) of [SOAP.](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector)

1. Zie [Uw integratieserviceomgeving beheren](../logic-apps/ise-manage-integration-service-environment.md)als u aangepaste connectors in uw ISE wilt beheren.

## <a name="next-steps"></a>Volgende stappen

* [Integratieserviceomgevingen beheren](../logic-apps/ise-manage-integration-service-environment.md)
