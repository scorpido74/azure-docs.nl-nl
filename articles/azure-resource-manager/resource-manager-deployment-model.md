---
title: Resource Manager en klassieke implementatie | Microsoft Docs
description: Lees hier wat de verschillen zijn tussen het implementatiemodel van Resource Manager en het klassieke implementatiemodel (of Service Management).
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: tomfitz
ms.openlocfilehash: 9356a1603a39f2ac4d18b27445bf0f8d3a555d7e
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982462"
---
# <a name="azure-resource-manager-vs-classic-deployment-understand-deployment-models-and-the-state-of-your-resources"></a>Azure Resource Manager versus klassieke implementatie: begrip van implementatiemodellen en de status van uw resources

> [!NOTE]
> De informatie in dit artikel wordt alleen gebruikt wanneer u een migratie van de klassieke implementatie naar de Azure Resource Manager-implementatie uitvoert.

In dit artikel vindt u informatie over Azure Resource Manager en het klassieke implementatiemodel. Het implementatiemodel van Resource Manager en het klassieke implementatiemodel zijn twee verschillende manieren voor het implementeren en beheren van uw Azure-oplossingen. U gebruikt de modellen via twee verschillende API-sets, en de geïmplementeerde resources kunnen sterk afwijken. De twee modellen zijn niet compatibel met elkaar. In dit artikel worden deze verschillen beschreven.

Om de implementatie en het beheer van resources te vereenvoudigen, wordt aangeraden om Resource Manager te gebruiken voor alle nieuwe resources. Indien mogelijk is het ook raadzaam om bestaande resources opnieuw te implementeren via Resource Manager.

