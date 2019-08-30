---
title: 'Importeren vanuit Azure Blob Storage: Module verwijzing'
titleSuffix: Azure Machine Learning service
description: In dit onderwerp wordt beschreven hoe u de Blob Storage module importeren uit Azure gebruikt in Azure Machine Learning-service om gegevens te lezen uit Azure Blob-opslag, zodat u de gegevens in een machine learning experiment kunt gebruiken.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: fea64070c496379351bb75f2a38aba9b4db70dcd
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128723"
---
# <a name="import-from-azure-blob-storage-module"></a>Importeren uit Azure Blob Storage-module

In dit artikel wordt een module van de Visual Interface (preview) voor de Azure Machine Learning-service beschreven.

Gebruik deze module om gegevens te lezen uit Azure Blob-opslag, zodat u de gegevens in een machine learning experiment kunt gebruiken.  

De Azure Blob-service is voor het opslaan van grote hoeveel heden gegevens, inclusief binaire gegevens. Azure-blobs zijn vanaf elke locatie toegankelijk via HTTP of HTTPS. Verificatie is mogelijk vereist, afhankelijk van het type Blob-opslag. 

- Open bare blobs zijn toegankelijk voor iedereen of door gebruikers die een SAS-URL hebben.
- Privé-blobs vereisen een aanmelding en referenties.

Voor het importeren uit Blob Storage moeten gegevens worden opgeslagen in blobs die gebruikmaken van de indeling **Block BLOB** . De bestanden die zijn opgeslagen in de BLOB moeten een door komma's gescheiden (CSV) of door tabs gescheiden (TSV) indelingen gebruiken. Wanneer u het bestand leest, worden de records en de toepasselijke kenmerk koppen als rijen in het geheugen geladen als een gegevensset.


We raden u ten zeerste aan om uw gegevens te profileren voordat u ze importeert, om ervoor te zorgen dat het schema op de verwachte manier wordt uitgevoerd. Het import proces scant een aantal Head rijen om het schema te bepalen, maar latere rijen kunnen extra kolommen bevatten of gegevens die fouten veroorzaken.



## <a name="manually-set-properties-in-the-import-data-module"></a>Eigenschappen hand matig instellen in de module gegevens importeren

In de volgende stappen wordt beschreven hoe u de import bron hand matig configureert.

1. Voeg de module **gegevens importeren** toe aan uw experiment. U kunt deze module vinden in de-interface, in de **gegevens invoer en-uitvoer**

2. Selecteer voor **gegevens bron** **Azure Blob Storage**.

3. Kies voor **verificatie type** **openbaar (SAS-URL)** als u weet dat de gegevens zijn verstrekt als een open bare gegevens bron. Een SAS-URL is een gebonden URL voor open bare toegang die u kunt genereren met behulp van een Azure-opslag programma.

    Kies anders **account**.

4. Als uw gegevens zich in een **open bare** BLOB bevinden die toegankelijk is via een SAS-URL, hebt u geen aanvullende referenties nodig omdat de URL-teken reeks alle informatie bevat die nodig is voor down loads en authenticatie.

    Typ of plak in het veld **URI** de volledige URI waarmee het account en de open bare BLOB worden gedefinieerd.



5. Als uw gegevens zich in een **privé** -account bevindt, moet u referenties opgeven, inclusief de account naam en de sleutel.

    - Typ of plak in het **account naam**de naam van het account dat de BLOB bevat waartoe u toegang wilt.

        Als de volledige URL van het opslag account bijvoorbeeld is `http://myshared.blob.core.windows.net`, typt `myshared`u.

    - Plak voor **account sleutel**de toegangs sleutel voor opslag die is gekoppeld aan het account.

        Als u de toegangs sleutel niet kent, raadpleegt u de sectie ' uw Azure Storage-accounts beheren ' in dit artikel: [Over Azure Storage-accounts](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

6. Voor **pad naar container, map of BLOB**, typt u de naam van de specifieke blob die u wilt ophalen.

    Als u bijvoorbeeld een bestand met de naam **data01. CSV** hebt geüpload naar de container **trainingdata** in een account met de naam **MYMLDATA**, zou de volledige URL voor het `http://mymldata.blob.core.windows.net/trainingdata/data01.txt`bestand zijn:.

    Daarom typt u in het veld **pad naar container, map of BLOB**:`trainingdata/data01.csv`

    Als u meerdere bestanden wilt importeren, kunt u de Joker `*` tekens (asterisk) `?` of (vraag teken) gebruiken.

    Als er bijvoorbeeld wordt aangenomen dat `trainingdata` de container meerdere bestanden bevat met een compatibele indeling, kunt u de volgende specificatie gebruiken om alle bestanden te lezen `data`die beginnen met en deze samen voegen tot één gegevensset:

    `trainingdata/data*.csv`

    U kunt geen joker tekens gebruiken in container namen. Als u bestanden uit meerdere containers wilt importeren, gebruikt u een afzonderlijk exemplaar van de module **import data** voor elke container en voegt u vervolgens de gegevens sets samen met behulp van de module [rijen toevoegen](./add-rows.md) .

    > [!NOTE]
    > Als u de optie hebt geselecteerd, **gebruikt u in cache**geplaatste resultaten. alle wijzigingen die u aanbrengt in de bestanden in de container, activeren niet dat de gegevens in het experiment worden vernieuwd.

7. Selecteer voor **BLOB-bestands indeling**een optie die de indeling aangeeft van de gegevens die zijn opgeslagen in de blob, zodat Azure machine learning de gegevens op de juiste wijze kunt verwerken. De volgende indelingen worden ondersteund:

    - **CSV**: Door komma's gescheiden waarden (CSV) zijn de standaard opslag indeling voor het exporteren en importeren van bestanden in Azure Machine Learning. Als de gegevens al een veldnamenrij bevatten, selecteert u de optie, het **bestand bevat een koprij**of de koptekst wordt beschouwd als een gegevensrij.

       

    - **TSV**: Door tabs gescheiden waarden (TSV) zijn een indeling die wordt gebruikt door veel machine learning-hulpprogram ma's. Als de gegevens al een veldnamenrij bevatten, selecteert u de optie, het **bestand bevat een koprij**of de koptekst wordt beschouwd als een gegevensrij.

       

    - **ARFF**: Deze indeling biedt ondersteuning voor het importeren van bestanden in de indeling die wordt gebruikt door de Fridge-toolset. 

   

8. Voer het experiment uit.


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 