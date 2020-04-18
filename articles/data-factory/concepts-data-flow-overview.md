---
title: Toewijzing gegevensstromen
description: Een overzicht van gegevensstromen in Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/28/2020
ms.openlocfilehash: 8420cba043e7fc9285b473b877f0ff10212e9fda
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605317"
---
# <a name="what-are-mapping-data-flows"></a>Wat zijn toewijzingsgegevensstromen?

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Gegevensstromen in kaart brengen zijn visueel ontworpen gegevenstransformaties in Azure Data Factory. Gegevensstromen stellen dataengineers in staat om grafische logica voor gegevenstransformatie te ontwikkelen zonder code te schrijven. De resulterende gegevensstromen worden uitgevoerd als activiteiten binnen Azure Data Factory-pijplijnen die geschaalde Apache Spark-clusters gebruiken. Gegevensstroomactiviteiten kunnen worden ingeschakeld via bestaande plannings-, controle-, stroom- en bewakingsmogelijkheden van Data Factory.

Het in kaart brengen van gegevensstromen biedt een volledig visuele ervaring zonder codering vereist. Uw gegevensstromen worden uitgevoerd op uw uitvoeringscluster voor geschaalde gegevensverwerking. Azure Data Factory verwerkt alle codevertaling, padoptimalisatie en uitvoering van uw gegevensstroomtaken.

## <a name="getting-started"></a>Aan de slag

Als u een gegevensstroom wilt maken, selecteert u het plusteken onder **Fabrieksresources**en selecteert u **Gegevensstroom**. 

![Nieuwe gegevensstroom](media/data-flow/newdataflow2.png "nieuwe gegevensstroom")

Met deze actie brengt u het canvas voor gegevensstromen, waar u uw transformatielogica maken. Selecteer **Bron toevoegen** om te beginnen met het configureren van uw brontransformatie. Zie [Brontransformatie](data-flow-source.md)voor meer informatie.

## <a name="data-flow-canvas"></a>Gegevensstroomcanvas

Het gegevensstroomcanvas is opgesplitst in drie delen: de bovenste balk, de grafiek en het configuratiepaneel. 

![Canvas](media/data-flow/canvas1.png "Canvas")

### <a name="graph"></a>Graph

De grafiek toont de transformatiestroom. Het toont de afstamming van brongegevens als deze in een of meer putten stroomt. Als u een nieuwe bron wilt toevoegen, selecteert u **Bron toevoegen**. Als u een nieuwe transformatie wilt toevoegen, selecteert u het plusteken rechtsonder in een bestaande transformatie.

![Canvas](media/data-flow/canvas2.png "Canvas")

### <a name="azure-integration-runtime-data-flow-properties"></a>Runtime-gegevensstroomeigenschappen voor Azure-integratie

![Foutopsporingsknop](media/data-flow/debugbutton.png "Foutopsporingsknop")

Wanneer u begint te werken met gegevensstromen in ADF, wilt u de schakelaar Foutopsporing inschakelen voor gegevensstromen boven aan de gebruikersinterface van de browser. Hiermee wordt een Spark-cluster uitgevoerd om te gebruiken voor interactieve foutopsporing, gegevensvoorbeelden en foutopsporingsuitvoeringen voor pijplijnen. U de grootte instellen van het cluster dat wordt gebruikt door een aangepaste [Azure Integration Runtime te](concepts-integration-runtime.md)kiezen. De foutopsporingssessie blijft tot 60 minuten in leven na uw laatste gegevensvoorbeeld of de laatste uitvoering van de foutopsporingspijplijn.

Wanneer u uw pijplijnen met gegevensstroomactiviteiten operationaliseert, gebruikt ADF de Azure Integration Runtime die is gekoppeld aan de [activiteit](control-flow-execute-data-flow-activity.md) in de eigenschap 'Uitvoeren op'.

De standaard Azure Integration Runtime is een klein 4-core single worker node-cluster waarmee u gegevens bekijken en snel foutopsporingspijplijnen uitvoeren tegen minimale kosten. Stel een grotere Azure IR-configuratie in als u bewerkingen uitvoert op grote gegevenssets.

