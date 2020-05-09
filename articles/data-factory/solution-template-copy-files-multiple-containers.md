---
title: Bestanden kopiëren vanuit meerdere containers
description: Meer informatie over het gebruik van een oplossings sjabloon voor het kopiëren van bestanden uit meerdere containers met behulp van Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/1/2018
ms.openlocfilehash: 73560c49e10ab96c934d4dd3cea9395093a26420
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629045"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Bestanden van meerdere containers met Azure Data Factory kopiëren

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt een oplossings sjabloon beschreven die u kunt gebruiken om bestanden te kopiëren uit meerdere containers tussen bestands archieven. U kunt dit bijvoorbeeld gebruiken om uw data Lake te migreren van AWS S3 naar Azure Data Lake Store. Of u kunt de sjabloon gebruiken om alles te repliceren van een Azure Blob-opslag account naar een ander.

> [!NOTE]
> Als u bestanden wilt kopiëren uit één container, is het efficiënter om met het [hulp programma gegevens kopiëren](copy-data-tool.md) een pijp lijn te maken met één Kopieer activiteit. De sjabloon in dit artikel is meer dan u nodig hebt voor dat eenvoudige scenario.

## <a name="about-this-solution-template"></a>Over deze oplossings sjabloon

Met deze sjabloon worden de containers uit uw bron opslag archief opgesomd. Vervolgens worden deze containers naar het doel archief gekopieerd.

De sjabloon bevat drie activiteiten:
- **GetMetadata** scant uw opslag opslag en haalt de lijst met containers op.
- **Foreach** haalt de container lijst op uit de **GetMetadata** -activiteit en herhaalt vervolgens de lijst en geeft elke container door aan de Kopieer activiteit.
- **Copy** kopieert elke container uit het opslag archief van de bron naar het doel archief.

De sjabloon definieert de volgende para meters:
- *SourceFileFolder* is het mappad van de gegevens bron opslag, waar u een lijst van de containers kunt ophalen. Het pad is de hoofdmap die meerdere container mappen bevat. De standaard waarde van deze para meter `sourcefolder`is.
- *SourceFileDirectory* is het pad naar de submap onder de hoofdmap van uw gegevens bron archief. De standaard waarde van deze para meter `subfolder`is.
- *DestinationFileFolder* is het mappad waarnaar de bestanden worden gekopieerd in uw doel archief. De standaard waarde van deze para meter `destinationfolder`is.
- *DestinationFileDirectory* is het pad naar de submap waarnaar de bestanden worden gekopieerd in uw doel archief. De standaard waarde van deze para meter `subfolder`is.

## <a name="how-to-use-this-solution-template"></a>Deze oplossings sjabloon gebruiken

1. Ga naar het **bestand containers met meerdere bestanden kopiëren tussen** de sjabloon voor het opslaan van bestanden. Een **nieuwe** verbinding maken met uw bron opslag archief. In het bron opslag archief kunt u bestanden uit meerdere containers kopiëren.

    ![Een nieuwe verbinding maken met de bron](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Maak een **nieuwe** verbinding naar uw doel opslag archief.

    ![Een nieuwe verbinding maken met de bestemming](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Selecteer **deze sjabloon gebruiken**.

    ![Deze sjabloon gebruiken](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. U ziet de pijp lijn, zoals in het volgende voor beeld:

    ![De pijp lijn weer geven](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Selecteer **debug**, voer de **para meters**in en selecteer **volt ooien**.

    ![De pijplijn uitvoeren](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Bekijk het resultaat.

    ![Bekijk het resultaat](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Volgende stappen

- [Bulksgewijs kopiëren van een Data Base met behulp van een controle tabel met Azure Data Factory](solution-template-bulk-copy-with-control-table.md)

- [Bestanden van meerdere containers met Azure Data Factory kopiëren](solution-template-copy-files-multiple-containers.md)
