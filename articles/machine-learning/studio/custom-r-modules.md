---
title: Aangepaste R-modules definiëren
titleSuffix: ML Studio (classic) - Azure
description: In dit onderwerp wordt beschreven hoe u een aangepaste R Studio (klassiek) schrijven en implementeren. Het legt uit wat aangepaste R-modules zijn en welke bestanden worden gebruikt om ze te definiëren.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: 5b8dab14a9416795eccef1f71988a048c8bedb48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218168"
---
# <a name="define-custom-r-modules-for-azure-machine-learning-studio-classic"></a>Aangepaste R-modules definiëren voor Azure Machine Learning Studio (klassiek)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

In dit onderwerp wordt beschreven hoe u een aangepaste R Studio (klassiek) schrijven en implementeren. Het legt uit wat aangepaste R-modules zijn en welke bestanden worden gebruikt om ze te definiëren. Het illustreert hoe u de bestanden maken die een module definiëren en hoe u de module registreert voor implementatie in een Machine Learning-werkruimte. De elementen en kenmerken die in de definitie van de aangepaste module worden gebruikt, worden vervolgens nader beschreven. Hoe u extra functionaliteit en bestanden en meerdere uitgangen gebruikt wordt ook besproken. 



## <a name="what-is-a-custom-r-module"></a>Wat is een aangepaste R-module?
Een **aangepaste module** is een door de gebruiker gedefinieerde module die kan worden geüpload naar uw werkruimte en kan worden uitgevoerd als onderdeel van het Azure Machine Learning Studio (klassiek) experiment. Een **aangepaste R-module** is een aangepaste module die een door de gebruiker gedefinieerde R-functie uitvoert. **R** is een programmeertaal voor statistische computing en afbeeldingen die op grote schaal wordt gebruikt door statistici en data scientists voor het implementeren van algoritmen. Momenteel is R de enige taal die wordt ondersteund in aangepaste modules, maar ondersteuning voor extra talen is gepland voor toekomstige releases.

Aangepaste modules hebben **eersteklas status** in Azure Machine Learning Studio (klassiek) in de zin dat ze kunnen worden gebruikt net als elke andere module. Ze kunnen worden uitgevoerd met andere modules, opgenomen in gepubliceerde experimenten of in visualisaties. U hebt controle over het algoritme dat door de module wordt geïmplementeerd, de invoer- en uitvoerpoorten die moeten worden gebruikt, de modelleringsparameters en andere verschillende runtime-gedragingen. Een experiment met aangepaste modules kan ook worden gepubliceerd in de Azure AI-galerie voor eenvoudig delen.

## <a name="files-in-a-custom-r-module"></a>Bestanden in een aangepaste R-module
Een aangepaste R-module wordt gedefinieerd door een .zip-bestand dat ten minste twee bestanden bevat:

* Een **bronbestand** dat de R-functie implementeert die door de module wordt weergegeven
* Een **XML-definitiebestand** dat de aangepaste module-interface beschrijft

Extra extra bestanden kunnen ook worden opgenomen in het .zip-bestand dat functionaliteit biedt die toegankelijk is vanuit de aangepaste module. Deze optie wordt besproken in het gedeelte **Argumenten** van de verwijzingssectie **Elementen in het XML-definitiebestand** volgens het voorbeeld snelstart.

