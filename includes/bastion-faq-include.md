---
title: bestand opnemen
description: bestand opnemen
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b4e479405a9606a8353785828d0c9c94ef8c32ed
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73850324"
---
### <a name="regions"></a>Welke regio's zijn er beschikbaar?

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="publicip"></a>Heb ik een openbaar IP-adres nodig op mijn virtuele machine?

U hebt geen openbaar IP-adres nodig op de virtuele Azure-machine waarmee u verbinding maakt met de Azure Bastion-service. De Bastion-service opent de RDP/SSH-sessie/verbinding met uw virtuele machine via het privé-IP-adres van uw virtuele machine in het virtuele netwerk.

### <a name="rdpssh"></a>Heb ik een RDP-of SSH-client?

U hebt geen RDP- of SSH-client nodig voor RDP/SSH-toegang tot uw virtuele Azure-machine in Azure Portal. Met de [Azure Portal](https://portal.azure.com) kunt u RDP/SSH-toegang tot uw virtuele machine rechtstreeks in de browser krijgen.

### <a name="agent"></a>Heb ik een agent nodig die wordt uitgevoerd op de virtuele machine van Azure?

U hoeft geen agent of aanvullende software te installeren in uw browser of op uw virtuele Azure-machine. De Bastion-service werkt zonder agent en vereist geen aanvullende software voor RDP/SSH.

### <a name="browsers"></a>Welke browsers worden ondersteund?

Gebruik de micro soft Edge-browser of Google Chrome in Windows. Gebruik voor de Apple Mac de browser Google Chrome. Microsoft Edge Chromium wordt ook ondersteund in Windows en op de Mac.

### <a name="roles"></a>Zijn er rollen vereist voor toegang tot een virtuele machine?

De volgende rollen zijn vereist om een verbinding te maken:

* Rol van lezer op de virtuele machine
* De rol van lezer op de NIC met het privé-IP-adres van de virtuele machine
* Rol van lezer in de Azure Bastion-resource

### <a name="pricingpage"></a>Wat zijn de prijzen?

Zie de pagina [prijzen](https://aka.ms/BastionHostPricing) voor meer informatie.

### <a name="session"></a>Waarom krijg ik het fout bericht ' uw sessie is verlopen ' voordat de Bastion-sessie wordt gestart?

Een sessie moet alleen worden gestart vanuit de Azure Portal. Meld u aan bij de Azure Portal en start de sessie opnieuw. Als u rechtstreeks vanuit een andere browser sessie of tabblad naar de URL gaat, wordt deze fout verwacht. Het helpt ervoor te zorgen dat uw sessie veiliger is en dat de sessie alleen via de Azure Portal kan worden geopend.

### <a name="keyboard"></a>Welke toetsenbord indelingen worden ondersteund tijdens de externe sessie van Bastion?

Azure Bastion ondersteunt momenteel de toetsenbord indeling en-US-QWERTY in de VM.  Ondersteuning voor andere land instellingen voor toetsenbord indeling wordt uitgevoerd.

