---
title: Gegevens, werkbladen en tabellen beheren in Excel Online
description: Gegevens beheren in werkbladen en tabellen in Excel Online voor Bedrijven of Excel Online voor OneDrive met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: b1df1219590aa94331a4404b1315eb7231c59699
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445870"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>Excel Online-gegevens beheren met Azure Logic Apps

Met [Azure Logic Apps](../logic-apps/logic-apps-overview.md) en de Excel Online for [Business-connector](/connectors/excelonlinebusiness/) of Excel Online [voor OneDrive-connector](/connectors/excelonline/) u geautomatiseerde taken en werkstromen maken op basis van uw gegevens in Excel Online voor Bedrijven of OneDrive. Deze connector biedt acties waarmee u werken met uw gegevens en spreadsheets beheren, bijvoorbeeld:

* Nieuwe werkbladen en tabellen maken.
* Werkbladen, tabellen en rijen oppakken en beheren.
* Voeg enkele rijen en sleutelkolommen toe.

U vervolgens de uitvoer van deze acties gebruiken met acties voor andere services. Als u bijvoorbeeld elke week een actie gebruikt waarmee werkbladen worden gemaakt, u een andere actie gebruiken die bevestigingse-mail verzendt met behulp van de Office 365 Outlook-connector.

Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> De [Excel Online voor Bedrijven-](/connectors/excelonlinebusiness/) en Excel Online voor [OneDrive-connectors](/connectors/excelonline/) werken met Azure Logic Apps en verschillen van de [Excel-connector voor PowerApps.](/connectors/excel/)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een [Office 365-account](https://www.office.com/) voor uw werkaccount of persoonlijk Microsoft-account

  Uw Excel-gegevens kunnen bestaan als een CSV-bestand (comma-separated value) in een opslagmap, bijvoorbeeld in OneDrive. 
  U ook hetzelfde CSV-bestand gebruiken met de [flat-file connector.](../logic-apps/logic-apps-enterprise-integration-flatfile.md)

* Basiskennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waar u toegang wilt krijgen tot uw Excel Online-gegevens. Deze connector biedt alleen acties, dus om uw logische app te starten, selecteert u een aparte trigger, bijvoorbeeld de **recidieftrigger.**

## <a name="add-excel-action"></a>Excel-actie toevoegen

1. Open uw logische app in de Logic App Designer in de [Azure-portal](https://portal.azure.com)als deze nog niet is geopend.

1. Kies Onder de trigger De optie **Nieuwe stap**.

1. Voer in het zoekvak 'excel' in als filter. Selecteer onder de lijst met acties de gewenste actie.

   > [!NOTE]
   > De Logic App Designer kan geen tabellen met 100 of meer kolommen laden. Verlaag indien mogelijk het aantal kolommen in de geselecteerde tabel, zodat de ontwerper de tabel kan laden.

1. Meld u desgevraagd aan bij uw Office 365-account.

   Uw referenties geven toestemming voor uw logische app om een verbinding met Excel Online te maken en toegang te krijgen tot uw gegevens.

1. Blijf de nodige details geven voor de geselecteerde actie en de workflow van uw logische app opbouwen.

## <a name="connector-reference"></a>Connector-verwijzing

Zie deze referentiepagina's voor connectoren, acties en limieten, zoals beschreven in de OpenAPI-bestanden (voorheen Swagger)-bestanden van de connector:

* [Excel Online voor Bedrijven](/connectors/excelonlinebusiness/)
* [Excel Online voor OneDrive](/connectors/excelonline/)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)
