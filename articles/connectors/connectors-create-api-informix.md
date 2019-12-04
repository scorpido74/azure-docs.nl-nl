---
title: Verbinding maken met de IBM Informix-data base
description: Resources beheren met de IBM Informix REST Api's en Azure Logic Apps
services: logic-apps
ms.suite: integration
author: gplarsen
ms.author: plarsen
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/26/2016
tags: connectors
ms.openlocfilehash: d6f768bc76d19c0aa21a245c008a4b05588f8f43
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789725"
---
# <a name="get-started-with-the-informix-connector"></a>Aan de slag met de Informix-connector
Micro soft-connector voor Informix verbindt Logic Apps met resources die zijn opgeslagen in een IBM Informix-data base. De Informix-connector bevat een micro soft-client om te communiceren met externe Informix-Server computers via een TCP/IP-netwerk. Dit omvat Cloud databases, zoals IBM Informix voor Windows die worden uitgevoerd in azure virtualisatie, en on-premises data bases met behulp van de on-premises gegevens gateway. Zie de [lijst met ondersteunde](connectors-create-api-informix.md#supported-informix-platforms-and-versions) IBM Informix-platforms en-versies (in dit onderwerp).

De connector ondersteunt de volgende database bewerkingen:

* Database tabellen weer geven
* Eén rij lezen met selecteren
* Alle rijen lezen met behulp van selecteren
* Eén rij toevoegen met INSERT
* Een rij met UPDATE wijzigen
* Eén rij verwijderen met behulp van verwijderen

In dit onderwerp wordt beschreven hoe u de connector in een logische app kunt gebruiken om database bewerkingen te verwerken.

Zie [een logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md)voor meer informatie over Logic apps.

## <a name="available-actions"></a>Beschik bare acties
Deze connector ondersteunt de volgende bewerkingen voor logische apps:

* Getables
* GetRow
* Ophalen
* InsertRow
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>Tabellen weer geven
Het maken van een logische app voor elke bewerking bestaat uit veel stappen die worden uitgevoerd via de Microsoft Azure-portal.

In de logische app kunt u een actie toevoegen aan lijst tabellen in een Informix-data base. Met deze actie wordt de connector geïnstrueerd een Informix-schema-instructie te verwerken, zoals `CALL SYSIBM.SQLTABLES`.

### <a name="create-a-logic-app"></a>Een logische app maken
1. Selecteer in het **Start bord van Azure**de optie **+** (plus teken), **Web en mobiel**en vervolgens **logische app**.
2. Voer de **naam**in, zoals `InformixgetTables`, **abonnement**, **Resource groep**, **locatie**en **app service plan**. Selecteer vastmaken **aan dash board**en selecteer vervolgens **maken**.

### <a name="add-a-trigger-and-action"></a>Een trigger en actie toevoegen
1. Selecteer in de **Logic apps Designer** **lege LogicApp** in de lijst **sjablonen** .
2. Selecteer in de lijst **Triggers** de optie **terugkeer patroon**. 
3. Selecteer in de trigger voor **terugkeer patroon** de optie **bewerken**, selecteer de vervolg keuzelijst **frequentie** om **dag**te selecteren en selecteer vervolgens **interval** tot type **7**.  
4. Selecteer het vak **+ nieuwe stap** en selecteer vervolgens **een actie toevoegen**.
5. Typ in de lijst **acties** op **Informix** in het invoervak **Zoek naar meer acties** en selecteer vervolgens **Informix-ophalen tabellen (preview)** .
   
   ![](./media/connectors-create-api-informix/InformixconnectorActions.png)  
6. Schakel in het deel venster configuratie van **Informix-ophalen van tabellen** het **selectie vakje** in om verbinding te maken **via een on-premises gegevens gateway**. U ziet dat de instellingen van de Cloud naar on-premises worden gewijzigd.
   
   * Typ waarde voor **Server**, in de vorm van adres of alias dubbele punt poort nummer. Typ bijvoorbeeld `ibmserver01:9089`.
   * Typ de waarde voor de **Data Base**. Typ bijvoorbeeld `nwind`.
   * Selecteer een waarde voor **authenticatie**. Selecteer bijvoorbeeld **Basic**.
   * Typ de waarde voor de **gebruikers naam**. Typ bijvoorbeeld `informix`.
   * Typ een waarde voor het **wacht woord**. Typ bijvoorbeeld `Password1`.
   * Selecteer een waarde voor de **Gateway**. Selecteer bijvoorbeeld **datagateway01**.
7. Selecteer **maken**en selecteer vervolgens **Opslaan**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)
8. Selecteer in de Blade **InformixgetTables** in de lijst **alle uitvoeringen** onder **samen vatting**het eerste vermelde item (de meest recente uitvoering).
9. Selecteer in de Blade **logische app uitvoeren** de optie **Details uitvoeren**. Selecteer in de lijst **actie** de optie **Get_tables**. Bekijk de waarde voor **status**, die moet worden **geslaagd**. Selecteer de **koppeling invoer** om de invoer weer te geven. Selecteer de **koppeling uitvoer**en Bekijk de uitvoer. Dit moet een lijst met tabellen bevatten.
   
   ![](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>De verbindingen maken
Deze connector ondersteunt verbindingen met data base on-premises en in de Cloud met behulp van de volgende verbindings eigenschappen. 

| Eigenschap | Beschrijving |
| --- | --- |
| server |Vereist. Hiermee wordt een teken reeks waarde geaccepteerd die een TCP/IP-adres of alias vertegenwoordigt, in de IPv4-of IPv6-indeling, gevolgd door een TCP/IP-poort nummer (door komma's gescheiden). |
| database |Vereist. Hiermee wordt een teken reeks waarde geaccepteerd die een DRDA relationele database naam vertegenwoordigt (RDBNAM). Informix accepteert een teken reeks van 128 bytes (Data Base staat bekend als een IBM Informix-database naam (dbname)). |
| verificatie |Optioneel. Hiermee wordt een waarde voor het lijst item geaccepteerd: Basic of Windows (Kerberos). |
| gebruikersnaam |Vereist. Hiermee wordt een teken reeks waarde geaccepteerd. |
| wachtwoord |Vereist. Hiermee wordt een teken reeks waarde geaccepteerd. |
| #B0 |Vereist. Hiermee wordt een waarde van het lijst item geaccepteerd die de on-premises gegevens gateway vertegenwoordigt die is gedefinieerd voor Logic Apps binnen de opslag groep. |

## <a name="create-the-on-premises-gateway-connection"></a>De on-premises gateway verbinding maken
Deze connector heeft toegang tot een on-premises Informix-data base met behulp van de on-premises gegevens gateway. Zie de onderwerpen over gateways voor meer informatie. 

1. Schakel in het configuratie venster **gateways** het **selectie vakje** in om **verbinding via gateway**in te scha kelen. Zie de instellingen wijzigen van Cloud naar on-premises.
2. Typ waarde voor **Server**, in de vorm van adres of alias dubbele punt poort nummer. Typ bijvoorbeeld `ibmserver01:9089`.
3. Typ de waarde voor de **Data Base**. Typ bijvoorbeeld `nwind`.
4. Selecteer een waarde voor **authenticatie**. Selecteer bijvoorbeeld **Basic**.
5. Typ de waarde voor de **gebruikers naam**. Typ bijvoorbeeld `informix`.
6. Typ een waarde voor het **wacht woord**. Typ bijvoorbeeld `Password1`.
7. Selecteer een waarde voor de **Gateway**. Selecteer bijvoorbeeld **datagateway01**.
8. Selecteer **maken** om door te gaan. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>De Cloud verbinding maken
Deze connector kan toegang krijgen tot een Informix-data base in de Cloud. 

1. In het deel venster configuratie van **gateways** , sluit u het **selectie vakje** uitgeschakeld (niet geklikt) **verbinding maken via gateway**. 
2. Typ een waarde voor de **verbindings naam**. Typ bijvoorbeeld `hisdemo2`.
3. Typ een waarde voor de naam van de **Informix-server**, in de vorm van adres of alias dubbele poort nummer. Typ bijvoorbeeld `hisdemo2.cloudapp.net:9089`.
4. Typ een waarde voor de naam van de **Informix-data base**. Typ bijvoorbeeld `nwind`.
5. Typ de waarde voor de **gebruikers naam**. Typ bijvoorbeeld `informix`.
6. Typ een waarde voor het **wacht woord**. Typ bijvoorbeeld `Password1`.
7. Selecteer **maken** om door te gaan. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>Alle rijen ophalen met behulp van selecteren
U kunt een actie van een logische app maken om alle rijen in de tabel Informix op te halen. Met deze actie wordt de connector geïnstrueerd om een Informix SELECT-instructie, zoals `SELECT * FROM AREA`, te verwerken.

### <a name="create-a-logic-app"></a>Een logische app maken
1. Selecteer in het **Start bord van Azure**de optie **+** (plus teken), **Web en mobiel**en vervolgens **logische app**.
2. Voer de **naam** in (bijvoorbeeld '**InformixgetRows**'), **abonnement**, **resource groep**, **locatie**en **app service plan**. Selecteer vastmaken **aan dash board**en selecteer vervolgens **maken**.

### <a name="add-a-trigger-and-action"></a>Een trigger en actie toevoegen
1. Selecteer in de **Logic apps Designer** **lege LogicApp** in de lijst **sjablonen** .
2. Selecteer in de lijst **Triggers** de optie **terugkeer patroon**. 
3. Selecteer in de trigger voor **terugkeer patroon** de optie **bewerken**, selecteer de vervolg keuzelijst **frequentie** om **dag**te selecteren en selecteer vervolgens **interval** tot type **7**. 
4. Selecteer het vak **+ nieuwe stap** en selecteer vervolgens **een actie toevoegen**.
5. Typ in de lijst **acties** het type **Informix** in het invoervak **Zoek naar meer acties** en selecteer vervolgens **Informix-rijen ophalen (preview)** .
6. Selecteer in de actie **rijen ophalen (voor beeld)** de optie **verbinding wijzigen**.
7. Selecteer in het deel venster **verbindingen** configuratie de optie **nieuwe maken**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorNewConnection.png)
8. In het deel venster configuratie van **gateways** , sluit u het **selectie vakje** uitgeschakeld (niet geklikt) **verbinding maken via gateway**.
   
   * Typ een waarde voor de **verbindings naam**. Typ bijvoorbeeld `HISDEMO2`.
   * Typ een waarde voor de naam van de **Informix-server**, in de vorm van adres of alias dubbele poort nummer. Typ bijvoorbeeld `HISDEMO2.cloudapp.net:9089`.
   * Typ een waarde voor de naam van de **Informix-data base**. Typ bijvoorbeeld `NWIND`.
   * Typ de waarde voor de **gebruikers naam**. Typ bijvoorbeeld `informix`.
   * Typ een waarde voor het **wacht woord**. Typ bijvoorbeeld `Password1`.
9. Selecteer **maken** om door te gaan.
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)
10. Selecteer in de lijst **tabel naam** de **pijl-omlaag**en selecteer vervolgens **gebied**.
11. Selecteer eventueel **Geavanceerde opties weer geven** om query opties op te geven.
12. Selecteer **Opslaan**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsTableName.png)
13. Selecteer in de Blade **InformixgetRows** in de lijst **alle uitvoeringen** onder **samen vatting**het eerste vermelde item (de meest recente uitvoering).
14. Selecteer in de Blade **logische app uitvoeren** de optie **Details uitvoeren**. Selecteer in de lijst **actie** de optie **Get_rows**. Bekijk de waarde voor **status**, die moet worden **geslaagd**. Selecteer de **koppeling invoer** om de invoer weer te geven. Selecteer de **koppeling uitvoer**en Bekijk de uitvoer. Dit moet een lijst met rijen bevatten.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>Eén rij toevoegen met INSERT
U kunt een actie van een logische app maken om één rij in een Informix-tabel toe te voegen. Met deze actie wordt de connector geïnstrueerd om een Informix INSERT-instructie, zoals `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`, te verwerken.

