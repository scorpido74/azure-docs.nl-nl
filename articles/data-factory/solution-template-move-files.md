---
title: Bestanden verplaatsen tussen opslag op basis van bestanden met behulp van Azure Data Factory
description: Meer informatie over hoe u een oplossings sjabloon gebruikt om bestanden te verplaatsen tussen opslag op basis van een bestand met behulp van Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 7/12/2019
ms.openlocfilehash: f6f83917e84a880fb86b5f592c4d51b03462753d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684160"
---
# <a name="move-files-with-azure-data-factory"></a>Bestanden verplaatsen met Azure Data Factory

In dit artikel wordt een oplossings sjabloon beschreven die u kunt gebruiken om bestanden van de ene map naar de andere te verplaatsen tussen archieven op basis van bestanden. Een van de veelvoorkomende scenario's voor het gebruik van deze sjabloon: bestanden worden doorlopend verplaatst naar een opslagmap in uw bron archief. Door een schema trigger te maken, kunt u deze bestanden regel matig van de bron naar het doel archief verplaatsen.  Op basis van de manier waarop ADF-pijp lijn ' bestanden verplaatsen ' krijgt, worden de bestanden opgehaald uit de map voor de overloop, worden ze gekopieerd naar een andere map in het doel archief en worden de bestanden vervolgens verwijderd uit de map voor het land van het bron archief.

> [!NOTE]
> Houd er rekening mee dat deze sjabloon is ontworpen om bestanden te verplaatsen in plaats van mappen te verplaatsen.  Als u de map wilt verplaatsen door de gegevensset zodanig te wijzigen dat deze alleen een mappad bevat en vervolgens met behulp van de Kopieer activiteit en activiteit verwijderen om te verwijzen naar dezelfde gegevensset die een map vertegenwoordigt, moet u zeer voorzichtig zijn. Dit komt omdat u ervoor moet zorgen dat er geen nieuwe bestanden in de map arriveren tussen het kopiëren en het verwijderen van de bewerking. Als er nieuwe bestanden in de map arriveren op het moment dat de Kopieer activiteit de Kopieer taak heeft voltooid, maar de Delete-activiteit niet is gesterd, is het mogelijk dat het nieuwe binnenkomende bestand dat niet is gekopieerd naar de destinati wordt verwijderd met de Delete-activiteit. Als u de volledige map wilt verwijderen.

## <a name="about-this-solution-template"></a>Over deze oplossings sjabloon

Met deze sjabloon worden de bestanden uit uw op bron bestand gebaseerde opslag opgehaald. Vervolgens wordt elk van deze verplaatst naar het doel archief.

De sjabloon bevat vijf activiteiten:
- **GetMetadata** haalt de lijst met objecten op, inclusief de bestanden en submappen uit uw map in de bron Store. De objecten worden niet recursief opgehaald. 
- **Filter** filter de objecten lijst van de **GetMetadata** -activiteit om alleen de bestanden te selecteren. 
- **Foreach** haalt de lijst met bestanden op uit de **filter** activiteit en herhaalt vervolgens de lijst en geeft elk bestand door aan de activiteit kopiëren en verwijderen.
- **Copy** kopieert één bestand van de bron naar het doel archief.
- Met **verwijderen** wordt hetzelfde bestand uit het bron archief verwijderd.

De sjabloon definieert twee para meters:
- *FolderPath_SourceStore* is het mappad van het bron archief waaruit u bestanden wilt verplaatsen. 
- *FolderPath_DestinationStore* is het mappad van het doel archief waarnaar u bestanden wilt verplaatsen. 

## <a name="how-to-use-this-solution-template"></a>Deze oplossings sjabloon gebruiken

1. Ga naar de sjabloon **bestanden verplaatsen** . Selecteer bestaande verbinding of maak een **nieuwe** verbinding met de bron bestands opslag waarvan u bestanden wilt verplaatsen. Houd er rekening mee dat **DataSource_Folder** en **DataSource_File** verwijzen naar dezelfde verbinding als de bron bestands opslag.

    ![Een nieuwe verbinding maken met de bron](media/solution-template-move-files/move-files1.png)

2. Selecteer bestaande verbinding of maak een **nieuwe** verbinding met uw doel bestands archief waarnaar u bestanden wilt verplaatsen.

    ![Een nieuwe verbinding maken met de bestemming](media/solution-template-move-files/move-files2.png)

3. Selecteer **deze sjabloon gebruiken**.

    ![Deze sjabloon gebruiken](media/solution-template-move-files/move-files3.png)
    
4. U ziet de pijp lijn, zoals in het volgende voor beeld:

    ![De pijp lijn weer geven](media/solution-template-move-files/move-files4.png)

5. Selecteer **debug**, voer de **para meters**in en selecteer **volt ooien**.   De para meters zijn het pad naar de map waarnaar u bestanden wilt verplaatsen en het mappad waarnaar u bestanden wilt verplaatsen. 

    ![De pijplijn uitvoeren](media/solution-template-move-files/move-files5.png)

6. Bekijk het resultaat.

    ![Bekijk het resultaat](media/solution-template-move-files/move-files6.png)

## <a name="next-steps"></a>Volgende stappen

- [Nieuwe en gewijzigde bestanden kopiëren met behulp van LastModifiedDate met Azure Data Factory](solution-template-copy-new-files-lastmodifieddate.md)

- [Bestanden van meerdere containers met Azure Data Factory kopiëren](solution-template-copy-files-multiple-containers.md)