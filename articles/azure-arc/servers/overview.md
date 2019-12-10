---
title: Overzicht van Azure Arc voor servers
description: Meer informatie over het gebruik van Azure Arc voor servers voor het automatiseren van de levens cyclus van infra structuur en toepassingen.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: bobbytreed
ms.author: robreed
keywords: Azure Automation, DSC, Power shell, desired state Configuration, update beheer, bijhouden van wijzigingen, inventaris, runbooks, Python, grafisch, hybride
ms.date: 11/04/2019
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: d091b89342570b73ccde5fe496a3432102617918
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951425"
---
# <a name="what-is-azure-arc-for-servers"></a>Wat is Azure Arc voor servers?

Met Azure Arc voor servers kunt u computers beheren die zich buiten Azure bevinden.
Wanneer een niet-Azure-machine is verbonden met Azure, wordt het een **verbonden machine** en wordt deze behandeld als een resource in Azure. Elke **verbonden computer** heeft een resource-id, wordt beheerd als onderdeel van een resource groep in een abonnement en voor delen van standaard Azure-constructs, zoals Azure Policy en tagging.

Op elke machine moet een agent pakket worden geïnstalleerd om het te verbinden met Azure. In de rest van dit document wordt het proces gedetailleerd uitgelegd.

Computers krijgen de status **verbonden** of **losgekoppeld** op basis van hoe recent de agent is ingecheckt. Elke check-in wordt een heartbeat genoemd. Als een computer in de afgelopen vijf minuten niet is ingecheckt, wordt deze weer gegeven als offline totdat de verbinding is hersteld.  <!-- For more information on troubleshooting agent connectivity, see [Troubleshooting Azure Arc for servers](troubleshoot/arc-for-servers.md). -->

![Verbonden servers](./media/overview/arc-for-servers-onboarded-servers.png)

## <a name="clients"></a>Clients

### <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

In de open bare preview ondersteunen we het volgende:

- Windows Server 2012 R2 en hoger
- Ubuntu 16,04 en 18,04

De open bare preview-versie is ontworpen voor evaluatie doeleinden en mag niet worden gebruikt om kritieke productie resources te beheren.

## <a name="azure-subscription-and-service-limits"></a>Azure-abonnement en service limieten

