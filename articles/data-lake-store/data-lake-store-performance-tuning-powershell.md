---
title: Azure Data Lake Storage Gen1-prestatieafstemming - PowerShell
description: Tips voor het verbeteren van de prestaties bij het gebruik van Azure PowerShell met Azure Data Lake Storage Gen1.
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: stewu
ms.openlocfilehash: c975af1799d427651b76bb9fde5ff765afed3f86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904565"
---
# <a name="performance-tuning-guidance-for-using-powershell-with-azure-data-lake-storage-gen1"></a>Richtlijnen voor prestatieafstemming voor het gebruik van PowerShell met Azure Data Lake Storage Gen1

In dit artikel worden de eigenschappen beschreven die u afstemmen om betere prestaties te krijgen terwijl u PowerShell gebruikt om te werken met Data Lake Storage Gen1.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="performance-related-properties"></a>Prestatiegerelateerde eigenschappen

| Eigenschap            | Standaard | Beschrijving |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Met deze parameter kunt u het aantal parallelle threads voor het uploaden of downloaden van elk bestand kiezen. Dit getal vertegenwoordigt de maximale threads die per bestand kunnen worden toegewezen, maar u minder threads krijgen, afhankelijk van uw scenario (bijvoorbeeld als u een bestand van 1 KB uploadt, krijgt u één thread, zelfs als u om 20 threads vraagt).  |
| ConcurrentFileCount | 10      | Deze parameter is specifiek bedoeld voor het uploaden en downloaden van mappen. De parameter bepaalt het aantal bestanden dat tegelijk kan worden geüpload of gedownload. Dit aantal vertegenwoordigt het maximum aantal gelijktijdige bestanden dat in één keer kan worden geüpload of gedownload, maar u minder gelijktijdigheid krijgen, afhankelijk van uw scenario (bijvoorbeeld als u twee bestanden uploadt, krijgt u twee gelijktijdige bestanden uploads, zelfs als u het vraagt voor 15). |

**Voorbeeld:**

Deze opdracht downloadt bestanden van Data Lake Storage Gen1 naar het lokale station van de gebruiker met 20 threads per bestand en 100 gelijktijdige bestanden.

```PowerShell
Export-AzDataLakeStoreItem -AccountName "Data Lake Storage Gen1 account name" `
    -PerFileThreadCount 20 `
    -ConcurrentFileCount 100 `
    -Path /Powershell/100GB `
    -Destination C:\Performance\ `
    -Force `
    -Recurse
```

## <a name="how-to-determine-property-values"></a>Hoe waardevan eigenschap te bepalen

De volgende vraag die u zou kunnen hebben is hoe te bepalen welke waarde te bieden voor de prestaties-gerelateerde eigenschappen. Hier volgen een aantal richtlijnen.

* **Stap 1: Bepaal het totale aantal draaden** - Begin met het berekenen van het totale aantal te gebruiken draad. Als algemene richtlijn moet u zes threads gebruiken voor elke fysieke kern.

    `Total thread count = total physical cores * 6`

    **Voorbeeld:**

    We nemen hier aan dat u de PowerShell-opdrachten uitvoert vanaf een D14-VM met 16 kernen

    `Total thread count = 16 cores * 6 = 96 threads`

* **Stap 2: Bereken PerFileThreadCount** - We berekenen onze PerFileThreadCount op basis van de grootte van de bestanden. Voor bestanden kleiner dan 2,5 GB is het niet nodig om deze parameter te wijzigen omdat de standaardwaarde van 10 voldoende is. Voor bestanden groter dan 2,5 GB moet u 10 threads gebruiken als basis voor de eerste 2,5 GB en 1 thread toevoegen voor elke extra toename van 256 MB in bestandsgrootte. Als u een map kopieert met bestanden van zeer verschillende groottes, kunt u overwegen ze op vergelijkbare grootte te sorteren. Grote verschillen in bestandsgroottes kunnen tot slechtere prestaties leiden. Als het niet mogelijk is om bestanden op grootte te sorteren, dient u de PerFileThreadCount in te stellen op basis van het grootste bestand.

    `PerFileThreadCount = 10 threads for the first 2.5 GB + 1 thread for each additional 256 MB increase in file size`

    **Voorbeeld:**

    Ervan uitgaande dat u 100 bestanden hebt, variërend van 1 GB tot 10 GB, gebruiken we de 10 GB als de grootste bestandsgrootte voor vergelijking, die als volgt zou lezen.

    `PerFileThreadCount = 10 + ((10 GB - 2.5 GB) / 256 MB) = 40 threads`

* **Stap 3: Gelijktijdigaantal berekenen** - Gebruik het totale aantal threaden en PerFileThreadCount om GelijktijdigFileCount te berekenen op basis van de volgende vergelijking:

    `Total thread count = PerFileThreadCount * ConcurrentFileCount`

    **Voorbeeld:**

    We gaan uit van de gebruikte voorbeeldwaarden

    `96 = 40 * ConcurrentFileCount`

    De **ConcurrentFileCount** is **2,4**, wat we kunnen afronden naar **2**.

## <a name="further-tuning"></a>Verder afstemmen

Mogelijk is verder afstemmen nodig omdat u werkt met bestanden van uiteenlopende groottes. De voorgaande berekening werkt goed als alle of de meeste bestanden groter en dichter bij het bereik van 10 GB zijn. Als het echter bestanden van zeer uiteenlopende groottes betreft, waarbij veel bestanden kleiner zijn, kunt u de PerFileThreadCount verlagen. Als we de PerFileThreadCount verlagen, kunnen we de ConcurrentFileCount verhogen. Dus, als we aannemen dat de meeste van onze bestanden kleiner zijn in het bereik van 5 GB, kunnen we onze berekening opnieuw uitvoeren:

`PerFileThreadCount = 10 + ((5 GB - 2.5 GB) / 256 MB) = 20`

Dus, **ConcurrentFileCount** wordt 96/20, dat is 4,8, afgerond op **4**.

U kunt deze instellingen blijven aanpassen door de **PerFileThreadCount** te verhogen of te verlagen, afhankelijk van de verdeling van de bestandsgroottes.

### <a name="limitation"></a>Beperking

* **Het aantal bestanden is lager dan de ConcurrentFileCount**: als het aantal bestanden dat u uploadt lager is dan de **ConcurrentFileCount** die u hebt berekend, dient u de **ConcurrentFileCount** te verlagen tot deze gelijk is met het aantal bestanden. U kunt eventuele overblijvende threads gebruiken om de **PerFileThreadCount** te verhogen.

* **Te veel threads**: als u het aantal threads te veel verhoogt zonder dat u de grootte van uw cluster verhoogt, kan dit tot slechtere prestaties leiden. Er treden mogelijk conflicten op wanneer u van context wisselt op de CPU.

* **Onvoldoende gelijktijdigheid**: als de gelijktijdigheid onvoldoende is, is uw cluster mogelijk te klein. U het aantal knooppunten in uw cluster verhogen, waardoor u meer gelijktijdigheid krijgt.

* **Beperkingsfouten**: er treden mogelijk beperkingsfouten op als uw gelijktijdigheid te hoog is. Als er beperkingsfouten optreden, dient u uw gelijktijdigheid te verkleinen of contact met ons op te nemen.

## <a name="next-steps"></a>Volgende stappen

* [Azure Data Lake Storage Gen1 gebruiken voor vereisten voor big data](data-lake-store-data-scenarios.md) 
* [Gegevens beveiligen in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Azure Data Lake Analytics gebruiken met Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight gebruiken met Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

