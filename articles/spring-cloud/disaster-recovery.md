---
title: Geo-nood herstel voor Azure Lente in de Cloud | Microsoft Docs
description: Meer informatie over het beveiligen van uw lente-Cloud toepassing vanuit regionale uitval
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.openlocfilehash: 4961e5a63e5bc1933cf19b1f291b521d89cbda0e
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279139"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Herstel na nood geval in de cloud van Azure lente

In dit artikel worden enkele strategieën beschreven die u kunt gebruiken om te voor komen dat uw Azure lente-Cloud toepassingen downtime-uitval ondervinden.  Elke regio of Data Center kan downtime ondervinden die is veroorzaakt door regionale rampen, maar een zorgvuldige planning kan invloed hebben op uw klanten.

## <a name="plan-your-application-deployment"></a>De implementatie van uw toepassing plannen

Azure lente-Cloud toepassingen worden uitgevoerd in een specifieke regio.  Azure werkt vanuit diverse geografische regio’s over de hele wereld. Een Azure-geografie is een gedefinieerd gebied van de wereld dat ten minste één Azure-regio bevat. Een Azure-regio is een gebied binnen een geografie met een of meer data centers.  Elke Azure-regio is gekoppeld aan een andere regio binnen dezelfde geografie, waarbij een regionale koppeling wordt gemaakt. Azure serializt platform updates (gepland onderhoud) over regionale paren, zodat slechts één regio per paar tegelijk wordt bijgewerkt. In het geval van een storing die betrekking heeft op meerdere regio's, wordt voor het herstel ten minste één regio in elk paar een prioriteit gegeven.

Voor hoge Beschik baarheid en bescherming tegen rampen moet u uw lente-Cloud toepassingen implementeren in meerdere regio's.  Azure biedt een lijst met [gekoppelde regio's](../best-practices-availability-paired-regions.md) , zodat u uw lente-Cloud implementaties kunt plannen voor regionale paren.  We raden u aan drie belang rijke factoren te overwegen bij het ontwerpen van uw micro service-architectuur: regionale Beschik baarheid, Azure gekoppelde regio's en beschik baarheid van de service.

*  Beschik baarheid van regio: Kies een geografisch gebied dat uw gebruikers dicht kunnen bevinden om de netwerk vertraging en de verzend tijd te minimaliseren.
*  Gekoppelde Azure-regio's: Kies gepaarde regio's binnen uw gekozen geografische gebied om ervoor te zorgen dat er gecooerdineerde platform updates en op prioriteiten gebaseerde herstel taken worden uitgevoerd.
*  Beschik baarheid van de service: Bepaal of de gekoppelde regio's warm/hot, Hot/warme of warme/koud moeten worden uitgevoerd.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Azure Traffic Manager gebruiken om verkeer te routeren

[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) biedt taak verdeling van verkeer op basis van DNS en kan netwerk verkeer distribueren over meerdere regio's.  Gebruik Azure Traffic Manager om klanten te leiden naar het dichtstbijzijnde Azure veer Cloud service-exemplaar.  Voor de beste prestaties en redundantie moet alle toepassings verkeer via Azure Traffic Manager worden doorgestuurd voordat het naar uw Azure lente-Cloud service wordt verzonden.

Als u Azure lente-Cloud toepassingen in meerdere regio's hebt, kunt u Azure Traffic Manager gebruiken om de stroom van verkeer naar uw toepassingen in elke regio te beheren.  Definieer een Azure Traffic Manager-eind punt voor elke service met behulp van het service-IP-adres. Klanten moeten verbinding maken met een Azure Traffic Manager DNS-naam die verwijst naar de Azure lente-Cloud service.  Met Azure Traffic Manager Load wordt verkeer gespreid over de gedefinieerde eind punten.  Als een nood geval een Data Center doorgeeft, stuurt Azure Traffic Manager verkeer van die regio naar het bijbehorende paar, waardoor de continuïteit van de service wordt gegarandeerd.