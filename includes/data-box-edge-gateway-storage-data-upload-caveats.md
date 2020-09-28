---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 09/25/2020
ms.author: alkohli
ms.openlocfilehash: 6dc201af2271909de15af9bac1a2e2bb68faed1a
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91401022"
---
De volgende voor behoud zijn van toepassing op gegevens die worden verplaatst naar Azure.

- We raden aan dat er niet meer dan één apparaat naar dezelfde container moet schrijven.
- Als u een bestaand Azure-object (zoals een BLOB of een bestand) in de Cloud hebt met dezelfde naam als het object dat wordt gekopieerd, overschrijft het apparaat het bestand in de Cloud.
- Een lege Directory-hiërarchie (zonder bestanden) die is gemaakt onder share mappen wordt niet geüpload naar de BLOB-containers.
- U kunt de gegevens kopiëren met behulp van slepen en neerzetten met de Verkenner of via de opdracht regel. Als de totale grootte van gekopieerde bestanden groter is dan 10 GB, raden we u aan een programma voor bulksgewijs kopiëren te gebruiken, zoals Robocopy of rsync. De hulpprogram ma's voor bulksgewijs kopiëren proberen de Kopieer bewerking opnieuw uit te voeren op onregelmatige fouten en bieden extra tolerantie.
- Als de share die is gekoppeld aan de Azure storage-container blobs uploadt die niet overeenkomen met het type blobs dat is gedefinieerd voor de share op het moment dat deze wordt gemaakt, worden dergelijke blobs niet bijgewerkt. U kunt bijvoorbeeld een blok-BLOB share op het apparaat maken. Koppel de share aan een bestaande Cloud container die pagina-blobs bevat. Vernieuw die share om de bestanden te downloaden. Wijzig enkele van de vernieuwde bestanden die al zijn opgeslagen als pagina-blobs in de Cloud. Upload fouten worden weer geven.
- Nadat een bestand in de shares is gemaakt, kan de naam van het bestand niet meer worden gewijzigd.
- Als een bestand uit een share wordt verwijderd, wordt de vermelding in het opslagaccount niet verwijderd.
- Als rsync wordt gebruikt om gegevens te kopiëren, `rsync -a` wordt de optie niet ondersteund.

