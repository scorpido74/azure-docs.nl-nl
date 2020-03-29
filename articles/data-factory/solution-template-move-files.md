---
title: Bestanden verplaatsen tussen bestandsopslag
description: Meer informatie over het gebruik van een oplossingssjabloon om bestanden te verplaatsen tussen bestandsopslag met Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/12/2019
ms.openlocfilehash: b3165daa06ed975df9ccb677699d3ceb449327ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74941900"
---
# <a name="move-files-with-azure-data-factory"></a>Bestanden verplaatsen met Azure Data Factory

In dit artikel wordt een oplossingssjabloon beschreven waarmee u bestanden van de ene map naar de andere verplaatsen tussen bestanden. Een van de meest voorkomende scenario's voor het gebruik van deze sjabloon: bestanden worden voortdurend naar een bestemmingsmap van uw bronarchief verwijderd. Door een planningstrigger te maken, kan de ADF-pijplijn deze bestanden periodiek verplaatsen van de bron naar het doelarchief.  De manier waarop ADF-pijplijn "bestanden verplaatsen" bereikt, is de bestanden uit de bestemmingsmap halen, elk van deze bestanden kopiëren naar een andere map in het doelarchief en vervolgens dezelfde bestanden verwijderen uit de bestemmingsmap in het bronarchief.

> [!NOTE]
> Houd er rekening mee dat deze sjabloon is ontworpen om bestanden te verplaatsen in plaats van mappen te verplaatsen.  Als u de map wilt verplaatsen door de gegevensset te wijzigen om deze alleen een mappad te laten bevatten, en vervolgens de kopieeractiviteit te gebruiken en activiteit te verwijderen om te verwijzen naar dezelfde gegevensset die een map vertegenwoordigt, moet u heel voorzichtig zijn. Het is omdat je ervoor moet zorgen dat er geen nieuwe bestanden in de map komen tussen kopieerbewerking en verwijderen. Als er nieuwe bestanden in de map aankomen op het moment dat uw kopieeractiviteit net de kopieertaak heeft voltooid, maar de activiteit Verwijderen niet is aangestaard, is het mogelijk dat de activiteit Verwijderen dit nieuwe binnenkomende bestand verwijdert dat NIET naar de bestemming nog niet door het verwijderen van de hele map.

## <a name="about-this-solution-template"></a>Over deze oplossingssjabloon

Met deze sjabloon worden de bestanden uit uw bronbestandsopslag opgehaald. Het verplaatst vervolgens elk van hen naar de bestemming op te slaan.

De sjabloon bevat vijf activiteiten:
- **GetMetadata** krijgt de lijst met objecten, waaronder de bestanden en submappen, uit uw map in het bronarchief. Het zal niet ophalen van de objecten recursief. 
- **Filter** de lijst met objecten uit **activiteit Metagegevens** ophalen om alleen de bestanden te selecteren. 
- **ForEach** krijgt de bestandslijst uit de **filteractiviteit** en vervolgens wordt de lijst weergegeven en geeft elk bestand door aan de activiteit Kopiëren en Verwijderen.
- **Kopieer** kopieën van één bestand van de bron naar het doelarchief.
- **Delete** verwijdert hetzelfde bestand uit het bronarchief.

De sjabloon definieert vier parameters:
- *SourceStore_Location* is het mappad van uw bronarchief waar u bestanden naartoe wilt verplaatsen. 
- *SourceStore_Directory* is het submappad van uw bronarchief waar u bestanden naartoe wilt verplaatsen.
- *DestinationStore_Location* is het mappad van uw bestemmingsarchief waar u bestanden naartoe wilt verplaatsen. 
- *DestinationStore_Directory* is het submappad van uw bestemmingsarchief waar u bestanden naartoe wilt verplaatsen.

## <a name="how-to-use-this-solution-template"></a>Deze oplossingssjabloon gebruiken

1. Ga naar de sjabloon **Bestanden verplaatsen.** Selecteer bestaande verbinding of maak een **nieuwe** verbinding met uw bronbestandsarchief waar u bestanden naartoe wilt verplaatsen. Houd er rekening mee dat **DataSource_Folder** en **DataSource_File** verwijzen naar dezelfde verbinding van uw bronbestandsarchief.

    ![Een nieuwe verbinding met de bron maken](media/solution-template-move-files/move-files1.png)

2. Selecteer bestaande verbinding of maak een **nieuwe** verbinding met uw doelarchief waar u bestanden naartoe wilt verplaatsen.

    ![Een nieuwe verbinding met de bestemming maken](media/solution-template-move-files/move-files2.png)

3. Selecteer Het tabblad **Deze sjabloon gebruiken.**
    
4. U ziet de pijplijn, zoals in het volgende voorbeeld:

    ![De pijplijn weergeven](media/solution-template-move-files/move-files4.png)

5. Selecteer **Foutopsporing,** voer de **parameters**in en selecteer **Voltooien**.   De parameters zijn het mappad waar u bestanden naartoe wilt verplaatsen en het mappad waar naar u bestanden wilt verplaatsen. 

    ![De pijplijn uitvoeren](media/solution-template-move-files/move-files5.png)

6. Bekijk het resultaat.

    ![Bekijk het resultaat](media/solution-template-move-files/move-files6.png)

## <a name="next-steps"></a>Volgende stappen

- [Nieuwe en gewijzigde bestanden kopiëren op LastModifiedDate met Azure Data Factory](solution-template-copy-new-files-lastmodifieddate.md)

- [Bestanden uit meerdere containers kopiëren met Azure Data Factory](solution-template-copy-files-multiple-containers.md)