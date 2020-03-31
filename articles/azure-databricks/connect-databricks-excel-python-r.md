---
title: 'Verbinding maken met Azure Databricks vanuit Excel, Python of R '
description: Meer informatie over het gebruik van het Simba-stuurprogramma om Azure Databricks te verbinden met Excel, Python of R.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: mamccrea
ms.openlocfilehash: f7494d36cf9b16ac6c7a1287a6ff96dd2285c6e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73601949"
---
# <a name="connect-to-azure-databricks-from-excel-python-or-r"></a>Verbinding maken met Azure Databricks vanuit Excel, Python of R

In dit artikel leert u hoe u het ODBC-stuurprogramma van Databricks gebruiken om Azure Databricks te verbinden met Microsoft Excel-, Python- of R-taal. Zodra u de verbinding tot stand hebt gebracht, hebt u toegang tot de gegevens in Azure Databricks vanuit de Excel-, Python- of R-clients. U de clients ook gebruiken om de gegevens verder te analyseren. 

## <a name="prerequisites"></a>Vereisten

* U moet beschikken over een Azure Databricks-werkruimte, een Spark-cluster en voorbeeldgegevens die zijn gekoppeld aan uw cluster. Als u deze vereisten nog niet hebt, voltooit u de quickstart bij [Een Spark-taak uitvoeren op Azure Databricks met behulp van de Azure-portal.](quickstart-create-databricks-workspace-portal.md)

