---
title: Synapse Studio-notebooks
description: In dit artikel leert u hoe u Azure Synapse Studio-notitie blokken maakt en ontwikkelt om gegevens voor te bereiden en visualisatie te maken.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/01/2020
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: 2feaf33f7bc31396764bfbaa3ae6291b6752e961
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2020
ms.locfileid: "89612799"
---
# <a name="create-develop-and-maintain-synapse-studio-preview-notebooks-in-azure-synapse-analytics"></a>Synapse Studio-notebooks maken, ontwikkelen en onderhouden in azure Synapse Analytics

Een Synapse Studio-Notebook (preview-versie) is een webinterface waarmee u bestanden kunt maken die live code, visualisaties en tekst verhalen bevatten. Notebooks zijn een goede plaats om ideeën te valideren en snelle experimenten te gebruiken om inzicht te krijgen in uw gegevens. Notebooks worden ook veel gebruikt in gegevens voorbereiding, gegevens visualisatie, machine learning en andere scenario's voor Big data.

Met een Azure Synapse Studio-notebook kunt u het volgende doen:

* Ga aan de slag met de installatie van nul.
* Beveilig gegevens met ingebouwde beveiligings functies van het bedrijf.
* Gegevens analyseren over onbewerkte indelingen (CSV, txt, JSON, enzovoort), verwerkte bestands indelingen (Parquet, Delta Lake, ORC, enzovoort) en SQL-gegevens bestanden in tabel vorm tegen Spark en SQL.
* Blijf productief met verbeterde ontwerp mogelijkheden en ingebouwde gegevens visualisatie.

In dit artikel wordt beschreven hoe u notitie blokken gebruikt in azure Synapse Studio.

## <a name="create-a-notebook"></a>Een notebook maken

Er zijn twee manieren om een notitie blok te maken. U kunt een nieuw notitie blok maken of een bestaand notitie blok importeren in een Azure Synapse-werk ruimte vanuit het **objectverkenner**. Azure Synapse Studio-notebooks kunnen standaard Jupyter Notebook IPYNB-bestanden herkennen.

![Synapse-Create-import-notebook](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook.png)

## <a name="develop-notebooks"></a>Notebooks ontwikkelen

Notitie blokken bestaan uit cellen. Dit zijn afzonderlijke blokken code of tekst die onafhankelijk of als groep kunnen worden uitgevoerd.

### <a name="add-a-cell"></a>Een cel toevoegen

Er zijn meerdere manieren om een nieuwe cel aan uw notitie blok toe te voegen.

