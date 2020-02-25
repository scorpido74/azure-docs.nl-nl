---
title: Herstel van opslag account container
description: Herstel van opslag account container
services: storage
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
ms.date: 10/30/2019
ms.service: storage
manager: dcscontentpm
ms.openlocfilehash: 8b9b69fb32edab54fb2df558ea292df264e00cbb
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562272"
---
# <a name="storage-account-container-recovery"></a>Herstel van opslag account container

Azure Storage biedt gegevens tolerantie via automatische replica's. Dit voor komt echter niet dat toepassings code of gebruikers gegevens kunnen beschadigen, ongeacht of ze per ongeluk of opzettelijk zijn beschadigd. Voor het bijhouden van gegevens kwaliteit in het gezicht van de toepassing of de gebruikers fout zijn meer geavanceerde technieken vereist, zoals het kopiëren van de gegevens naar een secundaire opslag locatie met een audit logboek.

## <a name="checking-azure-storage-account-type"></a>Controleren Azure Storage account type

1. Navigeer naar [Azure Portal](https://portal.azure.com/).

2. Zoek uw opslagaccount.

3. Controleer in de sectie **overzicht** op **replicatie**.

   ![Installatiekopie](media/storage-account-container-recovery/1.png)

4. Als het replicatie type **GRS/Ra-GRS**is, is het herstellen van de account container mogelijk zonder garantie. Voor alle andere replicatie typen is het niet mogelijk.

5. Verzamel de volgende informatie en verwerk een ondersteunings aanvraag met Microsoft Ondersteuning.

   * Naam van opslag account:
   * Container naam:
   * Tijdstip van verwijdering:

   De volgende tabel bevat een overzicht van het bereik van herstel van de opslag account container, afhankelijk van de replicatie strategie.

   |Type inhoud|LRS|ZRS|GRS|RA-GRS| 
   |---|---|---|---|---|
   |Storage-Container|Nee|Nee|Ja|Ja| 

   * We kunnen proberen de container voor het opslag account te herstellen, maar zonder enige garantie. 

## <a name="things-not-to-do-for-recover-to-be-successful"></a>Alles wat u niet moet doen om te herstellen is geslaagd

* Maak de container niet opnieuw (met dezelfde naam).  
* Als u de container al opnieuw hebt gemaakt, moet u de container verwijderen voordat u een ondersteunings aanvraag voor herstel hebt ingediend.

## <a name="steps-to-prevent-this-in-the-future"></a>Stappen om dit in de toekomst te voor komen

1. Om dit in de toekomst te voor komen, kunt u het beste [verwijderen](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete).

2. We raden u ook aan de [snap shot](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob) -functie uit te voeren.
 
## <a name="next-steps"></a>Volgende stappen

Hier volgen twee voor beelden van codes voor de functie:

  * [Een BLOB-moment opname maken en beheren in .NET](https://docs.microsoft.com/azure/storage/storage-blob-snapshots)
  * [BLOB-moment opnamen gebruiken met Power shell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)
  

