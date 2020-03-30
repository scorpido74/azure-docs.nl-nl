---
title: Job Browser & Job View gebruiken - Azure Data Lake Analytics
description: In dit artikel wordt beschreven hoe u Job Browser en Job View gebruiken voor Azure Data Lake Analytics-taken.
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: bdf27b4d-6f58-4093-ab83-4fa3a99b5650
ms.topic: conceptual
ms.date: 08/02/2017
ms.openlocfilehash: 2d33a6ec5ff6b687913914e9433b85765aaa7aec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71309936"
---
# <a name="use-job-browser-and-job-view-for-azure-data-lake-analytics"></a>Taakbrowser en taakweergave gebruiken voor Azure Data Lake Analytics
De Azure Data Lake Analytics-servicearchieven hebben taken ingediend in een queryarchief. In dit artikel leert u hoe u Job Browser en Job View gebruiken in Azure Data Lake Tools for Visual Studio om de historische taakgegevens te vinden. 

Standaard archiveert de Data Lake Analytics-service de taken gedurende 30 dagen. De vervaldatum kan worden geconfigureerd vanuit de Azure-portal door het aangepaste verloopbeleid te configureren. U hebt na afloop geen toegang meer tot de taakgegevens. 

## <a name="prerequisites"></a>Vereisten
Zie [Vereisten voor Gegevensmeertools voor Visual Studio.](data-lake-analytics-data-lake-tools-get-started.md#prerequisites)

## <a name="open-the-job-browser"></a>De vacaturebrowser openen
Toegang tot de taakbrowser via **Server Explorer>Azure>Data Lake Analytics>Jobs** in Visual Studio.  Met de vacaturebrowser hebt u toegang tot de querywinkel van een Data Lake Analytics-account. Job Browser geeft Query Store aan de linkerkant weer, met basistaakgegevens en Taakweergave aan de rechterkant met gedetailleerde taakinformatie.

## <a name="job-view"></a>Taakweergave
Job View toont de gedetailleerde informatie van een taak. Als u een taak wilt openen, u dubbelklikken op een taak in de vacaturebrowser of deze openen in het menu Data Lake door op Taakweergave te klikken. Er moet een dialoogvenster worden weergegeven dat is gevuld met de taak-URL.

![Visual Studio-functiebrowser voor Data Lake-hulpprogramma's](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view.png)

Taakweergave bevat:

* Taakoverzicht
  
    De taakweergave vernieuwen om de meer recente informatie over lopende taken te bekijken.
  
  * Functiestatus (grafiek):
    
      Functiestatus geeft een overzicht van de taakfasen:
    
      ![Status van Azure Data Lake Analytics-taakfasen](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-phases.png)
    
    * Voorbereiden: Upload uw script naar de cloud, stel het script samen en optimaliseer het script met behulp van de compilerservice.
    * Wachtrij: taken worden in de wachtrij geplaatst wanneer ze wachten op voldoende resources of de taken de maximale gelijktijdige taken per accountbeperking overschrijden. De prioriteitsinstelling bepaalt de volgorde van taken in de wachtrij - hoe lager het getal, hoe hoger de prioriteit.
    * Uitgevoerd: de taak wordt uitgevoerd in uw Data Lake Analytics-account.
    * Finaliseren: de taak is voltooid (bijvoorbeeld het voltooien van het bestand).
      
      De taak kan mislukken in elke fase. Bijvoorbeeld compilatiefouten in de voorbereidingsfase, time-outfouten in de wachtrijfase en uitvoeringsfouten in de uitvoeringsfase, enz.
  * Basisinformatie
    
      De basistaakinformatie wordt weergegeven in het onderste gedeelte van het deelvenster Functieoverzicht.
    
      ![Status van Azure Data Lake Analytics-taakfasen](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-info.png)
    
    * Taakresultaat: geslaagd of mislukt. De taak kan mislukken in elke fase.
    * Totale duur: Wandkloktijd (duur) tussen het indienen van tijd en eindtijd.
    * Totale rekentijd: de som van elke vertex uitvoeringstijd, u beschouwen als de tijd dat de taak wordt uitgevoerd in slechts een hoekpunt. Raadpleeg Total Vertices voor meer informatie over vertex.
    * Verzenden/starten/eindtijd: het moment waarop de Data Lake Analytics-service het indienen van een taak ontvangt/de taak begint uit te voeren/deze taak met succes beëindigt of niet.
    * Compilatie/Wachtrij/Running: Wandkloktijd doorgebracht tijdens de fase Voorbereiden/Wachtrijen/Hardlopen.
    * Account: het Data Lake Analytics-account dat wordt gebruikt voor het uitvoeren van de taak.
    * Auteur: De gebruiker die de taak heeft ingediend, kan het account van een echte persoon of een systeemaccount zijn.
    * Prioriteit: De prioriteit van de taak. Hoe lager het getal, hoe hoger de prioriteit. Het heeft alleen invloed op de volgorde van de taken in de wachtrij. Het instellen van een hogere prioriteit doet geen afbreuk aan het uitvoeren van taken.
    * Parallelisme: het gevraagde maximum aantal gelijktijdige Azure Data Lake Analytics Units (ADLAUs), ook bekend als vertices. Momenteel is één hoekpunt gelijk aan één VM met twee virtuele kern en zes GB RAM, hoewel dit in toekomstige Data Lake Analytics-updates kan worden bijgewerkt.
    * Links bytes: bytes die moeten worden verwerkt totdat de taak is voltooid.
    * Gelezen/geschreven bytes: Bytes die zijn gelezen/geschreven sinds de taak is gestart.
    * Totale vertices: De baan is opgesplitst in vele stukken van het werk, elk stuk van het werk heet een hoekpunt. Deze waarde beschrijft uit hoeveel werkstukken de taak bestaat. U een hoekpunt beschouwen als een basisproceseenheid, aka Azure Data Lake Analytics Unit (ADLAU), en vertices kunnen parallellisme worden uitgevoerd. 
    * Voltooid/lopend/mislukt: het aantal voltooide/lopende/mislukte vertices. Vertices kunnen mislukken als gevolg van zowel gebruikerscode als systeemfouten, maar de systeempogingen zijn automatisch een paar keer mislukt. Als de hoekpunt nog steeds mislukt na opnieuw proberen, zal de hele taak mislukken.
* Taakgrafiek
  
    Een U-SQL-script vertegenwoordigt de logica van het transformeren van invoergegevens naar uitvoergegevens. Het script wordt gecompileerd en geoptimaliseerd voor een fysiek uitvoeringsplan tijdens de voorbereidingsfase. Job Graph is om het fysieke uitvoeringsplan weer te geven.  Het volgende diagram illustreert het proces:
  
    ![Status van Azure Data Lake Analytics-taakfasen](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-logical-to-physical-plan.png)
  
    Een baan is opgesplitst in vele stukken van het werk. Elk stuk werk wordt een Vertex genoemd. De vertices zijn gegroepeerd als Super Vertex (aka stadium), en gevisualiseerd als Job Graph. De groene podiumplakkaten in de taakgrafiek tonen de fasen.
  
    Elke hoekpunt in een fase doet hetzelfde soort werk met verschillende stukken van dezelfde gegevens. Als u bijvoorbeeld een bestand hebt met één TB-gegevens en er honderden vertices van lezen, leest elk van hen een stuk. Deze vertices zijn gegroepeerd in dezelfde fase en doen hetzelfde werk op verschillende stukken van hetzelfde invoerbestand.
  
  * <a name="state-information"></a>Podiuminformatie
    
      In een bepaalde fase worden sommige getallen weergegeven in het plakkaat.
    
      ![Fase van de taakgrafiek van Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage.png)
    
    * SV1-uittreksel: de naam van een fase, genoemd door een getal en de bewerkingsmethode.
    * 84 vertices: Het totale aantal vertices in deze fase. De figuur geeft aan hoeveel stukken werk in deze fase is verdeeld.
    * 12.90 s/vertex: De gemiddelde vertex uitvoeringstijd voor deze fase. Dit cijfer wordt berekend door SOM (elke vertex uitvoeringstijd) / (totale Vertex telling). Wat betekent dat als je alle vertices die in parallelisme worden uitgevoerd, zou kunnen toewijzen, het hele stadium is voltooid in 12,90 s. Het betekent ook dat als al het werk in deze fase serieel wordt gedaan, de kosten zouden worden #vertices * AVG-tijd.
    * 850.895 rijen geschreven: Totaal aantal rij's geschreven in deze fase.
    * R/W: Hoeveelheid gelezen/Geschreven gegevens in deze fase in bytes.
    * Kleuren: kleuren worden in de fase gebruikt om de status van verschillende hoekpunten aan te geven.
      
      * Groen geeft aan dat het hoekpunt is geslaagd.
      * Oranje geeft aan dat het hoekpunt opnieuw is geprobeerd. Het opnieuw geprobeerde hoekpunt is mislukt, maar wordt automatisch en opnieuw geprobeerd door het systeem en de algemene fase is voltooid. Als het hoekpunt opnieuw is geprobeerd, maar nog steeds mislukt, wordt de kleur rood en is de hele taak mislukt.
      * Rood geeft aan dat het niet is gelukt, wat betekent dat een bepaalde hoekpunt een paar keer opnieuw was geprobeerd door het systeem, maar nog steeds is mislukt. Dit scenario zorgt ervoor dat de hele taak mislukt.
      * Blauw betekent dat een bepaald hoekpunt loopt.
      * Wit geeft aan dat het hoekpunt wacht. Het hoekpunt kan wachten om te worden gepland zodra een ADLAU beschikbaar is, of het kan wachten op invoer, omdat de invoergegevens mogelijk niet klaar zijn.
      
      U meer details voor het werkgebied vinden door met één status met de muiscursor te zweven:
      
      ![Details van de fase van de taakgrafiek van Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage-details.png)
  * Vertices: Beschrijft de vertices details, bijvoorbeeld, hoeveel vertices in totaal, hoeveel vertices zijn voltooid, zijn ze niet of nog steeds actief / wachten, enz.
  * Gegevens lezen cross/intra pod: Bestanden en gegevens worden opgeslagen in meerdere pods in gedistribueerd bestandssysteem. De waarde hier beschrijft hoeveel gegevens zijn gelezen in dezelfde pod of cross pod.
  * Totale rekentijd: De som van elke vertex uitvoeringstijd in de fase, u het beschouwen als de tijd die het zou duren als al het werk in de fase wordt uitgevoerd in slechts een hoekpunt.
  * Gegevens en rijen geschreven/gelezen: geeft aan hoeveel gegevens of rijen zijn gelezen/geschreven of moeten worden gelezen.
  * Vertex leesfouten: beschrijft hoeveel vertices zijn mislukt tijdens het lezen van gegevens.
  * Vertex dubbele teruggooi: Als een hoekpunt te traag loopt, kan het systeem meerdere vertices plannen om hetzelfde werk uit te voeren. Reductant vertices worden weggegooid zodra een van de vertices met succes is voltooid. Vertex-dubbele teruggooi registreert het aantal vertices dat als duplicaties in de fase wordt weggegooid.
  * Vertex intrekkingen: De hoekpunt werd opgevolgd, maar krijgen later opnieuw te wijten aan een aantal redenen. Als downstream-vertex bijvoorbeeld tussenliggende invoergegevens verliest, wordt het upstream-hoekpunt gevraagd om opnieuw te worden uitgevoerd.
  * Vertex planning uitvoeringen: de totale tijd dat de vertices zijn gepland.
  * Min/Average/Max Vertex data read: The minimum/average/maximum of every vertex read data.
  * Duur: De wandkloktijd die een fase inbeslagneemt, moet u het profiel laden om deze waarde te zien.
  * Taak afspelen
    
      Data Lake Analytics voert taken uit en archiveert de vertices running information of the jobs, zoals wanneer de vertices worden gestart, gestopt, mislukt en hoe ze opnieuw worden geprobeerd, enz. Alle informatie wordt automatisch aangemeld in de querywinkel en opgeslagen in het functieprofiel. U het functieprofiel downloaden via 'Laadprofiel' in taakweergave en u het afspelen van vacatures bekijken na het downloaden van het functieprofiel.
    
      Taakweergave is een belichamingvan wat er in het cluster is gebeurd. Het helpt u de voortgang van de uitvoering van taken te bekijken en prestatieafwijkingen en knelpunten visueel op te sporen in een zeer korte tijd (meestal minder dan 30).
  * Weergave werkheatkaart 
    
      Taakheatmap kan worden geselecteerd via de vervolgkeuzelijst Weergave in Taakgrafiek. 
    
      ![Kaartkaartweergave van Azure Data Lake Analytics-taakgrafiek](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-display.png)
    
      Het toont de I/O, tijd en doorvoer warmtekaart van een taak, waardoor u vinden waar de taak het grootste deel van de tijd doorbrengt, of dat uw baan een I/O-grenstaak is, enzovoort.
    
      ![Voorbeeld van het overzicht van de grafiekvan Azure Data Lake Analytics-taakgrafiek](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-example.png)
    
    * Voortgang: de voortgang van de uitvoering van de taak, zie Informatie in fase-informatie.
    * Gelezen/geschreven gegevens: De heatmap van de totale gegevens die in elke fase worden gelezen/geschreven.
    * Rekentijd: De heat map van SOM (elke vertex uitvoeringstijd), u dit beschouwen als hoe lang het zou duren als al het werk in de fase wordt uitgevoerd met slechts 1 hoekpunt.
    * Gemiddelde uitvoeringstijd per knooppunt: De heatmap van SOM (elke vertex uitvoeringstijd) / (Vertex Number). Wat betekent dat als je alle vertices die in parallelisme worden uitgevoerd, zou kunnen toewijzen, het hele stadium zal worden gedaan in dit tijdsbestek.
    * Input/Output doorvoer: de heat map van input/output doorvoer van elke fase, u bevestigen of uw taak is een I / O gebonden baan door middel van deze.
* Metagegevensbewerkingen
  
    U bepaalde metagegevensbewerkingen uitvoeren in uw U-SQL-script, zoals een database maken, een tabel neerzetten, enz. Deze bewerkingen worden weergegeven in Metagegevensbewerking na compilatie. U hier beweringen vinden, entiteiten maken, entiteiten neerzetten.
  
    ![Azure Data Lake Analytics Metagegevensbewerkingen metagegevens](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-metadata-operations.png)
* Staatsgeschiedenis
  
    De staatsgeschiedenis wordt ook gevisualiseerd in Functieoverzicht, maar u hier meer details krijgen. U de gedetailleerde informatie vinden, zoals wanneer de taak is voorbereid, in de wachtrij staat, is gestart met hardlopen, beëindigd. Ook u vinden hoe vaak de taak is gecompileerd (de CcsAttempts: 1), wanneer is de taak die wordt verzonden naar het cluster eigenlijk (de Detail: Dispatching job naar cluster), enz.
  
    ![Statusgeschiedenis azure Data Lake Analytics-taakweergave](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-state-history.png)
* Diagnostiek
  
    De tool diagnosticeert taakuitvoering automatisch. U ontvangt waarschuwingen wanneer er fouten of prestatieproblemen zijn in uw taken. Houd er rekening mee dat je Profiel moet downloaden om hier alle informatie te krijgen. 
  
    ![Azure Data Lake Analytics Job View-diagnose](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-diagnostics.png)
  
  * Waarschuwingen: Er verschijnt hier een waarschuwing met compilerwaarschuwing. U op de koppeling 'x probleem(en)' klikken om meer details te krijgen zodra de waarschuwing wordt weergegeven.
  * Vertex run te lang: Als een hoekpunt loopt uit de tijd (zeg 5 uur), problemen zullen hier worden gevonden.
  * Resourcegebruik: Als u meer of onvoldoende parallellisme toegewezen hebt dan nodig is, worden hier problemen gevonden. U ook op Resourcegebruik klikken om meer details te zien en wat-als-scenario's uit te voeren om een betere toewijzing van resources te vinden (zie deze handleiding voor meer informatie).
  * Geheugencontrole: Als een hoekpunt meer dan 5 GB geheugen gebruikt, worden hier problemen gevonden. Taak uitvoering kan krijgen gedood door het systeem als het gebruikt meer geheugen dan systeembeperking.

## <a name="job-detail"></a>Functiedetail
Taakdetail toont de gedetailleerde informatie van de taak, waaronder Script, Resources en Vertex Execution View.

![Functiedetail van Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-details.png)

* Script
  
    Het U-SQL-script van de taak wordt opgeslagen in het queryarchief. U het originele U-SQL-script bekijken en indien nodig opnieuw verzenden.
* Resources
  
    U de uitvoer van taakcompilaties vinden die zijn opgeslagen in het queryarchief via Resources. U bijvoorbeeld algebra.xml vinden die wordt gebruikt om de Job Graph, de door u geregistreerde verzamelingen, enz.
* Vertex-uitvoeringsweergave
  
    Het toont vertices uitvoeringsdetails. Het taakprofiel archiveert elk vertex-uitvoeringslogboek, zoals totale gelezen/geschreven gegevens, runtime, status, enz. Via deze weergave u meer informatie krijgen over hoe een taak is uitgevoerd. Zie [De vertex-uitvoeringsweergave gebruiken in Data Lake Tools voor Visual Studio voor](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)meer informatie.

## <a name="next-steps"></a>Volgende stappen
* Zie [Accessing diagnostics logs for Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md) (Diagnostische logboeken openen voor Azure Data Lake Analytics) voor logboekregistratie van diagnostische informatie.
* Zie [Websitelogboeken analyseren met Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md) voor een complexere query.
* Zie [De uitvoeringsweergave Vertex gebruiken in Data Lake Tools voor Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md) als u de weergave Vertex-uitvoering wilt gebruiken.

