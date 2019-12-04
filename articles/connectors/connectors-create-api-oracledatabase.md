---
title: Verbinding maken met Oracle Database
description: Records invoegen en beheren met Oracle Database REST-Api's en Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/29/2017
tags: connectors
ms.openlocfilehash: 99abd48bde97c2a2e085688cdfbb365e5e4cfd56
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789423"
---
# <a name="get-started-with-the-oracle-database-connector"></a>Aan de slag met de Oracle Database-Connector

Met behulp van de Oracle Database-connector maakt u organisatie werk stromen die gebruikmaken van de gegevens in uw bestaande data base. Deze connector kan verbinding maken met een on-premises Oracle Database, of een virtuele machine van Azure met Oracle Database geïnstalleerd. Met deze connector kunt u het volgende doen:

* Bouw uw werk stroom door een nieuwe klant toe te voegen aan een klanten database of een bestelling bij te werken in een Data Base voor orders.
* Gebruik acties om een rij gegevens op te halen, een nieuwe rij in te voegen en zelfs te verwijderen. Als er bijvoorbeeld een record wordt gemaakt in Dynamics CRM Online (een trigger), voegt u een rij in een Oracle Database (een actie). 

Dit artikel laat u zien hoe u de Oracle Database-Connector in een logische app kunt gebruiken.

## <a name="prerequisites"></a>Vereisten

* Ondersteunde Oracle-versies: 
    * Oracle 9 en hoger
    * Oracle-client software 8.1.7 en hoger

* De on-premises gegevens gateway installeren. [Als u verbinding maakt met on-premises gegevens uit Logic apps](../logic-apps/logic-apps-gateway-connection.md) , worden de stappen weer gegeven. De gateway is vereist om verbinding te maken met een on-premises Oracle Database, of een virtuele machine van Azure met Oracle DB geïnstalleerd. 

    > [!NOTE]
    > De on-premises gegevens gateway fungeert als een brug en biedt een beveiligde gegevens overdracht tussen on-premises gegevens (gegevens die zich niet in de Cloud bevindt) en uw Logic apps. Dezelfde gateway kan worden gebruikt met meerdere services en meerdere gegevens bronnen. Het is dus mogelijk dat u de gateway slechts één keer hoeft te installeren.

