---
title: Artefacten toevoegen aan integratie service omgevingen (ISEs) in Azure Logic Apps
description: Voeg Logic apps, aangepaste connectors en integratie accounts toe aan uw ISE (Integration service Environment) om toegang te krijgen tot Azure Virtual Networks (VNETs), terwijl u persoonlijke en geïsoleerd van open bare of ' wereld wijd ' Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 6327f0c14b46ceaadbf7adaa58a70c32b39b7c2a
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71960491"
---
# <a name="add-artifacts-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Artefacten toevoegen aan uw integratie service omgeving (ISE) in Azure Logic Apps

Nadat u een [ISE (Integration service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)hebt gemaakt, voegt u artefacten toe, zoals Logic apps, integratie accounts en connectors, zodat ze toegang hebben tot de resources in uw virtuele Azure-netwerk.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* De ISE die u hebt gemaakt om uw Logic apps uit te voeren. Als u nog geen ISE hebt, [maakt u eerst een ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>Logische apps maken

Voer de volgende stappen uit om Logic apps te bouwen die worden uitgevoerd in uw integratie service omgeving (ISE):

1. Zoek en open uw ISE als deze nog niet is geopend. Selecteer in het menu ISE onder **instellingen**de optie **Logic apps** > **toevoegen**.

   ![Nieuwe logische app toevoegen aan ISE](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

   -of-

   Selecteer in het hoofd menu van Azure **een resource maken** > -**integratie** > **Logic-app**.

1. Geef de naam, het Azure-abonnement en de Azure-resource groep (nieuw of bestaand) op die u wilt gebruiken voor uw logische app.

1. Selecteer in de lijst **locatie** , onder de sectie **integratie service omgevingen** , uw ISE, bijvoorbeeld:

   ![Integratie service omgeving selecteren](./media/add-artifacts-integration-service-environment-ise/create-logic-app-with-integration-service-environment.png)

   > [!IMPORTANT]
   > Als u uw Logic Apps wilt gebruiken met een integratie account, moeten deze Logic apps en het integratie account dezelfde ISE gebruiken.

1. Blijf [op de gebruikelijke manier door gaan met het maken van uw logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   Zie [geïsoleerd versus Global in het overzicht van ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)voor meer informatie over hoe triggers en acties werken en hoe ze worden gelabeld wanneer u een ISE gebruikt vergeleken met de service Global Logic apps.

1. Zie [uw integratie service omgeving beheren](../logic-apps/ise-manage-integration-service-environment.md)als u logische apps en API-verbindingen in uw ISE wilt beheren.

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>Integratie accounts maken

Op basis van de [ISE-SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) die u hebt geselecteerd bij het maken, bevat uw ISE geen extra kosten voor het gebruik van een bepaald integratie account. Logic apps die zich in een Integration service Environment (ISE) bevinden, kunnen verwijzen naar alleen integratie accounts die zich in dezelfde ISE bevinden. Voor een integratie account om te werken met Logic apps in een ISE, moeten zowel het integratie account als de logische apps *dezelfde omgeving* gebruiken als hun locatie. Zie [integratie accounts met ISE](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment)voor meer informatie over integratie accounts en ISEs.

Voer de volgende stappen uit om een integratie account te maken dat gebruikmaakt van een ISE:

1. Zoek en open uw ISE als deze nog niet is geopend. Selecteer in het menu ISE onder **instellingen**de optie **integratie accounts** > **toevoegen**.

   ![Nieuw integratie account toevoegen aan ISE](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

   -of-

   Selecteer in het hoofd menu van Azure **een resource maken** > **integratie** > **integratie account**.

1. Geef de naam, het Azure-abonnement, de Azure-resource groep (nieuw of bestaand) en de prijs categorie op die u voor uw integratie account wilt gebruiken.

1. Selecteer in de lijst **locatie** , onder de sectie **integratie service omgevingen** , dezelfde ISE die uw Logic apps gebruiken, bijvoorbeeld:

   ![Integratie service omgeving selecteren](./media/add-artifacts-integration-service-environment-ise/create-integration-account-with-integration-service-environment.png)

1. [Koppel uw logische app op de gebruikelijke manier aan uw integratie account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

1. Ga door met het toevoegen van artefacten aan uw integratie account, zoals [handels partners](../logic-apps/logic-apps-enterprise-integration-partners.md) en [overeenkomsten](../logic-apps/logic-apps-enterprise-integration-agreements.md).

1. Zie [uw integratie service omgeving beheren](../logic-apps/ise-manage-integration-service-environment.md)voor informatie over het beheren van integratie accounts in uw ISE.

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>ISE-connectors toevoegen

U kunt door micro soft beheerde connectors toevoegen die beschikbaar zijn voor gebruik in uw ISE, maar die niet zijn geïmplementeerd in uw ISE.

1. Selecteer in het menu ISE, onder **instellingen**, **beheerde connectors**. Selecteer **toevoegen**op de werk balk.

   ![Beheerde connectors weer geven](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. Open in het deel venster **een nieuwe beheerde connector toevoegen** de lijst **connector zoeken** . Als de gewenste connector beschikbaar is, selecteert u die connector en selecteert u **maken**.

   De lijst bevat alleen de connectors die in aanmerking komen, maar die niet zijn geïmplementeerd in uw ISE. Connectors die al in uw ISE zijn geïmplementeerd, kunnen niet worden geselecteerd.

   ![In aanmerking komende connector selecteren](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>Aangepaste connectors maken

Als u aangepaste connectors in uw ISE wilt gebruiken, maakt u die aangepaste connectors rechtstreeks in uw ISE.

1. Zoek en open uw ISE als deze nog niet is geopend. Selecteer in het menu ISE onder **instellingen**de optie **aangepaste connectors** > **toevoegen**.

   ![Een aangepaste connector maken](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. Geef de naam, het Azure-abonnement en de Azure-resource groep (nieuw of bestaand) op die u voor uw aangepaste connector wilt gebruiken.

1. Selecteer in de lijst **locatie** , onder de sectie **integratie service omgevingen** , dezelfde ISE die uw Logic apps gebruiken en selecteer **maken**, bijvoorbeeld:

   ![Integratie service omgeving selecteren](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-with-integration-service-environment.png)

1. Selecteer de nieuwe aangepaste connector en selecteer vervolgens **bewerken**, bijvoorbeeld:

   ![Aangepaste connector selecteren en bewerken](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. Ga door met het maken van de connector op de gebruikelijke manier vanuit een [OpenAPI-definitie](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) of [SOAP](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector).

1. Zie [uw integratie service omgeving beheren](../logic-apps/ise-manage-integration-service-environment.md)voor het beheren van aangepaste connectors in uw ISE.

## <a name="next-steps"></a>Volgende stappen

* [Integratie service omgevingen beheren](../logic-apps/ise-manage-integration-service-environment.md)
