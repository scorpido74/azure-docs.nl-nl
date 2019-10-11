---
title: Een query uitvoeren op een SQL Server Linux-docker-container met Azure Databricks
description: In dit artikel wordt beschreven hoe u Azure Databricks implementeert in uw virtuele netwerk, ook wel bekend als VNet-injectie.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 773ffe264446e6a4d9ef2e88634e4f2c9b8aeb45
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72273979"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>Zelf studie: een SQL Server Linux-docker-container in een virtueel netwerk doorzoeken van een Azure Databricks notebook

In deze zelf studie leert u hoe u Azure Databricks integreert met een SQL Server Linux docker-container in een virtueel netwerk. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Azure Databricks-werk ruimte implementeren in een virtueel netwerk
> * Een virtuele Linux-machine installeren in een openbaar netwerk
> * Docker installeren
> * Microsoft SQL Server installeren op de Linux docker-container
> * Een query uitvoeren op de SQL Server met JDBC vanuit een Databricks-notebook

## <a name="prerequisites"></a>Vereisten

* Maak een [Databricks-werk ruimte in een virtueel netwerk](quickstart-create-databricks-workspace-vnet-injection.md).

* Installeer [Ubuntu voor Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab).

* [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) downloaden.

## <a name="create-a-linux-virtual-machine"></a>Een virtuele Linux-machine maken

