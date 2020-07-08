---
title: Persoonlijke Azure-koppeling gebruiken om netwerken veilig te verbinden met Azure Automation
description: Persoonlijke Azure-koppeling gebruiken om netwerken veilig te verbinden met Azure Automation
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 06/22/2020
ms.subservice: ''
ms.openlocfilehash: fa473591355ef9e1ee582dd9c9b820dfa2f93f36
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85269005"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-automation"></a>Persoonlijke Azure-koppeling gebruiken om netwerken veilig te verbinden met Azure Automation

Een privé-eindpunt in Azure is een netwerkinterface waarmee u privé en veilig verbinding maakt met een service die door Azure Private Link mogelijk wordt gemaakt. Persoonlijk eind punt gebruikt een privé-IP-adres uit uw VNet, waardoor de Automation-Service in uw VNet effectief wordt. Netwerk verkeer tussen de computers in het VNet en het Automation-account passeert over het VNet en een persoonlijke koppeling in het micro soft backbone-netwerk, waardoor de bloot stelling van het open bare Internet wordt voor komen.

U hebt bijvoorbeeld een VNet waarvoor u uitgaande internet toegang hebt uitgeschakeld. U wilt uw Automation-account echter privé openen en automatiserings functies zoals webhooks, status configuratie en runbook-taken gebruiken voor Hybrid Runbook Workers. Daarnaast wilt u dat gebruikers alleen via het VNET toegang hebben tot het Automation-account. Dit kan worden bereikt door persoonlijke eind punten te implementeren.

In dit artikel wordt beschreven hoe u een persoonlijk eind punt kunt instellen met uw Automation-account.

![Conceptueel overzicht van privé koppeling voor Azure Automation](./media/private-link-security/private-endpoints-automation.png)

## <a name="advantages"></a>Voordelen

Met een persoonlijke koppeling kunt u het volgende doen:

- Maak privé verbinding met Azure Automation zonder open bare toegang tot het netwerk te openen.
- Maak privé verbinding met Azure Monitor Log Analytics werk ruimte zonder open bare toegang tot het netwerk.

    >[!NOTE]
    >Dit is vereist als uw Automation-account is gekoppeld aan een Log Analytics werk ruimte om taak gegevens door te sturen, en wanneer u functies hebt ingeschakeld, zoals Updatebeheer, Wijzigingen bijhouden en inventaris, status configuratie of VM's buiten bedrijfsuren starten/stoppen. Zie voor meer informatie over persoonlijke koppeling voor Azure Monitor de [koppeling Azure private gebruiken om netwerken veilig te verbinden met Azure monitor](../../azure-monitor/platform/private-link-security.md).

- Zorg ervoor dat uw automatiserings gegevens alleen toegankelijk zijn via geautoriseerde particuliere netwerken.
- Voorkom gegevens exfiltration uit uw particuliere netwerken door uw Azure Automation-bron te definiëren die verbinding maakt via uw persoonlijke eind punt.
- U kunt uw privé on-premises netwerk veilig verbinden met Azure Automation met behulp van ExpressRoute en een persoonlijke koppeling.
- Bewaar al het verkeer binnen het Microsoft Azure backbone-netwerk.

