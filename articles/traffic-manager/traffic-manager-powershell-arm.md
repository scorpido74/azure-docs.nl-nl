---
title: PowerShell gebruiken om Traffic Manager in Azure te beheren
description: Ga met dit leerpad aan de slag met Azure PowerShell voor Traffic Manager.
services: traffic-manager
documentationcenter: na
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: rohink
ms.openlocfilehash: 7886764a69eefa68be071a801bea65ae995fbdc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938497"
---
# <a name="using-powershell-to-manage-traffic-manager"></a>PowerShell gebruiken om Traffic Manager te beheren

Azure Resource Manager is de voorkeursbeheerinterface voor services in Azure. Azure Traffic Manager-profielen kunnen worden beheerd met API's en hulpprogramma's op basis van Azure Resource Manager.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-model"></a>Resourcemodel

Azure Traffic Manager is geconfigureerd met behulp van een verzameling instellingen genaamd een Traffic Manager-profiel. Dit profiel bevat DNS-instellingen, instellingen voor verkeersroutering, eindpuntcontrole-instellingen en een lijst met serviceeindpunten waarnaar verkeer wordt doorgestuurd.

Elk Traffic Manager-profiel wordt vertegenwoordigd door een bron van het type 'TrafficManagerProfiles'. Op HET NIVEAU VAN DE API van DE REST, is URI voor elk profiel als volgt:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="setting-up-azure-powershell"></a>Azure PowerShell instellen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Deze instructies maken gebruik van Microsoft Azure PowerShell. In het volgende artikel wordt uitgelegd hoe u Azure PowerShell installeert en configureert.

* [Azure PowerShell installeren en configureren](/powershell/azure/overview)

In de voorbeelden in dit artikel wordt ervan uitgegaan dat u een bestaande resourcegroep hebt. U een resourcegroep maken met de volgende opdracht:

```powershell
New-AzResourceGroup -Name MyRG -Location "West US"
```

> [!NOTE]
> Azure Resource Manager vereist dat alle resourcegroepen een locatie hebben. Deze locatie wordt gebruikt als standaardvoor resources die in die resourcegroep zijn gemaakt. Aangezien de profielbronnen van Traffic Manager echter globaal en niet regionaal zijn, heeft de locatie van de brongroep geen invloed op Azure Traffic Manager.

## <a name="create-a-traffic-manager-profile"></a>Een profiel van verkeersbeheer maken

Als u een Traffic Manager-profiel wilt maken, gebruikt u de `New-AzTrafficManagerProfile` cmdlet:

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

In de volgende tabel worden de parameters beschreven:

| Parameter | Beschrijving |
| --- | --- |
| Name |De resourcenaam voor de profielbron Traffic Manager. Profielen in dezelfde resourcegroep moeten unieke namen hebben. Deze naam staat los van de DNS-naam die wordt gebruikt voor DNS-query's. |
| ResourceGroupName |De naam van de resourcegroep die de profielbron bevat. |
| TrafficRoutingMethod |Hiermee geeft u de verkeersrouteringsmethode op die wordt gebruikt om te bepalen welk eindpunt wordt geretourneerd als reactie op een DNS-query. Mogelijke waarden zijn 'Performance', 'Weighted' of 'Priority'. |
| Relatieve DnsName |Hiermee geeft u het hostnamegedeelte op van de DNS-naam die wordt opgegeven door dit Traffic Manager-profiel. Deze waarde wordt gecombineerd met de DNS-domeinnaam die door Azure Traffic Manager wordt gebruikt om de volledig gekwalificeerde domeinnaam (FQDN) van het profiel te vormen. Het instellen van de waarde van 'contoso' wordt bijvoorbeeld 'contoso.trafficmanager.net'. |
| TTL |Hiermee geeft u de DNS Time-to-Live (TTL) in enkele seconden op. Deze TTL informeert de lokale DNS-resolvers en DNS-clients hoe lang het duurt voordat DNS-antwoorden voor dit Traffic Manager-profiel moeten worden opgeslagen. |
| MonitorProtocol |Hiermee geeft u het protocol op dat moet worden gebruikt om de status van eindpunt te controleren. Mogelijke waarden zijn 'HTTP' en 'HTTPS'. |
| MonitorPort |Hiermee geeft u de TCP-poort op die wordt gebruikt om de status van eindpunt te controleren. |
| MonitorPad |Hiermee geeft u het pad op ten opzichte van de eindpuntdomeinnaam die wordt gebruikt om de status van eindpunt te zoeken. |

