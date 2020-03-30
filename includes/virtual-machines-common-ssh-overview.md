---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 12/21/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 3f9b86dd3716a25ab95265cf46b616144f57163b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "71168618"
---
## <a name="overview-of-ssh-and-keys"></a>Overzicht van SSH en toetsen

[SSH](https://www.ssh.com/ssh/) is een versleuteld verbindingsprotocol dat veilige aanmeldingen via onbeveiligde verbindingen mogelijk maakt. SSH is het standaardverbindingsprotocol voor Linux VM's dat wordt gehost in Azure. Hoewel SSH zelf een versleutelde verbinding biedt, maakt het gebruik van wachtwoorden met SSH-verbindingen de VM nog steeds kwetsbaar voor brute-force-aanvallen of het raden van wachtwoorden. Een veiligere en voorkeursmethode om verbinding te maken met een VM met SSH is door gebruik te maken van een publiek-private sleutelpaar, ook wel *SSH-sleutels genoemd.* 

* De *openbare sleutel* wordt geplaatst op uw Linux VM, of een andere dienst die u wilt gebruiken met public-key cryptografie.

* De *privésleutel* blijft op uw lokale systeem. Houd deze privésleutel geheim. Deel deze niet.

Wanneer u een SSH-client gebruikt om verbinding te maken met uw Linux-VM (die de openbare sleutel heeft), test de externe VM de client om ervoor te zorgen dat deze over de privésleutel beschikt. Als de client de privésleutel heeft, krijgt deze toegang tot de VM. 

Afhankelijk van het beveiligingsbeleid van uw organisatie u één sleutelpaar met openbaar-privésleutel opnieuw gebruiken om toegang te krijgen tot meerdere Azure VM's en -services. U hebt geen apart paar sleutels nodig voor elke virtuele machine of service die u wilt openen. 

Uw openbare sleutel kan met iedereen worden gedeeld, maar alleen u (of uw lokale beveiligingsinfrastructuur) moet in het bezit zijn van uw privésleutel.