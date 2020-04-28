---
title: 'Verbinding maken met Azure Databricks vanuit Excel, python of R '
description: Meer informatie over het gebruik van het Simba-stuur programma om Azure Databricks te verbinden met Excel, python of R.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: mamccrea
ms.openlocfilehash: f7494d36cf9b16ac6c7a1287a6ff96dd2285c6e2
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "73601949"
---
# <a name="connect-to-azure-databricks-from-excel-python-or-r"></a>Verbinding maken met Azure Databricks vanuit Excel, python of R

In dit artikel leert u hoe u het ODBC-stuur programma Databricks kunt gebruiken om verbinding te maken Azure Databricks met micro soft Excel, python of R. Zodra u de verbinding tot stand hebt gebracht, kunt u de gegevens in Azure Databricks openen vanuit de Excel-, python-of R-clients. U kunt ook de-clients gebruiken om de gegevens verder te analyseren. 

## <a name="prerequisites"></a>Vereisten

* U moet beschikken over een Azure Databricks-werk ruimte, een Spark-cluster en voorbeeld gegevens die zijn gekoppeld aan uw cluster. Als u deze vereisten nog niet hebt, voltooit u de Snelstartgids bij het [uitvoeren van een Spark-taak op Azure Databricks met behulp van de Azure Portal](quickstart-create-databricks-workspace-portal.md).

