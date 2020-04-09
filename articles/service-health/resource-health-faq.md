---
title: Veelgestelde vragen over Azure Resource Health
description: Overzicht van Azure Resource Health
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 7459a29dca01dc186d75b4545f89068569975607
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985028"
---
# <a name="azure-resource-health-faq"></a>Veelgestelde vragen over Azure Resource Health
Meer informatie over de antwoorden op veelgestelde vragen over Azure Resource Health.

## <a name="what-is-azure-resource-health"></a>Wat is Azure Resource Health?
Resource Health helpt u bij het diagnosticeren en krijgen van ondersteuning wanneer een Azure-probleem van invloed is op uw resources. Het biedt u informatie over de huidige en eerdere status van uw resources en helpt u problemen verhelpen. Resource Health biedt technische ondersteuning als u hulp nodig heeft bij problemen met Azure-services.  

## <a name="what-is-the-resource-health-intended-for"></a>Waar is de Resource Health voor bedoeld?
Zodra een probleem met een resource is gedetecteerd, kan Resource Health u helpen bij het diagnosticeren van de hoofdoorzaak. Het biedt hulp om het probleem en de technische ondersteuning te beperken wanneer u meer hulp nodig hebt bij Azure-serviceproblemen.

## <a name="what-health-checks-are-performed-by-resource-health"></a>Welke gezondheidscontroles worden uitgevoerd door Resource Health?
Resourcestatus voert verschillende controles uit op basis van het [resourcetype](resource-health-checks-resource-types.md). Deze controles zijn bedoeld om drie soorten kwesties te implementeren: 
- Ongeplande gebeurtenissen, bijvoorbeeld een onverwachte herstart van de host
- Geplande gebeurtenissen, zoals geplande updates van host-bes
- Gebeurtenissen die worden geactiveerd door acties van gebruikers, bijvoorbeeld een gebruiker die een virtuele machine opnieuw opstart

## <a name="what-does-each-of-the-health-status-mean"></a>Wat betekent elk van de gezondheidstoestand?
Er zijn drie verschillende gezondheidstoestanden:
- Beschikbaar: er zijn geen bekende problemen in het Azure-platform die van invloed kunnen zijn op deze bron
- Niet beschikbaar: resourcestatus heeft problemen gedetecteerd die van invloed zijn op de bron
- Onbekend: resourcestatus kan de status van een resource niet bepalen omdat deze is gestopt met het ontvangen van informatie over een resource. 

## <a name="what-does-the-unknown-status-mean-is-something-wrong-with-my-resource"></a>Wat betekent de onbekende status? Is er iets mis met mijn bron?
De status is ingesteld op onbekend wanneer Resourcehealth geen informatie meer ontvangt over een specifieke bron. Hoewel deze status geen definitieve indicatie is van de status van de resource, kan dit in gevallen waarin u problemen ondervindt, erop wijzen dat er een Azure-probleem is.

## <a name="how-can-i-get-help-for-a-resource-that-is-unavailable"></a>Hoe kan ik hulp krijgen voor een resource die niet beschikbaar is?
U een ondersteuningsverzoek indienen via het blade resourcestatus. U hebt geen ondersteuningsovereenkomst met Microsoft nodig om een aanvraag te openen wanneer de bron niet beschikbaar is omdat platformgebeurtenissen.

## <a name="does-resource-health-differentiate-between-unavailability-caused-by-platform-problems-versus-something-i-did"></a>Maakt Resource Health onderscheid tussen onbeschikbaarheid veroorzaakt door platformproblemen ten opzichte van iets wat ik heb gedaan?
Ja, wanneer een resource niet beschikbaar is, identificeert Resource Health de hoofdoorzaak binnen een van de volgende categorieën: 
-   Door de gebruiker gestarte actie
-   Geplande gebeurtenis 
-   Ongeplande gebeurtenis

In de portal worden door gebruikers geïnitieerde acties weergegeven met behulp van een blauw meldingspictogram, terwijl geplande en ongeplande gebeurtenissen worden weergegeven met behulp van een rood waarschuwingspictogram. Meer details zijn te vinden in het [overzicht Resourcehealth](Resource-health-overview.md).  