## <a name="quickstart-example-define-package-and-register-a-custom-r-module"></a>Snelstartvoorbeeld: een aangepaste R-module definiëren, verpakken en registreren
In dit voorbeeld wordt uitgelegd hoe u de bestanden samenstellen die vereist zijn door een aangepaste R-module, ze verpakken in een zip-bestand en de module vervolgens registreren in uw Machine Learning-werkruimte. Het voorbeeld zip-pakket en voorbeeldbestanden kunnen worden gedownload van [download CustomAddRows.zip-bestand](https://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409).

## <a name="the-source-file"></a>Het bronbestand
Overweeg het voorbeeld van een module **Aangepaste rijen toevoegen** die de standaardimplementatie wijzigt van de module Rijen **toevoegen** die wordt gebruikt om rijen (observaties) uit twee gegevenssets (gegevensframes) te verbinden. De standaardmodule **Rijen toevoegen** voegt de rijen van de tweede invoergegevensset `rbind` toe aan het einde van de eerste invoergegevensset met behulp van het algoritme. De `CustomAddRows` aangepaste functie accepteert ook twee gegevenssets, maar accepteert ook een Booleaanse swapparameter als extra invoer. Als de swapparameter is ingesteld op **FALSE,** retourneert deze dezelfde gegevensset als de standaardimplementatie. Maar als de swapparameter **WAAR**is, voegt de functie rijen van de eerste invoergegevensset toe aan het einde van de tweede gegevensset. Het bestand CustomAddRows.R dat de `CustomAddRows` implementatie van de R-functie bevat die wordt weergegeven door de module Rijen toevoegen met **aangepaste toevoegen,** heeft de volgende R-code.

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) 
    {
        if (swap)
        {
            return (rbind(dataset2, dataset1));
        }
        else
        {
            return (rbind(dataset1, dataset2));
        } 
    } 

### <a name="the-xml-definition-file"></a>Het XML-definitiebestand
Als u `CustomAddRows` deze functie als de module Azure Machine Learning Studio (klassieke) wilt blootstellen, moet een XML-definitiebestand worden gemaakt om op te geven hoe de module **Aangepaste rijen toevoegen** eruit moet zien en zich moet gedragen. 

    <!-- Defined a module using an R Script -->
    <Module name="Custom Add Rows">
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another. Dataset 2 is concatenated to Dataset 1 when Swap is FALSE, and vice versa when Swap is TRUE.</Description>

    <!-- Specify the base language, script file and R function to use for this module. -->        
        <Language name="R" 
         sourceFile="CustomAddRows.R" 
         entryPoint="CustomAddRows" />  

    <!-- Define module input and output ports -->
    <!-- Note: The values of the id attributes in the Input and Arg elements must match the parameter names in the R Function CustomAddRows defined in CustomAddRows.R. -->
        <Ports>
            <Input id="dataset1" name="Dataset 1" type="DataTable">
                <Description>First input dataset</Description>
            </Input>
            <Input id="dataset2" name="Dataset 2" type="DataTable">
                <Description>Second input dataset</Description>
            </Input>
            <Output id="dataset" name="Dataset" type="DataTable">
                <Description>The combined dataset</Description>
            </Output>
        </Ports>

    <!-- Define module parameters -->
        <Arguments>
            <Arg id="swap" name="Swap" type="bool" >
                <Description>Swap input datasets.</Description>
            </Arg>
        </Arguments>
    </Module>


Het is van cruciaal belang op te merken dat de waarde van de **id-kenmerken** van de **invoer-** en **argelementen** in het XML-bestand moet overeenkomen met de functieparameternamen van de R-code in het bestand CustomAddRows.R PRECIES: (*gegevensset1*, *gegevensset2*en *swap* in het voorbeeld). Ook moet de waarde van het **entryPoint-kenmerk** van het element **Taal** overeenkomen met de naam van de functie in het R-script PRECIES: (*CustomAddRows* in het voorbeeld). 

Het **id-kenmerk** voor het element **Uitvoer** komt daarentegen niet overeen met variabelen in het R-script. Wanneer er meer dan één uitvoer vereist is, retourneert u eenvoudig een lijst van de R-functie met resultaten *die in dezelfde volgorde* zijn geplaatst als **uitvoerelementen,** worden gedeclareerd in het XML-bestand.

### <a name="package-and-register-the-module"></a>Pakket en registreer de module
Sla deze twee bestanden op als *CustomAddRows.R* en *CustomAddRows.xml* en rits de twee bestanden samen in een *bestand CustomAddRows.zip.*

