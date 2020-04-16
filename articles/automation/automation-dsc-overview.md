---
title: Overzicht van configuratie van Azure Automation State
description: Dit artikel is een overzicht van Azure Automation State Configuration (DSC), de voorwaarden en de bekende problemen.
keywords: powershell dsc, gewenste statusconfiguratie, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 04/15/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1166f5a1d7586c54255120a656b060c93f842fd9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406186"
---
# <a name="state-configuration-overview"></a>Overzicht van State Configuration

Azure Automation State Configuration is een Azure-service waarmee u PowerShell Desired State Configuration (DSC)-configuraties schrijven, beheren en [compileren.](/powershell/scripting/dsc/configurations/configurations) De service importeert ook [DSC-resources](/powershell/scripting/dsc/resources/resources) en wijst configuraties toe voor doelknooppunten, allemaal in de cloud.

## <a name="why-use-azure-automation-state-configuration"></a>Voordelen van Azure Automation State Configuration

Azure Automation State Configuration biedt verschillende voordelen ten opzichte van het gebruik van DSC buiten Azure.

### <a name="built-in-pull-server"></a>Ingebouwde pull-server

Azure Automation State Configuration biedt een DSC pull-server die vergelijkbaar is met de [Windows Feature DSC-Service.](/powershell/scripting/dsc/pull-server/pullserver) Doelknooppunten kunnen automatisch configuraties ontvangen, voldoen aan de gewenste status en rapporteren over de naleving ervan. De ingebouwde pull-server in Azure Automation elimineert de noodzaak om uw eigen pull-server in te stellen en te onderhouden. Azure Automation kan zich richten op virtuele of fysieke Windows- of Linux-machines, in de cloud of on-premises.

### <a name="manage-all-your-dsc-artifacts"></a>Al uw DSC-artefacten beheren

Azure Automation State Configuration brings the same management layer to [PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) as it offers for PowerShell scripting. Vanuit de Azure-portal of vanuit PowerShell u al uw DSC-configuraties, resources en doelknooppunten beheren.

