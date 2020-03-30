---
title: Opslagaccount containerherstel
description: Opslagaccount containerherstel
services: storage
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
ms.date: 10/30/2019
ms.service: storage
manager: dcscontentpm
ms.openlocfilehash: 8b9b69fb32edab54fb2df558ea292df264e00cbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562272"
---
# <a name="storage-account-container-recovery"></a>Herstel van opslagaccountcontainer

Azure Storage biedt gegevenstolerantie via geautomatiseerde replica's. Dit voorkomt echter niet dat toepassingscode of gebruikers gegevens beschadigen, zowel per ongeluk als kwaadwillig. Het onderhouden van gegevensgetrouwheid in het licht van toepassings- of gebruikersfouten vereist meer geavanceerde technieken, zoals het kopiëren van de gegevens naar een secundaire opslaglocatie met een controlelogboek.

## <a name="checking-azure-storage-account-type"></a>Azure Storage-accounttype controleren

1. Navigeer naar de [Azure-portal](https://portal.azure.com/).

2. Zoek uw opslagaccount.

3. Controleer in de sectie **Overzicht** op **Replicatie**.

   ![Installatiekopie](media/storage-account-container-recovery/1.png)

4. Als het replicatietype **GRS/RA-GRS**is, is het herstel van accountcontainer mogelijk zonder garantie. Voor alle andere replicatietypen is dit niet mogelijk.

5. Verzamel de volgende informatie en dien een ondersteuningsverzoek in bij Microsoft Support.

   * Naam opslagaccount:
   * Containernaam:
   * Tijd van verwijdering:

   In de volgende tabel vindt u een overzicht van het bereik van Opslagaccountcontainerherstel, afhankelijk van de replicatiestrategie.

   |Type inhoud|LRS|ZRS|GRS|RA - GRS| 
   |---|---|---|---|---|
   |Opslagcontainer|Nee|Nee|Ja|Ja| 

   * We kunnen proberen om de opslag account container te herstellen, maar zonder enige garantie. 

## <a name="things-not-to-do-for-recover-to-be-successful"></a>Dingen niet te doen voor herstellen om succesvol te zijn

* Maak de container (met dezelfde naam) niet opnieuw.  
* Als u de container al opnieuw hebt gemaakt, moet u de container verwijderen voordat u een ondersteuningsverzoek voor herstel indient.

## <a name="steps-to-prevent-this-in-the-future"></a>Stappen om dit in de toekomst te voorkomen

1. Om dit in de toekomst te voorkomen, is soft [delete](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)de meest aanbevolen functie om te gebruiken.

2. We raden ook de [Snapshot-functie](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob) aan.
 
## <a name="next-steps"></a>Volgende stappen

Hier zijn twee voorbeeldcodes op de functie:

  * [Een blobmomentopname maken en beheren in .NET](https://docs.microsoft.com/azure/storage/storage-blob-snapshots)
  * [Blob-momentopnamen gebruiken met PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)
  