Als u ze wilt registreren in uw Machine Learning-werkruimte, gaat u naar uw werkruimte in Azure Machine Learning Studio (klassiek), klikt u op de **+NIEUWE** knop onderaan en kiest **u MODULE-> VAN ZIP-PAKKET** om de nieuwe module **Aangepaste rijen toevoegen** te uploaden.

![Zip uploaden](./media/custom-r-modules/upload-from-zip-package.png)

De module **Aangepaste rijen toevoegen** is nu klaar om te worden geopend door uw Machine Learning-experimenten.

## <a name="elements-in-the-xml-definition-file"></a>Elementen in het XML-definitiebestand
### <a name="module-elements"></a>Module-elementen
Het **element Module** wordt gebruikt om een aangepaste module in het XML-bestand te definiëren. Meerdere modules kunnen in één XML-bestand worden gedefinieerd met meerdere **module-elementen.** Elke module in uw werkruimte moet een unieke naam hebben. Registreer een aangepaste module met dezelfde naam als een bestaande aangepaste module en vervangt de bestaande module door de nieuwe. Aangepaste modules kunnen echter worden geregistreerd met dezelfde naam als een bestaande Azure Machine Learning Studio (klassieke) module. Als dit het zo is, worden ze weergegeven in de categorie **Aangepast** van het modulepalet.

    <Module name="Custom Add Rows" isDeterministic="false"> 
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another...</Description>/> 


Binnen het element **Module** u twee extra optionele elementen opgeven:

* een **eigenaarelement** dat in de module is ingesloten  
* een **beschrijvingselement** dat tekst bevat die in snelle hulp voor de module wordt weergegeven en wanneer u de module in de machine learning-gebruikersinterface overdekomma's houdt.

Regels voor tekenslimieten in de module-elementen:

* De waarde van het **kenmerk naam** in het element **Module** mag niet langer zijn dan 64 tekens. 
* De inhoud van het element **Beschrijving** mag niet groter zijn dan 128 tekens in lengte.
* De inhoud van het element **Eigenaar** mag niet groter zijn dan 32 tekens in lengte.

De resultaten van een module kunnen deterministisch of niet-deterministisch zijn.** Standaard worden alle modules als deterministisch beschouwd. Dat wil zeggen, gezien een onveranderlijke set van input parameters en gegevens, moet de module dezelfde resultaten eacRAND of een functie tijd die het wordt uitgevoerd terug te keren. Gezien dit gedrag voert Azure Machine Learning Studio (klassiek) alleen modules opnieuw uit die als deterministisch zijn gemarkeerd als een parameter of de invoergegevens zijn gewijzigd. Het retourneren van de in de cache opgeslagen resultaten biedt ook een veel snellere uitvoering van experimenten.

Er zijn functies die niet deterministisch zijn, zoals RAND of een functie die de huidige datum of tijd retourneert. Als uw module een niet-deterministische functie gebruikt, u opgeven dat de module niet-deterministisch is door het optionele **kenmerk isDeterministic** in te stellen op **FALSE**. Dit zorgt ervoor dat de module opnieuw wordt uitgevoerd wanneer het experiment wordt uitgevoerd, zelfs als de moduleinvoer en parameters niet zijn gewijzigd. 

### <a name="language-definition"></a>Taaldefinitie
Het **element Taal** in uw XML-definitiebestand wordt gebruikt om de aangepaste moduletaal op te geven. Momenteel is R de enige ondersteunde taal. De waarde van het kenmerk **sourceFile** moet de naam zijn van het R-bestand dat de functie bevat die moet worden aanroepen wanneer de module wordt uitgevoerd. Dit bestand moet deel uitmaken van het zip-pakket. De waarde van het **kenmerk entryPoint** is de naam van de functie die wordt aangeroepen en moet overeenkomen met een geldige functie die is gedefinieerd in het bronbestand.

    <Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />


