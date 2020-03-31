---
title: Werk rond instellingen voor opslagfirewall
description: Een firewallinstelling voor opslagnetwerken kan fouten veroorzaken bij het maken van een Azure Blob-opslagdoel in Azure HPC-cache. Dit artikel geeft een tijdelijke oplossing voor de beperking totdat er een softwarefix is.
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/7/2019
ms.author: rohogue
ms.openlocfilehash: 6643662d498db8cbcffcb120a9ceabc46cfc04cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74174406"
---
# <a name="work-around-blob-storage-account-firewall-settings"></a>Rond firewallinstellingen van Blob-opslag werken

Een bepaalde instelling die wordt gebruikt in firewalls voor opslagaccounten, kan ertoe leiden dat het maken van uw Blob-opslagdoel mislukt. Het Azure HPC-cacheteam werkt aan een softwareoplossing voor dit probleem, maar u er omheen werken door de instructies in dit artikel te volgen.

De firewall-instelling die alleen toegang van 'geselecteerde netwerken' toestaat, kan voorkomen dat de cache een Blob-opslagdoel maakt. Deze configuratie bevindt zich op de pagina **Firewalls en instellingen voor virtuele netwerken** van het opslagaccount.

Het probleem is dat de cacheservice een verborgen servicevirtueel netwerk gebruikt dat gescheiden is van klantomgevingen. Het is niet mogelijk om dit netwerk expliciet te autoriseren om toegang te krijgen tot uw opslagaccount.

Wanneer u een Blob-opslagdoel maakt, gebruikt de cacheservice dit netwerk om te controleren of de container leeg is. Als de firewall geen toegang toestaat vanuit het verborgen netwerk, mislukt de controle en mislukt het maken van opslagdoel.

Als u het probleem wilt oplossen, wijzigt u tijdelijk uw firewall-instellingen terwijl u het opslagdoel maakt:

1. Ga naar de pagina Firewalls voor opslagaccount **firewalls en virtuele netwerken** en wijzig de instelling 'Toegang toestaan vanaf alle **netwerken.**
1. Maak het Blob-opslagdoel in uw Azure HPC-cache.
1. Nadat het opslagdoel is gemaakt, wijzigt u de firewallinstelling van het account terug naar **Geselecteerde netwerken.**

Azure HPC Cache gebruikt het virtuele netwerk van de service niet om toegang te krijgen tot het doel voor voltooide opslag.

Neem voor hulp bij deze tijdelijke oplossing [contact op met Microsoft Service en Ondersteuning](hpc-cache-support-ticket.md).
