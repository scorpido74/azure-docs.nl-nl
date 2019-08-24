---
title: Toegang tot gegevens bronnen on-premises vanuit Azure Logic Apps | Microsoft Docs
description: Verbinding maken met on-premises gegevens bronnen vanuit Logic apps met behulp van een on-premises gegevens gateway
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 029dc8daaf456c155d46eefa699772882bdabee5
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982874"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Verbinding maken met on-premises gegevens bronnen vanuit Azure Logic Apps

Om toegang te krijgen tot gegevens bronnen on-premises vanuit uw Logic apps, maakt u een on-premises gegevens gateway resource in de Azure Portal. Uw Logic apps kunnen vervolgens gebruikmaken [van de on-premises connectors](../logic-apps/logic-apps-gateway-install.md#supported-connections). In dit artikel wordt beschreven hoe u uw Azure gateway-resource maakt *nadat* u [de gateway op uw lokale computer hebt gedownload en geïnstalleerd](../logic-apps/logic-apps-gateway-install.md). 

> [!TIP]
> Als u verbinding wilt maken met virtuele netwerken van Azure, kunt u in plaats daarvan een [*integratie service omgeving*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) maken. 

Zie de volgende artikelen voor meer informatie over het gebruik van de gateway met andere services:

* [On-premises gegevens gateway van micro soft Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft Flow on-premises gegevens gateway](https://flow.microsoft.com/documentation/gateway-manage/)
* [Microsoft PowerApps on-premises gegevens gateway](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Azure Analysis Services on-premises gegevens gateway](../analysis-services/analysis-services-gateway.md)

## <a name="prerequisites"></a>Vereisten

* U hebt [de gegevens gateway al gedownload en geïnstalleerd op een lokale computer](../logic-apps/logic-apps-gateway-install.md).

* De installatie van de gateway is niet al gekoppeld aan een gateway bron in Azure. U kunt uw gateway-installatie alleen koppelen aan één gateway bron, die wordt uitgevoerd wanneer u de gateway bron maakt en de installatie van de gateway selecteert. Met deze koppeling kan de gateway-installatie niet meer beschikbaar zijn voor andere resources.

* Wanneer u zich aanmeldt bij de Azure Portal en de gateway bron maakt, moet u ervoor zorgen dat u hetzelfde aanmeldings account gebruikt dat eerder werd gebruikt voor [het installeren van de on-premises gegevens gateway](../logic-apps/logic-apps-gateway-install.md#requirements) , samen met hetzelfde [Azure-abonnement](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access) dat is gebruikt voor het installeren van de #b0. Als u nog geen Azure-abonnement hebt, <a href="https://azure.microsoft.com/free/" target="_blank">meldt u zich aan voor een gratis Azure-account</a>.

* Als u de gateway resource wilt maken en onderhouden in de Azure Portal, moet uw [Windows-Service account](../logic-apps/logic-apps-gateway-install.md#windows-service-account) mini maal **Inzender** machtigingen hebben. De on-premises gegevens gateway wordt uitgevoerd als een Windows-service en wordt ingesteld `NT SERVICE\PBIEgwService` om te worden gebruikt voor de aanmeldings referenties van de Windows-service. 

  > [!NOTE]
  > Het Windows-Service account wijkt af van het account dat wordt gebruikt om verbinding te maken met on-premises gegevens bronnen en van het werk-of school account van Azure dat wordt gebruikt om u aan te melden bij Cloud Services.

## <a name="download-and-install-gateway"></a>Gateway downloaden en installeren

Voordat u met de stappen in dit artikel kunt door gaan, moet u controleren of uw gateway al is geïnstalleerd op een lokale computer.
Als u dat nog niet hebt gedaan, volgt u de stappen om [de on-premises gegevens gateway te downloaden en te installeren](../logic-apps/logic-apps-gateway-install.md). 

<a name="create-gateway-resource"></a>

## <a name="create-azure-resource-for-gateway"></a>Een Azure-resource maken voor de gateway

Nadat u de gateway hebt geïnstalleerd op een lokale computer, kunt u vervolgens een Azure-resource maken voor uw gateway. Met deze stap wordt ook uw gateway resource gekoppeld aan uw Azure-abonnement.

1. Meld u aan bij <a href="https://portal.azure.com" target="_blank">Azure Portal</a>. Zorg ervoor dat u hetzelfde e-mail adres voor Azure werk of school gebruikt om de gateway te installeren.

2. Selecteer in het hoofd menu van Azure de optie **een resource** > 
-**integratie** > **voor on-premises gegevens gateway**maken.

   ![On-premises gegevens gateway zoeken](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

3. Geef op de pagina **verbindings gateway maken** deze informatie op voor uw gateway Bron:

   | Eigenschap | Description | 
   |----------|-------------|
   | **Resourcenaam** | De resource naam van de gateway, die alleen letters, cijfers, afbreek streepjes`-`(), onderstrepings tekens`_`(), haakjes`(`( `)`,) en punten (`.`) kan bevatten. | 
   | **Abonnement** | De naam van uw Azure-abonnement. dit moet hetzelfde abonnement zijn als uw logische app. Het standaard abonnement is gebaseerd op het Azure-account waarmee u zich hebt aangemeld. | 
   | **Resourcegroep** | De naam voor de [Azure-resource groep](../azure-resource-manager/resource-group-overview.md) voor het ordenen van gerelateerde resources | 
   | **Location** | Azure beperkt deze locatie tot dezelfde regio die is geselecteerd voor de gateway-Cloud service tijdens de installatie van de [Gateway](../logic-apps/logic-apps-gateway-install.md). <p>**Opmerking**: Zorg ervoor dat deze gateway resource locatie overeenkomt met de locatie van de Cloud service van de gateway. Anders wordt de installatie van de gateway mogelijk niet weer gegeven in de lijst met geïnstalleerde gateways, zodat u deze in de volgende stap kunt selecteren. U kunt verschillende regio's voor uw gateway bron en voor uw logische app gebruiken. | 
   | **Installatie naam** | Als de installatie van de gateway nog niet is geselecteerd, selecteert u de gateway die u eerder hebt geïnstalleerd. | 
   | | | 

   Hier volgt een voorbeeld:

   ![Geef details op voor het maken van uw on-premises gegevens gateway](./media/logic-apps-gateway-connection/createblade.png)

4. Als u de gateway resource wilt toevoegen aan uw Azure-dash board, selecteert u **vastmaken aan dash board**. Wanneer u klaar bent, kiest u **Maken**.

   Als u uw gateway op elk gewenst moment wilt zoeken of bekijken, selecteert u in het hoofd menu van Azure **alle services**. 
   Voer in het zoekvak ' on-premises gegevens gateways ' in en selecteer vervolgens **on-premises gegevens gateways**.

   ![On-premises gegevens gateways zoeken](./media/logic-apps-gateway-connection/find-on-premises-data-gateway-enterprise-integration.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Verbinding maken met on-premises gegevens

Nadat u de gateway resource hebt gemaakt en uw Azure-abonnement aan deze resource hebt gekoppeld, kunt u nu een verbinding maken tussen uw logische app en uw on-premises gegevens bron met behulp van de gateway.

1. In de Azure Portal maakt of opent u de logische app in de ontwerp functie voor logische apps.

2. Voeg een connector toe die on-premises verbindingen ondersteunt, bijvoorbeeld **SQL Server**.

3. Stel nu uw verbinding in:

   1. Selecteer **verbinding via on-premises gegevens gateway**. 

   2. Selecteervoor gateways de gateway resource die u eerder hebt gemaakt. 

      Hoewel uw locatie voor de gateway verbinding in dezelfde regio als uw logische app moet staan, kunt u een gateway in een andere regio selecteren.

   3. Geef een unieke verbindings naam en de andere vereiste informatie op. 

      De unieke naam van de verbinding helpt u die verbinding later eenvoudig te identificeren, met name wanneer u meerdere verbindingen maakt. Indien van toepassing moet u ook het gekwalificeerde domein voor uw gebruikers naam toevoegen.
   
      Hier volgt een voorbeeld:

      ![Verbinding maken tussen logische app en gegevens gateway](./media/logic-apps-gateway-connection/blankconnection.png)

   4. Wanneer u klaar bent, kiest u **Maken**. 

Uw gateway verbinding is nu klaar voor gebruik door uw logische app.

## <a name="edit-connection"></a>Verbinding bewerken

Nadat u een gateway verbinding voor uw logische app hebt gemaakt, kunt u de instellingen voor die specifieke verbinding later bijwerken.

1. Uw gateway verbinding zoeken:

   * Als u alle API-verbindingen voor uw logische app wilt vinden, selecteert u in het menu van de logische app onder **ontwikkelingsprogram ma's**de optie **API-verbindingen**. 
   
     ![Ga naar uw logische app, selecteer API-verbindingen](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

   * Om te zoeken naar alle API-verbindingen die zijn gekoppeld aan uw Azure-abonnement: 

     * Ga in het hoofd menu van Azure naar **alle services** > **Web** > **API-verbindingen**. 
     * Of Ga vanuit het hoofd menu van Azure naar **alle resources**.

2. Selecteer de gewenste gateway verbinding en kies vervolgens API- **verbinding bewerken**.

   > [!TIP]
   > Als uw updates niet van kracht worden, kunt u proberen om [de Windows-service van de gateway te stoppen en opnieuw te starten](./logic-apps-gateway-install.md#restart-gateway).

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Gateway resource verwijderen

Als u een andere gateway bron wilt maken, koppelt u uw gateway aan een andere resource of verwijdert u de gateway bron, kunt u de gateway bron verwijderen zonder dat dit van invloed is op de installatie van de gateway. 

1. Ga naar **alle resources**in het hoofd menu van Azure. 

2. Zoek en selecteer de bron van de gateway.

3. Als dit nog niet is geselecteerd, selecteert u in het menu van de gateway resource de optie **on-premises gegevens gateway**. 

4. Kies **verwijderen**op de werk balk van de resource.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="get-support"></a>Ondersteuning krijgen

* Ga naar het [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) (Forum voor Azure Logic Apps) als u vragen hebt.
* Als u ideeën voor functies wilt indienen of erop wilt stemmen, gaat u naar de [website voor feedback van Logic Apps-gebruikers](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Volgende stappen

* [Uw logische apps beveiligen](./logic-apps-securing-a-logic-app.md)
* [Algemene voor beelden en scenario's voor Logic apps](./logic-apps-examples-and-scenarios.md)
