---
title: Overzicht van Azure Arc voor servers (voorbeeld)
description: Meer informatie over het gebruik van Azure Arc voor servers om machines te beheren die buiten Azure worden gehost alsof het een Azure-bron is.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
keywords: azure automation, DSC, powershell, desired state configuration, update management, change tracking, inventory, runbooks, python, graphical, hybrid
ms.date: 03/24/2020
ms.topic: overview
ms.openlocfilehash: 5ffe845178fffc252e95794bfb58801c9bff16a7
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411230"
---
# <a name="what-is-azure-arc-for-servers-preview"></a>Wat is Azure Arc voor servers (voorbeeld)

Met Azure Arc voor servers (preview) u uw Windows- en Linux-machines die buiten Azure worden gehost, beheren op uw bedrijfsnetwerk of andere cloudprovider, net als de manier waarop u native Azure-virtuele machines beheert. Wanneer een hybride machine is verbonden met Azure, wordt deze een verbonden machine en wordt deze behandeld als een bron in Azure. Elke verbonden machine heeft een Resource-id, wordt beheerd als onderdeel van een resourcegroep binnen een abonnement en profiteert van standaard Azure-constructies zoals Azure Policy en het toepassen van tags.

Om deze ervaring te bieden met uw hybride machines die buiten Azure worden gehost, moet de Azure Connected Machine-agent worden geïnstalleerd op elke machine die u van plan bent verbinding te maken met Azure. Deze agent levert geen andere functionaliteit en vervangt de Azure [Log Analytics-agent](../../azure-monitor/platform/log-analytics-agent.md)niet. De agent Logboekanalyse voor Windows en Linux is vereist wanneer u het besturingssysteem en de workloads die op de machine worden uitgevoerd proactief wilt controleren, deze wilt beheren met behulp van automatiseringsrunboeken of oplossingen zoals Updatebeheer of andere Azure-services zoals [Azure Security Center](../../security-center/security-center-intro.md)wilt gebruiken.

>[!NOTE]
>Deze preview-release is bedoeld voor evaluatiedoeleinden en we raden u aan geen kritieke productiemachines te beheren.
>

## <a name="supported-scenarios"></a>Ondersteunde scenario's

Azure Arc voor servers (preview) ondersteunt de volgende scenario's met verbonden machines:

