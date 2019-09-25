---
title: Gegevens bronnen verbinden met Azure Sentinel? | Microsoft Docs
description: Meer informatie over hoe u gegevens bronnen verbindt met Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: angrobe
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: d8d3e52882a5cde9b00bf07ded933ae4d45b454b
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240167"
---
# <a name="connect-data-sources"></a>Verbinding maken met gegevensbronnen





Als u Azure Sentinel wilt inschakelen, moet u eerst verbinding maken met uw gegevens bronnen. Azure Sentinel wordt geleverd met een aantal connectors voor micro soft-oplossingen, die beschikbaar zijn in de doos en die realtime-integratie bieden, waaronder oplossingen voor micro soft Threat Protection, en Microsoft 365 bronnen, waaronder Office 365, Azure AD, Azure ATP en Microsoft Cloud App Security, en nog veel meer. Daarnaast zijn er ingebouwde connectors voor het bredere beveiligings ecosysteem voor niet-micro soft-oplossingen. U kunt ook algemene gebeurtenis indeling, syslog of REST-API gebruiken om uw gegevens bronnen met Azure Sentinel te verbinden.  

1. Selecteer in het menu **Data connectors**. Op deze pagina kunt u de volledige lijst met connectors zien die door Azure Sentinel worden geboden en hun status. Selecteer de connector die u wilt verbinden en selecteer de **pagina connector openen**. 

   ![Gegevens verzamelaars](./media/collect-data/collect-data-page.png)

1. Controleer op de pagina specifieke connector of u aan alle vereisten hebt voldaan en volg de instructies voor het verbinden van de gegevens met Azure Sentinel. Het kan enige tijd duren voordat de logboeken worden gesynchroniseerd met Azure Sentinel. Nadat u verbinding hebt gemaakt, ziet u een samen vatting van de gegevens in de grafiek **ontvangen gegevens** en de connectiviteits status van de gegevens typen.

   ![Verzamelaars koppelen](./media/collect-data/opened-connector-page.png)
  
1. Klik op het tabblad **volgende stappen** om een lijst van out-of-the-Box-inhoud weer te geven. Azure Sentinel levert voor het specifieke gegevens type.

   ![Gegevens verzamelaars](./media/collect-data/data-insights.png)
 

## <a name="data-connection-methods"></a>Gegevens verbindings methoden

De volgende gegevens verbindings methoden worden ondersteund door Azure Sentinel:

- **Micro soft-Services**:<br> Micro soft-Services zijn systeem eigen verbonden, met behulp van de Azure Foundation voor out-of-the-box-integratie, de volgende oplossingen kunnen met een paar klikken worden verbonden:
    - [Office 365](connect-office-365.md)
    - [Azure AD-controle logboeken en-aanmeldingen](connect-azure-active-directory.md)
    - [Azure-activiteit](connect-azure-activity.md)
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Azure Security Center](connect-azure-security-center.md)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Azure Advanced Threat Protection](connect-azure-atp.md)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [Windows-beveiligings gebeurtenissen](connect-windows-security-events.md) 
    - [Windows Firewall](connect-windows-firewall.md)

- **Externe oplossingen via API**: Sommige gegevens bronnen zijn verbonden via Api's die worden meegeleverd met de verbonden gegevens bron. Doorgaans bieden de meeste beveiligings technologieën een set Api's waarmee gebeurtenis logboeken kunnen worden opgehaald. De Api's maken verbinding met Azure Sentinel en verzamelen specifieke gegevens typen en verzenden ze naar Azure Log Analytics. Apparaten verbonden via API zijn:
    - [Barracuda](connect-barracuda.md)
    - [Symantec](connect-symantec.md)
