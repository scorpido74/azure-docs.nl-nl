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
ms.openlocfilehash: 43359b66ba747dba7b3294d022a2c1aa2a3e624c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84233243"
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

In deze Quick Start hebt u een SQL Edge-module geïmplementeerd op een IoT Edge apparaat.

## <a name="next-steps"></a>Volgende stappen

- [Machine learning en kunst matige intelligentie met ONNX in SQL Edge](onnx-overview.md).
- [Bouw een end-to-end IOT-oplossing met SQL Edge met behulp van IOT Edge](tutorial-deploy-azure-resources.md).