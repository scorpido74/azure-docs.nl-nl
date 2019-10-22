---
title: Gegevens stromen toewijzen in Azure Data Factory | Microsoft Docs
description: Een overzicht van het toewijzen van gegevens stromen in Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/7/2019
ms.openlocfilehash: 94bde7b2e2a6f3902d83de90b06638035fd34397
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72679127"
---
# <a name="what-are-mapping-data-flows"></a>Wat zijn gegevens stromen toewijzen?

Het toewijzen van gegevens stromen zijn visueel ontworpen gegevens transformaties in Azure Data Factory. Met gegevens stromen kunnen data engineers grafische gegevens transformatie logica ontwikkelen zonder code te schrijven. De resulterende gegevens stromen worden uitgevoerd als activiteiten binnen Azure Data Factory pijp lijnen die gebruikmaken van geschaalde Spark-clusters. Gegevens stroom activiteiten kunnen worden uitgevoerd via bestaande Data Factory plannings-, beheer-, stroom-en bewakings mogelijkheden.

Het toewijzen van gegevens stromen biedt een volledig visuele ervaring zonder code ring vereist. Uw gegevens stromen worden uitgevoerd op uw eigen uitvoerings cluster voor uitgeschaalde gegevens verwerking. Azure Data Factory verwerkt alle code omzetting, optimalisatie van paden en de uitvoering van uw gegevens stroom taken.

## <a name="getting-started"></a>Aan de slag

Als u een gegevens stroom wilt maken, selecteert u het plus teken onder **Factory-resources**en selecteert u vervolgens **gegevens stroom**. 

![Nieuwe gegevens stroom](media/data-flow/newdataflow2.png "nieuwe gegevens stroom")

Hiermee gaat u naar het canvas voor gegevens stromen waar u uw transformatie logica kunt maken. Selecteer **bron toevoegen** om het configureren van de bron transformatie te starten. Zie [bron transformatie](data-flow-source.md)voor meer informatie.

## <a name="data-flow-canvas"></a>Canvas voor gegevens stroom

Het canvas voor de gegevens stroom is onderverdeeld in drie delen: de bovenste balk, de grafiek en het configuratie paneel. 

![Geval](media/data-flow/canvas1.png "Geval")

### <a name="graph"></a>Grafiek

In de grafiek wordt de transformatie stroom weer gegeven. De afkomst van de bron gegevens worden weer gegeven terwijl deze in een of meer sinks worden stromen. Selecteer **bron toevoegen**om een nieuwe bron toe te voegen. Als u een nieuwe trans formatie wilt toevoegen, selecteert u het plus teken aan de rechter benedenhoek van een bestaande trans formatie.

![Geval](media/data-flow/canvas2.png "Geval")

### <a name="configuration-panel"></a>Configuratie paneel

In het configuratie scherm worden de instellingen weer gegeven die specifiek zijn voor de momenteel geselecteerde trans formatie. Als er geen trans formatie is geselecteerd, wordt de gegevens stroom weer gegeven. In de algehele configuratie van de gegevens stroom kunt u de naam en beschrijving bewerken op het tabblad **Algemeen** of para meters toevoegen via het tabblad **para meters** . Zie [Data flow-para meters toewijzen](parameters-data-flow.md)voor meer informatie.

Elke trans formatie heeft ten minste vier configuratie tabbladen.

#### <a name="transformation-settings"></a>Instellingen voor trans formatie

Het eerste tabblad in het configuratie venster van elke trans formatie bevat de instellingen die specifiek zijn voor die trans formatie. Zie de documentatie pagina van die trans formatie voor meer informatie.

![Tabblad Bron instellingen](media/data-flow/source1.png "Tabblad Bron instellingen")

#### <a name="optimize"></a>Optimaliseren

Het tabblad **Optimize** bevat instellingen voor het configureren van partitie schema's.

![Optimaliseren](media/data-flow/optimize1.png "Optimaliseren")

De standaard instelling is **huidige partitionering gebruiken**, waarmee wordt Azure Data Factory het partitioneren van het schema systeem eigen te gebruiken voor gegevens stromen die worden uitgevoerd op Spark. In de meeste scenario's wordt deze instelling aangeraden.

Er zijn exemplaren waarvan u de partitionering mogelijk wilt aanpassen. Als u bijvoorbeeld uw trans formaties naar één bestand in het Lake wilt uitvoeren, selecteert u **één partitie** in een Sink-trans formatie.

Het is ook mogelijk dat u de prestaties van het partitioneren van schema's wilt beheren. Het aanpassen van de partitionering biedt controle over de distributie van uw gegevens over reken knooppunten en gegevens lokale optimalisaties die zowel positieve als negatieve effecten kunnen hebben op de prestaties van uw algemene gegevens stroom. Zie de [Data flow-prestatie handleiding](concepts-data-flow-performance.md)voor meer informatie.