* Down load het Databricks ODBC-stuur programma van de [Download pagina voor het Databricks-stuur programma](https://databricks.com/spark/odbc-driver-download). Installeer de 32-bits of 64-bits versie, afhankelijk van de toepassing van waaruit u verbinding wilt maken met Azure Databricks. Als u bijvoorbeeld verbinding wilt maken vanuit Excel, installeert u de 32-bits versie van het stuur programma. Als u verbinding wilt maken vanaf R en Python, installeert u de 64-bits versie van het stuur programma.

* Stel een persoonlijk toegangs token in op Databricks. Zie [token beheer](/azure/databricks/dev-tools/api/latest/authentication)voor instructies.

## <a name="set-up-a-dsn"></a>Een DSN instellen

Een naam van een gegevens bron (DSN) bevat informatie over een specifieke gegevens bron. Een ODBC-stuur programma heeft deze DSN nodig om verbinding te maken met een gegevens bron. In deze sectie kunt u een DSN instellen die kan worden gebruikt met het ODBC-stuur programma Databricks om verbinding te maken met Azure Databricks van clients zoals micro soft Excel, python of R.

1. Ga in de werk ruimte Azure Databricks naar het Databricks-cluster.

    ![Databricks-cluster openen](./media/connect-databricks-excel-python-r/open-databricks-cluster.png "Databricks-cluster openen")

2. Klik onder het tabblad **configuratie** op het tabblad **JDBC/ODBC** en kopieer de waarden voor de **server hostnaam** en het **http-pad**. U hebt deze waarden nodig om de stappen in dit artikel te volt ooien.

    ![Databricks-configuratie ophalen](./media/connect-databricks-excel-python-r/get-databricks-jdbc-configuration.png "Databricks-configuratie ophalen")

3. Start op uw computer de **ODBC-gegevens bron** toepassing (32-bits of 64-bits), afhankelijk van de toepassing. Als u verbinding wilt maken vanuit Excel, gebruikt u de 32-bits versie. Gebruik de 64-bits versie om verbinding te maken vanaf R en python.

    ![ODBC starten](./media/connect-databricks-excel-python-r/launch-odbc-app.png "ODBC-app starten")

4. Klik op het tabblad **gebruikers-DSN** op **toevoegen**. Selecteer in het dialoog venster **nieuwe gegevens bron maken** het **Simba Spark ODBC-stuur programma**en klik vervolgens op **volt ooien**.

    ![ODBC starten](./media/connect-databricks-excel-python-r/add-new-user-dsn.png "ODBC-app starten")

5. Geef in het dialoog venster **Simba Spark ODBC-stuur programma** de volgende waarden op:

    ![DSN configureren](./media/connect-databricks-excel-python-r/odbc-dsn-setup.png "DSN configureren")

    De volgende tabel bevat informatie over de waarden die u kunt opgeven in het dialoog venster.
    
    |Veld  | Waarde  |
    |---------|---------|
    |**Naam van de gegevensbron**     | Geef een naam op voor de gegevens bron.        |
    |**Host (s)**     | Geef de waarde op die u hebt gekopieerd uit de Databricks-werk ruimte voor de hostnaam van de *Server*.        |
    |**Importeer**     | Voer *443*in.        |
    |**Verificatie** > **mechanisme**     | Selecteer de *gebruikers naam en het wacht woord*.        |
    |**Gebruikersnaam**     | Voer een *token*in.        |
    |**Wachtwoord**     | Voer de token waarde in die u hebt gekopieerd uit de Databricks-werk ruimte. |
    
    Voer de volgende aanvullende stappen uit in het dialoog venster DSN-instellingen.
    
    * Klik op **http-opties**. Plak in het dialoog venster dat wordt geopend de waarde voor het *http-pad* dat u hebt gekopieerd uit de Databricks-werk ruimte. Klik op **OK**.
    * Klik op **SSL-opties**. Schakel in het dialoog venster dat wordt geopend het selectie vakje **SSL inschakelen** in. Klik op **OK**.
    * Klik op **testen** om de verbinding met Azure Databricks te testen. Klik op **OK** om de configuratie op te slaan.
    * Klik in het dialoog venster **ODBC-gegevens bron beheer** op **OK**.

U hebt uw DSN nu ingesteld. In de volgende secties gebruikt u deze DSN om verbinding te maken met Azure Databricks vanuit Excel, python of R.

## <a name="connect-from-microsoft-excel"></a>Verbinding maken vanuit micro soft Excel

In deze sectie haalt u gegevens op uit Azure Databricks in micro soft Excel met behulp van de DSN die u eerder hebt gemaakt. Voordat u begint, moet u ervoor zorgen dat micro soft Excel op uw computer is geïnstalleerd. U kunt een proef versie van Excel gebruiken vanuit de [proef koppeling van micro soft Excel](https://products.office.com/excel).

1. Open een lege werkmap in micro soft Excel. Klik in het **gegevens** lint op **gegevens ophalen**. Klik op **van andere bronnen** en klik vervolgens op **van ODBC**.

    ![ODBC starten vanuit Excel](./media/connect-databricks-excel-python-r/launch-odbc-from-excel.png "ODBC starten vanuit Excel")

2. Selecteer in het dialoog venster **van ODBC** de DSN die u eerder hebt gemaakt en klik vervolgens op **OK**.

    ![DSN selecteren](./media/connect-databricks-excel-python-r/excel-select-dsn.png "DSN selecteren")

3. Als u wordt gevraagd om referenties, typt u bij gebruikers naam **token**. Geef bij wacht woord de token waarde op die u hebt opgehaald uit de Databricks-werk ruimte.

    ![Referenties opgeven voor Databricks](./media/connect-databricks-excel-python-r/excel-databricks-token.png "DSN selecteren")

4. In het venster navigator selecteert u de tabel in Databricks die u naar Excel wilt laden en klikt u vervolgens op **Load**. 

    ![DTA laden in Excel](./media/connect-databricks-excel-python-r/excel-load-data.png "DTA laden in Excel")

Zodra u de gegevens in uw Excel-werkmap hebt, kunt u er analytische bewerkingen op uitvoeren.

## <a name="connect-from-r"></a>Verbinding maken vanaf R

> [!NOTE]
> Deze sectie bevat informatie over het integreren van een R Studio-client die op uw bureau blad wordt uitgevoerd met Azure Databricks. Zie [r studio op Azure Databricks](/azure/databricks/spark/latest/sparkr/rstudio)voor instructies over het gebruik van r studio op het Azure Databricks cluster zelf.

In deze sectie gebruikt u een R-taal-IDE om te verwijzen naar gegevens die beschikbaar zijn in Azure Databricks. Voordat u begint, moet u het volgende hebben geïnstalleerd op de computer.

* Een IDE voor R-taal. In dit artikel wordt RStudio gebruikt voor desktop. U kunt dit installeren via [R Studio downloaden](https://www.rstudio.com/products/rstudio/download/).
* Als u RStudio voor het bureau blad gebruikt als uw IDE, installeert u ook micro [https://aka.ms/rclient/](https://aka.ms/rclient/)Soft R client van. 

Open RStudio en voer de volgende stappen uit:

- Verwijzen naar `RODBC` het pakket. Hierdoor kunt u verbinding maken met Azure Databricks met behulp van de DSN die u eerder hebt gemaakt.
- Een verbinding tot stand brengen met behulp van de DSN.
- Een SQL-query uitvoeren op de gegevens in Azure Databricks. In het volgende code fragment is *radio_sample_data* een tabel die al bestaat in azure Databricks.
- Voer enkele bewerkingen uit op de query om de uitvoer te controleren. 

Met het volgende code fragment worden de volgende taken uitgevoerd:

    # reference the 'RODBC' package
    require(RODBC)
    
    # establish a connection using the DSN you created earlier
    conn <- odbcConnect("<ENTER DSN NAME HERE>")
    
    # run a SQL query using the connection you created
    res <- sqlQuery(conn, "SELECT * FROM radio_sample_data")
    
    # print out the column names in the query output
    names(res) 
        
    # print out the number of rows in the query output
    nrow (res)

## <a name="connect-from-python"></a>Verbinding maken vanuit python

In deze sectie gebruikt u een python IDE (zoals inactief) om te verwijzen naar gegevens die beschikbaar zijn in Azure Databricks. Voordat u begint, moet u de volgende vereisten volt ooien:

* Installeer python vanaf deze [locatie](https://www.python.org/downloads/). Als u python vanaf deze koppeling installeert, wordt ook inactief geïnstalleerd.

* Installeer het `pyodbc` pakket vanaf een opdracht prompt op de computer. Voer de volgende opdracht uit:

      pip install pyodbc

Open inactief en voer de volgende stappen uit:

- Importeer het `pyodbc` pakket. Hierdoor kunt u verbinding maken met Azure Databricks met behulp van de DSN die u eerder hebt gemaakt.
- Een verbinding tot stand brengen met behulp van de DSN die u eerder hebt gemaakt.
-  Voer een SQL-query uit met behulp van de verbinding die u hebt gemaakt. In het volgende code fragment is *radio_sample_data* een tabel die al bestaat in azure Databricks.
- Voer bewerkingen uit op de query om de uitvoer te controleren.

Met het volgende code fragment worden de volgende taken uitgevoerd:

```python
# import the `pyodbc` package:
import pyodbc

# establish a connection using the DSN you created earlier
conn = pyodbc.connect("DSN=<ENTER DSN NAME HERE>", autocommit=True)

# run a SQL query using the connection you created
cursor = conn.cursor()
cursor.execute("SELECT * FROM radio_sample_data")

# print the rows retrieved by the query.
for row in cursor.fetchall():
    print(row)
```

## <a name="next-steps"></a>Volgende stappen

* Zie [gegevens bronnen voor Azure Databricks voor](/azure/databricks/data/data-sources/index) meer informatie over de bronnen waaruit u gegevens kunt importeren in azure Databricks.


