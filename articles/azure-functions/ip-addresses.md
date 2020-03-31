---
title: IP-adressen in Azure-functies
description: Meer informatie over het vinden van inkomende en uitgaande IP-adressen voor functie-apps en wat de oorzaak is van deze wijzigingen.
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: a1c4174b8f1f2349cbd35c32cbee468ee5b4cd4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276489"
---
# <a name="ip-addresses-in-azure-functions"></a>IP-adressen in Azure-functies

In dit artikel worden de volgende onderwerpen uitgelegd met betrekking tot IP-adressen van functie-apps:

* De IP-adressen vinden die momenteel door een functie-app worden gebruikt.
* Wat zorgt ervoor dat de IP-adressen van een functie-app worden gewijzigd.
* De IP-adressen beperken die toegang hebben tot een functie-app.
* Hoe krijg je speciale IP-adressen voor een functie-app.

IP-adressen zijn gekoppeld aan functie-apps, niet aan afzonderlijke functies. Binnenkomende HTTP-aanvragen kunnen het binnenkomende IP-adres niet gebruiken om afzonderlijke functies aan te roepen. ze moeten de standaarddomeinnaam (functionappname.azurewebsites.net) of een aangepaste domeinnaam gebruiken.

## <a name="function-app-inbound-ip-address"></a>Inbound IP-adres van de functie-app

Elke functie-app heeft één binnenkomend IP-adres. Ga als het gaat om het vinden van dat IP-adres:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Navigeer naar de functie-app.
3. Selecteer **Platformfuncties**.
4. Selecteer **Eigenschappen**en het binnenkomende IP-adres wordt weergegeven onder **Virtueel IP-adres**.

## <a name="function-app-outbound-ip-addresses"></a><a name="find-outbound-ip-addresses"></a>Uitgaande IP-adressen van de functie-app

Elke functie-app heeft een set beschikbare uitgaande IP-adressen. Elke uitgaande verbinding van een functie, zoals een back-enddatabase, gebruikt een van de beschikbare uitgaande IP-adressen als het IP-adres van de oorsprong. U niet van tevoren weten welk IP-adres een bepaalde verbinding zal gebruiken. Daarom moet uw back-endservice de firewall openen voor alle uitgaande IP-adressen van de functie-app.

Ga als lid van het nieuwe IP-adres naar een functie-app:

