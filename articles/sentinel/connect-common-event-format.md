---
title: CEF-gegevens verbinden met Azure Sentinel preview | Microsoft Docs
description: Verbind een externe oplossing die CEF-berichten (common Event Format) naar Azure Sentinel verzendt met behulp van een Linux-machine als een proxy.
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
ms.openlocfilehash: b7f03c11b53c6dc61fad6b916e7c08086917b416
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88565737"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Verbind uw externe oplossing met de algemene gebeurtenis indeling


Wanneer u verbinding maakt met een externe oplossing die CEF berichten verzendt, zijn er drie stappen om verbinding te maken met Azure Sentinel:

STAP 1: [verbinding maken met CEF met behulp van de agent](connect-cef-agent.md) stap 2: [oplossingen uitvoeren-specifieke stappen](connect-cef-solution-config.md) stap 3: de [verbinding controleren](connect-cef-verify.md)

In dit artikel wordt beschreven hoe de verbinding werkt, aan de hand van vereisten en krijgt u de stappen voor het implementeren van de agent op beveiligings oplossingen die algemene Event Format-berichten (CEF) boven op syslog verzenden. 

> [!NOTE] 
> Gegevens worden opgeslagen op de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

Als u deze verbinding wilt maken, moet u een agent implementeren op een toegewezen Linux-machine (VM of on-premises) ter ondersteuning van de communicatie tussen het apparaat en de Azure-Sentinel. In het volgende diagram worden de instellingen in het geval van een virtuele Linux-machine in azure beschreven.

 ![CEF in azure](./media/connect-cef/cef-syslog-azure.png)

Deze installatie bestaat ook als u een virtuele machine in een andere Cloud of op een on-premises computer gebruikt. 

 ![CEF on-premises](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>Beveiligingsoverwegingen

Zorg ervoor dat u de beveiliging van de computer configureert op basis van het beveiligings beleid van uw organisatie. U kunt bijvoorbeeld uw netwerk zodanig configureren dat het wordt uitgelijnd met het beveiligings beleid van uw bedrijfs netwerk en de poorten en protocollen in de daemon wijzigen om af te stemmen op uw vereisten. U kunt de volgende instructies gebruiken om de beveiligings configuratie van uw computer te verbeteren:  [Beveilig de virtuele machine in azure](../virtual-machines/linux/security-policy.md), [Aanbevolen procedures voor netwerk beveiliging](../security/fundamentals/network-best-practices.md).

Als u TLS-communicatie tussen de beveiligings oplossing en de syslog-computer wilt gebruiken, moet u de syslog-daemon (rsyslog of syslog-ng) configureren om te communiceren in TLS: [syslog-verkeer met TLS-rsyslog versleutelen](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [logboek berichten versleutelen met TLS-syslog-aardgas](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).

 
## <a name="prerequisites"></a>Vereisten
Zorg ervoor dat op de Linux-machine die u als proxy gebruikt, een van de volgende besturings systemen wordt uitgevoerd:

- 64-bits
  - CentOS 6 en 7
  - Amazon Linux 2017,09
  - Oracle Linux 6 en 7
  - Red Hat Enterprise Linux Server 6 en 7
  - Debian GNU/Linux 8 en 9
  - Ubuntu Linux 14,04 LTS, 16,04 LTS en 18,04 LTS
  - SUSE Linux Enterprise Server 12
- 32-bits
   - CentOS 6
   - Oracle Linux 6
   - Red Hat Enterprise Linux Server 6
   - Debian GNU/Linux 8 en 9
   - Ubuntu Linux 14,04 LTS en 16,04 LTS
 
 - Daemon-versies
   - Syslog-ng: 2,1-3.22.1
   - Rsyslog: V8
  
 - Ondersteuning voor syslog-Rfc's
   - Syslog RFC 3164
   - Syslog RFC 5424
 
Zorg ervoor dat uw computer ook aan de volgende vereisten voldoet: 
- Machtigingen
    - U moet over verhoogde machtigingen (sudo) beschikken op uw computer. 
- Softwarevereisten
    - Zorg ervoor dat python op uw computer wordt uitgevoerd



## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u CEF-apparaten verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats.md).