## <a name="can-i-integrate-resource-health-with-my-monitoring-tools"></a>Kan ik Resource Health integreren met mijn monitoringtools?
Resourcestatus heeft [preview-ondersteuning](resource-health-alert-arm-template-guide.md) voor waarschuwingen op basis van activiteitslogboeken. Waarschuwingen voor activiteitenlogboeken gebruiken [actiegroepen](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) om gebruikers te laten weten dat er een waarschuwing is geactiveerd. Actiegroepen ondersteunen verschillende meldingskanalen, zoals e-mail, sms, webhook en ITSM-acties.

## <a name="where-do-i-find-resource-health"></a>Waar vind ik Resource Health?
Nadat u zich hebt aangemeld bij de Azure-portal, zijn er meerdere manieren waarop u toegang hebt tot Resourcestatus:
- Navigeer naar uw bron. Selecteer **resourcestatus** in de linkernavigatie
- Ga naar het azure servicestatusblad.  Selecteer **resourcestatus**in de linkernavigatie .
- Open het **mes Help + Ondersteuning** door het vraagteken in de rechterbovenhoek van de portal te selecteren en vervolgens **Help + ondersteuning te**selecteren. Zodra het blad wordt geopend, selecteert u **Resourcestatus**

U de API voor resourcestatus ook gebruiken om informatie te verkrijgen over de status van uw resources.

## <a name="is-resource-health-available-for-all-resource-types"></a>Is resourcestatus beschikbaar voor alle resourcetypen?
De lijst met gezondheidscontroles en resourcetypen die worden ondersteund door resourcestatus is [hier](resource-health-checks-resource-types.md)te vinden.

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>Wat moet ik doen als mijn resource beschikbaar wordt weergegeven, maar ik geloof dat dit niet het is?"
Wanneer u de status van een resource controleert, klikt u onder de status van de status **op Onjuiste status melden**. Voordat u het rapport indient, u aanvullende gegevens verstrekken over waarom u van mening bent dat de huidige status onjuist is.

## <a name="is-resource-health-available-for-all-azure-regions"></a>Is resourcestatus beschikbaar voor alle Azure-regio's? 
Resourcestatus is beschikbaar in alle Azure-geo's.

## <a name="how-is-resource-health-different-from-azure-status-or-the-service-health-dashboard"></a>Hoe verschilt resourcestatus van de Azure-status of het dashboard Servicestatus?
De informatie van Resource Health is specifieker dan wat wordt geleverd door de Azure-status of het dashboard Servicestatus.

Terwijl [de Azure-status](https://status.azure.com) en het dashboard Servicestatus u informeren over serviceproblemen die van invloed zijn op een brede set klanten (bijvoorbeeld een Azure-regio), worden met Resource Health gedetailleerdere gebeurtenissen weergegeven die alleen relevant zijn voor de specifieke bron. Als een host bijvoorbeeld onverwacht opnieuw wordt opgestart, waarschuwt Resource Health alleen die klanten waarvan de virtuele machines op die host actief waren.

Het is belangrijk op te merken dat om u volledige zichtbaarheid te bieden van gebeurtenissen die van invloed zijn op uw resources, Resource Health ook gebeurtenissen weergeeft die zijn gepubliceerd in het dashboard Servicestatus.

## <a name="do-i-need-to-activate-resource-health-for-each-resource"></a>Moet ik ResourceHealth activeren voor elke resource?
Nee, gezondheidsinformatie is beschikbaar voor alle resourcetypen die beschikbaar zijn via Resourcestatus. 

## <a name="do-we-need-to-enable-resource-health-for-my-organization"></a>Moeten we Resource Health inschakelen voor mijn organisatie?
Nee.  Azure Resource Health is toegankelijk binnen de Azure-portal zonder installatievereisten.

## <a name="is-resource-health-available-free-of-charge"></a>Is Resource Health gratis beschikbaar?
Ja.  Azure Resource Health is gratis.

## <a name="what-are-the-recommendations-that-resource-health-provides"></a>Wat zijn de aanbevelingen die Resource Health biedt?
Op basis van de status biedt Resource Health u aanbevelingen met als doel de tijd die u hebt besteed aan het oplossen van problemen te verminderen. Voor beschikbare resources zijn de aanbevelingen gericht op het oplossen van de meest voorkomende problemen die klanten tegenkomen. Als de resource niet beschikbaar is vanwege een niet-geplande azure-gebeurtenis, ligt de focus op het assisteren tijdens en na het herstelproces. 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over resourcestatus:
-  [Overzicht van Azure Resource Health](Resource-health-overview.md)
-  [Resourcetypen en statuscontroles die beschikbaar zijn via Azure Resource Health](resource-health-checks-resource-types.md)
