---
title: Gegevensbronnen verbinden met Azure Sentinel | Microsoft Documenten
description: Meer informatie over het verbinden van gegevensbronnen met Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: angrobe
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: yelevin
ms.openlocfilehash: 9d2d1985b23e1c7f5e0f7d9fd2795bd85e28ace0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240084"
---
# <a name="connect-data-sources"></a>Verbinding maken met gegevensbronnen

Als u Azure Sentinel aan boord wilt hebben, moet u eerst verbinding maken met uw gegevensbronnen. Azure Sentinel wordt geleverd met een aantal connectors voor Microsoft-oplossingen, die uit de doos beschikbaar zijn en realtime integratie bieden, waaronder Microsoft Threat Protection-oplossingen en Microsoft 365-bronnen, waaronder Office 365, Azure AD, Azure ATP en Microsoft Cloud App-beveiliging en meer. Daarnaast zijn er ingebouwde connectors voor het bredere beveiligingsecosysteem voor niet-Microsoft-oplossingen. U ook algemene gebeurtenisindeling, Syslog of REST-API gebruiken om uw gegevensbronnen ook met Azure Sentinel te verbinden.  

1. Selecteer **gegevensconnectoren**in het menu . Op deze pagina ziet u de volledige lijst met connectors die Azure Sentinel biedt en hun status. Selecteer de verbindingsconnector die u wilt aansluiten en selecteer **Connectorpagina openen**. 

   ![Gegevensverzamelaars](./media/collect-data/collect-data-page.png)

1. Zorg er op de specifieke verbindingspagina voor dat u aan alle vereisten hebt voldaan en volg de instructies om de gegevens aan Azure Sentinel te verbinden. Het kan enige tijd duren voordat de logboeken worden gesynchroniseerd met Azure Sentinel. Nadat u verbinding hebt gemaakt, ziet u een overzicht van de gegevens in de grafiek **Gegevens ontvangen** en de verbindingsstatus van de gegevenstypen.

   ![Verzamelaars verbinden](./media/collect-data/opened-connector-page.png)
  
1. Klik op het tabblad **Volgende stappen** om een lijst met out-of-the-box-inhoud te krijgen die Azure Sentinel biedt voor het specifieke gegevenstype.

   ![Gegevensverzamelaars](./media/collect-data/data-insights.png)
 

## <a name="data-connection-methods"></a>Gegevensverbindingsmethoden

De volgende methoden voor gegevensverbinding worden ondersteund door Azure Sentinel:

- **Service tot service integratie:**<br> Sommige services zijn native verbonden, zoals AWS- en Microsoft-services, deze services maken gebruik van de Azure-basis voor out-of-the-box-integratie, de volgende oplossingen kunnen in een paar klikken worden verbonden:
    - [Amazon Web Services - CloudTrail](connect-aws.md)
    - [Azure-activiteit](connect-azure-activity.md)
    - [Azure AD-controlelogboeken en aanmeldingen](connect-azure-active-directory.md)
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Azure Advanced Threat Protection](connect-azure-atp.md)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Azure Security Center](connect-azure-security-center.md)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [Domeinnaamserver](connect-dns.md)
    - [Office 365](connect-office-365.md)
    - [Microsoft Defender ATP](connect-microsoft-defender-advanced-threat-protection.md)
    - [Microsoft Web Application Firewall](connect-microsoft-waf.md)
    - [Windows Firewall](connect-windows-firewall.md)
    - [Windows-beveiligingsgebeurtenissen](connect-windows-security-events.md)

- **Externe oplossingen via API**: Sommige gegevensbronnen zijn verbonden met API's die worden geleverd door de verbonden gegevensbron. Doorgaans bieden de meeste beveiligingstechnologieën een set API's waarmee gebeurtenislogboeken kunnen worden opgehaald. De API's maken verbinding met Azure Sentinel en verzamelen specifieke gegevenstypen en sturen deze naar Azure Log Analytics. Toestellen die via API zijn verbonden, zijn:
    - [Barracuda](connect-barracuda.md)
    - [Barracuda CloudGen-firewall](connect-barracuda-cloudgen-firewall.md)
    - [Citrix Analytics (Security)](connect-citrix-analytics.md)
    - [F5 BIG-IP](connect-f5-big-ip.md)
    - [Forcepoint DLP](connect-forcepoint-dlp.md)
    - [Squadra Technologies secRMM](connect-squadra-secrmm.md)
    - [Symantec ICDX](connect-symantec.md)
    - [Zimperium](connect-zimperium-mtd.md)