- Wijs [gastconfiguraties van Azure Policy toe](../../governance/policy/concepts/guest-configuration.md) met dezelfde ervaring als beleidstoewijzing voor virtuele Azure-machines.
- Loggegevens die zijn verzameld door de loganalytics-agent en zijn opgeslagen in de log [analytics-werkruimte](../../azure-monitor/platform/design-logs-deployment.md#access-mode) waarmee de machine is geregistreerd, bevatten nu eigenschappen die specifiek zijn voor de machine, zoals Resource ID, die kunnen worden gebruikt om toegang tot broncontextlogboeken te ondersteunen.

## <a name="supported-regions"></a>Ondersteunde regio’s

Met Azure Arc voor servers (voorbeeld) worden alleen bepaalde regio's ondersteund:

- WestUS2 WestUS2
- West-Europa
- WestAzië

In de meeste gevallen moet de locatie die u selecteert wanneer u het installatiescript maakt, de Azure-regio zijn die geografisch het dichtst bij de locatie van uw machine ligt. Gegevens in rust worden opgeslagen in de Azure-geografie met de regio die u opgeeft, wat ook van invloed kan zijn op uw regiokeuze als u vereisten voor gegevensresidentie hebt. Als de Azure-regio waarmee uw machine is verbonden, wordt beïnvloed door een storing, wordt de verbonden machine niet beïnvloed, maar kunnen beheerbewerkingen met Azure mogelijk niet worden voltooid. Voor veerkracht in het geval van een regionale storing is het het beste om de machines op elke locatie te verbinden met een andere Azure-regio als u meerdere locaties hebt die een geografisch redundante service bieden.

## <a name="prerequisites"></a>Vereisten

### <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

De volgende versies van het Windows- en Linux-besturingssysteem worden officieel ondersteund voor de Azure Connected Machine-agent: 

- Windows Server 2012 R2 en hoger (inclusief Windows Server Core)
- Ubuntu 16.04 en 18.04
- CentOS Linux 7
- SUSE Linux Enterprise Server (SLES) 15
- Red Hat Enterprise Linux (RHEL) 7
- Amazon Linux 7

>[!NOTE]
>Deze preview-versie van de agent Connected Machine voor Windows ondersteunt alleen Windows Server die is geconfigureerd om de Engelse taal te gebruiken.
>

### <a name="required-permissions"></a>Vereiste machtigingen

- Voor aan boord van machines bent u lid van de azure **connected machine onboarding-rol.**

- Als u een machine wilt lezen, wijzigen, opnieuw wilt ingebouwden en verwijderen, bent u lid van de rol **Azure Connected Machine Resource Administrator.** 

### <a name="azure-subscription-and-service-limits"></a>Azure-abonnements- en servicelimieten

Voordat u uw machines configureert met Azure Arc voor servers (voorbeeld), moet u de azure resource [manager-abonnementslimieten](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) en [resourcegroeplimieten controleren](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) om te plannen voor het aantal machines dat moet worden aangesloten.

## <a name="tls-12-protocol"></a>TLS 1.2-protocol

Om de beveiliging van gegevens die onderweg zijn naar Azure te garanderen, raden we u ten zeerste aan om machine te configureren voor tls(1.2) transportlayer security.2. Oudere versies van TLS/Secure Sockets Layer (SSL) zijn kwetsbaar bevonden en hoewel ze momenteel nog werken om achterwaartse compatibiliteit mogelijk te maken, worden ze **niet aanbevolen.** 

|Platform/Taal | Ondersteuning | Meer informatie |
| --- | --- | --- |
|Linux | Linux-distributies zijn meestal afhankelijk van [OpenSSL](https://www.openssl.org) voor TLS 1.2-ondersteuning. | Controleer de [OpenSSL Changelog](https://www.openssl.org/news/changelog.html) om te bevestigen dat uw versie van OpenSSL wordt ondersteund.|
| Windows Server 2012 R2 en hoger | Standaard ondersteund en ingeschakeld. | Om te bevestigen dat u nog steeds de [standaardinstellingen gebruikt.](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings)|

### <a name="networking-configuration"></a>Netwerkconfiguratie

De Connected Machine-agent voor Linux en Windows communiceert veilig outbound naar Azure Arc via TCP-poort 443. Als de machine verbinding maakt via een firewall of proxyserver om via internet te communiceren, controleert u onderstaande vereisten om inzicht te krijgen in de vereisten voor netwerkconfiguratie.

Als de uitgaande connectiviteit wordt beperkt door uw firewall of proxyserver, controleert u of de onderstaande URL's niet worden geblokkeerd. Als u alleen de IP-bereiken of domeinnamen toestaat die nodig zijn om de agent met de service te laten communiceren, moet u ook toegang verlenen tot de volgende servicetags en URL's.

Servicetags:

- AzureActiveDirectory
- AzureTrafficManager

Urls:

| Agentresource | Beschrijving |
|---------|---------|
|management.azure.com|Azure Resource Manager|
|login.windows.net|Azure Active Directory|
|dc.services.visualstudio.com|Application Insights|
|agentserviceapi.azure-automation.net|Gastconfiguratie|
|*-agentservice-prod-1.azure-automation.net|Gastconfiguratie|
|*.his.hybridcompute.azure-automation.net|Hybride identiteitsservice|

Zie het JSON-bestand - [Azure IP Ranges and Service Tags – Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519)voor een lijst met IP-adressen voor elke servicetag/regio. Microsoft publiceert wekelijkse updates met elke Azure-service en de IP-bereiken die het gebruikt. Voor meer informatie, bekijk [Servicetags](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

De URL's in de vorige tabel zijn vereist naast de IP-adresbereikgegevens van servicetagomdat de meeste services momenteel geen servicetagregistratie hebben. Als zodanig zijn de IP-adressen onderhevig aan wijzigingen. Als IP-adresbereiken vereist zijn voor uw firewallconfiguratie, moet de **AzureCloud-servicetag** worden gebruikt om toegang te verlenen tot alle Azure-services. Schakel de beveiligingscontrole of inspectie van deze URL's niet uit, laat ze niet zoals u zou doen met ander internetverkeer.

### <a name="register-azure-resource-providers"></a>Azure-bronproviders registreren

Azure Arc voor servers (preview) is afhankelijk van de volgende Azure-bronproviders in uw abonnement om deze service te kunnen gebruiken:

- **Microsoft.HybridCompute Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

Als ze niet zijn geregistreerd, u ze registreren met behulp van de volgende opdrachten:

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

U de bronproviders ook registreren in de Azure-portal door de stappen onder [Azure-portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)te volgen.

## <a name="connected-machine-agent"></a>Aangesloten machineagent

U het Azure Connected Machine-agentpakket voor Windows en Linux downloaden van de onderstaande locaties.

- [Windows-agent Windows Installer-pakket](https://aka.ms/AzureConnectedMachineAgent) vanuit het Microsoft Download Center.
- Linux agent pakket wordt gedistribueerd vanuit microsoft's [pakket repository](https://packages.microsoft.com/) met behulp van de voorkeur pakket formaat voor de distributie (. RPM of . DEB).

>[!NOTE]
>Tijdens deze preview is er slechts één pakket uitgebracht, dat geschikt is voor Ubuntu 16.04 of 18.04.

De Azure Connected Machine-agent voor Windows en Linux kan handmatig of automatisch worden geüpgraded naar de nieuwste release, afhankelijk van uw vereisten. Zie [hier](manage-agent.md) voor meer informatie

### <a name="agent-status"></a>Agentstatus

De agent Connected Machine stuurt elke 5 minuten een regelmatig heartbeatbericht naar de service. Als de service deze hartslagberichten van een machine niet meer ontvangt, wordt die machine als offline beschouwd en wordt de status automatisch binnen 15 tot 30 minuten gewijzigd in **Losgekoppeld** in de portal. Na ontvangst van een volgend heartbeatbericht van de agent Verbonden machine wordt de status automatisch gewijzigd in **Verbonden**.

## <a name="install-and-configure-agent"></a>Agent installeren en configureren

Het rechtstreeks verbinden van machines in uw hybride omgeving met Azure kan met verschillende methoden worden uitgevoerd, afhankelijk van uw vereisten. In de volgende tabel wordt elke methode benadrukt om te bepalen welke het beste werkt voor uw organisatie.

| Methode | Beschrijving |
|--------|-------------|
| Interactief | Installeer de agent handmatig op een enkel of klein aantal machines volgens de stappen in [Connect-machines vanuit Azure-portal.](onboard-portal.md)<br> Vanuit de Azure-portal u een script genereren en uitvoeren op de machine om de installatie- en configuratiestappen van de agent te automatiseren.|
| Op schaal | Installeer en configureer de agent voor meerdere machines volgens de [Connect-machines met behulp van een Service Principal.](onboard-service-principal.md)<br> Deze methode maakt een serviceprincipal om machines niet-interactief aan te sluiten.|
| Op schaal | Installeer en configureer de agent voor meerdere machines volgens de methode [Windows PowerShell DSC](onboard-dsc.md).<br> Deze methode maakt gebruik van een serviceprincipal om machines niet-interactief te verbinden met PowerShell DSC. |

## <a name="next-steps"></a>Volgende stappen

- Als u azure arc voor servers wilt evalueren (voorbeeld), volgt u het artikel [Hybride machines verbinden met Azure vanuit de Azure-portal](onboard-portal.md). 
