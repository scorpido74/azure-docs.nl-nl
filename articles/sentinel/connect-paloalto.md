---
title: Palo Alto Networks-gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van gegevens van Palo Alto-netwerken met Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: rkarlin
ms.openlocfilehash: 8b1331eb99fd3d061d231ae48c40a721911e74db
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73475861"
---
# <a name="connect-palo-alto-networks-to-azure-sentinel"></a>Palo Alto Networks verbinden met Azure Sentinel



In dit artikel wordt uitgelegd hoe u uw Palo Alto Networks-apparaat verbindt met Azure Sentinel. Met de Palo Alto Networks Data Connector kunt u eenvoudig uw Palo Alto Networks-logboeken verbinden met Azure Sentinel, voor het weer geven van Dash boards, het maken van aangepaste waarschuwingen en het verbeteren van het onderzoek. Door gebruik te maken van Palo Alto-netwerken op Azure Sentinel krijgt u meer inzicht in het Internet gebruik van uw organisatie en worden de mogelijkheden voor beveiligings bewerkingen verbeterd. 


## <a name="how-it-works"></a>Het werkt als volgt

U moet een agent implementeren op een toegewezen Linux-machine (VM of on-premises) ter ondersteuning van de communicatie tussen Palo Alto-netwerken en Azure Sentinel. In het volgende diagram worden de instellingen in het geval van een virtuele Linux-machine in azure beschreven.

 ![CEF in azure](./media/connect-cef/cef-syslog-azure.png)

Deze installatie bestaat ook als u een virtuele machine in een andere Cloud of op een on-premises computer gebruikt. 

 ![CEF on-premises](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>Beveiligingsoverwegingen

Zorg ervoor dat u de beveiliging van de computer configureert op basis van het beveiligings beleid van uw organisatie. U kunt bijvoorbeeld uw netwerk zodanig configureren dat het wordt uitgelijnd met het beveiligings beleid van uw bedrijfs netwerk en de poorten en protocollen in de daemon wijzigen om af te stemmen op uw vereisten. U kunt de volgende instructies gebruiken om de beveiligings configuratie van uw computer te verbeteren:  [Beveilig de virtuele machine in azure](../virtual-machines/linux/security-policy.md), [Aanbevolen procedures voor netwerk beveiliging](../security/fundamentals/network-best-practices.md).

Als u TLS-communicatie tussen de beveiligings oplossing en de syslog-computer wilt gebruiken, moet u de syslog-daemon (rsyslog of syslog-ng) configureren om te communiceren in TLS: [syslog-verkeer met TLS-rsyslog versleutelen](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [logboek berichten versleutelen met TLS: syslog-aardgas](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).

 
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
## <a name="step-1-deploy-the-agent"></a>STAP 1: de agent implementeren

In deze stap moet u de Linux-computer selecteren die zal fungeren als een proxy tussen Azure Sentinel en uw beveiligings oplossing. U moet een script uitvoeren op de proxy computer die:
- Hiermee wordt de Log Analytics agent geïnstalleerd en zo nodig geconfigureerd om syslog-berichten op poort 514 via TCP te belui Steren en de CEF-berichten te verzenden naar uw Azure Sentinel-werk ruimte.
- Hiermee configureert u de syslog-daemon voor het door sturen van CEF-berichten naar de Log Analytics-agent met behulp van poort 25226.
- Hiermee stelt u de syslog-agent in om de gegevens te verzamelen en veilig naar Log Analytics te verzenden, waar ze worden geparseerd en verrijkt.
 
 
1. Klik in de Azure-Sentinel Portal op **Data connectors** en selecteer **Palo Alto Networks** en **Open vervolgens connector-pagina**. 

1. Onder **installeren en configureren van de syslog-agent**selecteert u het type computer, azure, andere Cloud of on-premises. 
   > [!NOTE]
   > Omdat met het script in de volgende stap de Log Analytics-agent wordt geïnstalleerd en de computer wordt verbonden met uw Azure-Sentinel-werk ruimte, moet u ervoor zorgen dat deze computer niet is verbonden met een andere werk ruimte.
1. U moet over verhoogde machtigingen (sudo) beschikken op uw computer. Zorg ervoor dat u python op uw computer hebt met behulp van de volgende opdracht: `python –version`

1. Voer het volgende script uit op de proxy computer.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. Zorg ervoor dat het script wordt uitgevoerd en controleer of er geen fout-of waarschuwings berichten worden weer gegeven.


 
## <a name="step-2-forward-palo-alto-networks-logs-to-the-syslog-agent"></a>STAP 2: Palo Alto Networks-logboeken door sturen naar de syslog-agent

Configureer Palo Alto-netwerken om syslog-berichten in de CEF-indeling door te sturen naar uw Azure-werk ruimte via de syslog-agent:
1.  Ga naar [CEF-configuratie handleidingen (common Event Format)](https://docs.paloaltonetworks.com/resources/cef) en down load de PDF voor uw type apparaat. Volg alle instructies in de hand leiding om uw Palo Alto Networks-apparaat in te stellen voor het verzamelen van CEF-gebeurtenissen. 

1.  Ga naar [syslog-bewaking configureren](https://aka.ms/asi-syslog-paloalto-forwarding) en volg stap 2 en 3 om CEF Event forwarding te configureren van uw Palo Alto Networks-apparaat naar Azure Sentinel.

    1. Zorg ervoor dat u de **syslog-server indeling** instelt op **BSD**.

       > [!NOTE]
       > De kopieer-en plak bewerkingen van de PDF kunnen de tekst wijzigen en wille keurige tekens invoegen. Om dit te voor komen, kopieert u de tekst naar een editor en verwijdert u alle tekens die de logboek indeling kunnen verstoren voordat u deze plakt, zoals u kunt zien in dit voor beeld.
 
        ![CEF-probleem met tekst kopiëren](./media/connect-cef/paloalto-text-prob1.png)

2. Als u het relevante schema in Log Analytics voor de gebeurtenissen van het Palo Alto-netwerken wilt gebruiken, zoekt u naar **CommonSecurityLog**.

## <a name="step-3-validate-connectivity"></a>STAP 3: connectiviteit valideren

1. Open Log Analytics om ervoor te zorgen dat Logboeken worden ontvangen met behulp van het CommonSecurityLog-schema.<br> Het kan 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven. 

1. Voordat u het script uitvoert, wordt u aangeraden berichten te verzenden vanuit uw beveiligings oplossing om er zeker van te zijn dat ze worden doorgestuurd naar de syslog-proxy computer die u hebt geconfigureerd. 
1. U moet over verhoogde machtigingen (sudo) beschikken op uw computer. Zorg ervoor dat u python op uw computer hebt met behulp van de volgende opdracht: `python –version`
1. Voer het volgende script uit om de connectiviteit tussen de agent, Azure Sentinel en uw beveiligings oplossing te controleren. Er wordt gecontroleerd of het door sturen van daemon correct is geconfigureerd, luistert naar de juiste poorten en dat er geen communicatie tussen de daemon en de Log Analytics agent wordt geblokkeerd. Het script verzendt ook beeldberichten TestCommonEventFormat om de end-to-end-connectiviteit te controleren. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`







## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Palo Alto Networks-apparaten verbindt met Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag [met het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).

