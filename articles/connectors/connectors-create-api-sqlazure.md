---
title: Verbinding maken met SQL Server of Azure SQL Database
description: Automatiseer taken voor SQL-data bases on-premises of in de Cloud met behulp van Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/12/2020
tags: connectors
ms.openlocfilehash: f63553ced8484b3ce328fb9537d5831ae1e27fe8
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84191475"
---
# <a name="automate-workflows-for-sql-server-or-azure-sql-database-by-using-azure-logic-apps"></a>Werk stromen automatiseren voor SQL Server of Azure SQL Database met behulp van Azure Logic Apps

In dit artikel wordt beschreven hoe u toegang kunt krijgen tot gegevens in uw SQL database vanuit een logische app met de SQL Server-connector. Op die manier kunt u taken, processen of werk stromen automatiseren die uw SQL-gegevens en-resources beheren door logische apps te maken. De SQL Server-connector werkt zowel voor [SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) als [Azure SQL database](../azure-sql/database/sql-database-paas-overview.md) en [Azure SQL Managed instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).

U kunt logische apps maken die worden uitgevoerd wanneer deze worden geactiveerd door gebeurtenissen in uw SQL database of in andere systemen, zoals Dynamics CRM Online. Uw Logic apps kunnen ook gegevens ophalen, invoegen en verwijderen, samen met het uitvoeren van SQL-query's en opgeslagen procedures. U kunt bijvoorbeeld een logische app maken die automatisch controleert op nieuwe records in Dynamics CRM Online, items toevoegt aan uw SQL database voor nieuwe records en vervolgens e-mail waarschuwingen over de toegevoegde items verzendt.

Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps](../logic-apps/logic-apps-overview.md) en [Quick Start: uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md). Zie de pagina met Naslag informatie over [SQL Server-connector](https://docs.microsoft.com/connectors/sql/)voor connector-specifieke technische gegevens, beperkingen en bekende problemen.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een [SQL Server-Data Base](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) of [Azure-SQL database](../azure-sql/database/single-database-create-quickstart.md)

  Uw tabellen moeten gegevens bevatten zodat uw logische app resultaten kan retour neren wanneer bewerkingen worden aangeroepen. Als u een Azure SQL Database maakt, kunt u voorbeeld databases gebruiken die zijn opgenomen.

* De naam van uw SQL-Server, de database naam, uw gebruikers naam en uw wacht woord. U hebt deze referenties nodig zodat u uw logica kunt machtigen om toegang te krijgen tot uw SQL Server.

  * Voor SQL Server kunt u deze informatie vinden in het connection string:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * Voor Azure SQL Database kunt u deze details vinden in de connection string of in de Azure Portal onder de SQL Database eigenschappen:

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

* De [on-premises gegevens gateway](../logic-apps/logic-apps-gateway-install.md) die is geïnstalleerd op een lokale computer en een [Azure data gateway-resource die is gemaakt in het Azure Portal](../logic-apps/logic-apps-gateway-connection.md) voor deze scenario's:

  * Uw Logic apps worden niet uitgevoerd in een [integratie service omgeving (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

  * Uw Logic apps *worden* uitgevoerd in een Integration service-omgeving, maar u moet Windows-verificatie gebruiken voor uw SQL Server-verbinding. Voor dit scenario gebruikt u de niet-ISE-versie van de SQL Server connector samen met de gegevens gateway, omdat de ISE-versie geen ondersteuning biedt voor Windows-verificatie.

* De logische app waar u toegang tot uw SQL database nodig hebt. Als u uw logische app wilt starten met een SQL-trigger, hebt u een [lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)nodig.

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>Een SQL-trigger toevoegen

In Azure Logic Apps moet elke logische app beginnen met een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), die wordt geactiveerd wanneer een bepaalde gebeurtenis plaatsvindt of wanneer aan een bepaalde voor waarde wordt voldaan. Telkens wanneer de trigger wordt geactiveerd, maakt de Logic Apps-Engine een exemplaar van een logische app en begint de werk stroom van de logische app uit te voeren.

1. Maak in de Azure Portal of Visual Studio een lege logische app, waarmee Logic Apps Designer wordt geopend. In dit voor beeld wordt de Azure Portal gebruikt.

1. Voer op de ontwerp functie in het zoekvak ' SQL Server ' in als uw filter. Selecteer in de lijst triggers de gewenste SQL-trigger.

   In dit voor beeld wordt de trigger **Wanneer een item is gemaakt** gebruikt.

   ![De trigger ' wanneer een item wordt gemaakt ' selecteren](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Als u wordt gevraagd om een verbinding te maken, [maakt u nu uw SQL-verbinding](#create-connection). Als uw verbinding bestaat, selecteert u een **tabel naam**.

   ![Selecteer de tabel die u wilt](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

1. Stel de eigenschappen **interval** en **frequentie** in, waarmee wordt aangegeven hoe vaak uw logische app de tabel controleert.

   Deze trigger retourneert slechts één rij uit de geselecteerde tabel, niets anders. Als u andere taken wilt uitvoeren, moet u andere acties toevoegen waarmee de gewenste taken worden uitgevoerd. Als u de gegevens in deze rij bijvoorbeeld wilt weer geven, kunt u andere acties toevoegen waarmee een bestand wordt gemaakt dat de velden uit de geretourneerde rij bevat, en vervolgens e-mail waarschuwingen verzenden. Zie de [referentie pagina van de connector](https://docs.microsoft.com/connectors/sql/)voor meer informatie over andere beschik bare acties voor deze connector.

1. Wanneer u klaar bent, selecteert u op de werk balk ontwerpen de optie **Opslaan**.

   Met deze stap wordt uw logische app Live in azure automatisch ingeschakeld en gepubliceerd.

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>Een SQL-actie toevoegen

In Azure Logic Apps is een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) een stap in uw werk stroom die volgt op een trigger of een andere actie. In dit voor beeld wordt de logische app gestart met de [terugkeer patroon](../connectors/connectors-native-recurrence.md)en wordt een actie aangeroepen waarmee een rij uit een SQL database wordt opgehaald.

1. Open in de Azure Portal of Visual Studio uw logische app in Logic Apps Designer. In dit voor beeld wordt de Azure Portal gebruikt.

1. Selecteer **nieuwe stap**onder de trigger of actie waar u de SQL-actie wilt toevoegen.

   ![Nieuwe stap toevoegen aan uw logische app](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Als u een actie wilt toevoegen tussen de bestaande stappen, plaatst u de muis aanwijzer op de verbindings pijl. Selecteer het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

1. Onder **Kies een actie**, voert u in het zoekvak ' SQL Server ' in als uw filter. Selecteer in de lijst acties de gewenste SQL-actie.

   In dit voor beeld wordt de actie **rij ophalen** gebruikt, waarmee één record wordt opgehaald.

   ![Zoek en selecteer de actie SQL-rij ophalen](./media/connectors-create-api-sqlazure/find-select-sql-get-row-action.png)

   Met deze actie wordt slechts één rij uit de geselecteerde tabel geretourneerd, niets anders. Als u de gegevens in deze rij wilt weer geven, kunt u andere acties toevoegen waarmee een bestand wordt gemaakt dat de velden uit de geretourneerde rij bevat en dat bestand opslaan in een opslag account in de Cloud. Zie de [referentie pagina van de connector](https://docs.microsoft.com/connectors/sql/)voor meer informatie over andere beschik bare acties voor deze connector.

1. Als u wordt gevraagd om een verbinding te maken, [maakt u nu uw SQL-verbinding](#create-connection). Als uw verbinding bestaat, selecteert u een **tabel naam**en voert u de **rij-id** voor de gewenste record in.

   ![De tabel naam en de rij-ID invoeren](./media/connectors-create-api-sqlazure/specify-table-row-id-property-value.png)

1. Wanneer u klaar bent, selecteert u op de werk balk ontwerpen de optie **Opslaan**.

   Met deze stap wordt uw logische app Live in azure automatisch ingeschakeld en gepubliceerd.

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Verbinding maken met uw database

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>Bulk gegevens verwerken

Soms moet u met de resultaten sets zo groot zijn dat de connector niet alle resultaten tegelijk retourneert, of u wilt de controle over de grootte en de structuur voor uw resultaten sets beter te bepalen. Hier volgt een aantal manieren waarop u dergelijke grote resultaten sets kunt verwerken:

* Schakel *paginering*in om de resultaten te beheren als kleinere sets. Zie voor meer informatie [bulk gegevens, records en items ophalen met behulp van paginering](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md).

* Maak een opgeslagen procedure waarmee de resultaten op de gewenste manier worden georganiseerd.

  Wanneer u meerdere rijen haalt of invoegt, kan de logische app deze rijen door lopen met behulp van een [*until-lus*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) binnen deze [grenzen](../logic-apps/logic-apps-limits-and-config.md). Als uw logische app echter moet werken met record sets zo groot is, bijvoorbeeld duizenden of miljoenen rijen, die u de kosten wilt beperken die voortvloeien uit aanroepen naar de data base.

  Als u de resultaten op de gewenste manier wilt indelen, kunt u een [*opgeslagen procedure*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) maken die wordt uitgevoerd in uw SQL-exemplaar en gebruikmaakt van de **Select-order by-** instructie. Met deze oplossing hebt u meer controle over de grootte en de structuur van uw resultaten. Uw logische app roept de opgeslagen procedure aan met behulp van de actie voor het uitvoeren van een **opgeslagen procedure** van de SQL Server-connector.

  Raadpleeg de volgende artikelen voor meer informatie over de oplossing:

  * [SQL-paginering voor bulk overdracht van gegevens met Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [SELECT-ORDER BY-component](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

### <a name="handle-dynamic-bulk-data"></a>Dynamische bulk gegevens verwerken

Soms is de geretourneerde uitvoer dynamisch, wanneer u een opgeslagen procedure aanroept in de SQL Server-connector. In dit scenario voert u de volgende stappen uit:

1. Open **Logic apps Designer**.
1. Voer een test uitvoering van uw logische app uit om de uitvoer indeling te bekijken. Kopieer de uitvoer van uw voor beeld.
1. Selecteer in de ontwerp functie, onder de actie waar u de opgeslagen procedure aanroept, de optie **nieuwe stap**.
1. Zoek onder **Kies een actie**naar en selecteer de actie [**JSON parseren**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) .
1. Selecteer in de actie **JSON parseren** **voorbeeld lading gebruiken om schema te genereren**.
1. Plak in het venster **een voor beeld van een JSON-nettolading invoeren of plakken** de uitvoer van het voor beeld en selecteer vervolgens **gereed**.
1. Als er een fout bericht wordt weer gegeven dat Logic Apps geen schema kan genereren, controleert u of de syntaxis van de voorbeeld uitvoer correct is ingedeeld. Als u het schema nog steeds niet kunt genereren, voert u het hand matig in het vak **schema** in.
1. Selecteer **Opslaan**op de werk balk van de ontwerp functie.
1. Gebruik de gegevens tokens die worden weer gegeven in de lijst met dynamische inhoud onder de [actie **JSON parseren** ](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action)om toegang te krijgen tot de eigenschappen van de JSON-inhoud.

## <a name="connector-specific-details"></a>Connector-specifieke Details

Zie de [referentie pagina van de connector](https://docs.microsoft.com/connectors/sql/)voor technische informatie over de triggers, acties en limieten van deze connector.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [connectors voor Azure Logic apps](../connectors/apis-list.md)
