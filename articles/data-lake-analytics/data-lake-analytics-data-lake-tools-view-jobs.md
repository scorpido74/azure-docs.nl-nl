---
title: Taak browser gebruiken & taak weergave-Azure Data Lake Analytics
description: In dit artikel wordt beschreven hoe u de taak browser en taak weergave gebruikt voor Azure Data Lake Analytics taken.
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: bdf27b4d-6f58-4093-ab83-4fa3a99b5650
ms.topic: conceptual
ms.date: 08/02/2017
ms.openlocfilehash: 2d33a6ec5ff6b687913914e9433b85765aaa7aec
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309936"
---
# <a name="use-job-browser-and-job-view-for-azure-data-lake-analytics"></a>Taak browser en taak weergave gebruiken voor Azure Data Lake Analytics
De Azure Data Lake Analytics-Service archiveert taken in een query-archief. In dit artikel leert u hoe u taak browser en taak weergave kunt gebruiken in Azure Data Lake-Hulpprogram Ma's voor Visual Studio om de historische taak gegevens te vinden. 

De Data Lake Analytics-Service archiveert standaard de taken gedurende 30 dagen. De verval periode kan worden geconfigureerd via de Azure Portal door het aangepaste verloop beleid te configureren. Na het verlopen hebt u geen toegang tot de taak gegevens. 