### <a name="ports"></a>Poorten
De invoer- en uitvoerpoorten voor een aangepaste module worden opgegeven in onderliggende elementen van de sectie **Poorten** van het XML-definitiebestand. De volgorde van deze elementen bepaalt de lay-out ervaren (UX) door gebruikers. De eerste **onderliggende invoer** of **uitvoer** die wordt weergegeven in het element **Poorten** van het XML-bestand, wordt de meest linkse invoerpoort in de MACHINE Learning UX.
Elke invoer- en uitvoerpoort kan een optioneel **onderliggend element Beschrijving** hebben dat de tekst opgeeft die wordt weergegeven wanneer u de muiscursor boven de poort in de gebruikersinterface machine learning plaatst.

**Havenregels**:

* Het maximum aantal **invoer- en uitvoerpoorten** is 8 voor elk.

### <a name="input-elements"></a>Invoerelementen
Met invoerpoorten u gegevens doorgeven aan uw R-functie en werkruimte. De **gegevenstypen** die worden ondersteund voor invoerpoorten zijn als volgt: 

**Gegevenstabel:** Dit type wordt doorgegeven aan uw R-functie als een data.frame. In feite worden alle typen (bijvoorbeeld CSV-bestanden of ARFF-bestanden) die worden ondersteund door Machine Learning en die compatibel zijn met **DataTable,** automatisch geconverteerd naar een data.frame. 

        <Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
            <Description>Input Dataset 1</Description>
           </Input>

Het **id-kenmerk** dat aan elke **DataTable-invoerpoort** is gekoppeld, moet een unieke waarde hebben en deze waarde moet overeenkomen met de bijbehorende parameter in uw R-functie.
Optionele **DataTable-poorten** die niet worden doorgegeven als invoer in een experiment hebben de waarde **NULL** doorgegeven aan de R-functie en optionele zip-poorten worden genegeerd als de invoer niet is verbonden. Het kenmerk **isOptioneel** is optioneel voor zowel de **typen DataTable** als **Zip** en is standaard *onwaar.*

**Zip:** Aangepaste modules kunnen een zip-bestand accepteren als invoer. Deze invoer wordt uitgepakt in de R-werkmap van uw functie

        <Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
            <Description>Zip files to be extracted to the R working directory.</Description>
           </Input>

Voor aangepaste R-modules hoeft de ID voor een Zip-poort niet overeen te komen met de parameters van de R-functie. Dit komt omdat het zip-bestand automatisch wordt geëxtraheerd naar de R-werkmap.

**Invoerregels:**

* De waarde van het **id-kenmerk** van het **element Invoer** moet een geldige R-variabele naam zijn.
* De waarde van het **id-kenmerk** van het **element Invoer** mag niet langer zijn dan 64 tekens.
* De waarde van het **naamkenmerk** van het **element Invoer** mag niet langer zijn dan 64 tekens.
* De inhoud van het element **Beschrijving** mag niet langer zijn dan 128 tekens
* De waarde van het **typekenmerk** van het **element Invoer** moet *Zip* of *DataTable*zijn.
* De waarde van het kenmerk **isOptioneel** van het **element Invoer** is niet vereist (en is standaard *onwaar* wanneer dit niet is opgegeven); maar als het is gespecificeerd, moet het *waar* of *onwaar*zijn.

### <a name="output-elements"></a>Uitvoerelementen
**Standaarduitgangspoorten:** Uitvoerpoorten worden toegewezen aan de retourwaarden van uw R-functie, die vervolgens door volgende modules kunnen worden gebruikt. *DataTable* is het enige standaard uitvoerpoorttype dat momenteel wordt ondersteund. (Ondersteuning voor *leerlingen* en *transformaties* is aanstaande.) Een *DataTable-uitvoer* wordt gedefinieerd als:

    <Output id="dataset" name="Dataset" type="DataTable">
        <Description>Combined dataset</Description>
    </Output>

