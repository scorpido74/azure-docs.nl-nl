---
title: Geo-disaster herstel azure springcloud | Microsoft Documenten
description: Meer informatie over hoe u uw Spring Cloud-toepassing beschermen tegen regionale storingen
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.openlocfilehash: 4961e5a63e5bc1933cf19b1f291b521d89cbda0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279139"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Azure Spring Cloud disaster recovery

In dit artikel worden enkele strategieën uitgelegd die u gebruiken om uw Azure Spring Cloud-toepassingen te beschermen tegen downtime.  Elke regio of datacenter kan downtime ervaren als gevolg van regionale rampen, maar een zorgvuldige planning kan de impact op uw klanten beperken.

## <a name="plan-your-application-deployment"></a>Uw toepassingsimplementatie plannen

Azure Spring Cloud-toepassingen worden uitgevoerd in een specifieke regio.  Azure werkt vanuit diverse geografische regio’s over de hele wereld. Een Azure-geografie is een gedefinieerd gebied van de wereld dat ten minste één Azure-regio bevat. Een Azure-gebied is een gebied binnen een geografie dat een of meer datacenters bevat.  Elke Azure-regio is gekoppeld aan een andere regio binnen dezelfde geografie, waarbij samen een regionaal paar wordt gemaakt. Azure serialiseert platformupdates (gepland onderhoud) voor regionale paren, zodat slechts één regio in elk paar tegelijk wordt bijgewerkt. In het geval van een storing die meerdere regio's treft, wordt ten minste één regio in elk paar geprioriteerd voor herstel.

Om te zorgen voor een hoge beschikbaarheid en bescherming tegen rampen moet u uw Spring Cloud-toepassingen in meerdere regio's implementeren.  Azure biedt een lijst met [gekoppelde regio's,](../best-practices-availability-paired-regions.md) zodat u uw Spring Cloud-implementaties plannen op regionale paren.  We raden u aan drie belangrijke factoren in overweging te nemen bij het ontwerpen van uw microservicearchitectuur: regiobeschikbaarheid, gekoppelde Azure-regio's en beschikbaarheid van services.

*  Beschikbaarheid van de regio: kies een geografisch gebied dicht bij uw gebruikers om de netwerkvertraging en de transmissietijd te minimaliseren.
*  Gekoppelde Azure-regio's: kies gekoppelde regio's binnen het door u gekozen geografische gebied om te zorgen voor gecoördineerde platformupdates en indien nodig geprioriteerde herstelinspanningen.
*  Beschikbaarheid van de service: bepaal of uw gekoppelde regio's warm/warm, warm/warm of warm/koud moeten werken.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Azure Traffic Manager gebruiken om verkeer te routeren

[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) biedt DNS-gebaseerde traffic load-balancing en kan netwerkverkeer over meerdere regio's distribueren.  Gebruik Azure Traffic Manager om klanten naar de dichtstbijzijnde Azure Spring Cloud-serviceinstantie te leiden.  Voor de beste prestaties en redundantie u al het toepassingsverkeer door Azure Traffic Manager leiden voordat u het naar uw Azure Spring Cloud-service verzendt.

Als u Azure Spring Cloud-toepassingen in meerdere regio's hebt, gebruikt u Azure Traffic Manager om de verkeersstroom naar uw toepassingen in elke regio te beheren.  Definieer een Azure Traffic Manager-eindpunt voor elke service met behulp van het bedrijfs-IP-adres. Klanten moeten verbinding maken met een DNS-naam van Azure Traffic Manager die naar de Azure Spring Cloud-service wijst.  Azure Traffic Manager load balanceert verkeer over de gedefinieerde eindpunten.  Als een ramp een datacenter treft, leidt Azure Traffic Manager verkeer van die regio naar het paar, zodat de servicecontinuïteit wordt gewaarborgd.