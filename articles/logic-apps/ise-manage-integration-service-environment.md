---
title: Integratie service omgevingen in Azure Logic Apps beheren
description: Controleer de netwerk status en beheer logische apps, verbindingen, aangepaste connectors en integratie accounts in uw integratie service omgeving (ISE) voor Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 1d91813e0f39207bcf7768de89600a6bdee0fc53
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792617"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Uw ISE (Integration service Environment) beheren in Azure Logic Apps

Volg de stappen in dit onderwerp om de netwerk status van uw [ISE (Integration service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) te controleren en de logische apps, verbindingen, integratie accounts en connectors te beheren die aanwezig zijn in uw ISE. Zie [artefacten toevoegen aan uw integratie service omgeving](../logic-apps/add-artifacts-integration-service-environment-ise.md)om deze artefacten aan uw ISE toe te voegen.

## <a name="view-your-ise"></a>Uw ISE weer geven

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Voer in het zoekvak van de portal "integratie service omgevingen" in en selecteer vervolgens **integratie service omgevingen**.

   ![Integratie service omgevingen zoeken](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. Selecteer uw integratie service omgeving in de lijst met resultaten.

   ![Integratie service omgeving selecteren](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

1. Ga door naar de volgende secties om logische apps, verbindingen, connectors of integratie accounts in uw ISE te vinden.

<a name="check-network-health"></a>

## <a name="check-network-health"></a>De netwerk status controleren

Selecteer in het menu ISE onder **instellingen**de optie **netwerk status**. Dit deel venster toont de integriteits status voor uw subnetten en uitgaande afhankelijkheden op andere services.

![De netwerk status controleren](./media/ise-manage-integration-service-environment/ise-check-network-health.png)

<a name="find-logic-apps"></a>

## <a name="manage-your-logic-apps"></a>Uw Logic apps beheren

U kunt de Logic apps in uw ISE weer geven en beheren.

1. Selecteer in het menu ISE onder **instellingen**de optie **Logic apps**.

   ![Logic apps weer geven](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. Als u logische Apps wilt verwijderen die u niet meer nodig hebt in uw ISE, selecteert u deze Logic apps en selecteert u vervolgens **verwijderen**. Selecteer **Ja**om te bevestigen dat u wilt verwijderen.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>API-verbindingen beheren

U kunt de verbindingen weer geven en beheren die zijn gemaakt door de Logic apps die worden uitgevoerd in uw ISE.

1. Selecteer in het menu ISE onder **instellingen**de optie **API-verbindingen**.

   ![API-verbindingen weer geven](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. Als u verbindingen wilt verwijderen die u niet meer nodig hebt in uw ISE, selecteert u deze verbindingen en selecteert u vervolgens **verwijderen**. Selecteer **Ja**om te bevestigen dat u wilt verwijderen.

<a name="manage-api-connectors"></a>

## <a name="manage-ise-connectors"></a>ISE-connectors beheren

U kunt de API-connectors weer geven en beheren die zijn geïmplementeerd in uw ISE.

1. Selecteer in het menu ISE, onder **instellingen**, **beheerde connectors**.

   ![Beheerde connectors weer geven](./media/ise-manage-integration-service-environment/ise-view-managed-connectors.png)

1. Als u connectors wilt verwijderen die u niet beschikbaar wilt maken in uw ISE, selecteert u deze connectors en selecteert u vervolgens **verwijderen**. Selecteer **Ja**om te bevestigen dat u wilt verwijderen.

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>Aangepaste connectors beheren

U kunt de aangepaste connectors weer geven en beheren die u hebt geïmplementeerd voor uw ISE.

1. Selecteer in het menu ISE onder **instellingen**de optie **aangepaste connectors**.

   ![Aangepaste connectoren vinden](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. Als u aangepaste connectors die u niet meer nodig hebt in uw ISE wilt verwijderen, selecteert u deze connectors en selecteert u vervolgens **verwijderen**. Selecteer **Ja**om te bevestigen dat u wilt verwijderen.

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>Integratieaccounts beheren

1. Selecteer in uw ISE-menu onder **instellingen**de optie **integratie accounts**.

   ![Integratie accounts zoeken](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. Als u integratie accounts uit uw ISE wilt verwijderen wanneer u deze niet meer nodig hebt, selecteert u deze integratie accounts en selecteert u vervolgens **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [maken van verbinding met virtuele netwerken van Azure vanuit geïsoleerde Logic apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
