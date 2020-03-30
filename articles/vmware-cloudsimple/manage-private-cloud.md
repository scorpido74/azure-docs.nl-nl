---
title: Azure VMware-oplossing beheren door CloudSimple Private Cloud
description: Beschrijft de mogelijkheden die beschikbaar zijn voor het beheren van uw CloudSimple Private Cloud-bronnen en -activiteiten
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 620c0226d3aca907352658ebbe1b94c7673d91cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014824"
---
# <a name="manage-private-cloud-resources-and-activity"></a>Private Cloud-bronnen en -activiteiten beheren

Private clouds worden beheerd vanuit cloudsimple portal.  Controleer de status, beschikbare resources, activiteit in de privécloud en andere instellingen via de CloudSimple-portal.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op .

## <a name="access-the-cloudsimple-portal"></a>Toegang tot de CloudSimple-portal

Toegang tot de [CloudSimple-portal](access-cloudsimple-portal.md).

## <a name="view-the-list-of-private-clouds"></a>Bekijk de lijst met privéwolken

Op het tabblad **Privéwolken** op de pagina **Resources** worden alle privéwolken in uw abonnement weergegeven. Informatie omvat de naam, het aantal vSphere-clusters, locatie, de huidige status van de private cloud en brongegevens.

![Privécloudpagina](media/manage-private-cloud.png)

Selecteer een Private Cloud voor meer informatie en acties.

## <a name="private-cloud-summary"></a>Samenvatting van de private cloud

Bekijk een uitgebreid overzicht van de geselecteerde Private Cloud.  Overzichtspagina bevat de DNS-servers die zijn geïmplementeerd in de Private Cloud.  U DNS-forwarding instellen van on-premises DNS-servers naar uw Private Cloud DNS-servers.  Zie DNS configureren voor naamomzetting voor Private Cloud vCenter configureren voor meer informatie over [DNS-forwarding van on-premises](https://docs.azure.cloudsimple.com/on-premises-dns-setup/).

![Samenvatting van de privécloud](media/private-cloud-summary.png)

### <a name="available-actions"></a>Beschikbare acties

* [VSphere-client starten](https://docs.azure.cloudsimple.com/vsphere-access/). Toegang tot het vCenter voor deze Private Cloud.
* [Inkoopknooppunten](create-nodes.md). Voeg knooppunten toe aan deze Private Cloud.
* [Uitvouwen](expand-private-cloud.md). Voeg knooppunten toe aan deze Private Cloud.
* **Vernieuwen**. Werk de informatie op deze pagina bij.
* **Verwijderen**. U de Private Cloud op elk gewenst moment verwijderen. **Voordat u het verwijderen uitbrengt, moet u ervoor zorgen dat u een back-up hebt gemaakt van alle systemen en gegevens.** Als u een privécloud verwijdert, worden alle VM's, vCenter-configuratie en gegevens verwijderd. Klik **op Verwijderen** in de sectie overzicht voor de geselecteerde private cloud. Na verwijdering worden alle Private Cloud-gegevens gewist in een beveiligd, zeer compatibel verwijderingsproces.
* [VSphere-bevoegdheden wijzigen](escalate-private-cloud-privileges.md).  Escaleer uw privileges op deze Private Cloud.

## <a name="private-cloud-vlanssubnets"></a>Private Cloud VLANS/subnetten

Bekijk de lijst met gedefinieerde VLAN's/subnetten voor de geselecteerde Private Cloud.  De lijst bevat het beheer VAN VLAN's/subnetten die zijn gemaakt toen de private cloud werd gemaakt.

![Private Cloud - VLAN's/Subnetten](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Beschikbare acties

* [VLANS/Subnetten toevoegen](https://docs.azure.cloudsimple.com/create-vlan-subnet/). Voeg een VLAN/subset toe aan deze Private Cloud.

Een VLAN/Subnet selecteren voor volgende acties
* [Firewalltabel bijvoegen](https://docs.azure.cloudsimple.com/firewall/). Voeg een firewalltabel toe aan deze Private Cloud.
* **Bewerken**
* **Verwijderen** (alleen door de gebruiker gedefinieerde VLAN's/Subnetten)

## <a name="private-cloud-activity"></a>Private Cloud-activiteit

Bekijk de volgende informatie voor de geselecteerde Private Cloud.  De activiteitsgegevens zijn een gefilterde lijst met alle activiteiten voor de geselecteerde Private Cloud.  Deze pagina toont maximaal 25 recente activiteiten.

* Recente waarschuwingen
* Recente gebeurtenissen
* Recente taken
* Recente controle

![Private Cloud - Activiteit](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Cloudracks

Cloudracks zijn de bouwstenen van uw Private Cloud. Elk rek biedt een eenheid van capaciteit. CloudSimple configureert automatisch cloudracks op basis van uw selecties bij het maken of uitbreiden van een Private Cloud.  Bekijk de volledige lijst met cloudracks, inclusief de Private Cloud waaraan elk is toegewezen.

![Private Cloud - Cloud Racks](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>vSphere-beheernetwerk

Lijst met VMware-beheerbronnen en virtuele machines die momenteel zijn geconfigureerd in de Private Cloud. Informatie omvat de softwareversie, volledig gekwalificeerde domeinnaam (FQDN) en IP-adres van de bronnen.

![Private Cloud - vSphere Management Network](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Volgende stappen

* [VMware-VM's in Azure gebruiken](quickstart-create-vmware-virtual-machine.md)
* Meer informatie over [Private Clouds](cloudsimple-private-cloud.md)