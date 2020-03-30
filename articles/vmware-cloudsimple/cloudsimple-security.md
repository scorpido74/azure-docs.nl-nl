---
title: Azure VMware-oplossing door CloudSimple - Beveiliging voor CloudSimple Services
description: Beschrijft de modellen voor gedeelde verantwoordelijkheid voor de beveiliging van CloudSimple-services
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1a33e20ec540a05885eb13a3828d28ffc9923fff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024990"
---
# <a name="cloudsimple-security-overview"></a>Overzicht van CloudSimple-beveiliging

In dit artikel vindt u een overzicht van hoe beveiliging wordt geïmplementeerd op de Azure VMware-oplossing door cloudsimple-service, -infrastructuur en -datacenter. U leert over gegevensbescherming en -beveiliging, netwerkbeveiliging en hoe kwetsbaarheden en patches worden beheerd.

## <a name="shared-responsibility"></a>Gedeelde verantwoordelijkheid

Azure VMware Solution by CloudSimple maakt gebruik van een gedeeld verantwoordelijkheidsmodel voor beveiliging. Vertrouwde beveiliging in de cloud wordt bereikt door de gedeelde verantwoordelijkheden van klanten en Microsoft als serviceprovider. Deze matrix van verantwoordelijkheid biedt een hogere beveiliging en elimineert single points of failure.

## <a name="azure-infrastructure"></a>Azure-infrastructuur

Azure-infrastructuurbeveiligingsoverwegingen omvatten de locatie van datacenters en apparatuur.

### <a name="datacenter-security"></a>Datacenterbeveiliging

Microsoft heeft een hele divisie gewijd aan het ontwerpen, bouwen en exploiteren van de fysieke faciliteiten die Azure ondersteunen. Dit team investeert in het handhaven van state-of-the-art fysieke veiligheid. Zie [Azure-faciliteiten, lokalen en fysieke beveiliging](../security/azure-physical-security.md)voor meer informatie over fysieke beveiliging.

### <a name="equipment-location"></a>Locatie van apparatuur

De bare metal hardware-apparatuur die uw Private Clouds uitvoert, wordt gehost in Azure-datacenterlocaties.  De kooien waar die apparatuur zich bevindt, vereist biometrische tweefactorauthenticatie om toegang te krijgen.

## <a name="dedicated-hardware"></a>Toegewezen hardware

Als onderdeel van de CloudSimple-service krijgen alle CloudSimple-klanten speciale bare metal-hosts met lokale aangesloten schijven die fysiek geïsoleerd zijn van andere tenanthardware. Een ESXi hypervisor met vSAN draait op elk knooppunt. De nodes worden beheerd via klantgerichte VMware vCenter en NSX. Het niet delen van hardware tussen tenants biedt een extra laag isolatie- en beveiligingsbeveiliging.

## <a name="data-security"></a>Gegevensbeveiliging

Klanten behouden de controle en het eigendom van hun gegevens. Data stewardship van klantgegevens is de verantwoordelijkheid van de klant.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Gegevensbescherming voor gegevens in rust en gegevens in beweging binnen interne netwerken

Voor gegevens in rust in de Private Cloud-omgeving u vSAN-versleuteling gebruiken. vSAN-versleuteling werkt met VMware-gecertificeerde externe key management servers (KMS) in uw eigen virtuele netwerk of on-premises.  U beheert zelf de sleutels van de gegevensversleuteling. Voor gegevens in beweging binnen de Private Cloud ondersteunt vSphere versleuteling van gegevens via de draad voor al het vmkernel-verkeer (inclusief vMotion-verkeer).

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>Gegevensbescherming voor gegevens die nodig zijn om zich via openbare netwerken te verplaatsen

Om gegevens te beschermen die via openbare netwerken worden verplaatst, u IPsec- en SSL VPN-tunnels maken voor uw Private Clouds. Algemene versleutelingsmethoden worden ondersteund, waaronder AES van 128 byte en 256 byte. Onderweg gegevens (inclusief verificatie, administratieve toegang en klantgegevens) worden versleuteld met standaardversleutelingsmechanismen (SSH, TLS 1.2 en Secure RDP). Communicatie die gevoelige informatie vervoert, maakt gebruik van de standaard versleutelingsmechanismen.

### <a name="secure-disposal"></a>Veilige verwijdering

