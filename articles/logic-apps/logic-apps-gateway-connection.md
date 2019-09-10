---
title: Toegang tot gegevens bronnen on-premises vanaf Azure Logic Apps
description: Verbinding maken met on-premises gegevens bronnen vanuit Logic apps met behulp van een on-premises gegevens gateway
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: 65c1d427939dc39aebece24b923bc4ebfbf136bb
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861009"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Verbinding maken met on-premises gegevens bronnen vanuit Azure Logic Apps

Om toegang te krijgen tot gegevens bronnen on-premises vanuit uw Logic apps, maakt u een on-premises gegevens gateway resource in de Azure Portal. Uw Logic apps kunnen vervolgens gebruikmaken [van de on-premises connectors](../logic-apps/logic-apps-gateway-install.md#supported-connections). In dit artikel wordt beschreven hoe u uw Azure gateway-resource maakt *nadat* u [de gateway op uw lokale computer hebt gedownload en geïnstalleerd](../logic-apps/logic-apps-gateway-install.md). Zie [hoe de gateway](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service)werkt voor meer informatie over de werking van de gateway.

> [!TIP]
> Als u verbinding wilt maken met virtuele netwerken van Azure, kunt u in plaats daarvan een [*integratie service omgeving*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) maken. 

Zie de volgende artikelen voor meer informatie over het gebruik van de gateway met andere services:

* [On-premises gegevens gateway van micro soft Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft Flow on-premises gegevens gateway](https://flow.microsoft.com/documentation/gateway-manage/)
* [Microsoft PowerApps on-premises gegevens gateway](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Azure Analysis Services on-premises gegevens gateway](../analysis-services/analysis-services-gateway.md)

## <a name="prerequisites"></a>Vereisten

* U hebt [de on-premises gegevens gateway al geïnstalleerd op een lokale computer](../logic-apps/logic-apps-gateway-install.md).

* U hebt [hetzelfde Azure-account en Azure-abonnement](../logic-apps/logic-apps-gateway-install.md#requirements) dat u hebt gebruikt toen u de on-premises gegevens gateway installeerde.

* U hebt uw gateway-installatie nog niet eerder aan een andere gateway bron in azure gekoppeld.

  Wanneer u een gateway bron maakt, selecteert u een gateway-installatie om aan de gateway bron te koppelen. Een gateway-installatie die al is gekoppeld, is niet beschikbaar om te selecteren bij het maken van Gateway-resources.

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Azure gateway-resource maken

Nadat u de gateway op een lokale computer hebt geïnstalleerd, maakt u de Azure-resource voor uw gateway. 

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met hetzelfde Azure-account dat is gebruikt om de gateway te installeren.

1. Voer in het zoekvak Azure Portal "on-premises gegevens gateway" in en selecteer **on-premises gegevens gateways**.

   ![On-premises gegevens gateway zoeken](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

1. Selecteer **toevoegen**onder **on-premises gegevens gateways**.

   ![Gegevens gateway toevoegen](./media/logic-apps-gateway-connection/add-gateway.png)

1. Geef onder **verbindings gateway maken**deze informatie voor uw gateway bron op. Als u gereed bent, selecteert u **Maken**.

   | Eigenschap | Description |
   |----------|-------------|
   | **Resourcenaam** | De resource naam van de gateway, die alleen letters, cijfers, afbreek streepjes`-`(), onderstrepings tekens`_`(), haakjes`(`( `)`,) en punten (`.`) kan bevatten. |
   | **Abonnement** | Uw Azure-abonnement, dat gelijk moet zijn aan de installatie van de gateway en de logische app. Het standaard abonnement is gebaseerd op het Azure-account dat u hebt gebruikt om u aan te melden. |
   | **Resourcegroep** | De [Azure-resource groep](../azure-resource-manager/resource-group-overview.md) die u wilt gebruiken |
   | **Location** | Dezelfde regio als de locatie die is geselecteerd voor de gateway-Cloud service tijdens de installatie van de [Gateway](../logic-apps/logic-apps-gateway-install.md). Anders wordt de installatie van de gateway niet weer gegeven in de lijst met **installatie namen** die u kunt selecteren. De locatie van de logische app kan verschillen van de resource locatie van uw gateway. |
   | **Installatie naam** | Als de installatie van de gateway nog niet is geselecteerd, selecteert u de gateway die u eerder hebt geïnstalleerd. Eerder gekoppelde gateway-installaties worden niet weer gegeven in deze lijst om te selecteren. |
   |||

   Hier volgt een voorbeeld:

   ![Geef details op voor het maken van uw on-premises gegevens gateway](./media/logic-apps-gateway-connection/gateway-details.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Verbinding maken met on-premises gegevens

Nadat u de gateway resource hebt gemaakt en uw Azure-abonnement aan deze resource hebt gekoppeld, kunt u nu een verbinding maken tussen uw logische app en uw on-premises gegevens bron met behulp van de gateway.

1. In de Azure Portal maakt of opent u de logische app in de ontwerp functie voor logische apps.

1. Voeg een connector toe die on-premises verbindingen ondersteunt, bijvoorbeeld **SQL Server**.

1. Selecteer **verbinding via on-premises gegevens gateway**. 

1. Selecteer voor **gateways**de gateway resource die u hebt gemaakt.

   > [!NOTE]
   > De lijst met gateways bevat gateway bronnen in andere regio's omdat de locatie van de logische app kan verschillen van de locatie van de gateway bron.

1. Geef een unieke verbindings naam en andere vereiste informatie op die afhankelijk is van de verbinding die u wilt maken.

   Met een unieke verbindings naam kunt u die verbinding later gemakkelijk vinden, met name als u meerdere verbindingen maakt. Indien van toepassing moet u ook het gekwalificeerde domein voor uw gebruikers naam toevoegen.
   
   Hier volgt een voorbeeld:

   ![Verbinding maken tussen logische app en gegevens gateway](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. Als u gereed bent, selecteert u **Maken**. 

Uw gateway verbinding is nu klaar voor gebruik door uw logische app.

## <a name="edit-connection"></a>Verbinding bewerken

Als u de instellingen voor een gateway verbinding wilt bijwerken, kunt u de verbinding bewerken.

1. Als u alle API-verbindingen voor uw logische app wilt vinden, selecteert u in het menu van de logische app onder **ontwikkelingsprogram ma's**de optie **API-verbindingen**.
   
   ![Selecteer API-verbindingen in het menu van de logische app.](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

1. Selecteer de gewenste gateway verbinding en selecteer vervolgens API- **verbinding bewerken**.

   > [!TIP]
   > Als uw updates niet van kracht worden, kunt u proberen om het gateway Windows-Service account voor de gateway-installatie te [stoppen en opnieuw te starten](../logic-apps/logic-apps-gateway-install.md#restart-gateway) .

Om te zoeken naar alle API-verbindingen die zijn gekoppeld aan uw Azure-abonnement: 

* Ga in het hoofd menu van Azure naar **alle services** > **Web** > **API-verbindingen**.
* Of Ga vanuit het hoofd menu van Azure naar **alle resources**. Stel het **type** filter in op **API-verbinding**.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Gateway resource verwijderen

Als u een andere gateway bron wilt maken, koppelt u de gateway-installatie aan een andere gateway bron of verwijdert u de gateway bron, kunt u de gateway bron verwijderen zonder dat dit van invloed is op de installatie van de gateway. 

1. Selecteer **alle resources**in het hoofd menu van Azure. Zoek en selecteer de bron van de gateway.

1. Als dit nog niet is geselecteerd, selecteert u in het menu van de gateway resource de optie **on-premises gegevens gateway**. Selecteer **verwijderen**op de werk balk van de gateway resource.

   Bijvoorbeeld:

   ![Gateway verwijderen](./media/logic-apps-gateway-connection/gateway-delete.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Volgende stappen

* [Uw logische apps beveiligen](./logic-apps-securing-a-logic-app.md)
* [Algemene voor beelden en scenario's voor Logic apps](./logic-apps-examples-and-scenarios.md)
