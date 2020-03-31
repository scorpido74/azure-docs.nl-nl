---
title: Beleid voor service-eindpunten voor virtuele Azure-netwerken | Microsoft Docs
description: Lees hoe u het verkeer van Virtual Network naar Azure-serviceresources filtert met behulp van beleid voor service-eindpunten
services: virtual-network
documentationcenter: na
author: RDhillon
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: 926da07ffaf0c61ca2a7fd02351ef3635ec4d73b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651288"
---
# <a name="virtual-network-service-endpoint-policies-for-azure-storage"></a>Eindpuntbeleid voor virtuele netwerkservices voor Azure Storage

Met het eindpuntbeleid voor het eindpunt van de Service voor virtueel netwerk (VNet) u het uitgaande virtuele netwerkverkeer naar Azure Storage-accounts filteren via het eindpunt van de service en gegevensexfiltratie toestaan voor alleen specifieke Azure Storage-accounts. Endpoint-beleid biedt gedetailleerd toegangscontrole voor virtueel netwerkverkeer naar Azure Storage wanneer u verbinding maakt via het eindpunt van de service.

![Uitgaand virtueel netwerkverkeer beveiligen naar Azure Storage-accounts](./media/virtual-network-service-endpoint-policies-overview/vnet-service-endpoint-policies-overview.png)

Deze functie is algemeen beschikbaar voor __Azure Storage__ in alle __algemene Azure-regio's.__

## <a name="key-benefits"></a>Belangrijkste voordelen

Het beleid voor service-eindpunten voor virtuele netwerken biedt de volgende voordelen:

- __Verbeterde beveiliging voor uw virtual network-verkeer naar Azure Storage__

  [Met Azure-servicetags voor netwerkbeveiligingsgroepen](https://aka.ms/servicetags) u uitgaand virtueel netwerkverkeer beperken tot specifieke Azure Storage-regio's. Hierdoor kan er echter verkeer worden verhandeld naar elk account binnen het geselecteerde Azure Storage-gebied.
  
  Met eindpuntbeleid u de Azure Storage-accounts opgeven die uitgaande toegang voor virtuele netwerken hebben en de toegang tot alle andere opslagaccounts beperken. Dit geeft veel meer gedetailleerde beveiligingscontrole voor het beschermen van gegevensexfiltratie van uw virtuele netwerk.

- __Schaalbaar beleid met hoge beschikbaarheid voor het filteren van verkeer naar Azure-services__

   Beleid voor eindpunten zorgt voor horizontaal schaalbare oplossingen met hoge beschikbaarheid, waarmee het verkeer van virtuele netwerken naar Azure-services kan worden gefilterd via service-eindpunten. Er is geen extra overhead vereist voor het onderhoud van centrale netwerkapparatuur voor dit verkeer in uw virtuele netwerken.

## <a name="json-object-for-service-endpoint-policies"></a>JSON-object voor serviceeindpuntbeleid
Laten we eens snel kijken naar het object Service Endpoint Policy.

```json
"serviceEndpointPolicyDefinitions": [
    {
            "description": null,
            "name": "MySEP-Definition",
            "resourceGroup": "MySEPDeployment",
            "service": "Microsoft.Storage",
            "serviceResources": [ 
                    "/subscriptions/subscriptionID/resourceGroups/MySEPDeployment/providers/Microsoft.Storage/storageAccounts/mystgacc"
            ],
            "type": "Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions"
    }
]
```

## <a name="configuration"></a>Configuratie

-   U het eindpuntbeleid configureren om virtueel netwerkverkeer te beperken tot specifieke Azure Storage-accounts.
-   Het beleid voor eindpunten wordt geconfigureerd op een subnet in een virtueel netwerk. Serviceeindpunten voor Azure Storage moeten op het subnet zijn ingeschakeld om het beleid toe te passen.
-   Met het eindpuntbeleid u specifieke Azure Storage-accounts toevoegen om de lijst toe te staan met behulp van de resource-ID-indeling. U de toegang tot
    - alle opslagaccounts in een abonnement<br>
      `E.g. /subscriptions/subscriptionId`

    - alle opslagaccounts in een resourcegroep<br>
      `E.g. subscriptions/subscriptionId/resourceGroups/resourceGroupName`
     
    - een individueel opslagaccount door de bijbehorende Azure Resource Manager resourceId aan te geven. Dit omvat het verkeer naar blobs, tabellen, wachtrijen, bestanden en Azure Data Lake Storage Gen2. <br>
    `E.g. /subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/storageAccountName`
-   Als er standaard geen beleidsregels zijn gekoppeld aan een subnet met eindpunten, hebt u toegang tot alle opslagaccounts in de service. Zodra een beleid op dat subnet is geconfigureerd, zijn alleen de resources die in het beleid zijn opgegeven toegankelijk via rekenprocessen in dat subnet. Toegang tot alle andere opslagaccounts wordt geweigerd.
-   Wanneer u serviceeindpuntbeleid toepast op een subnet, wordt het *endpoint-bereik* van Azure Storage Service geüpgraded van regionaal naar **globaal**. Dit betekent dat al het verkeer naar Azure Storage daarna via het eindpunt van de service wordt beveiligd. Het eindpuntbeleid van service is ook wereldwijd van toepassing, zodat opslagaccounts, die niet expliciet zijn toegestaan, de toegang worden geweigerd. 
-   U kunt meerdere beleidsregels toepassen op een subnet. Wanneer meerdere beleidsregels aan het subnet zijn gekoppeld, is virtueel netwerkverkeer toegestaan voor bronnen die in een van deze beleidsregels zijn opgegeven. Toegang tot alle andere serviceresources, die niet in een beleidsregel zijn opgegeven, wordt geweigerd.

    > [!NOTE]  
    > Serviceeindpuntbeleid zijn **beleid voor gebruiksvoorwaarden,** dus afgezien van de opgegeven resources zijn alle andere resources beperkt. Zorg ervoor dat alle afhankelijkheden van serviceresources voor uw toepassingen worden geïdentificeerd en in het beleid worden vermeld.

- Alleen de opslagaccounts die gebruikmaken van het Azure-resourcemodel, kunnen worden opgegeven in het beleid voor eindpunten. Uw klassieke Azure Storage-accounts ondersteunen geen Azure Service Endpoint-beleid.
- Secundaire toegang op basis van RA-GRS wordt automatisch toegestaan als het primaire account wordt vermeld.
- Opslagaccounts kunnen zich in zowel hetzelfde als een ander abonnement of in zowel dezelfde als een andere Azure Active Directory-tenant bevinden als het virtuele netwerk.

## <a name="scenarios"></a>Scenario's

- **Gekoppelde, verbonden of meerdere virtuele netwerken**: Als u het verkeer in gekoppelde virtuele netwerken wilt filteren, moeten de beleidsregels voor eindpunten afzonderlijk op deze virtuele netwerken worden toegepast.
- **Internetverkeer filteren met Netwerkapparaten of Azure Firewall:** Filter Azure-serviceverkeer met beleidsregels, over serviceeindpunten en filter de rest van het internet- of Azure-verkeer via apparaten of Azure Firewall.
- **Verkeer filteren op Azure-services die zijn geïmplementeerd in virtuele netwerken:** op dit moment worden Azure Service Endpoint-beleidsregels niet ondersteund voor beheerde Azure-services die in uw virtuele netwerk worden geïmplementeerd. 
- **Verkeer vanaf on-premises naar Azure-services filteren**: Het beleid voor service-eindpunten is alleen van toepassing op het verkeer van de subnetten die aan het beleid zijn gekoppeld. Als u toegang vanaf on-premises tot bepaalde Azure-serviceresources wilt toestaan, moet het verkeer worden gefilterd met behulp van virtuele netwerkapparaten of firewalls.

## <a name="logging-and-troubleshooting"></a>Logboekregistratie en problemen oplossen
Voor beleid voor service-eindpunten is gecentraliseerde logboekregistratie niet beschikbaar. Zie [Logboekregistratie voor service-eindpunten](virtual-network-service-endpoints-overview.md#logging-and-troubleshooting) voor diagnostische logboeken voor services.

### <a name="troubleshooting-scenarios"></a>Scenario’s voor probleemoplossing
- Toegang geweigerd tot opslagaccounts die in preview werkten (niet in geo-gekoppelde regio)
  - Met de upgrade van Azure Storage naar Global Service Tags is het bereik van Service Endpoint en dus Service Endpoint-beleid nu globaal. Dus alle verkeer naar Azure Storage wordt versleuteld via Service Endpoints en alleen opslagaccounts die expliciet in het beleid worden vermeld, hebben toegang.
  - Sta de lijst expliciet toe met alle vereiste opslagaccounts om de toegang te herstellen.  
  - Neem contact op met de ondersteuning van Azure.
- De toegang wordt geweigerd voor accounts die in het beleid voor eindpunten worden vermeld
  - Mogelijk wordt de toegang geblokkeerd door netwerkbeveiligingsgroepen of firewall-filters
  - Als het verwijderen/het opnieuw toepassen van het beleid ervoor zorgt dat de verbinding wordt verbroken:
    - Valideren of de Azure-service is geconfigureerd om toegang toe te staan vanuit het virtuele netwerk via eindpunten of dat het standaardbeleid voor de resource is ingesteld op *Alles toestaan*.
    - Controleer of het verkeer via de eindpunten wordt weergegeven in de diagnostische gegevens.
    - Controleer in de stroomlogboeken voor de netwerkbeveiligingsgroep en de opslaglogboeken of de toegang, zoals verwacht, via de service-eindpunten verloopt.
    - Neem contact op met de ondersteuning van Azure.
- De toegang wordt geweigerd voor accounts niet worden vermeld in het beleid voor service-eindpunten
  - Valideren of Azure Storage is geconfigureerd om toegang toe te staan vanuit het virtuele netwerk via eindpunten of dat het standaardbeleid voor de resource is ingesteld op *Alles toestaan*.
  - Zorg ervoor dat de accounts geen **klassieke opslagaccounts** zijn met serviceeindpuntbeleid op het subnet.
- Een beheerde Azure-service werkt niet meer nadat een Service Endpoint-beleid is toegepast op het subnet
  - Beheerde services worden op dit moment niet ondersteund met serviceeindpuntbeleid. *Bekijk deze ruimte voor updates.*

## <a name="provisioning"></a>Inrichten

Op subnetten kan het beleid voor service-eindpunten worden geconfigureerd door een gebruiker met schrijftoegang tot een virtueel netwerk. Meer informatie over [ingebouwde rollen](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) in Azure en het toewijzen van specifieke machtigingen voor [aangepaste rollen](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Virtuele netwerken en Azure Storage-accounts kunnen zich in dezelfde of verschillende abonnementen bevinden of Azure Active Directory-tenants.

## <a name="limitations"></a>Beperkingen

- U kunt alleen beleid voor service-eindpunten implementeren op virtuele netwerken die zijn geïmplementeerd met behulp van het Azure Resource Manager-implementatiemodel.
- Virtuele netwerken moeten zich in dezelfde regio bevinden als het beleid voor service-eindpunten.
- U kunt alleen beleid voor service-eindpunten toepassen op een subnet als de service-eindpunten zijn geconfigureerd voor de Azure-services die in het beleid worden vermeld.
- Het is niet mogelijk om beleid voor service-eindpunten te gebruiken voor verkeer van uw on-premises netwerk naar Azure-services.
- Azure managed services ondersteunen momenteel geen Endpoint-beleid. Dit omvat beheerde services die zijn geïmplementeerd in de gedeelde subnetten (bijvoorbeeld *Azure HDInsight, Azure Batch, Azure ADDS, Azure APplication Gateway, Azure VPN-gateway, Azure Firewall)* of in de speciale subnetten (bijvoorbeeld *Azure App Service Environment, Azure Redis Cache, Azure API Management, Azure SQL MI, klassieke managed services).*

 > [!WARNING]
 > Azure-services die in uw virtuele netwerk zijn geïmplementeerd, zoals Azure HDInsight, hebben toegang tot andere Azure-services, zoals Azure Storage, om aan de vereisten voor de infrastructuur te kunnen voldoen. Als u het beleid voor eindpunten tot specifieke resources beperkt, kan de toegang tot deze infrastructuurresources worden onderbroken voor de Azure-services die in uw virtuele netwerk zijn geïmplementeerd.

- Klassieke opslagaccounts worden niet ondersteund in het beleid voor eindpunten. Het beleid weigert standaard de toegang tot alle klassieke opslagaccounts. Als uw toepassing toegang moet hebben tot Azure Resource Manager en klassieke opslagaccounts, moet er geen beleid voor eindpunten worden gebruikt voor dit verkeer.

## <a name="pricing-and-limits"></a>Prijzen en beperkingen

Er worden geen extra kosten in rekening gebracht voor het gebruik van beleid voor service-eindpunten. Het huidige prijsmodel voor Azure-services (zoals Azure Storage) via service-eindpunten is van toepassing.

Voor beleid voor service-eindpunten gelden de volgende limieten: 

 |Resource | Standaardlimiet |
 |---------|---------------|
 |ServiceEndpointPoliciesPerSubscription |500 |
 |ServiceEndpintPoliciesPerSubnet|100 |
 |ServiceResourcesPerServiceEndpointPolicyDefinition|200 |

## <a name="next-steps"></a>Volgende stappen

- Lees [hoe u beleid voor service-eindpunten voor virtuele netwerken configureert](virtual-network-service-endpoint-policies-portal.md)
- Meer informatie over [Service-eindpunten voor virtuele netwerken](virtual-network-service-endpoints-overview.md)
