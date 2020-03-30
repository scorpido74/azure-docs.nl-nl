---
title: IBM DB2-resources openen en beheren
description: IBM DB2-resources lezen, bewerken, bijwerken en beheren door geautomatiseerde werkstromen te bouwen met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: plarsen, logicappspm
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: 32b482607827ee4420e39b1936586d64f9ea3139
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651378"
---
# <a name="access-and-manage-ibm-db2-resources-by-using-azure-logic-apps"></a>IBM DB2-resources openen en beheren met Azure Logic Apps

Met [Azure Logic Apps](../logic-apps/logic-apps-overview.md) en de IBM [DB2-connector](/connectors/db2/)u geautomatiseerde taken en werkstromen maken op basis van de resources die zijn opgeslagen in uw DB2-database. Uw werkstromen kunnen verbinding maken met de bronnen in uw database, uw databasetabellen lezen en aanbieden, rijen toevoegen, rijen wijzigen, rijen verwijderen en meer. U acties opnemen in uw logische apps die antwoorden uit uw database ontvangen en de uitvoer beschikbaar maken voor andere acties.

In dit artikel ziet u hoe u een logische app maken die verschillende databasebewerkingen uitvoert. Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="supported-platforms-and-versions"></a>Ondersteunde platforms en versies

De DB2-connector bevat een Microsoft-client die communiceert met externe DB2-servers in een TCP/IP-netwerk. U deze connector gebruiken voor toegang tot clouddatabases zoals IBM DB2 voor Windows die worden uitgevoerd in Azure-virtualisatie. U hebt ook toegang tot on-premises DB2-databases nadat u [de on-premises datagateway](../logic-apps/logic-apps-gateway-connection.md)hebt geïnstalleerd en ingesteld.

De IBM DB2-connector ondersteunt deze IBM DB2-platforms en -versies, samen met IBM DB2-compatibele producten die de DISTRIBUTEd Relational Database Architecture (DRDA) SQL Access Manager (SQLAM)-versies 10 en 11 ondersteunen:

| Platform | Versie | 
|----------|---------|
| IBM DB2 voor z/OS | 11.1, 10.1 |
| IBM DB2 voor i | 7.3, 7.2, 7.1 |
| IBM DB2 voor LUW | 11, 10.5 |
|||

## <a name="supported-database-operations"></a>Ondersteunde databasebewerkingen

De IBM DB2-connector ondersteunt deze databasebewerkingen, die de bijbehorende acties in de connector in kaart brengen:

| Databasebewerking | Connectoractie |
|--------------------|------------------|
| Databasetabellen weergeven | Tabellen oppakken |
| Eén rij lezen met SELECT | Rij oppakken |
| Alle rijen lezen met SELECT | Rijen oppakken |
| Eén rij toevoegen met INVOEGEN | Rij invoegen |
| Eén rij bewerken met UPDATE | Rij bijwerken |
| Eén rij verwijderen met DELETE | Rij verwijderen |
|||

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een IBM DB2-database, cloud-based of on-premises

* Basiskennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waar u toegang wilt krijgen tot uw DB2-database. Deze connector biedt alleen acties, dus om uw logische app te starten, selecteert u een aparte trigger, bijvoorbeeld de **recidieftrigger.**
De voorbeelden in dit artikel gebruiken de **recidieftrigger.**

<a name="add-db2-action"></a>

## <a name="add-db2-action---get-tables"></a>DB2-actie toevoegen - Tabellen opmaken

