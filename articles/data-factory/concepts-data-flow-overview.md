---
title: Gegevens stromen toewijzen in Azure Data Factory | Microsoft Docs
description: Een overzicht van het toewijzen van gegevens stromen in Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/7/2019
ms.openlocfilehash: 8d0ad794caee8a06c8d403a981037d6560fb3f43
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030106"
---
# <a name="what-are-mapping-data-flows"></a>Wat zijn toewijzingsgegevensstromen?

Het toewijzen van gegevens stromen zijn visueel ontworpen gegevens transformaties in Azure Data Factory. Met gegevens stromen kunnen data engineers grafische gegevens transformatie logica ontwikkelen zonder code te schrijven. De resulterende gegevens stromen worden uitgevoerd als activiteiten binnen Azure Data Factory pijp lijnen met behulp van uitgeschaalde Spark-clusters. Gegevens stroom activiteiten kunnen worden uitgevoerd via bestaande Data Factory plannings-, beheer-, flow-en bewakings mogelijkheden.

Het toewijzen van gegevens stromen biedt een volledig visuele ervaring zonder code ring vereist. Uw gegevens stromen worden uitgevoerd op uw eigen uitvoerings cluster voor uitgeschaalde gegevens verwerking. Azure Data Factory verwerkt alle code omzetting, optimalisatie van paden en de uitvoering van uw gegevens stroom taken.

## <a name="getting-started"></a>Aan de slag

Als u een gegevens stroom wilt maken, klikt u op het plus teken onder Factory-resources. 

nieuwe gegevensstroom ![nieuwe gegevens](media/data-flow/newdataflow2.png "stroom")

Hiermee gaat u naar het canvas voor gegevens stromen waar u uw transformatie logica kunt maken. Klik op het vak Bron toevoegen om het configureren van de bron transformatie te starten. Zie [bron transformatie](data-flow-source.md)voor meer informatie.

## <a name="data-flow-canvas"></a>Canvas voor gegevens stroom

Het canvas voor de gegevens stroom is onderverdeeld in drie delen: de bovenste balk, de grafiek en het configuratie paneel. 

![Canvas](media/data-flow/canvas1.png "Canvas")

### <a name="graph"></a>Graph

In de grafiek wordt de transformatie stroom weer gegeven. De afkomst van de bron gegevens worden weer gegeven terwijl deze in een of meer sinks worden stromen. Klik op het vak Bron toevoegen om een nieuwe bron toe te voegen. Als u een nieuwe trans formatie wilt toevoegen, klikt u op het plus teken aan de rechter kant van een bestaande trans formatie.

![Canvas](media/data-flow/canvas2.png "Canvas")

### <a name="configuration-panel"></a>Configuratie paneel

In het configuratie scherm worden de instellingen weer gegeven die specifiek zijn voor de momenteel geselecteerde trans formatie of, als er geen trans formatie is geselecteerd, de gegevens stroom. In de algehele configuratie van de gegevens stroom kunt u de naam en beschrijving bewerken op het tabblad **Algemeen** of para meters toevoegen via het tabblad **para meters** . Zie [Data flow-para meters toewijzen](parameters-data-flow.md)voor meer informatie.

Elke trans formatie heeft ten minste vier configuratie tabbladen:

#### <a name="transformation-settings"></a>Instellingen voor trans formatie

Het eerste tabblad in het configuratie venster van elke trans formatie bevat de instellingen die specifiek zijn voor die trans formatie. Raadpleeg de documentatie pagina van die trans formatie voor meer informatie.

Tabblad Bron ![instellingen]tabblad(media/data-flow/source1.png "bron instellingen")

#### <a name="optimize"></a>Optimaliseren

Het tabblad _Optimize_ bevat instellingen voor het configureren van partitie schema's.

![Optimaliseren optimaliseren](media/data-flow/optimize1.png "")

De standaard instelling is ' huidige partitionering gebruiken ', waarmee wordt Azure Data Factory het partitioneren van het schema systeem eigen te gebruiken voor gegevens stromen die worden uitgevoerd op Spark. In de meeste scenario's is deze instelling de aanbevolen methode.

Er zijn exemplaren waarvoor u de partities wilt aanpassen. Als u bijvoorbeeld uw trans formaties naar één bestand in het Lake wilt uitvoeren, kiest u enkele partitie in een Sink-trans formatie.

Het is ook mogelijk dat u de prestaties van het partitioneren van schema's wilt beheren. Het aanpassen van de partitionering biedt controle over de distributie van uw gegevens over reken knooppunten en gegevens lokale optimalisaties die zowel positieve als negatieve effecten kunnen hebben op de prestaties van uw algemene gegevens stroom. Zie de [Data flow-prestatie handleiding](concepts-data-flow-performance.md)voor meer informatie.

