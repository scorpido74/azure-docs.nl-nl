---
title: Beleid voor service-eindpunten voor virtuele Azure-netwerken | Microsoft Docs
description: Lees hoe u het verkeer van Virtual Network naar Azure-serviceresources filtert met behulp van beleid voor service-eindpunten
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sumi
ms.openlocfilehash: 1aa4328a6d5367ef356ce33807289a873c93d90f
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77056696"
---
# <a name="virtual-network-service-endpoint-policies-preview"></a>Beleid voor service-eindpunten voor virtuele netwerken (preview)

Met beleid voor service-eindpunten voor Virtual Network (VNet) kunt u via service-eindpunten verkeer van Virtual Network naar Azure-services filteren, waardoor alleen bepaalde Azure-serviceresources worden toegelaten. Beleid voor eindpunten zorgt voor nauwkeurig toegangsbeheer voor verkeer van Virtual Network naar Azure-services.

Deze functie is beschikbaar als __Preview-versie__ in alle open bare Azure-regio's voor Azure STorage.

Voor de nieuwste updates over de preview gaat u naar de pagina [Updates voor Azure Virtual Network](https://azure.microsoft.com/updates/?product=virtual-network).

> [!NOTE]  
> Tijdens de preview is het beleid voor service-eindpunten voor virtuele netwerken mogelijk niet even goed beschikbaar en even betrouwbaar als functies die al algemeen beschikbaar zijn. Zie [Microsoft Azure Supplemental Terms of Use for Microsoft Azure Previews (Microsoft Azure Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="key-benefits"></a>Belangrijkste voordelen

Het beleid voor service-eindpunten voor virtuele netwerken biedt de volgende voordelen:

- __Verbeterde beveiliging voor uw verkeer van Virtual Network naar Azure-services__

  Met [Azure-servicetags voor netwerkbeveiligingsgroepen](https://aka.ms/servicetags) kunt u het uitgaande verkeer van virtuele netwerken beperken tot bepaalde Azure-services. Hiermee wordt er echter verkeer doorgelaten naar alle resources van die Azure-service. 
  
  Met beleid voor eindpunten kunt u de uitgaande toegang van virtuele netwerken nu beperken tot alleen bepaalde Azure-resources. Dit zorgt ervoor dat u de beveiliging van de gegevens die worden geraadpleegd in uw virtuele netwerk veel nauwkeuriger kunt beheren. 

- __Schaalbaar beleid met hoge beschikbaarheid voor het filteren van verkeer naar Azure-services__

   Beleid voor eindpunten zorgt voor horizontaal schaalbare oplossingen met hoge beschikbaarheid, waarmee het verkeer van virtuele netwerken naar Azure-services kan worden gefilterd via service-eindpunten. Er is geen extra overhead vereist voor het onderhoud van centrale netwerkapparatuur voor dit verkeer in uw virtuele netwerken.

## <a name="configuration"></a>Configuratie

- U kunt het beleid voor eindpunten zo configureren dat het verkeer van virtuele netwerken beperkt blijft tot bepaalde Azure-serviceresources. Voor de preview wordt het beleid voor eindpunten ondersteund voor Azure Storage. 
- Het beleid voor eindpunten wordt geconfigureerd op een subnet in een virtueel netwerk. Als u het beleid wilt toepassen, moeten de service-eindpunten op het subnet zijn ingeschakeld voor alle Azure-services in het beleid.
- Met het beleid voor eindpunten kunt u bepaalde Azure-serviceresources in een whitelist opnemen met behulp van de resourceID-indeling. U kunt de toegang tot alle resources in een abonnement of resourcegroep beperken. Bovendien kunt u de toegang tot specifieke resources beperken. 
- Als er geen beleid is gekoppeld aan een subnet met eindpunten, hebt u standaard toegang tot alle resources in de service. Zodra een beleid op dat subnet is geconfigureerd, zijn alleen de resources die in het beleid zijn opgegeven toegankelijk via rekenprocessen in dat subnet. Voor die specifieke service wordt de toegang tot alle andere resources geweigerd. 
- U kunt verkeer naar Azure-serviceresources filteren in de regio's waar een service-eindpunt is geconfigureerd. Toegang tot serviceresources in andere regio's is standaard toegestaan. 

  > [!NOTE]  
  > Als u de uitgaande toegang van Azure-resources in een virtueel netwerk wilt beperken tot de eindpuntregio's van de service, moet u ook met behulp van [servicetags](security-overview.md#service-tags) de groepsregels voor netwerkbeveiliging zo configureren dat deze alleen verkeer naar de eindpuntregio's van de service toestaan.

- U kunt meerdere beleidsregels toepassen op een subnet. Wanneer er voor dezelfde service meerdere beleidsregels aan het subnet zijn gekoppeld, wordt het verkeer van virtuele netwerken naar resources die in deze beleidsregels zijn opgegeven, toegestaan. Toegang tot alle andere serviceresources, die niet in een beleidsregel zijn opgegeven, wordt geweigerd. 

  > [!NOTE]  
  > Beleid staat alleen toegang toe tot vermelde serviceresources van een virtueel netwerk. Al het andere verkeer naar de service wordt automatisch geweigerd wanneer u specifieke resources aan het beleid toevoegt. Zorg ervoor dat alle serviceresourceafhankelijkheden voor uw toepassingen kunnen worden geïdentificeerd en in het beleid kunnen worden opgenomen.

  > [!WARNING]  
  > Azure-services die in uw virtuele netwerk zijn geïmplementeerd, zoals Azure HDInsight, hebben toegang tot andere Azure-services, zoals Azure Storage, om aan de vereisten voor de infrastructuur te kunnen voldoen. Als u het beleid voor eindpunten tot specifieke resources beperkt, kan de toegang tot deze infrastructuurresources worden onderbroken voor services die in uw virtuele netwerk zijn geïmplementeerd. Voor bepaalde services, die staan vermeld in [Beperkingen tijdens de preview-fase](#limitations), wordt het beleid voor service-eindpunten niet ondersteund voor eventuele beheerde Azure-services die in uw virtuele netwerk zijn geïmplementeerd.

- Voor Azure Storage: 
  -  U kunt de toegang beperken door de *resourceId* van het opslagaccount in Azure Resource Manager op te nemen. Dit omvat het verkeer naar blobs, tabellen, wachtrijen, bestanden en Azure Data Lake Storage Gen2.

     U kunt Azure-opslagaccounts in de definitie van het eindpuntbeleid opnemen zoals hieronder wordt aangegeven:
    
     Als u een specifiek opslagaccount wilt toestaan:         
     `subscriptions/subscriptionId/resourceGroups/resourceGroup/providers/Microsoft.Storage/storageAccounts/storageAccountName`
      
     Als u alle accounts in een abonnement en resourcegroep wilt toestaan: `/subscriptions/subscriptionId/resourceGroups/resourceGroup`
     
     Als u alle accounts in een abonnement wilt toestaan: `/subscriptions/subscriptionId`
    
- Alleen de opslagaccounts die gebruikmaken van het Azure-resourcemodel, kunnen worden opgegeven in het beleid voor eindpunten.  

  > [!NOTE]  
  > De toegang tot klassieke opslagaccounts wordt geblokkeerd met beleid voor eindpunten.

- De primaire locatie voor het vermelde account moet zich bevinden in een van de gekoppelde regio's van het service-eindpunt voor het subnet. 

  > [!NOTE]  
  > Met behulp van beleidsregels kunnen serviceresources van andere regio’s worden opgegeven. De virtuele netwerktoegang tot de Azure-services wordt alleen gefilterd voor de gekoppelde regio's. Als netwerkbeveiligingsgroepen niet zijn beperkt tot de gekoppelde regio’s voor Azure Storage, dan heeft het virtuele netwerk toegang tot alle opslagaccounts buiten de gekoppelde accounts.

- Secundaire toegang op basis van RA-GRS wordt automatisch toegestaan als het primaire account wordt vermeld. 
- Opslagaccounts kunnen zich in zowel hetzelfde als een ander abonnement of in zowel dezelfde als een andere Azure Active Directory-tenant bevinden als het virtuele netwerk. 

## <a name="limitations"></a>Beperkingen

- U kunt alleen beleid voor service-eindpunten implementeren op virtuele netwerken die zijn geïmplementeerd met behulp van het Azure Resource Manager-implementatiemodel.
- Virtuele netwerken moeten zich in dezelfde regio bevinden als het beleid voor service-eindpunten.
- U kunt alleen beleid voor service-eindpunten toepassen op een subnet als de service-eindpunten zijn geconfigureerd voor de Azure-services die in het beleid worden vermeld.
- Het is niet mogelijk om beleid voor service-eindpunten te gebruiken voor verkeer van uw on-premises netwerk naar Azure-services.
- U moet geen beleid voor eindpunten toepassen op subnetten met beheerde Azure-services, die afhankelijk zijn van Azure-services om aan de vereisten voor de infrastructuur te kunnen voldoen. 

  > [!WARNING]  
  > Azure-services die in uw virtuele netwerk zijn geïmplementeerd, zoals Azure HDInsight, hebben toegang tot andere Azure-services, zoals Azure Storage, om aan de vereisten voor de infrastructuur te kunnen voldoen. Als u het beleid voor eindpunten tot specifieke resources beperkt, kan de toegang tot deze infrastructuurresources worden onderbroken voor de Azure-services die in uw virtuele netwerk zijn geïmplementeerd.
  
  - Sommige Azure-services kunnen worden geïmplementeerd in subnetten met andere rekenprocessen. Zorg ervoor dat het beleid voor eindpunten niet op het subnet wordt toegepast, als de beheerde services die hieronder worden vermeld in het subnet worden geïmplementeerd.
   
    - Azure HDInsight
    - Azure Batch (Azure Resource Manager)
    - Azure Active Directory Domain Services (Azure Resource Manager)
    - Azure Application Gateway (Azure Resource Manager)
    - Azure VPN Gateway (Azure Resource Manager)
    - Azure Firewall

  - Sommige Azure-services worden geïmplementeerd in speciaal toegewezen subnetten. Tijdens de preview wordt het beleid voor eindpunten geblokkeerd op al die services, die hieronder staan vermeld. 

     - Azure App Service Environment
     - Azure Redis Cache
     - Azure API Management
     - Azure SQL Managed Instance
     - Azure Active Directory Domain Services
     - Azure Application Gateway (Klassiek)
     - Azure VPN Gateway (Klassiek)

- Azure Storage: Klassieke opslagaccounts worden niet ondersteund in het beleid voor eindpunten. Het beleid weigert standaard de toegang tot alle klassieke opslagaccounts. Als uw toepassing toegang moet hebben tot Azure Resource Manager en klassieke opslagaccounts, moet er geen beleid voor eindpunten worden gebruikt voor dit verkeer. 

## <a name="nsgs-with-service-endpoint-policies"></a>Netwerkbeveiligingsgroepen met beleid voor service-eindpunten
- Netwerkbeveiligingsgroepen laten standaard uitgaand internetverkeer toe, waaronder verkeer van Virtual Network naar Azure-services.
- Als u al het uitgaande internetverkeer wilt weigeren en alleen het verkeer naar specifieke Azure-serviceresources wilt toestaan, doet u dat als volgt: 

  Stap 1: Configureer de netwerkbeveiligingsgroepen zodanig dat die alleen uitgaand verkeer toestaan naar Azure-services in eindpuntregio’s. Doe dit met behulp van *Azure-servicetags*. Zie [servicetags voor netwerkbeveiligingsgroepen](https://aka.ms/servicetags) voor meer informatie
      
  Regels voor netwerkbeveiligingsgroepen die de toegang beperken tot alleen eindpuntregio’s, zien eruit als in het volgende voorbeeld:

  ```
  Allow AzureStorage.WestUS2,
  Allow AzureStorage.WestCentralUS,
  Deny all
  ```

  Stap 2: Het beleid voor service-eindpunten toepassen dat toegang biedt tot alleen bepaalde Azure-serviceresources.

  > [!WARNING]  
  > Als een netwerkbeveiligingsgroep niet is geconfigureerd om de toegang van een Azure-service van een virtueel netwerk te beperken tot eindpuntregio's, hebt u toegang tot serviceresources in andere regio's, zelfs als het beleid voor service-eindpunten wordt toegepast.

## <a name="scenarios"></a>Scenario 's

- **Gekoppelde, verbonden of meerdere virtuele netwerken**: Als u het verkeer in gekoppelde virtuele netwerken wilt filteren, moeten de beleidsregels voor eindpunten afzonderlijk op deze virtuele netwerken worden toegepast.
- **Internetverkeer filteren met netwerkapparaten of Azure Firewall**: Filter het verkeer van Azure-services met behulp van beleid, via eindpunten, en filter de rest van het internet- of Azure-verkeer via apparaten of Azure Firewall. 
- **Verkeer filteren op Azure-services die in virtuele netwerken zijn geïmplementeerd**: Tijdens de preview wordt het beleid voor service-eindpunten niet ondersteund voor eventuele beheerde Azure-services die in uw virtuele netwerk zijn geïmplementeerd. 
 Zie [beperkingen](#limitations) voor de betreffende services.
- **Verkeer vanaf on-premises naar Azure-services filteren**: Het beleid voor service-eindpunten is alleen van toepassing op het verkeer van de subnetten die aan het beleid zijn gekoppeld. Als u toegang vanaf on-premises tot bepaalde Azure-serviceresources wilt toestaan, moet het verkeer worden gefilterd met behulp van virtuele netwerkapparaten of firewalls.

## <a name="logging-and-troubleshooting"></a>Logboekregistratie en problemen oplossen
Voor beleid voor service-eindpunten is gecentraliseerde logboekregistratie niet beschikbaar. Zie [Logboekregistratie voor service-eindpunten](virtual-network-service-endpoints-overview.md#logging-and-troubleshooting) voor diagnostische logboeken voor services.

### <a name="troubleshooting-scenarios"></a>Scenario’s voor probleemoplossing
- De toegang tot opslagaccounts die niet in het beleid voor eindpunten is vermeld, is toegestaan
  - Netwerkbeveiligingsgroepen kunnen toegang tot het internet of tot Azure Storage-accounts in andere regio's toestaan.
  - Netwerkbeveiligingsgroepen moeten zo worden geconfigureerd dat al het uitgaande internetverkeer wordt geweigerd en dat alleen verkeer naar specifieke Azure Storage-regio’s wordt toegestaan. Zie Netwerkbeveiligingsgroepen voor meer informatie.
- De toegang wordt geweigerd voor accounts die in het beleid voor eindpunten worden vermeld
  - Mogelijk wordt de toegang geblokkeerd door netwerkbeveiligingsgroepen of firewall-filters
  - Als het verwijderen/het opnieuw toepassen van het beleid ervoor zorgt dat de verbinding wordt verbroken:
    - Controleer of de Azure-service zo is geconfigureerd dat toegang via het virtuele netwerk is toegestaan, via eindpunten, of dat het standaardbeleid voor de resource is ingesteld op *Alles toestaan*.
      > [!NOTE]      
      > Serviceresources mogen niet worden beveiligd op virtuele netwerken om toegang te krijgen via beleidsregels voor eindpunten. Als best practice op het gebied van beveiliging raden we u echter aan om de serviceresources te beveiligen op uw vertrouwde netwerken, zoals uw virtuele Azure-netwerken (via service-eindpunten) en uw on-premises netwerk (via een IP-firewall).
  
    - Controleer of het verkeer via de eindpunten wordt weergegeven in de diagnostische gegevens.
    - Controleer in de stroomlogboeken voor de netwerkbeveiligingsgroep en de opslaglogboeken of de toegang, zoals verwacht, via de service-eindpunten verloopt.
    - Neem contact op met de ondersteuning van Azure.
- De toegang wordt geweigerd voor accounts niet worden vermeld in het beleid voor service-eindpunten
  - Mogelijk wordt de toegang geblokkeerd door netwerkbeveiligingsgroepen of firewall-filters. Zorg ervoor dat de servicetag *Azure Storage* is toegestaan voor de eindpuntregio’s. Voor beleidsbeperkingen raadpleegt u [beperkingen](#limitations).
  Als een beleid wordt toegepast, wordt klassieke opslagaccounts bijvoorbeeld de toegang geweigerd.
  - Controleer of de Azure-service zo is geconfigureerd dat toegang via het virtuele netwerk is toegestaan, via eindpunten, of dat het standaardbeleid voor de resource is ingesteld op *Alles toestaan*.

## <a name="provisioning"></a>Inrichten

Op subnetten kan het beleid voor service-eindpunten worden geconfigureerd door een gebruiker met schrijftoegang tot een virtueel netwerk. Meer informatie over [ingebouwde rollen](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) in Azure en het toewijzen van specifieke machtigingen voor [aangepaste rollen](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Virtuele netwerken en Azure-serviceresources kunnen aanwezig zijn binnen hetzelfde abonnement, in verschillende abonnementen of in Azure Active Directory-tenants. 

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