1. Selecteer in de Azure Portal het pictogram voor **virtual machines**. Selecteer vervolgens **+ toevoegen**.

    ![Nieuwe virtuele machine van Azure toevoegen](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. Kies op het tabblad **basis beginselen** de optie Ubuntu Server 16,04 LTS. Wijzig de VM-grootte in B1ms. deze heeft één VCPU'S en 2 GB RAM-geheugen. De minimale vereiste voor een Linux SQL Server docker-container is 2 GB. Kies een gebruikers naam en wacht woord voor de beheerder.

    ![Tabblad basis informatie van de configuratie van de nieuwe virtuele machine](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. Ga naar het tabblad **netwerken** . Kies het virtuele netwerk en het open bare subnet dat uw Azure Databricks cluster bevat. Selecteer **controleren + maken**en vervolgens **maken** om de virtuele machine te implementeren.

    ![Tabblad netwerk van de configuratie van de nieuwe virtuele machine](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. Wanneer de implementatie is voltooid, gaat u naar de virtuele machine. Let op het open bare IP-adres en het virtuele netwerk/subnet in het **overzicht**. Het **open bare IP-adres** selecteren

    ![Overzicht van virtuele machines](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. Wijzig de **toewijzing** in **statisch** en voer een **DNS-naam label**in. Selecteer **Opslaan**en start de virtuele machine opnieuw op.

    ![Configuratie van openbaar IP-adres](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. Selecteer het tabblad **netwerken** onder **instellingen**. U ziet dat de netwerk beveiligings groep die is gemaakt tijdens de Azure Databricks-implementatie is gekoppeld aan de virtuele machine. Selecteer **regel voor binnenkomende poort toevoegen**.

7. Voeg een regel toe voor het openen van poort 22 voor SSH. Gebruik de volgende instellingen:
    
    |Instelling|Voorgestelde waarde|Beschrijving|
    |-------|---------------|-----------|
    |Bron|IP-adressen|IP-adressen geeft aan dat binnenkomend verkeer van een specifiek IP-adres van de bron wordt toegestaan of geweigerd door deze regel.|
    |IP-adressen van bron|< uw open bare IP-adres @ no__t-0|Voer het open bare IP-adres in. U kunt uw open bare IP-adres vinden door naar [Bing.com](https://www.bing.com/) te gaan en naar **' mijn IP '** te zoeken.|
    |Poortbereiken van bron|*|Verkeer vanaf elke poort toestaan.|
    |Bestemming|IP-adressen|IP-adressen geeft aan dat uitgaand verkeer voor een specifiek IP-adres van de bron wordt toegestaan of geweigerd door deze regel.|
    |Doel-IP-adressen|het open bare IP-adres van de VM < @ no__t-0|Voer het open bare IP-adres van uw virtuele machine in. U kunt dit vinden op de pagina **overzicht** van de virtuele machine.|
    |Poortbereiken van doel|22|Open poort 22 voor SSH.|
    |Prioriteit|290|Geef een prioriteit voor de regel op.|
    |Naam|SSH-databricks-zelf studie-VM|Geef een naam op voor de regel.|


    ![Binnenkomende beveiligings regel toevoegen voor poort 22](./media/vnet-injection-sql-server/open-port.png)

8. Voeg een regel toe om poort 1433 voor SQL te openen met de volgende instellingen:

    |Instelling|Voorgestelde waarde|Beschrijving|
    |-------|---------------|-----------|
    |Bron|IP-adressen|IP-adressen geeft aan dat binnenkomend verkeer van een specifiek IP-adres van de bron wordt toegestaan of geweigerd door deze regel.|
    |IP-adressen van bron|10.179.0.0/16|Voer het adres bereik voor het virtuele netwerk in.|
    |Poortbereiken van bron|*|Verkeer vanaf elke poort toestaan.|
    |Bestemming|IP-adressen|IP-adressen geeft aan dat uitgaand verkeer voor een specifiek IP-adres van de bron wordt toegestaan of geweigerd door deze regel.|
    |Doel-IP-adressen|het open bare IP-adres van de VM < @ no__t-0|Voer het open bare IP-adres van uw virtuele machine in. U kunt dit vinden op de pagina **overzicht** van de virtuele machine.|
    |Poortbereiken van doel|1433|Open poort 22 voor SQL Server.|
    |Prioriteit|300|Geef een prioriteit voor de regel op.|
    |Naam|SQL-databricks-zelf studie-VM|Geef een naam op voor de regel.|

    ![Binnenkomende beveiligings regel toevoegen voor poort 1433](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>SQL Server uitvoeren in een docker-container

1. Open [Ubuntu voor Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)of een ander hulp programma dat u in staat stelt om SSH in te stellen op de virtuele machine. Ga naar de virtuele machine in de Azure Portal en selecteer **verbinding maken** om de SSH-opdracht op te halen die u nodig hebt om verbinding te maken.

    ![Verbinding maken met de virtuele machine](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. Voer de opdracht in de Ubuntu-terminal in en voer het beheerders wachtwoord in dat u hebt gemaakt tijdens het configureren van de virtuele machine.

    ![Ubuntu Terminal SSH-aanmelding](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. Gebruik de volgende opdracht om docker te installeren op de virtuele machine.

    ```bash
    sudo apt-get install docker.io
    ```

    Controleer de installatie van docker met de volgende opdracht:

    ```bash
    sudo docker --version
    ```

4. Installeer de installatie kopie.

    ```bash
    sudo docker pull mcr.microsoft.com/mssql/server:2017-latest
    ```

    Controleer de installatie kopieën.

    ```bash
    sudo docker images
    ```

5. Voer de container uit vanuit de installatie kopie.

    ```bash
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 --name sql1  -d mcr.microsoft.com/mssql/server:2017-latest
    ```

    Controleer of de container wordt uitgevoerd.

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>Een SQL Database maken

1. Open SQL Server Management Studio en maak verbinding met de server met behulp van de server naam en SQL-verificatie. De aanmeldings naam van de gebruiker is **sa** en het wacht woord is het wacht woord dat is ingesteld in de docker-opdracht. Het wacht woord in de voorbeeld opdracht is `Password1234`.

    ![Verbinding maken met SQL Server met behulp van SQL Server Management Studio](./media/vnet-injection-sql-server/ssms-login.png)

2. Zodra u verbinding hebt gemaakt, selecteert u **nieuwe query** en voert u het volgende code fragment in om een Data Base te maken, een tabel en enkele records in de tabel in te voegen.

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

    ![Een query uitvoeren om een SQL Server Data Base te maken](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>SQL Server opvragen van Azure Databricks

1. Navigeer naar uw Azure Databricks-werk ruimte en controleer of u een cluster hebt gemaakt als onderdeel van de vereisten. Selecteer vervolgens **een notitie blok maken**. Geef het notitie blok een naam, selecteer *python* als taal en selecteer het cluster dat u hebt gemaakt.

    ![Nieuwe instellingen voor Databricks-notebook](./media/vnet-injection-sql-server/create-notebook.png)

2. Gebruik de volgende opdracht om het interne IP-adres van de SQL Server virtuele machine te pingen. Deze ping moet slagen. Als dat niet het geval is, controleert u of de container wordt uitgevoerd en raadpleegt u de configuratie van de netwerk beveiligings groep (NSG).

    ```python
    %sh
    ping 10.179.64.4
    ```

    U kunt ook de nslookup opdracht gebruiken om te controleren.

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. Zodra u de SQL Server hebt gepingd, kunt u een query uitvoeren op de data base en tabellen. Voer de volgende python-code uit:

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

Als u deze niet meer nodig hebt, verwijdert u de resource groep, de Azure Databricks-werk ruimte en alle gerelateerde resources. Als u de taak verwijdert, vermijdt u onnodig factureren. Als u van plan bent de Azure Databricks-werk ruimte in de toekomst te gebruiken, kunt u het cluster stoppen en later opnieuw opstarten. Als u deze Azure Databricks werk ruimte niet wilt blijven gebruiken, verwijdert u alle resources die u in deze zelf studie hebt gemaakt met behulp van de volgende stappen:

1. Klik in het menu aan de linkerkant in het Azure Portal op **resource groepen** en klik vervolgens op de naam van de resource groep die u hebt gemaakt.

2. Selecteer op de pagina van de resource groep **verwijderen**, typ de naam van de resource die u wilt verwijderen in het tekstvak en selecteer vervolgens opnieuw **verwijderen** .

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over het extra heren, transformeren en laden van gegevens met behulp van Azure Databricks.
> [!div class="nextstepaction"]
> [Zelf studie: gegevens extra heren, transformeren en laden met behulp van Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