![Schermafbeelding van de pagina Azure Automation](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>Rapportagegegevens importeren in Azure Monitor-logboeken

Knooppunten die worden beheerd met Azure Automation State Configuration sturen gedetailleerde rapportagestatusgegevens naar de ingebouwde pull-server. U azure automation state configuration configureren om deze gegevens naar uw Log Analytics-werkruimte te verzenden. Zie [Gegevens over azure automation state configuration reporting data doorsturen naar Azure Monitor Logs](automation-dsc-diagnostics.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Houd rekening met de volgende vereisten wanneer u Azure Automation State Configuration voor DSC gebruikt.

### <a name="operating-system-requirements"></a>Vereisten voor het besturingssysteem

Voor knooppunten met Windows worden de volgende versies ondersteund:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

>[!NOTE]
>Omdat het [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) standalone product SKU geen implementatie van DSC bevat, kan het niet worden beheerd door PowerShell DSC of Azure Automation State Configuration.

Voor knooppunten met Linux ondersteunt de DSC Linux-extensie alle Linux-distributies die worden vermeld onder [Ondersteunde Linux-distributies.](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions)

### <a name="dsc-requirements"></a>DSC-vereisten

Voor alle Windows-knooppunten die in Azure worden uitgevoerd, wordt [Windows Management Framework 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) geïnstalleerd tijdens het instappen. Voor knooppunten met Windows Server 2012 en Windows 7 is [WinRM](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency) ingeschakeld.

Voor alle Linux-knooppunten die in Azure worden uitgevoerd, is [PowerShell DSC voor Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) geïnstalleerd tijdens het instappen.

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>Configuratie van particuliere netwerken

Als uw knooppunten zich in een privénetwerk bevinden, zijn de volgende poort- en URL's vereist. Deze bronnen bieden netwerkconnectiviteit voor het beheerde knooppunt en stellen DSC in staat om te communiceren met Azure Automation.

* Poort: Alleen TCP 443 vereist voor uitgaande internettoegang
* Globale URL: ***.azure-automation.net**
* Wereldwijde URL van US Gov Virginia: ***.azure-automation.us**
* Agentservice: **\<https://\>workspaceId .agentsvc.azure-automation.net**

Als u DSC-resources gebruikt die communiceren tussen knooppunten, zoals de [Bronnen WachtFor*,](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource)moet u ook communicatie tussen knooppunten toestaan. Zie de documentatie voor elke DSC-bron om inzicht te krijgen in deze netwerkvereisten.

#### <a name="proxy-support"></a>Proxy-ondersteuning

Proxy-ondersteuning voor de DSC-agent is beschikbaar in Windows-versie 1809 en hoger. U schakelt deze optie in `ProxyURL` `ProxyCredential` door de waarden in te stellen voor en in het [metaconfiguratiescript](automation-dsc-onboarding.md#generate-dsc-metaconfigurations) dat wordt gebruikt om knooppunten te registreren.

>[!NOTE]
>Azure Automation State Configuration biedt geen DSC-proxyondersteuning voor eerdere versies van Windows.

Voor Linux-knooppunten ondersteunt de DSC-agent `http_proxy` een proxyserver en gebruikt de variabele om de URL op te geven.

#### <a name="azure-automation-state-configuration-network-ranges-and-namespace"></a>Azure Automation State Configuration network ranges and namespace Azure Automation State Configuration network ranges and namespace Azure Automation State Configuration network ranges and namespace Azure Automation

Wanneer u uitzonderingen definieert, raden we u aan de IP-adressen in de volgende tabel te gebruiken. Voor IP-adressen u het [XML-bestand Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653) downloaden vanuit het Microsoft Downloadcentrum. Dit bestand bevat de momenteel geïmplementeerde bereiken en eventuele toekomstige wijzigingen in de IP-bereiken. Het wordt wekelijks bijgewerkt.

Als u een Automatiseringsaccount hebt dat is gedefinieerd voor een bepaalde regio, u de communicatie beperken tot dat regionale datacenter. In de volgende tabel vindt u de DNS-record voor elke regio:

| **Regio** | **DNS-record** |
| --- | --- |
| VS - west-centraal | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| VS - zuid-centraal |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| VS - oost    | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
| VS - oost 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Canada - midden |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europa -west |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Europa - noord |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Azië - zuidoost |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| India - centraal |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Japan - oost |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Australië - zuidoost |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Verenigd Koninkrijk Zuid | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| VS (overheid) - Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

> [!NOTE]
> Het XML-bestand met XML-adres azure datacenter bevat de IP-adresbereiken die worden gebruikt in de Microsoft Azure-datacenters. Het bestand bevat compute-, SQL- en opslagbereiken.
>
>Er wordt wekelijks een bijgewerkt bestand geplaatst. Het bestand weerspiegelt de momenteel geïmplementeerde bereiken en eventuele komende wijzigingen in de IP-bereiken. Nieuwe bereiken die in het bestand worden weergegeven, worden gedurende ten minste één week niet in de datacenters gebruikt. Het is een goed idee om elke week een nieuw XML-bestand te downloaden. Vervolgens u uw site bijwerken om de services die in Azure worden uitgevoerd, correct te identificeren. 

Als u een Azure ExpressRoute-gebruiker bent, moet u er rekening mee houden dat dit bestand wordt gebruikt om de BGP-advertentie (Border Gateway Protocol) van Azure-ruimte in de eerste week van elke maand bij te werken.

## <a name="next-steps"></a>Volgende stappen

- Zie [Aan de slag met Azure Automation State Configuration](automation-dsc-getting-started.md)om aan de slag te gaan met DSC in Azure Automation State Configuration .
- Zie [Onboard-machines voor beheer door Azure Automation State Configuration voor](automation-dsc-onboarding.md)meer informatie over het ingaan van knooppunten.
- Zie [Configuraties compileren in Azure Automation State Configuration](automation-dsc-compile.md)voor meer informatie over het samenstellen van DSC-configuraties, zodat u ze toewijzen aan doelknooppunten.
- Zie [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation)voor een PowerShell-cmdletreferentie.
- Zie Azure Automation State Configuration Pricing voor prijsinformatie voor [prijsinformatie.](https://azure.microsoft.com/pricing/details/automation/)
- Zie Continue implementatie naar virtuele machines met Azure [Automation State Configuration en Chocolatey](automation-dsc-cd-chocolatey.md)voor een voorbeeld van het gebruik van Azure Automation State Configuration in a continuous deployment pipeline.
