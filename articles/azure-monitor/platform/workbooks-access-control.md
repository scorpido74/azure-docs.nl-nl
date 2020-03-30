---
title: Toegangsbeheer voor Azure Monitor-werkmappen
description: Vereenvoudig complexe rapportage met vooraf gebouwde en aangepaste geparameteriseerde werkmappen met op rollen gebaseerd toegangscontrole
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 20116ab105e4eb12875ba3cb279fb261eb5c70e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658418"
---
# <a name="access-control"></a>Toegangsbeheer

Toegangsbesturingselement in werkmappen verwijst naar twee dingen:

* Toegang vereist om gegevens in een werkmap te lezen. Deze toegang wordt beheerd door standaard [Azure-rollen](https://docs.microsoft.com/azure/role-based-access-control/overview) op de resources die in de werkmap worden gebruikt. Werkmappen geven geen toegang tot deze bronnen op of configureren deze niet. Gebruikers zouden meestal deze toegang tot deze bronnen met behulp van de [Monitoring Reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) rol op deze middelen.

* Toegang vereist om werkmappen op te slaan

    - Voor `("My")` het opslaan van privéwerkmappen zijn geen extra bevoegdheden vereist. Alle gebruikers kunnen privéwerkmappen opslaan en alleen zij kunnen die werkmappen zien.
    - Voor het opslaan van gedeelde werkmappen zijn schrijfrechten in een resourcegroep vereist om de werkmap op te slaan. Deze bevoegdheden worden meestal opgegeven door de rol [Monitorbijdrager,](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) maar kunnen ook worden ingesteld via de rol *Werkboekenbijdrager.*
    
## <a name="standard-roles-with-workbook-related-privileges"></a>Standaardrollen met werkmapgerelateerde bevoegdheden

[Monitoring Reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) bevat standaard /leesbevoegdheden die worden gebruikt door monitoringtools (inclusief werkmappen) om gegevens uit bronnen te lezen.

[Monitoring bijdrager bevat](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) algemene `/write` bevoegdheden die worden `workbooks/write` gebruikt door verschillende bewakingstools voor het opslaan van items (inclusief bevoegdheden om gedeelde werkmappen op te slaan).
'Werkmapinzender' voegt bevoegdheden voor werkboeken/schrijven toe aan een object om gedeelde werkmappen op te slaan.
Gebruikers hebben geen speciale bevoegdheden nodig om privéwerkmappen op te slaan die alleen zij kunnen zien.

Voor aangepast toegangsbeheer op basis van rollen:

Toevoegen `microsoft.insights/workbooks/write` om gedeelde werkmappen op te slaan. Zie de rol [Werkmapinzender](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Ga aan de slag met](workbooks-visualizations.md) het leren van meer over werkmappen met veel uitgebreide visualisatiesopties.