Als u het partitioneren van een trans formatie wilt wijzigen, klikt u op het tabblad optimaliseren en selecteert u het keuze rondje partitioneren instellen. Vervolgens krijgt u een reeks opties voor partitioneren. De beste manier om partities te partitioneren is afhankelijk van uw gegevens volumes, kandidaat-sleutels, null-waarden en kardinaliteit. Een best practice is om te beginnen met de standaard partitionering en probeer verschillende opties voor partitioneren. U kunt testen met behulp van de fout opsporing voor pijp lijnen uitvoeren en uitvoerings tijd en partitie gebruik in elke transformatie groepering weer geven in de weer gave controle. Zie [gegevens stromen bewaken](concepts-data-flow-monitoring.md)voor meer informatie.

Hieronder vindt u de beschik bare opties voor partitioneren.

##### <a name="round-robin"></a>Round robin 

Round Robin is een eenvoudige partitie waarmee gegevens gelijkmatig over de partities worden gedistribueerd. Gebruik Round Robin wanneer u niet over de juiste belangrijkste kandidaten beschikt voor het implementeren van een solide strategie voor slimme partitionering. U kunt het aantal fysieke partities instellen.

##### <a name="hash"></a>Hash

Azure Data Factory produceert een hash van kolommen voor het produceren van uniforme partities, zodat rijen met vergelijk bare waarden in dezelfde partitie vallen. Wanneer u de hash-optie gebruikt, moet u testen op mogelijke partitie scheefheid. U kunt het aantal fysieke partities instellen.

##### <a name="dynamic-range"></a>Dynamisch bereik

Het dynamische bereik maakt gebruik van Spark dynamische bereiken op basis van de kolommen of expressies die u opgeeft. U kunt het aantal fysieke partities instellen. 

##### <a name="fixed-range"></a>Vast bereik

Bouw een expressie die een vast bereik voor waarden in de gepartitioneerde gegevens kolommen levert. U moet een goed idee hebben van uw gegevens voordat u deze optie kunt gebruiken om te voor komen dat u het verschil partitioneert. De waarden die u voor de expressie invoert, worden gebruikt als onderdeel van een partitie functie. U kunt het aantal fysieke partities instellen.

##### <a name="key"></a>Sleutel

Als u een goed beeld hebt van de kardinaliteit van uw gegevens, is het mogelijk dat sleutel partities een goede partitie strategie zijn. Bij sleutel partities worden partities gemaakt voor elke unieke waarde in de kolom. U kunt het aantal partities niet instellen omdat het aantal is gebaseerd op unieke waarden in de gegevens.

#### <a name="inspect"></a>Controleert

Het tabblad _controleren_ bevat een weer gave van de meta gegevens van de gegevensstroom die u transformeert. U kunt de kolom aantallen, gewijzigde kolommen, toegevoegde kolommen, gegevens typen, kolom ordening en kolom verwijzingen bekijken. Inspecteer is een alleen-lezen weer gave van uw meta gegevens. U hoeft de foutopsporingsmodus niet in te scha kelen om meta gegevens in het deel venster controleren weer te geven.

![Controleren controleren](media/data-flow/inspect1.png "")

Wanneer u de vorm van uw gegevens via trans formaties wijzigt, worden de wijzigingen in de meta gegevens in het deel venster controleren weer gegeven. Als er geen gedefinieerd schema is in uw bron transformatie, worden de meta gegevens niet weer gegeven in het deel venster controleren. Het ontbreken van meta gegevens is gebruikelijk in schema-drift-scenario's.

#### <a name="data-preview"></a>Voor beeld van gegevens

Als de foutopsporingsmodus is ingeschakeld, biedt het tabblad _voor beeld van gegevens_ een interactieve moment opname van de gegevens bij elke trans formatie. Zie voor meer informatie de [Preview van gegevens in de foutopsporingsmodus](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Bovenste balk

De bovenste balk bevat acties die van invloed zijn op de hele gegevens stroom, zoals opslaan en valideren. U kunt ook scha kelen tussen de grafiek-en configuratie modus met behulp van de knoppen **diagram weer geven** en **grafieken verbergen** .

Grafiek ![verbergen grafiek](media/data-flow/hideg.png "verbergen")

Als u uw grafiek verbergt, kunt u later door uw transformatie knooppunten navigeren via de knoppen **vorige** en **volgende** .

![Navigeren](media/data-flow/showhide.png "")

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het maken van een [bron transformatie](data-flow-source.md)
* Meer informatie over het bouwen van uw gegevens stromen in [foutopsporingsmodus](concepts-data-flow-debug-mode.md)
