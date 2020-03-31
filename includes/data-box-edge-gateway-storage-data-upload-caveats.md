---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/21/2019
ms.author: alkohli
ms.openlocfilehash: 8c87e14071b3bb40421ab655c172df739570e295
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176490"
---
Volgende kanttekeningen zijn van toepassing op gegevens die worden verplaatst naar Azure.

- We raden aan dat meer dan één apparaat niet naar dezelfde container moet schrijven.
- Als u een bestaand Azure-object (zoals een blob of een bestand) in de cloud hebt met dezelfde naam als het object dat wordt gekopieerd, overschrijft het apparaat het bestand in de cloud.
- Een lege maphiërarchie (zonder bestanden) die onder sharemappen is gemaakt, wordt niet geüpload naar de blobcontainers.
- U de gegevens kopiëren met slepen en neerzetten met Verkenner of via de opdrachtregel. Als de totale grootte van bestanden die worden gekopieerd groter is dan 10 GB, raden we u aan een bulkkopieerprogramma te gebruiken, zoals Robocopy of rsync. De bulkkopieergereedschappen proberen de kopieerbewerking opnieuw uit op intermitterende fouten en bieden extra tolerantie.
- Als het aandeel dat is gekoppeld aan de Azure-opslagcontainer blobs uploadt die niet overeenkomen met het type blobs dat is gedefinieerd voor het aandeel op het moment van maken, worden dergelijke blobs niet bijgewerkt. U maakt bijvoorbeeld een blokblobaandeel op het apparaat. Koppel het aandeel aan een bestaande cloudcontainer met paginablobs. Vernieuw dat aandeel om de bestanden te downloaden. Wijzig enkele van de vernieuwde bestanden die al zijn opgeslagen als paginablobs in de cloud. U ziet uploadfouten.
