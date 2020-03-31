---
title: Verbinding maken met ibm Informix-database
description: Taken en werkstromen automatiseren die resources beheren die zijn opgeslagen in IBM Informix met Azure Logic Apps
services: logic-apps
ms.suite: integration
author: gplarsen
ms.author: plarsen
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/07/2020
tags: connectors
ms.openlocfilehash: dccb715c974037b4e3080f3e51576feae34c03df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76757965"
---
# <a name="manage-ibm-informix-database-resources-by-using-azure-logic-apps"></a>IBM Informix-databasebronnen beheren met Azure Logic Apps

Met [Azure Logic Apps](../logic-apps/logic-apps-overview.md) en de [Informix-connector](/connectors/informix/)u geautomatiseerde taken en werkstromen maken die resources beheren in een IBM Informix-database. Deze connector bevat een Microsoft-client die communiceert met externe Informix-servercomputers in een TCP/IP-netwerk, inclusief clouddatabases zoals IBM Informix voor Windows die worden uitgevoerd in Azure-virtualisatie en on-premises databases wanneer u de [on-premises gegevensgateway gebruikt.](../logic-apps/logic-apps-gateway-connection.md) U verbinding maken met deze Informix-platforms en -versies als deze zijn geconfigureerd om DRDA-clientverbindingen (Distributed Relational Database Architecture) te ondersteunen:

* IBM Informix 12.1
* IBM Informix 11.7

In dit onderwerp ziet u hoe u de connector in een logische app gebruiken om databasebewerkingen te verwerken.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Download en installeer [de on-premises gegevensgateway](../logic-apps/logic-apps-gateway-install.md) voor on-premises databases op een lokale computer en maak vervolgens [een Azure-gegevensgatewaybron in de Azure-portal.](../logic-apps/logic-apps-gateway-connection.md)

* De logische app waar u toegang nodig hebt tot uw Informix-database. Deze connector biedt alleen acties, dus uw logische app moet al beginnen met een trigger, bijvoorbeeld de [recidieftrigger](../connectors/connectors-native-recurrence.md). 

## <a name="add-an-informix-action"></a>Een Informix-actie toevoegen

