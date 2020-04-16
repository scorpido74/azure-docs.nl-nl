---
title: Azure Synapse Studio -laptops (preview)-notitieblokken maken, ontwikkelen en onderhouden
description: In dit artikel leert u hoe u Azure Synapse Studio-notitieblokken (preview)-notitieblokken maakt en ontwikkelt om gegevensvoorbereiding en visualisatie uit te brengen.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: ruxu
ms.reviewer: ''
ms.openlocfilehash: 506339cefa90fb17bedfc946f70cb4d7d8047cf2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430225"
---
# <a name="create-develop-and-maintain-azure-synapse-studio-preview-notebooks"></a>Azure Synapse Studio -laptops (preview)-notitieblokken maken, ontwikkelen en onderhouden

Een Azure Synapse Studio (preview) notebook is een webinterface waarmee u bestanden maken die live code, visualisaties en verhalende tekst bevatten. Notebooks zijn een goede plek om ideeën te valideren en snelle experimenten te gebruiken om inzichten uit uw gegevens te krijgen. Notebooks worden ook veel gebruikt in gegevensvoorbereiding, datavisualisatie, machine learning en andere Big Data-scenario's.

Met een Azure Synapse Studio-laptop u:

* Ga aan de slag met nul installatie-inspanning.
* Houd gegevens veilig met ingebouwde bedrijfsbeveiligingsfuncties.
* Analyseer gegevens over raw-indelingen (CSV, txt, JSON, enz.), verwerkte bestandsindelingen (parket, Delta Lake, ORC, enz.) en SQL-tabelgegevensbestanden tegen Spark en SQL.
* Wees productief met verbeterde ontwerpmogelijkheden en ingebouwde gegevensvisualisatie.

In dit artikel wordt beschreven hoe u notitieblokken gebruikt in Azure Synapse Studio.

## <a name="create-a-notebook"></a>Een notebook maken

Er zijn twee manieren om een notitieblok te maken. U een nieuw notitieblok maken of een bestaand notitieblok importeren in een Azure Synapse-werkruimte vanuit de **Object Explorer**. Azure Synapse Studio-laptops kunnen standaard Jupyter Notebook IPYNB-bestanden herkennen.

![synaps-create-import-notebook](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook.png)

## <a name="develop-notebooks"></a>Notitieblokken ontwikkelen

Notitieblokken bestaan uit cellen, die afzonderlijke blokken code of tekst zijn die onafhankelijk of als groep kunnen worden uitgevoerd.

### <a name="add-a-cell"></a>Een cel toevoegen

Er zijn meerdere manieren om een nieuwe cel aan uw notitieblok toe te voegen.

