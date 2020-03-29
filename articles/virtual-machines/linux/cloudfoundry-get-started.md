---
title: Aan de slag met Cloud Foundry op Microsoft Azure
description: OSS of Pivotal Cloud Foundry uitvoeren op Microsoft Azure
author: seanmck
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 01/19/2017
ms.author: seanmck
ms.openlocfilehash: ba0b4a636d8f785f1c169e4066c9cf6d25ba81db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970053"
---
# <a name="cloud-foundry-on-azure"></a>Cloud Foundry op Azure

Cloud Foundry is een open-source platform-as-a-service (PaaS) voor het bouwen, implementeren en gebruiken van 12-factor toepassingen die in verschillende talen en frameworks zijn ontwikkeld. In dit document worden de opties beschreven die u hebt voor het uitvoeren van Cloud Foundry op Azure en hoe u aan de slag.

## <a name="cloud-foundry-offerings"></a>Cloud Foundry-aanbod

Er zijn twee vormen van Cloud Foundry beschikbaar voor azure: open-source Cloud Foundry (OSS CF) en Pivotal Cloud Foundry (PCF). OSS CF is een volledig [open-source](https://github.com/cloudfoundry) versie van Cloud Foundry beheerd door de Cloud Foundry Foundation. Pivotal Cloud Foundry is een enterprise distributie van Cloud Foundry van Pivotal Software Inc. We kijken naar enkele van de verschillen tussen de twee aanbiedingen.

### <a name="open-source-cloud-foundry"></a>Open-source Cloud Foundry

U OSS Cloud Foundry implementeren op Azure door eerst een BOSH-directeur te implementeren en vervolgens Cloud Foundry te implementeren, met behulp van de [instructies op GitHub.](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md) Zie de [documentatie](https://docs.cloudfoundry.org/) van de Cloud Foundry Foundation voor meer informatie over het gebruik van OSS CF.

Microsoft biedt ondersteuning voor OSS CF via de volgende communitykanalen:

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slack"></a>bosh-azure-cpi-kanaal op [Cloud Foundry Slack](https://slack.cloudfoundry.org/)
- [cf-bosh mailinglijst](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- GitHub-problemen voor de [CPI-](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) en [servicebroker](https://github.com/Azure/meta-azure-service-broker/issues)

>[!NOTE]
> Het ondersteuningsniveau voor uw Azure-resources, zoals de virtuele machines waar u Cloud Foundry uitvoert, is gebaseerd op uw Azure-ondersteuningsovereenkomst. Communityondersteuning met de beste inspanning is alleen van toepassing op de cloudfoundry-specifieke componenten.

### <a name="pivotal-cloud-foundry"></a>Pivotal Cloud Gieterij

Pivotal Cloud Foundry omvat hetzelfde kernplatform als de OSS-distributie, samen met een reeks eigen beheertools en bedrijfsondersteuning. Als u PCF op Azure wilt uitvoeren, moet u een licentie van Pivotal aanschaffen. Het PCF-aanbod van de Azure-marktplaats bevat een proeflicentie van 90 dagen.

De tools omvatten [Pivotal Operations Manager](https://docs.pivotal.io/pivotalcf/customizing/), een webapplicatie die de implementatie en het beheer van een Cloud Foundry-basis vereenvoudigt, en Pivotal Apps [Manager](https://docs.pivotal.io/pivotalcf/console/), een webapplicatie voor het beheren van gebruikers en toepassingen.

Naast de ondersteuningskanalen die hierboven voor OSS CF worden vermeld, geeft een PCF-licentie u het recht om contact op te nemen met Pivotal voor ondersteuning. Microsoft en Pivotal hebben ook ondersteuningsworkflows ingeschakeld waarmee u contact opnemen met een van beide partijen voor hulp en uw aanvraag op de juiste manier laten routeren, afhankelijk van waar het probleem zich bevindt.

## <a name="azure-service-broker"></a>Azure Service Broker

Cloud Foundry moedigt de ["twaalf-factor app"-methode](https://12factor.net/) aan, die een schone scheiding van stateloze toepassingsprocessen en stateful backing services bevordert. [Servicebrokers](https://docs.cloudfoundry.org/services/api.html) bieden een consistente manier om backservices aan toepassingen te leveren en te binden. De [Azure-servicebroker](https://github.com/Azure/meta-azure-service-broker) biedt een aantal van de belangrijkste Azure-services via dit kanaal, waaronder Azure-opslag en Azure SQL.

Als u Pivotal Cloud Foundry gebruikt, is de servicebroker ook [beschikbaar als tegel](https://docs.pivotal.io/azure-sb/installing.html) van het Pivotal Network.

## <a name="related-resources"></a>Gerelateerde resources

### <a name="azure-devops-services-plugin"></a>Azure DevOps Services-plug-in

Cloud Foundry is zeer geschikt voor agile software ontwikkeling, inclusief het gebruik van continuous integration (CI) en continuous delivery (CD). Als u Azure DevOps Services gebruikt om uw projecten te beheren en een CI/CD-pijplijn wilt instellen die cloudgieterij target, u de [azure DevOps Services Cloud Foundry-buildextensie](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension)gebruiken. De plug-in maakt het eenvoudig om implementaties naar Cloud Foundry te configureren en te automatiseren, of deze nu in Azure of een andere omgeving wordt uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

- [Pivotal Cloud Foundry implementeren vanuit azure marketplace](https://azure.microsoft.com/marketplace/partners/pivotal/pivotal-cloud-foundryazure-pcf/)
- [Een app implementeren voor Cloud Foundry in Azure](./cloudfoundry-deploy-your-first-app.md)