De cmdlet maakt een Traffic Manager-profiel in Azure en retourneert een bijbehorend profielobject naar PowerShell. Op dit moment bevat het profiel geen eindpunten. Zie Eindpunten voor verkeersbeheer toevoegen voor meer informatie over het toevoegen van eindpunten aan een Traffic Manager-profiel.

## <a name="get-a-traffic-manager-profile"></a>Een profiel van verkeersbeheer

Als u een bestaand profielobject `Get-AzTrafficManagerProfle` van Traffic Manager wilt ophalen, gebruikt u de cmdlet:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

Met deze cmdlet wordt een profielobject Traffic Manager geretourneerd.

## <a name="update-a-traffic-manager-profile"></a>Een profiel van verkeersbeheer bijwerken

Het wijzigen van Traffic Manager-profielen volgt een proces in drie stappen:

1. Haal het `Get-AzTrafficManagerProfile` profiel op met `New-AzTrafficManagerProfile`behulp van het profiel dat is geretourneerd door .
2. Wijzig het profiel. U eindpunten toevoegen en verwijderen of eindpunt- of profielparameters wijzigen. Deze wijzigingen zijn off-line bewerkingen. U wijzigt alleen het lokale object in het geheugen dat het profiel vertegenwoordigt.
3. Bega uw `Set-AzTrafficManagerProfile` wijzigingen met behulp van de cmdlet.

Alle profieleigenschappen kunnen worden gewijzigd, behalve de RelativeDnsName van het profiel. Als u de relatieve dnsname wilt wijzigen, moet u het profiel en een nieuw profiel met een nieuwe naam verwijderen.

In het volgende voorbeeld wordt uitgelegd hoe u de TTL van het profiel wijzigen:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
$TmProfile.Ttl = 300
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

Er zijn drie soorten eindpunten voor verkeersbeheer:

1. **Azure-eindpunten** zijn services die worden gehost in Azure
2. **Externe eindpunten** zijn services die buiten Azure worden gehost
3. **Geneste eindpunten** worden gebruikt om geneste hiërarchieën van Traffic Manager-profielen te construeren. Geneste eindpunten maken geavanceerde configuraties voor het routeren van verkeer voor complexe toepassingen mogelijk.

In alle drie de gevallen kunnen eindpunten op twee manieren worden toegevoegd:

1. Met behulp van een 3-stappenproces eerder beschreven. Het voordeel van deze methode is dat er meerdere eindpuntwijzigingen kunnen worden aangebracht in één update.
2. Met behulp van de cmdlet Nieuw-AzTrafficManagerEndpoint. Deze cmdlet voegt een eindpunt toe aan een bestaand Traffic Manager-profiel in één bewerking.

## <a name="adding-azure-endpoints"></a>Azure-eindpunten toevoegen

Azure-eindpunten-referentieservices die worden gehost in Azure. Er worden twee typen Azure-eindpunten ondersteund:

1. Azure App Service
2. Azure PublicIpAddress-resources (die kunnen worden gekoppeld aan een load-balancer of een virtuele machine NIC). Op het PublicIpAddress moet een DNS-naam zijn toegewezen die is toegewezen in Traffic Manager.

In elk geval:

* De service wordt opgegeven met behulp van `Add-AzTrafficManagerEndpointConfig` `New-AzTrafficManagerEndpoint`de parameter 'targetResourceId' van of .
* De 'Target' en 'EndpointLocation' worden geïmpliceerd door de TargetResourceId.
* Het opgeven van het 'Gewicht' is optioneel. Gewichten worden alleen gebruikt als het profiel is geconfigureerd om de 'Gewogen' verkeersrouteringsmethode te gebruiken. Anders worden ze genegeerd. Indien gespecificeerd, moet de waarde een getal tussen 1 en 1000 zijn. De standaardwaarde is '1'.
* Het opgeven van de 'Prioriteit' is optioneel. Prioriteiten worden alleen gebruikt als het profiel is geconfigureerd om de verkeersrouteringsmethode 'Prioriteit' te gebruiken. Anders worden ze genegeerd. Geldige waarden zijn 1 tot 1000 met lagere waarden die een hogere prioriteit aangeven. Indien opgegeven voor één eindpunt, moeten ze worden opgegeven voor alle eindpunten. Als deze worden weggelaten, worden standaardwaarden vanaf '1' toegepast in de volgorde waarin de eindpunten worden weergegeven.

