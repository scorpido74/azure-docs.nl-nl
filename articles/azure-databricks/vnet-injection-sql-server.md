---
title: Een SQL Server Linux Docker-container opvragen met Azure Databricks
description: In dit artikel wordt beschreven hoe u Azure Databricks implementeert in uw virtuele netwerk, ook wel VNet-injectie genoemd.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 460079248e6cbd939c36b84f94cac41dce4dda2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73747661"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>Zelfstudie: Een SQL Server Linux Docker-container in een virtueel netwerk opvragen vanuit een Azure Databricks-notitieblok

In deze zelfstudie leert u hoe u Azure Databricks integreren met een SQL Server Linux Docker-container in een virtueel netwerk. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Azure Databricks-werkruimte implementeren in een virtueel netwerk
> * Een Virtuele Linux-machine installeren in een openbaar netwerk
> * Docker installeren
> * Microsoft SQL Server installeren op Linux-dockercontainer
> * De SQL-server opvragen met JDBC vanuit een Databricks-notitieblok

## <a name="prerequisites"></a>Vereisten

* Maak een [Databricks-werkruimte in een virtueel netwerk.](quickstart-create-databricks-workspace-vnet-injection.md)

* Installeer [Ubuntu voor Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab).

* [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) downloaden.

## <a name="create-a-linux-virtual-machine"></a>Een virtuele Linux-machine maken

