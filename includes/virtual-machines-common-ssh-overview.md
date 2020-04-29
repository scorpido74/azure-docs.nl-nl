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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "71168618"
---
## <a name="overview-of-ssh-and-keys"></a>Overzicht van SSH en sleutels

[SSH](https://www.ssh.com/ssh/) is een versleuteld verbindings protocol waarmee beveiligde aanmeldingen via niet-beveiligde verbindingen worden toegestaan. SSH is het standaard verbindings protocol voor Linux-Vm's die worden gehost in Azure. Hoewel SSH zelf een versleutelde verbinding biedt, zorgt het gebruik van wacht woorden met SSH-verbindingen ervoor dat de virtuele machine nog steeds kwetsbaar is voor zwakke aanvallen of het raden van wacht woorden. Een veiligere en voorkeurs methode voor het maken van verbinding met een virtuele machine met SSH is door gebruik te maken van een openbaar-persoonlijk sleutel paar, ook wel *SSH-sleutels*genoemd. 

* De *open bare sleutel* wordt op uw virtuele Linux-machine geplaatst of op een andere service die u wilt gebruiken met crypto grafie met een open bare sleutel.

* De *persoonlijke sleutel* blijft op het lokale systeem. Houd deze privésleutel geheim. Deel deze niet.

Wanneer u een SSH-client gebruikt om verbinding te maken met uw virtuele Linux-machine (die beschikt over de open bare sleutel), test de externe VM de client om te controleren of deze de persoonlijke sleutel bezit. Als de client de persoonlijke sleutel heeft, wordt de toegang tot de virtuele machine verleend. 

Afhankelijk van het beveiligings beleid van uw organisatie, kunt u één enkel openbaar persoonlijk sleutel paar gebruiken om toegang te krijgen tot meerdere Azure-Vm's en-services. U hebt geen afzonderlijk paar sleutels nodig voor elke VM of service die u wilt gebruiken. 

Uw open bare sleutel kan worden gedeeld met iedereen, maar alleen u (of uw lokale beveiligings infrastructuur) moet uw persoonlijke sleutel hebben.