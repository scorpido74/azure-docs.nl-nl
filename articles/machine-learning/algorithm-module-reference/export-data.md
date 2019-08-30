---
title: 'Gegevens exporteren: Module verwijzing'
titleSuffix: Azure Machine Learning service
description: Meer informatie over het gebruik van de module gegevens exporteren in Azure Machine Learning-service voor het opslaan van resultaten, tussenliggende gegevens en werk gegevens van uw experimenten in Cloud opslag doelen buiten Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: a4fb539f4c86d27813b60964794fc1f398d3f2a4
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128765"
---
# <a name="export-data-module"></a>Gegevens module exporteren

In dit artikel wordt een module van de Visual Interface (preview) voor de Azure Machine Learning-service beschreven.

Gebruik deze module om resultaten, tussenliggende gegevens en werk gegevens van uw experimenten op te slaan in Cloud opslag buiten Azure Machine Learning.

Deze module ondersteunt het exporteren of opslaan van uw gegevens naar de volgende Cloud gegevens Services:


- **Exporteren naar Azure Blob Storage**: Hiermee worden gegevens opgeslagen in de Blob service in Azure. Gegevens in de Blob service kunnen openbaar worden gedeeld of worden opgeslagen in beveiligde gegevens opslag voor toepassingen.

  
## <a name="how-to-configure-export-data"></a>Export gegevens configureren

1. Voeg de module **gegevens exporteren** toe aan uw experiment in de-interface. U kunt deze module vinden in de categorie **invoer en uitvoer** .

2. Verbind gegevens met het **exporteren** naar de module die de gegevens bevat die u wilt exporteren.

3. Dubbel klik op **gegevens exporteren** om het deel venster **Eigenschappen** te openen.

4. Selecteer bij **gegevens bestemming**het type Cloud opslag waar u uw gegevens wilt opslaan. Als u wijzigingen aanbrengt in deze optie, worden alle andere eigenschappen opnieuw ingesteld. Zorg er eerst voor dat u deze optie selecteert.

5. Geef een account naam en verificatie methode op die nodig zijn voor toegang tot het opgegeven opslag account.

    **Exporteren naar Azure Blob Storage** is de enige optie in een persoonlijke preview. Hieronder ziet u hoe u de module kunt instellen.
    1. De Azure Blob-service is voor het opslaan van grote hoeveel heden gegevens, inclusief binaire gegevens. Er zijn twee typen Blob-opslag: open bare blobs en blobs waarvoor aanmeldings referenties zijn vereist.

    2. Kies voor **verificatie type** **openbaar (SAS)** als u weet dat de opslag toegang ondersteunt via een SAS-URL.

          Een SAS-URL is een speciaal type URL dat kan worden gegenereerd met behulp van een Azure-opslag programma. Dit is alleen beschikbaar voor een beperkte periode.  Het bevat alle informatie die nodig is voor verificatie en downloaden.

        Voor **URI**, typt of plakt u de volledige URI waarmee het account en de open bare BLOB worden gedefinieerd.

        Voor de bestands indeling CSV en TSV worden ondersteund.

    3. Voor particuliere accounts kiest u **account**en geeft u de account naam en de account sleutel op, zodat het experiment naar het opslag account kan schrijven.

         - **Accountnaam**: Typ of plak de naam van het account waar u de gegevens wilt opslaan. Als de volledige URL van het opslag account bijvoorbeeld is `http://myshared.blob.core.windows.net`, typt `myshared`u.

        - **Account sleutel**: Plak de toegangs sleutel voor opslag die is gekoppeld aan het account.

        -  **Pad naar container, map of BLOB**: Typ de naam van de BLOB waar de geëxporteerde gegevens worden opgeslagen. Als u bijvoorbeeld de resultaten van uw experiment wilt opslaan in een nieuwe blob met de naam **results01. CSV** in de container voorspellingen in een account met de naam **MYMLDATA**, is `http://mymldata.blob.core.windows.net/predictions/results01.csv`de volledige URL voor de blob.

            Daarom geeft u in het veld **pad naar container, map of BLOB**de naam van de container en de BLOB als volgt op:`predictions/results01.csv`

        - Als u de naam opgeeft van een blob die nog niet bestaat, maakt Azure de BLOB voor u.

       -  Wanneer u naar een bestaande BLOB schrijft, kunt u opgeven dat de huidige inhoud van de BLOB moet worden overschreven door de eigenschap in te stellen op de **schrijf modus voor Azure Blob Storage**. Deze eigenschap is standaard ingesteld op **fout**, wat betekent dat er een fout optreedt wanneer een bestaand blob-bestand met dezelfde naam wordt gevonden.


    4. Selecteer de indeling waarin de gegevens moeten worden opgeslagen voor de **bestands indeling voor het BLOB-bestand**.

        - **CSV**: Door komma's gescheiden waarden (CSV) zijn de standaard indeling voor opslag. Als u kolom koppen samen met de gegevens wilt exporteren, selecteert u de optie voor het schrijven van de **rij-BLOB-header**.  Zie [converteren naar CSV](./convert-to-csv.md)voor meer informatie over de door komma's gescheiden indeling die in azure machine learning wordt gebruikt.

        - **TSV**: TSV-indeling (door tabs gescheiden waarden) is compatibel met veel machine learning-hulpprogram ma's. Als u kolom koppen samen met de gegevens wilt exporteren, selecteert u de optie voor het schrijven van de **rij-BLOB-header**.  

 
    5. **In cache opgeslagen resultaten gebruiken**: Selecteer deze optie als u wilt voor komen dat de resultaten naar het blobbestand worden herschreven telkens wanneer u het experiment uitvoert. Als er geen andere wijzigingen zijn in de module parameters, schrijft het experiment de resultaten alleen de eerste keer dat de module wordt uitgevoerd of wanneer er wijzigingen in de gegevens zijn.

    6. Voer het experiment uit.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 