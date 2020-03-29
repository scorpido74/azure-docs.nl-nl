---
title: Verbinding maken met Oracle Database
description: Records invoegen en beheren met Oracle Database REST API's en Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/29/2017
tags: connectors
ms.openlocfilehash: 99abd48bde97c2a2e085688cdfbb365e5e4cfd56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789423"
---
# <a name="get-started-with-the-oracle-database-connector"></a>Aan de slag met de Oracle Database-connector

Met de Oracle Database-connector maakt u organisatorische workflows die gegevens gebruiken in uw bestaande database. Deze connector kan verbinding maken met een on-premises Oracle Database of een virtuele Azure-machine met Oracle Database geïnstalleerd. Met deze connector u:

* Bouw uw werkstroom op door een nieuwe klant toe te voegen aan een klantendatabase of een order bij te werken in een orderdatabase.
* Gebruik acties om een rij met gegevens op te halen, een nieuwe rij in te voegen en zelfs te verwijderen. Wanneer bijvoorbeeld een record wordt gemaakt in Dynamics CRM Online (een trigger), voegt u een rij in een Oracle Database (een actie). 

In dit artikel ziet u hoe u de Oracle Database-connector gebruiken in een logische app.

## <a name="prerequisites"></a>Vereisten

* Ondersteunde Oracle-versies: 
    * Oracle 9 en hoger
    * Oracle-clientsoftware 8.1.7 en hoger

* Installeer de on-premises datagateway. [Maak verbinding met on-premises gegevens uit logische apps](../logic-apps/logic-apps-gateway-connection.md) bevat de stappen. De gateway is vereist om verbinding te maken met een on-premises Oracle Database of een Azure VM met Oracle DB geïnstalleerd. 

    > [!NOTE]
    > De on-premises datagateway fungeert als een brug en biedt een veilige gegevensoverdracht tussen on-premises gegevens (gegevens die zich niet in de cloud bevinden) en uw logische apps. Dezelfde gateway kan worden gebruikt met meerdere services en meerdere gegevensbronnen.Het kan dus dat u de gateway maar één keer hoeft te installeren.

