---
title: SQL Database Edge Preview implementeren met de Azure-portal | Microsoft Documenten
description: Meer informatie over het implementeren van Azure SQL Database Edge met de Azure-portal
keywords: sql-databaserand implementeren
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 9da922de38d820864b3f83de80fe64eb3ac792e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246718"
---
# <a name="deploy-azure-sql-database-edge-preview"></a>Azure SQL Database Edge Preview implementeren

Azure SQL Database Edge Preview is een relationele databaseengine die is geoptimaliseerd voor IoT- en Azure IoT Edge-implementaties. Het biedt mogelijkheden om een krachtige gegevensopslag- en verwerkingslaag te maken voor IoT-toepassingen en -oplossingen. In deze snelle start ziet u hoe u aan de slag met het maken van een Azure SQL Database Edge-module via Azure IoT Edge met behulp van de Azure-portal.

## <a name="before-you-begin"></a>Voordat u begint

* Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/)aan.
* Meld u aan bij [Azure Portal](https://portal.azure.com/).
* Dien [hier](https://azure.microsoft.com/services/sql-database-edge/#contact)een aanvraag in om het abonnement in te schakelen voor implementatie van SQL Database Edge.
* Maak een [Azure IoT Hub](../iot-hub/iot-hub-create-through-portal.md).
* Registreer een [IoT Edge-apparaat via de Azure-portal.](../iot-edge/how-to-register-device-portal.md)
* Bereid het IoT Edge-apparaat [voor om de IoT Edge-module vanuit de Azure-portal](../iot-edge/how-to-deploy-modules-portal.md)te implementeren.

> [!NOTE]
> Als u een Azure Linux VM als Een IoT Edge-apparaat wilt implementeren, raadpleegt u deze [quickstart-handleiding](../iot-edge/quickstart-linux.md).

## <a name="deploy-sql-database-edge-module-from-azure-marketplace"></a>SQL Database Edge-module implementeren vanuit Azure Marketplace

Azure Marketplace is een online marktplaats voor toepassingen en services waar u bladeren door een breed scala aan bedrijfstoepassingen en -oplossingen die zijn gecertificeerd en geoptimaliseerd om op Azure te worden uitgevoerd, inclusief [IoT Edge-modules.](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) Azure SQL Database Edge kan via de marktplaats worden geïmplementeerd op een edge-apparaat.

1. Zoek de Azure SQL Database Edge-module op de Azure Marketplace.<br><br>

   ![SQL Database Edge in MarketPlace](media/deploy-portal/find-offer-marketplace.png)

2. Kies het softwareplan dat het beste bij uw vereisten past en klik op **Maken**. <br><br>

   ![Kies het juiste softwareplan](media/deploy-portal/pick-correct-plan.png)

3. Geef op de pagina Doelapparaten voor IoT Edge-module de volgende details op en klik op **Maken**

   |**Veld**  |**Beschrijving**  |
   |---------|---------|
   |Abonnement  |  Het Azure-abonnement waaronder de IoT Hub is gemaakt |
   |IoT Hub   |  Naam van de IoT-hub waar het IoT Edge-apparaat is geregistreerd en selecteer vervolgens de optie 'Implementeren naar een apparaat'|
   |Naam IoT Edge-apparaat  |  Naam van het IoT Edge-apparaat waar SQL Database Edge zou worden geïmplementeerd |

4. Navigeer op de pagina **Modules instellen** naar de sectie over implementatiemodules en klik op Configureren ten opzichte **van** de SQL Database Edge-module. 

5. Geef in het deelvenster **Aangepaste IoT Edge-modules** de gewenste waarden op voor de omgevingsvariabelen en/of pas de maakopties en gewenste eigenschappen voor de module aan. Voor een volledige lijst met ondersteunde omgevingsvariabelen verwijzen [SQL Server Container Environment Variables](/sql/linux/sql-server-linux-configure-environment-variables/).

   |**Parameter**  |**Beschrijving**|
   |---------|---------|
   | Name | Naam voor de module. |
   |SA_PASSWORD  | Geef een sterk wachtwoord op voor het SQL Database Edge-beheerdersaccount. |
   |MSSQL_LCID   | Hiermee stelt u de taal-id in die voor SQL Server moet worden gebruikt. 1036 is bijvoorbeeld Frans. |
   |MSSQL_COLLATION | Hiermee stelt u de standaardcollatie voor SQL Server in. Met deze instelling wordt de standaardtoewijzing van taal-ID (LCID) overschreven aan collatie. |

   > [!NOTE]
   > Wijzig of werk de **Image URI** of de **ACCEPT_EULA-instellingen** in de module niet aan.

6. Werk in het deelvenster **Aangepaste modules van IoT Edge** de gewenste waarde voor de gewenste container voor de **hostpoort**bij. Als u meer dan één SQL DB Edge-module wilt implementeren, moet u de optie mounts bijwerken om een nieuwe bron te maken & doelpaar voor het aanhoudende volume. Voor meer informatie over mounts en volume verwijzen [we: Volumes](https://docs.docker.com/storage/volumes/) gebruiken op dockerdocumentatie. 

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

7. Werk in het deelvenster **Aangepaste IoT Edge-modules** de gewenste eigenschappen van de *setmoduletwin* bij om de locatie van het SQL-pakket en de taakgegevens van streamanalytics op te nemen. Deze twee velden zijn optioneel en moeten worden gebruikt als u de SQL Database Edge-module wilt implementeren met een database en een streamingtaak.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

8. Stel in het deelvenster Aangepaste modules van **IoT Edge** het *beleid opnieuw starten* in op altijd en gewenste *status* op uitvoeren.
9. Klik in het deelvenster **Aangepaste IoT Edge-modules** op **Opslaan**.
10. Klik **op** de pagina Modules instellen op **Volgende**.
11. Geef **op de pagina Route opgeven (optioneel)** van de pagina **Modules instellen** de routes op voor module naar module of module naar IoT Edge Hub-communicatie zie [Modules implementeren en routes instellen in IoT Edge.](../iot-edge/module-composition.md)
12. Klik op **Volgende**.
13. Klik **op Verzenden**.

In deze quickstart hebt u een SQL Database Edge-module geïmplementeerd op een IoT Edge-apparaat.

## <a name="next-steps"></a>Volgende stappen

- [Machine Learning en kunstmatige intelligentie met ONNX in SQL Database Edge](onnx-overview.md).
- Een end-to-end IoT-oplossing bouwen met SQL Database Edge met IoT Edge.
