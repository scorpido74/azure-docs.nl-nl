---
title: Verbeter de operationele Excellency voor uw Azure-abonnementen met Azure Advisor
description: Gebruik Advisor om te optimaliseren en verouderd te zijn in operationele uitmuntendheid voor uw Azure-abonnementen
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: f34284ba62bd5dea98345ebe73365b332d38ee78
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443074"
---
# <a name="achieve-operational-excellence-with-azure-advisor"></a>Behaal de operationele uitmuntendheid met Azure Advisor

Azure Advisor aanbevelingen voor de operationele uitmuntendheid van klanten met proces-en werk stroom efficiëntie, beheer baarheid en aanbevolen procedures voor de implementatie. U kunt deze aanbevelingen van Advisor ophalen op het tabblad **operationele uitmuntendheid** van het Advisor-dash board.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Azure Service Health waarschuwingen maken om een melding te ontvangen wanneer Azure-problemen van invloed zijn op u

We raden u aan Azure Service Health waarschuwingen in te stellen om te worden gewaarschuwd wanneer de Azure-Service problemen van invloed zijn op u. [Azure service Health](https://azure.microsoft.com/features/service-health/) is een gratis service die persoonlijke begeleiding en ondersteuning biedt wanneer u een Azure-service probleem ondervindt. Advisor identificeert abonnementen waarvoor geen waarschuwingen zijn geconfigureerd en raadt u aan om er een te maken.

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Uw opslag accounts ontwerpen om te voor komen dat de maximale abonnements limiet wordt bereikt

Een Azure-regio kan Maxi maal 250 opslag accounts per abonnement ondersteunen. Zodra de limiet is bereikt, kunt u geen opslag accounts meer maken in deze combi natie van regio en abonnement. Advisor controleert uw abonnementen en aanbevelingen voor het Opper vlak zodat u kunt ontwerpen voor minder opslag accounts voor elk wat bijna de maximale limiet bereikt.

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Zorg ervoor dat u toegang hebt tot Azure-Cloud experts wanneer u deze nodig hebt

Wanneer u een kritieke werk belasting uitvoert, is het belang rijk dat u toegang hebt tot technische ondersteuning wanneer dat nodig is. Advisor identificeert potentiële bedrijfs kritieke abonnementen die geen technische ondersteuning hebben die zijn opgenomen in hun ondersteunings plan en waarmee wordt geadviseerd om een upgrade uit te voeren naar een optie met technische ondersteuning.

## <a name="repair-invalid-log-alert-rules"></a>Ongeldige regels voor logboek waarschuwingen herstellen

Met Azure Advisor worden waarschuwings regels gedetecteerd waarvoor ongeldige query's zijn opgegeven in de voor waarde-sectie. De logboekwaarschuwingsregels worden gemaakt in Azure Monitor en worden gebruikt om analytische query's met bepaalde intervallen uit te voeren. De resultaten van de query bepalen of er een waarschuwing moet worden gegeven. Analytics-query’s kunnen ongeldig worden als gevolg van wijzigingen in bronnen, tabellen of opdrachten waarnaar wordt verwezen. Advisor wordt aangeraden de query in de waarschuwings regel te corrigeren om te voor komen dat deze automatisch wordt uitgeschakeld en de bewakings dekking van uw resources in azure te controleren. [Meer informatie over het oplossen van waarschuwings regels](https://aka.ms/aa_logalerts_queryrepair)

## <a name="follow-best-practices-using-azure-policy"></a>Volg de aanbevolen procedures voor het gebruik van Azure Policy

Azure Policy is een service in Azure die u gebruikt om beleidsregels te maken, toe te wijzen en te beheren. Met deze beleidsregels worden verschillende regels en effecten voor uw resources afgedwongen. Hieronder vindt u de aanbevelingen voor Azure-beleid om u te helpen bij het bezorgen van operationele Excellency: 
1. Tags beheren met Azure Policy: dit beleid voegt de opgegeven tag en waarde toe of vervangt deze wanneer een resource wordt gemaakt of bijgewerkt. Bestaande resources kunnen worden hersteld door een hersteltaak te activeren. Dit heeft ook geen invloed op labels op resource groepen.
2. Vereisten voor geonaleving afdwingen met behulp van Azure Policy: met het beleid kunt u de locaties beperken die uw organisatie kan opgeven tijdens het implementeren van resources. 
3. Toegestane Sku's voor virtuele machines opgeven voor implementaties: met dit beleid kunt u een set virtuele machine-Sku's opgeven die uw organisatie kan implementeren.
4. Controleren op Vm's die geen beheerde schijven gebruiken met behulp van Azure Policy
5. Gebruik ' een tag overnemen van resource groepen ' met behulp van Azure Policy: met het beleid worden de opgegeven tag en waarde van de bovenliggende resource groep toegevoegd of vervangen wanneer een resource wordt gemaakt of bijgewerkt. Bestaande resources kunnen worden hersteld door een hersteltaak te activeren.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Advisor-aanbevelingen:
* [Inleiding tot Advisor](advisor-overview.md)
* [Aan de slag](advisor-get-started.md)
* [Aanbevelingen voor Advisor-kosten](advisor-cost-recommendations.md)
* [Aanbevelingen voor Advisor-prestaties](advisor-performance-recommendations.md)
* [Aanbevelingen voor hoge Beschik baarheid van Advisor](advisor-high-availability-recommendations.md)
* [Aanbevelingen voor de beveiliging van Advisor](advisor-security-recommendations.md)
