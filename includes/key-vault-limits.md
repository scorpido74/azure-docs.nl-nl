---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 04/21/2020
ms.author: jroth
ms.openlocfilehash: 065365ec2dc429013732725ccb22f73c519b6c0e
ms.sourcegitcommit: 3988965cc52a30fc5fed0794a89db15212ab23d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/22/2020
ms.locfileid: "85200188"
---
### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Belangrijkste trans acties (Maxi maal 10 seconden toegestane trans acties per kluis per regio<sup>1</sup>):

|Type sleutel|HSM-sleutel<br>Sleutel maken|HSM-sleutel<br>Alle andere trans acties|Software sleutel<br>Sleutel maken|Software sleutel<br>Alle andere trans acties|
|:---|---:|---:|---:|---:|
|RSA 2.048-bits|5|1000|10|2.000|
|RSA 3.072-bits|5|250|10|500|
|RSA 4.096-bits|5|125|10|250|
|ECC P-256|5|1000|10|2.000|
|ECC P-384|5|1000|10|2.000|
|ECC P-521|5|1000|10|2.000|
|ECC-SECP256K1|5|1000|10|2.000|

> [!NOTE]
> In de vorige tabel zien we dat voor RSA 2.048-bits software sleutels, 2.000 GET trans acties per 10 seconden zijn toegestaan. Voor RSA 2.048-bits HSM-sleutels, 1.000 GET trans acties per 10 seconden zijn toegestaan.
>
> De drempel waarden voor beperking worden gewogen en afdwinging ligt op de som. Wanneer u bijvoorbeeld in de vorige tabel GET-bewerkingen uitvoert op RSA HSM-sleutels, is het acht maal duurder voor het gebruik van 4.096-bits sleutels vergeleken met 2.048-bits sleutels. Dat komt doordat 1000/125 = 8.
>
> In een gegeven interval van 10 seconden kan een Azure Key Vault-client *slechts een* van de volgende bewerkingen uitvoeren voordat er een http- `429` status code voor beperking wordt aangetroffen:
> - 2.000 RSA 2.048-bits software sleutel GET trans acties
> - 1.000 RSA 2.048-bits HSM-sleutel GET-trans acties
> - 125 RSA 4.096-bits HSM-sleutel GET-trans acties
> - 124 RSA 4.096-bits HSM-Key GET-trans acties en 8 RSA 2.048-bits HSM-sleutel GET-trans acties

### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Geheimen, sleutels voor beheerde opslag accounts en kluis transacties:

| Transactie type | Maxi maal aantal toegestane trans acties in 10 seconden per kluis per regio<sup>1</sup> |
| --- | --- |
| Alle trans acties |2.000 |

Zie [Azure Key Vault Throttle-instructies](../articles/key-vault/key-vault-ovw-throttling.md)voor meer informatie over het afhandelen van beperking wanneer deze limieten worden overschreden.

<sup>1</sup> een limiet voor het hele abonnement voor alle transactie typen is vijf keer per sleutel kluis limiet. HSM-andere trans acties per abonnement zijn bijvoorbeeld beperkt tot 5.000 trans acties in tien seconden per abonnement.

### <a name="azure-private-link-integration"></a>Integratie van persoonlijke Azure-koppelingen

> [!NOTE]
> Het aantal sleutelkluizen met ingeschakelde privé-eindpunten per abonnement is een aanpasbare limiet. De limiet die hieronder wordt weergegeven, is de standaardlimiet. Als u een hogere limiet wilt aanvragen voor uw service, kunt u een e-mail sturen naar akv-privatelink@microsoft.com. We zullen deze aanvragen per geval goedkeuren.

| Resource | Limiet |
| -------- | ----- |
| Persoonlijke eind punten per sleutel kluis | 64 |
| Sleutel kluizen met persoonlijke eind punten per abonnement | 400 |