Lees de Azure Resource Manager limieten en plan het aantal computers dat moet worden verbonden volgens de richt lijn die wordt vermeld voor het [abonnement](../../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager)en voor de [resource groepen](../../azure-subscription-service-limits.md#resource-group-limits). In het bijzonder is er standaard een limiet van 800 servers per resource groep.

## <a name="networking-configuration"></a>Netwerk configuratie

Tijdens de installatie en runtime vereist de agent connectiviteit met **Azure Arc service-eind punten**. Als uitgaande connectiviteit wordt geblokkeerd door firewalls, moet u ervoor zorgen dat de volgende Url's niet standaard worden geblokkeerd. Alle verbindingen zijn uitgaand van de agent naar Azure en zijn beveiligd met **SSL**. Al het verkeer kan via een **https** -proxy worden doorgestuurd. Als u de IP-bereiken of domein namen toestaat waarmee de servers verbinding mogen maken, moet u poort 443 toegang tot de volgende service tags en DNS-namen toestaan.

Service Tags:

* AzureActiveDirectory
* AzureTrafficManager

Zie voor een lijst met IP-adressen voor elke servicetag/regio het JSON-bestand- [Azure IP-bereiken en de service Tags – open bare Cloud](https://www.microsoft.com/download/details.aspx?id=56519). Micro soft publiceert wekelijkse updates met elke Azure-service en de IP-bereiken die worden gebruikt. Zie [service Tags](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)voor meer informatie.

Deze DNS-namen worden naast de informatie over het IP-adres bereik van de service-tag vermeld, omdat het meren deel van de services momenteel geen servicetag registratie heeft en, als zodanig, de Ip's kunnen worden gewijzigd. Als er IP-bereiken zijn vereist voor de configuratie van de firewall, moet u de **Cloud** -servicetag gebruiken om toegang tot alle Azure-Services toe te staan. Schakel de beveiligings controle of inspectie van deze Url's niet uit, maar sta toe als andere Internet verkeer.

| Domeinomgeving | Vereiste Azure-service-eindpunten |
|---------|---------|
|management.azure.com|Azure Resource Manager|
|login.windows.net|Azure Active Directory|
|dc.services.visualstudio.com|Application Insights|
|agentserviceapi.azure-automation.net|Gastconfiguratie|
|*-agentservice-prod-1.azure-automation.net|Gastconfiguratie|
|*. his.hybridcompute.azure-automation.net|Hybride identiteits service|

### <a name="installation-network-requirements"></a>Vereisten voor installatie netwerk

Down load het pakket voor de [Azure Connected machine agent](https://aka.ms/AzureConnectedMachineAgent) via onze officiële distributie servers de onderstaande sites moeten toegankelijk zijn vanuit uw omgeving. U kunt ervoor kiezen om het pakket te downloaden naar een bestands share en de agent van daaruit te installeren. In dit geval moet het onboarding-script dat is gegenereerd op basis van de Azure Portal mogelijk worden gewijzigd.

Windows:

* `aka.ms`
* `download.microsoft.com`

Linux:

* `aka.ms`
* `packages.microsoft.com`

Zie de sectie [proxy server configuratie](quickstart-onboard-powershell.md#proxy-server-configuration)voor informatie over het configureren van de agent voor het gebruik van uw proxy.

## <a name="register-the-required-resource-providers"></a>De vereiste resource providers registreren

Als u Azure Arc voor servers wilt gebruiken, moet u de vereiste resource providers registreren.

* **Microsoft.HybridCompute**
* **Micro soft. GuestConfiguration**

U kunt de resource providers registreren met de volgende opdrachten:

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Azure CLI:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

U kunt de resource providers ook registreren met behulp van de portal door de stappen onder [Azure Portal](../../azure-resource-manager/resource-manager-supported-services.md#azure-portal)te volgen.

## <a name="machine-changes-after-installing-the-agent"></a>Computer wijzigingen na de installatie van de agent

Als er een oplossing voor het bijhouden van wijzigingen in uw omgeving is geïmplementeerd, kunt u de onderstaande lijst gebruiken om de wijzigingen die zijn aangebracht door het **AzCMAgent-installatie pakket (Azure Connected machine agent)** , bij te houden, te identificeren en toe te staan.

Nadat u de agent hebt geïnstalleerd, ziet u de volgende wijzigingen die zijn aangebracht op uw servers.

### <a name="windows"></a>Windows

Geïnstalleerde services:

* `Himds`: de service **Azure Connected machine agent** .
* `Dscservice` of `gcd`-de **gast configuratie** service.

Bestanden die zijn toegevoegd aan de server:

* `%ProgramFiles%\AzureConnectedMachineAgent\*.*` locatie van bestanden van een **met Azure verbonden machine agent** .
* `%ProgramData%\GuestConfig\*.*` - **gast configuratie** Logboeken.

Locaties van register sleutels:

* `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Connected Machine Agent`-register sleutels voor de **Azure Connected machine agent**.

### <a name="linux"></a>Linux

Geïnstalleerde services:

* `Himdsd`: de service **Azure Connected machine agent** .
* `dscd` of `gcd`-de **gast configuratie** service.

Bestanden die zijn toegevoegd aan de server:

* `/var/opt/azcmagent/**` locatie van bestanden van een **met Azure verbonden machine agent** .
* `/var/lib/GuestConfig/**` - **gast configuratie** Logboeken.

## <a name="supported-scenarios"></a>Ondersteunde scenario 's

Nadat u een knoop punt hebt geregistreerd, kunt u beginnen met het beheren van uw knoop punten met behulp van andere Azure-Services.

In de open bare preview worden de volgende scenario's ondersteund voor **verbonden computers**.

## <a name="guest-configuration"></a>Gastconfiguratie

Nadat u de computer hebt verbonden met Azure, kunt u Azure-beleid toewijzen aan **verbonden computers** met dezelfde ervaring als beleids toewijzing voor virtuele Azure-machines.

Zie [de gast configuratie van Azure Policy begrijpen](../../governance/policy/concepts/guest-configuration.md)voor meer informatie.

De logboeken van de gast configuratie agent voor een **verbonden computer** bevinden zich op de volgende locaties:

* Windows - `%ProgramFiles%\AzureConnectedMachineAgent\logs\dsc.log`
* Linux:-`/opt/logs/dsc.log`

## <a name="log-analytics"></a>Log Analytics

Logboek gegevens die worden verzameld door de [micro soft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) en opgeslagen in log Analytics werk ruimte bevatten nu eigenschappen die specifiek zijn voor de computer, zoals **ResourceID**, die kunnen worden gebruikt voor de toegang tot de Resource Center-Logboeken.

- Op computers waarop de MMA-agent al is geïnstalleerd, wordt de functionaliteit van **Azure Arc** ingeschakeld via bijgewerkte Management Packs.
- [MMA-agent versie 10.20.18011 of hoger](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows#agent-and-vm-extension-version) is vereist voor Azure Arc voor servers-integratie.
- Bij het uitvoeren van query's voor logboek gegevens in [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview), bevat het geretourneerde gegevens schema de Hybrid **ResourceID** in de vorm `/subscriptions/<SubscriptionId/resourceGroups/<ResourceGroup>/providers/Microsoft.HybridCompute/machines/<MachineName>`.

Zie [aan de slag met log Analytics in azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)voor meer informatie.

<!-- MMA agent version 10.20.18011 and later -->

## <a name="next-steps"></a>Volgende stappen

Er zijn twee methoden om computers te verbinden met Azure Arc voor servers.

* **Interactief** : Volg de Quick Start van de [Portal](quickstart-onboard-portal.md) om een script uit de portal te genereren en op de computer uit te voeren. Dit is de beste optie als u een computer tegelijk verbindt.
* **Op schaal** : Volg de [Power shell-Snelstartgids](quickstart-onboard-powershell.md) om een service-principal te maken voor het niet-interactief verbinden van machines.
