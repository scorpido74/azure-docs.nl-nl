---
title: Spraakserviceversleuteling van gegevens in rust
titleSuffix: Azure Cognitive Services
description: Spraakserviceversleuteling van gegevens in rust.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 3ccc9820f38a8c32d0b390663eb6b4430b42e8f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372357"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>Spraakserviceversleuteling van gegevens in rust

Speech Service versleutelt uw gegevens automatisch wanneer deze worden gehandhaafd in de cloud. Spraakserviceversleuteling beschermt uw gegevens en helpt u om aan uw organisatorische beveiligings- en nalevingsverplichtingen te voldoen.

## <a name="about-cognitive-services-encryption"></a>Versleuteling van Cognitive Services

Gegevens worden versleuteld en gedecodeerd met [FIPS 140-2-compatibele](https://en.wikipedia.org/wiki/FIPS_140-2) [256-bits AES-versleuteling.](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) Versleuteling en decryptie zijn transparant, wat betekent dat encryptie en toegang voor u worden beheerd. Uw gegevens zijn standaard beveiligd en u hoeft uw code of toepassingen niet te wijzigen om te profiteren van versleuteling.

## <a name="about-encryption-key-management"></a>Over beheer van versleutelingssleutels

Wanneer u aangepaste spraak en aangepaste spraak gebruikt, kan spraakservice volgende gegevens opslaan in de cloud:  

* Spraaktraceringsgegevens - alleen als u de tracering voor uw aangepaste eindpunt inschakelt
* Geüploade trainings- en testgegevens

Standaard worden uw gegevens opgeslagen in de opslag van Microsoft en maakt uw abonnement gebruik van door Microsoft beheerde versleutelingssleutels. Je hebt ook een optie om je eigen opslagaccount voor te bereiden. Toegang tot het archief wordt beheerd door de Managed Identity en spraakservice heeft geen directe toegang tot uw eigen gegevens, zoals spraaktraceringsgegevens, aanpassingstrainingsgegevens en aangepaste modellen.

Zie [Wat zijn beheerde identiteiten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)voor meer informatie over Beheerde identiteit.

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>Breng uw eigen opslag (BYOS) voor aanpassing en logboekregistratie

Als u toegang wilt vragen om uw eigen opslag mee te nemen, vult u de Spraakservice in en verzendt u [deze in - neem uw eigen aanvraagformulier (Storage) mee.](https://aka.ms/cogsvc-cmk) Nadat u bent goedgekeurd, moet u uw eigen opslagaccount maken om de gegevens op te slaan die nodig zijn voor aanpassing en logboekregistratie. Wanneer u een opslagaccount toevoegt, schakelt de spraakservicebron een beheerde identiteit met systeem toegewezen in. Nadat de beheerde identiteit met systeemtoegewezen is ingeschakeld, wordt deze bron geregistreerd bij Azure Active Directory (AAD). Na registratie krijgt de beheerde identiteit toegang tot het opslagaccount. Hier vindt u meer informatie over Beheerde identiteiten. Zie [Wat zijn beheerde identiteiten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)voor meer informatie over Beheerde identiteit.

> [!IMPORTANT]
> Als u beheerde identiteiten met systeemtoegewezen uitschakelt, wordt de toegang tot het opslagaccount verwijderd. Hierdoor kunnen de delen van de Spraakservice die toegang tot het opslagaccount vereisen, niet meer werken.  

## <a name="regional-availability"></a>Regionale beschikbaarheid

BYOS is momenteel beschikbaar in deze regio's:

* US - zuid-centraal
* VS - west 2
* VS - oost

## <a name="next-steps"></a>Volgende stappen

* [Spraakservice - breng uw eigen aanvraagformulier voor opslag (BYOS) mee](https://aka.ms/cogsvc-cmk)
* [Wat zijn beheerde identiteiten.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)
