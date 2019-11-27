---
title: Overzicht van Azure Automation status configuratie
description: Een overzicht van Azure Automation State Configuration (DSC), de voor waarden en bekende problemen
keywords: Power shell DSC, desired state Configuration, Power shell DSC Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5ed18f8a8bbd8bd323dec54ca3f700c7ce168dde
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231632"
---
# <a name="azure-automation-state-configuration-overview"></a>Overzicht van Azure Automation status configuratie

Azure Automation status configuratie is een Azure-service waarmee u Power shell desired state Configuration (DSC)- [configuraties](/powershell/scripting/dsc/configurations/configurations)kunt schrijven, beheren en compileren, [DSC-resources](/powershell/scripting/dsc/resources/resources)kunt importeren en configuraties kunt toewijzen aan doel knooppunten, allemaal in de Cloud.

## <a name="why-use-azure-automation-state-configuration"></a>Waarom Azure Automation status configuratie gebruiken?

Azure Automation status configuratie biedt verschillende voor delen ten opzichte van het gebruik van DSC buiten Azure.

### <a name="built-in-pull-server"></a>Ingebouwde pull-server

Azure Automation status configuratie biedt een DSC-pull-server vergelijkbaar met de [Windows-functie DSC-service](/powershell/scripting/dsc/pull-server/pullserver) zodat doel knooppunten automatisch configuraties ontvangen, voldoen aan de gewenste status en rapporteren aan hun naleving. De ingebouwde pull-server in Azure Automation elimineert de nood zaak om uw eigen pull-server in te stellen en te onderhouden. Azure Automation kunnen virtuele of fysieke Windows-of Linux-machines in de Cloud of on-premises zijn gericht.

### <a name="management-of-all-your-dsc-artifacts"></a>Beheer van al uw DSC-artefacten

Met de configuratie van de Azure Automation-status wordt dezelfde Management laag naar de [gewenste status configuratie van Power shell](/powershell/scripting/dsc/overview/overview) verzonden als Azure Automation aanbiedingen voor Power shell-scripts.

Vanuit het Azure Portal, of vanuit Power shell, kunt u al uw DSC-configuraties,-resources en-doel knooppunten beheren.

