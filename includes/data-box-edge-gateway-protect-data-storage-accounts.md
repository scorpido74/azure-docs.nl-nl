---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 8840e6649058837207c75dbdf7baa5c0b11ef387
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75466731"
---
Uw apparaat is gekoppeld aan een opslagaccount dat wordt gebruikt als bestemming voor uw gegevens in Azure. De toegang tot het opslagaccount wordt beheerd door het abonnement en twee 512-bits opslagtoegangssleutels die zijn gekoppeld aan dat opslagaccount.

Een van de sleutels wordt gebruikt voor verificatie wanneer het Data Box Edge-apparaat toegang heeft tot het opslagaccount. De andere sleutel wordt in reserve gehouden, zodat u de toetsen periodiek draaien.

Om veiligheidsredenen vereisen veel datacenters sleutelrotatie. We raden u aan deze aanbevolen procedures voor sleutelrotatie te volgen:

- De sleutel van uw opslagaccount is vergelijkbaar met het hoofdwachtwoord voor uw opslagaccount. Bescherm uw accountsleutel zorgvuldig. Deel het wachtwoord niet uit onder andere gebruikers, codeer het niet en sla het nergens op in platte tekst die toegankelijk is voor anderen.
- Regenereert uw accountsleutel via de Azure-portal als u denkt dat deze kan worden aangetast. Zie [Toegangssleutels voor opslagaccount beheren](../articles/storage/common/storage-account-keys-manage.md)voor meer informatie .
- Uw Azure-beheerder moet de primaire of secundaire sleutel periodiek wijzigen of regenereren met behulp van de sectie Opslag van de Azure-portal om rechtstreeks toegang te krijgen tot het opslagaccount.