* Installeer de Oracle-client op de computer waarop u de on-premises gegevens gateway hebt geïnstalleerd. Zorg ervoor dat u de 64-bits Oracle-gegevens provider voor .NET van Oracle installeert:  

  [64-bits ODAC 12c release 4 (12.1.0.2.4) voor Windows x64](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Als de Oracle-client niet is geïnstalleerd, treedt er een fout op wanneer u de verbinding probeert te maken of gebruiken. Zie de veelvoorkomende fouten in dit artikel.


## <a name="add-the-connector"></a>De connector toevoegen

> [!IMPORTANT]
> Deze connector heeft geen triggers. Het heeft alleen acties. Wanneer u uw logische app maakt, voegt u dus een andere trigger toe om uw logische app te starten, zoals **planning-recurrence**of **aanvraag/antwoord-antwoord**. 

1. Maak in de [Azure Portal](https://portal.azure.com)een lege logische app.

2. Selecteer aan het begin van uw logische app de trigger **aanvraag/antwoord-aanvraag** : 

    ![](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. Selecteer **Opslaan**. Wanneer u opslaat, wordt er automatisch een aanvraag-URL gegenereerd. 

4. Selecteer **nieuwe stap**en selecteer **een actie toevoegen**. Typ `oracle` om de beschik bare acties weer te geven: 

    ![](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > Dit is ook de snelste manier om de triggers en acties weer te geven die beschikbaar zijn voor elke connector. Typ een deel van de naam van de connector, zoals `oracle`. In de ontwerp functie worden alle triggers en acties weer gegeven. 

5. Selecteer een van de acties, zoals **Oracle database-rij ophalen**. Selecteer **verbinding via on-premises gegevens gateway**. Voer de naam van de Oracle-Server, de verificatie methode, de gebruikers naam, het wacht woord en selecteer de gateway:

    ![](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. Zodra de verbinding is gemaakt, selecteert u een tabel in de lijst en voert u de rij-ID in voor uw tabel. U moet de id van de tabel weten. Als u dat niet weet, neemt u contact op met uw Oracle DB-beheerder en haalt u de uitvoer op van `select * from yourTableName`. Dit geeft u de informatie die u nodig hebt om door te gaan.

    In het volgende voor beeld worden taak gegevens geretourneerd vanuit een Human Resources-Data Base: 

    ![](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. In deze volgende stap kunt u een van de andere connectors gebruiken om uw werk stroom te bouwen. Als u het ophalen van gegevens van Oracle wilt testen, stuurt u een e-mail met de Oracle-gegevens via een van de e-mail connectors verzenden, zoals Office 365 of Gmail. Gebruik de dynamische tokens uit de Oracle-tabel om de `Subject` en `Body` van uw e-mail adres te maken:

    ![](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **Sla** de logische app op en selecteer vervolgens **uitvoeren**. Sluit de ontwerp functie en Bekijk de uitvoerings geschiedenis voor de status. Als dit mislukt, selecteert u de map met het mislukte bericht. De ontwerp functie wordt geopend en toont u de stap die is mislukt. Daarnaast wordt de fout informatie weer gegeven. Als dit lukt, ontvangt u een e-mail bericht met de informatie die u hebt toegevoegd.


### <a name="workflow-ideas"></a>Werk stroom ideeën

* U wilt de #oracle hashtag bewaken en de tweets in een Data Base plaatsen, zodat deze kunnen worden opgevraagd en in andere toepassingen kan worden gebruikt. Voeg in een logische app de trigger `Twitter - When a new tweet is posted` toe en voer de **#oracle** hashtag in. Voeg vervolgens de `Oracle Database - Insert row` actie toe en selecteer de tabel:

    ![](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* Berichten worden verzonden naar een Service Bus wachtrij. U wilt deze berichten ophalen en in een Data Base opnemen. Voeg in een logische app de trigger `Service Bus - when a message is received in a queue` toe en selecteer de wachtrij. Voeg vervolgens de `Oracle Database - Insert row` actie toe en selecteer de tabel:

    ![](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>Algemene fouten

#### <a name="error-cannot-reach-the-gateway"></a>**Fout**: kan de gateway niet bereiken

**Oorzaak**: de on-premises gegevens gateway kan geen verbinding maken met de Cloud. 

**Risico beperking**: Zorg ervoor dat uw gateway wordt uitgevoerd op de on-premises machine waarop u deze hebt geïnstalleerd en dat deze verbinding kan maken met internet.  U wordt aangeraden de gateway niet te installeren op een computer die kan worden uitgeschakeld of in de slaap stand kan worden gezet. U kunt ook de on-premises gegevens Gateway Service (PBIEgwService) opnieuw starten.

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-see-httpsgomicrosoftcomfwlinkplinkid272376httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**Fout**: de gebruikte provider is afgeschaft: System. data. OracleClient vereist Oracle client software version 8.1.7 of hoger. Zie [https://go.microsoft.com/fwlink/p/?LinkID=272376](https://go.microsoft.com/fwlink/p/?LinkID=272376) om de officiële provider te installeren.

**Oorzaak**: de Oracle-client-SDK is niet geïnstalleerd op de computer waarop de on-premises gegevens gateway wordt uitgevoerd.  

**Oplossing**: down load en installeer de Oracle-client-SDK op dezelfde computer als de on-premises gegevens gateway.

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**Fout**: in tabel [TableName] worden geen sleutel kolommen gedefinieerd

**Oorzaak**: de tabel heeft geen primaire sleutel.  

**Oplossing**: de Oracle Database-Connector vereist dat er een tabel met een primaire sleutel kolom wordt gebruikt.

#### <a name="currently-not-supported"></a>Momenteel niet ondersteund

* Weergaven 
* Een tabel met samengestelde sleutels
* Geneste object typen in tabellen
 
## <a name="connector-specific-details"></a>Connector-specifieke Details

Bekijk de triggers en acties die zijn gedefinieerd in Swagger en Zie ook eventuele limieten in de details van de [connector](/connectors/oracle/). 

## <a name="get-some-help"></a>Hulp krijgen

Het [Azure Logic apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) is een goede plaats om vragen te stellen, vragen te beantwoorden en te zien wat andere Logic Apps gebruikers doen. 

U kunt Logic Apps en connectors verbeteren door te stemmen en uw ideeën op [https://aka.ms/logicapps-wish](https://aka.ms/logicapps-wish)te verzenden. 


## <a name="next-steps"></a>Volgende stappen
[Maak een logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)en verken de beschik bare connectors in Logic apps in de [lijst api's](apis-list.md).