Als u het partitioneren van een trans formatie wilt wijzigen, selecteert u het tabblad **optimaliseren** en selecteert u het keuze rondje **partitioneren instellen** . Vervolgens krijgt u een reeks opties voor partitioneren. De beste manier om partities te partitioneren is afhankelijk van uw gegevens volumes, kandidaat-sleutels, null-waarden en kardinaliteit. 

Een best practice is om te beginnen met de standaard partitionering en probeer verschillende opties voor partitioneren. U kunt testen met behulp van de uitvoering van de fout opsporing voor de pijp lijn en de uitvoerings tijd en het partitie gebruik in elke transformatie groepering weer geven vanuit de weer gave controle. Zie [gegevens stromen bewaken](concepts-data-flow-monitoring.md)voor meer informatie.

De volgende opties voor partitioneren zijn beschikbaar.

##### <a name="round-robin"></a>Round Robin 

Round Robin is een eenvoudige partitie waarmee gegevens gelijkmatig over partities worden verdeeld. Gebruik round robin als u niet beschikt over de juiste belangrijkste kandidaten voor het implementeren van een solide strategie voor slimme partitionering. U kunt het aantal fysieke partities instellen.

##### <a name="hash"></a>Hash

Azure Data Factory produceert een hash van kolommen voor het produceren van uniforme partities, zodat rijen met vergelijk bare waarden in dezelfde partitie vallen. Wanneer u de hash-optie gebruikt, moet u testen op mogelijke partitie scheefheid. U kunt het aantal fysieke partities instellen.

##### <a name="dynamic-range"></a>Dynamisch bereik

Het dynamische bereik maakt gebruik van Spark dynamische bereiken op basis van de kolommen of expressies die u opgeeft. U kunt het aantal fysieke partities instellen. 

##### <a name="fixed-range"></a>Vast bereik

Bouw een expressie die een vast bereik voor waarden in de gepartitioneerde gegevens kolommen levert. U moet een goed idee hebben van uw gegevens voordat u deze optie gebruikt om het hellen van partities te voor komen. De waarden die u voor de expressie invoert, worden gebruikt als onderdeel van een partitie functie. U kunt het aantal fysieke partities instellen.

##### <a name="key"></a>Sleutel

Als u een goed beeld hebt van de kardinaliteit van uw gegevens, is het mogelijk dat sleutel partities een goede strategie zijn. Bij sleutel partities worden partities gemaakt voor elke unieke waarde in de kolom. U kunt het aantal partities niet instellen omdat het aantal is gebaseerd op unieke waarden in de gegevens.

#### <a name="inspect"></a>Controleert

Het tabblad **controleren** bevat een weer gave van de meta gegevens van de gegevensstroom die u transformeert. U kunt de kolom aantallen, gewijzigde kolommen, toegevoegde kolommen, gegevens typen, kolom ordening en kolom verwijzingen bekijken. **Inspecteer** is een alleen-lezen weer gave van uw meta gegevens. U hoeft de foutopsporingsmodus niet in te scha kelen om meta gegevens in het deel venster **controleren** weer te geven.

![Controleert](media/data-flow/inspect1.png "Controleert")

Wanneer u de vorm van uw gegevens via trans formaties wijzigt, worden de wijzigingen in de meta gegevens in het deel venster **controleren** weer gegeven. Als er geen gedefinieerd schema is in uw bron transformatie, worden de meta gegevens niet weer gegeven in het deel venster **controleren** . Het ontbreken van meta gegevens is gebruikelijk in schema-drift-scenario's.

#### <a name="data-preview"></a>Voor beeld van gegevens

Als de foutopsporingsmodus is ingeschakeld, biedt het tabblad **voor beeld van gegevens** een interactieve moment opname van de gegevens bij elke trans formatie. Zie voor meer informatie de [Preview van gegevens in de foutopsporingsmodus](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Bovenste balk

De bovenste balk bevat acties die van invloed zijn op de hele gegevens stroom, zoals opslaan en valideren. U kunt ook tussen grafiek-en configuratie modi scha kelen met behulp van de knoppen **diagram weer geven** en **grafiek verbergen** .

![Grafiek verbergen](media/data-flow/hideg.png "Grafiek verbergen")

Als u uw grafiek verbergt, kunt u op een later tijdstip door uw transformatie knooppunten bladeren via de knoppen **vorige** en **volgende** .

![Knoppen Vorige en volgende](media/data-flow/showhide.png "knoppen Vorige en volgende")

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het maken van een [bron transformatie](data-flow-source.md).
* Meer informatie over het bouwen van uw gegevens stromen in de [foutopsporingsmodus](concepts-data-flow-debug-mode.md).
