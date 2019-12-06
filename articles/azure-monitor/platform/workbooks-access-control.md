---
title: Toegangs beheer voor werkmappen Azure Monitor
description: Vereenvoudig complexe rapportage met vooraf ontwikkelde en aangepaste werkmappen met op rollen gebaseerd toegangs beheer
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: c9b9f9ca2a9c08c05a3ce32230a39ca79625cd72
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872924"
---
# <a name="access-control"></a>Toegangsbeheer

Toegangs beheer in werkmappen verwijst naar twee dingen:

* De toegang is vereist om gegevens in een werkmap te lezen. Deze toegang wordt bepaald door de standaard [functies van Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) voor de resources die worden gebruikt in de werkmap. Werkmappen opgeven of configureren geen toegang tot deze resources. Gebruikers krijgen deze toegang gewoonlijk tot deze resources met behulp van de rol [controle lezer](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) op deze resources.

* Toegang vereist om werkmappen op te slaan

    - Het opslaan van persoonlijke `("My")` werkmappen vereist geen aanvullende bevoegdheden. Alle gebruikers kunnen persoonlijke werkmappen opslaan en alleen deze werkmappen worden weer geven.
    - Het opslaan van gedeelde werkmappen vereist schrijf bevoegdheden in een resource groep om de werkmap op te slaan. Deze bevoegdheden worden meestal opgegeven door de rol [bewaking Inzender](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) , maar kunnen ook worden ingesteld via de rol *werkmappen Inzender* .
    
## <a name="standard-roles-with-workbook-related-privileges"></a>Standaard rollen met machtigingen die betrekking hebben op de werkmap

[Bewakings lezer](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) bevat standaard/Read bevoegdheden die door controle hulpprogramma's (inclusief werkmappen) worden gebruikt om gegevens van resources te lezen.

[Bewakings bijdrager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) bevat algemene `/write` bevoegdheden die worden gebruikt door verschillende controle hulpprogramma's voor het opslaan van items (inclusief `workbooks/write` privilege voor het opslaan van gedeelde werkmappen).
"Werkmappen Inzender" voegt "werkmappen/schrijf bevoegdheden" toe aan een object om gedeelde werkmappen op te slaan.
Er zijn geen speciale bevoegdheden vereist voor gebruikers om persoonlijke werkmappen op te slaan die alleen kunnen worden weer geven.

Voor aangepast toegangs beheer op basis van rollen:

Voeg `microsoft.insights/workbooks/write` toe om gedeelde werkmappen op te slaan. Zie de rol [werkmap Inzender](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Ga](workbooks-visualizations.md) voor meer informatie over werkmappen veel uitgebreide visualisaties opties.