* Download het Databricks ODBC-stuurprogramma van [de downloadpagina van Databricks driver.](https://databricks.com/spark/odbc-driver-download) Installeer de 32-bits of 64-bits versie, afhankelijk van de toepassing van waaruit u verbinding wilt maken met Azure Databricks. Als u bijvoorbeeld verbinding wilt maken vanuit Excel, installeert u de 32-bits versie van het stuurprogramma. Als u verbinding wilt maken met R en Python, installeert u de 64-bits versie van het stuurprogramma.

* Stel een persoonlijk toegangstoken in Databricks in. Zie [Tokenbeheer](/azure/databricks/dev-tools/api/latest/authentication)voor instructies .

## <a name="set-up-a-dsn"></a>Een DSN instellen

Een gegevensbronnaam (DSN) bevat de informatie over een specifieke gegevensbron. Een ODBC-stuurprogramma heeft deze DSN nodig om verbinding te maken met een gegevensbron. In deze sectie stelt u een DSN in die kan worden gebruikt met het DATAbricks ODBC-stuurprogramma om verbinding te maken met Azure Databricks van clients zoals Microsoft Excel, Python of R.

1. Navigeer vanuit de azure databricks-werkruimte naar het cluster Databricks.

    ![Cluster Gegevensstenen openen](./media/connect-databricks-excel-python-r/open-databricks-cluster.png "Cluster Gegevensstenen openen")

2. Klik op het tabblad **Configuratie** op het tabblad **JDBC/ODBC** en kopieer de waarden voor **Serverhostname** en **HTTP-pad**. U hebt deze waarden nodig om de stappen in dit artikel uit te voeren.

    ![Gegevensbricks-configuratie behouden](./media/connect-databricks-excel-python-r/get-databricks-jdbc-configuration.png "Gegevensbricks-configuratie behouden")

3. Start de **ODBC Data Sources-toepassing** (32-bits of 64-bits) op uw computer, afhankelijk van de toepassing. Als u verbinding wilt maken vanuit Excel, gebruikt u de 32-bits versie. Gebruik de 64-bits versie om verbinding te maken via R en Python.

    ![ODBC starten](./media/connect-databricks-excel-python-r/launch-odbc-app.png "ODBC-app starten")

4. Klik onder het tabblad **DSN** voor gebruiker op **Toevoegen**. Selecteer in het dialoogvenster **Nieuwe gegevensbron maken** de optie **Simba Spark ODBC-stuurprogramma**en klik op **Voltooien**.

    ![ODBC starten](./media/connect-databricks-excel-python-r/add-new-user-dsn.png "ODBC-app starten")

5. Geef in het dialoogvenster **Simba Spark ODBC-stuurprogramma** de volgende waarden op:

    ![DSN configureren](./media/connect-databricks-excel-python-r/odbc-dsn-setup.png "DSN configureren")

    In de volgende tabel vindt u informatie over de waarden die u in het dialoogvenster moet opgeven.
    
    |Veld  | Waarde  |
    |---------|---------|
    |**Naam van de gegevensbron**     | Geef een naam op voor de gegevensbron.        |
    |**Host(s)**     | Geef de waarde op die u hebt gekopieerd vanuit de werkruimte Databricks voor *Serverhostname.*        |
    |**Poort**     | Voer *443*in .        |
    |**Verificatiemechanisme** > **Mechanism**     | Selecteer *Gebruikersnaam en wachtwoord*.        |
    |**Gebruikersnaam**     | Token *token*invoeren .        |
    |**Wachtwoord**     | Voer de tokenwaarde in die u hebt gekopieerd vanuit de werkruimte Databricks. |
    
    Voer de volgende extra stappen uit in het dialoogvenster DSN-instelling.
    
    * Klik op **HTTP-opties**. Plak in het dialoogvenster dat wordt geopend de waarde voor *HTTP-pad* dat u hebt gekopieerd uit de werkruimte Databricks. Klik op **OK**.
    * Klik op **SSL-opties**. Schakel in het dialoogvenster dat wordt geopend het selectievakje **SSL inschakelen** in. Klik op **OK**.
    * Klik **op Testen** om de verbinding met Azure Databricks te testen. Klik op **OK** om de configuratie op te slaan.
    * Klik in het dialoogvenster **ODBC-gegevensbronbeheerder** op **OK**.

Je hebt nu je DSN ingesteld. In de volgende secties gebruikt u deze DSN om verbinding te maken met Azure Databricks vanuit Excel, Python of R.

## <a name="connect-from-microsoft-excel"></a>Verbinding maken vanuit Microsoft Excel

In deze sectie haalt u gegevens uit Azure Databricks naar Microsoft Excel met behulp van de DSN die u eerder hebt gemaakt. Controleer voordat u begint of Microsoft Excel op uw computer is geïnstalleerd. U een proefversie van Excel gebruiken via [microsoft Excel-testkoppeling](https://products.office.com/excel).

1. Open een lege werkmap in Microsoft Excel. Klik op het lint **Gegevens** op **Gegevens ophalen**. Klik **op Uit andere bronnen** en klik vervolgens op Van **ODBC**.

    ![ODBC starten vanuit Excel](./media/connect-databricks-excel-python-r/launch-odbc-from-excel.png "ODBC starten vanuit Excel")

2. Selecteer in het dialoogvenster **Van ODBC** de DSN die u eerder hebt gemaakt en klik op **OK**.

    ![DSN selecteren](./media/connect-databricks-excel-python-r/excel-select-dsn.png "DSN selecteren")

3. Als u om referenties wordt gevraagd, voert u voor gebruikersnaam **token**in. Geef voor wachtwoord de tokenwaarde op die u hebt opgehaald uit de werkruimte Databricks.

    ![Referenties voor Databricks verstrekken](./media/connect-databricks-excel-python-r/excel-databricks-token.png "DSN selecteren")

4. Selecteer in het navigatorvenster de tabel in Gegevensstenen die u naar Excel wilt laden en klik op **Laden**. 

    ![DTA laden in Excel](./media/connect-databricks-excel-python-r/excel-load-data.png "DTA laden in Excel")

Zodra u de gegevens in uw Excel-werkmap hebt, u er analytische bewerkingen op uitvoeren.

## <a name="connect-from-r"></a>Verbinding maken vanaf R

> [!NOTE]
> In dit gedeelte vindt u informatie over het integreren van een R Studio-client die op uw bureaublad wordt uitgevoerd met Azure Databricks. Zie [R Studio op Azure Databricks](/azure/databricks/spark/latest/sparkr/rstudio)voor instructies over het gebruik van R Studio op het Azure Databricks-cluster zelf.

In deze sectie gebruikt u een IDE in de R-taal om te verwijzen naar gegevens die beschikbaar zijn in Azure Databricks. Voordat u begint, moet u het volgende op de computer hebben geïnstalleerd.

* Een IDE voor R-taal. In dit artikel wordt RStudio voor desktop gebruikt. U het installeren van [R Studio downloaden.](https://www.rstudio.com/products/rstudio/download/)
* Als u RStudio voor desktop als IDE gebruikt, [https://aka.ms/rclient/](https://aka.ms/rclient/)installeert u ook Microsoft R-client van . 

Open RStudio en volg de volgende stappen:

- Verwijs `RODBC` naar het pakket. Hiermee u verbinding maken met Azure Databricks via de DSN die u eerder hebt gemaakt.
- Een verbinding tot stand brengen met de DSN.
- Voer een SQL-query uit op de gegevens in Azure Databricks. In het volgende fragment is *radio_sample_data* een tabel die al bestaat in Azure Databricks.
- Voer bepaalde bewerkingen uit op de query om de uitvoer te verifiëren. 

In het volgende codefragment worden deze taken uitgevoerd:

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

## <a name="connect-from-python"></a>Verbinding maken vanuit Python

In deze sectie gebruikt u een Python IDE (zoals IDLE) om te verwijzen naar gegevens die beschikbaar zijn in Azure Databricks. Voer voordat u begint de volgende vereisten in:

* Installeer Python vanaf [hier.](https://www.python.org/downloads/) Het installeren van Python via deze link installeert ook IDLE.

* Installeer het `pyodbc` pakket vanuit een opdrachtprompt op de computer. Voer de volgende opdracht uit:

      pip install pyodbc

Open IDLE en doe de volgende stappen:

- Importeer `pyodbc` het pakket. Hiermee u verbinding maken met Azure Databricks via de DSN die u eerder hebt gemaakt.
- Maak een verbinding met de DSN die u eerder hebt gemaakt.
-  Voer een SQL-query uit met de verbinding die u hebt gemaakt. In het volgende fragment is *radio_sample_data* een tabel die al bestaat in Azure Databricks.
- Voer bewerkingen uit op de query om de uitvoer te verifiëren.

In het volgende codefragment worden deze taken uitgevoerd:

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

* Zie [Gegevensbronnen voor Azure Databricks voor](/azure/databricks/data/data-sources/index) meer informatie over bronnen van waaruit u gegevens importeren in Azure Databricks


