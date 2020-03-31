---
title: Gegevens uit Azure Data Explorer visualiseren met Sisense
description: In dit artikel leest u hoe u Azure Data Explorer instelt als gegevensbron voor Sisense en de gegevens visualiseert.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: f0840b90e1036c23fa58d94515bfeb035299c07f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66358181"
---
# <a name="visualize-data-from-azure-data-explorer-in-sisense"></a>Gegevens van Azure Data Explorer visualiseren in Sisense

Sisense is een analytics business intelligence platform waarmee u analytics-apps bouwen die zeer interactieve gebruikerservaringen bieden. Met de business intelligence en dashboardrapportagesoftware u in een paar klikken toegang krijgen tot gegevens en deze combineren. U verbinding maken met gestructureerde en ongestructureerde gegevensbronnen, tabellen van meerdere bronnen met minimale scripting en codering verbinden en interactieve webdashboards en rapporten maken. In dit artikel leert u hoe u Azure Data Explorer instelt als gegevensbron voor Sisense en gegevens uit een voorbeeldcluster visualiseert.

## <a name="prerequisites"></a>Vereisten

Je hebt het volgende nodig om dit artikel te voltooien:

* [Sisense-app downloaden en installeren](https://documentation.sisense.com/latest/getting-started/download-install.htm) 

* Maak een cluster en database met de voorbeeldgegevens van StormEvents. Zie [Snelstart: Een Azure Data Explorer-cluster en -database maken](create-cluster-database-portal.md) en [voorbeeldgegevens opnemen in Azure Data Explorer](ingest-sample-data.md)voor meer informatie.

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="connect-to-sisense-dashboards-using-azure-data-explorer-jdbc-connector"></a>Verbinding maken met Sisense-dashboards met Azure Data Explorer JDBC-connector

1. Download en kopieer de nieuwste versies van de volgende jar-bestanden naar *.. \Sisense\DataConnectors\jdbcdrivers\adx* 

    * activering-1.1.jar
    * adal4j-1.6.0.jar
    * commons-codec-1.10.jar
    * commons-collections4-4.1.jar
    * commons-lang3-3.5.jar
    * gson-2.8.0.jar
    * jcip-annotaties-1.0-1.jar
    * json-smart-1.3.1.jar
    * lang-tag-1.4.4.jar
    * post-1.4.7.jar
    * mssql-jdbc-7.2.1.jre8.jar
    * nimbus-jose-jwt-7.0.1.jar
    * oauth2-oidc-sdk-5.24.1.jar
    * slf4j-api-1.7.21.jar
    
1. Open **de Sisense-app.**
1. Selecteer het tabblad **Gegevens** en selecteer **+ElastiCube** om een nieuw ElastiCube-model te maken.
    
    ![ElastiCube selecteren](media/sisense/data-select-elasticube.png)

1. In **Nieuw ElastiCube-model toevoegen,** het ElastiCube-model benoemen en **opslaan**.
   
    ![Nieuw ElastiCube-model toevoegen](media/sisense/add-new-elasticube-model.png)

1. Selecteer **+ Gegevens**.

    ![Knop Gegevens selecteren](media/sisense/select-data.png)

1. Selecteer op het tabblad **Connector selecteren** de optie **Algemene JDBC-connector.**

    ![Jdbc-connector kiezen](media/sisense/select-connector.png)

1. Voer op het tabblad **Verbinding** de volgende velden in voor de **algemene JDBC-connector** en selecteer **Volgende**.

    ![JDBC-connectorinstellingen](media/sisense/jdbc-connector.png)

    |Veld |Beschrijving |
    |---------|---------|
    |Verbindingsreeks     |   `jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword`      |
    |JDBC JARs-map  |    `..\Sisense\DataConnectors\jdbcdrivers\adx`     |
    |Naam van de rijklasse    |   `com.microsoft.sqlserver.jdbc.SQLServerDriver`      |
    |Gebruikersnaam   |    AAD-gebruikersnaam     |
    |Wachtwoord     |   Aad-gebruikerswachtwoord      |

1. Zoek op het tabblad **Gegevens selecteren** zoeken op **Database selecteren** om de relevante database te selecteren waarvoor u machtigingen hebt. Selecteer in dit voorbeeld *test1*.

    ![database selecteren](media/sisense/select-database.png)

1. In het deelvenster *Test* (databasenaam):
    1. Selecteer de tabelnaam om een voorbeeld van de tabel te bekijken en de namen van de tabelkolom te bekijken. U onnodige kolommen verwijderen.
    1. Schakel het selectievakje van de desbetreffende tabel in om die tabel te selecteren. 
    1. Selecteer **Done**.

    ![tabel selecteren](media/sisense/select-table-see-columns.png)    

1. Selecteer **Bouwen** om uw gegevensset te bouwen. 

    * Selecteer **Bouwen** in **Build**het venster Bouwen .

      ![Venster bouwen](media/sisense/build-window.png)

    * Wacht tot het bouwproces is voltooid en selecteer **Vervolgens Geslaagd bouwen**.

      ![Bouwen geslaagd](media/sisense/build-succeeded.png)

## <a name="create-sisense-dashboards"></a>Sisense-dashboards maken

1. Selecteer op het **+**  > tabblad **Analytics** **nieuw dashboard** om dashboards in deze gegevensset te maken.

    ![Nieuw dashboard](media/sisense/new-dashboard.png)

1. Kies een dashboard en selecteer **Maken**. 

    ![Dashboard maken](media/sisense/create-dashboard.png)

1. Selecteer **onder Nieuwe widget**+ Selecteer Gegevens **selecteren** om een nieuwe widget te maken. 

    ![Velden toevoegen aan het dashboard StormEvents](media/sisense/storm-dashboard-add-field.png)  

1. Selecteer **+ Meer gegevens toevoegen** om extra kolommen aan uw grafiek toe te voegen. 

    ![Meer gegevens toevoegen aan grafiek](media/sisense/add-more-data.png)

1. Selecteer **+ Widget** om een andere widget te maken. Sleep widgets om uw dashboard te herschikken.

    ![Storm-dashboard](media/sisense/final-dashboard.png)

U uw gegevens nu verkennen met visuele analyses, extra dashboards bouwen en gegevens omzetten in bruikbare inzichten om een impact op uw bedrijf te maken.

## <a name="next-steps"></a>Volgende stappen

* [Query's schrijven voor Azure Data Explorer](write-queries.md)

