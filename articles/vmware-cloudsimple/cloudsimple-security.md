---
title: Azure VMware-oplossingen (AVS)-beveiliging voor AVS-Services
description: Beschrijft de modellen van gedeelde verantwoordelijkheden voor beveiliging van AVS-Services
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d2d45f827d4165175a4a5429f0b9393a55e2ff1e
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024990"
---
# <a name="avs-security-overview"></a>Overzicht van AVS-beveiliging

Dit artikel bevat een overzicht van de manier waarop beveiliging wordt geïmplementeerd op de Azure VMware-oplossing door de AVS-service, infra structuur en het Data Center. Meer informatie over gegevens beveiliging en beveiliging, netwerk beveiliging en hoe beveiligings problemen en patches worden beheerd.

## <a name="shared-responsibility"></a>Gedeelde verantwoordelijkheid

De Azure VMware-oplossing per AVS gebruikt een gedeeld verantwoordelijkheids model voor beveiliging. Vertrouwde beveiliging in de Cloud wordt bereikt door de gedeelde verantwoordelijkheden van klanten en micro soft als een service provider. Deze matrix van verantwoordelijkheid biedt betere beveiliging en elimineert individuele storings punten.

## <a name="azure-infrastructure"></a>Azure-infrastructuur

Beveiligings overwegingen voor Azure-infra structuur omvatten de data centers en de locatie van het apparaat.

### <a name="datacenter-security"></a>Beveiliging van data centers

Micro soft heeft een volledige afdeling voor het ontwerpen, bouwen en gebruiken van de fysieke faciliteiten die ondersteuning bieden voor Azure. Dit team is geïnvesteerd in het onderhouden van geavanceerde fysieke beveiliging. Zie [Azure-faciliteiten,-premises en fysieke beveiliging](../security/azure-physical-security.md)voor meer informatie over de fysieke beveiliging.

### <a name="equipment-location"></a>Locatie van apparatuur

De bare-metal hardware die uw AVS-persoonlijke Clouds uitvoert, wordt gehost op Azure Data Center-locaties. De kooien waar dat apparaat is, vereist biometrische twee ledige verificatie om toegang te krijgen.

## <a name="dedicated-hardware"></a>Toegewezen hardware

Als onderdeel van de AVS-service krijgen alle AVS-klanten toegewezen bare-metal hosts met lokale gekoppelde schijven die fysiek zijn geïsoleerd van andere Tenant-hardware. Een ESXi-Hyper Visor met vSAN wordt uitgevoerd op elk knoop punt. De knoop punten worden beheerd via een door de klant toegewezen VMware vCenter-en NSX. Het delen van hardware tussen tenants biedt geen extra laag voor isolatie en beveiliging.

## <a name="data-security"></a>Databeveiliging

Klanten houden toezicht en eigendom van hun gegevens. Gegevens opslag van klant gegevens is de verantwoordelijkheid van de klant.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Gegevens beveiliging voor gegevens in rust tijd en gegevens in beweging in interne netwerken

Voor Data-at-rest in de cloud omgeving van de AVS kunt u vSAN-versleuteling gebruiken. vSAN-versleuteling werkt met VMware Certified External Key Management servers (KMS) in uw eigen virtuele netwerk of on-premises. U beheert de gegevens versleutelings sleutels zelf. Voor gegevens in beweging in de Privécloud van de AVS ondersteunt vSphere versleuteling van gegevens via de kabel voor al het vmkernel-verkeer (inclusief vMotion-verkeer).

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>Gegevens beveiliging voor gegevens die nodig zijn om te scha kelen via open bare netwerken

Voor het beveiligen van gegevens die via open bare netwerken worden verplaatst, kunt u IPsec-en SSL-VPN-tunnels maken voor uw AVS-persoonlijke Clouds. Algemene versleutelings methoden worden ondersteund, met inbegrip van 128-byte en 256-bytes AES. Gegevens die onderweg zijn (inclusief verificatie, beheerders toegang en klant gegevens) worden versleuteld met standaard versleutelings mechanismen (SSH, TLS 1,2 en Secure RDP). Communicatie waarbij gevoelige informatie wordt getransporteerd, maakt gebruik van de standaard versleutelings mechanismen.

### <a name="secure-disposal"></a>Beveiligde verwijdering

