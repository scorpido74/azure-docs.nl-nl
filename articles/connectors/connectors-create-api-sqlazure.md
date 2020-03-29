---
title: Verbinding maken met SQL Server of Azure SQL Database
description: Taken voor SQL-databases op locatie of in de cloud automatiseren met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam; logicappspm
ms.topic: conceptual
ms.date: 11/08/2019
tags: connectors
ms.openlocfilehash: 93b63d332f00c31a352c11e483fc3ce5cb45a922
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789189"
---
# <a name="automate-workflows-for-sql-server-or-azure-sql-database-by-using-azure-logic-apps"></a>Werkstromen voor SQL Server of Azure SQL Database automatiseren met Azure Logic Apps

In dit artikel ziet u hoe u gegevens in uw SQL-database openen vanuit een logische app met de SQL Server-connector. Op die manier u taken, processen of werkstromen automatiseren die uw SQL-gegevens en -bronnen beheren door logische apps te maken. De SQL Server-connector werkt voor zowel [on-premises SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) als voor [cloudgebaseerde Azure SQL Database.](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)

U logische apps maken die worden uitgevoerd wanneer ze worden geactiveerd door gebeurtenissen in uw SQL-database of in andere systemen, zoals Dynamics CRM Online. Uw logische apps kunnen ook gegevens opvragen, invoegen en verwijderen, samen met het uitvoeren van SQL-query's en opgeslagen procedures. U bijvoorbeeld een logische app bouwen die automatisch controleert op nieuwe records in Dynamics CRM Online, items toevoegt aan uw SQL-database voor nieuwe records en vervolgens e-mailwaarschuwingen stuurt over de toegevoegde items.

Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps](../logic-apps/logic-apps-overview.md) en [Quickstart: Uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md). Zie de [referentiepagina sql server-connectorconnector](https://docs.microsoft.com/connectors/sql/)voor specifieke technische informatie, beperkingen en bekende problemen voor connectorspecifieke technische informatie, beperkingen en bekende problemen.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een [SQL Server-database](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) of [Azure SQL-database](../sql-database/sql-database-get-started-portal.md)

  Uw tabellen moeten gegevens bevatten, zodat uw logische app resultaten kan retourneren bij het aanroepen van bewerkingen. Als u een Azure SQL-database maakt, u voorbeelddatabases gebruiken die zijn opgenomen.

* Uw SQL-servernaam, databasenaam, uw gebruikersnaam en uw wachtwoord. U hebt deze referenties nodig, zodat u uw logica autoriseren om toegang te krijgen tot uw SQL-server.

  * Voor SQL Server vindt u deze gegevens in de verbindingstekenreeks:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * Voor Azure SQL Database u deze gegevens vinden in de verbindingstekenreeks of in de Azure-portal onder de eigenschappen van SQL Database:

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

* De [on-premises gegevensgateway](../logic-apps/logic-apps-gateway-install.md) die is geïnstalleerd op een lokale computer en een [Azure-gegevensgatewaybron die is gemaakt in de Azure-portal](../logic-apps/logic-apps-gateway-connection.md) voor deze scenario's:

  * Uw logische apps worden niet uitgevoerd in een [integratieserviceomgeving (ISE).](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)

  * Uw logische apps worden *wel* uitgevoerd in een integratieserviceomgeving, maar u moet Windows-verificatie gebruiken voor uw SQL Server-verbinding. Gebruik voor dit scenario de niet-ISE-versie van de SQL Server-connector samen met de gegevensgateway omdat de ISE-versie geen Windows-verificatie ondersteunt.

* De logische app waar u toegang nodig hebt tot uw SQL-database. Als u uw logica-app wilt starten met een SQL-trigger, hebt u een [lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)nodig.

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>Een SQL-trigger toevoegen

In Azure Logic Apps moet elke logische app beginnen met een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), die wordt geactiveerd wanneer een specifieke gebeurtenis plaatsvindt of wanneer aan een specifieke voorwaarde is voldaan. Elke keer dat de trigger wordt geactiveerd, maakt de Logic Apps-engine een logische app-instantie en wordt de werkstroom van de logische app uitgevoerd.

1. Maak in de Azure-portal of Visual Studio een lege logische app, waarmee Logic Apps Designer wordt geopend. In dit voorbeeld wordt de Azure-portal gebruikt.

