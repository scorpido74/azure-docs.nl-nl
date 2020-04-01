---
title: Beveiligingsverharding in virtuele AKS-machinehosts
description: Meer informatie over de beveiligingsverharding in AKS VM-hostbesturingssysteem
services: container-service
author: mlearned
ms.topic: article
ms.date: 09/11/2019
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: b7552fc083c5ed340dc54c2a31160b0c8b4bd076
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420906"
---
# <a name="security-hardening-for-aks-agent-node-host-os"></a>Beveiliging verharding voor AKS agent node host OS

Azure Kubernetes Service (AKS) is een beveiligde service die voldoet aan SOC-, ISO-, PCI DSS- en HIPAA-standaarden. Dit artikel behandelt de beveiligingsverharding toegepast op AKS virtuele machine hosts. Zie [Beveiligingsconcepten voor toepassingen en clusters in Azure Kubernetes Service (AKS) voor](https://docs.microsoft.com/azure/aks/concepts-security)meer informatie over AKS-beveiliging.

> [!Note]
> Dit document is alleen bedoeld voor Linux-agents in AKS.

AKS-clusters worden geïmplementeerd op virtuele hostmachines, die een beveiligingsgeoptimaliseerd besturingssysteem uitvoeren dat wordt gebruikt voor containers die op AKS worden uitgevoerd. Dit host-besturingssysteem is gebaseerd op een **Ubuntu 16.04.LTS-afbeelding** met extra beveiligingsverharding en optimalisaties toegepast (zie Details voor verharding van beveiliging).

Het doel van het beveiligde geharde host-besturingssysteem is om het oppervlak van de aanval te verminderen en te optimaliseren voor de inzet van containers op een veilige manier.

> [!Important]
> De beveiliging gehard OS is niet CIS benchmarked. Hoewel er overlappingen zijn met CIS-benchmarks, is het doel niet om CIS-compatibel te zijn. Het doel voor host OS verharding is om te convergeren op een beveiligingsniveau dat in overeenstemming is met microsoft's eigen interne host beveiligingsstandaarden.

## <a name="security-hardening-features"></a>Functies voor beveiligingsverharding

* AKS biedt standaard een beveiligingsgeoptimaliseerd hostbesturingssysteem. Er is geen optie om een alternatief besturingssysteem te selecteren.

* Azure past dagelijkse patches (inclusief beveiligingspatches) toe op virtuele machinehosts van AKS. Sommige van deze patches vereisen een reboot, terwijl anderen dat niet zullen doen. U bent verantwoordelijk voor het plannen van AKS VM-host reboots indien nodig. Voor richtlijnen over het automatiseren van AKS-patching zie [patchen AKS-knooppunten.](https://docs.microsoft.com/azure/aks/node-updates-kured)

## <a name="what-is-configured"></a>Wat is geconfigureerd

| Gos  | Beschrijving van de controle|
|---|---|
| 1.1.1.1 |Zorg ervoor dat de montage van cramfs filesystems is uitgeschakeld|
| 1.1.1.2 |Zorg ervoor dat de montage van freevxfs filesystems is uitgeschakeld|
| 1.1.1.3 |Zorg ervoor dat de montage van jffs2-bestandssystemen is uitgeschakeld|
| 1.1.1.4 |Zorg ervoor dat de montage van HFS-bestandssystemen is uitgeschakeld|
| 1.1.1.5 |Zorg ervoor dat de montage van HFS Plus-bestandssystemen is uitgeschakeld|
|1.4.3 |Verificatie controleren die vereist is voor de modus voor één gebruiker |
|1.7.1.2 |Ervoor zorgen dat de waarschuwingbanner voor lokale aanmelding correct is geconfigureerd |
|1.7.1.3 |Ervoor zorgen dat de waarschuwingsbanner voor extern inloggen correct is geconfigureerd |
|1.7.1.5 |Ervoor zorgen dat machtigingen voor /etc/issue zijn geconfigureerd |
|1.7.1.6 |Ervoor zorgen dat machtigingen op /etc/issue.net zijn geconfigureerd |
|2.1.5 |Ervoor zorgen dat --streaming-connection-idle-time-out niet is ingesteld op 0 |
|3.1.2 |Ervoor zorgen dat het verzenden van pakketten is uitgeschakeld |
|3.2.1 |Ervoor zorgen dat bronrouteerde pakketten niet worden geaccepteerd |
|3.2.2 |Ervoor zorgen dat ICMP-omleidingen niet worden geaccepteerd |
|3.2.3 |Zorg ervoor dat beveiligde ICMP-omleidingen niet worden geaccepteerd |
|3.2.4 |Zorg ervoor dat verdachte pakketten worden geregistreerd |
|3.3.1 |Ervoor zorgen dat IPv6-routeradvertenties niet worden geaccepteerd |
|3.5.1 |Ervoor zorgen dat DCCP is uitgeschakeld |
|3.5.2 |Ervoor zorgen dat SCTP is uitgeschakeld |
|3.5.3 |Ervoor zorgen dat RDS is uitgeschakeld |
|3.5.4 |Ervoor zorgen dat TIPC is uitgeschakeld |
|4.2.1.2 |Controleren of logboekregistratie is geconfigureerd |
|5.1.2 |Machtigingen op /etc/crontab zijn geconfigureerd |
|5.2.4 |Ervoor zorgen dat SSH X11 forwarding is uitgeschakeld |
|5.2.5 |Ervoor zorgen dat SSH MaxAuthTries is ingesteld op 4 of minder |
|5.2.8 |Ervoor zorgen dat SSH root login is uitgeschakeld |
|5.2.10 |Ervoor zorgen dat SSH PermitUserEnvironment is uitgeschakeld |
|5.2.11 |Zorg ervoor dat alleen goedgekeurde MAX-algoritmen worden gebruikt |
|5.2.12 |Ervoor zorgen dat SSH Idle Timeout-interval is geconfigureerd |
|5.2.13 |Zorg ervoor dat SSH LoginGraceTime is ingesteld op één minuut of minder |
|5.2.15 |Ervoor zorgen dat ssh-waarschuwingsbanner is geconfigureerd |
|5.3.1 |Ervoor zorgen dat vereisten voor het maken van wachtwoorden zijn geconfigureerd |
|5.4.1.1 |Ervoor zorgen dat het verlopen van het wachtwoord 90 dagen of minder is |
|5.4.1.4 |Zorg ervoor dat inactieve wachtwoordvergrendeling 30 dagen of minder is |
|5.4.4 |Ervoor zorgen dat standaardgebruikersmasker 027 of restrictiever is |
|5.6 |Ervoor zorgen dat de toegang tot de su-opdracht is beperkt|

## <a name="additional-notes"></a>Aanvullende opmerkingen
 
* Om het aanvalsoppervlak verder te verminderen, zijn sommige onnodige stuurprogramma's voor kernelmodules uitgeschakeld in het besturingssysteem.

* Het beveiligingsgeharde besturingssysteem is speciaal gebouwd en onderhouden voor AKS en wordt NIET buiten het AKS-platform ondersteund.

## <a name="next-steps"></a>Volgende stappen  

Zie de volgende artikelen voor meer informatie over AKS-beveiliging: 

[Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/intro-kubernetes)

[AKS-beveiligingsoverwegingen](https://docs.microsoft.com/azure/aks/concepts-security)

[AKS best practices](https://docs.microsoft.com/azure/aks/best-practices)