* Installeer de Oracle Client op de machine waar u de on-premises datagateway hebt geïnstalleerd.Zorg ervoor dat u de 64-bits Oracle Data Provider voor .NET van Oracle installeert:  

  [64-bits ODAC 12c release 4 (12.1.0.2.4) voor Windows x64](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Als de Oracle-client niet is geïnstalleerd, treedt er een fout op wanneer u de verbinding probeert te maken of te gebruiken. Zie de veelvoorkomende fouten in dit artikel.


## <a name="add-the-connector"></a>De connector toevoegen

> [!IMPORTANT]
> Deze connector heeft geen triggers. Het heeft alleen maar acties. Dus wanneer u uw logische app maakt, voegt u een andere trigger toe om uw logische app te starten, zoals **Plannen - Herhaling**of Verzoek / Antwoord - **Reactie**. 

1. Maak in de [Azure-portal](https://portal.azure.com)een lege logische app.

2. Selecteer aan het begin van uw logische app de trigger **Verzoek / Antwoord - Verzoek:** 

    ![](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. Selecteer **Opslaan**. Wanneer u opslaat, wordt een aanvraag-URL automatisch gegenereerd. 

4. Selecteer **Nieuwe stap** en selecteer **Een actie toevoegen**. Typ `oracle` in om de beschikbare acties te bekijken: 

    ![](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > Dit is ook de snelste manier om de triggers en acties te zien die beschikbaar zijn voor elke connector. Typ een deel van de `oracle`connectornaam, zoals . De ontwerper geeft een lijst van alle triggers en acties. 

5. Selecteer een van de acties, zoals **Oracle Database - Rij opvragen**. Selecteer **Verbinding maken via on-premises datagateway.** Voer de naam van de Oracle-server, verificatiemethode, gebruikersnaam, wachtwoord en selecteer de gateway:

    ![](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. Nadat u verbinding hebt gemaakt, selecteert u een tabel in de lijst en voert u de rij-id in uw tabel in. U moet de id van de tabel kennen. Als u het niet weet, neemt u contact op `select * from yourTableName`met uw Oracle DB-beheerder en krijgt u de uitvoer van . Dit geeft u de identificeerbare informatie die u nodig hebt om verder te gaan.

    In het volgende voorbeeld worden taakgegevens geretourneerd uit een Human Resources-database: 

    ![](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. In deze volgende stap u een van de andere connectors gebruiken om uw workflow te bouwen. Als u wilt testen of u gegevens van Oracle wilt ophalen, stuurt u uzelf een e-mail met de Oracle-gegevens met behulp van een van de e-mailconnectors verzenden, zoals Office 365 of Gmail. Gebruik de dynamische tokens uit de `Subject` `Body` Oracle-tabel om het en van uw e-mail te bouwen:

    ![](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **Sla** uw logische app op en selecteer **Uitvoeren**. Sluit de ontwerper en bekijk de runsgeschiedenis voor de status. Als dit niet lukt, selecteert u de mislukte berichtenrij. De ontwerper opent en toont u welke stap is mislukt, en toont ook de foutinformatie. Als het lukt, dan moet u een e-mail ontvangen met de informatie die u hebt toegevoegd.


### <a name="workflow-ideas"></a>Werkstroomideeën

* U wilt de #oracle hashtag controleren en de tweets in een database plaatsen, zodat ze kunnen worden opgevraagd en gebruikt in andere toepassingen. Voeg in een logische `Twitter - When a new tweet is posted` app de trigger toe en voer de **hashtag #oracle** in. Voeg vervolgens `Oracle Database - Insert row` de actie toe en selecteer de tabel:

    ![](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* Berichten worden verzonden naar een wachtrij voor servicebussen. U wilt deze berichten te krijgen, en zet ze in een database. Voeg in een logische `Service Bus - when a message is received in a queue` app de trigger toe en selecteer de wachtrij. Voeg vervolgens `Oracle Database - Insert row` de actie toe en selecteer de tabel:

    ![](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>Algemene fouten

#### <a name="error-cannot-reach-the-gateway"></a>**Fout:** kan de gateway niet bereiken

**Oorzaak:** de on-premises datagateway kan geen verbinding maken met de cloud. 

**Mitigatie:** zorg ervoor dat uw gateway wordt uitgevoerd op de on-premises machine waar u deze hebt geïnstalleerd en dat deze verbinding kan maken met internet.We raden u aan de gateway niet te installeren op een computer die kan worden uitgeschakeld of in de slaapstand kan staan.U ook de on-premises datagatewayservice (PBIEgwService) opnieuw starten.

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-see-httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**Fout**: De gebruikte provider is afgeschaft: 'System.Data.OracleClient vereist Oracle-clientsoftwareversie 8.1.7 of hoger.'. Zie [https://go.microsoft.com/fwlink/p/?LinkID=272376](https://go.microsoft.com/fwlink/p/?LinkID=272376) om de officiële provider te installeren.

**Oorzaak:** de Oracle-client SDK is niet geïnstalleerd op de machine waar de on-premises datagateway wordt uitgevoerd.  

**Oplossing:** Download en installeer de Oracle client SDK op dezelfde computer als de on-premises datagateway.

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**Fout**: Tabel '[Tabelnaam]' definieert geen belangrijke kolommen

**Oorzaak**: De tabel heeft geen primaire sleutel.  

**Resolutie:** de Oracle Database-connector vereist dat een tabel met een primaire sleutelkolom wordt gebruikt.

#### <a name="currently-not-supported"></a>Momenteel niet ondersteund

* Weergaven 
* Elke tabel met samengestelde toetsen
* Geneste objecttypen in tabellen
 
## <a name="connector-specific-details"></a>Connector-specifieke details

Bekijk alle triggers en acties die in de branie zijn gedefinieerd en zie ook eventuele limieten in de [connectordetails.](/connectors/oracle/) 

## <a name="get-some-help"></a>Hulp krijgen

Het [Azure Logic Apps-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) is een geweldige plek om vragen te stellen, vragen te beantwoorden en te zien wat andere Logic Apps-gebruikers doen. 

U Logic Apps en connectors helpen verbeteren [https://aka.ms/logicapps-wish](https://aka.ms/logicapps-wish)door te stemmen en uw ideeën in te dienen op. 


## <a name="next-steps"></a>Volgende stappen
[Maak een logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)en verken de beschikbare connectors in Logic Apps in [de LIJST met API's](apis-list.md).