Voor uitvoer in aangepaste R-modules hoeft de waarde van het **id-kenmerk** niet te corresponderen met iets in het R-script, maar het moet wel uniek zijn. Voor één module-uitvoer moet de retourwaarde van de functie R een *data.frame*zijn. Als u meer dan één object van een ondersteund gegevenstype wilt uitvoeren, moeten de juiste uitvoerpoorten worden opgegeven in het XML-definitiebestand en moeten de objecten als lijst worden geretourneerd. De uitvoerobjecten worden toegewezen aan uitvoerpoorten van links naar rechts, die de volgorde weergeven waarin de objecten in de geretourneerde lijst worden geplaatst.

Als u bijvoorbeeld de module **Aangepaste rijen toevoegen** wilt wijzigen om de oorspronkelijke twee gegevenssets uit te zetten, *gegevensset1* en *gegevensset2,* definieert u naast de nieuwe samengevoegde *gegevensset, gegevensset*(in een volgorde, van links naar rechts, zoals: *gegevensset*, *gegevensset1*, *gegevensset2),* en definieervervolgens de uitvoerpoorten in het xml-bestand CustomAddRows.xml als volgt:

    <Ports> 
        <Output id="dataset" name="Dataset Out" type="DataTable"> 
            <Description>New Dataset</Description> 
        </Output> 
        <Output id="dataset1_out" name="Dataset 1 Out" type="DataTable"> 
            <Description>First Dataset</Description> 
        </Output> 
        <Output id="dataset2_out" name="Dataset 2 Out" type="DataTable"> 
            <Description>Second Dataset</Description> 
        </Output> 
        <Input id="dataset1" name="Dataset 1" type="DataTable"> 
            <Description>First Input Table</Description>
        </Input> 
        <Input id="dataset2" name="Dataset 2" type="DataTable"> 
            <Description>Second Input Table</Description> 
        </Input> 
    </Ports> 


En retourneer de lijst met objecten in een lijst in de juiste volgorde in 'CustomAddRows.R':

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) { 
        if (swap) { dataset <- rbind(dataset2, dataset1)) } 
        else { dataset <- rbind(dataset1, dataset2)) 
        } 
    return (list(dataset, dataset1, dataset2)) 
    } 

**Visualisatie-uitvoer:** U ook een uitvoerpoort van type *Visualisatie*opgeven, die de uitvoer van het R-grafische apparaat en de console-uitvoer weergeeft. Deze poort maakt geen deel uit van de R-functieoutput en verstoort de volgorde van de andere uitvoerpoorttypen niet. Als u een visualisatiepoort wilt toevoegen aan de aangepaste modules, voegt u een **element Uitvoer** met een waarde van *visualisatie* toe voor het **typekenmerk:**

    <Output id="deviceOutput" name="View Port" type="Visualization">
      <Description>View the R console graphics device output.</Description>
    </Output>

**Uitvoerregels:**

* De waarde van het **id-kenmerk** van het element **Uitvoer** moet een geldige R-variabele naam zijn.
* De waarde van het **id-kenmerk** van het element **Uitvoer** mag niet langer zijn dan 32 tekens.
* De waarde van het **naamkenmerk** van het element **Uitvoer** mag niet langer zijn dan 64 tekens.
* De waarde van het **typekenmerk** van het element **Uitvoer** moet *Visualisatie*zijn .

