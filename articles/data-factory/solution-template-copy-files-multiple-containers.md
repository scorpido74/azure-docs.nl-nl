---
title: Bestanden kopiëren vanuit meerdere containers
description: Meer informatie over het gebruik van een oplossingssjabloon om bestanden uit meerdere containers te kopiëren met Azure Data Factory.
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
ms.openlocfilehash: 383b70bbb02e7a200c7ec0a994f7cf11e9b9520e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414820"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Bestanden uit meerdere containers kopiëren met Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt een oplossingssjabloon beschreven waarmee u bestanden uit meerdere containers tussen bestandsopslag kopiëren. U het bijvoorbeeld gebruiken om uw gegevensmeer te migreren van AWS S3 naar Azure Data Lake Store. U de sjabloon ook gebruiken om alles te repliceren, van het ene Azure Blob-opslagaccount naar het andere.

> [!NOTE]
> Als u bestanden uit één container wilt kopiëren, is het efficiënter om het [hulpprogramma Gegevens kopiëren](copy-data-tool.md) te gebruiken om een pijplijn met één kopieeractiviteit te maken. De sjabloon in dit artikel is meer dan je nodig hebt voor dat eenvoudige scenario.

## <a name="about-this-solution-template"></a>Over deze oplossingssjabloon

Met deze sjabloon worden de containers opsomt in uw bronopslagarchief. Vervolgens kopieert het deze containers naar de bestemmingswinkel.

De sjabloon bevat drie activiteiten:
- **GetMetadata** scant uw bronopslagarchief en krijgt de containerlijst.
- **ForEach** krijgt de containerlijst van de **activiteit GetMetadata** en vervolgens wordt de lijst overen en geeft elke container door aan de activiteit Kopiëren.
- **Kopieer** kopieën van elke container van het bronopslagarchief naar het doelarchief.

De sjabloon definieert de volgende parameters:
- *SourceFileFolder* is het mappad van uw gegevensbronarchief, waar u een lijst met de containers krijgen. Het pad is de hoofdmap, die meerdere containermappen bevat. De standaardwaarde van `sourcefolder`deze parameter is .
- *SourceFileDirectory* is het submappad onder de hoofdmap van uw gegevensbronarchief. De standaardwaarde van `subfolder`deze parameter is .
- *DestinationFileFolder* is het mappad waarnaar de bestanden worden gekopieerd in uw bestemmingsarchief. De standaardwaarde van `destinationfolder`deze parameter is .
- *DestinationFileDirectory* is het submappad waarnaar de bestanden worden gekopieerd in uw bestemmingsarchief. De standaardwaarde van `subfolder`deze parameter is .

## <a name="how-to-use-this-solution-template"></a>Deze oplossingssjabloon gebruiken

1. Ga naar de sjabloon **Meerdere bestandencontainers kopiëren tussen bestandsopslag.** Maak een **nieuwe** verbinding met uw bronopslagarchief. In het opslagarchief voor bronnen u bestanden uit meerdere containers kopiëren.

    ![Een nieuwe verbinding met de bron maken](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Maak een **nieuwe** verbinding met uw bestemmingsopslag.

    ![Een nieuwe verbinding met de bestemming maken](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Selecteer **Deze sjabloon gebruiken**.

    ![Deze sjabloon gebruiken](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. U ziet de pijplijn, zoals in het volgende voorbeeld:

    ![De pijplijn weergeven](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Selecteer **Foutopsporing,** voer de **parameters**in en selecteer **Voltooien**.

    ![De pijplijn uitvoeren](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Bekijk het resultaat.

    ![Bekijk het resultaat](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Volgende stappen

- [Bulkkopie uit een database met behulp van een controletabel met Azure Data Factory](solution-template-bulk-copy-with-control-table.md)

- [Bestanden uit meerdere containers kopiëren met Azure Data Factory](solution-template-copy-files-multiple-containers.md)
