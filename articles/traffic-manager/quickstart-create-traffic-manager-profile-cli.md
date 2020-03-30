---
title: Snelstart:Een profiel maken voor HA-toepassingen - Azure CLI - Azure Traffic Manager
description: In dit quickstart-artikel wordt beschreven hoe u een Traffic Manager-profiel maakt om een zeer beschikbare webtoepassing te maken.
services: traffic-manager
author: rohinkoul
mnager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2019
ms.author: rohink
ms.openlocfilehash: e19850243498fc24c9a726f4603590df15f3a046
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79531512"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-cli"></a>Snelstart: een Traffic Manager-profiel maken voor een zeer beschikbare webtoepassing met Azure CLI

In deze quickstart wordt beschreven hoe u een Traffic Manager-profiel maakt die hoge beschikbaarheid van uw webtoepassing biedt.

In deze snelstart maakt u twee exemplaren van een webtoepassing. Ze worden elk in een andere Azure-regio uitgevoerd. U maakt een Traffic Manager-profiel op basis van [eindpuntprioriteit](traffic-manager-routing-methods.md#priority-traffic-routing-method). het profiel stuurt gebruikersverkeer door naar de primaire site waar de webtoepassing wordt uitgevoerd. Traffic Manager bewaakt de webtoepassing continu. Als de primaire site niet beschikbaar is, biedt Traffic Manager automatische failover voor de back-upsite.

Als u nog geen abonnement op Azure hebt, maak dan nu een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze zelfstudie versie 2.0.28 of hoger van Azure CLI uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Een resourcegroep maken
Maak een resourcegroep maken met [az group create](https://docs.microsoft.com/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* op de *locatie Eastus geaald:*

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

## <a name="create-a-traffic-manager-profile"></a>Een Traffic Manager-profiel maken

Maak een Traffic Manager-profiel aan met [behulp van het AZ-netwerkverkeersbeheerderprofiel](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-create) dat gebruikersverkeer aanstuurt op basis van de prioriteit van eindpunten.

Vervang in het volgende voorbeeld **<profile_name>** door een unieke profielnaam van Traffic Manager.

```azurecli-interactive

az network traffic-manager profile create \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --routing-method Priority \
    --path "/" \
    --protocol HTTP \
    --unique-dns-name <profile_name> \
    --ttl 30 \
    --port 80

```

## <a name="create-web-apps"></a>Web-apps maken

Voor deze quickstart moeten twee exemplaren van een webtoepassing worden geïmplementeerd in twee verschillende Azure-regio's (*VS - oost* en *Europa - west*). Elk exemplaar dient als primair en failover-eindpunt voor Traffic Manager.

### <a name="create-web-app-service-plans"></a>Webapp-serviceplannen maken
Maak webapp-serviceplannen met behulp van [az-appservice-abonnement maken](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) voor de twee exemplaren van de webtoepassing die u in twee verschillende Azure-regio's implementeert.

Vervang in het volgende voorbeeld **<appspname_eastus>** en<appspname_westeurope **>** door een unieke naam van het app-serviceplan

```azurecli-interactive

az appservice plan create \
    --name <appspname_eastus> \
    --resource-group myResourceGroup \
    --location eastus \
    --sku S1

az appservice plan create \
    --name <appspname_westeurope> \
    --resource-group myResourceGroup \
    --location westeurope \
    --sku S1

```

### <a name="create-a-web-app-in-the-app-service-plan"></a>Een web-app maken in het app-serviceplan
Maak twee exemplaren van de webtoepassing met [az webapp maken](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) in de App Service-plannen in de *Azure-regio's Oost-VS* en *West-Europa.*

Vervang in het volgende voorbeeld **<app1name_eastus>** en<app2name_westeurope **>** door een unieke appnaam en vervang<**appspname_eastus>** en<**appspname_westeurope>** door de naam die wordt gebruikt om de appserviceplannen in de vorige sectie te maken.

```azurecli-interactive

az webapp create \
    --name <app1name_eastus> \
    --plan <appspname_eastus> \
    --resource-group myResourceGroup

az webapp create \
    --name <app2name_westeurope> \
    --plan <appspname_westeurope> \
    --resource-group myResourceGroup

```

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-eindpunten toevoegen
Voeg de twee Web Apps als Traffic Manager-eindpunten met behulp van het EINDPUNT van [az-netwerkbeheer als](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-create) volgt toe aan het Traffic Manager-profiel:

- Bepaal de web-app-id en voeg de web-app toe in de *Regio Oost-VS* Azure als het primaire eindpunt om al het gebruikersverkeer te routeren. 
- Bepaal de web-app-id en voeg de web-app in de *Azure-regio West-Europa* toe als het failovereindpunt. 

Als het primaire eindpunt niet beschikbaar is, wordt het verkeer automatisch naar het failover-eindpunt gerouteerd.

Vervang in het volgende voorbeeld **<app1name_eastus>** en<app2name_westeurope **>** door de app-namen die in de vorige sectie voor elke regio zijn gemaakt, vervang<**appspname_eastus>** en<**appspname_westeurope>** door de naam die wordt gebruikt om de appserviceplannen in de vorige sectie te maken en vervang<**profile_name>** door de profielnaam die in de vorige sectie werd gebruikt. 

**Oost-Amerikaans eindpunt**

```azurecli-interactive

az webapp show \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --query id

```

Noteer id die in uitvoer wordt weergegeven en gebruik in de volgende opdracht om het eindpunt toe te voegen:

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 1 \
    --endpoint-status Enabled
```

**Eindpunt West-Europa**

```azurecli-interactive

az webapp show \
    --name <app2name_westeurope> \
    --resource-group myResourceGroup \
    --query id

```

Noteer id die in uitvoer wordt weergegeven en gebruik in de volgende opdracht om het eindpunt toe te voegen:

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_westeurope> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 2 \
    --endpoint-status Enabled

```

## <a name="test-your-traffic-manager-profile"></a>Uw Traffic Manager-profiel testen

In deze sectie controleert u de domeinnaam van uw Traffic Manager-profiel. Tevens configureert u het primaire eindpunt zodanig dat het niet beschikbaar is. Ten slotte kunt u zien dat de web-app nog steeds beschikbaar is. Dat komt omdat Traffic Manager het verkeer naar het failover-eindpunt stuurt.

Vervang in het volgende voorbeeld **<app1name_eastus>** en<app2name_westeurope **>** door de app-namen die in de vorige sectie voor elke regio zijn gemaakt, vervang<**appspname_eastus>** en<**appspname_westeurope>** door de naam die wordt gebruikt om de appserviceplannen in de vorige sectie te maken en vervang<**profile_name>** door de profielnaam die in de vorige sectie werd gebruikt.

### <a name="determine-the-dns-name"></a>DNS-naam bepalen

Bepaal de DNS-naam van het Traffic [Manager-profiel met behulp van het AZ-netwerkverkeersbeheerderprofiel weergeven](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-show).

```azurecli-interactive

az network traffic-manager profile show \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --query dnsConfig.fqdn

```

Kopieer de waarde **Relatieve DnsName.** De DNS-naam van uw Traffic Manager-profiel is *http://<* relativednsname *>.trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>Traffic Manager in werking zien
1. Voer in een webbrowser de DNS-naam van uw Traffic Manager-profiel (*http://<* relativednsname *>.trafficmanager.net)* in om de standaardwebsite van uw web-app te bekijken.

    > [!NOTE]
    > In dit quickstartscenario worden alle aanvragen gerouteerd naar het primaire eindpunt. Het is ingesteld op **Priority 1**.
2. Als u Traffic Manager-failover in actie wilt weergeven, schakelt u uw primaire site uit met [de update van het eindpunt van het AZ-netwerkverkeersbeheerder](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update).

   ```azurecli-interactive

    az network traffic-manager endpoint update \
        --name <app1name_eastus> \
        --resource-group myResourceGroup \
        --profile-name <profile_name> \
        --type azureEndpoints \
        --endpoint-status Disabled
    
   ```

3. Kopieer de DNS-naam van uw Traffic Manager-profiel (*http://<* relativednsname *>.trafficmanager.net)* om de website in een nieuwe webbrowsersessie te bekijken.
4. Controleer of de web-app nog beschikbaar is.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent, verwijdert u de brongroepen, webtoepassingen en alle gerelateerde bronnen met de verwijderen van [de AZ-groep.](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete)

```azurecli-interactive

az group delete \
    --resource-group myResourceGroup

```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Traffic Manager-profiel gemaakt dat hoge beschikbaarheid biedt voor uw webtoepassing. Voor meer informatie over het routeren van verkeer gaat u door naar de zelfstudies voor Traffic Manager.

> [!div class="nextstepaction"]
> [Traffic Manager tutorials](tutorial-traffic-manager-improve-website-response.md) (Traffic Manager-zelfstudies)
