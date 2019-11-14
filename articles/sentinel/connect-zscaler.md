---
title: Zscaler-gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van Zscaler-gegevens met Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: rkarlin
ms.openlocfilehash: 45351cc29b2b7028863aff06ab5a511674604d6f
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048957"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Verbinding maken met Zscaler Internet toegang tot Azure Sentinel

> [!IMPORTANT]
> De Zscaler-gegevens connector in azure Sentinel is momenteel beschikbaar als open bare preview.
> Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In dit artikel wordt uitgelegd hoe u uw Zscaler Internet Access-apparaat verbindt met Azure Sentinel. Met de Zscaler-gegevens connector kunt u eenvoudig uw Zscaler Internet Access-Logboeken (ZIA) verbinden met Azure Sentinel, voor het weer geven van Dash boards, het maken van aangepaste waarschuwingen en het verbeteren van onderzoek. Door Zscaler te gebruiken in azure Sentinel krijgt u meer inzicht in het Internet gebruik van uw organisatie en worden de mogelijkheden voor beveiligings bewerkingen verbeterd. 


## <a name="how-it-works"></a>Het werkt als volgt

U moet een agent implementeren op een toegewezen Linux-machine (VM of on-premises) ter ondersteuning van de communicatie tussen Zscaler Internet toegang en Azure Sentinel. In het volgende diagram worden de instellingen in het geval van een virtuele Linux-machine in azure beschreven.

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
 
 
1. Klik in de Azure Sentinel-Portal op **Data connectors** en selecteer **Zscaler** en open vervolgens de **pagina connector**. 

1. Onder **installeren en configureren van de syslog-agent**selecteert u het type computer, azure, andere Cloud of on-premises. 
   > [!NOTE]
   > Omdat met het script in de volgende stap de Log Analytics-agent wordt geïnstalleerd en de computer wordt verbonden met uw Azure-Sentinel-werk ruimte, moet u ervoor zorgen dat deze computer niet is verbonden met een andere werk ruimte.
1. U moet over verhoogde machtigingen (sudo) beschikken op uw computer. Zorg ervoor dat u python op uw computer hebt met behulp van de volgende opdracht: `python –version`

1. Voer het volgende script uit op de proxy computer.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. Zorg ervoor dat het script wordt uitgevoerd en controleer of er geen fout-of waarschuwings berichten worden weer gegeven.


## <a name="step-2-configure-your-zscaler-to-send-cef-messages"></a>STAP 2: Configureer uw Zscaler om CEF-berichten te verzenden

1. Op het Zscaler-apparaat moet u deze waarden instellen zodat het apparaat de benodigde logboeken naar de Azure Sentinel syslog-agent verzendt, op basis van de Log Analytics agent. U kunt deze para meters in uw apparaat wijzigen, op voor waarde dat u ze ook wijzigt in de syslog-daemon op de onderverklikker agent van Azure.
    - Protocol = TCP
    - Poort = 514
    - Indeling = CEF
    - IP-adres: Zorg ervoor dat u de CEF-berichten verzendt naar het IP-adres van de virtuele machine die u voor dit doel hebt toegewezen.
 Zie de [implementatie handleiding voor Zscaler en Azure](https://aka.ms/ZscalerCEFInstructions)voor meer informatie.
 
   > [!NOTE]
   > Deze oplossing ondersteunt syslog RFC 3164 of RFC 5424.


1. Als u het relevante schema in Log Analytics voor de CEF-gebeurtenissen wilt gebruiken, zoekt u naar `CommonSecurityLog`.

## <a name="step-3-validate-connectivity"></a>STAP 3: connectiviteit valideren

1. Open Log Analytics om ervoor te zorgen dat Logboeken worden ontvangen met behulp van het CommonSecurityLog-schema.<br> Het kan 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven. 

1. Voordat u het script uitvoert, wordt u aangeraden berichten te verzenden vanuit uw beveiligings oplossing om er zeker van te zijn dat ze worden doorgestuurd naar de syslog-proxy computer die u hebt geconfigureerd. 
1. U moet over verhoogde machtigingen (sudo) beschikken op uw computer. Zorg ervoor dat u python op uw computer hebt met behulp van de volgende opdracht: `python –version`
1. Voer het volgende script uit om de connectiviteit tussen de agent, Azure Sentinel en uw beveiligings oplossing te controleren. Er wordt gecontroleerd of het door sturen van daemon correct is geconfigureerd, luistert naar de juiste poorten en dat er geen communicatie tussen de daemon en de Log Analytics agent wordt geblokkeerd. Het script verzendt ook beeldberichten TestCommonEventFormat om de end-to-end-connectiviteit te controleren. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u verbinding maakt met Zscaler Internet toegang tot Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag [met het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats.md).