Als uw CloudSimple-service verloopt of wordt beëindigd, bent u verantwoordelijk voor het verwijderen of verwijderen van uw gegevens. CloudSimple zal met u samenwerken om alle klantgegevens zoals die in de klantovereenkomst zijn vastgelegd te verwijderen of terug te sturen, behalve voor zover CloudSimple wettelijk verplicht is om sommige of alle persoonsgegevens te bewaren. Indien nodig om persoonsgegevens te bewaren, zal CloudSimple de gegevens archiveren en redelijke maatregelen nemen om te voorkomen dat de klantgegevens verder worden verwerkt.

### <a name="data-location"></a>Locatie van gegevens

Wanneer u uw Private Clouds instelt, kiest u de Azure-regio waar ze worden geïmplementeerd. VMware virtuele machinegegevens worden niet verplaatst van dat fysieke datacenter, tenzij u gegevensmigratie of offsite data back-up uit te voeren. U ook workloads hosten en gegevens opslaan binnen meerdere Azure-regio's, indien nodig voor uw behoeften.

De klantgegevens die woonachtig zijn in hypergeconvergeerde knooppunten van Private Cloud, doorkruisen geen locaties zonder de expliciete actie van de tenantbeheerder. Het is uw verantwoordelijkheid om uw workloads op een zeer beschikbare manier te implementeren.

### <a name="data-backups"></a>Gegevensback-ups

CloudSimple maakt geen back-ups of archiveert geen klantgegevens. CloudSimple voert wel periodieke back-ups uit van vCenter- en NSX-gegevens om een hoge beschikbaarheid van beheerservers te bieden. Voorafgaand aan de back-up worden alle gegevens versleuteld bij de vCenter-bron met VMware API's. De versleutelde gegevens worden getransporteerd en opgeslagen in Azure blob. Versleutelingssleutels voor back-ups worden opgeslagen in een zeer beveiligde CloudSimple beheerde kluis die wordt uitgevoerd in het virtuele CloudSimple-netwerk in Azure.

## <a name="network-security"></a>Netwerkbeveiliging

De CloudSimple-oplossing is gebaseerd op lagen van netwerkbeveiliging.

### <a name="azure-edge-security"></a>Azure edge-beveiliging

De CloudSimple-services zijn gebouwd bovenop de basisnetwerkbeveiliging van Azure. Azure past beveiligings-in-diepte technieken voor detectie en tijdige reactie op netwerk-gebaseerde aanvallen in verband met afwijkende binnendringen of uitgaande verkeerspatronen en gedistribueerde denial-of-service (DDoS) aanvallen. Deze beveiligingscontrole is van toepassing op Private Cloud-omgevingen en de besturingsvlaksoftware die door CloudSimple is ontwikkeld.

### <a name="segmentation"></a>Segmentatie

De CloudSimple-service heeft logischerwijs afzonderlijke Layer 2-netwerken die de toegang tot uw eigen privénetwerken in uw Private Cloud-omgeving beperken. U uw Private Cloud-netwerken verder beschermen met behulp van een firewall. Met de CloudSimple-portal u ew- en NS-regels voor netwerkverkeerscontroles definiëren voor al het netwerkverkeer, inclusief intra Private Cloud-verkeer, inter-private cloudverkeer, algemeen verkeer naar het internet en netwerkverkeer naar on-premises via IPsec VPN of ExpressRoute-verbinding.

## <a name="vulnerability-and-patch-management"></a>Kwetsbaarheid en patchbeheer

CloudSimple is verantwoordelijk voor het periodiek patchen van beheerde VMware-software (ESXi, vCenter en NSX).

## <a name="identity-and-access-management"></a>Identiteits- en toegangsbeheer

Klanten kunnen zich verifiëren naar hun Azure-account (in Azure AD) met behulp van multi-factor authenticatie of SSO als voorkeur. Vanuit de Azure-portal u de CloudSimple-portal starten zonder referenties opnieuw in te voeren.

CloudSimple ondersteunt optionele configuratie van een identiteitsbron voor het Private Cloud vCenter. U een [on-premises identiteitsbron,](set-vcenter-identity.md)een nieuwe identiteitsbron voor de Private Cloud of [Azure AD](azure-ad.md)gebruiken.

Standaard krijgen klanten de bevoegdheden die nodig zijn voor de dagelijkse activiteiten van vCenter binnen de Private Cloud. Dit machtigingsniveau omvat geen beheerderstoegang tot vCenter. Als beheerderstoegang tijdelijk vereist is, u [uw bevoegdheden](escalate-private-cloud-privileges.md) voor een beperkte periode escaleren terwijl u de beheertaken uitvoert.
