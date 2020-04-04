---
title: Toegang tot gegevensbronnen op locatie
description: Verbinding maken met on-premises gegevensbronnen van Azure Logic Apps door een azure on-premises gegevensgatewaybron te maken
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 02/14/2020
ms.openlocfilehash: 096943ff796f9c12c7f8715cadce5c3085965d4d
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657139"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Verbinding maken met on-premises gegevensbronnen vanuit Azure Logic Apps

Voordat u via uw logische apps toegang hebt tot gegevensbronnen op locatie, moet u een Azure-bron maken nadat u [de *on-premises gegevensgateway* op een lokale computer hebt geïnstalleerd.](../logic-apps/logic-apps-gateway-install.md) Uw logische apps gebruiken deze Azure-gatewaybron vervolgens in de triggers en acties die worden geleverd door de [on-premises connectors](../connectors/apis-list.md#on-premises-connectors) die beschikbaar zijn voor Azure Logic Apps.

In dit artikel ziet u hoe u uw Azure-gatewaybron maakt voor een eerder [geïnstalleerde gateway op uw lokale computer.](../logic-apps/logic-apps-gateway-install.md) Zie Hoe de gateway [werkt](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service)voor meer informatie over de gateway.

> [!TIP]
> Als u verbinding wilt maken met virtuele Azure-netwerken, u in plaats daarvan overwegen een [*integratieserviceomgeving te*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) maken. 

Zie de volgende artikelen voor informatie over het gebruik van de gateway met andere services:

* [Microsoft Power Automatiseer on-premises datagateway](/power-automate/gateway-reference)
* [Microsoft Power BI on-premises datagateway](/power-bi/service-gateway-onprem)
* [On-premises microsoft Power Apps-gateway](/powerapps/maker/canvas-apps/gateway-reference)
* [On-premises azure Analysis Services-gateway](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Ondersteunde gegevensbronnen

In Azure Logic Apps ondersteunt de on-premises datagateway de [on-premises connectors](../connectors/apis-list.md#on-premises-connectors) voor deze gegevensbronnen:

* BizTalk Server 2016
* Bestandssysteem
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle Database
* PostgreSQL
* SAP
* SharePoint Server
* SQL Server
* Teradata

Azure Logic Apps ondersteunt lees- en schrijfbewerkingen via de gegevensgateway. Deze bewerkingen hebben echter [beperkingen op hun laadvermogengrootte](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations). Hoewel de gateway zelf geen extra kosten met zich meebrengt, is het [prijsmodel van Logic Apps](../logic-apps/logic-apps-pricing.md) van toepassing op deze connectors en andere bewerkingen in Azure Logic Apps.

## <a name="prerequisites"></a>Vereisten

* U [hebt de on-premises gegevensgateway](../logic-apps/logic-apps-gateway-install.md)al op een lokale computer geïnstalleerd.

* U gebruikt [hetzelfde Azure-account en -abonnement](../logic-apps/logic-apps-gateway-install.md#requirements) dat is gebruikt bij het installeren van die gegevensgateway. Dit Azure-account moet deel uitmaken van één [Azure Active Directory -tenant of -map](../active-directory/fundamentals/active-directory-whatis.md#terminology).

* Uw gateway-installatie is nog niet geregistreerd en geclaimd door een andere Azure-gatewaybron.

  Wanneer u een gatewaybron maakt in de Azure-portal, selecteert u een gateway-installatie, die wordt gekoppeld aan uw gatewaybron en alleen die gatewaybron. In Azure Logic Apps gebruiken on-premises triggers en acties vervolgens de gatewaybron om verbinding te maken met on-premises gegevensbronnen. In deze triggers en acties selecteert u uw Azure-abonnement en de bijbehorende gatewaybron die u wilt gebruiken. Elke gatewaybron wordt gekoppeld aan slechts één gateway-installatie, die wordt gekoppeld aan slechts één Azure-account.

  > [!NOTE]
  > Alleen de gatewaybeheerder kan de gatewaybron maken in de Azure-portal. Momenteel worden serviceprincipals niet ondersteund. 

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Azure-gatewaybron maken

Nadat u de gateway op een lokale computer hebt geïnstalleerd, maakt u de Azure-bron voor uw gateway.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met hetzelfde Azure-account dat is gebruikt om de gateway te installeren.

1. Typ in het zoekvak Azure-portal 'on-premises gegevensgateway' en selecteer **On-premises gegevensgateways**.

   ![Zoek 'On-premises datagateway'](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. Selecteer Onder **On-premises gegevensgateways**de optie **Toevoegen**.

   ![Nieuwe Azure-bron toevoegen voor gegevensgateway](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. Geef deze informatie onder **Verbindingsgateway maken**deze informatie op voor uw gatewaybron. Als u gereed bent, selecteert u **Maken**.

   | Eigenschap | Beschrijving |
   |----------|-------------|
   | **Resourcenaam** | Geef een naam op voor uw gatewaybron die alleen`-`letters,`_`cijfers, koppeltekens`(` `)`( ),`.`onderstreept ( ), haakjes ( , of perioden ( ). |
   | **Abonnement** | Selecteer het Azure-abonnement voor het Azure-account dat is gebruikt voor de gateway-installatie. Het standaardabonnement is gebaseerd op het Azure-account dat u hebt gebruikt om u aan te melden. |
   | **Resourcegroep** | De [Azure-brongroep](../azure-resource-manager/management/overview.md) die u wilt gebruiken |
   | **Locatie** | Dezelfde regio of locatie die is geselecteerd voor de gatewaycloudservice tijdens [de installatie van de gateway.](../logic-apps/logic-apps-gateway-install.md) Anders wordt uw gateway-installatie niet weergegeven in de lijst **Installatienaam.** De locatie van uw logische app kan afwijken van de locatie van uw gatewaybron. |
   | **Installatienaam** | Selecteer een gateway-installatie die alleen in de lijst wordt weergegeven wanneer aan deze voorwaarden is voldaan: <p><p>- De gateway-installatie maakt gebruik van hetzelfde gebied als de gatewaybron die u wilt maken. <br>- De gateway-installatie is niet gekoppeld aan een andere Azure-gatewaybron. <br>- De gateway-installatie is gekoppeld aan hetzelfde Azure-account dat u gebruikt om de gatewaybron te maken. <br>- Uw Azure-account behoort tot één [Azure Active Directory -tenant of -map (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md#terminology) en is hetzelfde account dat is gebruikt voor de gateway-installatie. <p><p>Zie de sectie [Veelgestelde vragen](#faq) voor meer informatie. |
   |||

   Hier is een voorbeeld dat een gateway-installatie toont die zich in dezelfde regio bevindt als uw gatewaybron en is gekoppeld aan hetzelfde Azure-account:

   ![Details opgeven om gegevensgatewaybron te maken](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Verbinding maken met on-premises gegevens

Nadat u uw gatewaybron hebt gemaakt en uw Azure-abonnement aan deze bron hebt gekoppeld, u nu een verbinding maken tussen uw logische app en uw on-premises gegevensbron met behulp van de gateway.

1. Maak of open uw logische app in de Logische App Designer in de Azure-portal.

1. Voeg een connector toe die on-premises verbindingen ondersteunt, bijvoorbeeld **SQL Server.**

1. Selecteer **Verbinding maken via on-premises datagateway.**

1. Selecteer **onder Gateways**in de lijst **Abonnementen** uw Azure-abonnement met de gewenste gatewaybron.

1. Selecteer in de lijst **Verbindingsgateway,** waarin de beschikbare gatewaybronnen in uw geselecteerde abonnement worden weergegeven, de gewenste gatewaybron. Elke gatewaybron is gekoppeld aan één gateway-installatie.

   > [!NOTE]
   > De lijst met gateways bevat gatewaybronnen in andere regio's omdat de locatie van uw logische app kan verschillen van de locatie van uw gatewaybron. 

1. Geef een unieke verbindingsnaam en andere vereiste informatie op, die afhankelijk is van de verbinding die u wilt maken.

   Met een unieke verbindingsnaam u die verbinding later gemakkelijk vinden, vooral als u meerdere verbindingen maakt. Voeg indien van toepassing ook het gekwalificeerde domein voor uw gebruikersnaam toe.

   Hier volgt een voorbeeld:

   ![Verbinding maken tussen logische app en gegevensgateway](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. Als u gereed bent, selecteert u **Maken**.

Uw gatewayverbinding is nu klaar voor het gebruik van uw logische app.

## <a name="edit-connection"></a>Verbinding bewerken

Als u de instellingen voor een gatewayverbinding wilt bijwerken, u uw verbinding bewerken.

1. Als u alle API-verbindingen voor alleen uw logische app wilt vinden, selecteert u in het menu van uw logische app onder **Ontwikkelhulpprogramma's** **API-verbindingen**.

   ![Selecteer in het menu van de logische app de optie 'API-verbindingen'](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. Selecteer de gewenste gatewayverbinding en selecteer **API-verbinding bewerken**.

   > [!TIP]
   > Als uw updates niet van kracht worden, probeert u [het Gateway Windows-serviceaccount](../logic-apps/logic-apps-gateway-install.md#restart-gateway) voor uw gateway-installatie te stoppen en opnieuw op te starten.

Ga als verkenner op zoek naar alle API-verbindingen die zijn gekoppeld aan uw Azure-abonnement:

* Selecteer in het menu van de Azure-portal de optie **Alle services** > **Web** > **API-verbindingen**.
* Of selecteer **Alle bronnen**in het menu van de Azure-portal . Stel het filter **Type** in op **API-verbinding**.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Gatewaybron verwijderen

Als u een andere gatewaybron wilt maken, uw gateway-installatie wilt koppelen aan een andere gatewaybron of de gatewaybron wilt verwijderen, u de gatewaybron verwijderen zonder de gateway-installatie te beïnvloeden.

1. Selecteer in het menu van de Azure-portal **alle bronnen**of zoek naar en selecteer **Alle bronnen** op een pagina. Zoek en selecteer uw gatewaybron.

1. Als dit nog niet is geselecteerd, selecteert u in het menu Gateway gateway **on-premises gegevens .** Selecteer **verwijderen**op de werkbalk gatewaybron .

   Bijvoorbeeld:

   ![Gatewaybron in Azure verwijderen](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**V:** Waarom wordt mijn gateway-installatie niet weergegeven wanneer ik mijn gatewaybron in Azure maak? <br/>
**A:** Dit probleem kan om deze redenen gebeuren:

* Uw Azure-account moet hetzelfde account zijn dat is gekoppeld aan de gateway-installatie op de lokale computer. Controleer of u bent aangemeld bij de Azure-portal met dezelfde identiteit als die is gekoppeld aan de gateway-installatie. Zorg er ook voor dat uw Azure-account deel uitmaakt van één [Azure AD-tenant of -map](../active-directory/fundamentals/active-directory-whatis.md#terminology) en is ingesteld op dezelfde Azure AD-tenant of -map die is gebruikt tijdens de installatie van poorten.

* Uw gatewaybron en gateway-installatie moeten dezelfde regio gebruiken. De locatie van uw logische app kan echter afwijken van de locatie van uw gatewaybron.

* Uw gateway-installatie is al geregistreerd en geclaimd door een andere gatewaybron. Deze installaties worden niet weergegeven in de lijst **Installatienaam.** Als u uw gatewayregistraties wilt controleren in de Azure-portal, zoekt u al uw Azure-bronnen met het type **On-premises gegevensgateways** voor *al* uw Azure-abonnementen. Zie [Gatewayresource verwijderen](#change-delete-gateway-resource)als u de gateway-installatie wilt loskoppelen van de andere gatewaybron.

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Volgende stappen

* [Uw logische apps beveiligen](./logic-apps-securing-a-logic-app.md)
* [Veelvoorkomende voorbeelden en scenario's voor logische apps](./logic-apps-examples-and-scenarios.md)
