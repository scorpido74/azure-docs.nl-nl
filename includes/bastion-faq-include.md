---
title: bestand opnemen
description: bestand opnemen
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 03/25/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 57a764b62fcda333f042794e176c24c8e6cc5526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80374074"
---
### <a name="which-regions-are-available"></a><a name="regions"></a>Welke regio's zijn beschikbaar?

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="do-i-need-a-public-ip-on-my-virtual-machine"></a><a name="publicip"></a>Heb ik een openbaar IP-adres nodig op mijn virtuele machine?

Wanneer u verbinding maakt met een vm met Azure Bastion, hebt u GEEN openbaar IP-adres nodig op de Azure Virtual Machine waarmee u verbinding maakt. De Bastion-service opent de RDP/SSH-sessie/-verbinding met uw virtuele machine via het privé-IP-adres van uw virtuele machine, binnen uw virtuele netwerk.

### <a name="is-ipv6-supported"></a>Wordt IPv6 ondersteund?

Op dit moment wordt IPv6 niet ondersteund. Azure Bastion ondersteunt alleen IPv4.

### <a name="do-i-need-an-rdp-or-ssh-client"></a><a name="rdpssh"></a>Heb ik een RDP- of SSH-client nodig?

U hebt geen RDP- of SSH-client nodig om toegang te krijgen tot de RDP/SSH tot uw Azure-virtuele machine in uw Azure-portal. Gebruik de [Azure-portal](https://portal.azure.com) om RDP/SSH rechtstreeks in de browser toegang te geven tot uw virtuele machine.

### <a name="does-azure-bastion-require-an-rds-cal-for-administrative-purposes-on-azure-hosted-vms"></a><a name="rdscal"></a>Heeft Azure Bastion een RDS CAL nodig voor administratieve doeleinden op door Azure gehoste VM's?
Nee, toegang tot Windows Server VM's door Azure Bastion vereist geen [RDS CAL](https://www.microsoft.com/en-us/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab) wanneer deze uitsluitend voor administratieve doeleinden wordt gebruikt.

### <a name="how-many-concurrent-rdp-and-ssh-sessions-does-each-azure-bastion-support"></a><a name="limits"></a>Hoeveel gelijktijdige RDP- en SSH-sessies ondersteunt elke Azure Bastion?
Zowel RDP als SSH zijn een op gebruik gebaseerd protocol. Een hoog gebruik van sessies zorgt ervoor dat de bastionhost een lager totaal aantal sessies ondersteunt. De onderstaande getallen gaan uit van normale dagelijkse workflows.

[!INCLUDE [limits](bastion-limits.md)]

### <a name="do-i-need-an-agent-running-in-the-azure-virtual-machine"></a><a name="agent"></a>Heb ik een agent nodig die wordt uitgevoerd in de virtuele Azure-machine?

U hoeft geen agent of software te installeren in uw browser of uw virtuele Azure-machine. De Bastion-service is agentloos en vereist geen extra software voor RDP/SSH.

### <a name="which-browsers-are-supported"></a><a name="browsers"></a>Welke browsers worden ondersteund?

Gebruik de Microsoft Edge-browser of Google Chrome op Windows. Gebruik voor Apple Mac de Google Chrome-browser. Microsoft Edge Chromium wordt ook ondersteund op zowel Windows als Mac, respectievelijk.

### <a name="are-any-roles-required-to-access-a-virtual-machine"></a><a name="roles"></a>Zijn er rollen nodig om toegang te krijgen tot een virtuele machine?

Om een verbinding te maken, zijn de volgende rollen vereist:

* De rol van de lezer op de virtuele machine
* De rol van de lezer op NIC met privé IP van de virtuele machine
* Leesrol op de Azure Bastion-bron

### <a name="what-is-the-pricing"></a><a name="pricingpage"></a>Wat is de prijs?

Zie de pagina [prijzen](https://aka.ms/BastionHostPricing) voor meer informatie.

### <a name="why-do-i-get-your-session-has-expired-error-message-before-the-bastion-session-starts"></a><a name="session"></a>Waarom krijg ik een foutmelding 'Uw sessie is verlopen' voordat de Bastion-sessie begint?

Een sessie mag alleen worden gestart vanuit de Azure-portal. Meld u aan bij de Azure-portal en begin uw sessie opnieuw. Als u rechtstreeks vanaf een andere browsersessie of tabblad naar de URL gaat, wordt deze fout verwacht. Het helpt ervoor te zorgen dat uw sessie veiliger is en dat de sessie alleen toegankelijk is via de Azure-portal.

### <a name="what-keyboard-layouts-are-supported-during-the-bastion-remote-session"></a><a name="keyboard"></a>Welke toetsenbordindelingen worden ondersteund tijdens de bastion-externe sessie?

Azure Bastion ondersteunt momenteel en-us-qwerty toetsenbordlay-out in de VM.  Ondersteuning voor andere landlocaties voor toetsenbordindeling is werk in uitvoering.

### <a name="is-user-defined-routing-udr-supported-on-an-azure-bastion-subnet"></a><a name="udr"></a>Wordt udr-routering (UDR) ondersteund op een Subnet Azure Bastion?

Nee. UDR wordt niet ondersteund op een Subnet Azure Bastion.
Voor scenario's die zowel Azure Bastion als Azure Firewall/Network Virtual Appliance (NVA) in hetzelfde virtuele netwerk bevatten, hoeft u verkeer van een Subnet Azure Bastion niet te forceren naar Azure Firewall omdat de communicatie tussen Azure Bastion en uw VM's privé is. Zie [Toegang tot VM's achter Azure Firewall met Bastion](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/)voor meer informatie.

### <a name="is-file-transfer-supported-with-azure-bastion-rdp-session"></a><a name="filetransfer"></a>Wordt bestandsoverdracht ondersteund met Azure Bastion RDP-sessie?

We werken hard om nieuwe functies toe te voegen. Vanaf nu wordt bestandsoverdracht niet ondersteund, maar maakt het deel uit van onze roadmap. Deel gerust uw feedback over nieuwe functies op de [feedbackpagina van Azure Bastion.](https://feedback.azure.com/forums/217313-networking?category_id=367303)

### <a name="how-do-i-handle-deployment-failures"></a><a name="udr"></a>Hoe ga ik om met implementatiefouten?

Bekijk eventuele foutberichten en verhoog indien nodig [een ondersteuningsverzoek in de Azure Portal.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Implementatiefouten kunnen het gevolg zijn van [Azure-abonnementslimieten, quota's en beperkingen](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits). In het bijzonder kunnen klanten een limiet tegenkomen op het aantal toegestane openbare IP-adressen per abonnement waardoor de Azure Bastion-implementatie mislukt.