### <a name="create-a-logic-app"></a>Een logische app maken
1. Selecteer in het **Start bord van Azure**de optie **+** (plus teken), **Web en mobiel**en vervolgens **logische app**.
2. Voer de **naam**in, zoals `InformixinsertRow`, **abonnement**, **Resource groep**, **locatie**en **app service plan**. Selecteer vastmaken **aan dash board**en selecteer vervolgens **maken**.

### <a name="add-a-trigger-and-action"></a>Een trigger en actie toevoegen
1. Selecteer in de **Logic apps Designer** **lege LogicApp** in de lijst **sjablonen** .
2. Selecteer in de lijst **Triggers** de optie **terugkeer patroon**. 
3. Selecteer in de trigger voor **terugkeer patroon** de optie **bewerken**, selecteer de vervolg keuzelijst **frequentie** om **dag**te selecteren en selecteer vervolgens **interval** tot type **7**. 
4. Selecteer het vak **+ nieuwe stap** en selecteer vervolgens **een actie toevoegen**.
5. Typ in de lijst **acties** op **Informix** in het invoervak **Zoek naar meer acties** en selecteer vervolgens **Informix-invoegrij (preview)** .
6. Selecteer in de actie **rijen ophalen (voor beeld)** de optie **verbinding wijzigen**. 
7. Selecteer in het deel venster **verbindingen** configuratie de optie om een verbinding te selecteren. Selecteer bijvoorbeeld **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Selecteer in de lijst **tabel naam** de **pijl-omlaag**en selecteer vervolgens **gebied**.
9. Voer waarden in voor alle vereiste kolommen (zie rood sterretje). Typ bijvoorbeeld `99999` voor **AREAID**, typ `Area 99999`en typ `102` voor **REGIONID**. 
10. Selecteer **Opslaan**.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowValues.png)
11. Selecteer in de Blade **InformixinsertRow** in de lijst **alle uitvoeringen** onder **samen vatting**het eerste vermelde item (de meest recente uitvoering).
12. Selecteer in de Blade **logische app uitvoeren** de optie **Details uitvoeren**. Selecteer in de lijst **actie** de optie **Get_rows**. Bekijk de waarde voor **status**, die moet worden **geslaagd**. Selecteer de **koppeling invoer** om de invoer weer te geven. Selecteer de **koppeling uitvoer**en Bekijk de uitvoer. Dit moet de nieuwe rij bevatten.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>Eén rij ophalen met selecteren
U kunt een actie van een logische app maken om één rij in een Informix-tabel op te halen. Met deze actie wordt de connector geïnstrueerd om een Informix SELECT WHERE-instructie te verwerken, zoals `SELECT FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Een logische app maken
1. Selecteer in het **Start bord van Azure**de optie **+** (plus teken), **Web en mobiel**en vervolgens **logische app**.
2. Voer de **naam**in, zoals `InformixgetRow`, **abonnement**, **Resource groep**, **locatie**en **app service plan**. Selecteer vastmaken **aan dash board**en selecteer vervolgens **maken**.

### <a name="add-a-trigger-and-action"></a>Een trigger en actie toevoegen
1. Selecteer in de **Logic apps Designer** **lege LogicApp** in de lijst **sjablonen** .
2. Selecteer in de lijst **Triggers** de optie **terugkeer patroon**. 
3. Selecteer in de trigger voor **terugkeer patroon** de optie **bewerken**, selecteer de vervolg keuzelijst **frequentie** om **dag**te selecteren en selecteer vervolgens **interval** tot type **7**. 
4. Selecteer het vak **+ nieuwe stap** en selecteer vervolgens **een actie toevoegen**.
5. Typ in de lijst **acties** het type **Informix** in het invoervak **Zoek naar meer acties** en selecteer vervolgens **Informix-rijen ophalen (preview)** .
6. Selecteer in de actie **rijen ophalen (voor beeld)** de optie **verbinding wijzigen**. 
7. Selecteer in het deel venster configuraties voor **verbindingen** een bestaande verbinding selecteren. Selecteer bijvoorbeeld **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Selecteer in de lijst **tabel naam** de **pijl-omlaag**en selecteer vervolgens **gebied**.
9. Voer waarden in voor alle vereiste kolommen (zie rood sterretje). Typ bijvoorbeeld `99999` voor **AREAID**. 
10. Selecteer eventueel **Geavanceerde opties weer geven** om query opties op te geven.
11. Selecteer **Opslaan**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowValues.png)
12. Selecteer in de Blade **InformixgetRow** in de lijst **alle uitvoeringen** onder **samen vatting**het eerste vermelde item (de meest recente uitvoering).
13. Selecteer in de Blade **logische app uitvoeren** de optie **Details uitvoeren**. Selecteer in de lijst **actie** de optie **Get_rows**. Bekijk de waarde voor **status**, die moet worden **geslaagd**. Selecteer de **koppeling invoer** om de invoer weer te geven. Selecteer de **koppeling uitvoer**en Bekijk de uitvoer. Dit moet een rij bevatten.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>Eén rij wijzigen met UPDATE
U kunt een actie van een logische app maken om één rij in een Informix-tabel te wijzigen. Met deze actie wordt de connector geïnstrueerd om een Informix-UPDATE-instructie, zoals `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`, te verwerken.

### <a name="create-a-logic-app"></a>Een logische app maken
1. Selecteer in het **Start bord van Azure**de optie **+** (plus teken), **Web en mobiel**en vervolgens **logische app**.
2. Voer de **naam**in, zoals `InformixupdateRow`, **abonnement**, **Resource groep**, **locatie**en **app service plan**. Selecteer vastmaken **aan dash board**en selecteer vervolgens **maken**.

### <a name="add-a-trigger-and-action"></a>Een trigger en actie toevoegen
1. Selecteer in de **Logic apps Designer** **lege LogicApp** in de lijst **sjablonen** .
2. Selecteer in de lijst **Triggers** de optie **terugkeer patroon**. 
3. Selecteer in de trigger voor **terugkeer patroon** de optie **bewerken**, selecteer de vervolg keuzelijst **frequentie** om **dag**te selecteren en selecteer vervolgens **interval** tot type **7**. 
4. Selecteer het vak **+ nieuwe stap** en selecteer vervolgens **een actie toevoegen**.
5. Typ in de lijst **acties** het type **Informix** in het invoervak **Zoek naar meer acties** en selecteer vervolgens **Informix-update Row (preview)** .
6. Selecteer in de actie **rijen ophalen (voor beeld)** de optie **verbinding wijzigen**. 
7. Selecteer in het deel venster configuraties voor **verbindingen** een bestaande verbinding selecteren. Selecteer bijvoorbeeld **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Selecteer in de lijst **tabel naam** de **pijl-omlaag**en selecteer vervolgens **gebied**.
9. Voer waarden in voor alle vereiste kolommen (zie rood sterretje). Typ bijvoorbeeld `99999` voor **AREAID**, typ `Updated 99999`en typ `102` voor **REGIONID**. 
10. Selecteer **Opslaan**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowValues.png)
11. Selecteer in de Blade **InformixupdateRow** in de lijst **alle uitvoeringen** onder **samen vatting**het eerste vermelde item (de meest recente uitvoering).
12. Selecteer in de Blade **logische app uitvoeren** de optie **Details uitvoeren**. Selecteer in de lijst **actie** de optie **Get_rows**. Bekijk de waarde voor **status**, die moet worden **geslaagd**. Selecteer de **koppeling invoer** om de invoer weer te geven. Selecteer de **koppeling uitvoer**en Bekijk de uitvoer. Dit moet de nieuwe rij bevatten.
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>Eén rij verwijderen met behulp van verwijderen
U kunt een actie van een logische app maken om één rij in een Informix-tabel te verwijderen. Met deze actie wordt de connector geïnstrueerd om een Informix DELETE-instructie, zoals `DELETE FROM AREA WHERE AREAID = '99999'`, te verwerken.

### <a name="create-a-logic-app"></a>Een logische app maken
1. Selecteer in het **Start bord van Azure**de optie **+** (plus teken), **Web en mobiel**en vervolgens **logische app**.
2. Voer de **naam**in, zoals `InformixdeleteRow`, **abonnement**, **Resource groep**, **locatie**en **app service plan**. Selecteer vastmaken **aan dash board**en selecteer vervolgens **maken**.

### <a name="add-a-trigger-and-action"></a>Een trigger en actie toevoegen
1. Selecteer in de **Logic apps Designer** **lege LogicApp** in de lijst **sjablonen** .
2. Selecteer in de lijst **Triggers** de optie **terugkeer patroon**. 
3. Selecteer in de trigger voor **terugkeer patroon** de optie **bewerken**, selecteer de vervolg keuzelijst **frequentie** om **dag**te selecteren en selecteer vervolgens **interval** tot type **7**. 
4. Selecteer het vak **+ nieuwe stap** en selecteer vervolgens **een actie toevoegen**.
5. Typ in de lijst **acties** op **Informix** in het invoervak **Zoek naar meer acties** en selecteer vervolgens **Informix-verwijderen (preview)** .
6. Selecteer in de actie **rijen ophalen (voor beeld)** de optie **verbinding wijzigen**. 
7. Selecteer een bestaande verbinding in het deel venster configuraties voor **verbindingen** . Selecteer bijvoorbeeld **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Selecteer in de lijst **tabel naam** de **pijl-omlaag**en selecteer vervolgens **gebied**.
9. Voer waarden in voor alle vereiste kolommen (zie rood sterretje). Typ bijvoorbeeld `99999` voor **AREAID**. 
10. Selecteer **Opslaan**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowValues.png)
11. Selecteer in de Blade **InformixdeleteRow** in de lijst **alle uitvoeringen** onder **samen vatting**het eerste vermelde item (de meest recente uitvoering).
12. Selecteer in de Blade **logische app uitvoeren** de optie **Details uitvoeren**. Selecteer in de lijst **actie** de optie **Get_rows**. Bekijk de waarde voor **status**, die moet worden **geslaagd**. Selecteer de **koppeling invoer** om de invoer weer te geven. Selecteer de **koppeling uitvoer**en Bekijk de uitvoer. Dit moet de verwijderde rij bevatten.
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="supported-informix-platforms-and-versions"></a>Ondersteunde Informix-platforms en-versies
Deze connector ondersteunt de volgende IBM Informix-versies, wanneer deze zijn geconfigureerd voor de ondersteuning van gedistribueerde DRDA-client verbindingen (Relational Data Base Architecture).

* IBM Informix 12,1
* IBM Informix 11,7

## <a name="connector-specific-details"></a>Connector-specifieke Details

Bekijk de triggers en acties die zijn gedefinieerd in Swagger en Zie ook eventuele limieten in de details van de [connector](/connectors/informix/). 

## <a name="next-steps"></a>Volgende stappen
[Maak een logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Verken de andere beschik bare connectors in Logic Apps in onze [api's-lijst](apis-list.md).

