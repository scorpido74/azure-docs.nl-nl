---
title: Overzicht van Azure-Arc voor servers (preview-versie)
description: Meer informatie over het gebruik van Azure Arc voor servers voor het beheren van computers die buiten Azure worden gehost alsof het een Azure-resource is.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
keywords: Azure Automation, DSC, Power shell, desired state Configuration, update beheer, bijhouden van wijzigingen, inventaris, runbooks, Python, grafisch, hybride
ms.date: 02/03/2020
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: c38bc2d5d9a7e23c0bb3041a5cb3b5cd1da9adb1
ms.sourcegitcommit: d12880206cf9926af6aaf3bfafda1bc5b0ec7151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114239"
---
# <a name="what-is-azure-arc-for-servers-preview"></a>Wat is Azure Arc voor servers (preview-versie)

Met Azure Arc voor servers (preview) kunt u uw Windows-en Linux-machines die buiten Azure worden gehost, beheren op uw bedrijfs netwerk of een andere Cloud provider, op dezelfde manier als bij het beheren van systeem eigen virtuele Azure-machines. Wanneer een hybride machine is verbonden met Azure, wordt het een verbonden machine en wordt deze behandeld als een resource in Azure. Elke verbonden computer heeft een resource-ID, wordt beheerd als onderdeel van een resource groep binnen een abonnement en heeft voor delen van standaard Azure-constructs, zoals Azure Policy en het Toep assen van tags.

Om deze ervaring te bieden met uw hybride machines die buiten Azure worden gehost, moet de agent van de Azure Connected machine worden geïnstalleerd op elke computer die u wilt verbinden met Azure. Deze agent levert geen andere functionaliteit en vervangt de Azure [log Analytics-agent](../../azure-monitor/platform/log-analytics-agent.md)niet. De Log Analytics-agent voor Windows en Linux is vereist wanneer u het besturings systeem en de workloads die worden uitgevoerd op de machine proactief wilt bewaken, beheren met Automation-runbooks of-oplossingen, zoals Updatebeheer, of andere Azure-Services zoals [Azure Security Center](../../security-center/security-center-intro.md)gebruiken.

>[!NOTE]
>Deze preview-versie is bedoeld voor evaluatie doeleinden en wij raden u aan om essentiële productie machines niet te beheren.
>

## <a name="supported-scenarios"></a>Ondersteunde scenario's

Azure Arc voor servers (preview) ondersteunt de volgende scenario's met verbonden computers:

- Wijs [Azure Policy-gast configuraties](../../governance/policy/concepts/guest-configuration.md) toe met dezelfde ervaring als beleids toewijzing voor virtuele Azure-machines.
- Logboek gegevens die worden verzameld door de Log Analytics agent en die zijn opgeslagen in de Log Analytics werk ruimte waarin de machine is geregistreerd, bevat nu eigenschappen die specifiek zijn voor de machine, zoals de resource-ID, die kan worden gebruikt om de toegang tot bronnen in de [context](../../azure-monitor/platform/design-logs-deployment.md#access-mode) van het logboek te ondersteunen.

## <a name="supported-regions"></a>Ondersteunde regio’s

Met Azure Arc voor servers (preview) worden alleen bepaalde regio's ondersteund:

- WestUS2
- West-Europa
- WestAsia

## <a name="prerequisites"></a>Vereisten

### <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

De volgende versies van het Windows-en Linux-besturings systeem worden officieel ondersteund voor de Azure Connected machine agent: 

- Windows Server 2012 R2 en hoger
- Ubuntu 16,04 en 18,04

>[!NOTE]
>Deze preview-versie van de verbonden machine-agent voor Windows ondersteunt alleen Windows Server die is geconfigureerd voor gebruik van de Engelse taal.
>

### <a name="required-permissions"></a>Vereiste machtigingen

- Voor de onboarding van machines bent u lid van de rol **Azure Connected machine** .

- Als u een machine wilt lezen, wijzigen, opnieuw wilt indelen en verwijderen, bent u lid van de **Azure Connected machine resource Administrator** -rol. 

### <a name="azure-subscription-and-service-limits"></a>Azure-abonnement en service limieten

Voordat u uw computers met Azure-Arc voor servers (preview) configureert, moet u de limieten voor het Azure Resource Manager- [abonnement](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits---azure-resource-manager) en de [limieten van de resource groep](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) controleren om het aantal machines te plannen dat moet worden verbonden.

### <a name="networking-configuration"></a>Netwerk configuratie

De verbonden machine agent voor Linux en Windows communiceert veilig uitgaande naar Azure Arc via TCP-poort 443. Als de computer verbinding maakt via een firewall of proxy server om via internet te communiceren, raadpleegt u de vereisten hieronder om inzicht te krijgen in de vereisten voor de netwerk configuratie.

Als de uitgaande connectiviteit wordt beperkt door uw firewall of proxy server, controleert u of de Url's die hieronder worden weer gegeven niet zijn geblokkeerd. Als u alleen de IP-bereiken of domein namen toestaat die vereist zijn om de agent te laten communiceren met de-service, moet u ook toegang tot de volgende service tags en Url's toestaan.

Service Tags:

- AzureActiveDirectory
- AzureTrafficManager

Adres

| Agentresource | Beschrijving |
|---------|---------|
|management.azure.com|Azure Resource Manager|
|login.windows.net|Azure Active Directory|
|dc.services.visualstudio.com|Application Insights|
|agentserviceapi.azure-automation.net|Gastconfiguratie|
|*-agentservice-prod-1.azure-automation.net|Gastconfiguratie|
|*. his.hybridcompute.azure-automation.net|Hybride identiteits service|

Zie voor een lijst met IP-adressen voor elke servicetag/regio het JSON-bestand- [Azure IP-bereiken en de service Tags – open bare Cloud](https://www.microsoft.com/download/details.aspx?id=56519). Micro soft publiceert wekelijkse updates met elke Azure-service en de IP-bereiken die worden gebruikt. Bekijk [service Tags](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)voor meer informatie.

De Url's in de vorige tabel zijn vereist naast de IP-adres bereik gegevens van de service label, omdat de meeste services momenteel geen servicetag registratie hebben. Zo kunnen de IP-adressen worden gewijzigd. Als IP-adresbereiken vereist zijn voor de configuratie van de firewall, moet de **Cloud** -servicetag worden gebruikt om toegang tot alle Azure-Services toe te staan. Schakel de beveiligings controle of inspectie van deze Url's niet uit, en sta ze toe als andere Internet verkeer.

### <a name="register-azure-resource-providers"></a>Azure-resource providers registreren

Azure Arc voor servers (preview) is afhankelijk van de volgende Azure-resource providers in uw abonnement om deze service te kunnen gebruiken:

- **Micro soft. HybridCompute**
- **Micro soft. GuestConfiguration**

Als ze niet zijn geregistreerd, kunt u ze registreren met de volgende opdrachten:

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Azure CLI:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

U kunt de resource providers ook registreren in de Azure Portal door de stappen onder [Azure Portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)te volgen.

## <a name="connected-machine-agent"></a>Verbonden machine agent

U kunt het Azure Connected machine agent-pakket voor Windows en Linux downloaden van de hieronder vermelde locaties.

- [Windows agent Windows Installer-pakket](https://aka.ms/AzureConnectedMachineAgent) van het micro soft Download centrum.
- Linux-agent pakket wordt gedistribueerd vanuit de [pakket opslagplaats](https://packages.microsoft.com/) van micro soft met behulp van de voorkeurs indeling van het pakket voor de distributie (. RPM of. DEB).

>[!NOTE]
>Tijdens deze preview-versie is slechts één pakket vrijgegeven, dat geschikt is voor Ubuntu 16,04 of 18,04.

## <a name="install-and-configure-agent"></a>Agent installeren en configureren

Het koppelen van machines in uw hybride omgeving met Azure kan worden uitgevoerd met behulp van verschillende methoden, afhankelijk van uw vereisten. In de volgende tabel ziet u elke methode om te bepalen welke functie het beste werkt voor uw organisatie.

| Methode | Beschrijving |
|--------|-------------|
| Interactief | Installeer de agent hand matig op een enkele of een beperkt aantal machines Volg de stappen in [computers verbinden met Azure Portal](quickstart-onboard-portal.md).<br> Vanuit het Azure Portal kunt u een script genereren en uitvoeren op de computer om de installatie-en configuratie stappen van de agent te automatiseren.|
| Op schaal | Installeer en configureer de agent voor meerdere machines die de [Connect-computers volgen met behulp van een Service-Principal](quickstart-onboard-powershell.md).<br> Met deze methode maakt u een Service-Principal om machines niet-interactief te verbinden.|


## <a name="next-steps"></a>Volgende stappen

- Als u Azure-Arc voor servers (preview) wilt gaan evalueren, volgt u het artikel [verbinding maken met hybride computers met Azure via de Azure Portal](quickstart-onboard-portal.md). 