Als uw AVS-service verloopt of wordt beëindigd, bent u verantwoordelijk voor het verwijderen of verwijderen van uw gegevens. AVS werkt samen met u bij het verwijderen of retour neren van alle klant gegevens zoals die zijn opgegeven in de klant overeenkomst, behalve wanneer AVS wordt vereist door het toepasselijk recht om enkele of alle persoons gegevens te bewaren. Wanneer dit nodig is om persoonlijke gegevens te bewaren, worden de gegevens door AVS gearchiveerd en worden er redelijke maat regelen geïmplementeerd om te voor komen dat de klant gegevens worden verwerkt.

### <a name="data-location"></a>Gegevenslocatie

Wanneer u persoonlijke Clouds van uw AVS instelt, kiest u de Azure-regio waar ze worden geïmplementeerd. Virtuele VMware-machine gegevens worden niet verplaatst van dat fysieke Data Center tenzij u gegevens migratie of externe gegevens back-up uitvoert. U kunt werk belastingen ook hosten en gegevens opslaan in meerdere Azure-regio's, indien van toepassing op uw behoeften.

De klant gegevens die zich in de open bare AVS van de Privécloud bevinden, Hyper-geconvergeerde knoop punten passeren geen locaties zonder de expliciete actie van de Tenant beheerder. Het is uw verantwoordelijkheid om uw workloads op een Maxi maal beschik bare manier te implementeren.

### <a name="data-backups"></a>Gegevens back-ups

AVS maakt geen back-ups van klant gegevens en archiveert deze niet. AVS voert periodieke back-ups van vCenter-en NSX-gegevens uit om hoge Beschik baarheid van beheerser vers te bieden. Voordat de back-up wordt gemaakt, worden alle gegevens versleuteld op de vCenter-bron met behulp van VMware-Api's. De versleutelde gegevens worden getransporteerd en opgeslagen in Azure Blob. Versleutelings sleutels voor back-ups worden opgeslagen in een zeer veilige AVS Managed kluis die wordt uitgevoerd in het virtuele netwerk van de AVS in Azure.

## <a name="network-security"></a>Netwerkbeveiliging

De AVS-oplossing is afhankelijk van lagen van netwerk beveiliging.

### <a name="azure-edge-security"></a>Beveiliging van Azure Edge

De AVS-Services zijn gebaseerd op de basis netwerk beveiliging die wordt geboden door Azure. Azure past ingrijpende technieken toe voor detectie en tijdig antwoord op aanvallen op basis van het netwerk die zijn gekoppeld aan afwijkende ingangen of uitvoerige verkeers patronen en gedistribueerde Denial-of-service-aanvallen (DDoS). Dit beveiligings beheer is van toepassing op de AVS-Cloud omgevingen en de beheer vlak software die is ontwikkeld door AVS.

### <a name="segmentation"></a>Segmentering

De AVS-service heeft logische afzonderlijke laag 2-netwerken die de toegang tot uw eigen particuliere netwerken in uw cloud omgeving voor Privécloud beperken. U kunt uw automatische AVS-Cloud netwerken verder beveiligen met een firewall. Via de AVS-Portal kunt u regels voor het uitvoeren van ieuwe en NS-netwerk verkeer instellen voor alle netwerk verkeer, waaronder intra AVS Private Cloud Traffic, interavs Private Cloud Traffic, algemeen verkeer naar Internet en netwerk verkeer naar on-premises via IPsec VPN of ExpressRoute-verbinding.

## <a name="vulnerability-and-patch-management"></a>Beveiligings problemen en patch beheer

AVS is verantwoordelijk voor periodieke beveiligings patches van beheerde VMware-software (ESXi, vCenter en NSX).

## <a name="identity-and-access-management"></a>Identiteits- en toegangsbeheer

Klanten kunnen zich verifiëren bij hun Azure-account (in azure AD) met behulp van multi-factor Authentication of SSO als voor keur. Vanuit het Azure Portal kunt u de AVS-Portal starten zonder referenties opnieuw in te voeren.

AVS ondersteunt een optionele configuratie van een identiteits bron voor de AVS Privécloud-vCenter. U kunt een [on-premises identiteits bron](set-vcenter-identity.md), een nieuwe identiteits bron voor de Privécloud of [Azure AD](azure-ad.md)gebruiken.

Klanten krijgen standaard de bevoegdheden die nodig zijn voor de dagelijkse bewerkingen van de vCenter in de Privécloud van de AVS. Dit machtigings niveau bevat geen beheerders toegang tot vCenter. Als beheerders toegang tijdelijk vereist is, kunt u [uw bevoegdheden](escalate-private-cloud-privileges.md) voor een beperkte periode escaleren tijdens het volt ooien van de beheer taken.
