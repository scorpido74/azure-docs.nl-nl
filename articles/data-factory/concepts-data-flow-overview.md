---
title: Toewijzing gegevensstromen
description: Een overzicht van het toewijzen van gegevens stromen in Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/09/2020
ms.openlocfilehash: e8efb43ac0711bac1324ac2c9e3b59373ce59419
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84635119"
---
# <a name="what-are-mapping-data-flows"></a>Wat zijn toewijzingsgegevensstromen?

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Het toewijzen van gegevens stromen zijn visueel ontworpen gegevens transformaties in Azure Data Factory. Met gegevens stromen kunnen data engineers grafische gegevens transformatie logica ontwikkelen zonder code te schrijven. De resulterende gegevens stromen worden uitgevoerd als activiteiten binnen Azure Data Factory pijp lijnen die gebruikmaken van uitgeschaalde Apache Spark clusters. Gegevens stroom activiteiten kunnen worden ingeschakeld via bestaande Data Factory plannings-, beheer-, stroom-en bewakings mogelijkheden.

Het toewijzen van gegevens stromen biedt een volledig visuele ervaring zonder code ring vereist. Uw gegevens stromen worden uitgevoerd op uw uitvoerings cluster voor uitgeschaalde gegevens verwerking. Azure Data Factory verwerkt alle code omzetting, optimalisatie van paden en de uitvoering van uw gegevens stroom taken.

![Architectuur](media/data-flow/adf-data-flows.png "Architectuur")

## <a name="getting-started"></a>Aan de slag

Als u een gegevens stroom wilt maken, selecteert u het plus teken onder **Factory-resources**en selecteert u vervolgens **gegevens stroom**. 

![Nieuwe gegevens stroom](media/data-flow/newdataflow2.png "nieuwe gegevens stroom")

Met deze actie gaat u naar het canvas voor gegevens stromen, waar u uw transformatie logica kunt maken. Selecteer **bron toevoegen** om het configureren van de bron transformatie te starten. Zie [bron transformatie](data-flow-source.md)voor meer informatie.

## <a name="data-flow-canvas"></a>Canvas voor gegevens stroom

Het canvas voor de gegevens stroom is onderverdeeld in drie delen: de bovenste balk, de grafiek en het configuratie paneel. 

![Canvas](media/data-flow/canvas1.png "Canvas")

### <a name="graph"></a>Graph

In de grafiek wordt de transformatie stroom weer gegeven. De afkomst van de bron gegevens worden weer gegeven terwijl deze in een of meer sinks worden stromen. Selecteer **bron toevoegen**om een nieuwe bron toe te voegen. Als u een nieuwe trans formatie wilt toevoegen, selecteert u het plus teken aan de rechter benedenhoek van een bestaande trans formatie.

![Canvas](media/data-flow/canvas2.png "Canvas")

### <a name="azure-integration-runtime-data-flow-properties"></a>Eigenschappen van gegevens stroom voor Azure Integration runtime

![Knop fout opsporing](media/data-flow/debugbutton.png "Knop fout opsporing")

Wanneer u begint met het werken met gegevens stromen in ADF, wilt u de schakel optie voor fout opsporing inschakelen voor gegevens stromen boven aan de gebruikers interface van de browser. Dit draait om een Spark-cluster dat moet worden gebruikt voor interactieve fout opsporing, gegevens voorvertoningen en de uitvoering van de fout opsporing voor de pijp lijn. U kunt de grootte van het gebruikte cluster instellen door een aangepaste [Azure Integration runtime](concepts-integration-runtime.md)te kiezen. De foutopsporingssessie kan Maxi maal 60 minuten na de laatste gegevens preview of de laatste probleemoplossings pijplijn worden uitgevoerd.

Wanneer u uw pijp lijnen operationeel maken met gegevens stroom activiteiten, gebruikt ADF het Azure Integration Runtime dat is gekoppeld aan de [activiteit](control-flow-execute-data-flow-activity.md) in de eigenschap ' uitvoeren op '.

De standaard Azure Integration Runtime is een klein 4-core knoop punt cluster met één worker waarmee u gegevens kunt bekijken en snel probleemoplossings pijplijnen met minimale kosten. Stel een grotere Azure IR configuratie in als u bewerkingen uitvoert voor grote gegevens sets.

