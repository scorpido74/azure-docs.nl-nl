---
title: Verbinding maken met Azure SQL Edge en query's uitvoeren (preview-versie)
description: Meer informatie over het maken van verbinding en het uitvoeren van query's op Azure SQL Edge (preview)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 17bb5afebe0335bcdcf40298490f94999c04a621
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596916"
---
# <a name="connect-and-query-azure-sql-edge-preview"></a>Verbinding maken met Azure SQL Edge en Query's uitvoeren (preview-versie)

Nadat u de Azure SQL Edge-container hebt geïmplementeerd, kunt u verbinding maken met de SQL database-engine vanaf een van de volgende locaties.

- In de container
- Vanuit een andere docker-container die op dezelfde host wordt uitgevoerd.
- Op de hostmachine
- Van een andere client computer in het netwerk.

## <a name="tools-to-connect-to-azure-sql-edge"></a>Hulp middelen om verbinding te maken met Azure SQL Edge

Verbindingen met een exemplaar van Azure SQL Edge kunnen worden gemaakt op basis van de algemene hulpprogram ma's die hieronder worden genoemd.

* [Sqlcmd](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) -Sqlcmd-client hulpprogramma's zijn al opgenomen in de Azure SQL Edge-container installatie kopie. Als u aan een actieve container met een interactieve bash-shell koppelt, kunt u de hulpprogram ma's lokaal uitvoeren.
* [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)
* [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio)
* [Visual Studio code](https://docs.microsoft.com/sql/visual-studio-code/sql-server-develop-use-vscode)

Als u verbinding wilt maken met een Azure SQL Edge data base-engine vanaf een netwerk computer, hebt u het volgende nodig:

- *IP-adres of netwerk naam van de hostmachine* : dit is de hostmachine waar de Azure SQL Edge-container wordt uitgevoerd.
- *Poort toewijzing van Azure SQL Edge-container* -dit is de poort toewijzing voor de docker-container poort aan een poort op de host. Binnen de container SQL-rand wordt altijd toegewezen aan poort 1433. Dit kan worden gewijzigd als onderdeel van de Azure SQL Edge-implementatie. Als u het poort nummer wilt wijzigen, moet u de ' container Create-opties ' bijwerken voor de module SQL Edge in Azure IoT Edge. In het onderstaande voor beeld is poort 1433 op de container toegewezen aan poort 1600 op de host.

    ```JSON
    {
        "PortBindings": {
          "1433/tcp": [
            {
              "HostPort": "1600"
            }
          ]
        }
    }
    ```

- *Sa-wacht woord voor het SQL Edge-exemplaar* : dit is de waarde die is opgegeven voor de variabele **SA_PASSWORD** omgeving tijdens de implementatie van SQL Edge.

## <a name="connecting-to-the-database-engine-from-within-the-container"></a>Verbinding maken met de data base-engine vanuit de container

De [SQL Server opdracht regel Programma's](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) zijn opgenomen in de installatie kopie van de Azure SQL Edge-container. Als u aan de container met een interactieve opdracht prompt koppelt, kunt u de hulpprogram ma's lokaal uitvoeren.

1. Gebruik de `docker exec -it` opdracht om een interactieve bash-shell in de container die wordt uitgevoerd te starten. In het volgende voor beeld `e69e056c702d` is de container-ID.

    ```bash
    docker exec -it <Azure SQL Edge container id or name> /bin/bash
    ```

    > [!TIP]
    > U hoeft niet altijd de volledige container-ID op te geven. U hoeft alleen genoeg tekens op te geven om deze uniek te identificeren. In dit voor beeld is het mogelijk voldoende om te gebruiken `e6` of in `e69` plaats van de volledige id.

2. In de container kunt u lokaal verbinding maken met Sqlcmd. Sqlcmd bevindt zich standaard niet in het pad, dus u moet het volledige pad opgeven.

    ```bash
    /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P '<YourPassword>'
    ```

3. Wanneer u klaar bent met Sqlcmd, typt u `exit` .

4. Wanneer u klaar bent met de interactieve opdracht prompt, typt u `exit` . Uw container blijft actief nadat u de interactieve bash-shell hebt afgesloten.

## <a name="connect-to-sql-edge-from-another-container-on-the-same-host"></a>Verbinding maken met SQL Edge vanuit een andere container op dezelfde host

Omdat twee containers die op dezelfde host worden uitgevoerd, zich op hetzelfde docker-netwerk bevinden, kunnen ze gemakkelijk worden geopend met behulp van de container naam en het poort adres voor de service. Als u bijvoorbeeld verbinding maakt met het SQL Edge-exemplaar vanuit een andere python-module (container) op dezelfde host, kunt u een connection string gebruiken zoals in het volgende voor beeld. In het onderstaande voor beeld wordt ervan uitgegaan dat SQL Edge is geconfigureerd om te Luis teren op de standaard poort.

```python

import pyodbc
server = 'MySQLEdgeContainer' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

## <a name="connect-to-sql-edge-from-another-network-machine"></a>Verbinding maken met SQL Edge vanaf een andere netwerk computer

Als u verbinding wilt maken met het SQL Edge-exemplaar van een andere computer in het netwerk, moet u het IP-adres van de docker-host en de poort van de host waaraan de SQL Edge-container is toegewezen, gebruiken. Als het IP-adres van de docker-host bijvoorbeeld * xxx.xxx.xxx.xxx is en de SQL Edge-container is toegewezen aan poort *1600*van de host, zou het server adres voor SQL Edge **-instantie xxx. xxx. xxx. xxx, 1.600**zijn. Het bijgewerkte python-script zou worden

```python

import pyodbc
server = 'xxx.xxx.xxx.xxx,1600' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

Als u verbinding wilt maken met een exemplaar van SQL Edge met SQL Server Management Studio uitgevoerd op een Windows-computer, raadpleegt u [SQL Server Management Studio](https://docs.microsoft.com/sql/linux/sql-server-linux-manage-ssms).

Als u verbinding wilt maken met een exemplaar van SQL Edge met behulp van Visual Studio code op een Windows-, Mac-of Linux-computer, raadpleegt u [Visual Studio code](https://docs.microsoft.com/sql/visual-studio-code/sql-server-develop-use-vscode).

Als u verbinding wilt maken met een exemplaar van SQL Edge met behulp van Azure Data Studio op Windows, Mac of Linux-computer, raadpleegt u [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-server).

## <a name="see-also"></a>Zie ook

[Verbinding maken en Query's uitvoeren](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#connect-and-query)

[SQL Server-hulpprogram ma's installeren in Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools)
