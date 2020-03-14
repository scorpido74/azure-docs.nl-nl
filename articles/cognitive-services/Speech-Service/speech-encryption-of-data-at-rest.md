---
title: Spraak service versleuteling van gegevens in rust
titleSuffix: Azure Cognitive Services
description: Spraak service versleuteling van gegevens in rust.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 3ccc9820f38a8c32d0b390663eb6b4430b42e8f0
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372357"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>Spraak service versleuteling van gegevens in rust

Met de spraak service worden uw gegevens automatisch versleuteld wanneer deze persistent worden gemaakt in de Cloud. Met versleuteling van de spraak service worden uw gegevens beveiligd en wordt u geholpen bij het voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie.

## <a name="about-cognitive-services-encryption"></a>Over Cognitive Services versleuteling

Gegevens worden versleuteld en ontsleuteld met behulp van [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) [-compatibele 256-bits AES-](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) versleuteling. Versleuteling en ontsleuteling zijn transparant, wat betekent dat versleuteling en toegang voor u worden beheerd. Uw gegevens zijn standaard beveiligd en u hoeft uw code of toepassingen niet te wijzigen om te kunnen profiteren van versleuteling.

## <a name="about-encryption-key-management"></a>Over het beheer van versleutelings sleutels

Wanneer u Custom Speech en een aangepaste spraak-service gebruikt, kan de volgende gegevens in de cloud worden opgeslagen:  

* Gegevens voor spraak tracering: alleen als u de tracering inschakelt voor uw aangepaste eind punt
* Geüploade trainings-en test gegevens

Standaard worden uw gegevens opgeslagen in de opslag van micro soft en gebruikt uw abonnement micro soft Managed Encryption Keys. U hebt ook de mogelijkheid om uw eigen opslag account voor te bereiden. Toegang tot de Store wordt beheerd door de beheerde identiteit en de spraakherkennings service heeft geen rechtstreekse toegang tot uw eigen gegevens, zoals gegevens over spraak tracering, aanpassings training en aangepaste modellen.

Zie [Wat zijn beheerde](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)identiteiten voor meer informatie over beheerde identiteiten.

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>Uw eigen opslag (BYOS) meenemen voor aanpassing en logboek registratie

Als u toegang wilt aanvragen om uw eigen opslag te maken, vult u het [BYOS-aanvraag formulier (uw eigen opslag)](https://aka.ms/cogsvc-cmk)in en verzendt u dit. Na goed keuring moet u uw eigen opslag account maken om de gegevens op te slaan die nodig zijn voor aanpassing en logboek registratie. Wanneer u een opslag account toevoegt, wordt door de speech service-bron een door het systeem toegewezen beheerde identiteit ingeschakeld. Nadat de door het systeem toegewezen beheerde identiteit is ingeschakeld, wordt deze bron geregistreerd bij Azure Active Directory (AAD). Nadat het is geregistreerd, krijgt de beheerde identiteit toegang tot het opslag account. Meer informatie over beheerde identiteiten vindt u hier. Zie [Wat zijn beheerde](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)identiteiten voor meer informatie over beheerde identiteiten.

> [!IMPORTANT]
> Als u door het systeem toegewezen beheerde identiteiten uitschakelt, wordt de toegang tot het opslag account verwijderd. Dit zorgt ervoor dat de onderdelen van de spraak service die toegang tot het opslag account vereisen, niet meer werken.  

## <a name="regional-availability"></a>Regionale beschikbaarheid

BYOS is momenteel beschikbaar in deze regio's:

* US - zuid-centraal
* VS - west 2
* VS - oost

## <a name="next-steps"></a>Volgende stappen

* [Speech service-aanvraag formulier voor uw eigen opslag (BYOS)](https://aka.ms/cogsvc-cmk)
* [Wat zijn beheerde identiteiten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
