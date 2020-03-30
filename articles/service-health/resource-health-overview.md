---
title: Overzicht van Azure Resource Health
description: Overzicht van Azure Resource Health
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: 7a1dfe5e93d0e19aeb343d113a24ed882a5b3f69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159248"
---
# <a name="resource-health-overview"></a>Overzicht resourcestatus
 
Azure Resource Health helpt u bij het diagnosticeren en ondersteuning krijgen voor serviceproblemen die van invloed zijn op uw Azure-bronnen. Het rapporteert over de huidige en vroegere gezondheid van uw middelen.

[Azure-statusrapporten](https://status.azure.com) over serviceproblemen die van invloed zijn op een brede set Azure-klanten. Resource Health biedt u een gepersonaliseerd dashboard met de status van uw resources. Resourcestatus geeft alle keren weer dat uw resources niet beschikbaar zijn vanwege Azure-serviceproblemen. Met deze gegevens u eenvoudig zien of een SLA is geschonden.

## <a name="resource-definition-and-health-assessment"></a>Definitie van hulpbronnen en gezondheidsbeoordeling

Een *bron* is een specifiek exemplaar van een Azure-service, zoals een virtuele machine, web-app of SQL-database. Resourcestatus is gebaseerd op signalen van verschillende Azure-services om te beoordelen of een resource in orde is. Als een resource niet in orde is, analyseert Resource Health aanvullende informatie om de bron van het probleem te bepalen. Het rapporteert ook over acties die Microsoft onderneemt om het probleem op te lossen en identificeert dingen die u doen om het aan te pakken.

Zie de lijst met resourcetypen en statuscontroles bij [Azure Resource Health](resource-health-checks-resource-types.md)voor meer informatie over hoe de status wordt beoordeeld.

## <a name="health-status"></a>Algemene status

De status van een resource wordt weergegeven als een van de volgende statussen.

### <a name="available"></a>Beschikbaar

*Beschikbaar* betekent dat er geen gebeurtenissen zijn gedetecteerd die de status van de resource beïnvloeden. In gevallen waarin de resource is hersteld van ongeplande downtime in de afgelopen 24 uur, ziet u een melding 'Onlangs opgelost'.

![Status van *Beschikbaar* voor een virtuele machine met een melding 'Onlangs opgelost'](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Niet beschikbaar

*Niet beschikbaar* betekent dat de service een doorlopende platform- of niet-platformgebeurtenis heeft gedetecteerd die van invloed is op de status van de resource.

#### <a name="platform-events"></a>Platformevenementen

Platformgebeurtenissen worden geactiveerd door meerdere onderdelen van de Azure-infrastructuur. Ze omvatten zowel geplande acties (bijvoorbeeld gepland onderhoud) als onverwachte incidenten (bijvoorbeeld een ongeplande hostreboot of gedegradeerde hosthardware die naar verwachting na een bepaald tijdvenster mislukt).

Resourcestatus bevat aanvullende details over de gebeurtenis en het herstelproces. Het stelt u ook in staat om contact op te nemen met Microsoft Support, zelfs als u geen actieve ondersteuningsovereenkomst hebt.

![Status van *Niet beschikbaar* voor een virtuele machine vanwege een platformgebeurtenis](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Niet-platformgebeurtenissen

Niet-platformgebeurtenissen worden geactiveerd door gebruikersacties. Voorbeelden hiervan zijn het stoppen van een virtuele machine of het bereiken van het maximum aantal verbindingen met Azure Cache voor Redis.

![Status van "Niet beschikbaar" voor een virtuele machine vanwege een niet-platformgebeurtenis](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Onbekend

*Onbekend* betekent dat Resource Health al meer dan 10 minuten geen informatie over de bron heeft ontvangen. Hoewel deze status geen definitieve indicatie is van de status van de resource, is het een belangrijk gegevenspunt voor het oplossen van problemen.

Als de resource wordt uitgevoerd zoals verwacht, wordt de status van de resource na enkele minuten gewijzigd in *Beschikbaar.*

Als u problemen ondervindt met de resource, kan de *status Onbekende* betekenen dat een gebeurtenis in het platform van invloed is op de resource.

![Status van *Onbekend* voor een virtuele machine](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Verminderd beschikbaar

*Gedegradeerd* betekent dat uw resource een prestatieverlies heeft gedetecteerd, hoewel deze nog steeds beschikbaar is voor gebruik.

Verschillende bronnen hebben hun eigen criteria voor wanneer ze melden dat ze zijn gedegradeerd.

![Status van *Gedegradeerd* voor een virtuele machine](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>Een onjuiste status melden

Als u denkt dat de huidige status onjuist is, u het ons vertellen door **de foutieve status melden te**selecteren. In gevallen waarin een Azure-probleem u treft, raden we u aan contact op te nemen met ondersteuning vanuit Resourcehealth.

![Formulier om informatie in te dienen over een onjuiste status](./media/resource-health-overview/incorrect-status.png)

## <a name="history-information"></a>Geschiedenisinformatie

U hebt toegang tot maximaal 30 dagen geschiedenis in de sectie Geschiedenis van **de gezondheid** van Resource Health.

![Lijst van gebeurtenissen in resourcestatus van de afgelopen twee weken](./media/resource-health-overview/history-blade.png)

## <a name="get-started"></a>Aan de slag

Ga als u De resourcestatus voor één resource wilt openen:

1. Meld u aan bij Azure Portal.
2. Blader naar de resource.
3. Selecteer **resourcestatus**in het resourcemenu in het linkerdeelvenster .

![Resourcestatus openen vanuit de resourceweergave](./media/resource-health-overview/from-resource-blade.png)

U ook toegang krijgen tot Resourcestatus door **Alle services** en **typeresourcestatus** te selecteren in het tekstvak filter. Selecteer [resourcestatus](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth)in het ondersteuningsvenster **Help +** ondersteuning .

![Resourcestatus openen vanuit "Alle services"](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Volgende stappen

Bekijk deze verwijzingen voor meer informatie over Resource Health:
-  [Resourcetypen en statuscontroles in Azure Resource Health](resource-health-checks-resource-types.md)
-  [Veelgestelde vragen over Azure Resource Health](resource-health-faq.md)
