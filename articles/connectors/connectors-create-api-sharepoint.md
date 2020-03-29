---
title: Verbinding maken met SharePoint vanuit Azure Logic Apps
description: Werk- en werkstromen automatiseren die resources in SharePoint Online of SharePoint Server on-premises bewaken en beheren met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: bb82ef2d6fb83c2e1b0fa81aa9504c9bb7d8234b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789253"
---
# <a name="monitor-and-manage-sharepoint-resources-with-azure-logic-apps"></a>SharePoint-bronnen bewaken en beheren met Azure Logic Apps

Met Azure Logic Apps en de SharePoint-connector u geautomatiseerde taken en werkstromen maken die resources bewaken en beheren, zoals bestanden, mappen, lijsten, items, personen enzovoort, in SharePoint Online of in SharePoint Server op locatie, bijvoorbeeld:

* Controleer wanneer bestanden of items worden gemaakt, gewijzigd of verwijderd.
* Items maken, ontvangen, bijwerken of verwijderen.
* Bijlagen toevoegen, oppakken of verwijderen. Haal de inhoud uit bijlagen.
* Bestanden maken, kopiëren, bijwerken of verwijderen. 
* Bestandseigenschappen bijwerken. Download de inhoud, metagegevens of eigenschappen voor een bestand.
* Mappen weergeven of extraheren.
* Lijsten of lijstweergaven oppakken.
* Stel de goedkeuringsstatus van inhoud in.
* Personen oplossen.
* Http-aanvragen verzenden naar SharePoint.
* Entiteitswaarden ophalen.

U triggers gebruiken die reacties van SharePoint ontvangen en de uitvoer beschikbaar maken voor andere acties. U acties in uw logische apps gebruiken om taken uit te voeren in SharePoint. U ook andere acties de uitvoer van SharePoint-acties laten gebruiken. Als u bijvoorbeeld regelmatig bestanden ophaalt via SharePoint, u berichten naar uw team verzenden met behulp van de Slack-connector.
Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Uw SharePoint-siteadres en gebruikersreferenties

  Uw referenties geven toestemming voor uw logische app om een verbinding te maken en toegang te krijgen tot uw SharePoint-account. 

* Voordat u logische apps koppelen aan on-premises systemen zoals SharePoint Server, moet u [een on-premises gegevensgateway installeren en instellen.](../logic-apps/logic-apps-gateway-install.md) Op die manier u opgeven om de gateway-installatie te gebruiken wanneer u de SharePoint Server-verbinding voor uw logische app maakt.

* Basiskennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waar u toegang wilt krijgen tot uw SharePoint-account. Als u wilt beginnen met een SharePoint-trigger, [maakt u een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Als u een SharePoint-actie wilt gebruiken, start u uw logische app met een trigger, zoals een Salesforce-trigger, als u een Salesforce-account hebt.

  U uw logische app bijvoorbeeld starten met de **Salesforce-trigger wanneer een record is gemaakt.** 
  Deze trigger vuurt elke keer dat een nieuwe record, zoals een lead, wordt gemaakt in Salesforce. 
  U deze trigger vervolgens volgen met de **bestandsactie SharePoint Create.** Op die manier maakt uw logische app een bestand in SharePoint met informatie over die nieuwe record wanneer de nieuwe record wordt gemaakt.

## <a name="connect-to-sharepoint"></a>Verbinding maken met SharePoint

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en open uw logische app in Logic App Designer, als deze nog niet is geopend.

1. Voer in het zoekvak 'sharepoint' in het zoekvak in als filter. Selecteer onder de lijst triggers de gewenste trigger. 

   -of-

   Kies Nieuwe **stap**voor bestaande logische apps onder de laatste stap waarin u een SharePoint-actie wilt toevoegen. 
   Voer in het zoekvak 'sharepoint' in als filter. 
   Selecteer onder de lijst met acties de gewenste actie.

   Als u een actie tussen de stappen wilt toevoegen, verplaatst u de aanwijzer over de pijl tussen de stappen. 
   Kies het plusteken (**+**) dat wordt weergegeven en selecteer Vervolgens Een actie **toevoegen**.

1. Wanneer u wordt gevraagd zich aan te melden, moet u de benodigde verbindingsgegevens verstrekken. Als u SharePoint Server gebruikt, controleert u verbinding **maken via on-premises gegevensgateway.** Wanneer u klaar bent, kiest u **Maken**.

1. Geef de benodigde details voor de geselecteerde trigger of actie en ga verder met het bouwen van de workflow van uw logische app.

## <a name="connector-reference"></a>Connector-verwijzing

Voor technische details over triggers, acties en limieten, die worden beschreven door de OpenAPI-beschrijving (voorheen Swagger) van de connector, raadpleegt u de [referentiepagina](/connectors/sharepoint/)van de connector.

## <a name="get-support"></a>Ondersteuning krijgen

* Ga naar het [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) (Forum voor Azure Logic Apps) als u vragen hebt.
* Als u ideeën voor functies wilt indienen of erop wilt stemmen, gaat u naar de [website voor feedback van Logic Apps-gebruikers](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)
