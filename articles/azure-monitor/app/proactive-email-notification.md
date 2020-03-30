---
title: Wijziging van de meldingsmelding voor slimme detectie - Azure-toepassingsinzichten
description: Wijzigen in de standaardontvangers van meldingen van Slimme detectie. Met Smart Detection u toepassingssporen controleren met Azure Application Insights voor ongebruikelijke patronen in trace telemetrie.
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 03/13/2019
ms.reviewer: mbullwin
ms.openlocfilehash: f73e5bbdd8585b3367e529a8fa00630042e56cac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671780"
---
# <a name="smart-detection-e-mail-notification-change"></a>Wijziging van de e-mailmelding voor slimme detectie

Op basis van feedback van klanten wijzigen we op 1 april 2019 de standaardrollen die e-mailmeldingen van Slimme detectie ontvangen.

## <a name="what-is-changing"></a>Wat verandert er?

Momenteel worden e-mailmeldingen voor slimme detectie standaard verzonden naar de rollen _Abonnement-eigenaar,_ _abonnementsbijdrager_en _abonnementslezer._ Deze rollen omvatten vaak gebruikers die niet actief betrokken zijn bij het toezicht, waardoor veel van deze gebruikers onnodig meldingen ontvangen. Om deze ervaring te verbeteren, maken we een wijziging, zodat e-mailmeldingen alleen standaard naar de rollen [Monitoring Reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) en [Monitoring Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) gaan.

## <a name="scope-of-this-change"></a>Omvang van deze wijziging

Deze wijziging heeft gevolgen voor alle regels voor slimme detectie, met uitzondering van de volgende regels:

* Slimme detectieregels gemarkeerd als voorbeeld. Deze Smart Detection-regels bieden geen ondersteuning voor e-mailmeldingen.

* Fout afwijkingen regel. Deze regel richt zich op de nieuwe standaardrollen zodra deze is gemigreerd van een klassieke waarschuwing naar het unified alerts platform (meer informatie is [hier](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement)beschikbaar .)

## <a name="how-to-prepare-for-this-change"></a>Hoe voor te bereiden op deze verandering?

Om ervoor te zorgen dat e-mailmeldingen van Slimme detectie naar relevante gebruikers worden verzonden, moeten deze gebruikers worden toegewezen aan de rollen [Monitoring Reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) of [Monitoring Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) van het abonnement.

Als u gebruikers wilt toewijzen aan de rollen MonitoringReader of Monitoring Contributor via de Azure-portal, voert u de stappen uit die zijn beschreven in het artikel [Een roltoewijzing toevoegen.](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment) Selecteer de _monitorlezer_ of _monitorbijdrager_ als de rol waaraan gebruikers zijn toegewezen.

> [!NOTE]
> Specifieke ontvangers van smartdetectiemeldingen, geconfigureerd met de optie _Extra e-mailontvangers_ in de regelinstellingen, worden niet beïnvloed door deze wijziging. Deze ontvangers blijven de e-mailmeldingen ontvangen.

Mocht u vragen of bedenkingen hebben over deze wijziging, aarzel dan niet om contact met ons op te [nemen.](mailto:smart-alert-feedback@microsoft.com)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over slimme detectie:

- [Foutafwijkingen](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Geheugenlekken](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Prestatieafwijkingen](../../azure-monitor/app/proactive-performance-diagnostics.md)
