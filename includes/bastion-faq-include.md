---
title: bestand opnemen
description: bestand opnemen
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 05/04/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: efb32631c5ee1eedece6d2a06b94702b602ed418
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86276108"
---
### <a name="which-regions-are-available"></a><a name="regions"></a>Welke regio's zijn er beschikbaar?

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="do-i-need-a-public-ip-on-my-virtual-machine"></a><a name="publicip"></a>Heb ik een openbaar IP-adres nodig op mijn virtuele machine?

Wanneer u verbinding maakt met een virtuele machine met behulp van Azure Bastion, hebt u geen openbaar IP-adres nodig op de virtuele machine van Azure waarmee u verbinding maakt. De Bastion-service opent de RDP/SSH-sessie/verbinding met uw virtuele machine via het privé-IP-adres van uw virtuele machine in het virtuele netwerk.

### <a name="is-ipv6-supported"></a>Wordt IPv6 ondersteund?

Op dit moment wordt IPv6 niet ondersteund. Azure Bastion ondersteunt alleen IPv4.

### <a name="do-i-need-an-rdp-or-ssh-client"></a><a name="rdpssh"></a>Heb ik een RDP-of SSH-client?

U hebt geen RDP-of SSH-client nodig om toegang te krijgen tot de RDP/SSH-verbinding met uw virtuele Azure-machine in uw Azure Portal. Met de [Azure Portal](https://portal.azure.com) kunt u RDP/SSH-toegang tot uw virtuele machine rechtstreeks in de browser krijgen.

### <a name="do-i-need-an-agent-running-in-the-azure-virtual-machine"></a><a name="agent"></a>Heb ik een agent nodig die wordt uitgevoerd op de virtuele machine van Azure?

U hoeft geen agent of software in uw browser of uw Azure virtual machine te installeren. De Bastion-service is zonder agent en er is geen aanvullende software voor RDP/SSH vereist.

### <a name="how-many-concurrent-rdp-and-ssh-sessions-does-each-azure-bastion-support"></a><a name="limits"></a>Hoeveel gelijktijdige RDP-en SSH-sessies worden elk ondersteund door Azure Bastion?

Zowel RDP als SSH zijn een protocol op basis van gebruik. Een hoog gebruik van sessies zorgt ervoor dat de bastion-host een lager totaal aantal sessies ondersteunt. De onderstaande nummers gaan uitgaan van de normale dagelijkse werk stromen.

[!INCLUDE [limits](bastion-limits.md)]

### <a name="what-features-are-supported-in-an-rdp-session"></a><a name="rdpfeaturesupport"></a>Welke functies worden ondersteund in een RDP-sessie?

Op dit moment wordt alleen tekst kopiëren/plakken ondersteund. Functies zoals het kopiëren van bestanden worden niet ondersteund. U kunt uw feedback over nieuwe functies op de [Azure Bastion-feedback pagina](https://feedback.azure.com/forums/217313-networking?category_id=367303)delen.

### <a name="which-browsers-are-supported"></a><a name="browsers"></a>Welke browsers worden ondersteund?

Gebruik de micro soft Edge-browser of Google Chrome in Windows. Voor Apple Mac gebruikt u Google Chrome-browser. Chroom van micro soft Edge wordt ook ondersteund in respectievelijk Windows en Mac.

### <a name="where-does-azure-bastion-store-customer-data"></a><a name="data"></a>Waar worden klant gegevens opgeslagen in azure Bastion?

Azure Bastion verplaatst of opslaat geen klant gegevens uit de regio waarin deze is geïmplementeerd.

### <a name="are-any-roles-required-to-access-a-virtual-machine"></a><a name="roles"></a>Zijn er rollen vereist voor toegang tot een virtuele machine?

De volgende rollen zijn vereist om een verbinding te maken:

* Rol van lezer op de virtuele machine
* De rol van lezer op de NIC met het privé-IP-adres van de virtuele machine
* Rol van lezer in de Azure Bastion-resource

### <a name="what-is-the-pricing"></a><a name="pricingpage"></a>Wat zijn de prijzen?

Zie de pagina [prijzen](https://aka.ms/BastionHostPricing) voor meer informatie.

### <a name="does-azure-bastion-require-an-rds-cal-for-administrative-purposes-on-azure-hosted-vms"></a><a name="rdscal"></a>Is voor Azure Bastion een RDS CAL vereist voor beheer doeleinden op Vm's die door Azure worden gehost?
Nee, voor toegang tot Windows Server-Vm's door Azure Bastion is geen [RDS CAL](https://www.microsoft.com/en-us/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab) vereist wanneer uitsluitend voor administratieve doel einden wordt gebruikt.

### <a name="what-keyboard-layouts-are-supported-during-the-bastion-remote-session"></a><a name="keyboard"></a>Welke toetsenbord indelingen worden ondersteund tijdens de externe sessie van Bastion?

Azure Bastion ondersteunt momenteel de toetsenbord indeling en-US-QWERTY in de VM.  Ondersteuning voor andere land instellingen voor toetsenbord indeling wordt uitgevoerd.

### <a name="is-user-defined-routing-udr-supported-on-an-azure-bastion-subnet"></a><a name="udr"></a>Wordt door de gebruiker gedefinieerde route ring (UDR) ondersteund op een Azure Bastion-subnet?

Nee. UDR wordt niet ondersteund in een Azure Bastion-subnet.
Voor scenario's met zowel Azure Bastion als Azure Firewall/netwerk virtueel apparaat (NVA) in hetzelfde virtuele netwerk, hoeft u geen verkeer van een Azure Bastion-subnet af te dwingen naar Azure Firewall omdat de communicatie tussen Azure Bastion en uw virtuele machines privé is. Zie [toegang tot vm's achter Azure Firewall met Bastion](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/)voor meer informatie.

### <a name="why-do-i-get-your-session-has-expired-error-message-before-the-bastion-session-starts"></a><a name="session"></a>Waarom krijg ik het fout bericht ' uw sessie is verlopen ' voordat de Bastion-sessie wordt gestart?

Een sessie moet alleen worden gestart vanuit de Azure Portal. Meld u aan bij de Azure Portal en start de sessie opnieuw. Als u rechtstreeks vanuit een andere browser sessie of tabblad naar de URL gaat, wordt deze fout verwacht. Het helpt ervoor te zorgen dat uw sessie veiliger is en dat de sessie alleen via de Azure Portal kan worden geopend.

### <a name="how-do-i-handle-deployment-failures"></a><a name="udr"></a>Implementatie fouten Hoe kan ik verwerken?

Bekijk eventuele fout berichten en [verhoog zo nodig een ondersteunings aanvraag in de Azure Portal](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) . Implementatie fouten kunnen het gevolg [zijn van limieten, quota's en beperkingen voor Azure-abonnementen](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits). Klanten kunnen met name een limiet voor het aantal open bare IP-adressen hebben die zijn toegestaan per abonnement dat ervoor zorgt dat de implementatie van Azure Bastion mislukt.
