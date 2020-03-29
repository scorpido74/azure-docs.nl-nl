---
title: Een document uploaden - Aangepaste vertaler
titleSuffix: Azure Cognitive Services
description: De functie voor het uploaden van documenten uploadt parallelle documenten (twee documenten waarbij de ene de oorsprong is en de andere de vertaling) naar de service.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: b2a249a40d8c782d54a12df43d33655f3409753c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647388"
---
# <a name="upload-a-document"></a>Een document uploaden

In [Aangepaste vertaler](https://portal.customtranslator.azure.ai)u parallelle documenten uploaden om uw vertaalmodellen te trainen. [Parallelle documenten](what-are-parallel-documents.md) zijn paren van documenten waarbij de ene een vertaling van de andere is. Een document in het paar bevat zinnen in de brontaal en het andere document bevat deze zinnen vertaald in de doeltaal.

Voordat u uw documenten uploadt, controleert u de [documentindelingen en richtlijnen voor naamgevingsconventieom](document-formats-naming-convention.md) om ervoor te zorgen dat uw bestandsindeling wordt ondersteund in Aangepaste vertaler.

## <a name="how-to-upload-document"></a>Hoe document uploaden?

Klik in de portal [Voor aangepaste vertalers](https://portal.customtranslator.azure.ai) op het tabblad Documenten om naar de pagina Documenten te gaan.

![Koppeling voor het uploaden van documenten](media/how-to/how-to-upload-1.png)


1.  Klik op de knop Bestanden uploaden op de pagina documenten.

    ![Documentpagina uploaden](media/how-to/how-to-upload-2.png)

2.  Vul in het dialoogvenster de volgende gegevens in:

    a.  Documenttype:

    -  Training: Deze documenten worden gebruikt voor de trainingsset.
    -  Tuning: Deze documenten worden gebruikt voor het afstemmen van de set.
    -  Testen: Deze documenten worden gebruikt voor het testen van de set.
    -  Woordlijst: deze document(s) worden gebruikt voor woordwoordenlijst.
    -  Zinwoordenboek: deze document(s) worden gebruikt voor zinswoordenboek

    b.  Taalpaar

    c.  Document overschrijven als dit bestaat: schakel dit selectievakje in als u bestaande documenten met dezelfde naam wilt overschrijven.

    d.  Vul de relevante sectie in voor parallelle gegevens of combogegevens.

    -  Parallelle gegevens:
        -  Bronbestand: Selecteer brontaalbestand van uw lokale computer.
        -  Doelbestand: selecteer doeltaalbestand van uw lokale computer.
        -  Documentnaam: alleen gebruikt als u parallelle bestanden uploadt.

    - Combo-gegevens:
        -  Combinatiebestand: selecteer het combinatiebestand vanaf uw lokale computer. Uw combinatiebestand heeft zowel van uw bron als doeltaalzinnen. [Naamgevingconventie](document-formats-naming-convention.md) is belangrijk voor combobestanden.

    e.  Klik op Uploaden

    ![Dialoogvenster Document uploaden](media/how-to/how-to-upload-dialog.png)

3.  Op dit moment verwerken we uw documenten en proberen we zinnen te extraheren. U op 'Voortgang uploaden weergeven' klikken om de status van uw documenten te controleren terwijl ze worden verwerkt.

    ![Dialoogvenster Documentverwerking uploaden](media/how-to/how-to-upload-processing-dialog.png)

4.  Op deze pagina worden de status en eventuele fouten voor elk bestand binnen uw upload weergegeven. Je de status van het aantal eerdere uploaden op elk gewenst moment bekijken door op het tabblad Uploadgeschiedenis te klikken.

    ![Dialoogvenster Documentgeschiedenis uploaden](media/how-to/how-to-upload-document-history.png)


## <a name="view-upload-history"></a>Uploadgeschiedenis weergeven

Op de pagina Uploadgeschiedenis u de geschiedenis van alle documentuploads bekijken, zoals documenttype, taalpaar, uploadstatus enz.

1. Klik in de portal [Aangepaste vertaler](https://portal.customtranslator.azure.ai) op Het tabblad Geschiedenis uploaden om de geschiedenis weer te geven.

    ![Tabblad Uploadgeschiedenis](media/how-to/how-to-upload-history-1.png)

2. Deze pagina toont de status van al uw eerdere uploads. Het toont uploads van meest recente tot minst recente. Voor elke upload wordt de documentnaam, de uploadstatus, de uploaddatum, het aantal geüploade bestanden, het type geüpload bestand en het taalpaar van het bestand weergegeven.

    ![Pagina Uploadgeschiedenis](media/how-to/how-to-document-history-2.png)

3. Klik op een uploadgeschiedenisrecord. Op de pagina details van de uploadgeschiedenis u de bestanden bekijken die zijn geüpload als onderdeel van de upload, de geüploade status van het bestand, de taal van het bestand en het foutbericht (als er een fout is in het uploaden).

## <a name="next-steps"></a>Volgende stappen

- Gebruik de [pagina documentdetails](how-to-view-document-details.md) om de lijst met uitgepakte zinnen te bekijken.
- [Hoe een model te trainen](how-to-train-model.md).