1. Voer op de ontwerper in het zoekvak 'sql-server' in als filter. Selecteer in de lijst triggers de gewenste SQL-trigger.

   In dit voorbeeld wordt de **trigger Wanneer een item wordt gemaakt, gebruikt.**

   ![De trigger 'Wanneer een item wordt gemaakt' selecteren](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Als u wordt gevraagd een verbinding te maken, [maakt u nu uw SQL-verbinding.](#create-connection) Als uw verbinding bestaat, selecteert u een **tabelnaam**.

   ![Selecteer de gewenste tabel](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

1. Stel de eigenschappen **Interval** en **Frequentie** in, waarin wordt aangegeven hoe vaak uw logische app de tabel controleert.

   Deze trigger retourneert slechts één rij uit de geselecteerde tabel, niets anders. Als u andere taken wilt uitvoeren, voegt u andere acties toe die de gewenste taken uitvoeren. Als u bijvoorbeeld de gegevens in deze rij wilt weergeven, u andere acties toevoegen die een bestand maken dat de velden uit de geretourneerde rij bevat en vervolgens e-mailwaarschuwingen verzenden. Zie de [referentiepagina van](https://docs.microsoft.com/connectors/sql/)de connector voor meer informatie over andere beschikbare acties voor deze connector.

1. Wanneer u klaar bent, selecteert u op de werkbalk van de ontwerper de optie **Opslaan**.

   Met deze stap wordt uw logische app automatisch live in Azure in- en gepubliceerd.

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>Een SQL-actie toevoegen

In Azure Logic Apps is een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) een stap in uw werkstroom die een trigger of een andere actie volgt. In dit voorbeeld begint de logische app met de [recidieftrigger](../connectors/connectors-native-recurrence.md)en roept een actie aan die een rij uit een SQL-database haalt.

1. Open uw logische app in de Azure-portal of Visual Studio in Logic Apps Designer. In dit voorbeeld wordt de Azure-portal gebruikt.

1. Selecteer Nieuwe **stap**onder de trigger of actie waarbij u de SQL-actie wilt toevoegen.

   ![Nieuwe stap toevoegen aan uw logica-app](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Als u een actie tussen bestaande stappen wilt toevoegen, verplaatst u de muis over de verbindingspijl. Selecteer het plusteken (**+**) dat wordt weergegeven en selecteer vervolgens Een actie **toevoegen**.

1. Voer **onder Een actie kiezen**in het zoekvak 'sql-server' in als filter. Selecteer in de lijst met acties de gewenste SQL-actie.

   In dit voorbeeld wordt de actie **Rij weergeven** gebruikt, waarmee één record wordt opgenomen.

   ![SQL-actie 'Rij opdoen' zoeken en selecteren](./media/connectors-create-api-sqlazure/find-select-sql-get-row-action.png)

   Met deze actie wordt slechts één rij uit de geselecteerde tabel geretourneerd, niets anders. Als u de gegevens in deze rij wilt weergeven, u andere acties toevoegen die een bestand maken dat de velden uit de geretourneerde rij bevat en dat bestand opslaan in een cloudopslagaccount. Zie de [referentiepagina van](https://docs.microsoft.com/connectors/sql/)de connector voor meer informatie over andere beschikbare acties voor deze connector.

1. Als u wordt gevraagd een verbinding te maken, [maakt u nu uw SQL-verbinding.](#create-connection) Als uw verbinding bestaat, selecteert u een **tabelnaam**en voert u de **rij-id** in voor de gewenste record.

   ![Voer de tabelnaam en rij-id in](./media/connectors-create-api-sqlazure/specify-table-row-id-property-value.png)

1. Wanneer u klaar bent, selecteert u op de werkbalk van de ontwerper de optie **Opslaan**.

   Met deze stap wordt uw logische app automatisch live in Azure in- en gepubliceerd.

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Verbinding maken met uw database

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>Bulkgegevens verwerken

Soms moet u werken met resultaatsets die zo groot zijn dat de connector niet alle resultaten tegelijkertijd retourneert, of wilt u betere controle over de grootte en structuur voor uw resultaatsets. Hier volgen enkele manieren waarop u zulke grote resultaatsets verwerken:

* Als u resultaten wilt beheren als kleinere sets, schakelt u *paginatie*in. Zie [Bulkgegevens, records en items opvragen met paginatie](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md)voor meer informatie.

* Maak een opgeslagen procedure die de resultaten op de gewenste manier organiseert.

  Bij het krijgen of invoegen van meerdere rijen kan uw logische app door deze rijen heen worden gehesen met behulp van een [*lus*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) tot binnen deze [limieten.](../logic-apps/logic-apps-limits-and-config.md) Wanneer uw logica-app echter moet werken met recordsets die zo groot zijn, bijvoorbeeld duizenden of miljoenen rijen, wilt u de kosten die voortvloeien uit oproepen naar de database minimaliseren.

  Als u de resultaten wilt ordenen op de gewenste manier, u een [*opgeslagen procedure*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) maken die wordt uitgevoerd in uw SQL-instantie en de instructie SELECT - **ORDER BY** gebruikt. Deze oplossing geeft u meer controle over de grootte en structuur van uw resultaten. Uw logische app roept de opgeslagen procedure aan met de actie **Opgeslagen procedure uitvoeren van** de SQL Server-connector.

  Zie de volgende artikelen voor meer oplossingsinformatie:

  * [SQL Pagination voor bulkgegevensoverdracht met Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [SELECT - ORDER BY Clause](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>Connector-specifieke details

Zie de [referentiepagina van](https://docs.microsoft.com/connectors/sql/)de connector voor technische informatie over de triggers, acties en limieten van deze connector.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [connectors voor Azure Logic Apps](../connectors/apis-list.md)
