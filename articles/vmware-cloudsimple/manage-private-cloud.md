---
title: Azure VMware Solutions (AVS) Privécloud beheren
description: Beschrijft de mogelijkheden die beschikbaar zijn voor het beheren van uw persoonlijke cloud resources en-activiteiten van uw AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 47bf2251f71204b99245c1a9d55ef87157c41dd8
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014824"
---
# <a name="manage-avs-private-cloud-resources-and-activities"></a>Automatische AVS-cloud resources en-activiteiten beheren

Persoonlijke Clouds van AVS worden beheerd vanuit de AVS-Portal. Controleer de status, beschik bare resources, activiteit op de privécloud van de AVS en andere instellingen van de AVS-Portal.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-avs-portal"></a>Toegang tot de AVS-Portal

Toegang tot de [AVS-Portal](access-cloudsimple-portal.md).

## <a name="view-the-list-of-avs-private-clouds"></a>De lijst met persoonlijke Clouds van AVS weer geven

Op het tabblad **persoonlijke Clouds** van de AVS op de pagina **resources** vindt u een overzicht van alle persoonlijke Clouds van de AVS in uw abonnement. De informatie bevat de naam, het aantal vSphere-clusters, de locatie, de huidige status van de Privécloud en de resource gegevens.

![Pagina met persoonlijke AVS-Cloud](media/manage-private-cloud.png)

Selecteer een persoonlijke AVS-Cloud voor meer informatie en acties.

## <a name="avs-private-cloud-summary"></a>Samen vatting van persoonlijke cloud van AVS

Bekijk een uitgebreide samen vatting van de geselecteerde Privécloud voor de Cloud. Overzichts pagina bevat de DNS-servers die zijn geïmplementeerd op de AVS-Privécloud. U kunt DNS-door sturen van lokale DNS-servers naar uw AVS-DNS-servers in de Privécloud-Cloud instellen. Zie voor meer informatie over DNS door sturen [DNS configureren voor naam omzetting voor automatische AVS-Cloud-vCenter vanuit on-premises](https://docs.azure.cloudsimple.com/on-premises-dns-setup/).

![Samen vatting van persoonlijke cloud van AVS](media/private-cloud-summary.png)

### <a name="available-actions"></a>Beschik bare acties

* [Start de vSphere-client](https://docs.azure.cloudsimple.com/vsphere-access/). Toegang tot de vCenter voor deze AVS-Privécloud.
* [Inkoop knooppunten](create-nodes.md). Voeg knoop punten toe aan deze AVS-Privécloud.
* [Uitvouwen](expand-private-cloud.md). Voeg knoop punten toe aan deze AVS-Privécloud.
* **Vernieuwen**. De gegevens op deze pagina bijwerken.
* **Verwijderen**. U kunt de AVS-Privécloud op elk gewenst moment verwijderen. **Voordat u verwijdert, moet u ervoor zorgen dat u een back-up hebt gemaakt van alle systemen en gegevens.** Als u een automatische AVS-Cloud verwijdert, worden alle Vm's, de configuratie van de vCenter en de gegevens verwijderd. Klik in de sectie samen vatting op **verwijderen** voor de geselecteerde AVS-privécloud. Na het verwijderen worden alle gegevens van de Privécloud van de Cloud gewist in een beveiligd, uiterst compatibel verwijderings proces.
* [Wijzig de vSphere-bevoegdheden](escalate-private-cloud-privileges.md). Escaleren uw bevoegdheden op deze AVS-Privécloud.

## <a name="avs-private-cloud-vlanssubnets"></a>Cloud-VLAN'S voor Privécloud/subnetten van AVS

De lijst met gedefinieerde VLAN'S of subnetten weer geven voor de geselecteerde AVS-privécloud. De lijst bevat de Management-VLAN'S/subnetten die zijn gemaakt tijdens het maken van de AVS-privécloud.

![Privécloud/subnetten van de automatische AVS-Cloud](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Beschik bare acties

* [Vlan's/subnetten toevoegen](https://docs.azure.cloudsimple.com/create-vlan-subnet/). Voeg een VLAN/subset toe aan deze Privécloud.

Een VLAN/subnet selecteren voor de volgende acties
* Een [firewall tabel toevoegen](https://docs.azure.cloudsimple.com/firewall/). Een firewall tabel koppelen aan deze Privécloud.
* **Wijzigt**
* **Verwijderen** (alleen door de gebruiker gedefinieerde Vlan's/subnetten)

## <a name="avs-private-cloud-activity"></a>Activiteit voor persoonlijke cloud van AVS

Bekijk de volgende informatie voor de geselecteerde Privécloud in de Cloud. De gegevens van de activiteit zijn een gefilterde lijst met alle activiteiten voor de geselecteerde Privécloud van de Cloud. Op deze pagina worden Maxi maal 25 recente activiteiten weer gegeven.

* Recente waarschuwingen
* Recente gebeurtenissen
* Recente taken
* Recente controle

![Privécloud-activiteit van de automatische AVS](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Cloud racks

Cloud racks zijn de bouw stenen van de Privécloud van uw AVS. Elk rek biedt een capaciteits eenheid. Met AVS worden Cloud racks automatisch geconfigureerd op basis van uw selecties bij het maken of uitbreiden van een nieuwe AVS-Cloud. Bekijk de volledige lijst met Cloud racks, met inbegrip van de Privécloud die elke is toegewezen.

![Privécloud-Cloud racks-AVS](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>vSphere-beheer netwerk

Lijst met VMware-beheer resources en virtuele machines die momenteel zijn geconfigureerd op de AVS-Privécloud. De informatie omvat de software versie, de Fully Qualified Domain Name (FQDN) en het IP-adres van de bronnen.

![VSphere-beheer netwerk voor de automatische AVS-Cloud](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Volgende stappen

* [VMware-Vm's in azure gebruiken](quickstart-create-vmware-virtual-machine.md)
* Meer informatie over [persoonlijke Clouds van AVS](cloudsimple-private-cloud.md)