### <a name="arguments"></a>Argumenten
Aanvullende gegevens kunnen worden doorgegeven aan de R-functie via moduleparameters die zijn gedefinieerd in het element **Argumenten.** Deze parameters worden weergegeven in het deelvenster meest rechtse eigenschappen van de machine learning-gebruikersinterface wanneer de module is geselecteerd. Argumenten kunnen een van de ondersteunde typen zijn of u een aangepaste opsomming maken wanneer dat nodig is. Net als bij de **elementen Poorten** kunnen de **elementen Arguments** een optioneel **beschrijvingselement** hebben dat de tekst opgeeft die wordt weergegeven wanneer u de muis boven de parameternaam houdt.
Optionele eigenschappen voor een module, zoals standaardWaarde, minValue en maxValue, kunnen aan elk argument worden toegevoegd als kenmerken van een **element Eigenschappen.** Geldige eigenschappen voor het element **Eigenschappen** zijn afhankelijk van het argumenttype en worden beschreven met de ondersteunde argumenttypen in de volgende sectie. Argumenten met de **eigenschap isOptional** ingesteld op **'true'** vereisen niet dat de gebruiker een waarde invoert. Als er geen waarde aan het argument wordt verstrekt, wordt het argument niet doorgegeven aan de functie itemitem. Argumenten van de optie entry point die optioneel zijn, moeten expliciet door de functie worden behandeld, bijvoorbeeld door een standaardwaarde van NULL toegewezen te krijgen in de definitie van de entry point-functie. Een optioneel argument zal alleen de andere argumentbeperkingen afdwingen, d.w.z. min of max, als een waarde door de gebruiker wordt opgegeven.
Net als bij ingangen en uitgangen is het van cruciaal belang dat elk van de parameters unieke ID-waarden bevatten die eraan zijn gekoppeld. In ons quickstartvoorbeeld was de bijbehorende id/parameter *swap*.

### <a name="arg-element"></a>Arg-element
Een moduleparameter wordt gedefinieerd met het element **Arg-onderliggende** element van de sectie **Argumenten** van het XML-definitiebestand. Net als bij de onderliggende elementen in de sectie **Poorten,** definieert het ordenen van parameters in de sectie **Argumenten** de indeling die in de UX wordt aangetroffen. De parameters worden van boven naar beneden in de gebruikersinterface weergegeven in dezelfde volgorde waarin ze zijn gedefinieerd in het XML-bestand. De typen die door Machine Learning worden ondersteund voor parameters, worden hier weergegeven. 

**int** – een parameter van het gehele gehele getal (32-bits).

    <Arg id="intValue1" name="Int Param" type="int">
        <Properties min="0" max="100" default="0" />
        <Description>Integer Parameter</Description>
    </Arg>


* *Optionele eigenschappen*: **min**, **max,** **standaard** en **isOptioneel**

**dubbel** – een parameter van het dubbele type.

    <Arg id="doubleValue1" name="Double Param" type="double">
        <Properties min="0.000" max="0.999" default="0.3" />
        <Description>Double Parameter</Description>
    </Arg>


* *Optionele eigenschappen*: **min**, **max,** **standaard** en **isOptioneel**

**bool** – een Booleaanse parameter die wordt vertegenwoordigd door een selectievakje in UX.

    <Arg id="boolValue1" name="Boolean Param" type="bool">
        <Properties default="true" />
        <Description>Boolean Parameter</Description>
    </Arg>



* *Optionele eigenschappen*: **standaard** - false als deze niet is ingesteld

**tekenreeks**: een standaardtekenreeks

    <Arg id="stringValue1" name="My string Param" type="string">
        <Properties isOptional="true" />
        <Description>String Parameter 1</Description>
    </Arg>    

* *Optionele eigenschappen*: **standaard** en **isOptioneel**

**ColumnPicker**: een parameter kolomselectie. Dit type wordt in de UX weergegeven als een kolomkiezer. Het element **Eigenschap** wordt hier gebruikt om de id van de poort op te geven waaruit kolommen zijn geselecteerd, waarbij het doelpoorttype *Gegevenstabel*moet zijn. Het resultaat van de kolomselectie wordt doorgegeven aan de functie R als een lijst met tekenreeksen met de geselecteerde kolomnamen. 

        <Arg id="colset" name="Column set" type="ColumnPicker">      
          <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
          <Description>Column set</Description>
        </Arg>


