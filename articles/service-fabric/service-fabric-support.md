---
title: Meer informatie over azure servicefabric-ondersteuningsopties
description: Ondersteunde Azure Service Fabric-clusterversies en koppelingen naar bestanden
author: pkcsf
ms.topic: troubleshooting
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: 7494f0072f27f2c9b00db7070f19dfc05627eacf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282092"
---
# <a name="azure-service-fabric-support-options"></a>Ondersteuningsopties voor Azure Service Fabric

Om de juiste ondersteuning te bieden voor uw Service Fabric-clusters waarop u uw toepassingstaken uitvoert, hebben we verschillende opties voor u ingesteld. Afhankelijk van het niveau van de ondersteuning die nodig is en de ernst van het probleem, u de juiste opties kiezen. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Productieproblemen rapporteren of betaalde ondersteuning aanvragen voor Azure

Open een ticket voor ondersteuning [op Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) voor het melden van problemen op uw Service Fabric-cluster [die](https://support.microsoft.com/oas/default.aspx?prid=16146)op Azure zijn geïmplementeerd.

Meer informatie over:
 
- [Ondersteuning van Microsoft voor Azure](https://azure.microsoft.com/support/plans/?b=16.44).
- [Microsoft premier support](https://support.microsoft.com/en-us/premier).

> [!Note]
> Met clusters die op een bronzen betrouwbaarheidslaag of single nodecluster worden uitgevoerd, u alleen testworkloads uitvoeren. Als u problemen ondervindt met een cluster dat draait op de betrouwbaarheid van brons of cluster met één knooppunt, helpt het Ondersteuningsteam van Microsoft u bij het oplossen van het probleem, maar voert het geen rootcauseanalyse uit. Raadpleeg [de betrouwbaarheidskenmerken van het cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) voor meer informatie.
>
> Voor meer informatie over wat er nodig is voor een productie-klaar cluster, verwijzen wij u naar de [productie gereedheid checklist](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Productieproblemen melden of betaalde ondersteuning aanvragen voor zelfstandige Service Fabric-clusters

Voor het melden van problemen op uw Service Fabric-cluster die on-premises of op andere clouds zijn geïmplementeerd, opent u een ticket voor professionele ondersteuning op [de Microsoft-ondersteuningsportal.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

Meer informatie over:

- [Professionele ondersteuning van Microsoft voor on-premises.](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)
- [Microsoft premier support](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Azure Service Fabric-problemen melden

We hebben een GitHub repo opgezet voor het melden van Service Fabric-problemen.  We houden ook actief toezicht op de volgende fora.

### <a name="github-repo"></a>GitHub repo 

Azure Service Fabric-problemen rapporteren over [servicefabric-problemen git repo](https://github.com/Azure/service-fabric-issues). Deze repo is bedoeld voor het melden en bijhouden van problemen met Azure Service Fabric en voor het indienen van kleine functieaanvragen. **Gebruik dit niet om problemen met live-site te melden.**

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow- en MSDN-forums

De [Service Fabric-tag op StackOverflow][stackoverflow] en het [Service Fabric-forum op MSDN][msdn-forum] worden het beste gebruikt voor het stellen van vragen over hoe het platform werkt en hoe u bepaalde taken ermee uitvoeren.

### <a name="azure-feedback-forum"></a>Azure-feedbackforum

Het [Azure Feedback Forum for Service Fabric][uservoice-forum] is de beste plek voor het indienen van grote functie-ideeën die u voor het product hebt, omdat we de meest populaire verzoeken bekijken, maken deel uit van onze planning op middellange tot lange termijn. We raden je aan om steun te verzamelen voor je suggesties binnen de community.

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Preview-versies van Service Fabric - niet ondersteund voor productiegebruik

Van tijd tot tijd brengen we versies uit met belangrijke functies waarop we feedback willen, die worden uitgebracht als previews. Deze preview-versies mogen alleen worden gebruikt voor testdoeleinden. Uw productiecluster moet altijd een ondersteunde, stabiele Service Fabric-versie uitvoeren. Een preview-versie begint altijd met een groot en klein versienummer van 255. Als u bijvoorbeeld een Service Fabric-versie 255.255.5703.949 ziet, mag die versie alleen worden gebruikt in testclusters en is deze in preview. Deze preview releases worden ook aangekondigd op de [Service Fabric team blog](https://blogs.msdn.microsoft.com/azureservicefabric) en zal details over de functies opgenomen.
Er is geen betaalde ondersteuningsoptie voor deze preview-releases. Gebruik een van de opties die worden vermeld onder [Problemen met Azure Service Fabric melden](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues) om vragen te stellen of feedback te geven.

## <a name="next-steps"></a>Volgende stappen

[Ondersteunde Service Fabric-versies](service-fabric-versions.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: https://aka.ms/servicefabricdocs
[sample-repos]: https://aka.ms/servicefabricsamples
