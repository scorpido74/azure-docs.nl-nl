---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 8840e6649058837207c75dbdf7baa5c0b11ef387
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75466731"
---
Uw apparaat is gekoppeld aan een opslag account dat wordt gebruikt als een bestemming voor uw gegevens in Azure. De toegang tot het opslag account wordt bepaald door het abonnement en de 2 512-bits opslag toegangs sleutels die aan dat opslag account zijn gekoppeld.

Een van de sleutels wordt gebruikt voor verificatie wanneer het Data Box Edge-apparaat toegang heeft tot het opslag account. De andere sleutel wordt in de reserve ring bewaard, zodat u de sleutels periodiek kunt draaien.

Uit veiligheids overwegingen vereist veel data centers voor het draaien van sleutels. U wordt aangeraden deze aanbevolen procedures te volgen voor het draaien van sleutels:

- De sleutel van uw opslagaccount is vergelijkbaar met het hoofdwachtwoord voor uw opslagaccount. Beveilig uw account sleutel zorgvuldig. Distribueer het wacht woord niet naar andere gebruikers, stuur het niet vast of sla het op een wille keurige plaats in de tekst zonder opmaak op die voor anderen toegankelijk is.
- Genereer uw account sleutel opnieuw via de Azure Portal als u denkt dat deze kan worden aangetast. Zie [toegangs sleutels voor opslag accounts beheren](../articles/storage/common/storage-account-keys-manage.md)voor meer informatie.
- Uw Azure-beheerder moet de primaire of secundaire sleutel regel matig wijzigen of opnieuw genereren met behulp van de sectie opslag van de Azure Portal om rechtstreeks toegang te krijgen tot het opslag account.