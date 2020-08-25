---
title: Azure SQL Edge (preview) implementeren met behulp van de Azure Portal
description: Meer informatie over het implementeren van Azure SQL Edge (preview) met behulp van de Azure Portal
keywords: SQL-rand implementeren
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 7af4264860f8d9950515cd5302f03822e7cbac39
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816861"
---
# <a name="deploy-azure-sql-edge-preview"></a>Azure SQL Edge implementeren (preview-versie) 

Azure SQL Edge (preview) is een relationele data base-engine die is geoptimaliseerd voor IoT-en Azure IoT Edge-implementaties. Het biedt mogelijkheden voor het maken van een hoogwaardige gegevens opslag en verwerkings laag voor IoT-toepassingen en-oplossingen. In deze Quick start ziet u hoe u aan de slag gaat met het maken van een Azure SQL Edge-module via Azure IoT Edge met behulp van de Azure Portal.

## <a name="before-you-begin"></a>Voordat u begint

* Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) aan.
* Meld u aan bij de [Azure-portal](https://portal.azure.com/).
* Een [Azure-IOT hub](../iot-hub/iot-hub-create-through-portal.md)maken.
* Registreer een [IOT edge apparaat vanuit de Azure Portal](../iot-edge/how-to-register-device-portal.md).
* Bereid het IoT Edge apparaat voor om [IOT Edge module te implementeren vanuit de Azure Portal](../iot-edge/how-to-deploy-modules-portal.md).

> [!NOTE]
> Zie deze [Snelstartgids](../iot-edge/quickstart-linux.md)voor informatie over het implementeren van een Azure Linux-VM als IOT edge apparaat.

## <a name="deploy-sql-edge-module-from-azure-marketplace"></a>SQL Edge-module implementeren vanuit Azure Marketplace

Azure Marketplace is een online Marketplace voor toepassingen en services waar u kunt bladeren door een breed scala aan bedrijfs toepassingen en oplossingen die zijn gecertificeerd en geoptimaliseerd om te worden uitgevoerd op Azure, met inbegrip van [IOT Edge-modules](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Azure SQL Edge kan via Marketplace worden geïmplementeerd op een edge-apparaat.

1. Zoek de Azure SQL Edge-module op de Azure Marketplace.<br><br>

   ![SQL-Edge in MarketPlace](media/deploy-portal/find-offer-marketplace.png)

2. Kies het software abonnement dat het beste voldoet aan uw vereisten en klik op **maken**. <br><br>

   ![Het juiste software abonnement kiezen](media/deploy-portal/pick-correct-plan.png)

3. Geef op de pagina doel apparaten voor IoT Edge module de volgende details op en klik vervolgens op **maken**

   |**Veld**  |**Beschrijving**  |
   |---------|---------|
   |Abonnement  |  Het Azure-abonnement waarmee de IoT Hub is gemaakt |
   |IoT Hub   |  De naam van de IoT Hub waarbij het IoT Edge-apparaat is geregistreerd en selecteer vervolgens de optie op een apparaat implementeren|
   |Naam van IoT Edge apparaat  |  De naam van het IoT Edge apparaat waarop de SQL-rand moet worden geïmplementeerd |

4. Ga op de pagina **modules instellen** naar de sectie over implementatie modules en klik op **configureren** voor de SQL Edge-module. 

5. Geef in het deel venster **aangepaste Modules IOT Edge** de gewenste waarden voor de omgevings variabelen op en/of pas de opties voor maken en de gewenste eigenschappen voor de module aan. Zie [SQL Server container omgevings variabelen](/sql/linux/sql-server-linux-configure-environment-variables/)voor een volledige lijst met ondersteunde omgevings variabelen.

   |**Parameter**  |**Beschrijving**|
   |---------|---------|
   | Naam | Naam voor de module. |
   |SA_PASSWORD  | Geef een sterk wacht woord op voor het SQL Edge-beheerders account. |
   |MSSQL_LCID   | Hiermee stelt u de taal-ID in die moet worden gebruikt voor SQL Server. Bijvoorbeeld, 1036 is Frans. |
   |MSSQL_COLLATION | Hiermee stelt u de standaard sortering voor SQL Server. Deze instelling overschrijft de standaard toewijzing van taal-ID (LCID) in sortering. |

   > [!NOTE]
   > Wijzig of werk de **afbeeldings-URI** of de **ACCEPT_EULA** -instellingen niet bij in de module.

6. Werk in het deel venster **aangepaste Modules IOT Edge** de container maken opties voor de gewenste waarde voor de poort van de **host**bij. Als u meer dan één SQL DB-rand module moet implementeren, moet u ervoor zorgen dat u de koppelings optie bijwerkt om een nieuw bron & doel voor het permanente volume te maken. Zie [volumes gebruiken](https://docs.docker.com/storage/volumes/) in docker-documentatie voor meer informatie over koppels en volumes. 

   ```json
       {
         "HostConfig": {
           "Binds": [
             "sqlvolume:/sqlvolume"
           ],
           "PortBindings": {
             "1433/tcp": [
               {
                 "HostPort": "1433"
               }
             ]
           },
           "Mounts": [
             {
               "Type": "volume",
               "Source": "sqlvolume",
               "Target": "/var/opt/mssql"
             }
           ]
         },
         "Env": [
           "MSSQL_AGENT_ENABLED=TRUE",
           "MSSQL_PID=Developer"
         ]
       }
   ```

7. Werk in het deel venster **aangepaste Modules IOT Edge** de *gewenste eigenschappen van de set-module configureren* om de locatie van het SQL-pakket en de gegevens van de stream Analytics-taak op te laten bevatten. Deze twee velden zijn optioneel en moeten worden gebruikt als u de module SQL Edge wilt implementeren met een Data Base en een streaming-taak.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

8. Stel in het deel venster **aangepaste Modules IOT Edge** het beleid voor het *opnieuw opstarten* in op altijd en de *gewenste status* om uit te voeren.
9. Klik in het deel venster **aangepaste Modules IOT Edge** op **Opslaan**.
10. Klik op de pagina **modules instellen** op **volgende**.
11. Geef op de pagina **route opgeven (optioneel)** van de **set-modules** de routes voor module naar module of module op IOT Edge hub-communicatie, Zie [modules implementeren en routes instellen in IOT Edge](../iot-edge/module-composition.md).
12. Klik op **Volgende**.
13. Klik op **Submit**

## <a name="connect-to-azure-sql-edge"></a>Verbinding maken met Azure SQL Edge

In de volgende stappen wordt het opdracht regel programma voor Azure SQL Edge, **Sqlcmd**, in de container gebruikt om verbinding te maken met Azure SQL Edge.

> [!NOTE]
> Sqlcmd-hulp programma is niet beschikbaar in de ARM64-versie van SQL Edge-containers.

1. Gebruik de `docker exec -it` opdracht om een interactieve bash-shell in de container die wordt uitgevoerd te starten. In het volgende voor beeld `azuresqledge` is de naam die is opgegeven door de `Name` para meter van uw IOT Edge-module.

   ```bash
   sudo docker exec -it azuresqledge "bash"
   ```

2. In de container kunt u lokaal verbinding maken met Sqlcmd. Sqlcmd bevindt zich standaard niet in het pad, dus u moet het volledige pad opgeven.

   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "<YourNewStrong@Passw0rd>"
   ```

   > [!TIP]
   > U kunt het wacht woord op de opdracht regel weglaten om het in te voeren.

3. Als dit is gelukt, krijgt u een **Sqlcmd** -opdracht prompt: `1>` .

## <a name="create-and-query-data"></a>Gegevens maken en er query's op uitvoeren

In de volgende secties wordt uitgelegd hoe u **Sqlcmd** en Transact-SQL kunt gebruiken om een nieuwe Data Base te maken, gegevens toe te voegen en een eenvoudige query uit te voeren.

### <a name="create-a-new-database"></a>Een nieuwe database maken

Met de volgende stappen maakt u een nieuwe Data Base met de naam `TestDB` .

1. Plak de volgende Transact-SQL-opdracht in de **Sqlcmd** -opdracht prompt om een test database te maken:

   ```sql
   CREATE DATABASE TestDB
   Go
   ```

2. Op de volgende regel schrijft u een query om de naam van alle data bases op uw server te retour neren:

   ```sql
   SELECT Name from sys.Databases
   Go
   ```

### <a name="insert-data"></a>Gegevens invoegen

Maak vervolgens een nieuwe tabel `Inventory` en Voeg twee nieuwe rijen in.

1. Ga vanaf de **Sqlcmd** -opdracht prompt naar de nieuwe `TestDB` Data Base:

   ```sql
   USE TestDB
   ```

2. Nieuwe tabel maken met de naam `Inventory` :

   ```sql
   CREATE TABLE Inventory (id INT, name NVARCHAR(50), quantity INT)
   ```

3. Gegevens invoegen in de nieuwe tabel:

   ```sql
   INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (2, 'orange', 154);
   ```

4. Typ `GO` om de vorige opdrachten uit te voeren:

   ```sql
   GO
   ```

### <a name="select-data"></a>Gegevens selecteren

Voer nu een query uit om gegevens uit de tabel te retour neren `Inventory` .

1. Voer vanaf de **Sqlcmd** -opdracht prompt een query in die rijen uit de `Inventory` tabel retourneert waarin de hoeveelheid groter is dan 152:

   ```sql
   SELECT * FROM Inventory WHERE quantity > 152;
   ```

2. Voer de volgende opdracht uit:

   ```sql
   GO
   ```

### <a name="exit-the-sqlcmd-command-prompt"></a>Sluit de Sqlcmd-opdracht prompt

1. Als u uw **Sqlcmd** -sessie wilt beëindigen, typt u `QUIT` :

   ```sql
   QUIT
   ```

2. Als u de interactieve opdracht prompt in uw container wilt afsluiten, typt u `exit` . Uw container blijft actief nadat u de interactieve bash-shell hebt afgesloten.

## <a name="connect-from-outside-the-container"></a>Verbinding maken van buiten de container

U kunt vanuit elk extern Linux-, Windows-of macOS-hulp programma verbinding maken met SQL-query's en deze uitvoeren op een exemplaar van de Azure SQL-rand die SQL-verbindingen ondersteunt. Voor meer informatie over het maken van een verbinding met een SQL Edge-container van buiten, raadpleegt u [verbinding maken en Query's uitvoeren op Azure SQL Edge](https://docs.microsoft.com/azure/azure-sql-edge/connect).

In deze Quick Start hebt u een SQL Edge-module geïmplementeerd op een IoT Edge apparaat. 

## <a name="next-steps"></a>Volgende stappen

- [Machine learning en kunst matige intelligentie met ONNX in SQL Edge](onnx-overview.md).
- [Bouw een end-to-end IOT-oplossing met SQL Edge met behulp van IOT Edge](tutorial-deploy-azure-resources.md).
- [Gegevens stromen in Azure SQL Edge](stream-data.md)