![Scherm afbeelding van de Azure Automation pagina](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>Rapport gegevens importeren in Azure Monitor logboeken

Knoop punten die worden beheerd met Azure Automation status configuratie, verzenden gedetailleerde rapportage status gegevens naar de ingebouwde pull-server. U kunt Azure Automation status configuratie configureren om deze gegevens te verzenden naar uw Log Analytics-werk ruimte. Zie voor meer informatie over het verzenden van status gegevens van status configuratie naar uw Log Analytics-werk ruimte [Azure Automation status configuratie rapport gegevens naar Azure monitor logboeken](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Vereisten

Houd rekening met de volgende vereisten wanneer u Azure Automation State Configuration (DSC) gebruikt.

### <a name="operating-system-requirements"></a>Besturingssysteem vereisten

Voor knoop punten waarop Windows worden uitgevoerd, worden de volgende versies ondersteund:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

De zelfstandige product-SKU van [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) bevat geen implementatie van de gewenste status cumuleren zodat deze niet kan worden beheerd met Power shell DSC of Azure Automation State Configuration.

Voor knoop punten waarop Linux wordt uitgevoerd, worden de volgende distributies/versies ondersteund:

De DSC Linux-extensie ondersteunt alle Linux-distributies die worden vermeld onder [ondersteunde Linux-distributies](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions).

### <a name="dsc-requirements"></a>DSC-vereisten

Voor alle Windows-knoop punten die worden uitgevoerd in azure, wordt [WMF 5,1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) tijdens het voorbereiden geïnstalleerd.  Voor knoop punten met Windows Server 2012 en Windows 7 [wordt WinRM ingeschakeld](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency).

Voor alle Linux-knoop punten die worden uitgevoerd in azure, wordt [Power shell DSC voor Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) geïnstalleerd tijdens de onboarding.

### <a name="network-planning"></a>Particuliere netwerken configureren

Als uw knoop punten zich in een particulier netwerk bevinden, zijn de volgende poort en Url's vereist voor de status configuratie (DSC) om te communiceren met Automation:

* Poort: alleen TCP 443 is vereist voor uitgaande internet toegang.
* Globale URL: *. azure-automation.net
* Globale URL van US Gov-Virginia: *. azure-automation.us
* Agent service: https://\<workspaceId\>. agentsvc.azure-automation.net

Dit biedt netwerk connectiviteit voor het beheerde knoop punt om te communiceren met Azure Automation.
Als u DSC-resources gebruikt die communiceren tussen knoop punten, zoals de [WaitFor *-resources](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource), moet u ook verkeer tussen knoop punten toestaan.
Raadpleeg de documentatie voor elke DSC-resource voor meer informatie over deze netwerk vereisten.

#### <a name="proxy-support"></a>Proxy ondersteuning

Proxy ondersteuning voor de DSC-agent is beschikbaar in Windows versie 1809 en hoger.
Als u deze optie wilt configureren, stelt u de waarde voor **ProxyURL** en **ProxyCredential** in het- [configuratie script](automation-dsc-onboarding.md#generating-dsc-metaconfigurations) dat wordt gebruikt voor het registreren van knoop punten.
Proxy is niet beschikbaar in DSC voor eerdere versies van Windows.

Voor Linux-knoop punten ondersteunt de DSC-agent proxy en wordt de http_proxy variabele gebruikt om de URL te bepalen.

#### <a name="azure-state-configuration-network-ranges-and-namespace"></a>Netwerkbereiken en naam ruimte van Azure status configuratie

Het is raadzaam om de adressen te gebruiken die worden weer gegeven bij het definiëren van uitzonde ringen. Voor IP-adressen kunt u de [IP-adresbereiken van Microsoft Azure Data Center](https://www.microsoft.com/download/details.aspx?id=41653)downloaden. Dit bestand wordt wekelijks bijgewerkt en heeft de huidige geïmplementeerde bereiken en eventuele toekomstige wijzigingen in de IP-bereiken.

Als u een Automation-account hebt dat is gedefinieerd voor een specifieke regio, kunt u de communicatie beperken tot dat regionale Data Center. De volgende tabel bevat de DNS-record voor elke regio:

| **Regio** | **DNS-record** |
| --- | --- |
| VS - west-centraal | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| VS - zuid-centraal |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| VS - oost   | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
| VS - oost 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Canada-Midden |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europa - west |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Europa - noord |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Azië - zuidoost |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| India - centraal |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Japan - oost |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Australië - zuidoost |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| VK - zuid | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov - Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Down load het [Azure Data Center IP-adres](https://www.microsoft.com/download/details.aspx?id=41653) XML-bestand in het micro soft Download centrum voor een lijst met IP-adressen van regio's in plaats van regio namen.

> [!NOTE]
> Het XML-bestand met IP-adressen van Azure Data Center geeft een lijst van de IP-adresbereiken die worden gebruikt in de Microsoft Azure data centers. Het bestand bevat compute-, SQL-en Storage-bereiken.
>
>Er wordt wekelijks een bijgewerkt bestand geplaatst. Het bestand weerspiegelt de huidige geïmplementeerde bereiken en eventuele toekomstige wijzigingen in de IP-bereiken. Nieuwe bereiken die in het bestand worden weer gegeven, worden gedurende ten minste één week niet gebruikt in de data centers.
>
> Het is een goed idee om elke week het nieuwe XML-bestand te downloaden. Werk vervolgens uw site bij om de services die in Azure worden uitgevoerd, correct te identificeren. Gebruikers van Azure ExpressRoute moeten weten dat dit bestand wordt gebruikt om de Border Gateway Protocol (BGP)-advertentie van Azure Space bij te werken in de eerste week van elke maand.

## <a name="next-steps"></a>Volgende stappen

- Zie aan de slag [met de configuratie van de Azure Automation-status](automation-dsc-getting-started.md) om aan de slag te gaan.
- Voor meer informatie over het voorbereiden van knoop punten, Zie [onboarding machines voor beheer door Azure Automation status configuratie](automation-dsc-onboarding.md)
- Zie [configuraties compileren in azure Automation status configuratie](automation-dsc-compile.md) voor meer informatie over het compileren van DSC-configuraties zodat u ze aan doel knooppunten kunt toewijzen.
- Zie [Azure Automation status configuratie-cmdlets](/powershell/module/azurerm.automation/#automation) voor informatie over de Power shell-cmdlet.
- Zie [prijzen voor Azure Automation status configuratie](https://azure.microsoft.com/pricing/details/automation/) voor prijs informatie.
- Voor een voor beeld van het gebruik van Azure Automation status configuratie in een pijp lijn voor continue implementatie gaat u naar [continue implementatie met behulp van Azure Automation-status configuratie en chocolade](automation-dsc-cd-chocolatey.md)