1. Open uw logische app in de Logic App Designer in de [Azure-portal](https://portal.azure.com)als deze nog niet is geopend.

1. Kies Onder de trigger De optie **Nieuwe stap**.

1. Voer in het zoekvak 'db2' in als filter. Selecteer in dit voorbeeld onder de lijst met acties de optie: **Tabellen opvragen (voorbeeld)**

   ![Actie selecteren](./media/connectors-create-api-db2/select-db2-action.png)

   U wordt nu gevraagd om verbindingsgegevens voor uw DB2-database op te geven.

1. Volg de stappen voor het maken van verbindingen voor [clouddatabases](#cloud-connection) of [on-premises databases.](#on-premises-connection)

<a name="cloud-connection"></a>

## <a name="connect-to-cloud-db2"></a>Verbinding maken met cloud DB2

Als u uw verbinding wilt instellen, geeft u deze verbindingsgegevens op wanneer u daarom wordt gevraagd, kiest **u Maken**en slaat u vervolgens uw logische app op:

| Eigenschap | Vereist | Beschrijving |
|----------|----------|-------------|
| **Verbinding maken via on-premises gateway** | Nee | Geldt alleen voor on-premises verbindingen. |
| **Verbindingsnaam** | Ja | De naam voor uw verbinding, bijvoorbeeld "MyLogicApp-DB2-verbinding" |
| **Server** | Ja | Het adres- of aliascolonpoortnummer voor uw DB2-server, bijvoorbeeld 'myDB2server.cloudapp.net:50000' <p><p>**Opmerking:** deze waarde is een tekenreeks die een TCP/IP-adres of alias vertegenwoordigt, hetzij in de IPv4- of IPv6-indeling, gevolgd door een dubbele punt en een TCP/IP-poortnummer. |
| **Database** | Ja | De naam voor uw database <p><p>**Opmerking:** deze waarde is een tekenreeks die een DRDA Relationele databasenaam (RDBNAM) vertegenwoordigt: <p>- DB2 voor z/OS accepteert een 16-byte string waarbij de database bekend staat als een "IBM DB2 voor z/OS" locatie. <br>- DB2 voor ik accepteert een 18-byte string waar de database bekend staat als een "IBM DB2 for i" relationele database. <br>- DB2 voor LUW accepteert een 8-byte string. |
| **Gebruikersnaam** | Ja | Uw gebruikersnaam voor de database <p><p>**Opmerking:** Deze waarde is een tekenreeks waarvan de lengte is gebaseerd op de specifieke database: <p><p>- DB2 voor z/OS accepteert een 8-byte string. <br>- DB2 voor ik accepteert een 10-byte string. <br>- DB2 voor Linux of UNIX accepteert een 8-byte string. <br>- DB2 voor Windows accepteert een 30-byte string. |
| **Wachtwoord** | Ja | Uw wachtwoord voor de database |
||||

Bijvoorbeeld:

![Verbindingsgegevens voor clouddatabases](./media/connectors-create-api-db2/create-db2-cloud-connection.png)

<a name="on-premises-connection"></a>

## <a name="connect-to-on-premises-db2"></a>Verbinding maken met on-premises DB2

Voordat u uw verbinding maakt, moet u uw on-premises gegevensgateway al hebben geïnstalleerd. Anders u het instellen van uw verbinding niet voltooien. Als u uw gateway-installatie hebt, gaat u verder met het verstrekken van deze verbindingsgegevens en kiest u **Maken**.

| Eigenschap | Vereist | Beschrijving |
|----------|----------|-------------|
| **Verbinding maken via on-premises gateway** | Ja | Geldt wanneer u een on-premises verbinding wilt en toont de on-premises verbindingseigenschappen. |
| **Verbindingsnaam** | Ja | De naam voor uw verbinding, bijvoorbeeld "MyLogicApp-DB2-verbinding" | 
| **Server** | Ja | Het adres- of aliascolonpoortnummer voor uw DB2-server, bijvoorbeeld "myDB2server:50000" <p><p>**Opmerking:** deze waarde is een tekenreeks die een TCP/IP-adres of alias vertegenwoordigt, hetzij in de IPv4- of IPv6-indeling, gevolgd door een dubbele punt en een TCP/IP-poortnummer. |
| **Database** | Ja | De naam voor uw database <p><p>**Opmerking:** deze waarde is een tekenreeks die een DRDA Relationele databasenaam (RDBNAM) vertegenwoordigt: <p>- DB2 voor z/OS accepteert een 16-byte string waarbij de database bekend staat als een "IBM DB2 voor z/OS" locatie. <br>- DB2 voor ik accepteert een 18-byte string waar de database bekend staat als een "IBM DB2 for i" relationele database. <br>- DB2 voor LUW accepteert een 8-byte string. |
| **Verificatie** | Ja | Het verificatietype voor uw verbinding, bijvoorbeeld 'Basic', <p><p>**Opmerking:** Selecteer deze waarde in de lijst, waaronder Basic of Windows (Kerberos). |
| **Gebruikersnaam** | Ja | Uw gebruikersnaam voor de database <p><p>**Opmerking:** Deze waarde is een tekenreeks waarvan de lengte is gebaseerd op de specifieke database: <p><p>- DB2 voor z/OS accepteert een 8-byte string. <br>- DB2 voor ik accepteert een 10-byte string. <br>- DB2 voor Linux of UNIX accepteert een 8-byte string. <br>- DB2 voor Windows accepteert een 30-byte string. |
| **Wachtwoord** | Ja | Uw wachtwoord voor de database |
| **Gateway** | Ja | De naam voor uw geïnstalleerde on-premises datagateway <p><p>**Opmerking:** Selecteer deze waarde in de lijst, die alle geïnstalleerde gegevensgateways binnen uw Azure-abonnement en -brongroep bevat. |
||||

Bijvoorbeeld:

![Verbindingsgegevens voor on-premises databases](./media/connectors-create-api-db2/create-db2-on-premises-connection.png)

### <a name="view-output-tables"></a>Uitvoertabellen weergeven

Als u uw logische app handmatig wilt uitvoeren, kiest u op de werkbalk van de ontwerper de optie **Uitvoeren**. Nadat de logische app is uitgevoerd, u de uitvoer van de run bekijken.

1. Selecteer **Overzicht**in het menu van de logische app .

1. Selecteer **onder Samenvatting**in de sectie Geschiedenis **uitvoeren** de meest recente run, het eerste item in de lijst.

   ![Uitvoeringsgeschiedenis weergeven](./media/connectors-create-api-db2/run-history.png)

1. Onder **Logic-app run**u nu de status, invoer en uitvoer voor elke stap in uw logische app bekijken.
Vouw de actie **Tabellen oppakken** uit.

   ![Actie uitvouwen](./media/connectors-create-api-db2/expand-action-step.png)

1. Als u de ingangen wilt weergeven, kiest **u Ruwe ingangen weergeven**.

1. Als u de uitvoer wilt weergeven, kiest **u Ruwe uitvoer weergeven**.

   De uitgangen bevatten een lijst met tabellen.

   ![Uitvoertabellen weergeven](./media/connectors-create-api-db2/db2-connector-get-tables-outputs.png)

## <a name="get-row"></a>Rij oppakken

Als u één record wilt ophalen in een DB2-databasetabel, gebruikt u de actie **Rij ophalen** in uw logische app. Met deze actie `SELECT WHERE` wordt bijvoorbeeld een `SELECT FROM AREA WHERE AREAID = '99999'`DB2-instructie uitgevoerd.

1. Als u DB2-acties nog nooit eerder hebt gebruikt in uw logische app, controleert u de stappen in de [actie DB2 toevoegen - Tabellen downloaden,](#add-db2-action) maar voegt u in plaats daarvan de actie **Rij downloaden** toe en keert u hier terug om verder te gaan.

   Nadat u de actie **Rij downloaden** hebt toegevoegd, wordt de manier waarop uw voorbeeldlogica-app wordt weergegeven:

   ![Rijactie weerkrijgen](./media/connectors-create-api-db2/db2-get-row-action.png)

1. Waarden opgeven voor alle vereiste eigenschappen (*). Nadat u een tabel hebt geselecteerd, worden in de actie de relevante eigenschappen weergegeven die specifiek zijn voor records in die tabel.

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Tabelnaam** | Ja | De tabel met de gewenste record, zoals 'GEBIED' in dit voorbeeld |
   | **Gebieds-ID** | Ja | De id voor de record die u wilt, zoals "99999" in dit voorbeeld |
   ||||

   ![Tabel selecteren](./media/connectors-create-api-db2/db2-get-row-action-select-table.png)

1. Wanneer u klaar bent, kiest u op de werkbalk van de ontwerper de optie **Opslaan**.

### <a name="view-output-row"></a>Uitvoerrij weergeven

Als u uw logische app handmatig wilt uitvoeren, kiest u op de werkbalk van de ontwerper de optie **Uitvoeren**. Nadat de logische app is uitgevoerd, u de uitvoer van de run bekijken.

1. Selecteer **Overzicht**in het menu van de logische app .

1. Selecteer **onder Samenvatting**in de sectie Geschiedenis **uitvoeren** de meest recente run, het eerste item in de lijst.

1. Onder **Logic-app run**u nu de status, invoer en uitvoer voor elke stap in uw logische app bekijken.
Vouw de actie **Rij weernemen** uit.

1. Als u de ingangen wilt weergeven, kiest **u Ruwe ingangen weergeven**.

1. Als u de uitvoer wilt weergeven, kiest **u Ruwe uitvoer weergeven**.

   De uitgangen bevatten de opgegeven rij.

   ![Uitvoerrij weergeven](./media/connectors-create-api-db2/db2-connector-get-row-outputs.png)

## <a name="get-rows"></a>Rijen oppakken

Als u alle records in een DB2-databasetabel wilt ophalen, gebruikt u de actie **Rijen ophalen** in uw logica-app. Met deze actie `SELECT` wordt bijvoorbeeld een `SELECT * FROM AREA`DB2-instructie uitgevoerd.

1. Als u DB2-acties nog nooit eerder hebt gebruikt in uw logische app, controleert u de stappen in de [actie DB2 toevoegen - Tabellen downloaden,](#add-db2-action) maar voegt u in plaats daarvan de actie **Rijen downloaden** toe en keert u hier terug om verder te gaan.

   Nadat u de actie **Rijen downloaden** hebt toegevoegd, wordt de manier waarop uw voorbeeldlogica-app wordt weergegeven:

   ![Actie Rijen uitvoeren](./media/connectors-create-api-db2/db2-get-rows-action.png)

1. Open de lijst **Tabelnaam** en selecteer vervolgens de gewenste tabel, namelijk 'GEBIED' in dit voorbeeld:

   ![Tabel selecteren](./media/connectors-create-api-db2/db2-get-rows-action-select-table.png)

1. Als u een filter of query voor resultaten wilt opgeven, kiest **u Geavanceerde opties weergeven**.

1. Wanneer u klaar bent, kiest u op de werkbalk van de ontwerper de optie **Opslaan**.

### <a name="view-output-rows"></a>Uitvoerrijen weergeven

Als u uw logische app handmatig wilt uitvoeren, kiest u op de werkbalk van de ontwerper de optie **Uitvoeren**. Nadat de logische app is uitgevoerd, u de uitvoer van de run bekijken.

1. Selecteer **Overzicht**in het menu van de logische app .

1. Selecteer **onder Samenvatting**in de sectie Geschiedenis **uitvoeren** de meest recente run, het eerste item in de lijst.

1. Onder **Logic-app run**u nu de status, invoer en uitvoer voor elke stap in uw logische app bekijken.
Vouw de actie **Rijen weergeven** uit.

1. Als u de ingangen wilt weergeven, kiest **u Ruwe ingangen weergeven**.

1. Als u de uitvoer wilt weergeven, kiest **u Ruwe uitvoer weergeven**.

   De uitvoer bevat alle records in de opgegeven tabel.

   ![Uitvoerrijen weergeven](./media/connectors-create-api-db2/db2-connector-get-rows-outputs.png)

## <a name="insert-row"></a>Rij invoegen

Als u één record wilt toevoegen aan een DB2-databasetabel, gebruikt u de actie **Rij invoegen** in uw logische app. Met deze actie `INSERT` wordt bijvoorbeeld een `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`DB2-instructie uitgevoerd.

1. Als u DB2-acties nog nooit eerder hebt gebruikt in uw logische app, controleert u de stappen in de [actie DB2 toevoegen - Tabellen downloaden,](#add-db2-action) maar voegt u in plaats daarvan de actie **Rij invoegen** toe en keert u hier terug om verder te gaan.

   Nadat u de actie **Rij invoegen** hebt toegevoegd, wordt de manier waarop uw voorbeeldlogica-app wordt weergegeven:

   ![Rijactie invoegen](./media/connectors-create-api-db2/db2-insert-row-action.png)

1. Waarden opgeven voor alle vereiste eigenschappen (*). Nadat u een tabel hebt geselecteerd, worden in de actie de relevante eigenschappen weergegeven die specifiek zijn voor records in die tabel.

   In dit voorbeeld gelden hier de eigenschappen:

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Tabelnaam** | Ja | De tabel waar de record moet worden toegevoegd, zoals 'GEBIED'. |
   | **Gebieds-ID** | Ja | De ID voor het gebied toe te voegen, zoals "99999" |
   | **Beschrijving van het gebied** | Ja | De beschrijving voor het gebied toe te voegen, zoals "Area 99999" |
   | **Regio-ID** | Ja | De ID voor de regio toe te voegen, zoals "102" |
   |||| 

   Bijvoorbeeld:

   ![Tabel selecteren](./media/connectors-create-api-db2/db2-insert-row-action-select-table.png)

1. Wanneer u klaar bent, kiest u op de werkbalk van de ontwerper de optie **Opslaan**.

### <a name="view-insert-row-outputs"></a>Rijuitvoer invoegen weergeven

Als u uw logische app handmatig wilt uitvoeren, kiest u op de werkbalk van de ontwerper de optie **Uitvoeren**. Nadat de logische app is uitgevoerd, u de uitvoer van de run bekijken.

1. Selecteer **Overzicht**in het menu van de logische app .

1. Selecteer **onder Samenvatting**in de sectie Geschiedenis **uitvoeren** de meest recente run, het eerste item in de lijst.

1. Onder **Logic-app run**u nu de status, invoer en uitvoer voor elke stap in uw logische app bekijken.
De actie **Rij invoegen** uitvouwen.

1. Als u de ingangen wilt weergeven, kiest **u Ruwe ingangen weergeven**.

1. Als u de uitvoer wilt weergeven, kiest **u Ruwe uitvoer weergeven**.

   De uitvoer bevat de record die u aan de opgegeven tabel hebt toegevoegd.

   ![Uitvoer weergeven met ingevoegde rij](./media/connectors-create-api-db2/db2-connector-insert-row-outputs.png)

## <a name="update-row"></a>Rij bijwerken

Als u één record in een DB2-databasetabel wilt bijwerken, gebruikt u de actie **Rij bijwerken** in uw logische app. Met deze actie `UPDATE` wordt bijvoorbeeld een `UPDATE AREA SET AREAID = '99999', AREADESC = 'Updated 99999', REGIONID = 102)`DB2-instructie uitgevoerd.

1. Als u DB2-acties nog nooit eerder hebt gebruikt in uw logische app, controleert u de stappen in de [actie DB2 toevoegen - Tabellen downloaden,](#add-db2-action) maar voegt u in plaats daarvan de actie **Rij bijwerken** toe en keert u hier terug om verder te gaan.

   Nadat u de actie **Rij bijwerken** hebt toegevoegd, wordt de manier waarop uw voorbeeldlogica-app wordt weergegeven:

   ![Actie rij bijwerken](./media/connectors-create-api-db2/db2-update-row-action.png)

1. Waarden opgeven voor alle vereiste eigenschappen (*). Nadat u een tabel hebt geselecteerd, worden in de actie de relevante eigenschappen weergegeven die specifiek zijn voor records in die tabel.

   In dit voorbeeld gelden hier de eigenschappen:

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Tabelnaam** | Ja | De tabel waar de record moet worden bijgewerkt, zoals 'GEBIED' |
   | **Rij-id** | Ja | De ID voor de record te updaten, zoals "99999" |
   | **Gebieds-ID** | Ja | De nieuwe area ID, zoals "99999" |
   | **Beschrijving van het gebied** | Ja | De nieuwe gebiedsbeschrijving, zoals "Updated 99999" |
   | **Regio-ID** | Ja | De nieuwe regio-ID, zoals "102" |
   ||||

   Bijvoorbeeld:

   ![Tabel selecteren](./media/connectors-create-api-db2/db2-update-row-action-select-table.png)

1. Wanneer u klaar bent, kiest u op de werkbalk van de ontwerper de optie **Opslaan**.

### <a name="view-update-row-outputs"></a>Updaterij-uitvoer weergeven

Als u uw logische app handmatig wilt uitvoeren, kiest u op de werkbalk van de ontwerper de optie **Uitvoeren**. Nadat de logische app is uitgevoerd, u de uitvoer van de run bekijken.

1. Selecteer **Overzicht**in het menu van de logische app .

1. Selecteer **onder Samenvatting**in de sectie Geschiedenis **uitvoeren** de meest recente run, het eerste item in de lijst.

1. Onder **Logic-app run**u nu de status, invoer en uitvoer voor elke stap in uw logische app bekijken.
De actie **Rij bijwerken** uitvouwen.

1. Als u de ingangen wilt weergeven, kiest **u Ruwe ingangen weergeven**.

1. Als u de uitvoer wilt weergeven, kiest **u Ruwe uitvoer weergeven**.

   De uitvoer bevat de record die u hebt bijgewerkt in de opgegeven tabel.

   ![Uitvoer weergeven met bijgewerkte rij](./media/connectors-create-api-db2/db2-connector-update-row-outputs.png)

## <a name="delete-row"></a>Rij verwijderen

Als u één record uit een DB2-databasetabel wilt verwijderen, gebruikt u de **actie Rij verwijderen** in uw logische app. Met deze actie `DELETE` wordt bijvoorbeeld een `DELETE FROM AREA WHERE AREAID = '99999'`DB2-instructie uitgevoerd.

1. Als u DB2-acties nog nooit eerder hebt gebruikt in uw logische app, controleert u de stappen in de [actie DB2 toevoegen - Tabellen downloaden,](#add-db2-action) maar voegt u in plaats daarvan de actie **Rij verwijderen** toe en keert u hier terug om verder te gaan.

   Nadat u de actie **Rij verwijderen** hebt toegevoegd, wordt de manier waarop uw voorbeeldlogica-app wordt weergegeven:

   ![Rijactie verwijderen](./media/connectors-create-api-db2/db2-delete-row-action.png)

1. Waarden opgeven voor alle vereiste eigenschappen (*). Nadat u een tabel hebt geselecteerd, worden in de actie de relevante eigenschappen weergegeven die specifiek zijn voor records in die tabel.

   In dit voorbeeld gelden hier de eigenschappen:

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Tabelnaam** | Ja | De tabel waar de record moet worden verwijderd, zoals 'GEBIED', zoals 'GEBIED'. |
   | **Rij-id** | Ja | De id voor de record te verwijderen, zoals "99999" |
   ||||

   Bijvoorbeeld:

   ![Tabel selecteren](./media/connectors-create-api-db2/db2-delete-row-action-select-table.png)

1. Wanneer u klaar bent, kiest u op de werkbalk van de ontwerper de optie **Opslaan**.

### <a name="view-delete-row-outputs"></a>Rijuitvoer weergeven

Als u uw logische app handmatig wilt uitvoeren, kiest u op de werkbalk van de ontwerper de optie **Uitvoeren**. Nadat de logische app is uitgevoerd, u de uitvoer van de run bekijken.

1. Selecteer **Overzicht**in het menu van de logische app .

1. Selecteer **onder Samenvatting**in de sectie Geschiedenis **uitvoeren** de meest recente run, het eerste item in de lijst.

1. Onder **Logic-app run**u nu de status, invoer en uitvoer voor elke stap in uw logische app bekijken.
De **actie Rij verwijderen** uitvouwen.

1. Als u de ingangen wilt weergeven, kiest **u Ruwe ingangen weergeven**.

1. Als u de uitvoer wilt weergeven, kiest **u Ruwe uitvoer weergeven**.

   De uitvoer bevat niet langer de record die u uit de opgegeven tabel hebt verwijderd.

   ![Uitvoer weergeven zonder verwijderde rij](./media/connectors-create-api-db2/db2-connector-delete-row-outputs.png)

## <a name="connector-reference"></a>Connector-verwijzing

Zie de [referentiepagina van](https://docs.microsoft.com/connectors/db2/)de connector voor meer technische details over deze connector, zoals triggers, acties en limieten zoals beschreven in het Swagger-bestand van de connector.

> [!NOTE]
> Voor logische apps in een [integratieserviceomgeving (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)gebruikt de ISE-versie met HET LABEL ISE in plaats daarvan de [ISE-berichtlimieten.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)
