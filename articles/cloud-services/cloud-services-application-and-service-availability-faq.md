---
title: Veelgestelde vragen over de beschikbaarheid van toepassingen en service
titleSuffix: Azure Cloud Services
description: In dit artikel worden de veelgestelde vragen weergegeven over de beschikbaarheid van toepassingen en services voor Microsoft Azure Cloud Services.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c294d4583ba2690e1f4952441ffb43bff1459059
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386915"
---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemen met de beschikbaarheid van toepassingen en services voor Azure Cloud Services: veelgestelde vragen (veelgestelde vragen)

Dit artikel bevat veelgestelde vragen over problemen met de beschikbaarheid van toepassingen en services voor [Microsoft Azure Cloud Services.](https://azure.microsoft.com/services/cloud-services) U ook de [pagina VM-grootte van Cloud Services](cloud-services-sizes-specs.md) raadplegen voor informatie over de grootte.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>Mijn rol is gerecycled. Is er een update uitgerold voor mijn cloudservice?
Ongeveer een keer per maand brengt Microsoft een nieuwe GastOS-versie uit voor Windows Azure PaaS VM's.Het gastbesturingssysteem is slechts één dergelijke update in de pijplijn. Een release kan worden beïnvloed door vele andere factoren. Bovendien draait Azure op honderdduizenden machines. Daarom is het onmogelijk om de exacte datum en tijd te voorspellen waarop je rollen opnieuw worden opgestart. We werken de Guest OS Update RSS-feed bij met de meest recente informatie die we hebben, maar je moet overwegen dat gerapporteerde tijd een geschatte waarde is. We zijn ons ervan bewust dat dit problematisch is voor klanten en werken aan een plan om opnieuw opstarten of juist tijd te beperken.

Zie [Azure Guest OS-releases en SDK-compatibiliteitsmatrix](cloud-services-guestos-update-matrix.md)voor meer informatie over recente updates van het gastbesturingssysteem.

Zie de MSDN-blogpost [Rolinstantie opnieuw opstarten vanwege OS-upgrades](https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)voor nuttige informatie over opnieuw opstarten en aanwijzingen voor technische details van updates van gast- en hostbesturingssysteems.

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>Waarom duurt het eerste verzoek voor mijn cloudservice nadat de service enige tijd niet actief is geweest langer dan normaal?
Wanneer de webserver het eerste verzoek ontvangt, wordt de code eerst opnieuw gecompileerd en vervolgens de aanvraag verwerkt. Daarom duurt het eerste verzoek langer dan de andere. Standaard wordt de app-groep afgesloten in gevallen van inactiviteit van de gebruiker. De app-groep wordt ook standaard elke 1.740 minuten (29 uur) gerecycled.

IIS-toepassingsgroepen (Internet Information Services) kunnen periodiek worden gerecycled om instabiele toestanden te voorkomen die kunnen leiden tot toepassingscrashes, vastlopen of geheugenlekken.

De volgende documenten helpen u dit probleem te begrijpen en te beperken:
* [Langzame initiële belasting voor IIS herstellen](https://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis)
* [IIS 7.5 webapplicatie eerste aanvraag na app-pool recyclen zeer traag](https://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow)

Als u het standaardgedrag van IIS wilt wijzigen, moet u opstarttaken gebruiken, want als u handmatig wijzigingen toepast op de webrolinstanties, gaan de wijzigingen uiteindelijk verloren.

Zie [Opstarttaken configureren en uitvoeren voor een cloudservice voor](cloud-services-startup-tasks.md)meer informatie.