U kunt de ADF de opdracht geven om een groep cluster resources (Vm's) te onderhouden door een TTL in te stellen in de Azure IR eigenschappen van de gegevens stroom. Deze actie resulteert in een snellere taak uitvoering voor volgende activiteiten.

#### <a name="azure-integration-runtime-and-data-flow-strategies"></a>Azure Integration runtime en data flow-strategieën

##### <a name="execute-data-flows-in-parallel"></a>Gegevens stromen parallel uitvoeren

Als u gegevens stromen in een pijp lijn parallel uitvoert, worden met ADF afzonderlijke Spark-clusters voor elke uitvoering van de activiteit gespind op basis van de instellingen in uw Azure Integration Runtime die aan elke activiteit zijn gekoppeld. Als u parallelle uitvoeringen wilt ontwerpen in ADF-pijp lijnen, voegt u uw gegevens stroom activiteiten toe zonder prioriteits beperkingen in de gebruikers interface.

Van deze drie opties wordt deze optie waarschijnlijk in de kortste tijd uitgevoerd. Elke parallelle gegevens stroom wordt echter op hetzelfde moment uitgevoerd op afzonderlijke clusters, zodat de volg orde van gebeurtenissen niet-deterministisch is.

Als u uw gegevensstroom activiteiten parallel in uw pijp lijnen uitvoert, wordt aanbevolen geen TTL te gebruiken. Deze actie is omdat parallelle uitvoeringen van uw gegevens stroom gelijktijdig met dezelfde Azure Integration Runtime resulteert in meerdere warme groeps instanties voor uw data factory.

##### <a name="overload-single-data-flow"></a>Overbelasting van één gegevens stroom

Als u al uw logica in één gegevens stroom plaatst, voert ADF diezelfde taak uitvoerings context uit op één Spark-cluster exemplaar.

Deze optie kan lastiger zijn om te volgen en problemen op te lossen, omdat uw bedrijfs regels en bedrijfs logica onsamenhangend kunnen zijn. Deze optie biedt ook veel herbruikbaarheid.

##### <a name="execute-data-flows-sequentially"></a>Gegevens stromen sequentieel uitvoeren

Als u de gegevens stroom activiteiten in volg orde van de pijp lijn uitvoert en u een TTL hebt ingesteld in de configuratie van de Azure IR, worden de reken resources (Vm's) door ADF opnieuw gebruikt, wat resulteert in snellere uitvoerings tijden. Er wordt nog steeds een nieuwe Spark-context voor elke uitvoering ontvangen.

Van deze drie opties neemt deze actie waarschijnlijk de langste tijd om end-to-end uit te voeren. Dit biedt echter een duidelijke schei ding van logische bewerkingen in elke stap van de gegevens stroom.

### <a name="configuration-panel"></a>Configuratie paneel

In het configuratie scherm worden de instellingen weer gegeven die specifiek zijn voor de momenteel geselecteerde trans formatie. Als er geen trans formatie is geselecteerd, wordt de gegevens stroom weer gegeven. In de algehele configuratie van de gegevens stroom kunt u de naam en beschrijving bewerken op het tabblad **Algemeen** of para meters toevoegen via het tabblad **para meters** . Zie [Data flow-para meters toewijzen](parameters-data-flow.md)voor meer informatie.

Elke trans formatie bevat ten minste vier configuratie tabbladen.

#### <a name="transformation-settings"></a>Instellingen voor trans formatie

Het eerste tabblad in het configuratie venster van elke trans formatie bevat de instellingen die specifiek zijn voor die trans formatie. Zie de documentatie pagina van die trans formatie voor meer informatie.

![Tabblad Bron instellingen](media/data-flow/source1.png "Tabblad Bron instellingen")

#### <a name="optimize"></a>Optimaliseren

Het tabblad **Optimize** bevat instellingen voor het configureren van partitie schema's.

![Optimaliseren](media/data-flow/optimize1.png "Optimaliseren")

De standaard instelling is **huidige partitionering gebruiken**, waarmee wordt Azure Data Factory het partitioneren van het schema systeem eigen te gebruiken voor gegevens stromen die worden uitgevoerd op Spark. In de meeste scenario's wordt deze instelling aangeraden.

Er zijn exemplaren waarvan u de partitionering mogelijk wilt aanpassen. Als u bijvoorbeeld uw trans formaties naar één bestand in het Lake wilt uitvoeren, selecteert u **één partitie** in een Sink-trans formatie.

Het is ook mogelijk dat u de prestaties van het partitioneren van schema's wilt beheren. Het aanpassen van de partitionering biedt controle over de distributie van uw gegevens over reken knooppunten en gegevens lokale optimalisaties die zowel positieve als negatieve effecten kunnen hebben op de prestaties van uw algemene gegevens stroom. Zie de [Data flow-prestatie handleiding](concepts-data-flow-performance.md)voor meer informatie.

Als u het partitioneren van een trans formatie wilt wijzigen, selecteert u het tabblad **optimaliseren** en selecteert u het keuze rondje **partitioneren instellen** . U krijgt een reeks opties voor partitioneren. De beste methode voor partitionering verschilt op basis van uw gegevens volumes, kandidaat-sleutels, null-waarden en kardinaliteit. 

Een best practice is om te beginnen met de standaard partitionering en probeer verschillende opties voor partitioneren. U kunt testen met behulp van de uitvoering van de fout opsporing voor de pijp lijn en de uitvoerings tijd en het partitie gebruik in elke transformatie groepering weer geven vanuit de weer gave controle. Zie [gegevens stromen bewaken](concepts-data-flow-monitoring.md)voor meer informatie.

De volgende opties voor partitioneren zijn beschikbaar.

##### <a name="round-robin"></a>Round Robin 

Round Robin is een eenvoudige partitie waarmee gegevens gelijkmatig over partities worden verdeeld. Gebruik Round Robin wanneer u niet over de juiste belangrijkste kandidaten beschikt voor het implementeren van een solide strategie voor slimme partitionering. U kunt het aantal fysieke partities instellen.

##### <a name="hash"></a>Hash

Azure Data Factory produceert een hash van kolommen om uniforme partities te maken, zodat rijen met vergelijk bare waarden in dezelfde partitie vallen. Wanneer u de hash-optie gebruikt, moet u testen op mogelijke partitie scheefheid. U kunt het aantal fysieke partities instellen.

##### <a name="dynamic-range"></a>Dynamisch bereik

Het dynamische bereik maakt gebruik van Spark dynamische bereiken op basis van de kolommen of expressies die u opgeeft. U kunt het aantal fysieke partities instellen. 

##### <a name="fixed-range"></a>Vast bereik

Bouw een expressie die een vast bereik voor waarden in de gepartitioneerde gegevens kolommen levert. U moet een goed idee hebben van uw gegevens voordat u deze optie gebruikt om het hellen van partities te voor komen. De waarden die u voor de expressie invoert, worden gebruikt als onderdeel van een partitie functie. U kunt het aantal fysieke partities instellen.

##### <a name="key"></a>Sleutel

Als u een goed beeld hebt van de kardinaliteit van uw gegevens, is het mogelijk dat sleutel partities een goede strategie zijn. Met sleutel partities maakt u partities voor elke unieke waarde in uw kolom. U kunt het aantal partities niet instellen omdat het nummer is gebaseerd op unieke waarden in de gegevens.

#### <a name="inspect"></a>Controleert

Het tabblad **controleren** bevat een weer gave van de meta gegevens van de gegevensstroom die u transformeert. U kunt kolom aantallen zien, de kolommen gewijzigd, de kolommen die zijn toegevoegd, de gegevens typen, de kolom volgorde en kolom verwijzingen. **Inspecteer** is een alleen-lezen weer gave van uw meta gegevens. U hoeft de foutopsporingsmodus niet in te scha kelen om meta gegevens in het deel venster **controleren** weer te geven.

![Controleert](media/data-flow/inspect1.png "Controleert")

Wanneer u de vorm van uw gegevens via trans formaties wijzigt, worden de wijzigingen in de meta gegevens in het deel venster **controleren** weer gegeven. Als er geen gedefinieerd schema is in uw bron transformatie, worden de meta gegevens niet weer gegeven in het deel venster **controleren** . Het ontbreken van meta gegevens is gebruikelijk in schema-drift-scenario's.

#### <a name="data-preview"></a>Voorbeeld van gegevens

Als de foutopsporingsmodus is ingeschakeld, biedt het tabblad **voor beeld van gegevens** een interactieve moment opname van de gegevens bij elke trans formatie. Zie voor meer informatie de [Preview van gegevens in de foutopsporingsmodus](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Bovenste balk

De bovenste balk bevat acties die van invloed zijn op de hele gegevens stroom, zoals opslaan en valideren. U kunt ook tussen grafiek-en configuratie modi scha kelen met behulp van de knoppen **diagram weer geven** en **grafiek verbergen** .

![Grafiek verbergen](media/data-flow/hideg.png "Grafiek verbergen")

Als u uw grafiek verbergt, kunt u op een later tijdstip door uw transformatie knooppunten bladeren via de knoppen **vorige** en **volgende** .

![Knoppen Vorige en volgende](media/data-flow/showhide.png "knoppen Vorige en volgende")

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het maken van een [bron transformatie](data-flow-source.md).
* Meer informatie over het bouwen van uw gegevens stromen in de [foutopsporingsmodus](concepts-data-flow-debug-mode.md).