1. Selecteer in de Azure-portal het pictogram voor **virtuele machines**. Selecteer vervolgens **+ Toevoegen**.

    ![Nieuwe virtuele Azure-machine toevoegen](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. Kies op het tabblad **Basisbeginselen** Ubuntu Server 18.04 LTS en wijzig de VM-grootte in B2's. Kies een gebruikersnaam en wachtwoord van de beheerder.

    ![Tabblad Basisvan nieuwe virtuele machineconfiguratie](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. Navigeer naar het tabblad **Netwerken.** Kies het virtuele netwerk en het openbare subnet met uw Azure Databricks-cluster. Selecteer **Controleren + maken**en vervolgens **Maken** om de virtuele machine te implementeren.

    ![Tabblad Netwerken met nieuwe configuratie van virtuele machines](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. Wanneer de implementatie is voltooid, navigeert u naar de virtuele machine. Let op het openbare IP-adres en virtueel netwerk/subnet in het **overzicht**. Het **openbare IP-adres selecteren**

    ![Overzicht van virtuele machines](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. Wijzig de **toewijzing** in **Statisch** en voer een **DNS-naamlabel in.** Selecteer **Opslaan**en start de virtuele machine opnieuw.

    ![Configuratie van openbare IP-adres](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. Selecteer het tabblad **Netwerken** onder **Instellingen**. De netwerkbeveiligingsgroep die is gemaakt tijdens de Azure Databricks-implementatie, is gekoppeld aan de virtuele machine. Selecteer **Inkomende poortregel toevoegen**.

7. Voeg een regel toe om poort 22 voor SSH te openen. Gebruik de volgende instellingen:
    
    |Instelling|Voorgestelde waarde|Beschrijving|
    |-------|---------------|-----------|
    |Bron|IP-adressen|IP-adressen geeft aan dat binnenkomend verkeer van een specifiek ip-adres van de bron door deze regel wordt toegestaan of geweigerd.|
    |IP-adressen van bron|<uw openbare ip\>|Voer het openbare IP-adres in. U uw openbare IP-adres vinden door [bing.com](https://www.bing.com/) te bezoeken en te zoeken naar **mijn IP.**|
    |Poortbereiken van bron|*|Sta verkeer vanuit elke poort toe.|
    |Doel|IP-adressen|IP-adressen geeft aan dat uitgaand verkeer voor een specifiek bron-IP-adres door deze regel wordt toegestaan of geweigerd.|
    |Doel-IP-adressen|<uw vm public ip\>|Voer het openbare IP-adres van uw virtuele machine in. U vindt dit op de **overzichtspagina** van uw virtuele machine.|
    |Poortbereiken van doel|22|Open poort 22 voor SSH.|
    |Prioriteit|290|Geef de regel een prioriteit.|
    |Name|ssh-databricks-tutorial-vm|Geef de regel een naam.|


    ![Inkomende beveiligingsregel toevoegen voor poort 22](./media/vnet-injection-sql-server/open-port.png)

8. Voeg een regel toe om poort 1433 voor SQL te openen met de volgende instellingen:

    |Instelling|Voorgestelde waarde|Beschrijving|
    |-------|---------------|-----------|
    |Bron|Alle|Bron geeft aan dat binnenkomend verkeer van een specifiek ip-adres van de bron door deze regel wordt toegestaan of geweigerd.|
    |Poortbereiken van bron|*|Sta verkeer vanuit elke poort toe.|
    |Doel|IP-adressen|IP-adressen geeft aan dat uitgaand verkeer voor een specifiek bron-IP-adres door deze regel wordt toegestaan of geweigerd.|
    |Doel-IP-adressen|<uw vm public ip\>|Voer het openbare IP-adres van uw virtuele machine in. U vindt dit op de **overzichtspagina** van uw virtuele machine.|
    |Poortbereiken van doel|1433|Open poort 22 voor SQL Server.|
    |Prioriteit|300|Geef de regel een prioriteit.|
    |Name|sql-databricks-tutorial-vm|Geef de regel een naam.|

    ![Inkomende beveiligingsregel toevoegen voor poort 1433](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>SQL Server uitvoeren in een Docker-container

1. Open [Ubuntu voor Windows,](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)of een ander hulpmiddel waarmee u SSH in de virtuele machine. Navigeer naar uw virtuele machine in de Azure-portal en selecteer **Verbinding maken** om de SSH-opdracht te krijgen die u nodig hebt om verbinding te maken.

    ![Verbinding maken met de virtuele machine](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. Voer de opdracht in uw Ubuntu-terminal in en voer het beheerderswachtwoord in dat u hebt gemaakt toen u de virtuele machine configureerde.

    ![Ubuntu terminal SSH aanmelden](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. Gebruik de volgende opdracht om Docker op de virtuele machine te installeren.

    ```bash
    sudo apt-get install docker.io
    ```

    Controleer de installatie van Docker met de volgende opdracht:

    ```bash
    sudo docker --version
    ```

4. Installeer de afbeelding.

    ```bash
    sudo docker pull mcr.microsoft.com/mssql/server:2017-latest
    ```

    Controleer de beelden.

    ```bash
    sudo docker images
    ```

5. Voer de container uit de afbeelding.

    ```bash
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 --name sql1  -d mcr.microsoft.com/mssql/server:2017-latest
    ```

    Controleer of de container actief is.

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>Een SQL-database maken

1. Open SQL Server Management Studio en maak verbinding met de server met de servernaam en SQL-verificatie. De gebruikersnaam voor aanmelden is **SA** en het wachtwoord is het wachtwoord dat is ingesteld in de opdracht Docker. Het wachtwoord in de `Password1234`opdracht voorbeeld is .

    ![Verbinding maken met SQL Server met SQL Server Management Studio](./media/vnet-injection-sql-server/ssms-login.png)

2. Nadat u verbinding hebt gemaakt, selecteert u **Nieuwe query** en voert u het volgende codefragment in om een database, een tabel te maken en enkele records in de tabel in te voegen.

    ```SQL
    CREATE DATABASE MYDB;
    GO
    USE MYDB;
    CREATE TABLE states(Name VARCHAR(20), Capitol VARCHAR(20));
    INSERT INTO states VALUES ('Delaware','Dover');
    INSERT INTO states VALUES ('South Carolina','Columbia');
    INSERT INTO states VALUES ('Texas','Austin');
    SELECT * FROM states
    GO
    ```

    ![Query om een SQL Server-database te maken](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>SQL Server opvragen vanuit Azure Databricks

1. Navigeer naar uw Azure Databricks-werkruimte en controleer of u een cluster hebt gemaakt als onderdeel van de vereisten. Selecteer vervolgens **Een notitieblok maken**. Geef het notitieblok een naam, selecteer *Python* als taal en selecteer het cluster dat u hebt gemaakt.

    ![Nieuwe databricks-notitieblokinstellingen](./media/vnet-injection-sql-server/create-notebook.png)

2. Gebruik de volgende opdracht om het interne IP-adres van de virtuele SQL Server-machine te pingen. Deze ping moet succesvol zijn. Als dit niet het zo is, controleert u of de container wordt uitgevoerd en controleert u de NSG-configuratie (Network Security Group).

    ```python
    %sh
    ping 10.179.64.4
    ```

    U ook de opdracht nslookup gebruiken om te bekijken.

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. Zodra u de SQL Server hebt gepingd, u de database en tabellen opvragen. Voer de volgende python-code uit:

    ```python
    jdbcHostname = "10.179.64.4"
    jdbcDatabase = "MYDB"
    userName = 'SA'
    password = 'Password1234'
    jdbcPort = 1433
    jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, userName, password)

    df = spark.read.jdbc(url=jdbcUrl, table='states')
    display(df)
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de brongroep, de Azure Databricks-werkruimte en alle gerelateerde resources wanneer dit niet meer nodig is. Het verwijderen van de taak voorkomt onnodige facturering. Als u de Azure Databricks-werkruimte in de toekomst wilt gebruiken, u het cluster stoppen en het later opnieuw starten. Als u deze Azure Databricks-werkruimte niet meer wilt gebruiken, verwijdert u alle resources die u in deze zelfstudie hebt gemaakt met de volgende stappen:

1. Klik in het linkermenu in de Azure-portal op **Resourcegroepen** en klik vervolgens op de naam van de resourcegroep die u hebt gemaakt.

2. Selecteer op de pagina Met uw brongroep de optie **Verwijderen**, typ de naam van de bron die u wilt verwijderen in het tekstvak en selecteer opnieuw **verwijderen.**

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over het extraheren, transformeren en laden van gegevens met Azure Databricks.
> [!div class="nextstepaction"]
> [Zelfstudie: Gegevens extraheren, transformeren en laden met Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