Als u geen ervaring hebt met Resource Manager, is het misschien handig om eerst de terminologie door te nemen die wordt beschreven in het [overzicht van Azure Resource Manager](resource-group-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="history-of-the-deployment-models"></a>Geschiedenis van de implementatiemodellen
In Azure was in eerste instantie alleen het klassieke implementatiemodel beschikbaar. In dit model waren alle resources zelfstandig en er was geen enkele manier om gerelateerde resources te groeperen. In plaats daarvan moest u handmatig bijhouden welke resources in uw oplossing of toepassing werden gebruikt en niet vergeten om de resources op een gecoördineerde manier te beheren. Als u een oplossing wilde implementeren, moest u elke resource afzonderlijk maken via de portal of een script schrijven waarmee alle bronnen in de juiste volgorde werden geïmplementeerd. Als u een oplossing wilde verwijderen, moest u elke resource afzonderlijk verwijderen. Het was niet mogelijk om op een eenvoudige manier van beleid voor toegangsbeheer toe te passen en bij te werken voor verwante resources. Het was evenmin mogelijk om tags te koppelen aan resources, om aan de hand van labels de resources makkelijker te bewaken en factureren.

In 2014 werd Azure Resource Manager geïntroduceerd, en daarmee het concept van resourcegroepen. Een resourcegroep is een container voor resources die een gemeenschappelijke levenscyclus delen. Het implementatiemodel van Resource Manager biedt diverse voordelen:

* U kunt alle services voor uw oplossing als een groep implementeren, beheren en bewaken, in plaats van deze services afzonderlijk te verwerken.
* U kunt de oplossing herhaaldelijk implementeren gedurende de levenscyclus en erop vertrouwen dat uw resources op een consistente manier worden geïmplementeerd.
* U kunt toegangsbeheer toepassen op alle resources in de resourcegroep. Deze beleidsregels worden automatisch toegepast wanneer nieuwe resources worden toegevoegd aan de resourcegroep.
* U kunt tags toepassen op de resources om alle resources in uw abonnement op een logische manier te organiseren.
* U kunt JSON (JavaScript Object Notation) gebruiken voor het definiëren van de infrastructuur voor uw oplossing. Het JSON-bestand is in feite de Resource Manager-sjabloon.
* U kunt de afhankelijkheden tussen resources zo definiëren dat deze in de juiste volgorde worden geïmplementeerd.

Op het moment dat Resource Manager werd toegevoegd, werden alle resources met terugwerkende kracht toegevoegd aan standaardresourcegroepen. Als u nu een resource maakt via klassieke implementatie, wordt de resource automatisch gemaakt binnen een standaardresourcegroep voor de service, zelfs als u de resourcegroep niet hebt opgegeven bij de implementatie. Het feit dat de resource deel uitmaakt van een resourcegroep betekent echter niet dat de resource is geconverteerd naar het Resource Manager-model.

## <a name="understand-support-for-the-models"></a>Ondersteuning voor de modellen begrijpen

Er zijn drie mogelijke scenario's:

1. Cloud-Services biedt geen ondersteuning voor het Resource Manager-implementatiemodel.
2. Virtuele machines, opslagaccounts en virtuele netwerken ondersteunen zowel het implementatiemodel van Resource Manager als het klassieke implementatiemodel.
3. Alle andere Azure-services ondersteunen Resource Manager.

Als de resource is gemaakt via de klassieke implementatie, geldt voor virtuele machines, opslagaccounts en virtuele netwerken dat u alleen klassieke bewerkingen kunt uitvoeren op de resource. Als de virtuele machine, het opslagaccount of het virtuele netwerk is gemaakt via Resource Manager, moet u alleen Resource Manager-bewerkingen gebruiken. Dit verschil kan problemen geven wanneer uw abonnement een combinatie van resources bevat die zijn gemaakt via Resource Manager en de klassieke implementatie. Deze combinatie van resources kan onverwachte resultaten tot gevolg hebben omdat de resources niet dezelfde bewerkingen ondersteunen.

In sommige gevallen kunt u met een opdracht van Resource Manager gegevens ophalen van een resource die via de klassieke implementatie is gemaakt of een beheertaak uitvoeren zoals het verplaatsen van een klassieke resource naar een andere resourcegroep. Maar hierdoor mag niet de indruk worden gewekt dat de resource ondersteuning biedt voor bewerkingen van Resource Manager. Stel dat u een resourcegroep hebt met daarin een virtuele machine die is gemaakt met het klassieke implementatiemodel. Als u de volgende PowerShell-opdracht van Resource Manager uitvoert:

```powershell
Get-AzResource -ResourceGroupName ExampleGroup -ResourceType Microsoft.ClassicCompute/virtualMachines
```

wordt deze virtuele machine geretourneerd:

```powershell
Name              : ExampleClassicVM
ResourceId        : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.ClassicCompute/virtualMachines/ExampleClassicVM
ResourceName      : ExampleClassicVM
ResourceType      : Microsoft.ClassicCompute/virtualMachines
ResourceGroupName : ExampleGroup
Location          : westus
SubscriptionId    : {guid}
```

Met de cmdlet **Get-AzVM** van Resource Manager worden echter alleen virtuele machines geretourneerd die zijn geïmplementeerd via Resource Manager. Met de volgende opdracht wordt niet de virtuele machine geretourneerd die via de klassieke implementatie is gemaakt.

```powershell
Get-AzVM -ResourceGroupName ExampleGroup
```

Tags worden alleen ondersteund door resources die via Resource Manager zijn gemaakt. U kunt geen tags toepassen op klassieke resources.

## <a name="changes-for-compute-network-and-storage"></a>Wijzigingen voor reken-, netwerk- en opslagresources
In het volgende diagram ziet u de reken-, netwerk- en opslagresources die zijn geïmplementeerd via Resource Manager.

![Architectuur van Resource Manager](./media/resource-manager-deployment-model/arm_arch3.png)

Dit zijn de verschillende relaties tussen de resources:

* Alle resources bestaan binnen een resourcegroep.
* De virtuele machine is afhankelijk van een specifiek opslagaccount dat is gedefinieerd in de Storage-resourceprovider (SRP) voor het opslaan van schijven in blob-opslag (vereist).
* De virtuele machine verwijst naar een specifieke NIC die is gedefinieerd in de Network-resourceprovider (NRP) (vereist) en een beschikbaarheidsset die is gedefinieerd in de Compute-resourceprovider (CRP) (optioneel).
* De NIC verwijst naar het toegewezen IP-adres van de virtuele machine (vereist), het subnet van het virtuele netwerk voor de virtuele machine (vereist) en een netwerkbeveiligingsgroep (optioneel).
* Het subnet binnen een virtueel netwerk verwijst naar een netwerkbeveiligingsgroep (optioneel).
* De instantie van de load balancer verwijst naar de back-endpool van IP-adressen met daarin de NIC van een virtuele machine (optioneel) en het openbare of particuliere IP-adres (optioneel) van een load balancer.

Dit zijn de onderdelen en hun relaties voor een klassieke implementatie:

![klassieke architectuur](./media/resource-manager-deployment-model/arm_arch1.png)

De klassieke oplossing voor het hosten van een virtuele machine omvat:

* Een vereiste cloudservice die als een container fungeert voor het hosten van virtuele machines (rekentaken). Virtuele machines worden automatisch ingericht met een netwerkinterfacekaart (NIC) en een IP-adres dat wordt toegewezen door Azure. Bovendien bevat de cloudservice een instantie van een externe load balancer, een openbaar IP-adres en standaardeindpunten om verkeer van Extern bureaublad en extern PowerShell-verkeer toe te staan voor virtuele Windows-machines en SSH-verkeer (Secure Shell) voor virtuele Linux-machines.
* Een vereist opslagaccount waarmee de VHD's voor een virtuele machine worden opgeslagen, met inbegrip van het besturingssysteem, tijdelijke en extra gegevensschijven (opslag).
* Een optioneel virtueel netwerk dat fungeert als een extra container, waarin u een structuur met subnetten kunt maken en het subnet kunt toewijzen waarin de virtuele machine zich bevindt (netwerk).

In de volgende tabel wordt beschreven wat er is veranderd aan de interactie tussen de resourceproviders voor Compute, Network en Storage:

| Item | Klassiek | Resource Manager |
| --- | --- | --- |
| Cloudservice voor virtuele machines |Cloudservice was een container voor de opslag van de virtuele machines waarvoor de beschikbaarheid van het platform en taakverdeling vereist was. |Cloudservice is niet langer een object dat is vereist voor het maken van een virtuele machine met het nieuwe model. |
| Virtuele netwerken |Een virtueel netwerk is optioneel voor de virtuele machine. Als hiervoor wordt gekozen, kan het virtuele netwerk niet worden geïmplementeerd met Resource Manager. |Een virtuele machine vereist een virtueel netwerk dat is geïmplementeerd met Resource Manager. |
| Opslagaccounts |De virtuele machine vereist een opslagaccount waarmee de VHD's voor het besturingssysteem, tijdelijke en extra gegevensschijven worden opgeslagen. |De virtuele machine vereist een opslagaccount voor het opslaan van de schijven in de blob-opslag. |
| Beschikbaarheidssets |De beschikbaarheid van het platform werd aangegeven door de configuratie van dezelfde "AvailabilitySetName" op de virtuele machines. Het maximumaantal foutdomeinen was 2. |Beschikbaarheidsset is een resource die beschikbaar wordt gesteld door Microsoft.Compute-provider. Virtuele machines die uiterst beschikbaar moeten zijn, worden opgenomen in de beschikbaarheidsset. Het maximumaantal foutdomeinen is nu 3. |
| Affiniteitsgroepen |Voor het maken van virtuele netwerken waren affiniteitsgroepen vereist. Door de introductie van regionale virtuele netwerken is dit echter niet meer nodig. |Het concept van affiniteitsgroepen bestaat niet meer in de API's die via Azure Resource Manager beschikbaar worden gesteld. |
| Taakverdeling |Bij het maken van een cloudservice wordt een impliciete load balancer voor de geïmplementeerde virtuele machines aangemaakt. |De load balancer is een resource die beschikbaar wordt gesteld door de Microsoft.Compute-provider. De primaire netwerkinterface van de virtuele machines waarvoor de taken moeten worden verdeeld moet verwijzen naar de load balancer. Load balancers kunnen intern of extern zijn. Een instantie van de load balancer verwijst naar de back-endpool van IP-adressen met daarin de NIC van een virtuele machine (optioneel) en het openbare of particuliere IP-adres (optioneel) van een load balancer. |
| Virtueel IP-adres |Cloud Services krijgt een standaard-VIP (virtueel IP-adres) toegewezen wanneer een VM wordt toegevoegd aan een cloudservice. Het virtuele IP-adres is het adres dat is gekoppeld aan de impliciete load balancer. |Het openbare IP-adres is een resource die beschikbaar wordt gesteld door de Microsoft.Compute-provider. Een openbaar IP-adres kan statisch (gereserveerd) of dynamisch zijn. Dynamische openbare IP-adressen kunnen worden toegewezen aan een load balancer. Openbare IP-adressen kunnen worden beveiligd met beveiligingsgroepen. |
| Gereserveerd IP-adres |U kunt een IP-adres in Azure reserveren en dit koppelen aan een cloudservice om ervoor te zorgen dat het IP-adres is vergrendeld. |Een openbaar IP-adres kan in de statische modus worden gemaakt en biedt dezelfde mogelijkheden als een gereserveerd IP-adres. |
| Openbaar IP-adres (PIP) per VM |Openbare IP-adressen kunnen ook rechtstreeks aan een VM worden gekoppeld. |Het openbare IP-adres is een resource die beschikbaar wordt gesteld door de Microsoft.Compute-provider. Een openbaar IP-adres kan statisch (gereserveerd) of dynamisch zijn. |
| Eindpunten |Invoereindpunten moesten eerder op een virtuele machine worden geconfigureerd voor open connectiviteit voor bepaalde poorten. Een van de algemene modi voor het maken van verbinding met virtuele machines wordt gerealiseerd door het instellen van invoereindpunten. |Inkomende NAT-regels kunnen op load balancers worden geconfigureerd om eindpunten in te schakelen op bepaalde poorten voor verbinding met de VM's. |
| DNS-naam |Een cloudservice krijgt een impliciete, globaal unieke DNS-naam. Bijvoorbeeld: `mycoffeeshop.cloudapp.net`. |DNS-namen zijn optionele parameters die voor de resource van een openbaar IP-adres kunnen worden opgegeven. De FQDN heeft de volgende notatie: `<domainlabel>.<region>.cloudapp.azure.com`. |
| Netwerkinterfaces |Primaire en secundaire netwerkinterface en de bijbehorende eigenschappen werden gedefinieerd als de netwerkconfiguratie van een virtuele machine. |De netwerkinterface is een resource die beschikbaar wordt gesteld door de Microsoft.Compute-provider. De levenscyclus van de netwerkinterface is niet gekoppeld aan een virtuele machine. Een NIC verwijst naar het toegewezen IP-adres van de virtuele machine (vereist), het subnet van het virtuele netwerk voor de virtuele machine (vereist) en een netwerkbeveiligingsgroep (optioneel). |

Zie [Verschillende implementatiemodellen gebruiken om vanuit de portal verbinding te maken met virtuele netwerken](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) om te lezen hoe u met behulp van verschillende implementatiemodellen verbinding kunt maken met virtuele netwerken.

## <a name="migrate-from-classic-to-resource-manager"></a>Migreren van klassiek naar Resource Manager
Als u klaar bent om uw resources te migreren van de klassieke implementatie naar de Resource Manager-implementatie, leest u deze onderwerpen:

1. [Technische details over door platforms ondersteunde migratie van klassiek naar Azure Resource Manager](../virtual-machines/windows/migration-classic-resource-manager-deep-dive.md)
2. [Platformondersteunde migratie van IaaS-resources van het klassieke implementatiemodel naar Azure Resource Manager](../virtual-machines/windows/migration-classic-resource-manager-overview.md)
3. [IaaS-resources migreren van klassiek naar Azure Resource Manager met behulp van Azure PowerShell](../virtual-machines/windows/migration-classic-resource-manager-ps.md)
4. [IaaS-resources migreren van klassiek naar Azure Resource Manager met behulp van Azure CLI](../virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md)

## <a name="frequently-asked-questions"></a>Veelgestelde vragen
**Kan ik een virtuele machine maken met Resource Manager voor implementatie in een virtueel netwerk dat is gemaakt met behulp van het klassieke implementatiemodel?**

Deze configuratie wordt niet ondersteund. U kunt Resource Manager niet gebruiken voor het implementeren van een virtuele machine in een virtueel netwerk dat is gemaakt met het klassieke implementatiemodel.

**Kan ik met Resource Manager een virtuele machine maken van een gebruikersinstallatiekopie die is gemaakt met het klassieke implementatiemodel?**

Deze configuratie wordt niet ondersteund. U kunt wel de VHD-bestanden kopiëren uit een opslagaccount dat is gemaakt met het klassieke implementatiemodel en de bestanden toevoegen aan een nieuw account dat is gemaakt met Resource Manager.

**Wat zijn de gevolgen voor het quotum voor mijn abonnement?**

De quota voor de virtuele machines, virtuele netwerken en opslagaccounts die zijn gemaakt met behulp van Azure Resource Manager zijn gescheiden van andere quota. Elk abonnement krijgt quota voor het maken van de resources met behulp van de nieuwe API's. U vindt [hier](../azure-subscription-service-limits.md) meer informatie over de extra quota.

**Kan ik mijn geautomatiseerde scripts voor het inrichten van virtuele machines, virtuele netwerken en opslagaccounts via de API's van Azure Resource Manager blijven gebruiken?**

Alle automatisering en scripts die u hebt gemaakt, blijven werken voor de bestaande virtuele machines en virtuele netwerken die zijn gemaakt in de modus Azure Service Management. De scripts moeten echter worden bijgewerkt voor het gebruik van het nieuwe schema voor het maken van dezelfde resources via de modus Resource Manager.

**Waar vind ik voorbeelden van Azure Resource Manager-sjablonen?**

U vindt een uitgebreide set startsjablonen voor Azure Resource Manager op [Azure-snelstartsjablonen](https://azure.microsoft.com/documentation/templates/).

## <a name="next-steps"></a>Volgende stappen

* Zie [Uw eerste Azure Resource Manager-sjabloon maken en implementeren](resource-manager-template-walkthrough.md) voor stapsgewijze instructies voor het maken van een sjabloon met de definitie van een virtuele machine, een opslagaccount en een virtueel netwerk.
* [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](resource-group-template-deploy.md) voor informatie over de opdrachten voor het implementeren van een sjabloon.

