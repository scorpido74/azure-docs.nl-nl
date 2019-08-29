---
title: Aan de slag met Cloud Foundry op Microsoft Azure | Microsoft Docs
description: OSS-of Pivot-Cloud Foundry uitvoeren op Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: seanmck
manager: gwallace
editor: ''
tags: ''
keywords: ''
ms.assetid: 2a15ffbf-9f86-41e4-b75b-eb44c1a2a7ab
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/19/2017
ms.author: seanmck
ms.openlocfilehash: d91ad0bea7f79dd67edd4f0bb9e06a37a0f86bea
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70091925"
---
# <a name="cloud-foundry-on-azure"></a>Cloud Foundry op Azure

Cloud Foundry is een open-source platform-as-a-service (PaaS) voor het bouwen, implementeren en gebruiken van 12-factor toepassingen die in verschillende talen en frameworks zijn ontwikkeld. In dit document worden de opties beschreven die u hebt voor het uitvoeren van Cloud Foundry op Azure en hoe u aan de slag kunt gaan.

## <a name="cloud-foundry-offerings"></a>Cloud Foundry-aanbiedingen

Er zijn twee soorten Cloud Foundry beschikbaar voor uitvoering op Azure: Open Source Cloud Foundry (OSS CF) en Pivotal Cloud Foundry (PCF). OSS CF is een volledig [open-source](https://github.com/cloudfoundry) versie van Cloud Foundry die wordt beheerd door de Cloud Foundry Foundation. Pivotal Cloud Foundry is een zakelijke distributie van Cloud Foundry van Pivotal Software Inc. We kijken naar enkele van de verschillen tussen de twee aanbiedingen.

### <a name="open-source-cloud-foundry"></a>Open-Source Cloud Foundry

U kunt OSS-Cloud Foundry implementeren op Azure door eerst een BOSH-Director te implementeren en vervolgens Cloud Foundry te implementeren met behulp van de [instructies in github](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md). Zie de [documentatie](https://docs.cloudfoundry.org/) van de Cloud Foundry Foundation voor meer informatie over het gebruik van OSS CF.

Micro soft biedt de beste ondersteuning voor OSS CF via de volgende Community-kanalen:

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slackhttpsslackcloudfoundryorg"></a>Bosh-Azure-CPI-kanaal voor [Cloud Foundry vertraging](https://slack.cloudfoundry.org/)
- [CF-Bosh-mailing lijst](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- Problemen met GitHub voor de [CPI](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) en [Service Broker](https://github.com/Azure/meta-azure-service-broker/issues)

>[!NOTE]
> Het ondersteunings niveau voor uw Azure-resources, zoals de virtuele machines waarop u Cloud Foundry uitvoert, is gebaseerd op uw Azure-ondersteunings overeenkomst. Community-ondersteuning met de hoogste inspanning geldt alleen voor de Cloud Foundry-specifieke onderdelen.

### <a name="pivotal-cloud-foundry"></a>Pivotal Cloud Foundry

Pivotal Cloud Foundry omvat hetzelfde kern platform als de OSS-distributie, samen met een aantal eigen beheer Programma's en Enter prise-ondersteuning. Als u PCF op Azure wilt uitvoeren, moet u een licentie van Pivot-out aanschaffen. De PCF-aanbieding van Azure Marketplace bevat een proef licentie van 90 dagen.

De hulpprogram ma's omvatten [Operations Manager](https://docs.pivotal.io/pivotalcf/customizing/), een webtoepassing waarmee de implementatie en het beheer van een Cloud Foundry Foundation en Pivot- [apps Manager](https://docs.pivotal.io/pivotalcf/console/)wordt vereenvoudigd, een webtoepassing voor het beheren van gebruikers en toepassingen.

Naast de ondersteunings kanalen die worden vermeld voor OSS CF hierboven, geeft een PCF-licentie u recht om contact op te nemen met Pivotal voor ondersteuning. Micro soft en Pivotal bieden ook ondersteuning voor werk stromen die u in staat stellen om contact op te nemen met een van de partijen voor hulp en om ervoor te zorgen dat uw vraag op de juiste wijze kan worden gerouteerd afhankelijk van de locatie van het probleem.

## <a name="azure-service-broker"></a>Azure Service Broker

Cloud Foundry moedigt de methodologie van de [' 12-factor-app '](https://12factor.net/) aan, die een schone schei ding van stateless toepassings processen en stateful-back-upservices propageert. [Service Brokers](https://docs.cloudfoundry.org/services/api.html) bieden een consistente manier voor het inrichten en binden van services aan toepassingen. De [Azure Service Broker](https://github.com/Azure/meta-azure-service-broker) bevat enkele van de belangrijkste Azure-Services via dit kanaal, waaronder Azure Storage en Azure SQL.

Als u gebruikmaakt van Cloud Foundry draaien, is de Service Broker ook [beschikbaar als een tegel](https://docs.pivotal.io/azure-sb/installing.html) van het draai bare netwerk.

## <a name="related-resources"></a>Gerelateerde resources

### <a name="azure-devops-services-plugin"></a>Azure DevOps Services-invoeg toepassing

Cloud Foundry is zeer geschikt voor de ontwikkeling van flexibele software, waaronder het gebruik van doorlopende integratie (CI) en continue levering (CD). Als u Azure DevOps Services gebruikt om uw projecten te beheren en een CI/CD-pijplijn wilt instellen Cloud Foundry, kunt u de [Azure DevOps Services Cloud Foundry build-extensie](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension)gebruiken. Met de invoeg toepassing kunt u eenvoudig implementaties configureren en automatiseren voor Cloud Foundry, of deze in azure of in een andere omgeving worden uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

- [Pivot-Cloud Foundry implementeren vanuit Azure Marketplace](https://azure.microsoft.com/marketplace/partners/pivotal/pivotal-cloud-foundryazure-pcf/)
- [Een app implementeren naar Cloud Foundry in azure](./cloudfoundry-deploy-your-first-app.md)