### <a name="example-1-adding-app-service-endpoints-using-add-aztrafficmanagerendpointconfig"></a>Voorbeeld 1: Eindpunten van app-service toevoegen met`Add-AzTrafficManagerEndpointConfig`

In dit voorbeeld maken we een Traffic Manager-profiel aan `Add-AzTrafficManagerEndpointConfig` en voegen we twee App Service-eindpunten toe met de cmdlet.

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$webapp1 = Get-AzWebApp -Name webapp1
Add-AzTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
$webapp2 = Get-AzWebApp -Name webapp2
Add-AzTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```
### <a name="example-2-adding-a-publicipaddress-endpoint-using-new-aztrafficmanagerendpoint"></a>Voorbeeld 2: Een publicIpAddress-eindpunt toevoegen met`New-AzTrafficManagerEndpoint`

In dit voorbeeld wordt een openbare IP-adresbron toegevoegd aan het profiel Traffic Manager. Het openbare IP-adres moet een DNS-naam hebben geconfigureerd en kan worden gekoppeld aan de NIC van een VM of aan een load balancer.

```powershell
$ip = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="adding-external-endpoints"></a>Externe eindpunten toevoegen

Traffic Manager gebruikt externe eindpunten om verkeer te leiden naar services die buiten Azure worden gehost. Net als bij Azure-eindpunten kunnen externe `Add-AzTrafficManagerEndpointConfig` eindpunten worden toegevoegd met behulp van, gevolgd door `Set-AzTrafficManagerProfile`, of `New-AzTrafficManagerEndpoint`.

Bij het opgeven van externe eindpunten:

* De domeinnaam van het eindpunt moet worden opgegeven met de parameter 'Target'.
* Als de verkeersrouteringsmethode 'Prestaties' wordt gebruikt, is de 'EndpointLocation' vereist. Anders is het optioneel. De waarde moet een [geldige Azure-regionaam](https://azure.microsoft.com/regions/)zijn.
* Het 'Gewicht' en 'Prioriteit' zijn optioneel.

### <a name="example-1-adding-external-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>Voorbeeld 1: Externe eindpunten toevoegen met `Add-AzTrafficManagerEndpointConfig` en`Set-AzTrafficManagerProfile`

In dit voorbeeld maken we een Traffic Manager-profiel, voegen we twee externe eindpunten toe en verbinden we de wijzigingen.

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointLocation "North Europe" -EndpointStatus Enabled
Add-AzTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointLocation "Central US" -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-adding-external-endpoints-using-new-aztrafficmanagerendpoint"></a>Voorbeeld 2: Externe eindpunten toevoegen met`New-AzTrafficManagerEndpoint`

In dit voorbeeld voegen we een extern eindpunt toe aan een bestaand profiel. Het profiel wordt opgegeven met de namen van de profiel- en resourcegroep.

```powershell
New-AzTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
```

## <a name="adding-nested-endpoints"></a>'Geneste' eindpunten toevoegen

Elk Traffic Manager-profiel geeft één verkeersrouteringsmethode op. Er zijn echter scenario's die een geavanceerdere verkeersroutering vereisen dan de routebepaling die wordt geboden door één Traffic Manager-profiel. U Traffic Manager-profielen nesten om de voordelen van meer dan één verkeersrouteringsmethode te combineren. Met geneste profielen u het standaard gedrag van Traffic Manager overschrijven om grotere en complexere toepassingsimplementaties te ondersteunen. Zie [Geneste verkeersbeheerprofielen](traffic-manager-nested-profiles.md)voor meer gedetailleerde voorbeelden .

Geneste eindpunten worden geconfigureerd op het bovenliggende profiel met behulp van een specifiek eindpunttype, 'Geneste Eindpunten'. Bij het opgeven van geneste eindpunten:

* Het eindpunt moet worden opgegeven met de parameter 'targetResourceId'.
* Als de verkeersrouteringsmethode 'Prestaties' wordt gebruikt, is de 'EndpointLocation' vereist. Anders is het optioneel. De waarde moet een [geldige Azure-regionaam](https://azure.microsoft.com/regions/)zijn.
* De 'Gewicht' en 'Prioriteit' zijn optioneel, net als voor Azure-eindpunten.
* De parameter 'MinChildEndpoints' is optioneel. De standaardwaarde is '1'. Als het aantal beschikbare eindpunten onder deze drempel valt, beschouwt het bovenliggende profiel het onderliggende profiel als 'gedegradeerd' en leidt het verkeer door naar de andere eindpunten in het bovenliggende profiel.

### <a name="example-1-adding-nested-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>Voorbeeld 1: Geneste eindpunten toevoegen met `Add-AzTrafficManagerEndpointConfig` en`Set-AzTrafficManagerProfile`

In dit voorbeeld maken we nieuwe onderliggende en bovenliggende profielen van Traffic Manager, voegen we het onderliggende kind toe als een genest eindpunt aan de ouder en verbinden we de wijzigingen.

```powershell
$child = New-AzTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$parent = New-AzTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
Set-AzTrafficManagerProfile -TrafficManagerProfile $parent
```

Voor beknoptheid in dit voorbeeld hebben we geen andere eindpunten toegevoegd aan de onderliggende of bovenliggende profielen.

### <a name="example-2-adding-nested-endpoints-using-new-aztrafficmanagerendpoint"></a>Voorbeeld 2: Geneste eindpunten toevoegen met`New-AzTrafficManagerEndpoint`

In dit voorbeeld voegen we een bestaand onderliggend profiel toe als een genest eindpunt aan een bestaand bovenliggend profiel. Het profiel wordt opgegeven met de namen van de profiel- en resourcegroep.

```powershell
$child = Get-AzTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
```

## <a name="adding-endpoints-from-another-subscription"></a>Eindpunten van een ander abonnement toevoegen

Traffic Manager kan werken met eindpunten van verschillende abonnementen. U moet overschakelen naar het abonnement met het eindpunt dat u wilt toevoegen om de benodigde invoer op te halen voor Traffic Manager. Vervolgens moet u overschakelen naar de abonnementen met het Traffic Manager-profiel en het eindpunt eraan toevoegen. In het onderstaande voorbeeld ziet u hoe u dit doen met een openbaar IP-adres.

```powershell
Set-AzContext -SubscriptionId $EndpointSubscription
$ip = Get-AzPublicIpAddress -Name $IpAddressName -ResourceGroupName $EndpointRG

Set-AzContext -SubscriptionId $trafficmanagerSubscription
New-AzTrafficManagerEndpoint -Name $EndpointName -ProfileName $ProfileName -ResourceGroupName $TrafficManagerRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="update-a-traffic-manager-endpoint"></a>Een eindpunt van verkeersbeheer bijwerken

Er zijn twee manieren om een bestaand eindpunt voor Traffic Manager bij te werken:

1. Haal het Traffic `Get-AzTrafficManagerProfile`Manager-profiel met behulp van , werk de `Set-AzTrafficManagerProfile`eindpunteigenschappen in het profiel bij en voer de wijzigingen door met behulp van . Deze methode heeft het voordeel dat u meer dan één eindpunt in één bewerking bijwerken.
2. Haal het eindpunt Van `Get-AzTrafficManagerEndpoint`Traffic Manager met behulp van , `Set-AzTrafficManagerEndpoint`werk de eindpunteigenschappen bij en voer de wijzigingen door met behulp van . Deze methode is eenvoudiger, omdat deze niet hoeft te worden geïndexeerd in de array Endpoints in het profiel.

### <a name="example-1-updating-endpoints-using-get-aztrafficmanagerprofile-and-set-aztrafficmanagerprofile"></a>Voorbeeld 1: Eindpunten `Get-AzTrafficManagerProfile` bijwerken met en`Set-AzTrafficManagerProfile`

In dit voorbeeld wijzigen we de prioriteit op twee eindpunten binnen een bestaand profiel.

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
$TmProfile.Endpoints[0].Priority = 2
$TmProfile.Endpoints[1].Priority = 1
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-updating-an-endpoint-using-get-aztrafficmanagerendpoint-and-set-aztrafficmanagerendpoint"></a>Voorbeeld 2: Een eindpunt `Get-AzTrafficManagerEndpoint` bijwerken met en`Set-AzTrafficManagerEndpoint`

In dit voorbeeld wijzigen we het gewicht van één eindpunt in een bestaand profiel.

```powershell
$endpoint = Get-AzTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
$endpoint.Weight = 20
Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Eindpunten en profielen in- en uitschakelen

Met Traffic Manager kunnen afzonderlijke eindpunten worden ingeschakeld en uitgeschakeld, evenals het inschakelen en uitschakelen van hele profielen.
Deze wijzigingen kunnen worden aangebracht door het eindpunt of de profielbronnen te krijgen/bijwerken/instellen. Om deze gemeenschappelijke bewerkingen te stroomlijnen, worden ze ook ondersteund via speciale cmdlets.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>Voorbeeld 1: Een traffic manager-profiel in- en uitschakelen

Als u een Traffic `Enable-AzTrafficManagerProfile`Manager-profiel wilt inschakelen, gebruikt u . Het profiel kan worden opgegeven met behulp van een profielobject. Het profielobject kan worden doorgegeven via de pijplijn of via de parameter 'TrafficManagerProfile'. In dit voorbeeld geven we het profiel op aan de naam van het profiel en de resourcegroep.

```powershell
Enable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Een Traffic Manager-profiel uitschakelen:

```powershell
Disable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

De cmdlet Disable-AzTrafficManagerProfile vraagt om bevestiging. Deze prompt kan worden onderdrukt met behulp van de parameter 'Force'.

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>Voorbeeld 2: Een eindpunt van een verkeersbeheerbeheer inschakelen en uitschakelen

Als u een eindpunt van `Enable-AzTrafficManagerEndpoint`Verkeersbeheer wilt inschakelen, gebruikt u . Er zijn twee manieren om het eindpunt op te geven

1. Een TrafficManagerEndpoint-object gebruiken dat via de pijplijn wordt doorgegeven of de parameter 'TrafficManagerEndpoint' gebruikt
2. Met de naam eindpunt, eindpunttype, profielnaam en resourcegroepnaam:

```powershell
Enable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Als u een eindpunt van Traffic Manager wilt uitschakelen:

```powershell
Disable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

Net `Disable-AzTrafficManagerProfile`als `Disable-AzTrafficManagerEndpoint` bij , de cmdlet vraagt om bevestiging. Deze prompt kan worden onderdrukt met behulp van de parameter 'Force'.

## <a name="delete-a-traffic-manager-endpoint"></a>Een eindpunt van verkeersbeheer verwijderen

Gebruik de `Remove-AzTrafficManagerEndpoint` cmdlet om afzonderlijke eindpunten te verwijderen:

```powershell
Remove-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Deze cmdlet vraagt om bevestiging. Deze prompt kan worden onderdrukt met behulp van de parameter 'Force'.

## <a name="delete-a-traffic-manager-profile"></a>Een profiel van verkeersbeheer verwijderen

Als u een Traffic Manager-profiel wilt verwijderen, gebruikt u de `Remove-AzTrafficManagerProfile` cmdlet en geeft u de namen van de profiel- en brongroep op:

```powershell
Remove-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

Deze cmdlet vraagt om bevestiging. Deze prompt kan worden onderdrukt met behulp van de parameter 'Force'.

Het te verwijderen profiel kan ook worden opgegeven met een profielobject:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
Remove-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile [-Force]
```

Deze volgorde kan ook worden gepipeteerd:

```powershell
Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>Volgende stappen

[Toezicht op verkeersbeheer](traffic-manager-monitoring.md)

[Prestatieoverwegingen voor Traffic Manager](traffic-manager-performance-considerations.md)
