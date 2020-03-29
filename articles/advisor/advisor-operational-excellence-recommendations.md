---
title: Operationele excellentie voor uw Azure-abonnementen verbeteren met Azure Advisor
description: Gebruik Advisor om te optimaliseren en volwassen te worden in operationele uitmuntendheid voor uw Azure-abonnementen
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: f34284ba62bd5dea98345ebe73365b332d38ee78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443074"
---
# <a name="achieve-operational-excellence-with-azure-advisor"></a>Operationele uitmuntendheid bereiken met Azure Advisor

Aanbevelingen voor operationele uitmuntendheid van Azure Advisor helpen klanten met proces- en workflow-efficiëntie, beheerbaarheid van resources en best practices voor implementatie. U deze aanbevelingen krijgen van Advisor op het tabblad **Operational Excellence** van het Dashboard Adviseur.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Azure Service Health-waarschuwingen maken om te worden gewaarschuwd wanneer Azure-problemen u beïnvloeden

We raden u aan Azure Service Health-waarschuwingen in te stellen om op de hoogte te worden gesteld wanneer azure-serviceproblemen van invloed zijn op u. [Azure Service Health](https://azure.microsoft.com/features/service-health/) is een gratis service die persoonlijke richtlijnen en ondersteuning biedt wanneer u wordt beïnvloed door een Azure-serviceprobleem. Advisor identificeert abonnementen die geen waarschuwingen hebben geconfigureerd en raadt aan er een te maken.

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Ontwerp uw opslagaccounts om te voorkomen dat u de maximale abonnementslimiet bereikt

Een Azure-regio kan maximaal 250 opslagaccounts per abonnement ondersteunen. Zodra de limiet is bereikt, u geen opslagaccounts meer maken in die regio/abonnementscombinatie. Advisor controleert uw abonnementen en oppervlakteaanbevelingen voor u om te ontwerpen voor minder opslagaccounts voor een account die dicht bij het bereiken van de maximale limiet ligt.

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Zorg ervoor dat u toegang hebt tot Azure-cloudexperts wanneer u dat nodig hebt

Bij het uitvoeren van een bedrijfskritieke workload is het belangrijk om toegang te hebben tot technische ondersteuning wanneer dat nodig is. Advisor identificeert potentiële bedrijfskritieke abonnementen die geen technische ondersteuning in hun ondersteuningsplan bevatten en beveelt een upgrade aan naar een optie die technische ondersteuning bevat.

## <a name="repair-invalid-log-alert-rules"></a>Ongeldige logboekwaarschuwingsregels herstellen

Azure Advisor detecteert waarschuwingsregels met ongeldige query's die zijn opgegeven in de sectie voorwaarde. De logboekwaarschuwingsregels worden gemaakt in Azure Monitor en worden gebruikt om analytische query's met bepaalde intervallen uit te voeren. De resultaten van de query bepalen of er een waarschuwing moet worden gegeven. Analytics-query’s kunnen ongeldig worden als gevolg van wijzigingen in bronnen, tabellen of opdrachten waarnaar wordt verwezen. Advisor raadt u aan de query in de waarschuwingsregel te corrigeren om te voorkomen dat deze automatisch wordt uitgeschakeld en ervoor te zorgen dat de dekking van uw resources in Azure wordt bewaakt. [Meer informatie over het oplossen van probleemoplossingsregels](https://aka.ms/aa_logalerts_queryrepair)

## <a name="follow-best-practices-using-azure-policy"></a>Aanbevolen procedures volgen met Azure-beleid

Azure Policy is een service in Azure die u gebruikt om beleidsregels te maken, toe te wijzen en te beheren. Met deze beleidsregels worden verschillende regels en effecten voor uw resources afgedwongen. Hieronder vindt u de aanbevelingen voor Azure-beleid om u te helpen operationele excellentie te bereiken: 
1. Tags beheren met Azure Policy: dit beleid voegt de opgegeven tag en waarde toe of vervangt deze wanneer een resource wordt gemaakt of bijgewerkt. Bestaande resources kunnen worden hersteld door een hersteltaak te activeren. Ook worden tags in resourcegroepen niet gewijzigd.
2. Vereisten voor geocompliance afdwingen met Azure Policy: met het beleid u de locaties beperken die uw organisatie kan opgeven bij het implementeren van resources. 
3. Geef toegestane SKU's voor virtuele machines op voor implementaties: met dit beleid u een set SKU's voor virtuele machines opgeven die uw organisatie kan implementeren.
4. 'ControleVM's die geen beheerde schijven gebruiken afdwingen' afdwingen met Azure-beleid
5. Gebruik 'Een tag overnemen van resourcegroepen' met Azure-beleid: het beleid voegt de opgegeven tag en waarde toe aan de bovenliggende resourcegroep wanneer een resource wordt gemaakt of bijgewerkt. Bestaande resources kunnen worden hersteld door een hersteltaak te activeren.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over aanbevelingen van Advisor:
* [Inleiding tot Adviseur](advisor-overview.md)
* [Slag](advisor-get-started.md)
* [Aanbevelingen voor kosten van adviseur](advisor-cost-recommendations.md)
* [Aanbevelingen voor prestaties van adviseur](advisor-performance-recommendations.md)
* [Aanbevelingen voor hoge beschikbaarheid](advisor-high-availability-recommendations.md)
* [Aanbevelingen voor beveiliging van adviseur](advisor-security-recommendations.md)