- **Externe oplossingen via agent**: Azure Sentinel kan worden aangesloten op alle andere gegevensbronnen die realtime logboekstreaming kunnen uitvoeren via het Syslog-protocol, via een agent. <br>De meeste apparaten gebruiken het Syslog-protocol om gebeurtenisberichten te verzenden die het logboek zelf en gegevens over het logboek bevatten. De indeling van de logboeken varieert, maar de meeste apparaten ondersteunen de opgemaakte opmaak op basis van Common Event Format (CEF) voor logboekgegevens. <br>De Azure Sentinel-agent, die is gebaseerd op de Log Analytics-agent, zet CEF-opgemaakte logboeken om in een indeling die kan worden ingenomen door Log Analytics. Afhankelijk van het type toestel wordt de agent direct op het toestel of op een speciale Linux-server geïnstalleerd. De agent voor Linux ontvangt gebeurtenissen van de Syslog daemon via UDP, maar als een Linux-machine wordt verwacht dat een hoog volume van Syslog gebeurtenissen te verzamelen, worden ze verzonden over TCP van de Syslog daemon naar de agent en van daar naar Log Analytics.
    - Firewalls, proxy's en eindpunten:
        - [Check Point](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [ExtraHop Reveal(x)](connect-extrahop.md)
        - [F5](connect-f5.md)
        - [Force Point-producten](connect-forcepoint-casb-ngfw.md)
        - [Fortinet](connect-fortinet.md)
        - [Palo Alto Networks](connect-paloalto.md)
        - [One Identity Safeguard](connect-one-identity.md)
        - [Andere CEF-toestellen](connect-common-event-format.md)
        - [Andere Syslog-apparaten](connect-syslog.md)
        - [Deep Security van Trend Micro](connect-trend-micro.md)
        - [Zscaler](connect-zscaler.md)
    - DLP-oplossingen
    - [Aanbieders van bedreigingsinformatie](connect-threat-intelligence.md)
    - [DNS-machines](connect-dns.md) - agent die rechtstreeks op de DNS-machine is geïnstalleerd
    - Linux-servers
    - Andere wolken
    
## <a name="agent-connection-options"></a>Verbindingsopties voor agenten<a name="agent-options"></a>

Om uw externe toestel aan te sluiten op Azure Sentinel, moet de agent worden geïmplementeerd op een speciale machine (VM of on-premises) om de communicatie tussen het toestel en Azure Sentinel te ondersteunen. U de agent automatisch of handmatig implementeren. Automatische implementatie is alleen beschikbaar als uw speciale machine een nieuwe vm is die u in Azure maakt. 


![CEF in Azure](./media/connect-cef/cef-syslog-azure.png)

U de agent ook handmatig implementeren op een bestaande Azure VM, op een vm in een andere cloud of op een on-premises machine.

![CEF op locatie](./media/connect-cef/cef-syslog-onprem.png)

## <a name="map-data-types-with-azure-sentinel-connection-options"></a>Gegevenstypen toewijzen met behulp van Azure Sentinel-verbindingsopties


| **Gegevenstype** | **Verbinding maken** | **Dataconnector?** | **Opmerkingen** |
|------|---------|-------------|------|
| AWSCloudTrail (AWSCloudTrail) | [Verbinding maken met AWS](connect-aws.md) | V | |
| AzureActivity | [Overzicht van Azure-activiteits-](connect-azure-activity.md) en [activiteitslogboeken verbinden](../azure-monitor/platform/platform-logs-overview.md)| V | |
| Controlelogboeken | [Verbinding maken met Azure AD](connect-azure-active-directory.md)  | V | |
| Loginlogs | [Verbinding maken met Azure AD](connect-azure-active-directory.md)  | V | |
| AzureFirewall |[Azure Diagnostics](../firewall/tutorial-diagnostics.md) | V | |
| InformationProtectionLogs_CL  | [Azure Information Protection-rapporten](https://docs.microsoft.com/azure/information-protection/reports-aip)<br>[Verbinding maken met Azure Information Protection](connect-azure-information-protection.md)  | V | Dit maakt meestal gebruik van de functie **InformationProtectionEvents** naast het gegevenstype. Zie [De rapporten wijzigen en aangepaste query's maken voor](https://docs.microsoft.com/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries) meer informatie.|
| AzureNetworkAnalytics_CL  | [Verkeersanalytische schema](../network-watcher/traffic-analytics.md) [Verkeersanalyse](../network-watcher/traffic-analytics.md)  | | |
| CommonSecurityLog (CommonSecurityLog)  | [CEF verbinden](connect-common-event-format.md)  | V | |
| OfficeActiviteit | [Verbinding maken met Office 365](connect-office-365.md) | V | |
| Beveiligingsgebeurtenissen | [Verbinding maken met Windows-beveiligingsgebeurtenissen](connect-windows-security-events.md)  | V | Zie [Werkmapvoor beveiligde](/azure/sentinel/quickstart-get-visibility#use-built-in-workbooks) protocollen voor de werkmappen onveilige protocollen  |
| Syslog | [Verbinding maken met Syslog](connect-syslog.md) | V | |
| Microsoft Web Application Firewall (WAF) - (AzureDiagnostics) |[Microsoft Web Application Firewall verbinden](connect-microsoft-waf.md) | V | |
| SymantecICDx_CL | [Symantec verbinden](connect-symantec.md) | V | |
| ThreatIntelligenceIndicator  | [Verbinding maken met bedreigingsinformatie](connect-threat-intelligence.md)  | V | |
| VMConnection <br> ServiceMapComputer_CL<br> ServiceMapProcess_CL|  [Azure Monitor-servicekaart](../azure-monitor/insights/service-map.md)<br>[Onboarding van Azure Monitor VM-inzichten](../azure-monitor/insights/vminsights-onboard.md) <br> [Azure Monitor VM-inzichten inschakelen](../azure-monitor/insights/vminsights-enable-overview.md) <br> [Eén VM On-boarding gebruiken](../azure-monitor/insights/vminsights-enable-single-vm.md)<br>  [On-boarding gebruiken via beleid](../azure-monitor/insights/vminsights-enable-at-scale-policy.md)| X | VM-inzichtenwerkmap  |
| DnsEvents DnsEvents | [DNS verbinden](connect-dns.md) | V | |
| W3CIISLog | [IIS-logboeken verbinden](../azure-monitor/platform/data-sources-iis-logs.md)  | X | |
| WireData (WireData) | [Draadgegevens verbinden](../azure-monitor/insights/wire-data.md) | X | |
| WindowsFirewall (WindowsFirewall) | [Windows Firewall verbinden](connect-windows-firewall.md) | V | |
| AADIP SecurityAlert  | [Verbinding maken met Azure AD Identity Protection](connect-azure-ad-identity-protection.md)  | V | |
| AATP SecurityAlert  | [Verbinding maken met Azure ATP](connect-azure-atp.md) | V | |
| ASC SecurityAlert  | [Verbinding maken met Azure Security Center](connect-azure-security-center.md)  | V | |
| MCAS SecurityAlert  | [Microsoft Cloud App-beveiliging verbinden](connect-cloud-app-security.md)  | V | |
| SecurityAlert | | | |
| Sysmon (Evenement) | [Sluit Sysmon aan](https://azure.microsoft.com/blog/detecting-in-memory-attacks-with-sysmon-and-azure-security-center)<br> [Windows-gebeurtenissen verbinden](../azure-monitor/platform/data-sources-windows-events.md) <br> [Haal de Sysmon Parser](https://github.com/Azure/Azure-Sentinel/blob/master/Parsers/SysmonParser.txt)| X | Sysmon-verzameling is niet standaard geïnstalleerd op virtuele machines. Zie [Sysmon](https://docs.microsoft.com/sysinternals/downloads/sysmon)voor meer informatie over het installeren van de Sysmon Agent. |
| ConfiguratieGegevens  | [VM-voorraad automatiseren](../automation/automation-vm-inventory.md)| X | |
| Configuratiewijzigen  | [VM-tracking automatiseren](../automation/change-tracking.md) | X | |
| F5 BIG-IP | [Verbinding maken met F5 BIG-IP](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)  | X | |
| McasShadowItRapportage  |  | X | |
| Barracuda_CL | [Verbinding maken met Barracuda](connect-barracuda.md) | V | |


## <a name="next-steps"></a>Volgende stappen

- Om aan de slag te gaan met Azure Sentinel, hebt u een abonnement op Microsoft Azure nodig. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/free/).
- Meer informatie over het [gebruik van uw gegevens naar Azure Sentinel](quickstart-onboard.md)en inzicht krijgen in uw gegevens en potentiële [bedreigingen.](quickstart-get-visibility.md)
