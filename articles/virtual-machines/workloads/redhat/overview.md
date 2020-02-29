---
title: Overzicht van Red Hat-workloads op Azure | Microsoft Docs
description: Meer informatie over de product aanbiedingen voor Red Hat die beschikbaar zijn op Azure
services: virtual-machines-linux
author: asinn826
manager: borisb2015
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 424ef37885d685829a11d1864a72b043a562231c
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920549"
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

### <a name="red-hat-gold-images-rhel-byos"></a>Red Hat Gold-installatie kopieën (`rhel-byos`)
Azure biedt ook Red Hat Gold-installatie kopieën. Deze installatie kopieën kunnen nuttig zijn voor klanten die bestaande Red Hat-abonnementen hebben en ze willen gebruiken in Azure. U moet uw bestaande Red Hat-abonnementen voor Red Hat Cloud Access inschakelen voordat u deze kunt gebruiken in Azure. Toegang tot deze installatie kopieën wordt automatisch verleend wanneer uw Red Hat-abonnementen zijn ingeschakeld voor Cloud toegang en voldoen aan de vereisten voor de geschiktheid. Met behulp van deze installatie kopieën kan een klant dubbel factureren voor komen die wordt gebruikt om de PAYG-installatie kopieën te gebruiken.
* [Meer informatie over het inschakelen van uw Red Hat-abonnementen voor Cloud toegang met Azure](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs)
* [Meer informatie over het zoeken naar Red Hat Gold-installatie kopieën in de Azure Portal, CLI of Power shell-cmdlet](./byos.md)

> [!NOTE]
> Opmerking over dubbele facturering: dubbele facturering wordt gedaan wanneer een gebruiker twee keer betaalt voor RHEL-abonnementen. Dit gebeurt meestal wanneer een klant abonnements beheer gebruikt om een recht op een RHEL PAYG VM te koppelen. Bijvoorbeeld: een klant die gebruikmaakt van abonnement-manager om een recht te koppelen voor SAP-pakketten op een RHEL PAYG-installatie kopie, wordt indirect gefactureerd, omdat ze twee keer betalen voor RHEL-eenmaal door de kosten voor de PAYG Premium en eenmaal via hun SAP-abonnement. Dit geldt niet voor BYOS van installatie kopie gebruikers.

### <a name="generation-2-images"></a>Installatie kopieën van de 2e generatie
Virtuele machines van de tweede generatie bieden een aantal nieuwere functies in vergelijking met virtuele machines van de eerste generatie. Meer informatie vindt u in de [documentatie van de tweede generatie](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2). Het belangrijkste verschil van een RHEL-installatie kopie is dat virtuele machines van de tweede generatie een UEFI gebruiken in plaats van BIOS-Firmware-Interface en een GUID-partitie tabel (GPT) gebruiken in plaats van een Master Boot Record (MBR) op de opstart tijd. Dit maakt, onder andere, de besturingssysteem schijf grootten die groter zijn dan 2 TB. Daarnaast worden de Vm's uit de [Mv2-serie](../../mv2-series.md) alleen uitgevoerd op installatie kopieën van de tweede generatie.

RHEL Generation 2-installatie kopieën zijn beschikbaar op Marketplace. Zoek naar ' Gen2 ' in de afbeeldings-SKU bij het aanbieden van alle installatie kopieën met behulp van de Azure CLI en ga naar het tabblad Geavanceerd in het implementatie proces van de VM om een virtuele machine van de 2e generatie te implementeren.

## <a name="red-hat-update-infrastructure-rhui"></a>Infra structuur voor Red Hat update (RHUI)
Azure biedt alleen een Red Hat update-infra structuur voor PAYG RHEL virtual machines (Vm's). RHUI is in feite een mirror van de Red Hat Cdn's, maar is alleen toegankelijk voor de virtuele machines van Azure PAYG RHEL. U krijgt toegang tot de juiste pakketten, afhankelijk van de RHEL-installatie kopie die u hebt geïmplementeerd. Bijvoorbeeld, een RHEL voor SAP-installatie kopie heeft naast base RHEL-pakketten ook toegang tot de SAP-pakketten.

### <a name="rhui-update-behavior"></a>Gedrag van RHUI-updates
RHEL-installatie kopieën die zijn verbonden met RHUI, worden standaard bijgewerkt naar de meest recente secundaire versie van RHEL wanneer een `yum update` wordt uitgevoerd. Dit gedrag houdt in dat een RHEL 7,4 VM kan worden bijgewerkt naar RHEL 7,7 als er een `yum update` bewerking wordt uitgevoerd. Dit is het ontwerp van RHUI. Dit upgrade gedrag kan echter worden verholpen door te scha kelen van normale RHEL-opslag plaatsen naar de [Eus-opslag plaatsen (Extended update support)](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms).

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [RHEL-installatie kopieën in azure](./redhat-images.md)
* Meer informatie over de [infra structuur voor Red Hat update](./redhat-rhui.md)
* Meer informatie over de [aanbieding voor Red Hat Gold image (`rhel-byos`)](./byos.md)