1. Vouw de knop linksboven **+ cel** uit en selecteer **code-cel toevoegen** of **tekst toevoegen**.

    ![add-cel-with-Cell-knop](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Beweeg de muis aanwijzer over de ruimte tussen twee cellen en selecteer **code toevoegen** of **tekst toevoegen**.

    ![add-cel: tussen ruimte](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. Gebruik sneltoetsen [onder de opdracht modus](#shortcut-keys-under-command-mode). Druk op **a** om een cel boven de huidige cel in te voegen. Druk op **B** om een cel onder de huidige cel in te voegen.

### <a name="set-a-primary-language"></a>Een primaire taal instellen

Azure Synapse Studio-notebooks ondersteunen vier Apache Spark talen:

* pySpark (python)
* Spark (scala)
* SparkSQL
* .NET voor Apache Spark (C#)

U kunt de primaire taal voor nieuwe toegevoegde cellen instellen in de vervolg keuzelijst in de bovenste opdracht balk.

   ![default-Synapse-taal](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Meerdere talen gebruiken

U kunt meerdere talen gebruiken in één notebook door de juiste Magic-opdracht voor de taal aan het begin van een cel op te geven. De volgende tabel bevat de Magic-opdrachten om te scha kelen tussen talen in cellen.

|Opdracht Magic |Taal | Beschrijving |  
|---|------|-----|
|%% pyspark| Python | Voer een **python** -query uit op Spark-context.  |
|% Spark| Scala | Voer een **scala** -query uit op Spark-context.  |  
|%% SQL| SparkSQL | Voer een **SparkSQL** -query uit op Spark-context.  |
|%% csharp | .NET voor Spark C # | Voer een **.net for Spark C#** -query uit op Spark-context. |

De volgende afbeelding is een voor beeld van hoe u een PySpark-query kunt schrijven met behulp van de opdracht **%% PySpark** Magic of een SparkSQL-query met de **%% SQL** Magic-opdracht in een **Spark (scala)-** notebook. U ziet dat de primaire taal voor het notitie blok is ingesteld op pySpark.

   ![Synapse-Spark-magics](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>Tijdelijke tabellen gebruiken om te verwijzen naar gegevens tussen talen

U kunt niet rechtstreeks verwijzen naar gegevens of variabelen in verschillende talen in een Synapse Studio-notebook. In Spark kan naar een tijdelijke tabel worden verwezen tussen talen. Hier volgt een voor beeld van het lezen van een `Scala` Data frame in `PySpark` en `SparkSQL` het gebruik van een Spark-tijdelijke tabel als tijdelijke oplossing.

1. In cel 1 leest u een data frame van de SQL-groeps connector met behulp van scala en maakt u een tijdelijke tabel.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.option("format", "DW connector predefined type")
   scalaDataFrame.registerTempTable( "mydataframetable" )
   ```

2. In cel 2 kunt u een query uitvoeren op de gegevens met behulp van Spark SQL.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. Gebruik in cel 3 de gegevens in PySpark.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IDE-stijl IntelliSense

Azure Synapse Studio-notebooks zijn geïntegreerd met de Monaco-editor om een IDE-stijl IntelliSense te bieden in de cel-editor. Met syntaxis markering, fout markering en automatische code voltooiingen kunt u code sneller schrijven en problemen identificeren.

De IntelliSense-functies bevinden zich op verschillende niveaus van de verval datum voor verschillende talen. Gebruik de onderstaande tabel om te zien wat er wordt ondersteund.

|Talen| Syntaxis markering | Syntaxis fout markering  | Syntaxis code volt ooien | Voltooiing van variabele code| Voltooiing van systeem functie code| Voltooiing van gebruikers functie code| Smart Indent | Code vouwen|
|--|--|--|--|--|--|--|--|--|
|PySpark (python)|Ja|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Spark (scala)|Ja|Ja|Ja|Ja|-|-|-|Ja|
|SparkSQL|Ja|Ja|-|-|-|-|-|-|
|.NET voor Spark (C#)|Yes|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>Tekst cel opmaken met werkbalk knoppen

U kunt de opmaak knoppen op de werk balk tekst cellen gebruiken om algemene kortings acties uit te voeren. Het bevat vette tekst, italicizing tekst, het invoegen van code fragmenten, het invoegen van een niet-geordende lijst, het invoegen van een geordende lijst en het invoegen van de afbeelding van de URL.

  ![Synapse: tekst-cel-werk balk](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

### <a name="undo-cell-operations"></a>Bewerkingen in een cel ongedaan maken
Klik op de knop **ongedaan maken** of druk op **CTRL + Z** om de meest recente bewerking van de cel in te trekken. Nu kunt u tot de laatste 20 historische acties ongedaan maken. 

   ![Synapse-ongedaan maken-cellen](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

### <a name="move-a-cell"></a>Een cel verplaatsen

Selecteer de weglatings tekens (...) om het menu met aanvullende celwaarden helemaal rechts te openen. Selecteer vervolgens **cel verplaatsen** of **omlaag verplaatsen** om de huidige cel te verplaatsen. 

U kunt ook sneltoetsen gebruiken [onder de opdracht modus](#shortcut-keys-under-command-mode). Druk op **CTRL + ALT + ↑** om de huidige cel omhoog te verplaatsen. Druk op **CTRL + ALT + ↓** om de huidige cel omlaag te verplaatsen.

   ![Move-a-cel](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

### <a name="delete-a-cell"></a>Een cel verwijderen

Als u een cel wilt verwijderen, selecteert u de weglatings tekens (...) om het menu met extra celwaarden helemaal rechts te openen en selecteert u vervolgens **cel verwijderen**. 

U kunt ook sneltoetsen gebruiken [onder de opdracht modus](#shortcut-keys-under-command-mode). Druk op **d, d** om de huidige cel te verwijderen.
  
   ![Delete-a-cel](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

### <a name="collapse-a-cell-input"></a>Een cel-invoer samen vouwen
Klik op de pijl knop aan de onderkant van de huidige cel om deze samen te vouwen. Als u deze wilt uitvouwen, klikt u op de pijl knop terwijl de cel wordt samengevouwen.

   ![samen vouwen-cel-invoer](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

### <a name="collapse-a-cell-output"></a>Een cel-uitvoer samen vouwen

Klik op de knop **uitvoer samen vouwen** in de linkerbovenhoek van de huidige cel uitvoer om deze samen te vouwen. Als u deze wilt uitvouwen, klikt u op de uitvoer van de **cel weer geven** terwijl de uitvoer van de cel wordt samengevouwen.

   ![samen vouwen-cel-uitvoer](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

## <a name="run-notebooks"></a>Notebooks gebruiken

U kunt de code cellen in uw notitie blok afzonderlijk of in één keer uitvoeren. De status en voortgang van elke cel worden weer gegeven in het notitie blok.

### <a name="run-a-cell"></a>Een cel uitvoeren

Er zijn verschillende manieren om de code in een cel uit te voeren.

1. Plaats de muis aanwijzer op de cel die u wilt uitvoeren en selecteer de knop **cel uitvoeren** of druk op **CTRL + ENTER**.

   ![Run-cel-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)


2. Selecteer de weglatings tekens (**...**) om het menu met de extra celwaarden helemaal rechts te openen. Selecteer vervolgens **cel uitvoeren**.

   ![Run-cel-2](./media/apache-spark-development-using-notebooks/synapse-run-cell-2.png)
   
3. Gebruik sneltoetsen [onder de opdracht modus](#shortcut-keys-under-command-mode). Druk op **SHIFT + ENTER** om de huidige cel uit te voeren en selecteer de cel hieronder. Druk op **ALT + ENTER** om de huidige cel uit te voeren en voeg hieronder een nieuwe cel in.


### <a name="run-all-cells"></a>Alle cellen uitvoeren
Klik op de knop **alles uitvoeren** om alle cellen in het huidige notitie blok in de juiste volg orde uit te voeren.

   ![run-alle-cellen](./media/apache-spark-development-using-notebooks/synapse-run-all.png)

### <a name="run-all-cells-above-or-below"></a>Alle cellen boven of onder uitvoeren

Selecteer de weglatings tekens (**...**) om het menu met de extra celwaarden helemaal rechts te openen. Selecteer vervolgens **cellen hierboven uitvoeren** om alle cellen boven de huidige in de reeks uit te voeren. Selecteer **onderstaande cellen uitvoeren** om alle cellen onder de huidige in de reeks uit te voeren.

   ![Run-cellen-boven-of-onder](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cancel-all-running-cells"></a>Alle actieve cellen annuleren
Klik op de knop **Alles annuleren** om de actieve cellen of cellen in de wachtrij te annuleren. 
   ![annuleren-alle cellen](./media/apache-spark-development-using-notebooks/synapse-cancel-all.png) 

### <a name="cell-status-indicator"></a>Indicator status van cel

Er wordt een stap-voor-stap-uitvoerings status weer gegeven onder de cel om u te helpen de huidige voortgang te zien. Zodra de uitvoering van de cel is voltooid, wordt een samen vatting van de uitvoering met de totale duur en eind tijd weer gegeven en bewaard voor toekomstige referentie.

![cel-status](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Voortgangs indicator Spark

Azure Synapse Studio notebook is louter Spark. Code cellen worden op afstand uitgevoerd in de Spark-groep. Er wordt een voortgangs indicator van Spark met een realtime voortgangs balk weer gegeven om u te helpen inzicht te krijgen in de status van de taak uitvoering.
Het aantal taken per taak of fase helpt u bij het identificeren van het parallelle niveau van uw Spark-taak. U kunt ook inzoomen op de Spark-gebruikers interface van een specifieke taak (of fase) door te klikken op de koppeling op de naam van de taak (of fase).


![vonk-voortgang-indicator](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Configuratie van Spark-sessie

U kunt de duur van de time-out, het nummer en de grootte van de uitvoerder opgeven om aan de huidige Spark-sessie te geven in de **sessie configureren**. De Spark-sessie moet opnieuw worden gestart om de configuratie wijzigingen van kracht te laten worden. Alle notebook-variabelen in de cache zijn gewist.

[![sessie beheer](./media/apache-spark-development-using-notebooks/synapse-spark-session-management.png)](./media/apache-spark-development-using-notebooks/synapse-spark-session-management.png#lightbox)

Er is nu een Spark-sessie aanbevolen voor het configuratie paneel van Spark Session. U kunt een Spark-groep rechtstreeks selecteren in het deel venster sessie configuratie en bekijken hoeveel knoop punten er worden gebruikt en hoeveel resterende uitvoerders er beschikbaar zijn. Deze informatie kan u helpen de sessie grootte op de juiste wijze in te stellen in plaats van deze weer te wijzigen.

![sessie-aanbevolen](./media/apache-spark-development-using-notebooks/synapse-spark-session-recommender.png)


## <a name="bring-data-to-a-notebook"></a>Gegevens naar een notitie blok brengen

U kunt gegevens laden uit Azure Blob Storage, Azure Data Lake Store gen 2 en SQL-pool, zoals wordt weer gegeven in de onderstaande code voorbeelden.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Een CSV lezen van Azure Data Lake Store Gen2 als Spark data frame

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (container_name, account_name, relative_path)

spark.conf.set("fs.azure.account.auth.type.%s.dfs.core.windows.net" %account_name, "SharedKey")
spark.conf.set("fs.azure.account.key.%s.dfs.core.windows.net" %account_name ,"Your ADLSg2 Primary Key")

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Een CSV van Azure Blob Storage lezen als Spark-data frame

```python

from pyspark.sql import SparkSession
from pyspark.sql.types import *

blob_account_name = "Your blob account name"
blob_container_name = "Your blob container name"
blob_relative_path = "Your blob relative path"
blob_sas_token = "Your blob sas token"

wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)

df = spark.read.option("header", "true") \
            .option("delimiter","|") \
            .schema(schema) \
            .csv(wasbs_path)

```

### <a name="read-data-from-the-primary-storage-account"></a>Gegevens uit het primaire opslag account lezen

U kunt rechtstreeks toegang krijgen tot gegevens in het primaire opslag account. Het is niet nodig om de geheime sleutels op te geven. Klik in Data Explorer met de rechter muisknop op een bestand en selecteer **Nieuw notitie blok** om een nieuw notitie blok met automatisch gegenereerde gegevens te bekijken.

![gegevens naar cel](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="visualize-data-in-a-notebook"></a>Gegevens in een notitie blok visualiseren

### <a name="produce-rendered-table-view"></a>Weer gave van gerenderde tabel genereren

Een weer gave met resultaten in tabel vorm wordt geleverd met de optie voor het maken van een staaf diagram, lijn diagram, cirkel diagram, spreidings diagram en vlak diagram. U kunt uw gegevens visualiseren zonder dat u code hoeft te schrijven. De grafieken kunnen worden aangepast in de **grafiek opties**. 

De uitvoer van **%% SQL** Magic-opdrachten wordt standaard weer gegeven in de gerenderde tabel weergave. U kunt <code>display(df)</code> een DataFrames-functie (RDD) voor de gedistribueerde tabel weer geven met Spark-of robuuste gegevens sets.

   [![Builtin-grafieken](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png)](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png#lightbox)

### <a name="visualize-built-in-charts-from-large-scale-dataset"></a>Ingebouwde grafieken visualiseren vanuit een grootschalige gegevensset 

De <code>display(df)</code> functie gebruikt standaard alleen de eerste 1000 rijen van de gegevens om de grafieken weer te geven. Controleer de **aggregatie over alle resultaten** en klik op de knop **Toep assen** . u gaat de grafiek genereren van de hele gegevensset. Een Spark-taak wordt geactiveerd wanneer de grafiek instelling wordt gewijzigd. het duurt even om de berekening te volt ooien en de grafiek weer te geven. 
    [![ingebouwd-grafieken-aggregatie-alle](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-aggregation-all.png)](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-aggregation-all.png#lightbox)


### <a name="visualize-data-statistic-information"></a>Gegevens statistiek gegevens visualiseren
U kunt gebruiken <code>display(df, summary = true)</code> om de statistische samen vatting van een gegeven Spark-data frame te controleren die de kolom naam, het kolom Type, de unieke waarden en ontbrekende waarden voor elke kolom bevatten. U kunt ook selecteren in een specifieke kolom om de minimale waarde, de maximale waarde, de gemiddelde waarde en de standaard afwijking weer te geven.
    [![ingebouwd-grafieken-samen vatting ](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-summary.png)](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-summary.png#lightbox)

### <a name="render-html-or-interactive-libraries"></a>HTML-of interactieve bibliotheken weer geven

U kunt HTML-of interactieve Bibliotheken, zoals **bokeh**, weer geven met behulp van **displayHTML ()**.

De volgende afbeelding is een voor beeld van het uitzetten van glyphs over een kaart met behulp van **bokeh**.

   ![bokeh-voor beeld](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png)
   

Voer de volgende voorbeeld code uit om de bovenstaande afbeelding te tekenen.

```python
from bokeh.plotting import figure, output_file
from bokeh.tile_providers import get_provider, Vendors
from bokeh.embed import file_html
from bokeh.resources import CDN
from bokeh.models import ColumnDataSource

tile_provider = get_provider(Vendors.CARTODBPOSITRON)

# range bounds supplied in web mercator coordinates
p = figure(x_range=(-9000000,-8000000), y_range=(4000000,5000000),
           x_axis_type="mercator", y_axis_type="mercator")
p.add_tile(tile_provider)

# plot datapoints on the map
source = ColumnDataSource(
    data=dict(x=[ -8800000, -8500000 , -8800000],
              y=[4200000, 4500000, 4900000])
)

p.circle(x="x", y="y", size=15, fill_color="blue", fill_alpha=0.8, source=source)

# create an html document that embeds the Bokeh plot
html = file_html(p, CDN, "my plot1")

# display this html
displayHTML(html)

```

## <a name="save-notebooks"></a>Notitie blokken opslaan

U kunt één notitie blok of alle notitie blokken in uw werk ruimte opslaan.

1. Als u de wijzigingen die u hebt aangebracht in één notitie blok wilt opslaan, selecteert u de knop **publiceren** op de opdracht balk van het notitie blok.

   ![publiceren-notebook](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Als u alle notitie blokken in uw werk ruimte wilt opslaan, selecteert u de knop **Alles publiceren** op de opdracht balk van de werk ruimte. 

   ![Alles publiceren](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

In de eigenschappen van het notitie blok kunt u configureren of de celinhoud moet worden meegenomen bij het opslaan.

   ![Notebook-eigenschappen](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Magic-opdrachten
U kunt uw vertrouwde Jupyter Magic-opdrachten gebruiken in azure Synapse Studio-notebooks. Controleer de onderstaande lijst als de huidige beschik bare Magic-opdrachten. Vertel ons [uw use cases op github](https://github.com/MicrosoftDocs/azure-docs/issues/new) zodat we meer Magic-opdrachten kunnen blijven bouwen om aan uw behoeften te voldoen.

Beschik bare lijn-magices: [% lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [% time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [% time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Beschik bare cel magics: [%% time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%% Capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%% Write File](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%% SQL](#use-multiple-languages), [%% pyspark](#use-multiple-languages), [%% Spark](#use-multiple-languages), [%% csharp](#use-multiple-languages)


## <a name="orchestrate-notebook"></a>Notitie blok indelen

### <a name="add-a-notebook-to-a-pipeline"></a>Een notitie blok toevoegen aan een pijp lijn

Klik op de knop **toevoegen aan pijp lijn** in de rechter bovenhoek om een notitie blok toe te voegen aan een bestaande pijp lijn of een nieuwe pijp lijn te maken.

![toevoegen aan pijp lijn](./media/apache-spark-development-using-notebooks/add-to-pipeline.png)

### <a name="designate-a-parameters-cell"></a>Een cel met para meters aanwijzen

Als u uw notitie blok wilt para meters, selecteert u de weglatings tekens (...) om het menu met extra celwaarden helemaal rechts te openen. Selecteer vervolgens de cel **Toggle para meter** om de cel aan te wijzen als de para meters-cel.

![Toggle-para meter](./media/apache-spark-development-using-notebooks/toggle-parameter-cell.png)

Azure Data Factory zoekt naar de para meters-cel en behandelt deze cel als standaard waarden voor de para meters die tijdens de uitvoerings tijd worden door gegeven. Uitvoerings engine voegt een nieuwe cel onder de cel para meters met invoer parameters toe om de standaard waarden te overschrijven. Als er geen para meters-cel wordt opgegeven, wordt de geïnjecteerde cel boven aan het notitie blok ingevoegd.

### <a name="assign-parameters-values-from-a-pipeline"></a>Parameter waarden toewijzen vanuit een pijp lijn

Zodra u een notitie blok met para meters hebt gemaakt, kunt u het uitvoeren vanuit een pijp lijn met de Azure Synapse-notebook activiteit. Nadat u de activiteit aan uw pijplijn doek hebt toegevoegd, kunt u de parameter waarden instellen in het gedeelte **basis parameters** op het tabblad **instellingen** . 

![Assign-para meter](./media/apache-spark-development-using-notebooks/assign-parameter.png)

Wanneer u parameter waarden toewijst, kunt u de taal van de [pijplijn expressie](../../data-factory/control-flow-expression-language-functions.md) of [systeem variabelen](../../data-factory/control-flow-system-variables.md)gebruiken.



## <a name="shortcut-keys"></a>Sneltoetsen

Net als Jupyter-notebooks hebben Azure Synapse Studio-notebooks een modale gebruikers interface. Het toetsen bord heeft verschillende dingen, afhankelijk van de modus waarin de notebook-cel zich bevindt. Synapse Studio-notitie blokken ondersteunen de volgende twee modi voor een bepaalde code-cel: opdracht modus en bewerkings modus.

1. Een cel bevindt zich in de opdracht modus als er geen tekst cursor wordt gevraagd om te typen. Wanneer een cel zich in de opdracht modus bevindt, kunt u het notitie blok als geheel bewerken, maar niet typen in afzonderlijke cellen. Voer de opdracht modus in door te drukken `ESC` of door met de muis te klikken buiten het editor gebied van de cel.

   ![opdracht modus](./media/apache-spark-development-using-notebooks/synapse-command-mode2.png)

2. De bewerkings modus wordt aangegeven door een tekst cursor waarin u wordt gevraagd in het gebied van de editor te typen. Wanneer een cel zich in de bewerkings modus bevindt, kunt u in de cel typen. Voer de bewerkings modus in door `Enter` met de muis te klikken op het editor gebied van een cel.
   
   ![bewerkingsmodus](./media/apache-spark-development-using-notebooks/synapse-edit-mode2.png)

### <a name="shortcut-keys-under-command-mode"></a>Sneltoetsen onder de opdracht modus

Met behulp van de volgende sneltoetsen kunt u eenvoudig navigeren en code uitvoeren in azure Synapse-notebooks.

| Actie |Synapse Studio-notebook-snelkoppelingen  |
|--|--|
|Voer de huidige cel uit en selecteer hieronder | SHIFT + ENTER |
|De huidige cel uitvoeren en onder invoegen | ALT + ENTER |
|Cel hierboven selecteren| Omhoog |
|Selecteer de cel onder| Omlaag |
|Cel boven invoegen| A |
|Cel onder invoegen| B |
|Geselecteerde cellen hierboven uitbreiden| Shift + omhoog |
|Geselecteerde cellen hieronder uitbreiden| Shift + pijl-omlaag|
|Cel omhoog verplaatsen| CTRL + ALT + ↑ |
|Cel omlaag verplaatsen| CTRL + ALT + ↓ |
|Geselecteerde cellen verwijderen| D, D |
|Overschakelen naar de bewerkings modus| Enter |

### <a name="shortcut-keys-under-edit-mode"></a>Sneltoetsen in de bewerkings modus

Met de volgende sneltoetsen kunt u gemakkelijker code in azure Synapse-notebooks navigeren en uitvoeren in de bewerkings modus.

| Actie |Synapse Studio-notebook-snelkoppelingen  |
|--|--|
|Cursor omhoog verplaatsen | Omhoog |
|Cursor omlaag verplaatsen|Omlaag|
|Ongedaan maken|CTRL + Z|
|Opnieuw uitvoeren|CTRL + Y|
|Opmerking/Opmerking opheffen|CTRL +/|
|Woord vóór verwijderen|CTRL + BACKSPACE|
|Woord verwijderen na|CTRL + DELETE|
|Naar begin van cel|Ctrl + Home|
|Naar einde van cel |Ctrl + End|
|Eén woord naar links gaan|Ctrl + pijl-links|
|Eén woord naar rechts gaan|CTRL + rechts|
|Alles selecteren|CTRL + A|
|Indent| CTRL +]|
|Inspringing verkleinen|CTRL + [|
|Overschakelen naar de opdracht modus| Esc |

## <a name="next-steps"></a>Volgende stappen
- [Voor beelden van Synapse-voorbeeld notitieblokken bekijken](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Quickstart: Een Apache Spark-pool (preview) maken in Azure Synapse Analytics met behulp van webhulpprogramma's](../quickstart-apache-spark-notebook.md)
- [Wat is Apache Spark in Azure Synapse Analytics?](apache-spark-overview.md)
- [.NET gebruiken voor Apache Spark met Azure Synapse Analytics](spark-dotnet.md)
- [Documentatie voor .NET voor Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
