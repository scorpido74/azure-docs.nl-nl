---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 0b9d87fd7929607da8407ae5bbfb2f6dd6d69dab
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "67176440"
---
#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Belangrijkste trans acties (Maxi maal 10 seconden toegestane trans acties per kluis per regio<sup>1</sup>):

|Sleuteltype|HSM-sleutel<br>Sleutel maken|HSM-sleutel<br>Alle andere trans acties|Softwaresleutel<br>Sleutel maken|Softwaresleutel<br>Alle andere trans acties|
|:---|---:|---:|---:|---:|
|RSA 2.048-bits|5|1000|10|2,000|
|RSA 3.072-bits|5|250|10|500|
|RSA 4.096-bits|5|125|10|250|
|ECC P-256|5|1000|10|2,000|
|ECC P-384|5|1000|10|2,000|
|ECC P-521|5|1000|10|2,000|
|ECC-SECP256K1|5|1000|10|2,000|

> [!NOTE]
> In de vorige tabel zien we dat voor RSA 2.048-bits software sleutels, 2.000 GET trans acties per 10 seconden zijn toegestaan. Voor RSA 2.048-bits HSM-sleutels, 1.000 GET trans acties per 10 seconden zijn toegestaan.
>
> De drempel waarden voor beperking worden gewogen en afdwinging ligt op de som. Wanneer u bijvoorbeeld in de vorige tabel GET-bewerkingen uitvoert op RSA HSM-sleutels, is het acht maal duurder voor het gebruik van 4.096-bits sleutels vergeleken met 2.048-bits sleutels. Dat komt doordat 1000/125 = 8.
>
> In een gegeven interval van 10 seconden kan een Azure Key Vault-client *slechts een* van de volgende bewerkingen uitvoeren voordat er een `429` HTTP-status code voor beperking wordt aangetroffen:
> - 2\.000 RSA 2.048-bits software sleutel GET trans acties
> - 1\.000 RSA 2.048-bits HSM-sleutel GET-trans acties
> - 125 RSA 4.096-bits HSM-sleutel GET-trans acties
> - 124 RSA 4.096-bits HSM-Key GET-trans acties en 8 RSA 2.048-bits HSM-sleutel GET-trans acties

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Geheimen, sleutels voor beheerde opslag accounts en kluis transacties:
| Transactie type | Maxi maal aantal toegestane trans acties in 10 seconden per kluis per regio<sup>1</sup> |
| --- | --- |
| Alle transacties |2,000 |

Zie [Azure Key Vault Throttle-instructies](../articles/key-vault/key-vault-ovw-throttling.md)voor meer informatie over het afhandelen van beperking wanneer deze limieten worden overschreden.

<sup>1</sup> een limiet voor het hele abonnement voor alle transactie typen is vijf keer per sleutel kluis limiet. HSM-andere trans acties per abonnement zijn bijvoorbeeld beperkt tot 5.000 trans acties in tien seconden per abonnement.
