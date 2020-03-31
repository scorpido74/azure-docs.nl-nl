---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/15/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a0a4a10797109cb3363027e2445259d06d9aa071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76118092"
---
## <a name="about-the-user-delegation-sas"></a>Over de gebruikersdelegatie SAS

Een SAS-token voor toegang tot een container of blob kan worden beveiligd met Azure AD-referenties of een accountsleutel. Een SAS beveiligd met Azure AD-referenties wordt een gebruikersdelegatie SAS genoemd, omdat het OAuth 2.0-token dat wordt gebruikt om de SAS te ondertekenen, namens de gebruiker wordt aangevraagd.

Microsoft raadt u aan om Azure AD-referenties waar mogelijk te gebruiken als best practice voor beveiliging, in plaats van de accountsleutel te gebruiken, die gemakkelijker kan worden gecompromitteerd. Wanneer voor uw toepassingsontwerp handtekeningen voor gedeelde toegang vereist is, gebruikt u Azure AD-referenties om een SAS voor gebruikersdelegatie te maken voor superieure beveiliging. Zie [SAS voor gebruikersdelegatie maken](/rest/api/storageservices/create-user-delegation-sas)voor meer informatie over de SAS voor gebruikersdelegatie.

> [!CAUTION]
> Elke client die over een geldige SAS beschikt, heeft toegang tot gegevens in uw opslagaccount, zoals toegestaan door die SAS. Het is belangrijk om een SAS te beschermen tegen kwaadaardig of onbedoeld gebruik. Gebruik discretie bij het distribueren van een SAS en een plan voor het intrekken van een gecompromitteerde SAS.

Zie Beperkte toegang tot Azure [Storage-bronnen verlenen met behulp van gedeelde toegangshandtekeningen (SAS)](../articles/storage/common/storage-sas-overview.md)voor meer informatie over handtekeningen voor gedeelde toegang.
