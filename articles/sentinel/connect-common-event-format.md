---
title: CEF-gegevens verbinden met Azure Sentinel Preview| Microsoft Documenten
description: Meer informatie over het verbinden van CEF-gegevens met Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2019
ms.author: yelevin
ms.openlocfilehash: 8314614616c6b8969832d52fc684d47ba1bf0fe3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588345"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Uw externe oplossing verbinden met common event-indeling


Wanneer u een externe oplossing verbindt die CEF-berichten verzendt, zijn er drie stappen om verbinding te maken met Azure Sentinel:

STAP 1: [CEF verbinden door de agent](connect-cef-agent.md) STAP 2 te implementeren: [Oplossingsspecifieke stappen uitvoeren](connect-cef-solution-config.md) STAP 3: Connectiviteit [verifiëren](connect-cef-verify.md)

In dit artikel wordt beschreven hoe de verbinding werkt, voorwaarden bevat en u de stappen biedt voor het implementeren van de agent op beveiligingsoplossingen die CEF-berichten (Common Event Format) verzenden bovenop Syslog. 

> [!NOTE] 
> Gegevens worden opgeslagen in de geografische locatie van de werkruimte waarop u Azure Sentinel uitvoert.

Om deze verbinding te maken, moet u een agent implementeren op een speciale Linux-machine (VM of on-premises) om de communicatie tussen het toestel en Azure Sentinel te ondersteunen. In het volgende diagram wordt de instelling beschreven in het geval van een Linux-vm in Azure.

 ![CEF in Azure](./media/connect-cef/cef-syslog-azure.png)

Deze instelling bestaat als u een VM gebruikt in een andere cloud of een on-premises machine. 

 ![CEF op locatie](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>Beveiligingsoverwegingen

Zorg ervoor dat u de beveiliging van de machine configureert volgens het beveiligingsbeleid van uw organisatie. U bijvoorbeeld uw netwerk configureren om af te stemmen op uw bedrijfsnetwerkbeveiligingsbeleid en de poorten en protocollen in de daemon wijzigen om aan uw vereisten te voldoen. U de volgende instructies gebruiken om de beveiligingsconfiguratie van uw machine te verbeteren:  [Secure VM in Azure](../virtual-machines/linux/security-policy.md), Best [practices for Network security](../security/fundamentals/network-best-practices.md).

Om TLS-communicatie tussen de beveiligingsoplossing en de Syslog-machine te gebruiken, moet u de Syslog daemon (rsyslog of syslog-ng) configureren om te communiceren in TLS: [Syslog Traffic versleutelen met TLS-rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [Logberichten versleutelen met TLS –syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).

 
## <a name="prerequisites"></a>Vereisten
Zorg ervoor dat de Linux-machine die u als proxy gebruikt, een van de volgende besturingssystemen draait:

- 64-bits
  - CentOS 6 en 7
  - Amazon Linux 2017.09
  - Oracle Linux 6 en 7
  - Red Hat Enterprise Linux Server 6 en 7
  - Debian GNU/Linux 8 en 9
  - Ubuntu Linux 14.04 LTS, 16.04 LTS en 18.04 LTS
  - SUSE Linux Enterprise Server 12
- 32-bits
   - CentOS 6
   - Oracle Linux 6
   - Red Hat Enterprise Linux Server 6
   - Debian GNU/Linux 8 en 9
   - Ubuntu Linux 14.04 LTS en 16.04 LTS
 
 - Daemon-versies
   - Syslog-ng: 2.1 - 3.22.1
   - Rsyslog: v8
  
 - Syslog RFC's ondersteund
   - Syslog RFC 3164
   - Syslog RFC 5424
 
Zorg ervoor dat uw machine ook aan de volgende eisen voldoet: 
- Machtigingen
    - U moet verhoogde machtigingen (sudo) op uw machine hebben. 
- Softwarevereisten
    - Zorg ervoor dat Python op uw machine draait



## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u CEF-apparaten verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats.md)

