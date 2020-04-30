---
title: Toegangs beperkingen Azure App Service
description: Meer informatie over het beveiligen van uw app in Azure App Service door toegangs beperkingen op te geven.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 298555da2056bc4c16d4d7b16615604f9798b91b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81639272"
---
# <a name="azure-app-service-access-restrictions"></a>Toegangs beperkingen Azure App Service

Met toegangs beperkingen kunt u een lijst met toegestane of geweigerde prioriteits instellingen definiëren waarmee de netwerk toegang tot uw app wordt beheerd. De lijst kan IP-adressen of Azure Virtual Network subnetten bevatten. Wanneer er sprake is van een of meer vermeldingen, wordt er een impliciete ' weigeren ' aan het einde van de lijst.

De mogelijkheden voor toegangs beperkingen werken met alle App Service gehoste werk belastingen, inclusief; Web apps, API apps, Linux-apps, Linux-container-apps en-functies.

Wanneer een aanvraag wordt gedaan voor uw app, wordt het van-adres geëvalueerd op basis van de IP-adres regels in de lijst met toegangs beperkingen. Als het van-adres zich in een subnet bevindt dat is geconfigureerd met Service-eind punten naar micro soft. Web, wordt het bron-subnet vergeleken met de regels voor het virtuele netwerk in de lijst met toegangs beperkingen. Als het adres geen toegang is toegestaan op basis van de regels in de lijst, reageert de service met een [HTTP 403-](https://en.wikipedia.org/wiki/HTTP_403) status code.

De functie voor toegangs beperkingen wordt geïmplementeerd in de front-end rollen van App Service, die een upstream zijn van de werk nemers waar de code wordt uitgevoerd. Toegangs beperkingen zijn daarom effectief netwerk-Acl's.

De mogelijkheid om de toegang tot uw web-app vanuit een Azure Virtual Network (VNet) te beperken, wordt [service-eind punten][serviceendpoints]genoemd. Met Service-eind punten kunt u de toegang beperken tot een multi tenant-service van geselecteerde subnetten. Deze moet worden ingeschakeld op zowel de netwerk kant als de service waarmee deze wordt ingeschakeld. Het werkt niet om het verkeer te beperken tot apps die worden gehost in een App Service Environment. Als u een App Service Environment hebt, kunt u de toegang tot uw app beheren met de regels voor IP-adressen.

![toegangs beperkingen stroom](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Toegangs beperkings regels toevoegen en bewerken in de portal ##

Als u een toegangs beperkings regel wilt toevoegen aan uw app, gebruikt u het menu om beperkingen voor **netwerk**>**toegang** te openen en klikt u op **toegangs beperkingen configureren**

![App Service-netwerk opties](media/app-service-ip-restrictions/access-restrictions.png)  

U kunt de lijst met toegangs beperkings regels die voor uw app zijn gedefinieerd, bekijken in de gebruikers interface voor toegangs beperkingen.

![toegangs beperkingen weer geven](media/app-service-ip-restrictions/access-restrictions-browse.png)

In de lijst worden alle huidige beperkingen voor uw app weer gegeven. Als u een VNet-beperking hebt voor uw app, wordt in de tabel weer gegeven of service-eind punten zijn ingeschakeld voor micro soft. Web. Als er geen beperkingen zijn gedefinieerd voor uw app, is uw app vanaf elke locatie toegankelijk.  

## <a name="adding-ip-address-rules"></a>IP-adres regels toevoegen

U kunt klikken op **[+] regel toevoegen** om een nieuwe toegangs beperkings regel toe te voegen. Zodra u een regel hebt toegevoegd, wordt deze direct van kracht. Regels worden in volg orde van prioriteit afgedwongen, te beginnen met het laagste nummer en worden opgevolgd. Er is een impliciete weigering alles die van kracht is nadat u zelfs één regel hebt toegevoegd.

Wanneer u een regel maakt, moet u toestaan/weigeren en ook het type regel selecteren. U moet ook de prioriteits waarde opgeven en waarvoor u de toegang wilt beperken.  U kunt eventueel een naam en beschrijving toevoegen aan de regel.  

![een beperkings regel voor IP-toegang toevoegen](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

Als u een regel op basis van een IP-adres wilt instellen, selecteert u een type IPv4 of IPv6. IP-adres notatie moet worden opgegeven in CIDR-notatie voor IPv4-en IPv6-adressen. Als u een exact adres wilt opgeven, kunt u een van de voor keuren, zoals 1.2.3.4/32, gebruiken waarbij de eerste vier octetten uw IP-adres vertegenwoordigen en/32 is het masker. De IPv4 CIDR-notatie voor alle adressen is 0.0.0.0/0. Voor meer informatie over CIDR-notatie kunt u [Klasseloze route ring](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)lezen. 

## <a name="service-endpoints"></a>Service-eindpunten

Met Service-eind punten kunt u de toegang tot geselecteerde subnetten van het virtuele Azure-netwerk beperken. Als u de toegang tot een specifiek subnet wilt beperken, maakt u een beperkings regel met een type Virtual Network. U kunt het abonnement, het VNet en het subnet kiezen waarvoor u toegang wilt toestaan of weigeren. Als service-eind punten niet al zijn ingeschakeld met micro soft. web voor het geselecteerde subnet, wordt het automatisch ingeschakeld voor u tenzij u het selectie vakje inschakelt waarin u wordt gevraagd om dat te doen. De situatie waarin u het wilt inschakelen voor de app, maar niet het subnet is grotendeels gerelateerd aan als u over de juiste machtigingen beschikt om service-eind punten in te scha kelen op het subnet. Als iemand anders moet worden opgehaald om service-eind punten in het subnet in te scha kelen, kunt u het selectie vakje inschakelen en uw app configureren voor service-eind punten in afwachting van later in het subnet. 

![een VNet-toegangs beperkings regel toevoegen](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Service-eind punten kunnen niet worden gebruikt om de toegang te beperken tot apps die worden uitgevoerd in een App Service Environment. Wanneer uw app zich in een App Service Environment bevindt, kunt u de toegang tot uw app beheren met de IP-toegangs regels. 

Met Service-eind punten kunt u uw app configureren met toepassings gateways of andere WAF-apparaten. U kunt ook toepassingen met meerdere lagen configureren met beveiligde back-endservers. Lees voor meer informatie over sommige mogelijkheden [netwerk functies en app service](networking-features.md) en [Application Gateway integratie met Service-eind punten](networking/app-gateway-with-service-endpoints.md).

## <a name="managing-access-restriction-rules"></a>Toegangs beperkings regels beheren

U kunt op elke rij klikken om een bestaande toegangs beperkings regel te bewerken. Bewerkingen zijn direct van kracht, inclusief wijzigingen in de volg orde van prioriteit.

![een toegangs beperkings regel bewerken](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

Wanneer u een regel bewerkt, kunt u het type niet wijzigen tussen een IP-adres regel en een Virtual Network regel. 

![een toegangs beperkings regel bewerken](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

Als u een regel wilt verwijderen, klikt u op de **..** . op de regel en klikt u vervolgens op **verwijderen**.

![toegangs beperkings regel verwijderen](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="blocking-a-single-ip-address"></a>Eén IP-adres blok keren ##

Wanneer u uw eerste IP-beperkings regel toevoegt, voegt de service een expliciete regel voor het **weigeren van alle** toe met de prioriteit 2147483647. In de praktijk is de regel expliciet voor het **weigeren van alle** regels de laatste regel uitgevoerd en wordt de toegang tot alle IP-adressen geblokkeerd die niet expliciet zijn toegestaan met een regel voor **toestaan** .

Voor het scenario waarin gebruikers een enkel IP-adres of IP-adres blok expliciet willen blok keren, maar alle andere toegang mogen toestaan, is het nood zakelijk om een expliciete regel voor **toestaan toe** te voegen.

![Eén IP-adres blok keren](media/app-service-ip-restrictions/block-single-address.png)

## <a name="scm-site"></a>SCM-site 

Naast het beheren van de toegang tot uw app, kunt u ook de toegang beperken tot de SCM-site die wordt gebruikt door uw app. De SCM-site is het Web Deploy-eind punt en ook de kudu-console. U kunt de toegangs beperkingen afzonderlijk toewijzen aan de SCM-site vanuit de app of dezelfde set gebruiken voor zowel de app als de SCM-site. Wanneer u het selectie vakje inschakelt om dezelfde beperkingen als uw app te hebben, is alles leeg. Als u het selectie vakje uitschakelt, worden alle instellingen die u eerder op de SCM-site had, toegepast. 

![toegangs beperkingen weer geven](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>Programmatische manipulatie van toegangs beperkings regels ##

[Azure cli](https://docs.microsoft.com/cli/azure/webapp/config/access-restriction?view=azure-cli-latest) en [Azure PowerShell](https://docs.microsoft.com/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule?view=azps-3.1.0) biedt ondersteuning voor het bewerken van toegangs beperkingen. Voor beeld van het toevoegen van een toegangs beperking met behulp van Azure CLI:

```azurecli-interactive
az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
    --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
```
Voor beeld van het toevoegen van een toegangs beperking met behulp van Azure PowerShell:

```azurepowershell-interactive
Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
    -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
```

Waarden kunnen ook hand matig worden ingesteld met een [Azure rest API](https://docs.microsoft.com/rest/api/azure/) put-bewerking in de app-configuratie in Resource Manager of met behulp van een Azure Resource Manager sjabloon. U kunt bijvoorbeeld resources.azure.com gebruiken en het ipSecurityRestrictions-blok bewerken om de vereiste JSON toe te voegen.

De locatie voor deze informatie in Resource Manager is:

management.azure.com/subscriptions/-**abonnements-id**/resourceGroups/**resource groepen**/providers/Microsoft.web/sites/**Web-app-naam**/config/Web? API-Version = 2018-02-01

De JSON-syntaxis voor het vorige voor beeld is:
```json
{
  "properties": {
    "ipSecurityRestrictions": [
      {
        "ipAddress": "122.133.144.0/24",
        "action": "Allow",
        "priority": 100,
        "name": "IP example rule"
      }
    ]
  }
}
```

## <a name="azure-functions-access-restrictions"></a>Toegangs beperkingen Azure Functions

Er zijn ook toegangs beperkingen beschikbaar voor functie-apps met dezelfde functionaliteit als App Service-abonnementen. Als u toegangs beperkingen inschakelt, wordt de portal code-editor uitgeschakeld voor alle niet-toegestane IP-adressen.

## <a name="next-steps"></a>Volgende stappen
[Toegangs beperkingen voor Azure Functions](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)

[Integratie met Service-eind punten Application Gateway](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
