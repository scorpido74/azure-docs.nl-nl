---
title: Verbinding maken met share point vanuit Azure Logic Apps | Microsoft Docs
description: Taken en werk stromen automatiseren waarmee resources in share point online of share Point server on-premises worden gecontroleerd en beheerd met behulp van Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: e0ec3149-507a-409d-8e7b-d5fbded006ce
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 8a34ee4e90b551da35aff8802c8badc0d74ff539
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050786"
---
# <a name="monitor-and-manage-sharepoint-resources-with-azure-logic-apps"></a>Share point-resources bewaken en beheren met Azure Logic Apps

Met Azure Logic Apps en de share point-connector kunt u geautomatiseerde taken en werk stromen maken voor het bewaken en beheren van resources, zoals bestanden, mappen, lijsten, items, personen, enzovoort, in share point online of in share Point server on-premises, bijvoorbeeld:

* Bewaken wanneer bestanden of items worden gemaakt, gewijzigd of verwijderd.
* Items maken, ophalen, bijwerken of verwijderen.
* Bijlagen toevoegen, ophalen of verwijderen. Haal de inhoud op uit bijlagen.
* Bestanden maken, kopiëren, bijwerken of verwijderen. 
* Bestands eigenschappen bijwerken. De inhoud, meta gegevens of eigenschappen voor een bestand ophalen.
* Mappen weer geven of uitpakken.
* Haal lijsten of lijst Weergaven op.
* Goedkeurings status van inhoud instellen.
* Personen oplossen.
* HTTP-aanvragen verzenden naar share point.
* Entiteits waarden ophalen.

U kunt triggers gebruiken die reacties ophalen van share point en de uitvoer beschikbaar maken voor andere acties. U kunt acties in uw Logic apps gebruiken om taken uit te voeren in share point. U kunt ook andere acties uitvoeren met de uitvoer van share point-acties. Als u bijvoorbeeld regel matig bestanden ophaalt vanuit share point, kunt u berichten verzenden naar uw team met behulp van de verbindings uitstel.
Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Het adres en de gebruikers referenties van uw share point-site

  Met uw referenties wordt uw logische app geautoriseerd om een verbinding te maken en toegang te krijgen tot uw share point-account. 

* Voordat u logische apps kunt verbinden met on-premises systemen zoals share Point server, moet u [een on-premises gegevens gateway installeren en instellen](../logic-apps/logic-apps-gateway-install.md). Op die manier kunt u opgeven dat u de gateway-installatie wilt gebruiken wanneer u de share Point server-verbinding maakt voor uw logische app.

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waartoe u toegang wilt krijgen tot uw share point-account. [Maak een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om te beginnen met een share point-trigger. Als u een share point-actie wilt gebruiken, start u uw logische app met een trigger, zoals een Sales Force-trigger, als u een Sales Force-account hebt.

  U kunt bijvoorbeeld uw logische app starten met de trigger **Wanneer een record wordt gemaakt** Sales Force. 
  Deze trigger wordt geactiveerd elke keer dat een nieuwe record, zoals een lead, wordt gemaakt in Sales Force. 
  U kunt deze trigger vervolgens volgen met de actie **bestand maken** van share point. Op die manier wordt bij het maken van de nieuwe record in de logische app een bestand in share point gemaakt met informatie over de nieuwe record.

## <a name="connect-to-sharepoint"></a>Verbinding maken met SharePoint

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open de logische app in de ontwerp functie voor logische apps, als deze nog niet is geopend.

1. Voor lege Logic apps voert u in het zoekvak de waarde share point in als uw filter. Selecteer de gewenste trigger onder de lijst met triggers. 

   -of-

   Kies voor bestaande Logic apps onder de laatste stap waar u een share point-actie wilt toevoegen de optie **nieuwe stap**. 
   Voer in het zoekvak ' share point ' in als uw filter. 
   Selecteer in de lijst acties de gewenste actie.

   Als u een actie tussen stappen wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen de stappen. 
   Kies het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

1. Wanneer u wordt gevraagd om u aan te melden, geeft u de benodigde verbindings gegevens op. Als u share Point server gebruikt, zorg er dan voor dat u **verbinding maken via een on-premises gegevens gateway**selecteert. Wanneer u klaar bent, kiest u **Maken**.

1. Geef de benodigde gegevens op voor de geselecteerde trigger of actie en ga door met het bouwen van de werk stroom van uw logische app.

## <a name="connector-reference"></a>Connector-verwijzing

Raadpleeg de [referentie pagina](/connectors/sharepoint/)van de connector voor technische informatie over triggers, acties en limieten die worden beschreven in de beschrijving van de OpenAPI (voorheen Swagger) van de connector.

## <a name="get-support"></a>Ondersteuning krijgen

* Ga naar het [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) (Forum voor Azure Logic Apps) als u vragen hebt.
* Als u ideeën voor functies wilt indienen of erop wilt stemmen, gaat u naar de [website voor feedback van Logic Apps-gebruikers](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps](../connectors/apis-list.md) -connectors