1. Vouw de knop linksboven **+ cel** uit en selecteer **Codecel toevoegen** of **Tekstcel toevoegen**.

    ![knop add-cell-with-cell](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Plaats de ruimte tussen twee cellen en selecteer **Code toevoegen** of **Tekst toevoegen**.

    ![invoegcel-tussen-ruimte](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. Sneltoetsen [gebruiken onder de opdrachtmodus](#shortcut-keys-under-command-mode). Druk op **A** om een cel boven de huidige cel in te voegen. Druk op **B** om een cel onder de huidige cel in te voegen.

### <a name="set-a-primary-language"></a>Een primaire taal instellen

Azure Synapse Studio-notitieblokken ondersteunen vier spark-talen:

* pyspark (python)
* vonk (Scala)
* sparkSQL
* Spark.NET (C#)

U de primaire taal instellen voor nieuwe toegevoegde cellen vanuit de vervolgkeuzelijst in de bovenste opdrachtbalk.

   ![standaardsynapstaal](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Meerdere talen gebruiken

U meerdere talen in één notitieblok gebruiken door aan het begin van een cel de juiste taaltoverstokopdracht op te geven. In de volgende tabel worden de magische opdrachten weergegeven om van celtaal te wisselen.

|Magisch, opdracht |Taal | Beschrijving |  
|---|------|-----|
|%%pyspark| Python | Voer een **Python-query** uit tegen Spark Context.  |
|%%spark| Scala | Voer een **Scala-query** uit tegen Spark Context.  |  
|%%sql| SparkSQL | Voer een **SparkSQL-query** uit tegen Spark Context.  |
|%%csharp | Spark.NET C # | Voer een **Spark.NET C#-query** uit tegen Spark-context. |

De volgende afbeelding is een voorbeeld van hoe u een PySpark-query schrijven met de magische opdracht **%%pyspark** of een SparkSQL-query met de opdracht **%%sql-magie** in een **Spark(Scala)-notitieblok.** De primaire taal voor het notitieblok is ingesteld op Scala.

   ![synaps-spark-magics](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>Tijdelijke tabellen gebruiken om gegevens in verschillende talen te verwijzen

U gegevens of variabelen niet rechtstreeks in verschillende talen in een Synapse Studio-notitieblok verwijzen. In Spark kan naar een tijdelijke tabel worden verwezen in verschillende talen. Hier volgt een voorbeeld van `Scala` het `PySpark` lezen `SparkSQL` van een DataFrame in en het gebruik van een Spark temp-tabel als tijdelijke oplossing.

1. Lees in Cel 1 een DataFrame van SQL-poolconnector met Scala en maak een tijdelijke tabel.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.option("format", "DW connector predefined type")
   scalaDataFrame.registerTempTable( "mydataframetable" )
   ```

2. Queryeren in Cel 2 met Spark SQL.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. Gebruik in Cel 3 de gegevens in PySpark.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IDE-stijl IntelliSense

Azure Synapse Studio-laptops zijn geïntegreerd met de Monaco-editor om IDE-stijl IntelliSense naar de celeditor te brengen. Syntaxismarkering, foutmaker en automatische codevoltooiingen helpen u om code te schrijven en problemen sneller te identificeren.

De IntelliSense-functies zijn op verschillende niveaus van volwassenheid voor verschillende talen. Gebruik de onderstaande tabel om te zien wat er wordt ondersteund.

|Talen| Syntaxishooglicht | Syntaxisfoutmarkering  | Voltooiing syntaxiscode | Voltooiing van variabele code| Voltooiing van systeemfunctiecode| Voltooiing van de gebruikersfunctiecode| Smart Indent | Code vouwen|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|Ja|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Vonk (Scala)|Ja|Ja|Ja|Ja|-|-|-|Ja|
|SparkSQL|Ja|Ja|-|-|-|-|-|-|
|Spark.NET (C#)|Ja|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>Tekstcel opmaken met werkbalkknoppen

U de opmaakknoppen op de werkbalk tekstcellen gebruiken om algemene afwaarderingsacties uit te voeren. Het bevat vetmakende tekst, cursief tekst, het invoegen van codefragmenten, het invoegen van ongeordende lijst, het invoegen van de geordende lijst en het invoegen van afbeelding van URL.

  ![synaps-tekst-celwerkbalk](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

### <a name="undo-cell-operations"></a>Celbewerkingen ongedaan maken
Klik op de knop **Ongedaan maken** of druk op **Ctrl+Z** om de meest recente celbewerking in te trekken. Nu u tot de laatste 20 historische celacties ongedaan maken. 

   ![synaps-ongedaan maken-cellen](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

### <a name="move-a-cell"></a>Een cel verplaatsen

Selecteer de ellipsen (...) om toegang te krijgen tot het menu Extra celacties helemaal rechts. Selecteer vervolgens **Cel omhoog** of Cel **omlaag verplaatsen** om de huidige cel te verplaatsen. 

U ook sneltoetsen gebruiken [onder de opdrachtmodus](#shortcut-keys-under-command-mode). Druk op **Ctrl+Alt+↑** om de huidige cel omhoog te gaan. Druk **op Ctrl+Alt+∙** om de huidige cel naar beneden te verplaatsen.

   ![move-a-cell](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

### <a name="delete-a-cell"></a>Een cel verwijderen

Als u een cel wilt verwijderen, selecteert u de ellips (...) om toegang te krijgen tot het menu Extra celacties helemaal rechts en selecteert u **Cel verwijderen**. 

U ook sneltoetsen gebruiken [onder de opdrachtmodus](#shortcut-keys-under-command-mode). Druk op **D, D** om de huidige cel te verwijderen.
  
   ![delete-a-cell](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

### <a name="collapse-a-cell-input"></a>Een celinvoer samenvouwen
Klik op de pijlknop onder aan de huidige cel om deze samen te vouwen. Als u deze wilt uitbreiden, klikt u op de pijlknop terwijl de cel is samengevouwen.

   ![insamenvouwen-celinvoer](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

### <a name="collapse-a-cell-output"></a>Een celuitvoer samenvouwen

Klik op de **uitvoerknop samenvouwen** linksboven in de huidige celuitvoer om deze samen te vouwen. Als u deze wilt uitbreiden, klikt u op de **celuitvoer weergeven** terwijl de celuitvoer is samengevouwen.

   ![samenvouwen-cel-uitvoer](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

## <a name="run-notebooks"></a>Notitieblokken uitvoeren

U de codecellen in uw notitieblok afzonderlijk of in één keer uitvoeren. De status en voortgang van elke cel wordt weergegeven in het notitieblok.

### <a name="run-a-cell"></a>Een cel uitvoeren

Er zijn verschillende manieren om de code in een cel uit te voeren.

1. Plaats de plaats in de cel die u wilt uitvoeren en selecteer de knop **Cel uitvoeren** of druk op **Ctrl+Enter**.

   ![run-cell-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)


2. Als u toegang wilt krijgen tot het menu Extra celacties helemaal rechts, selecteert u de ellipsen (**... ).** Selecteer vervolgens **Cel uitvoeren**.

   ![run-cell-2](./media/apache-spark-development-using-notebooks/synapse-run-cell-2.png)
   
3. Sneltoetsen [gebruiken onder de opdrachtmodus](#shortcut-keys-under-command-mode). Druk op **Shift+Enter** om de huidige cel uit te voeren en selecteer de cel hieronder. Druk op **Alt+Enter** om de huidige cel uit te voeren en voeg hieronder een nieuwe cel in.


### <a name="run-all-cells"></a>Alle cellen uitvoeren
Klik **op** de knop Alles uitvoeren om alle cellen in het huidige notitieblok achter elkaar uit te voeren.

   ![run-all-cells](./media/apache-spark-development-using-notebooks/synapse-run-all.png)

### <a name="run-all-cells-above-or-below"></a>Alle cellen boven of onder uitvoeren

Als u toegang wilt krijgen tot het menu Extra celacties helemaal rechts, selecteert u de ellipsen (**... ).** Selecteer vervolgens **Cellen hierboven uitvoeren** om alle cellen boven de huidige reeks uit te voeren. Selecteer **Cellen hieronder uitvoeren** om alle cellen onder de huidige reeks uit te voeren.

   ![run-cells-above-or-below](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cell-status-indicator"></a>Celstatusindicator

Er wordt een stapsgewijze celuitvoeringsstatus weergegeven onder de cel om u te helpen de huidige voortgang te zien. Zodra de celrun is voltooid, wordt een uitvoeringsstandmet de totale duur en eindtijd weergegeven en daar bewaard voor toekomstige referentie.

![celstatus](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Voortgangsindicator spark

Azure Synapse Studio-laptop is puur gebaseerd op Spark. Codecellen worden op afstand uitgevoerd op de Spark-pool. Er wordt een spark-taakvoortgangsindicator weergegeven met een realtime voortgangsbalk om u inzicht te geven in de status van de taakuitvoering.


![spark-progress-indicator](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Spark-sessieconfig

U de time-outduur, het aantal en de grootte van uitvoerders opgeven die u aan de huidige Spark-sessie moet geven in **Sessie configureren**. Start de Spark-sessie opnieuw op, omdat configuratiewijzigingen van kracht worden. Alle notitieblokvariabelen in de cache worden gewist.

![sessie-mgmt](./media/apache-spark-development-using-notebooks/synapse-spark-session-mgmt.png)


## <a name="bring-data-to-a-notebook"></a>Gegevens naar een notitieblok brengen

U gegevens laden uit Azure Blob Storage, Azure Data Lake Store Gen 2 en SQL-groep, zoals weergegeven in de onderstaande codevoorbeelden.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Lees een CSV uit Azure Data Lake Store Gen2 als Spark DataFrame

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (blob_container_name, blob_account_name,  blob_relative_path)

spark.conf.set("fs.azure.account.auth.type.%s.dfs.core.windows.net" %account_name, "SharedKey")
spark.conf.set("fs.azure.account.key.%s.dfs.core.windows.net" %account_name ,"Your ADLSg2 Primary Key")

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Een CSV uit Azure Blob Storage lezen als Spark DataFrame

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

### <a name="read-data-from-the-primary-storage-account"></a>Gegevens uit het primaire opslagaccount lezen

U hebt rechtstreeks toegang tot gegevens in het primaire opslagaccount. Het is niet nodig om de geheime sleutels te verstrekken. Klik in Gegevensverkenner met de rechtermuisknop op een bestand en selecteer **Nieuw notitieblok** om een nieuw notitieblok met gegevensextractor automatisch te bekijken.

![gegevens naar cel](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="visualize-data-in-a-notebook"></a>Gegevens in een notitieblok visualiseren

### <a name="display"></a>Weergave()

Een tabelresultatenweergave is voorzien van de optie om een staafdiagram, lijndiagram, cirkeldiagram, spreidingsdiagram en gebiedsdiagram te maken. U uw gegevens visualiseren zonder code te hoeven schrijven. De grafieken kunnen worden aangepast in de **grafiekopties**. 

De uitvoer van **sql-magische** opdrachten wordt standaard weergegeven in de weergegeven tabelweergave. U **display`<DataFrame name>`aanroepen** op spark dataframes of de functie Resilient Distributed Datasets (RDD) om de weergegeven tabelweergave te produceren.

   ![ingebouwde grafieken](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png)

### <a name="displayhtml"></a>DisplayHTML()

U HTML of interactieve bibliotheken, zoals **bokeh,** renderen met behulp van de **displayHTML()**.

De volgende afbeelding is een voorbeeld van het plotten van glyphs over een kaart met **bokeh**.

   ![bokeh-voorbeeld](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png)
   

Voer de volgende voorbeeldcode uit om de bovenstaande afbeelding te tekenen.

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

## <a name="save-notebooks"></a>Notitieblokken opslaan

U één notitieblok of alle notitieblokken opslaan in uw werkruimte.

1. Als u wijzigingen wilt opslaan die u in één notitieblok hebt aangebracht, selecteert u de knop **Publiceren** op de opdrachtbalk van het notitieblok.

   ![publicatie-notitieblok](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Als u alle notitieblokken in uw werkruimte wilt opslaan, selecteert u de knop **Alle publiceren** op de opdrachtbalk van de werkruimte. 

   ![publiceren-allen](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

In de eigenschappen van het notitieblok u configureren of u de celuitvoer wilt opnemen bij het opslaan.

   ![eigenschappen van notitieblokken](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Magische commando's
U uw vertrouwde Jupyter-magische opdrachten gebruiken in Azure Synapse Studio-notitieblokken. Bekijk de onderstaande lijst als de huidige beschikbare magische commando's. Vertel ons uw use cases op GitHub, zodat we meer magische commando's kunnen blijven uitbouwen om aan uw behoeften te voldoen.

Beschikbare lijn magics: [%lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Beschikbare celmagie: [%%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%%capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%%sql](#use-multiple-languages), [%%pyspark](#use-multiple-languages), [%%spark](#use-multiple-languages), [%%csharp](#use-multiple-languages)

## <a name="shortcut-keys"></a>Sneltoetsen

Net als bij Jupyter-laptops hebben Azure Synapse Studio-laptops een modale gebruikersinterface. Het toetsenbord doet verschillende dingen, afhankelijk van in welke modus de notebookcel zich bevindt. Synapse Studio-notebooks ondersteunen de volgende twee modi voor een bepaalde codecel: de opdrachtmodus en de bewerkingsmodus.

1. Een cel bevindt zich in de opdrachtmodus wanneer er geen tekstcursor is die u vraagt te typen. Wanneer een cel in de opdrachtmodus staat, u het notitieblok als geheel bewerken, maar niet typen in afzonderlijke cellen. Voer de opdrachtmodus in door op `ESC` of met de muis te klikken buiten het editorgebied van een cel.

   ![opdrachtmodus](./media/apache-spark-development-using-notebooks/synapse-command-mode2.png)

2. De bewerkingsmodus wordt aangegeven door een tekstcursor die u vraagt om het editorgebied in te typen. Wanneer een cel in de bewerkingsmodus staat, u het typen in de cel niet typen. Voer de bewerkingsmodus `Enter` in door op de muis te drukken of de muis te gebruiken om op het editorgebied van een cel te klikken.
   
   ![bewerkingsmodus](./media/apache-spark-development-using-notebooks/synapse-edit-mode2.png)

### <a name="shortcut-keys-under-command-mode"></a>Sneltoetsen onder opdrachtmodus

Met de volgende toetsaanslagen u eenvoudiger navigeren en code uitvoeren in Azure Synapse-notitieblokken.

| Actie |Sneltoetsen voor Synapse Studio-notitieblok  |
|--|--|
|De huidige cel uitvoeren en hieronder selecteren | Shift+Enter |
|De huidige cel uitvoeren en hieronder invoegen | Alt+Enter |
|Cel boven selecteren| Omhoog |
|Cel hieronder selecteren| Omlaag |
|Cel boven invoegen| A |
|Cel hieronder invoegen| B |
|Geselecteerde cellen hierboven uitbreiden| Shift+Omhoog |
|Geselecteerde cellen hieronder uitbreiden| Shift+Omlaag|
|Cel omhoog verplaatsen| Ctrl+Alt+↑ |
|Cel omlaag verplaatsen| Ctrl+Alt+∙ |
|Geselecteerde cellen verwijderen| D, D |
|Overschakelen naar de bewerkingsmodus| Enter |

### <a name="shortcut-keys-under-edit-mode"></a>Sneltoetsen onder bewerkingsmodus

Met de volgende toetsaanslagen u gemakkelijker navigeren en code uitvoeren in Azure Synapse-notitieblokken in de bewerkingsmodus.

| Actie |Sneltoetsen voor Synapse Studio-notitieblokken  |
|--|--|
|Cursor omhoog verplaatsen | Omhoog |
|Cursor omlaag verplaatsen|Omlaag|
|Ongedaan maken|Ctrl+Z|
|Opnieuw uitvoeren|Ctrl+Y|
|Opmerking/opmerking maken|Ctrl+/|
|Woord verwijderen vóór|Ctrl+Backspace|
|Woord verwijderen na|Ctrl +Verwijderen|
|Naar celstart gaan|Ctrl+Start|
|Naar het celeinde gaan |Ctrl+Einde|
|Eén woord naar links gaan|Ctrl+links|
|Eén woord naar rechts gaan|Ctrl+Rechts|
|Alles selecteren|Ctrl+A|
|Streepje| Ctrl+]|
|Dedent (Dedent)|Ctrl+ [|
|Overschakelen naar de opdrachtmodus| Esc |

## <a name="next-steps"></a>Volgende stappen

- [.NET voor Apache Spark-documentatie](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
