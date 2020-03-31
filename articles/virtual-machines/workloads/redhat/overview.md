---
title: Red Hat-workloads in Azure-overzicht | Microsoft Documenten
description: Meer informatie over het Red Hat-productaanbod dat beschikbaar is in Azure.
services: virtual-machines-linux
author: asinn826
manager: borisb2015
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 718447e1dbf597af4349eab0be78a2bb544dec90
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78970176"
---
# <a name="red-hat-workloads-on-azure"></a>Red Hat-workloads op Azure

Red Hat-workloads worden ondersteund door verschillende aanbiedingen op Azure. Red Hat Enterprise Linux (RHEL) beelden vormen de kern van RHEL workloads, net als de Red Hat Update Infrastructure (RHUI).

## <a name="red-hat-enterprise-linux-images"></a>Red Hat Enterprise Linux afbeeldingen

Azure biedt een breed aanbod van RHEL-afbeeldingen op Azure. Deze afbeeldingen worden beschikbaar gesteld via twee verschillende licentiemodellen: pay-as-you-go en bring-your-own-subscription (BYOS). Nieuwe RHEL-afbeeldingen op Azure worden gepubliceerd wanneer nieuwe RHEL-versies worden uitgebracht en bijgewerkt gedurende hun levenscyclus, indien nodig.

### <a name="pay-as-you-go-images"></a>Pay-as-you-go-afbeeldingen

Azure biedt een verscheidenheid aan RHEL-pay-as-you-go-afbeeldingen. Deze beelden komen goed recht op RHEL en zijn gekoppeld aan een bron van updates (Red Hat Update Infrastructure). Deze afbeeldingen brengen een premievergoeding in rekening voor het RHEL-recht en updates. RHEL pay-as-you-go afbeelding varianten omvatten:

* Standaard RHEL.
* RHEL voor SAP.
* RHEL voor SAP met services met hoge beschikbaarheid en update.

U de pay-as-you-go-afbeeldingen gebruiken als u zich geen zorgen wilt maken over het afzonderlijk betalen voor het juiste aantal abonnementen.

### <a name="red-hat-gold-images"></a>Red Hat Gold Afbeeldingen

Azure biedt ook Red`rhel-byos`Hat Gold Images ( ). Deze afbeeldingen kunnen handig zijn voor klanten die bestaande Red Hat-abonnementen hebben en deze in Azure willen gebruiken. U moet uw bestaande Red Hat-abonnementen voor Red Hat Cloud Access inschakelen voordat u ze in Azure gebruiken. Toegang tot deze afbeeldingen wordt automatisch verleend wanneer uw Red Hat-abonnementen zijn ingeschakeld voor Cloud Access en voldoen aan de vereisten om in aanmerking te komen. Met behulp van deze afbeeldingen kan een klant dubbele facturering voorkomen die kan worden gemaakt van het gebruik van de pay-as-you-go-afbeeldingen.
* Meer informatie over het [inschakelen van uw Red Hat-abonnementen voor Cloud Access met Azure](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs).
* Meer informatie over het [zoeken naar Red Hat Gold-afbeeldingen in de Azure-portal, de Azure CLI- of PowerShell-cmdlet.](./byos.md)

> [!NOTE]
> Dubbele facturering gebeurt wanneer een gebruiker twee keer betaalt voor RHEL-abonnementen. Dit scenario gebeurt meestal wanneer een klant Red Hat Subscription-Manager gebruikt om een recht toe te voegen aan een RHEL pay-as-you-go VM. Een klant die bijvoorbeeld Subscription-Manager gebruikt om een recht op SAP-pakketten toe te voegen aan een RHEL-pay-as-you-go-afbeelding, wordt indirect dubbel gefactureerd omdat hij twee keer voor RHEL betaalt. Ze betalen één keer via de pay-as-you-go premium fee en eenmaal via hun SAP-abonnement. Dit scenario gebeurt niet voor byos-beeldgebruikers.

### <a name="generation-2-images"></a>Generatie 2 beelden

Generatie 2 virtuele machines (VM's) bieden een aantal nieuwere functies in vergelijking met Generatie 1 VM's. Zie voor meer informatie de [generatie 2-documentatie.](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2) Het belangrijkste verschil met een RHEL-afbeeldingsperspectief is dat Generatie 2 VM's een UEFI gebruiken in plaats van BIOS-firmware-interface. Ze gebruiken ook een GUID Partition Table (GPT) in plaats van een master boot record (MBR) op de opstarttijd. Het gebruik van een GPT zorgt onder andere voor OS-schijfformaten groter dan 2 TB. Bovendien draaien de [Mv2-serie VM's](../../mv2-series.md) alleen op Generatie 2-afbeeldingen.

RHEL Generation 2-afbeeldingen zijn beschikbaar in de Azure Marketplace. Zoek naar 'gen2' in de afbeelding SKU in de lijst met alle afbeeldingen die worden weergegeven wanneer u de Azure CLI gebruikt. Ga naar het tabblad **Geavanceerd** in het VM-implementatieproces om een Generatie 2-VM te implementeren.

## <a name="red-hat-update-infrastructure"></a>Update-infrastructuur voor Red Hat

Azure biedt Red Hat Update Infrastructure alleen voor pay-as-you-go RHEL VM's. RHUI is in feite een spiegel van de Red Hat CDN's, maar is alleen toegankelijk voor de Azure pay-as-you-go RHEL VM's. U hebt toegang tot de juiste pakketten, afhankelijk van welke RHEL-afbeelding u hebt geïmplementeerd. Een RHEL voor SAP-afbeelding heeft bijvoorbeeld toegang tot de SAP-pakketten naast basisRHEL-pakketten.

### <a name="rhui-update-behavior"></a>RHUI-updategedrag

RHEL-afbeeldingen die standaard zijn verbonden met RHUI-update `yum update` met de nieuwste secundaire versie van RHEL wanneer een wordt uitgevoerd. Dit gedrag betekent dat een RHEL 7.4 VM kan worden `yum update` geüpgraded naar RHEL 7.7 als er een bewerking op wordt uitgevoerd. Dit gedrag is door het ontwerp voor RHUI. Als u dit upgradegedrag wilt beperken, schakelt u over van reguliere RHEL-repositories naar [Extended Update Support-repositories.](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [RHEL-afbeeldingen in Azure](./redhat-images.md).
* Meer informatie over [Red Hat Update Infrastructure](./redhat-rhui.md).
* Meer informatie over de [Red`rhel-byos`Hat Gold Image () aanbieding](./byos.md).
