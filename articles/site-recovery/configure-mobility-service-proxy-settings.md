---
title: Proxy-instellingen voor mobiliteitsservice voor Azure configureren voor Azure Disaster Recovery | Microsoft Documenten
description: Hier vindt u informatie over het configureren van de mobiliteitsservice wanneer klanten een proxy gebruiken in hun bronomgeving.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2020
ms.author: sideeksh
ms.openlocfilehash: 3d33b5a89a718a41e5c547551f6e7eb4f7033a63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503125"
---
# <a name="configure-mobility-service-proxy-settings-for-azure-to-azure-disaster-recovery"></a>Proxy-instellingen voor Mobility Service voor Azure configureren voor Azure- disaster recovery

In dit artikel vindt u richtlijnen voor het aanpassen van netwerkconfiguraties op de virtuele doelmachine (VM) van Azure wanneer u Azure VM's van de ene regio naar het andere repliceert en herstelt met [Azure Site Recovery](site-recovery-overview.md).

Het doel van dit document is om stappen te bieden om proxy-instellingen voor Azure Site Recovery Mobility Service te configureren in het Azure naar Azure Disaster Recovery-scenario. 

Proxy's zijn netwerkgateways die netwerkverbindingen met eindpunten toestaan/weigeren. Meestal is een proxy een machine buiten de clientmachine die toegang probeert te krijgen tot netwerkeindpunten. Een bypass lijst kan de client om verbindingen rechtstreeks met de eindpunten te maken zonder te gaan via de proxy. Een gebruikersnaam en wachtwoord kunnen optioneel worden ingesteld voor een proxy door netwerkbeheerders, zodat alleen geverifieerde clients proxy kunnen gebruiken. 

## <a name="before-you-start"></a>Voordat u begint

Meer informatie over hoe siteherstel voor [dit scenario](azure-to-azure-architecture.md)een rampherstel biedt.
Inzicht krijgen in de [netwerkrichtlijnen](azure-to-azure-about-networking.md) wanneer u Azure VM's van de ene regio naar de andere repliceert en herstelt met [Azure Site Recovery.](site-recovery-overview.md)
Zorg ervoor dat uw proxy op de juiste manier is ingesteld op basis van de behoeften van uw organisatie.

## <a name="configure-the-mobility-service"></a>De Mobiliteitsservice configureren

Mobility Service ondersteunt alleen niet-geverifieerde proxy's. Het biedt twee manieren om proxydetails in te voeren voor communicatie met eindpunten voor siteherstel. 

### <a name="method-1-auto-detection"></a>Methode 1: Automatische detectie

Mobility Service detecteert automatisch de proxy-instellingen vanuit omgevingsinstellingen of IE-instellingen (Alleen Windows) tijdens het inschakelen van replicatie. 

- Windows OS: Tijdens Replicatie inschakelen detecteert Mobility Service de proxy-instellingen zoals geconfigureerd in Internet Explorer voor de gebruiker van lokaal systeem. Als u proxy voor lokaal systeemaccount wilt instellen, kan een beheerder psexec gebruiken om een opdrachtprompt te starten en vervolgens Internet Explorer. 
- Windows OS: Proxy-instellingen zijn geconfigureerd als omgevingsvariabelen http_proxy en no_proxy. 
- Linux OS: Proxy-instellingen zijn geconfigureerd in /etc/profiel of /etc/omgeving als omgevingsvariabelen http_proxy, no_proxy. 
- De automatisch gedetecteerde proxy-instellingen worden opgeslagen in Mobility Service proxy config bestand ProxyInfo.conf 
- Standaardlocatie van ProxyInfo.conf 
    - Windows: C:\ProgramData\Microsoft Azure Site Recovery\Config\ProxyInfo.conf 
    - Linux: /usr/local/InMage/config/ProxyInfo.conf


### <a name="method-2-provide-custom-application-proxy-settings"></a>Methode 2: Aangepaste proxy-instellingen voor toepassingen opgeven

In dit geval biedt de klant aangepaste proxy-instellingen voor toepassingen in Mobility Service config-bestand ProxyInfo.conf. Met deze methode kunnen klanten alleen proxy bieden voor Mobility Service of een andere proxy voor Azure Site Recovery Mobility Service dan een proxy (of geen proxy) voor de rest van de toepassingen op de machine.

## <a name="proxy-template"></a>Proxysjabloon
ProxyInfo.conf bevat de volgende sjabloonhttp://1.2.3.4 [proxy] Adres=Port=5678 BypassList=hypervrecoverymanager.windowsazure.com,login.microsoftonline.com,blob.core.windows.net. De BypassList ondersteunt geen wildcards zoals '*.windows.net' maar het geven van windows.net is goed genoeg om te omzeilen. 

## <a name="next-steps"></a>Volgende stappen:
- Lees [netwerkrichtlijnen](site-recovery-azure-to-azure-networking-guidance.md) voor het repliceren van Azure VM's.
- Herstel na noodgevallen implementeren door [Azure VM's te repliceren.](site-recovery-azure-to-azure.md)