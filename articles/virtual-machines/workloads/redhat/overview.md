---
title: Overzicht van Red Hat-workloads op Azure | Microsoft Docs
description: Meer informatie over de product aanbiedingen van Red Hat die beschikbaar zijn op Azure.
services: virtual-machines-linux
author: asinn826
manager: borisb2015
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 718447e1dbf597af4349eab0be78a2bb544dec90
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970176"
---
# <a name="red-hat-workloads-on-azure"></a>Red Hat-workloads op Azure

Red Hat-workloads worden ondersteund door diverse aanbiedingen op Azure. Red Hat Enterprise Linux-installatie kopieën (RHEL) zijn de kern van RHEL-workloads, zoals de Red Hat Update infrastructure (RHUI).

## <a name="red-hat-enterprise-linux-images"></a>Red Hat Enterprise Linux installatie kopieën

Azure biedt een breed aanbod aan RHEL-installatie kopieën in Azure. Deze installatie kopieën worden beschikbaar gesteld via twee verschillende licentie modellen: betalen per gebruik en uw eigen abonnement (BYOS). Nieuwe RHEL-installatie kopieën in Azure worden gepubliceerd wanneer nieuwe RHEL-versies in hun levens duur worden uitgebracht en bijgewerkt, indien nodig.

### <a name="pay-as-you-go-images"></a>Betalen per gebruik-installatie kopieën

Azure biedt een aantal RHEL betalen per gebruik-installatie kopieën. Deze installatie kopieën zijn goed in aanmerking voor RHEL en zijn gekoppeld aan een bron van updates (Red Hat update Infrastructure). Met deze installatie kopieën worden Premium-kosten in rekening gebracht voor het RHEL recht en de updates. RHEL-varianten voor betalen per gebruik zijn onder andere:

* Standaard RHEL.
* RHEL voor SAP.
* RHEL voor SAP met hoge Beschik baarheid en Update Services.

U kunt de betalen per gebruik-installatie kopieën gebruiken als u zich geen zorgen meer wilt maken over het aantal afzonderlijke abonnementen.

### <a name="red-hat-gold-images"></a>Red Hat Gold-installatie kopieën

Azure biedt ook Red Hat Gold-installatie kopieën (`rhel-byos`). Deze installatie kopieën kunnen nuttig zijn voor klanten die bestaande Red Hat-abonnementen hebben en ze willen gebruiken in Azure. U bent verplicht uw bestaande Red Hat-abonnementen voor Red Hat Cloud Access in te scha kelen voordat u deze kunt gebruiken in Azure. Toegang tot deze installatie kopieën wordt automatisch verleend wanneer uw Red Hat-abonnementen zijn ingeschakeld voor Cloud toegang en voldoen aan de vereisten voor de geschiktheid. Door gebruik te maken van deze installatie kopieën kan een klant dubbele facturering voor komen die kan worden gemaakt met behulp van de betalen per gebruik-installatie kopie.
* Meer informatie over [het inschakelen van uw Red Hat-abonnementen voor Cloud toegang met Azure](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs).
* Meer informatie over het [zoeken naar Red Hat Gold-installatie kopieën in de Azure Portal, de Azure CLI of de Power shell-cmdlet](./byos.md).

> [!NOTE]
> Dubbele facturering wordt gedaan wanneer een gebruiker twee keer betaalt voor RHEL-abonnementen. Dit scenario treedt meestal op wanneer een klant een Red Hat Subscription-Manager gebruikt om een recht te koppelen aan een RHEL-VM met betalen per gebruik. Een klant die bijvoorbeeld gebruikmaakt van abonnement-manager om een recht te koppelen voor SAP-pakketten op een RHEL betalen per gebruik-installatie kopie, wordt indirect dubbel gefactureerd, omdat ze twee keer betalen voor RHEL. Ze betalen eenmaal via de betalen naar gebruik-tarieven en eenmaal via hun SAP-abonnement. Dit scenario heeft geen effect op het BYOS van installatie kopie gebruikers.

### <a name="generation-2-images"></a>Installatie kopieën van de 2e generatie

Virtuele machines van de tweede generatie bieden een aantal nieuwere functies vergeleken met virtuele machines van de eerste generatie. Zie de documentatie van de [tweede generatie](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2)voor meer informatie. Het belangrijkste verschil van een RHEL-afbeeldings perspectief is dat virtuele machines van de tweede generatie een UEFI gebruiken in plaats van BIOS-Firmware-Interface. Ze gebruiken ook een GUID-partitie tabel (GPT) in plaats van een Master Boot Record (MBR) op de opstart tijd. Het gebruik van een GPT maakt onder andere de grootte van het besturings systeem schijf groter dan 2 TB. Daarnaast worden de Vm's uit de [Mv2-serie](../../mv2-series.md) alleen uitgevoerd op installatie kopieën van de tweede generatie.

RHEL Generation 2-installatie kopieën zijn beschikbaar op de Azure Marketplace. Zoek naar ' Gen2 ' in de afbeeldings-SKU in de lijst met alle installatie kopieën die worden weer gegeven wanneer u de Azure CLI gebruikt. Ga naar het tabblad **Geavanceerd** in het implementatie proces van de VM om een virtuele machine van de tweede generatie te implementeren.

## <a name="red-hat-update-infrastructure"></a>Update-infrastructuur voor Red Hat

Azure biedt alleen een Red Hat update-infra structuur voor RHEL Vm's met betalen per gebruik. RHUI is in feite een mirror van de Red Hat Cdn's, maar is alleen toegankelijk voor de Azure betalen naar gebruik-RHEL Vm's. U hebt toegang tot de juiste pakketten, afhankelijk van de RHEL installatie kopie die u hebt geïmplementeerd. Zo heeft een RHEL voor SAP-installatie kopie ook toegang tot de SAP-pakketten en base RHEL-pakketten.

### <a name="rhui-update-behavior"></a>Gedrag van RHUI-updates

RHEL-installatie kopieën die zijn verbonden met RHUI update, zijn standaard ingesteld op de meest recente secundaire versie van RHEL wanneer een `yum update` wordt uitgevoerd. Dit gedrag houdt in dat een RHEL 7,4 VM kan worden bijgewerkt naar RHEL 7,7 als er een `yum update` bewerking wordt uitgevoerd. Dit gedrag is inherent aan het ontwerp voor RHUI. Als u dit upgrade gedrag wilt beperken, schakelt u van normale RHEL-opslag plaatsen naar [uitgebreide ondersteuning voor update-opslag](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms)plaatsen.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [RHEL-installatie kopieën in azure](./redhat-images.md).
* Meer informatie over de [infra structuur voor Red Hat-updates](./redhat-rhui.md).
* Meer informatie over de [aanbieding voor Red Hat Gold image (`rhel-byos`)](./byos.md).