U ADF instrueren om een pool van clusterresources (VM's) te onderhouden door een TTL in te stellen in de azure IR-gegevensstroomeigenschappen. Deze actie resulteert in een snellere uitvoering van de taak bij volgende activiteiten.

#### <a name="azure-integration-runtime-and-data-flow-strategies"></a>Runtime en dataflowstrategieën voor Azure-integratie

##### <a name="execute-data-flows-in-parallel"></a>Gegevensstromen parallel uitvoeren

Als u gegevensstromen parallel in een pijplijn uitvoert, draait ADF afzonderlijke Spark-clusters op voor elke activiteitsuitvoering op basis van de instellingen in uw Azure Integration Runtime die aan elke activiteit zijn gekoppeld. Als u parallelle uitvoeringen in ADF-pijplijnen wilt ontwerpen, voegt u uw gegevensstroomactiviteiten toe zonder voorrangsbeperkingen in de gebruikersinterface.

Van deze drie opties wordt deze optie waarschijnlijk in de kortste tijd uitgevoerd. Elke parallelle gegevensstroom wordt echter tegelijkertijd uitgevoerd op afzonderlijke clusters, zodat het ordenen van gebeurtenissen niet-deterministisch is.

Als u uw gegevensstroomactiviteiten parallel binnen uw pijplijnen uitvoert, wordt aanbevolen om TTL niet te gebruiken. Deze actie komt omdat parallelle uitvoeringen van uw gegevensstroom tegelijkertijd met dezelfde Azure Integration Runtime resulteren in meerdere warme poolexemplaren voor uw gegevensfabriek.

##### <a name="overload-single-data-flow"></a>Eén gegevensstroom overbelasten

Als u al uw logica in één gegevensstroom plaatst, voert ADF dezelfde taakuitvoeringscontext uit op één Spark-clusterexemplaar.

Deze optie kan uitdagender zijn om te volgen en problemen op te lossen, omdat uw bedrijfsregels en bedrijfslogica door elkaar kunnen worden gegooid. Deze optie biedt ook niet veel herbruikbaarheid.

##### <a name="execute-data-flows-serially"></a>Gegevensstromen serieel uitvoeren

Als u uw gegevensstroomactiviteiten in seriein de pijplijn uitvoert en u een TTL hebt ingesteld op de Azure IR-configuratie, gebruikt ADF de compute resources (VM's) opnieuw, wat resulteert in snellere latere uitvoeringstijden. U ontvangt nog steeds een nieuwe Spark-context voor elke uitvoering.

Van deze drie opties duurt deze actie waarschijnlijk het langst om end-to-end uit te voeren. Maar het biedt wel een schone scheiding van logische bewerkingen in elke datastroomstap.

### <a name="configuration-panel"></a>Configuratiepaneel

In het configuratiepaneel worden de instellingen weergegeven die specifiek zijn voor de geselecteerde transformatie. Als er geen transformatie is geselecteerd, wordt de gegevensstroom weergegeven. In de algemene configuratie van de gegevensstroom u de naam en beschrijving bewerken onder het tabblad **Algemeen** of parameters toevoegen via het tabblad **Parameters.** Zie [Parameters gegevensstroom toewijzen](parameters-data-flow.md)voor meer informatie .

Elke transformatie bevat ten minste vier configuratietabbladen.

#### <a name="transformation-settings"></a>Transformatie-instellingen

Het eerste tabblad in het configuratiedeelvenster van elke transformatie bevat de instellingen die specifiek zijn voor die transformatie. Zie de documentatiepagina van die transformatie voor meer informatie.

![Tabblad Broninstellingen](media/data-flow/source1.png "Tabblad Broninstellingen")

#### <a name="optimize"></a>Optimaliseren

Het tabblad **Optimaliseren** bevat instellingen om partitieschema's te configureren.

![Optimaliseren](media/data-flow/optimize1.png "Optimaliseren")

De standaardinstelling is **Huidige partitionering gebruiken,** waarmee Azure Data Factory het partitieschema moet gebruiken dat is voor gegevensstromen die op Spark worden uitgevoerd. In de meeste scenario's raden we deze instelling aan.

Er zijn instanties waarin u de partitionering wilt aanpassen. Als u bijvoorbeeld uw transformaties wilt uitvoeren naar één bestand in het meer, selecteert u **Eén partitie** in een gootsteentransformatie.

Een ander geval waarin u de partitioneringsschema's wilt beheren, is het optimaliseren van de prestaties. Het aanpassen van de partitionering biedt controle over de verdeling van uw gegevens over compute nodes en data lokalisaties die zowel positieve als negatieve effecten kunnen hebben op uw algehele gegevensstroomprestaties. Zie de [prestatiegids Gegevensstroom](concepts-data-flow-performance.md)voor meer informatie .

Als u de partitionering bij elke transformatie wilt wijzigen, selecteert u het tabblad **Optimaliseren** en selecteert u de knop **Partitioneren instellen.** U krijgt een reeks opties voor partitionering. De beste methode voor partitionering verschilt op basis van uw gegevensvolumes, kandidaatsleutels, null-waarden en kardinaliteit. 

Een aanbevolen toepassing is om te beginnen met standaard partitionering en vervolgens verschillende partitieopties uit te proberen. U testen met behulp van foutopsporingsuitvoeringen van pijplijnen en het uitvoerings- en partitiegebruik bekijken in elke transformatiegroepering vanuit de bewakingsweergave. Zie [Gegevensstromen controleren](concepts-data-flow-monitoring.md)voor meer informatie .

De volgende partitieopties zijn beschikbaar.

##### <a name="round-robin"></a>Ronde roodborstje 

Round robin is een eenvoudige partitie die automatisch gegevens gelijkmatig verdeelt over partities. Gebruik round-robin als je geen goede key candidates hebt om een solide, slimme partitioneringstrategie te implementeren. U het aantal fysieke partities instellen.

##### <a name="hash"></a>Hash

Azure Data Factory produceert een hash van kolommen om uniforme partities te produceren, zodat rijen met vergelijkbare waarden in dezelfde partitie vallen. Wanneer u de optie Hash gebruikt, test u op mogelijke partitiescheefheid. U het aantal fysieke partities instellen.

##### <a name="dynamic-range"></a>Dynamisch bereik

Het dynamisch bereik maakt gebruik van Spark dynamische bereiken op basis van de kolommen of expressies die u opgeeft. U het aantal fysieke partities instellen. 

##### <a name="fixed-range"></a>Vast bereik

Maak een expressie die een vast bereik biedt voor waarden in de kolommen met partitiegegevens. Om partitiescheefheid te voorkomen, moet u een goed begrip van uw gegevens hebben voordat u deze optie gebruikt. De waarden die u voor de expressie invoert, worden gebruikt als onderdeel van een partitiefunctie. U het aantal fysieke partities instellen.

##### <a name="key"></a>Sleutel

Als u een goed begrip van de kardinaliteit van uw gegevens, sleutel verdeling zou een goede strategie. Sleutelpartitionering maakt partities voor elke unieke waarde in uw kolom. U het aantal partities niet instellen omdat het getal is gebaseerd op unieke waarden in de gegevens.

#### <a name="inspect"></a>Inspecteren

Het tabblad **Inspecteren** biedt een weergave van de metagegevens van de gegevensstroom die u transformeert. U kolomtellingen, de kolommen die zijn gewijzigd, de toegevoegde kolommen, gegevenstypen, de kolomvolgorde en kolomverwijzingen zien. **Inspect** is een alleen-lezen weergave van uw metagegevens. U hoeft de foutopsporingsmodus niet te hebben ingeschakeld om metagegevens in het deelvenster **Inspecteren** te zien.

![Inspecteren](media/data-flow/inspect1.png "Inspecteren")

Als u de vorm van uw gegevens wijzigt door middel van transformaties, ziet u de wijzigingen in de metagegevens stromen in het deelvenster **Inspect.** Als er geen gedefinieerd schema in uw brontransformatie is, zijn metagegevens niet zichtbaar in het deelvenster **Inspecteren.** Gebrek aan metadata is gebruikelijk in schema drift scenario's.

#### <a name="data-preview"></a>Voorbeeld van gegevens

Als de foutopsporingsmodus is ingeschakeld, krijgt u op het tabblad **Gegevensvoorbeeld** een interactieve momentopname van de gegevens bij elke transformatie. Zie [Voorbeeld van gegevens in de foutopsporingsmodus](concepts-data-flow-debug-mode.md#data-preview)voor meer informatie .

### <a name="top-bar"></a>Bovenste balk

De bovenste balk bevat acties die van invloed zijn op de hele gegevensstroom, zoals opslaan en valideren. U ook schakelen tussen grafiek- en configuratiemodi met behulp van de knoppen **Grafiek weergeven** en **Grafiek verbergen.**

![Grafiek verbergen](media/data-flow/hideg.png "Grafiek verbergen")

Als u uw grafiek verbergt, u lateraal door uw transformatieknooppunten bladeren via de knoppen **Vorige** en **Volgende.**

![Vorige en volgende knoppen](media/data-flow/showhide.png "vorige en volgende knoppen")

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het maken van een [brontransformatie](data-flow-source.md).
* Meer informatie over het bouwen van uw gegevensstromen in [de foutopsporingsmodus.](concepts-data-flow-debug-mode.md)
