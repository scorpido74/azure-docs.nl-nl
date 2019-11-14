---
title: Toegang tot gegevens bronnen on-premises-Azure Logic Apps
description: Verbinding maken met on-premises gegevens bronnen vanuit Azure Logic Apps door een Azure on-premises gegevens gateway resource te maken
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 11/06/2019
ms.openlocfilehash: 20cdcfdd3795a7614c054ed96b82cc99fc2c5894
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74068899"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Verbinding maken met on-premises gegevens bronnen vanuit Azure Logic Apps

Voordat u toegang kunt krijgen tot gegevens bronnen on-premises vanuit uw Logic apps, moet u een Azure-resource maken nadat u [de *on-premises gegevens gateway* hebt geïnstalleerd op een lokale computer](../logic-apps/logic-apps-gateway-install.md). Uw Logic apps gebruiken deze Azure gateway-resource vervolgens in de triggers en acties die worden verschaft door de [on-premises connectors](../connectors/apis-list.md#on-premises-connectors) die beschikbaar zijn voor Azure Logic apps.

In dit artikel wordt beschreven hoe u een Azure gateway-resource maakt voor een eerder [geïnstalleerde gateway op uw lokale computer](../logic-apps/logic-apps-gateway-install.md). Zie [hoe de gateway werkt](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service)voor meer informatie over de gateway.

> [!TIP]
> Als u verbinding wilt maken met virtuele netwerken van Azure, kunt u in plaats daarvan een [*integratie service omgeving*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) maken. 

Zie de volgende artikelen voor meer informatie over het gebruik van de gateway met andere services:

* [Micro soft power on-premises gegevens gateway automatiseren](/power-automate/gateway-reference)
* [On-premises gegevens gateway van micro soft Power BI](/power-bi/service-gateway-onprem)
* [On-premises gegevens gateway van micro soft power apps](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services on-premises gegevens gateway](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Ondersteunde gegevensbronnen

De on-premises gegevens gateway in Azure Logic Apps ondersteunt de [on-premises connectors](../connectors/apis-list.md#on-premises-connectors) voor deze gegevens bronnen:

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

Azure Logic Apps ondersteunt Lees-en schrijf bewerkingen via de gegevens gateway. Deze bewerkingen hebben echter [limieten voor de grootte van de nettolading](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations). Hoewel de gateway zelf geen extra kosten in rekening brengt, is het [Logic apps prijs model](../logic-apps/logic-apps-pricing.md) van toepassing op deze connectors en andere bewerkingen in azure Logic apps.

## <a name="prerequisites"></a>Vereisten

* U hebt [de on-premises gegevens gateway al geïnstalleerd op een lokale computer](../logic-apps/logic-apps-gateway-install.md).

* U gebruikt [hetzelfde Azure-account en-abonnement](../logic-apps/logic-apps-gateway-install.md#requirements) dat is gebruikt bij het installeren van die gegevens gateway. Dit Azure-account moet deel uitmaken van een enkele [Azure Active Directory (Azure AD)-Tenant of-map](../active-directory/fundamentals/active-directory-whatis.md#terminology).

* De installatie van de gateway is niet al geregistreerd en geclaimd door een andere Azure gateway-resource.

  Wanneer u een gateway bron maakt in de Azure Portal, selecteert u een gateway-installatie, die verwijst naar de bron van de gateway en alleen die gateway bron. In Azure Logic Apps moeten on-premises triggers en acties vervolgens de gateway bron gebruiken om verbinding te maken met on-premises gegevens bronnen. In deze triggers en acties selecteert u uw Azure-abonnement en de bijbehorende gateway resource die u wilt gebruiken. Elke gateway bron is gekoppeld aan slechts één gateway-installatie, die slechts aan één Azure-account is gekoppeld.

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Azure gateway-resource maken

Nadat u de gateway op een lokale computer hebt geïnstalleerd, maakt u de Azure-resource voor uw gateway.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met hetzelfde Azure-account dat is gebruikt om de gateway te installeren.

1. Voer in het zoekvak Azure Portal "on-premises gegevens gateway" in en selecteer **on-premises gegevens gateways**.

   ![On-premises gegevens gateway zoeken](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

1. Selecteer **toevoegen**onder **on-premises gegevens gateways**.

   ![Nieuwe Azure-resource toevoegen voor gegevens gateway](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. Geef onder **verbindings gateway maken**deze informatie voor uw gateway bron op. Als u gereed bent, selecteert u **Maken**.

   | Eigenschap | Beschrijving |
   |----------|-------------|
   | **Resourcenaam** | Geef een naam op voor de gateway resource die alleen letters, cijfers, afbreek streepjes (`-`), onderstrepings tekens (`_`), haakjes (`(`, `)`) of punten (`.`) bevat. |
   | **Abonnement** | Selecteer het Azure-abonnement voor het Azure-account dat is gebruikt voor de installatie van de gateway. Het standaard abonnement is gebaseerd op het Azure-account dat u hebt gebruikt om u aan te melden. |
   | **Resourcegroep** | De [Azure-resource groep](../azure-resource-manager/resource-group-overview.md) die u wilt gebruiken |
   | **Locatie** | Dezelfde regio of locatie die is geselecteerd voor de gateway-Cloud service tijdens de installatie van de [Gateway](../logic-apps/logic-apps-gateway-install.md). Anders wordt de installatie van de gateway niet weer gegeven in de lijst **installatie naam** . De locatie van de logische app kan verschillen van de resource locatie van uw gateway. |
   | **Installatie naam** | Selecteer een gateway-installatie die alleen in de lijst wordt weer gegeven als aan deze voor waarden wordt voldaan: <p><p>-De gateway-installatie maakt gebruik van dezelfde regio als de gateway resource die u wilt maken. <br>-De gateway-installatie is niet gekoppeld aan een andere Azure gateway-resource. <br>-De gateway-installatie is gekoppeld aan hetzelfde Azure-account dat u gebruikt om de gateway bron te maken. <br>-Uw Azure-account hoort bij een enkele [Azure Active Directory (Azure AD)-Tenant of-map](../active-directory/fundamentals/active-directory-whatis.md#terminology) en is hetzelfde account dat is gebruikt voor de installatie van de gateway. <p><p>Zie het gedeelte Veelgestelde [vragen](#faq) voor meer informatie. |
   |||

   Hier volgt een voor beeld waarin een gateway-installatie wordt weer gegeven die zich in dezelfde regio bevindt als de gateway bron en is gekoppeld aan hetzelfde Azure-account:

   ![Details opgeven voor het maken van een gegevens gateway resource](./media/logic-apps-gateway-connection/add-azure-data-gateway-information.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Verbinding maken met on-premises gegevens

Nadat u de gateway resource hebt gemaakt en uw Azure-abonnement aan deze resource hebt gekoppeld, kunt u nu een verbinding maken tussen uw logische app en uw on-premises gegevens bron met behulp van de gateway.

1. In de Azure Portal maakt of opent u de logische app in de ontwerp functie voor logische apps.

1. Voeg een connector toe die on-premises verbindingen ondersteunt, bijvoorbeeld **SQL Server**.

1. Selecteer **verbinding via on-premises gegevens gateway**.

1. Selecteer in de lijst **abonnementen** onder **gateways**uw Azure-abonnement met de gateway resource die u wilt.

1. Selecteer in de lijst **verbindings gateway** , waarin de beschik bare gateway bronnen in het geselecteerde abonnement worden weer gegeven, de gateway resource die u wilt. Elke gateway bron is gekoppeld aan één gateway-installatie.

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

   ![Gateway resource verwijderen in azure](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway-resource.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**V**: Waarom wordt de installatie van mijn gateway niet weer gegeven wanneer ik mijn gateway resource Maak in azure? <br/>
**Een**: dit probleem kan om de volgende redenen optreden:

* Uw Azure-account moet hetzelfde account zijn dat is gekoppeld aan de gateway-installatie op de lokale computer. Controleer of u bent aangemeld bij de Azure Portal met dezelfde identiteit die is gekoppeld aan de gateway-installatie. Zorg er ook voor dat uw Azure-account tot één [Azure AD-Tenant of-directory](../active-directory/fundamentals/active-directory-whatis.md#terminology) behoort en is ingesteld op dezelfde Azure AD-Tenant of-map die werd gebruikt tijdens de installatie van de gateway.

* De installatie van de gateway bron en-gateway moet dezelfde regio gebruiken. De locatie van uw logische app kan echter verschillen van de locatie van uw gateway resource.

* De installatie van de gateway is al geregistreerd en geclaimd door een andere gateway resource. Deze installaties worden niet weer gegeven in de lijst **installatie naam** . Als u uw gateway registraties wilt controleren in de Azure Portal, kunt u alle Azure-resources die de **on-premises gegevens gateways** hebben, zoeken in *al* uw Azure-abonnementen. Zie [Gateway resource verwijderen](#change-delete-gateway-resource)als u de gateway-installatie wilt ontkoppelen van de andere gateway resource.

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Volgende stappen

* [Uw logische apps beveiligen](./logic-apps-securing-a-logic-app.md)
* [Algemene voor beelden en scenario's voor Logic apps](./logic-apps-examples-and-scenarios.md)