## <a name="prerequisites"></a>Vereisten
Zie [Data Lake-Hulpprogram ma's voor Visual Studio-vereisten](data-lake-analytics-data-lake-tools-get-started.md#prerequisites).

## <a name="open-the-job-browser"></a>De taak browser openen
Open de taak browser via **Server Explorer > Azure > Data Lake Analytics > taken** in Visual Studio.  Met de taak browser kunt u toegang krijgen tot de query Store van een Data Lake Analytics-account. In de taak browser wordt aan de linkerkant het query archief weer gegeven, met daarin de basis informatie over de taak en de weer gave taak aan de rechter kant waarin gedetailleerde taak gegevens worden weer gegeven.

## <a name="job-view"></a>Taak weergave
Taak weergave bevat gedetailleerde informatie over een taak. Als u een taak wilt openen, dubbelklikt u op een taak in de taak browser of opent u deze in het menu Data Lake door te klikken op taak weergave. Als het goed is, wordt er een dialoog venster weer geven dat is ingevuld met de taak-URL.

![Visual Studio-taak browser Data Lake-Hulpprogram Ma's](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view.png)

Taak weergave bevat:

* Taaksamenvatting
  
    Vernieuw de taak weergave om de meest recente informatie over het uitvoeren van taken weer te geven.
  
  * Taak status (grafiek):
    
      Taak status geeft een overzicht van de taak fasen:
    
      ![Status van Azure Data Lake Analytics-taak fasen](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-phases.png)
    
    * Stelt Upload uw script naar de Cloud, compileer en optimaliseer het script met behulp van de compiler service.
    * In de wachtrij Taken worden in de wachtrij geplaatst wanneer ze wachten op voldoende resources, of de taken overschrijden het maximum aantal gelijktijdige taken per account beperking. De prioriteits instelling bepaalt de volg orde van taken in de wachtrij. Hoe lager het getal, des te hoger de prioriteit.
    * Voer De taak wordt daad werkelijk uitgevoerd in uw Data Lake Analytics-account.
    * Wordt voltooid De taak wordt voltooid (zoals het volt ooien van het bestand).
      
      De taak kan in elke fase mislukken. Bijvoorbeeld compilatie fouten in de fase voor het voorbereiden, time-outfouten in de fase in de wachtrij en uitvoerings fouten in de actieve fase, enzovoort.
  * Algemene informatie
    
      De basis taak informatie wordt weer gegeven in het onderste deel van het deel venster taak overzicht.
    
      ![Status van Azure Data Lake Analytics-taak fasen](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-info.png)
    
    * Taak resultaat: Geslaagd of mislukt. De taak kan in elke fase mislukken.
    * Totale duur: De klok tijd van de wand (duur) tussen de verzend tijd en de eind tijd.
    * Totale reken tijd: De som van elke vertex uitvoerings tijd, u kunt deze beschouwen als de tijd dat de taak in slechts één hoek punt wordt uitgevoerd. Raadpleeg Total vertices voor meer informatie over het hoek punt.
    * Verzend-/begin-en eind tijd: Het tijdstip waarop de Data Lake Analytics Service verzen ding van taken ontvangt/begint met het uitvoeren van de taak/eindigt of niet.
    * Compileren/in wachtrij plaatsen/uitvoeren: De klok tijd van de wand tijdens de voor bereiding/uitvoering van de wachtrij.
    * Account Het Data Lake Analytics account dat wordt gebruikt voor het uitvoeren van de taak.
    * Lijsten De gebruiker die de taak heeft ingediend, kan het account van een echte persoon of een systeem account zijn.
    * Prioriteit: De prioriteit van de taak. Hoe lager het getal, des te hoger de prioriteit. Dit is alleen van invloed op de volg orde van de taken in de wachtrij. Het instellen van een hogere prioriteit heeft geen voor rang op het uitvoeren van taken.
    * Parallelle uitvoering Het aangevraagde maximum aantal gelijktijdige Azure Data Lake Analytics units (ADLAUs), ook wel hoek punten. Momenteel is één hoek punt gelijk aan één virtuele machine met twee Virtual core-en 6 GB RAM, maar dit kan in toekomstige Data Lake Analytics updates worden bijgewerkt.
    * Resterende bytes: Bytes dat moet worden verwerkt totdat de taak is voltooid.
    * Gelezen/geschreven bytes: Bytes dat is gelezen/geschreven sinds de uitvoering van de taak is gestart.
    * Totaal aantal hoek punten: De taak wordt onderverdeeld in veel werk stukken, elk werk stuk wordt een hoek punt genoemd. Met deze waarde wordt het aantal stukjes werk waaruit de taak bestaat, beschreven. U kunt een hoek punt beschouwen als een basis proces-eenheid, ook wel Azure Data Lake Analytics Unit (ADLAU) en hoek punten kunnen worden uitgevoerd op parallelle wijze. 
    * Voltooid/uitvoeren/mislukt: Het aantal voltooide, actieve/mislukte hoek punten. Hoek punten kunnen mislukken als gevolg van zowel gebruikers code-als systeem fouten, maar het systeem probeert automatisch een aantal keren nieuwe pogingen uit te proberen. Als het hoek punt nog steeds niet kan worden uitgevoerd na het opnieuw proberen, mislukt de hele taak.
* Taak grafiek
  
    Een U-SQL-script vertegenwoordigt de logica voor het transformeren van invoer gegevens naar uitvoer gegevens. Het script wordt gecompileerd en geoptimaliseerd naar een fysiek uitvoerings plan tijdens het voorbereiden van de fase. Taak grafiek is het weer geven van het fysieke uitvoerings plan.  Het volgende diagram illustreert het proces:
  
    ![Status van Azure Data Lake Analytics-taak fasen](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-logical-to-physical-plan.png)
  
    Een taak wordt onderverdeeld in veel werk stukken. Elk werk stuk wordt een hoek punt genoemd. De hoek punten zijn gegroepeerd als Super hoekpunt (ook wel stage) en gevisualiseerd als taak diagram. De placards voor het groene stadium in de taak grafiek tonen de fasen.
  
    Elk hoek punt in een fase voert hetzelfde soort werk uit met verschillende delen van dezelfde gegevens. Als u bijvoorbeeld een bestand hebt met één TB gegevens en er honderden hoek punten worden gelezen, wordt een segment gelezen. Deze hoek punten worden gegroepeerd in dezelfde fase en voeren hetzelfde werk uit op verschillende delen van hetzelfde invoer bestand.
  
  * <a name="state-information"></a>Informatie over fase
    
      In een bepaalde fase worden enkele getallen weer gegeven in de Placard.
    
      ![Grafiek fase Azure Data Lake Analytics taak](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage.png)
    
    * SV1 extract: De naam van een fase, aangeduid met een nummer en de bewerkings methode.
    * 84 hoek punten: Het totale aantal hoek punten in deze fase. De afbeelding geeft aan hoeveel stuks van het werk is verdeeld in deze fase.
    * 12,90 s/hoek punt: De gemiddelde uitvoerings tijd voor het hoek punt voor deze fase. Deze waarde wordt berekend op basis van de som (elke vertex uitvoerings tijd)/(totaal aantal Vertexen). Dit betekent dat als u alle hoek punten kunt toewijzen die worden uitgevoerd in de parallelle fase, het hele stadium is voltooid in 12,90 s. Dit betekent ook dat als alle werkzaamheden in deze fase serieel worden uitgevoerd, de kosten #vertices * Gem.
    * 850.895 rijen geschreven: Totaal aantal rijen dat in deze fase is geschreven.
    * R/W: De hoeveelheid gegevens die in deze fase is gelezen/geschreven in bytes.
    * Kleuren Kleuren worden in het werk gebied gebruikt om een andere hoekpunt status aan te geven.
      
      * Groen geeft aan dat het hoek punt is geslaagd.
      * Oranje geeft aan dat het hoek punt opnieuw wordt geprobeerd. Het opnieuw geprobeerde hoek punt is mislukt, maar wordt automatisch opnieuw geprobeerd en het systeem is voltooid. het algemene stadium is geslaagd. Als het hoek punt opnieuw is geprobeerd, maar nog steeds niet is gelukt, wordt de kleur rood en de hele taak mislukt.
      * Rood geeft aan dat er een fout is opgetreden, wat betekent dat een bepaald hoek punt een paar keer opnieuw is geprobeerd door het systeem, maar nog steeds mislukt. Dit scenario zorgt ervoor dat de volledige taak mislukt.
      * Blauw betekent dat een bepaalde hoek punt wordt uitgevoerd.
      * Wit geeft aan dat het hoek punt wacht. Het hoek punt kan wachten om te worden gepland zodra een ADLAU beschikbaar is, of omdat de invoer gegevens mogelijk niet gereed zijn.
      
      U kunt meer informatie over de fase vinden door de muis aanwijzer met één status aan te wijzen:
      
      ![Details van de fase van Azure Data Lake Analytics taak grafiek](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage-details.png)
  * Punten Hierin worden de details van de hoek punten beschreven, bijvoorbeeld het aantal hoek punten in totaal, het aantal hoek punten dat is voltooid, of het werk is mislukt of nog steeds actief is, enzovoort.
  * Cross/intra pod van gegevens lezen: Bestanden en gegevens worden opgeslagen in meerdere peulen in een gedistribueerd bestands systeem. De waarde hier beschrijft hoeveel gegevens er in dezelfde Pod of pod zijn gelezen.
  * Totale reken tijd: De som van elke vertex uitvoerings tijd in het werk gebied, u kunt dit beschouwen als de tijd die het zou duren als alle werk in het stadium in slechts één hoek punt wordt uitgevoerd.
  * Geschreven/gelezen gegevens en rijen: Geeft aan hoeveel gegevens of rijen er zijn gelezen/geschreven of moeten worden gelezen.
  * Lees fouten vertex: Hierin wordt beschreven hoeveel hoek punten zijn mislukt bij het lezen van gegevens.
  * Dubbele vertex verwijderingen: Als een hoek punt te langzaam wordt uitgevoerd, kan het systeem meerdere hoek punten plannen voor het uitvoeren van hetzelfde werk item. De vertices van de rebuis worden verwijderd zodra een van de hoek punten is voltooid. Met dubbele puntjes verwijdert u het aantal hoek punten dat als dubbele items in het werk gebied wordt genegeerd.
  * Vertex intrekkingen: Het hoek punt is voltooid, maar wordt later opnieuw uitgevoerd om een aantal redenen. Als bijvoorbeeld stroomafwaartse vertex tussenliggende invoer gegevens verliest, wordt de upstream-vertex gevraagd om opnieuw te worden uitgevoerd.
  * Uitvoerings bewerkingen voor vertex schema's: De totale tijd dat de hoek punten zijn gepland.
  * Min/gemiddeld/maximum aantal vertex gegevens gelezen: Het minimum/gemiddelde/maximum van elke vertex gegevens lezen.
  * Hebben De klok tijd die een fase in beslag neemt, moet u het profiel laden om deze waarde weer te geven.
  * Taak afspelen
    
      Data Lake Analytics voert taken uit en archiveert de hoek punten waarop gegevens van de taken worden uitgevoerd, zoals wanneer de hoek punten worden gestart, gestopt, mislukt en hoe ze opnieuw worden geprobeerd, enzovoort. Alle gegevens worden automatisch in het query archief geregistreerd en opgeslagen in het taak profiel. U kunt het taak profiel downloaden via ' profiel laden ' in de taak weergave en u kunt het taak afspelen bekijken nadat het taak profiel is gedownload.
    
      Het afspelen van taken is een Epitome visualisatie van wat er in het cluster is gebeurd. Het helpt u de voortgang van de taak uitvoering te bekijken en prestatie afwijkingen en knel punten in een zeer korte tijd visueel te detecteren (minder dan 30s doorgaans).
  * Heatmap van taak weer geven 
    
      Heatmap voor taak kan worden geselecteerd via de vervolg keuzelijst weer geven in taak diagram. 
    
      ![Weer gave van heap-toewijzing van Azure Data Lake Analytics taak grafiek](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-display.png)
    
      Hierin wordt de heatmap voor I/O, tijd en door Voer van een taak weer gegeven, zodat u kunt zien waar de taak de meeste tijd uitmaakt, of dat uw taak een I/O-grens taak is, enzovoort.
    
      ![Voor beeld van heap-toewijzing van Azure Data Lake Analytics taak grafiek](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-example.png)
    
    * Gang De voortgang van de taak uitvoering vindt u in de informatie in de fase.
    * Gegevens lezen/geschreven: De heatmap van het totale aantal gelezen/geschreven gegevens in elke fase.
    * Reken tijd: De heatmap van de som (elke vertex uitvoerings tijd), kunt u overwegen om te bepalen hoe lang het zou duren als alle werk in het stadium met slechts 1 hoek punt wordt uitgevoerd.
    * Gemiddelde uitvoerings tijd per knoop punt: De heatmap van de som (elke vertex uitvoerings tijd)/(hoek nummer). Dit betekent dat als u alle hoek punten kunt toewijzen die worden uitgevoerd in de parallelle modus, de hele fase wordt uitgevoerd in deze periode.
    * Invoer/uitvoer doorvoer: De heatmap voor invoer/uitvoer van elke fase, u kunt bevestigen of uw taak een I/O-gebonden taak is.
* Meta gegevens bewerkingen
  
    U kunt enkele meta gegevens bewerkingen uitvoeren in uw U-SQL-script, zoals het maken van een Data Base, het verwijderen van een tabel, enzovoort. Deze bewerkingen worden weer gegeven in de meta gegevens bewerking na compilatie. U kunt bevestigingen, entiteiten maken en entiteiten hier neerzetten vinden.
  
    ![Bewerkingen voor het weer geven van meta gegevens van Azure Data Lake Analytics taak](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-metadata-operations.png)
* Statusgeschiedenis
  
    De status geschiedenis wordt ook gevisualiseerd in taak overzicht, maar u kunt hier meer informatie vinden. U kunt de gedetailleerde informatie vinden, bijvoorbeeld wanneer de taak wordt voor bereid, in de wachtrij geplaatst, wordt uitgevoerd, is beëindigd. U kunt ook bepalen hoe vaak de taak is gecompileerd (de CcsAttempts: 1) wanneer de taak in feite wordt verzonden naar het cluster (de details: Taak verzenden naar cluster), enzovoort.
  
    ![Geschiedenis van status van Azure Data Lake Analytics taak weergave](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-state-history.png)
* Diagnostiek
  
    Het hulp programma diagnosticeert de uitvoering van de taak automatisch. Er worden waarschuwingen weer gegeven wanneer er fouten of prestatie problemen in uw taken optreden. Houd er rekening mee dat u het profiel moet downloaden om hier volledige informatie te krijgen. 
  
    ![Diagnostische gegevens van de taak weergave Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-diagnostics.png)
  
  * Berichten Er wordt hier een waarschuwing weer gegeven met de compiler waarschuwing. U kunt op de koppeling x-probleem (en) klikken om meer details te geven zodra de waarschuwing wordt weer gegeven.
  * Hoek punt wordt te lang uitgevoerd: Als een hoek punt is verlopen (bijvoorbeeld 5 uur), worden er problemen gevonden.
  * Resource gebruik: Als u meer of onvoldoende parallellisme hebt toegewezen dan nodig zijn, kunt u hier problemen vinden. U kunt ook klikken op resource gebruik om meer details te bekijken en wat-als-scenario's uit te voeren om een betere resource toewijzing te vinden (zie deze hand leiding voor meer informatie).
  * Geheugen controle: Als een hoek punt meer dan 5 GB geheugen gebruikt, worden er problemen hier weer gegeven. Het uitvoeren van de taak kan worden afgebroken door het systeem als er meer geheugen wordt gebruikt dan de beperking van het systeem.

## <a name="job-detail"></a>Taak Details
Taak Details toont gedetailleerde informatie over de taak, waaronder script, resources en Vertex Execution View.

![Details van Azure Data Lake Analytics taak](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-details.png)

* Script
  
    Het U-SQL-script van de taak wordt opgeslagen in het query archief. U kunt het oorspronkelijke U-SQL-script weer geven en het indien nodig opnieuw verzenden.
* Resources
  
    U kunt de uitvoer van de taak compilaties vinden die in de query-Store zijn opgeslagen via resources. U kunt bijvoorbeeld ' algebra. XML ' vinden, die wordt gebruikt voor het weer geven van de taak grafiek, de assembly's die u hebt geregistreerd, enzovoort.
* Uitvoerings weergave van vertex
  
    De details van de uitvoerings hoek van vertices worden weer gegeven. Het taak profiel archiveert elk uitvoerings logboek voor vertexen, zoals het totale aantal gelezen/geschreven gegevens, runtime, status, enzovoort. In deze weer gave kunt u meer informatie krijgen over hoe een taak is uitgevoerd. Zie [de weer gave vertex Execution gebruiken in data Lake-Hulpprogram ma's voor Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
* Zie [Accessing diagnostics logs for Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md) (Diagnostische logboeken openen voor Azure Data Lake Analytics) voor logboekregistratie van diagnostische informatie.
* Zie [Websitelogboeken analyseren met Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md) voor een complexere query.
* Zie [de weer gave vertex Execution gebruiken in data Lake-Hulpprogram ma's voor Visual Studio voor](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md) meer informatie over het gebruik van de vertex-uitvoerings weergave.