- **Externe oplossingen via agent**: Azure Sentinel kan worden verbonden met alle andere gegevens bronnen waarmee in realtime logboek streaming kan worden uitgevoerd met behulp van het syslog-protocol, via een agent. <br>De meeste apparaten gebruiken het syslog-protocol om gebeurtenis berichten te verzenden die het logboek zelf en gegevens over het logboek bevatten. De indeling van de logboeken varieert, maar de meeste apparaten ondersteunen de standaard gebeurtenis indeling (CEF). <br>De Azure Sentinel-agent, die is gebaseerd op de Log Analytics-agent, converteert CEF opgemaakt Logboeken in een indeling die door Log Analytics kan worden opgenomen. Afhankelijk van het type apparaat wordt de agent rechtstreeks op het apparaat geïnstalleerd of op een speciale Linux-server. De agent voor Linux ontvangt gebeurtenissen van de syslog-daemon via UDP, maar als een Linux-machine verwacht een hoog volume aan syslog-gebeurtenissen te verzamelen, worden ze via TCP van de syslog-daemon naar de agent verzonden en van daaruit naar Log Analytics.
    - Firewalls, proxy's en eind punten:
        - [F5](connect-f5.md)
        - [Controle punt](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [Fortinet](connect-fortinet.md)
        - [Palo Alto](connect-paloalto.md)
        - [Andere CEF-apparaten](connect-common-event-format.md)
        - [Andere syslog-apparaten](connect-syslog.md)
    - DLP-oplossingen
    - [Threat Intelligence-providers](connect-threat-intelligence.md)
    - [DNS-machines](connect-dns.md) -agent rechtstreeks geïnstalleerd op de DNS-computer
    - Linux-servers
    - Andere Clouds
    
## Verbindings opties voor agent<a name="agent-options"></a>

Om uw externe apparaat te verbinden met Azure Sentinel, moet de agent worden geïmplementeerd op een toegewezen machine (VM of on-premises) om de communicatie tussen het apparaat en de Azure-Sentinel te ondersteunen. U kunt de agent automatisch of hand matig implementeren. Automatische implementatie is alleen beschikbaar als uw toegewezen computer een nieuwe VM is die u in azure maakt. 


![CEF in azure](./media/connect-cef/cef-syslog-azure.png)

U kunt de agent ook hand matig implementeren op een bestaande Azure-VM, op een virtuele machine in een andere Cloud of op een on-premises machine.

![CEF on-premises](./media/connect-cef/cef-syslog-onprem.png)

## <a name="map-data-types-with-azure-sentinel-connection-options"></a>Gegevens typen toewijzen met Azure Sentinel-verbindings opties


| **Gegevenstype** | **Verbinding maken** | **Data Connector?** | **opmerkingen** |
|------|---------|-------------|------|
| AWSCloudTrail | [Verbinding maken met AWS](connect-aws.md) | V | |
| AzureActivity | Overzicht van [Azure activiteit](connect-azure-activity.md) en [activiteiten logboeken](../azure-monitor/platform/activity-logs-overview.md) verbinden| V | |
| AuditLogs | [Verbinding maken met Azure AD](connect-azure-active-directory.md)  | V | |
| SigninLogs | [Verbinding maken met Azure AD](connect-azure-active-directory.md)  | V | |
| AzureFirewall |[Azure Diagnostics](../firewall/tutorial-diagnostics.md) | V | |
| InformationProtectionLogs_CL  | [Azure Information Protection rapporten](https://docs.microsoft.com/azure/information-protection/reports-aip)<br>[Azure Information Protection verbinden](connect-azure-information-protection.md)  | V | Dit maakt gewoonlijk gebruik van de functie **InformationProtectionEvents** naast het gegevens type. Zie [de rapporten wijzigen en aangepaste query's maken](https://docs.microsoft.com/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries) voor meer informatie|
| AzureNetworkAnalytics_CL  | [Analytisch schema voor verkeer](../network-watcher/traffic-analytics.md) [Traffic Analytics](../network-watcher/traffic-analytics.md)  | | |
| CommonSecurityLog  | [Verbinding maken met CEF](connect-common-event-format.md)  | V | |
| OfficeActivity | [Verbinding maken met Office 365](connect-office-365.md) | V | |
| SecurityEvents | [Windows-beveiligings gebeurtenissen verbinden](connect-windows-security-events.md)  | V | Zie voor de werkmappen met Inveilige protocollen [onveilige protocollen werkmap instellen](https://blogs.technet.microsoft.com/jonsh/azure-sentinel-insecure-protocols-dashboard-setup/)  |
| Syslog | [Syslog verbinden](connect-syslog.md) | V | |
| Micro soft Web Application firewall (WAF)-(AzureDiagnostics) |[Verbinding maken met micro soft Web Application firewall](connect-microsoft-waf.md) | V | |
| SymantecICDx_CL | [Symantec verbinden](connect-symantec.md) | V | |
| ThreatIntelligenceIndicator  | [Bedreigings informatie verbinden](connect-threat-intelligence.md)  | V | |
| VMConnection <br> ServiceMapComputer_CL<br> ServiceMapProcess_CL|  [Toewijzing van Azure Monitor-service](../azure-monitor/insights/service-map.md)<br>[Voor bereiding op Azure Monitor VM Insights](../azure-monitor/insights/vminsights-onboard.md) <br> [Azure Monitor VM Insights inschakelen](../azure-monitor/insights/vminsights-enable-overview.md) <br> [Eén virtuele machine on-boarding gebruiken](../azure-monitor/insights/vminsights-enable-single-vm.md)<br>  [On-boarding gebruiken via het beleid](../azure-monitor/insights/vminsights-enable-at-scale-policy.md)| X | VM Insights-werkmap  |
| DnsEvents | [DNS verbinden](connect-dns.md) | V | |
| W3CIISLog | [IIS-logboeken verbinden](../azure-monitor/platform/data-sources-iis-logs.md)  | X | |
| WireData | [Bedradings gegevens verbinden](../azure-monitor/insights/wire-data.md) | X | |
| WindowsFirewall | [Windows Firewall verbinden](connect-windows-firewall.md) | V | |
| AADIP SecurityAlert  | [Verbinding maken met Azure AD Identity Protection](connect-azure-ad-identity-protection.md)  | V | |
| AATP SecurityAlert  | [Verbinding maken met Azure ATP](connect-azure-atp.md) | V | |
| ASC SecurityAlert  | [Azure Security Center verbinden](connect-azure-security-center.md)  | V | |
| MCAS SecurityAlert  | [Microsoft Cloud App Security verbinden](connect-cloud-app-security.md)  | V | |
| SecurityAlert | | | |
| Voor-en naactiviteit (gebeurtenis) | [Verbinding maken](https://azure.microsoft.com/blog/detecting-in-memory-attacks-with-sysmon-and-azure-security-center)<br> [Windows-gebeurtenissen verbinden](../azure-monitor/platform/data-sources-windows-events.md) <br> [De ophaal-parser ophalen](https://github.com/Azure/Azure-Sentinel/blob/master/Parsers/SysmonParser.txt)| X | De garbagecollection-verzameling wordt niet standaard op virtuele machines geïnstalleerd. [Zie voor](https://docs.microsoft.com/sysinternals/downloads/sysmon)meer informatie over het installeren van de opschoon agent. |
| ConfigurationData  | [VM-inventarisatie automatiseren](../automation/automation-vm-inventory.md)| X | |
| ConfigurationChange  | [VM-tracking automatiseren](../automation/change-tracking.md) | X | |
| F5 BIG-IP | [Verbinding maken F5 BIG-IP](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel.md)  | X | |
| McasShadowItReporting  |  | X | |
| Barracuda_CL | [Verbinding maken met Barracuda](connect-barracuda.md) | V | |


## <a name="next-steps"></a>Volgende stappen

- U hebt een abonnement op Microsoft Azure nodig om aan de slag te gaan met Azure Sentinel. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/free/).
- Meer informatie over het [voorbereiden van uw gegevens naar Azure Sentinel](quickstart-onboard.md)en [inzicht krijgen in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
