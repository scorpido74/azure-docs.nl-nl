---
title: Overzicht van Red Hat-workloads op Azure | Microsoft Docs
description: Meer informatie over de product aanbiedingen voor Red Hat die beschikbaar zijn op Azure
services: virtual-machines-linux
author: asinn826
manager: borisb2015
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 12/18/2019
ms.author: alsin
ms.openlocfilehash: df787d4102752bdf61e30bc00d0d08307ac12319
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75473162"
---
# <a name="red-hat-workloads-on-azure"></a>Red Hat-workloads op Azure
Red Hat-workloads worden ondersteund door diverse aanbiedingen op Azure. Red Hat Enterprise Linux-installatie kopieën (RHEL) zijn de kern van RHEL-workloads, zoals de Red Hat Update infrastructure (RHUI).

## <a name="red-hat-enterprise-linux-rhel-images"></a>Red Hat Enterprise Linux-installatie kopieën (RHEL)
Azure biedt een breed aanbod aan RHEL-installatie kopieën in Azure. Deze installatie kopieën worden beschikbaar gesteld via twee verschillende licentie modellen: betalen naar gebruik (PAYG) en meebrengen uw eigen abonnement (BYOS). Nieuwe RHEL-installatie kopieën in Azure worden gepubliceerd wanneer nieuwe RHEL-versies zo nodig worden uitgebracht en bijgewerkt in hun levens duur.

### <a name="pay-as-you-go-payg-images"></a>PAYG-installatie kopieën (betalen per gebruik)
Azure biedt een aantal RHEL PAYG-installatie kopieën. Deze installatie kopieën zijn goed in aanmerking voor RHEL en zijn gekoppeld aan een bron van updates (Red Hat update Infrastructure). Met deze installatie kopieën worden Premium-kosten in rekening gebracht voor het RHEL recht en de updates. RHEL PAYG image varianten omvatten:
* Standaard RHEL
* RHEL voor SAP
* RHEL voor SAP met hoge Beschik baarheid en Update Services.

U kunt de PAYG-installatie kopieën gebruiken als u zich geen zorgen hoeft te maken dat u afzonderlijk betaalt voor het juiste aantal abonnementen.

### <a name="bring-your-own-subscription-byos-images"></a>Afbeeldingen met uw eigen abonnement (BYOS) meenemen
Azure biedt ook RHEL BYOS-installatie kopieën. Deze installatie kopieën kunnen nuttig zijn voor klanten die bestaande Red Hat-abonnementen hebben en ze willen gebruiken in Azure. U moet uw bestaande abonnementen converteren naar Cloud Access-abonnementen voordat u deze kunt gebruiken in Azure. Toegang tot deze installatie kopieën wordt alleen verleend als Red Hat verifieert dat u voldoende Cloud toegangs abonnementen hebt. Met behulp van deze installatie kopieën kan een klant dubbel factureren voor komen die wordt gebruikt om de PAYG-installatie kopieën te gebruiken. U kunt [hier](https://aka.ms/rhel-byos)toegang aanvragen tot de BYOS-installatie kopieën.

> [!NOTE]
> Opmerking over dubbele facturering: dubbele facturering wordt gedaan wanneer een gebruiker twee keer betaalt voor RHEL-abonnementen. Dit gebeurt meestal wanneer een klant abonnements beheer gebruikt om een recht op een RHEL PAYG VM te koppelen. Bijvoorbeeld: een klant die gebruikmaakt van abonnement-manager om een recht te koppelen voor SAP-pakketten op een RHEL PAYG-installatie kopie, wordt indirect gefactureerd, omdat ze twee keer betalen voor RHEL-eenmaal door de kosten voor de PAYG Premium en eenmaal via hun SAP-abonnement. Dit geldt niet voor BYOS van installatie kopie gebruikers.

## <a name="red-hat-update-infrastructure-rhui"></a>Infra structuur voor Red Hat update (RHUI)
Azure biedt alleen een Red Hat update-infra structuur voor PAYG RHEL virtual machines (Vm's). RHUI is in feite een mirror van de Red Hat Cdn's, maar is alleen toegankelijk voor de virtuele machines van Azure PAYG RHEL. U krijgt toegang tot de juiste pakketten, afhankelijk van de RHEL-installatie kopie die u hebt geïmplementeerd. Bijvoorbeeld, een RHEL voor SAP-installatie kopie heeft naast base RHEL-pakketten ook toegang tot de SAP-pakketten.

### <a name="rhui-update-behavior"></a>Gedrag van RHUI-updates
RHEL-installatie kopieën die zijn verbonden met RHUI, worden standaard bijgewerkt naar de meest recente secundaire versie van RHEL wanneer een `yum update` wordt uitgevoerd. Dit gedrag houdt in dat een RHEL 7,4 VM kan worden bijgewerkt naar RHEL 7,7 als er een `yum update` bewerking wordt uitgevoerd. Dit is het ontwerp van RHUI. Dit upgrade gedrag kan echter worden verholpen door te scha kelen van normale RHEL-opslag plaatsen naar de [Eus-opslag plaatsen (Extended update support)](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms).

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [RHEL-installatie kopieën in azure](./redhat-images.md)
* Meer informatie over de [infra structuur voor Red Hat update](./redhat-rhui.md)
* Meer informatie over de [RHEL BYOS-aanbieding](./redhat-byos.md)