Zie [belang rijke voor delen van een persoonlijke koppeling](../../private-link/private-link-overview.md#key-benefits)voor meer informatie.

## <a name="how-it-works"></a>Uitleg

Met Azure Automation persoonlijke koppeling worden een of meer privé-eind punten (en dus ook de virtuele netwerken in) verbonden met de resource van uw Automation-account. Deze eind punten zijn machines die gebruikmaken van webhooks om een runbook te starten, machines die als host fungeren voor de Hybrid Runbook Worker Role en DSC-knoop punten.

Nadat u privé-eind punten voor Automation hebt gemaakt, worden elk van de open bare automatiserings-Url's, waarmee u of een machine rechtstreeks contact kan opnemen, toegewezen aan één persoonlijk eind punt in uw VNet.

### <a name="webhook-scenario"></a>Webhook-scenario

U kunt runbooks starten door een bericht te plaatsen op de webhook-URL. De URL ziet er bijvoorbeeld als volgt uit:`https://<automationAccountId>.webhooks. <region>.azure-automation.net/webhooks?token=gzGMz4SMpqNo8gidqPxAJ3E%3d`

### <a name="state-configuration-agentsvc-scenario"></a>Scenario met status configuratie (agentsvc)

Status configuratie biedt u Azure Configuration Management-service waarmee u Power shell desired state Configuration (DSC)-configuraties kunt schrijven, beheren en compileren voor knoop punten in elke Cloud of on-premises Data Center.

De agent op de computer registreert bij de DSC-service en gebruikt vervolgens het service-eind punt om de DSC-configuratie op te halen. Het eind punt van de Agent service ziet er als volgt uit: `https://<automationAccountId>.agentsvc.<region>.azure-automation.net` .

De URL voor openbaar & privé-eind punt zou echter hetzelfde moeten zijn, maar zou worden toegewezen aan een privé-IP-adres wanneer privé-koppeling is ingeschakeld.

## <a name="planning-based-on-your-network"></a>Planning op basis van uw netwerk

Voordat u uw Automation-account bron instelt, moet u rekening houden met de vereisten voor netwerk isolatie. Evalueer de toegang tot het open bare Internet van uw virtuele netwerken en de toegangs beperkingen voor uw Automation-account (inclusief het instellen van een persoonlijk koppelings bereik voor Azure Monitor Logboeken als deze zijn geïntegreerd met uw Automation-account).

### <a name="connect-to-a-private-endpoint"></a>Verbinding maken met een persoonlijk eind punt

Maak een persoonlijk eind punt om het netwerk te verbinden. U kunt deze taak uitvoeren in het [Azure Portal-privé koppelings centrum](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints). Zodra uw wijzigingen in publicNetworkAccess en privé-koppeling zijn toegepast, kan het tot 35 minuten duren voordat ze van kracht worden.

In deze sectie maakt u een persoonlijk eind punt voor uw Automation-account.

1. Selecteer in de linkerbovenhoek van het scherm **een resource maken > netwerk > persoonlijk koppelings centrum (preview)**.

2. Selecteer in **Private Link-centrum – Overzicht** bij de optie **Een particuliere verbinding met een service maken** de optie **Start**.

3. Typ of selecteer in **Een virtuele machine maken - Basisprincipes** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | **PROJECTGEGEVENS** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt deze in de vorige sectie gemaakt.  |
    | **EXEMPLAARDETAILS** |  |
    | Naam | Voer uw *PrivateEndpoint*in. |
    | Regio | Selecteer **YourRegion**. |
    |||

4. Selecteer **Volgende: Resource**.

5. Typ of selecteer in **Een privé-eindpunt maken – Resource** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    |Verbindingsmethode  | Selecteer Verbinding maken met een Azure-resource in mijn directory.|
    | Abonnement| Selecteer uw abonnement. |
    | Resourcetype | Selecteer **micro soft. Automation/automationAccounts**. |
    | Resource |*MyAutomationAccount* selecteren|
    |Stel subresource in |Selecteer *webhook* of *DSCAndHybridWorker* , afhankelijk van uw scenario.|
    |||

6. Selecteer **Volgende: Configuratie**.

7. Voer in **een persoonlijk eind punt maken-configuratie**de volgende gegevens in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    |**NETWERKEN**| |
    | Virtueel netwerk| Selecteer *MyVirtualNetwork*. |
    | Subnet | Selecteer *mySubnet*. |
    |**INTEGRATIE VAN PRIVÉ-DNS**||
    |Integreren met privé-DNS-zone |Selecteer **Ja**. |
    |Privé-DNS-zone |Selecteer *(nieuw) privatelink. Azure-Automation.net* |
    |||

8. Selecteer **Controleren + maken**. De pagina **Beoordelen en maken** wordt weergegeven, waar uw configuratie wordt gevalideerd in Azure.

9. Als u het bericht **Validatie geslaagd** ziet, selecteert u **Maken**.

Selecteer **privé-eind punten** in het **persoonlijke koppelings centrum (preview)** om uw persoonlijke koppelings bron weer te geven.

![Persoonlijke resource koppeling voor Automation](./media/private-link-security/private-link-automation-resource.png)

Selecteer de resource om alle details weer te geven. Hiermee maakt u een nieuw persoonlijk eind punt voor uw Automation-account en wijst u dit toe aan een persoonlijk IP-adres van uw virtuele netwerk. De **verbindings status** wordt weer gegeven als **goedgekeurd**.

Op dezelfde manier wordt een unieke Fully Qualified Domain Name (FQDN) gemaakt voor de status configuratie (agentsvc) en voor de Hybrid Runbook Worker job runtime (jrds). Elk daarvan krijgt een afzonderlijk IP-adres van uw VNet en de **verbindings status** wordt weer gegeven als **goedgekeurd**.

Als de service gebruiker RBAC-machtigingen heeft voor de Automation-resource, kan deze de automatische goedkeurings methode kiezen. In dit geval is er geen actie vereist van de service provider en wordt de verbinding automatisch goedgekeurd wanneer de aanvraag de Automation provider-resource bereikt.

## <a name="set-public-network-access-flags"></a>Toegangs vlaggen voor openbaar netwerk instellen

U kunt een Automation-account configureren om alle open bare configuraties te weigeren en alleen verbindingen via persoonlijke eind punten toe te staan om de netwerk beveiliging verder uit te breiden. Als u de toegang tot het Automation-account alleen wilt beperken vanuit het VNet en geen toegang via open bare Internet wilt toestaan, kunt u de `publicNetworkAccess` eigenschap instellen op `$true` .

Wanneer de instelling **open bare netwerk toegang weigeren** is ingesteld op `true` , worden alleen verbindingen via persoonlijke eind punten toegestaan en worden alle verbindingen via open bare eind punten geweigerd met een fout bericht.

Het volgende Power shell-script laat zien hoe `Get` en `Set` de eigenschap **open bare netwerk toegang** op het niveau van het Automation-account:

```powershell
$account = Get-AzResource -ResourceType Microsoft.Automation/automationAccounts -ResourceGroupName "<resourceGroupName>" -Name "<automationAccountName>" -ApiVersion "2020-01-13-preview"
$account.Properties | Add-Member -Name 'publicNetworkAccess' -Type NoteProperty -Value $true
$account | Set-AzResource -Force -ApiVersion "2020-01-13-preview"
```

## <a name="dns-configuration"></a>DNS-configuratie

Wanneer u verbinding maakt met een persoonlijke koppelings bron met behulp van een FQDN als onderdeel van de connection string, is het belang rijk om uw DNS-instellingen correct te configureren om te worden omgezet in het toegewezen privé-IP-adres. Bestaande Azure-Services hebben mogelijk al een DNS-configuratie die kan worden gebruikt om verbinding te maken via een openbaar eind punt. Deze moet worden overschreven om verbinding te maken via uw persoonlijke eind punt.

De netwerk interface die aan het persoonlijke eind punt is gekoppeld, bevat de volledige set informatie die nodig is voor het configureren van uw DNS, inclusief FQDN-en privé IP-adressen die zijn toegewezen voor een bepaalde persoonlijke koppelings bron.

U kunt de volgende opties gebruiken om uw DNS-instellingen voor privé-eind punten te configureren:

* Het hostbestand gebruiken (alleen aanbevolen voor testen). U kunt het hostbestand op een virtuele machine gebruiken om het gebruik van DNS voor naam omzetting eerst te negeren.

* Gebruik een [privé-DNS-zone](../../dns/private-dns-privatednszone.md). U kunt privé-DNS-zones gebruiken om de DNS-omzetting voor een bepaald persoonlijk eind punt te onderdrukken. Een persoonlijke DNS-zone kan worden gekoppeld aan uw virtuele netwerk om specifieke domeinen op te lossen. Als u de agent op de virtuele machine wilt inschakelen om te communiceren via het persoonlijke eind punt, maakt u een Privé-DNS record als `privatelink.azure-automation.net` . Voeg *een nieuwe DNS-* record toewijzing toe aan het IP-adres van het persoonlijke eind punt.

* Gebruik uw DNS-doorstuur server (optioneel). U kunt de DNS-doorstuur server gebruiken om de DNS-omzetting voor een bepaalde persoonlijke koppelings bron te negeren. Als uw [DNS-server](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) wordt gehost op een virtueel netwerk, kunt u een DNS-doorstuur regel maken voor het gebruik van een privé-DNS-zone om de configuratie voor alle persoonlijke koppelings bronnen te vereenvoudigen.

Zie voor meer informatie [Azure private endpoint DNS-configuratie](../../private-link/private-endpoint-dns.md).

## <a name="next-steps"></a>Volgende stappen

Zie [Wat is Azure private endpoint?](../../private-link/private-endpoint-overview.md)voor meer informatie over privé-eind punten.