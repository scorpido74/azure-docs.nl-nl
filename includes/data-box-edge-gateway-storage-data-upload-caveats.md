---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/21/2019
ms.author: alkohli
ms.openlocfilehash: 8c87e14071b3bb40421ab655c172df739570e295
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "67176490"
---
De volgende voor behoud zijn van toepassing op gegevens die worden verplaatst naar Azure.

- We raden aan dat er niet meer dan één apparaat naar dezelfde container moet schrijven.
- Als u een bestaand Azure-object (zoals een BLOB of een bestand) in de Cloud hebt met dezelfde naam als het object dat wordt gekopieerd, overschrijft het apparaat het bestand in de Cloud.
- Een lege Directory-hiërarchie (zonder bestanden) die is gemaakt onder share mappen wordt niet geüpload naar de BLOB-containers.
- U kunt de gegevens kopiëren met behulp van slepen en neerzetten met de Verkenner of via de opdracht regel. Als de totale grootte van gekopieerde bestanden groter is dan 10 GB, raden we u aan een programma voor bulksgewijs kopiëren te gebruiken, zoals Robocopy of rsync. De hulpprogram ma's voor bulksgewijs kopiëren proberen de Kopieer bewerking opnieuw uit te voeren op onregelmatige fouten en bieden extra tolerantie.
- Als de share die is gekoppeld aan de Azure storage-container blobs uploadt die niet overeenkomen met het type blobs dat is gedefinieerd voor de share op het moment dat deze wordt gemaakt, worden dergelijke blobs niet bijgewerkt. U kunt bijvoorbeeld een blok-BLOB share op het apparaat maken. Koppel de share aan een bestaande Cloud container die pagina-blobs bevat. Vernieuw die share om de bestanden te downloaden. Wijzig enkele van de vernieuwde bestanden die al zijn opgeslagen als pagina-blobs in de Cloud. Upload fouten worden weer geven.