* *Vereiste eigenschappen*: **portId** - komt overeen met de id van een invoerelement met type *DataTable*.
* *Optionele eigenschappen*:
  
  * **allowedTypes** - Filtert de kolomtypen waaruit u kiezen. Geldige waarden zijn: 
    
    * Numeriek
    * Booleaans
    * Categorische gegevens
    * Tekenreeks
    * Label
    * Functie
    * Score
    * Alle
  * **standaard** - Geldige standaardselecties voor de kolomkiezer zijn: 
    
    * Geen
    * Numerieke functie
    * NumericLabel (NumericLabel)
    * NumericScore (NumericScore)
    * NumericAll (NumericAll)
    * BooleanFunctie
    * BooleanLabel
    * BooleanScore
    * BooleanAll
    * Categorischfunctie
    * CategorischLabel
    * CategorischScore
    * CategorischAll
    * StringFunctie
    * StringLabel (StringLabel)
    * StringScore (StringScore)
    * StringAll (StringAll)
    * AllLabel AllLabel
    * AllFeature
    * AllScore AllScore
    * Alle

**Vervolgkeuzelijst:** een door de gebruiker opgegeven lijst met opsommingen (vervolgkeuzelijst). De vervolgkeuzeitems worden in het element **Eigenschappen** opgegeven met een **item-element.** De **id** voor elk **item** moet uniek zijn en een geldige R-variabele. De waarde van de **naam** van een **item** dient als zowel de tekst die u ziet als de waarde die wordt doorgegeven aan de functie R.

    <Arg id="color" name="Color" type="DropDown">
      <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
      </Properties>
      <Description>Select a color.</Description>
    </Arg>    

* *Optionele eigenschappen*:
  * **standaard** - De waarde voor de standaardeigenschap moet overeenkomen met een id-waarde van een van de **itemelementen.**

### <a name="auxiliary-files"></a>Hulpbestanden
Elk bestand dat in uw aangepaste module ZIP-bestand wordt geplaatst, zal beschikbaar zijn voor gebruik tijdens de uitvoeringstijd. Alle aanwezige directorystructuren blijven behouden. Dit betekent dat bestandssourcing lokaal hetzelfde werkt en in de Azure Machine Learning Studio (klassieke) uitvoering. 

> [!NOTE]
> Merk op dat alle bestanden worden geëxtraheerd naar 'src' directory, zodat alle paden moeten hebben 'src /' voorvoegsel.
> 
> 

Stel dat u rijen met NOS uit de gegevensset wilt verwijderen en ook dubbele rijen wilt verwijderen voordat u deze in CustomAddRows plaatst, en u hebt al een R-functie geschreven die dat doet in een bestand RemoveDupNARows.R:

    RemoveDupNARows <- function(dataFrame) {
        #Remove Duplicate Rows:
        dataFrame <- unique(dataFrame)
        #Remove Rows with NAs:
        finalDataFrame <- dataFrame[complete.cases(dataFrame),]
        return(finalDataFrame)
    }
U het hulpbestand RemoveDupNARows.R in de functie CustomAddRows bron:

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) {
        source("src/RemoveDupNARows.R")
            if (swap) { 
                dataset <- rbind(dataset2, dataset1))
             } else { 
                  dataset <- rbind(dataset1, dataset2)) 
             } 
        dataset <- removeDupNARows(dataset)
        return (dataset)
    }

Upload vervolgens een zip-bestand met 'CustomAddRows.R', 'CustomAddRows.xml' en 'RemoveDupNARows.R' als aangepaste R-module.

## <a name="execution-environment"></a>Uitvoeringsomgeving
De uitvoeringsomgeving voor het R-script gebruikt dezelfde versie van R als de **Script-module Uitvoeren** en kan dezelfde standaardpakketten gebruiken. U ook extra R-pakketten toevoegen aan uw aangepaste module door ze op te nemen in het aangepaste zip-pakket van de module. Gewoon laden ze in uw R script als je zou in je eigen R-omgeving. 

**Beperkingen van de uitvoeringsomgeving** zijn onder andere:

* Niet-permanente bestandssysteem: bestanden die zijn geschreven wanneer de aangepaste module wordt uitgevoerd, blijven niet bestaan in meerdere runs van dezelfde module.
* Geen netwerktoegang

