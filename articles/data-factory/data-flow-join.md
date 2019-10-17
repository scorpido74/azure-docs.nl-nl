---
title: Data stroom-koppelings transformatie Azure Data Factory
description: Data stroom-koppelings transformatie Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: da6c3c90ebbeffcf468aad3809da097976d8ef0d
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387228"
---
# <a name="mapping-data-flow-join-transformation"></a>Data flow-koppelings transformatie koppelen



Gebruik samen voegen om gegevens uit twee tabellen in uw gegevens stroom te combi neren. Klik op de trans formatie die de relatie links is en voeg een koppelings transformatie toe vanuit de werkset. In de deelname transformatie selecteert u een andere gegevens stroom van uw gegevens stroom naar de juiste relatie.

![Trans formatie koppelen](media/data-flow/join.png "Koppelen")

## <a name="join-types"></a>Koppelings typen

Het selecteren van een jointype is vereist voor de trans formatie voor samen voegen.

### <a name="inner-join"></a>Inner join

Inner join geeft alleen rijen door die overeenkomen met de kolom voorwaarden uit beide tabellen.

### <a name="left-outer"></a>Linker outer join

Alle rijen van de linkertabel die niet voldoen aan de voor waarde voor samen voeging, worden door gegeven en uitvoer kolommen uit de andere tabel zijn ingesteld op NULL naast alle rijen die door de inner join worden geretourneerd.

### <a name="right-outer"></a>Rechter outer join

Alle rijen uit de rechter stroom die niet aan de voor waarde voor samen voegen voldoen, worden door gegeven en uitvoer kolommen die overeenkomen met de andere tabel worden ingesteld op NULL, naast alle rijen die door de inner join worden geretourneerd.

### <a name="full-outer"></a>Volledige outer join

Full outer produceert alle kolommen en rijen van beide zijden met NULL-waarden voor kolommen die niet aanwezig zijn in de andere tabel.

### <a name="cross-join"></a>Cross-koppeling

Geef het Kruis product van de twee stromen op met een expressie. U kunt deze gebruiken om aangepaste voor waarden voor samen voeging te maken.

## <a name="specify-join-conditions"></a>Voor waarden voor samen voegen opgeven

De left samenvoegings voorwaarde is afkomstig van de gegevens stroom die aan de linkerkant van de koppeling is verbonden. De Right-voor waarde is de tweede gegevens stroom die aan de onderkant is verbonden met uw koppeling. Dit is een directe connector naar een andere stroom of een verwijzing naar een andere stroom.

U moet ten minste 1 (1.. n) voor waarden voor samen voegen invoeren. Dit kunnen velden zijn waarnaar direct wordt verwezen, geselecteerd in de vervolg keuzelijst of op expressies.

## <a name="join-performance-optimizations"></a>Prestaties optimaliseren

In tegens telling tot samen voegen in hulpprogram ma's als SSIS is samen voegen in de ADF-gegevens stroom geen verplichte samenvoeg bewerking. Daarom hoeven de toetsen voor samen voegen niet eerst te worden gesorteerd. De koppelings bewerking wordt uitgevoerd op basis van de optimale koppelings bewerking in Spark: Broadcast/toewijzing aan de zijkant:

![Trans formatie optimaliseren](media/data-flow/joinoptimize.png "Deelname aan optimalisatie")

Als uw gegevensset kan worden aangepast in het geheugen van het worker-knoop punt, kunnen we uw deelname prestaties optimaliseren. U kunt ook het partitioneren van uw gegevens op de koppelings bewerking opgeven om gegevens sets te maken die beter in het geheugen kunnen passen per werk nemer.

## <a name="self-join"></a>Self-deelname

U kunt Self-samenvoegings voorwaarden in de ADF-gegevens stroom behaalt met behulp van de trans formatie selecteren als alias voor een bestaande stroom. Maak eerst een ' nieuwe vertakking ' vanuit een stroom en voeg vervolgens een SELECT toe aan een alias voor de hele oorspronkelijke stroom.

![Self-deelname](media/data-flow/selfjoin.png "Self-deelname")

In het bovenstaande diagram bevindt de geselecteerde trans formatie zich bovenaan. Het gaat hierbij om het aliassen van de oorspronkelijke stroom naar ' OrigSourceBatting '. In de onderstaande gemarkeerde join-trans formatie ziet u dat we deze Select alias Stream gebruiken als de rechter join, zodat we naar dezelfde sleutel in de linker & rechter kant van de inner join kunnen verwijzen.

## <a name="composite-and-custom-keys"></a>Samengestelde en aangepaste sleutels

U kunt op elk gewenst moment aangepaste en samengestelde sleutels bouwen in de trans formatie voor samen voegen. Voeg rijen toe voor aanvullende samenvoegings kolommen met het plus teken (+) naast elke rij van de relatie. Of Bereken een nieuwe sleutel waarde in de opbouw functie voor expressies voor een on-the-vliegen-samenvoegings waarde.

## <a name="next-steps"></a>Volgende stappen

Nadat u gegevens hebt toegevoegd, kunt u [nieuwe kolommen maken](data-flow-derived-column.md) en [uw gegevens naar een doel gegevens archief sinken](data-flow-sink.md).
