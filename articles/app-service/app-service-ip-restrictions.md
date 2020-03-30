---
title: Beperkingen voor toegang voor Azure App Service
description: Meer informatie over het beveiligen van uw app in Azure App Service door toegangsbeperkingen op te geven.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: a02c099871ce9748f4c5f604900a7c4d57bb96b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473520"
---
# <a name="azure-app-service-access-restrictions"></a>Beperkingen voor azure-servicetoegang #

Met toegangsbeperkingen u een lijst met prioriteitsgeordende toegestane/weigeringslijst definiëren die de netwerktoegang tot uw app regelt. De lijst kan IP-adressen of Subnetten van Azure Virtual Network bevatten. Wanneer er een of meer vermeldingen zijn, is er dan een impliciete "alles ontkennen" dat aan het einde van de lijst bestaat.

De mogelijkheid voor toegangsbeperkingen werkt met alle door App Service gehoste werkbelastingen, waaronder; webapps, API-apps, Linux-apps, Linux-container-apps en -functies.

Wanneer een verzoek wordt ingediend bij uw app, wordt het FROM-adres beoordeeld aan de hand van de IP-adresregels in uw lijst met toegangsbeperkingen. Als het FROM-adres zich in een subnet bevindt dat is geconfigureerd met serviceeindpunten naar Microsoft.Web, wordt het bronsubnet vergeleken met de virtuele netwerkregels in uw lijst met toegangsbeperkingen. Als het adres geen toegang heeft op basis van de regels in de lijst, antwoordt de service met een [HTTP 403-statuscode.](https://en.wikipedia.org/wiki/HTTP_403)

De mogelijkheid voor toegangsbeperkingen wordt geïmplementeerd in de front-endrollen van App-service, die stroomopwaarts staan van de werknemershosts waar uw code wordt uitgevoerd. Daarom zijn toegangsbeperkingen effectief netwerk-ACL's.

De mogelijkheid om de toegang tot uw web-app vanaf een Azure Virtual Network (VNet) te beperken, wordt [serviceeindpunten][serviceendpoints]genoemd. Met serviceeindpunten u de toegang tot een multi-tenantservice van geselecteerde subnetten beperken. Het moet worden ingeschakeld aan zowel de netwerkkant als de service waarmee het wordt ingeschakeld. Het werkt niet om verkeer te beperken tot apps die worden gehost in een App-serviceomgeving. Als u zich in een App-serviceomgeving bevindt, u de toegang tot uw app beheren met IP-adresregels.

![toegangsbeperkingen stromen](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>Toegangsbeperkingsregels toevoegen en bewerken in de portal ##

Als u een regel voor toegangsbeperking aan uw app wilt toevoegen, gebruikt u het menu om>**netwerktoegangsbeperkingen** te openen en klikt u op **Toegangsbeperkingen configureren** **Network**

![App Service-netwerkopties](media/app-service-ip-restrictions/access-restrictions.png)  

In de gebruikersinterface voor toegangsbeperkingen u de lijst met toegangsbeperkingen bekijken die voor uw app zijn gedefinieerd.

![lijsttoegangsbeperkingen](media/app-service-ip-restrictions/access-restrictions-browse.png)

In de lijst worden alle huidige beperkingen weergegeven die in uw app staan. Als u een VNet-beperking op uw app hebt, wordt in de tabel weergegeven of serviceeindpunten zijn ingeschakeld voor Microsoft.Web. Als er geen gedefinieerde beperkingen zijn op uw app, is uw app overal toegankelijk.  

## <a name="adding-ip-address-rules"></a>IP-adresregels toevoegen

U op **[+] Regel toevoegen klikken** om een nieuwe regel voor toegangsbeperking toe te voegen. Zodra u een regel toevoegt, wordt deze onmiddellijk van kracht. Regels worden gehandhaafd in prioriteitvolgorde vanaf het laagste aantal en omhoog gaan. Er is een impliciete ontkennen alles wat van kracht is zodra u zelfs een enkele regel toe te voegen.

Wanneer u een regel maakt, moet u toestaan/weigeren en ook het type regel selecteren. U bent ook verplicht om de prioriteitswaarde te geven en waartoe u de toegang beperkt.  U optioneel een naam en beschrijving aan de regel toevoegen.  

![een IP-toegangsbeperkingsregel toevoegen](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

Als u een op IP-adres gebaseerde regel wilt instellen, selecteert u een type IPv4 of IPv6. IP-adresnotatie moet worden opgegeven in CIDR-notatie voor zowel IPv4- als IPv6-adressen. Als u een exact adres wilt opgeven, u iets als 1.2.3.4/32 gebruiken waarbij de eerste vier octetten uw IP-adres vertegenwoordigen en /32 het masker is. De IPv4 CIDR-notatie voor alle adressen is 0.0.0.0/0. Als u meer wilt weten over CIDR-notatie, u [Classless Inter-Domain Routing lezen.](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 

## <a name="service-endpoints"></a>Service-eindpunten

Met serviceeindpunten u de toegang tot geselecteerde virtuele netwerksubnetten van Azure beperken. Als u de toegang tot een specifiek subnet wilt beperken, maakt u een beperkingsregel met een type virtueel netwerk. U het abonnement, VNet en subnet kiezen waarmee u toegang wilt toestaan of weigeren. Als serviceeindpunten nog niet zijn ingeschakeld met Microsoft.Web voor het subnet dat u hebt geselecteerd, wordt dit automatisch voor u ingeschakeld, tenzij u het selectievakje inschakelt met de vraag om dit niet te doen. De situatie waarin u het op de app wilt inschakelen, maar niet het subnet is grotendeels gerelateerd aan als u de machtigingen hebt om serviceeindpunten op het subnet in te schakelen of niet. Als u iemand anders nodig hebt om serviceeindpunten in te schakelen op het subnet, u het selectievakje inschakelen en uw app laten configureren voor serviceeindpunten in afwachting dat deze later op het subnet wordt ingeschakeld. 

![Een VNet-toegangsbeperkingsregel toevoegen](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

Serviceeindpunten kunnen niet worden gebruikt om de toegang tot apps die worden uitgevoerd in een App-serviceomgeving te beperken. Wanneer uw app zich in een App-serviceomgeving bevindt, u de toegang tot uw app beheren met IP-toegangsregels. 

Met serviceeindpunten u uw app configureren met Application Gateways of andere WAF-apparaten. U ook toepassingen met meerdere lagen configureren met beveiligde backends. Voor meer informatie over enkele van de mogelijkheden, lees [Netwerkfuncties en App Service](networking-features.md) en [Application Gateway integratie met service eindpunten.](networking/app-gateway-with-service-endpoints.md)

## <a name="managing-access-restriction-rules"></a>Regels voor toegangsbeperking beheren

U op elke rij klikken om een bestaande toegangsbeperkingsregel te bewerken. Bewerkingen zijn onmiddellijk van kracht, inclusief wijzigingen in het bestellen van prioriteit.

![een toegangsbeperkingsregel bewerken](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

Wanneer u een regel bewerkt, u het type niet wijzigen tussen een IP-adresregel en een regel voor virtueel netwerk. 

![een toegangsbeperkingsregel bewerken](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

Als u een regel wilt verwijderen, klikt u op de **regel ...** op de regel en klikt u vervolgens op **Verwijderen**.

![toegangsbeperkingsregel verwijderen](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="blocking-a-single-ip-address"></a>Eén IP-adres blokkeren ##

Wanneer u uw eerste IP-beperkingsregel toevoegt, voegt de service een expliciete **regel Weigeren toe** met een prioriteit van 2147483647. In de praktijk wordt de expliciete **regel Alle weigeren** de laatste regel uitgevoerd en wordt de toegang tot een IP-adres dat niet expliciet is toegestaan met behulp van een regel **Toestaan** geblokkeerd.

Voor het scenario waarin gebruikers expliciet één IP-adres of IP-adresblok willen blokkeren, maar al het andere toegang willen verlenen, is het noodzakelijk om een expliciete **regel Toestaan alle** toe te voegen.

![één ip-adres blokkeren](media/app-service-ip-restrictions/block-single-address.png)

## <a name="scm-site"></a>SCM-site 

U niet alleen de toegang tot uw app beheren, maar u ook de toegang tot de scm-site die door uw app wordt gebruikt, beperken. De scm site is het web deploy endpoint en ook de Kudu console. U vanuit de app afzonderlijk toegangsbeperkingen toewijzen aan de scm-site of dezelfde set gebruiken voor zowel de app als de scm-site. Wanneer u het selectievakje inschakelt om dezelfde beperkingen als uw app te hebben, wordt alles gewist. Als u het selectievakje uitschakelt, worden de instellingen die u eerder op de scm-site had toegepast. 

![lijsttoegangsbeperkingen](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>Programmatische manipulatie van toegangsbeperkingsregels ##

[Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/access-restriction?view=azure-cli-latest) en [Azure PowerShell](https://docs.microsoft.com/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule?view=azps-3.1.0) hebben ondersteuning voor het bewerken van toegangsbeperkingen. Voorbeeld van het toevoegen van een toegangsbeperking met Azure CLI:

```azurecli-interactive
az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
    --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
```
Voorbeeld van het toevoegen van een toegangsbeperking met Azure PowerShell:

```azurepowershell-interactive
Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
    -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
```

Waarden kunnen ook handmatig worden ingesteld met een [Azure REST API](https://docs.microsoft.com/rest/api/azure/) PUT-bewerking op de app-configuratie in Resource Manager of met behulp van een Azure Resource Manager-sjabloon. U bijvoorbeeld resources.azure.com gebruiken en het ipSecurityRestrictions-blok bewerken om de vereiste JSON toe te voegen.

De locatie voor deze informatie in Resource Manager is:

management.azure.com/subscriptions/**abonnement-ID**/resourceGroepen/**resourcegroepen**/providers/Microsoft.Web/sites/**webappnaam**/config/web?api-version=2018-02-01

De syntaxis van JSON voor het eerdere voorbeeld is:
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

## <a name="azure-function-app-access-restrictions"></a>Beperkingen voor toegang voor Azure-functie-apps

Toegangsbeperkingen zijn ook beschikbaar voor functie-apps met dezelfde functionaliteit als app-serviceabonnementen. Als u toegangsbeperkingen inschakelt, schakelt u de portalcodeeditor uit voor niet-toegestane IP-gebruikers.

## <a name="next-steps"></a>Volgende stappen
[Toegangsbeperkingen voor Azure Function Apps](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)

[Application Gateway-integratie met serviceeindpunten](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
