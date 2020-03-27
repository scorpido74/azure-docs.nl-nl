---
title: Gegevens op het hoogste niveau van uw Azure-abonnement exporteren
description: Hierin wordt beschreven hoe u alle Azure-abonnements-id's kunt weergeven die zijn gekoppeld aan uw account.
author: bandersmsft
ms.reviewer: matrive
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 87135b309f0406528bcada1cd906dd2f8535d1ae
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202893"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Uw abonnementsgegevens op het hoogste niveau exporteren en weergeven
Als u de set met abonnements-id's wilt weergeven die zijn gekoppeld aan uw gebruikersreferenties, [downloadt u een .json-bestand met uw abonnementsgegevens in het Azure-accountcentrum](https://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

Het gedownloade .json-bestand bevat de volgende informatie:
- E-mail: Het e-mail adres dat is gekoppeld aan uw account.
- PUID: De unieke id die is gekoppeld aan uw factureringsaccount.
- Abonnements-id’s: Een lijst met abonnementen die deel uitmaken van uw account, geïnventariseerd op abonnements-id.

### <a name="subscriptionsjson-sample"></a>voorbeeld abonnementen.json

```json
{
  "Email":"admin@contoso.com",
  "Puid":"00052xxxxxxxxxxx",
  "SubscriptionIds":[
    "38124d4d-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "7c8308f1-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "39a25f2b-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "52ec2489-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "e42384b2-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "90757cdc-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
  ]
}
```