1. Meld u aan bij de [Azure Resource Explorer](https://resources.azure.com).
2. Selecteer **abonnementen > {uw abonnement} > providers > Microsoft.Web >-sites**.
3. Zoek in het JSON-paneel `id` de site met een eigenschap die eindigt op naam van uw functie-app.
4. Zie `outboundIpAddresses` `possibleOutboundIpAddresses`en . 

De set `outboundIpAddresses` van is momenteel beschikbaar voor de functie-app. De set `possibleOutboundIpAddresses` bevat IP-adressen die alleen beschikbaar zijn als de functie-app [wordt geschaald naar andere prijsniveaus.](#outbound-ip-address-changes)

Een alternatieve manier om de beschikbare uitgaande IP-adressen te vinden is door gebruik te maken van de [Cloud Shell:](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```
> [!NOTE]
> Wanneer een functie-app die wordt uitgevoerd op het [verbruiksplan](functions-scale.md#consumption-plan) wordt geschaald, kan een nieuwe reeks uitgaande IP-adressen worden toegewezen. Wanneer u het verbruiksplan uitvoert, moet u mogelijk het hele datacenter op de witte lijst plaatsen.

## <a name="data-center-outbound-ip-addresses"></a>Uitgaande IP-adressen voor datacenters

Als u de uitgaande IP-adressen die door uw functie-apps worden gebruikt, op de witte lijst moet zetten, is een andere optie om het datacenter van de functie-apps (Azure-regio) op de witte lijst te zetten. U [een JSON-bestand downloaden met IP-adressen voor alle Azure-datacenters.](https://www.microsoft.com/en-us/download/details.aspx?id=56519) Zoek vervolgens het JSON-fragment dat van toepassing is op het gebied waarin uw functie-app wordt uitgevoerd.

Zo zou bijvoorbeeld het JSON-fragment in West-Europa eruit kunnen zien:

```
{
  "name": "AzureCloud.westeurope",
  "id": "AzureCloud.westeurope",
  "properties": {
    "changeNumber": 9,
    "region": "westeurope",
    "platform": "Azure",
    "systemService": "",
    "addressPrefixes": [
      "13.69.0.0/17",
      "13.73.128.0/18",
      ... Some IP addresses not shown here
     "213.199.180.192/27",
     "213.199.183.0/24"
    ]
  }
}
```

 Voor informatie over wanneer dit bestand wordt bijgewerkt en wanneer de IP-adressen worden gewijzigd, vouwt u het gedeelte **Details** van de [pagina Downloadcentrum](https://www.microsoft.com/en-us/download/details.aspx?id=56519)uit.

## <a name="inbound-ip-address-changes"></a><a name="inbound-ip-address-changes"></a>Binnenkomende IP-adreswijzigingen

Het binnenkomende IP-adres **kan** veranderen wanneer u:

- Verwijder een functie-app en maak deze opnieuw in een andere resourcegroep.
- Verwijder de laatste functie-app in een resourcegroep en regiocombinatie en maak deze opnieuw.
- Verwijder een SSL-binding, bijvoorbeeld tijdens [het verlengen van het certificaat.](../app-service/configure-ssl-certificate.md#renew-certificate)

Wanneer uw functie-app wordt uitgevoerd in een [verbruiksplan,](functions-scale.md#consumption-plan)kan het binnenkomende IP-adres ook veranderen, zelfs als u geen acties hebt uitgevoerd, zoals de [hierboven genoemde](#inbound-ip-address-changes).

## <a name="outbound-ip-address-changes"></a>Wijzigingen in uitgaande IP-adres

De set beschikbare uitgaande IP-adressen voor een functie-app kan veranderen wanneer u:

* Onderneem actie die het binnenkomende IP-adres kan wijzigen.
* Wijzig de prijscategorie app-service-abonnement. De lijst met alle mogelijke uitgaande IP-adressen die uw app voor `possibleOutboundIPAddresses` alle prijsniveaus kan gebruiken, bevindt zich in de accommodatie. Zie [Uitgaande IP's zoeken](#find-outbound-ip-addresses).

Wanneer uw functie-app wordt uitgevoerd in een [verbruiksplan,](functions-scale.md#consumption-plan)kan het uitgaande IP-adres ook veranderen, zelfs als u geen acties hebt uitgevoerd, zoals de [hierboven genoemde](#inbound-ip-address-changes).

Een uitgaande IP-adreswijziging opzettelijk forceren:

1. Schaal uw App Service-abonnement omhoog of omlaag tussen de prijzen van Standaard en Premium v2.
2. Wacht 10 minuten.
3. Schaal terug naar waar je begon.

## <a name="ip-address-restrictions"></a>IP-adresbeperkingen

U een lijst met IP-adressen configureren die u wilt toestaan of de toegang tot een functie-app wilt weigeren. Zie [Statische IP-beperkingen van Azure App Service](../app-service/app-service-ip-restrictions.md)voor meer informatie .

## <a name="dedicated-ip-addresses"></a>Speciale IP-adressen

Als u statische, specifieke IP-adressen nodig hebt, raden we [App Service-omgevingen](../app-service/environment/intro.md) (de [geïsoleerde laag](https://azure.microsoft.com/pricing/details/app-service/) van App Service-abonnementen) aan. Zie [IP-adressen van app-serviceomgeving omgeving](../app-service/environment/network-info.md#ase-ip-addresses) en [hoe u binnenkomend verkeer naar een App-serviceomgeving beheert](../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md).

Ga als u erop uit dat uw functie-app wordt uitgevoerd in een App-serviceomgeving:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Navigeer naar de functie-app.
3. Selecteer het tabblad **Overzicht**.
4. De laag App-service-abonnement wordt weergegeven onder **app-serviceplan/prijslaag**. De prijscategorie App-serviceomgeving is **geïsoleerd**.
 
Als alternatief u de [Cloud Shell](../cloud-shell/quickstart.md)gebruiken:

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query sku --output tsv
```

De App `sku` Service-omgeving is `Isolated`.

## <a name="next-steps"></a>Volgende stappen

Een veelvoorkomende oorzaak van IP-wijzigingen zijn wijzigingen in de schaal van de functie-app. [Meer informatie over het schalen van de functie-app](functions-scale.md).