1. Open uw logische app in de Logic App Designer in de [Azure-portal](https://portal.azure.com)als deze nog niet is geopend.

1. Selecteer Onder de stap waarin u de actie Informix wilt toevoegen, de optie **Nieuwe stap**.

   Als u een actie tussen bestaande stappen wilt toevoegen, verplaatst u de muis over de verbindingspijl. Selecteer het plusteken (**+**) dat wordt weergegeven en selecteer vervolgens Een actie **toevoegen**.

1. Voer in het `informix` zoekvak in als filter. Selecteer in de lijst met acties de actie die u wilt, bijvoorbeeld:

   ![Selecteer de actie Informix die moet worden uitgevoerd](./media/connectors-create-api-informix/select-informix-connector-action.png)

   De connector biedt deze acties, die de bijbehorende databasebewerkingen uitvoeren:

   * Tabellen opmaken - Databasetabellen `CALL` weergeven met behulp van een instructie
   * Rijen opdoen - Alle `SELECT *` rijen lezen met een instructie
   * Rij opdoen - Een `SELECT WHERE` rij lezen met een instructie
   * Een rij toevoegen `INSERT` met een instructie
   * Een rij bewerken met `UPDATE` een instructie
   * Een rij verwijderen `DELETE` met behulp van een instructie

1. Als u wordt gevraagd verbindingsgegevens voor uw Informix-database op te geven, voert u de [stappen uit om de verbinding te maken](#create-connection)en gaat u verder met de volgende stap.

1. Geef de informatie op voor uw geselecteerde actie:

   | Actie | Beschrijving | Eigenschappen en beschrijvingen |
   |--------|-------------|-----------------------------|
   | **Tabellen oppakken** | Databasetabellen weergeven door een Informix CALL-instructie uit te voeren. | Geen |
   | **Rijen oppakken** | Haal alle rijen in de opgegeven tabel `SELECT *` op door een Informix-instructie uit te voeren. | **Tabelnaam:** de naam voor de gewenste Informix-tabel <p><p>Als u andere eigenschappen aan deze actie wilt toevoegen, selecteert u deze in de lijst **Nieuwe parameter toevoegen.** Zie het [referentieonderwerp van](/connectors/informix/)de connector voor meer informatie. |
   | **Rij oppakken** | Haal een rij uit de opgegeven tabel `SELECT WHERE` door een Informix-instructie uit te voeren. | - **Tabelnaam:** de naam voor de gewenste Informix-tabel <br>- **Rij-id:** de unieke id voor de rij, bijvoorbeeld,`9999` |
   | **Rij invoegen** | Voeg een rij toe aan de opgegeven Informix-tabel door een Informix-instructie `INSERT` uit te voeren. | - **Tabelnaam:** de naam voor de gewenste Informix-tabel <br>- **item**: de rij met de waarden die moeten worden toegevoegd |
   | **Rij bijwerken** | Een rij wijzigen in de opgegeven Informix-tabel door een Informix-instructie `UPDATE` uit te voeren. | - **Tabelnaam:** de naam voor de gewenste Informix-tabel <br>- **Rij-id:** de unieke id voor de rij om bij te werken, bijvoorbeeld`9999` <br>- **Rij:** de rij met de bijgewerkte waarden, bijvoorbeeld`102` |
   | **Rij verwijderen** | Verwijder een rij uit de opgegeven Informix-tabel door een Informix-instructie `DELETE` uit te voeren. | - **Tabelnaam:** de naam voor de gewenste Informix-tabel <br>- **Rij-id:** de unieke id voor de rij om te verwijderen, bijvoorbeeld`9999` |
   ||||

1. Sla uw logische app op. Test nu [uw logische app](#test-logic-app) of ga door met het bouwen van uw logische app.

<a name="create-connection"></a>

## <a name="connect-to-informix"></a>Verbinding maken met Informix

1. Als uw logische app verbinding maakt met een on-premises database, selecteert u **Verbinding maken via on-premises gegevensgateway.**

1. Geef deze verbindingsgegevens op en selecteer **Vervolgens Maken**.

   | Eigenschap | JSON-eigenschap | Vereist | Voorbeeldwaarde | Beschrijving |
   |----------|---------------|----------|---------------|-------------|
   | Verbindingsnaam | `name` | Ja | `informix-demo-connection` | De naam die u moet gebruiken voor de verbinding met uw Informix-database |
   | server | `server` | Ja | - Cloud:`informixdemo.cloudapp.net:9089` <br>- On-premises:`informixdemo:9089` | Het TCP/IP-adres of de alias die zich in de IPv4- of IPv6-indeling bevindt, gevolgd door een dubbele punt en een TCP/IP-poortnummer |
   | Database | `database` | Ja | `nwind` | De NAAM van de DRDA Relational Database (RDBNAM) of Informix-databasenaam (dbname). Informix accepteert een 128-byte string. |
   | Authentication | `authentication` | Alleen on-premises | **Basic** of **Windows** (kerberos) | Het verificatietype dat vereist is voor uw Informix-database. Deze eigenschap wordt alleen weergegeven wanneer u Verbinding maakt **via on-premises gegevensgateway.** |
   | Gebruikersnaam | `username` | Nee | <*database-gebruikersnaam*> | Een gebruikersnaam voor de database |
   | Wachtwoord | `password` | Nee | <*database-wachtwoord*> | Een wachtwoord voor de database |
   | Gateway | `gateway` | Alleen on-premises | - <*Azure-abonnement*> <br>- <*Azure-on-premises-data-gateway-resource*> | De azure-abonnement en de naam azure-bron voor de on-premises gegevensgateway die u hebt gemaakt in de Azure-portal. De eigenschap **Gateway** en subeigenschappen worden alleen weergegeven wanneer u **Verbinding via on-premises gegevensgateway**selecteert. |
   ||||||

   Bijvoorbeeld:

   * **Clouddatabase**

     ![Verbindingsgegevens van clouddatabase](./media/connectors-create-api-informix/informix-cloud-connection.png)

   * **On-premises database**

     ![On-premises databaseverbindingsgegevens](./media/connectors-create-api-informix/informix-on-premises-connection.png)

1. Sla uw logische app op.

<a name="test-logic-app"></a>

## <a name="test-your-logic-app"></a>Uw logische app testen

1. Selecteer Op de werkbalk Logic App Designer de optie **Uitvoeren**. Nadat uw logische app is uitgevoerd, u de uitvoer van die run bekijken.

1. Selecteer **Overzicht**in het menu van uw logische app . Selecteer in het overzichtsvenster onder Geschiedenis > **Overzichtsuitvoeringen**de meest recente uitvoering. **Summary**

1. Selecteer Details **uitvoeren**onder **Logische app uitvoeren**.

1. Selecteer in de lijst met acties de actie met de uitvoer die u wilt weergeven, bijvoorbeeld **Get_tables**.

   Als de actie is geslaagd, wordt de eigenschap **Status** gemarkeerd als **Geslaagd**.

1. Als u de invoer wilt weergeven, selecteert u onder **Invoerkoppeling**de URL-koppeling. Als u de uitvoer wilt weergeven, selecteert u onder koppeling **Uitvoerkoppeling** de URL-koppeling. Hier volgen enkele voorbeelden:

   * **Get_tables** toont een lijst met tabellen:

     ![Uitvoer van actie Tabellen ophalen](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

   * **Get_rows** toont een lijst met rijen:

     ![Uitvoer van actie Rijen ophalen](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

   * **Get_row** toont de opgegeven rij:

     ![Uitvoer van actie 'Rij ophalen'](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

   * **Insert_row** toont de nieuwe rij:

     ![Uitvoer van actie Rij invoegen](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

   * **Update_row** toont de bijgewerkte rij:

     ![Uitvoer van actie 'Rij bijwerken'](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

   * **Delete_row** toont de verwijderde rij:

     ![Uitvoer van actie Rij verwijderen](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="connector-specific-details"></a>Connector-specifieke details

Voor technische details over triggers, acties en limieten, die worden beschreven in de Swagger-beschrijving van de connector, raadpleegt u de [referentiepagina van](/connectors/informix/)de connector.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](apis-list.md)
