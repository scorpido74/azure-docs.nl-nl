---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2d16febd4676ca7ba763eb7bc6dcecda4608ebb5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74224344"
---
#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Belangrijkste transacties (maximaal toegestane transacties in 10 seconden, per kluis per regio<sup>1):</sup>

|Type sleutel|HSM-toets<br>CREATE-toets|HSM-toets<br>Alle andere transacties|Softwaresleutel<br>CREATE-toets|Softwaresleutel<br>Alle andere transacties|
|:---|---:|---:|---:|---:|
|RSA 2.048-bits|5|1000|10|2.000|
|RSA 3.072-bits|5|250|10|500|
|RSA 4.096-bits|5|125|10|250|
|ECC P-256|5|1000|10|2.000|
|ECC P-384|5|1000|10|2.000|
|ECC P-521|5|1000|10|2.000|
|ECC SECP256K1|5|1000|10|2.000|

> [!NOTE]
> In de vorige tabel zien we dat voor RSA 2.048-bits softwaresleutels 2.000 GET-transacties per 10 seconden zijn toegestaan. Voor RSA 2.048-bits HSM-sleutels zijn 1.000 GET-transacties per 10 seconden toegestaan.
>
> De beperkingsdrempels worden gewogen en de handhaving staat op hun som. Bijvoorbeeld, zoals weergegeven in de vorige tabel, wanneer u GET-bewerkingen uitvoert op RSA HSM-toetsen, is het acht keer duurder om 4.096-bits toetsen te gebruiken in vergelijking met 2.048-bits toetsen. Dat komt omdat 1.000/125 = 8.
>
> In een bepaald interval van 10 seconden kan een Azure Key Vault-client `429` slechts *één* van de volgende bewerkingen uitvoeren voordat deze een beperking van de HTTP-statuscode tegenkomt:
> - 2.000 RSA 2.048-bits softwaresleutel GET-transacties
> - 1.000 RSA 2.048-bits HSM-key GET-transacties
> - 125 RSA 4.096-bits HSM-key GET-transacties
> - 124 RSA 4.096-bits HSM-key GET-transacties en 8 RSA 2.048-bits HSM-key GET-transacties

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Geheimen, beheerde opslagaccountsleutels en kluistransacties:
| Type Transacties | Maximale transacties toegestaan in 10 seconden, per kluis per regio<sup>1</sup> |
| --- | --- |
| Alle transacties |2.000 |

Zie [Azure Key Vault-beperkingsrichtlijnen](../articles/key-vault/key-vault-ovw-throttling.md)voor informatie over het omgaan met beperking wanneer deze limieten worden overschreden.

<sup>1</sup> Een abonnementslimiet voor alle transactietypen is vijf keer per sleutelkluislimiet. HSM-andere transacties per abonnement zijn bijvoorbeeld beperkt tot 5.000 transacties in 10 seconden per abonnement.
