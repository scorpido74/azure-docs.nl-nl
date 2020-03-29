---
title: Meer items of records met pagination
description: Paginatie instellen om de standaardlimiet voor paginagrootte voor connectoracties in Azure Logic Apps te overschrijden
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: 75d9660eb35b5d7ddc644d177c11ae489e2853dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792121"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>Meer gegevens, items of records downloaden met behulp van paginatie in Azure Logic Apps

Wanneer u gegevens, items of records ophaalt met behulp van een connectoractie in [Azure Logic Apps,](../logic-apps/logic-apps-overview.md)u resultaatsets krijgen die zo groot zijn dat de actie niet alle resultaten tegelijkertijd retourneert. Bij sommige acties kan het aantal resultaten groter zijn dan de standaardpaginagrootte van de connector. In dit geval retourneert de actie alleen de eerste pagina met resultaten. De standaardpaginagrootte voor de actie Rijen **van** de SQL Server-connector is bijvoorbeeld 2048, maar kan variëren op basis van andere instellingen.

Met sommige acties u een *paginatie-instelling* inschakelen, zodat uw logische app meer resultaten kan ophalen tot de paginatielimiet, maar deze resultaten retourneren als één bericht wanneer de actie is voltooid. Wanneer u paginatie gebruikt, moet u een *drempelwaarde* opgeven, het doelaantal resultaten dat u wilt dat de actie retourneert. De actie haalt resultaten op totdat de opgegeven drempelwaarde is bereikt. Wanneer het totale aantal items lager is dan de opgegeven drempelwaarde, worden alle resultaten opgehaald door de actie.

Als u de paginatie-instelling inschakelt, worden pagina's met resultaten opgehaald op basis van het paginaformaat van een connector. Dit gedrag betekent dat u soms meer resultaten krijgt dan de opgegeven drempelwaarde. Bijvoorbeeld bij het gebruik van de actie SQL Server **Get rijen,** die de instelling paginatie ondersteunt:

* De standaardpaginagrootte van de actie is 2048 records per pagina.
* Stel dat u 10.000 records hebt en 5000 records als minimum opgeeft.
* Pagination krijgt pagina's met records, dus om ten minste het opgegeven minimum te krijgen, retourneert de actie 6144 records (3 pagina's x 2048 records), niet 5000 records.

Hier is een lijst met slechts enkele van de connectors waar u de standaardpaginagrootte voor specifieke acties overschrijden:

* [Azure Blob Storage](https://docs.microsoft.com/connectors/azureblob/)
* [Dynamics 365](https://docs.microsoft.com/connectors/dynamicscrmonline/)
* [Excel](https://docs.microsoft.com/connectors/excel/)
* [HTTP (HTTP)](https://docs.microsoft.com/azure/connectors/connectors-native-http)
* [IBM DB2](https://docs.microsoft.com/connectors/db2/)
* [Microsoft Teams](https://docs.microsoft.com/connectors/teams/)
* [Oracle Database](https://docs.microsoft.com/connectors/oracle/)
* [Salesforce](https://docs.microsoft.com/connectors/salesforce/)
* [SharePoint](https://docs.microsoft.com/connectors/sharepointonline/)
* [SQL Server](https://docs.microsoft.com/connectors/sql/)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, [meldt u zich aan voor een gratis Azure-account.](https://azure.microsoft.com/free/)

* De logische app en de actie waar u pagination wilt inschakelen. Zie [Snelstart: Uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md)als u geen logische app hebt.

## <a name="turn-on-pagination"></a>Pagination inschakelen

Als u wilt bepalen of een actie paginatie in de Logic App Designer ondersteunt, controleert u de instellingen van de actie voor de **instelling Pagination.** In dit voorbeeld ziet u hoe u paginatie inschakelt in de actie **Rijen voor pagina's van** SQL Server.

1. Kies in de rechterbovenhoek van de actie de knop Ellipsen (**...**) en selecteer **Instellingen**.

   ![De instellingen van de actie openen](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   Als de actie paginatie ondersteunt, wordt in de actie de **instelling Pagination** weergegeven.

1. Wijzig de **instelling Pagination** van **Aan** naar **Aan**. Geef **in** de eigenschap Threshold een gehele waarde op voor het doelaantal resultaten dat de actie moet retourneren.

   ![Minimaal aantal resultaten opgeven om terug te keren](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. Als je er klaar voor bent, kies je **Gereed.**

## <a name="workflow-definition---pagination"></a>Werkstroomdefinitie - paginatie

Wanneer u paginatie inschakelt voor een actie die deze mogelijkheid ondersteunt, `"paginationPolicy"` bevat de `"minimumItemCount"` werkstroomdefinitie van `"runtimeConfiguration"` uw logische app de eigenschap samen met de eigenschap in de eigenschap van die actie, bijvoorbeeld:

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.testuri.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "paginationPolicy": {
            "minimumItemCount": 1000
         }
      },
      "type": "Http"
   }
},
```

## <a name="get-support"></a>Ondersteuning krijgen

Ga naar het [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) (Forum voor Azure Logic Apps) als u vragen hebt.
