---
title: Azure API Management-services implementeren in meerdere Azure-regio's
titleSuffix: Azure API Management
description: Meer informatie over het implementeren van een Azure API Management-serviceinstantie naar meerdere Azure-regio's.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/12/2019
ms.author: apimpm
ms.openlocfilehash: 5c71f37741de06b8633e7eafaae2f29823214f74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442672"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Exemplaar van Azure API Management-service implementeren in meerdere Azure-regio's

Azure API Management ondersteunt implementatie met meerdere regio's, waardoor API-uitgevers één Azure API-beheerservice kunnen distribueren over een willekeurig aantal ondersteunde Azure-regio's. Multi-regiofunctie helpt de aanvragenlatentie te verminderen die worden waargenomen door geografisch gedistribueerde API-consumenten en verbetert de beschikbaarheid van de service als één regio offline gaat.

Een nieuwe Azure API Management-service bevat in eerste instantie slechts één [eenheid][unit] in één Azure-regio, het primaire gebied. Extra regio's kunnen worden toegevoegd aan de primaire of secundaire regio's. Een API Management-gatewaycomponent wordt geïmplementeerd in elke geselecteerde primaire en secundaire regio. Binnenkomende API-aanvragen worden automatisch doorgestuurd naar de dichtstbijzijnde regio. Als een regio offline gaat, worden de API-aanvragen automatisch doorde regio naar de dichtstbijzijnde gateway doorgestuurd.

> [!NOTE]
> Alleen de gatewaycomponent van API-beheer wordt geïmplementeerd in alle regio's. De component servicebeheer en de ontwikkelaarsportal worden alleen gehost in het primaire gebied. Daarom wordt in het geval van de uitval van de primaire regio de toegang tot de ontwikkelaarsportal en de mogelijkheid om de configuratie te wijzigen (bijvoorbeeld het toevoegen van API's, het toepassen van beleid) verminderd totdat de primaire regio weer online is. Terwijl het primaire gebied offline beschikbaar is, blijven secundaire regio's het API-verkeer weergeven met behulp van de nieuwste configuratie waarover ze beschikken.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="deploy-an-api-management-service-instance-to-a-new-region"></a><a name="add-region"> </a>Een API-beheerserviceinstantie implementeren in een nieuwe regio

> [!NOTE]
> Zie [Een API Management-serviceinstantie maken][create an api management service instance]als u nog geen API Management-serviceinstantie hebt gemaakt.

Navigeer in de Azure-portal naar de pagina **Schalen en prijzen** voor uw API Management-serviceinstantie.

![Tabblad Schalen][api-management-scale-service]

Als u wilt implementeren in een nieuw gebied, klikt u op **+ Regio toevoegen** vanaf de werkbalk.

![Regio toevoegen][api-management-add-region]

Selecteer de locatie in de vervolgkeuzelijst en stel het aantal eenheden in voor met de schuifregelaar.

![Eenheden opgeven][api-management-select-location-units]

Klik **op Toevoegen** om uw selectie in de tabel Locaties te plaatsen.

Herhaal dit proces totdat u alle locaties hebt geconfigureerd en klik op **Opslaan** vanaf de werkbalk om het implementatieproces te starten.

## <a name="delete-an-api-management-service-instance-from-a-location"></a><a name="remove-region"> </a>Een API-beheerserviceinstantie verwijderen van een locatie

Navigeer in de Azure-portal naar de pagina **Schalen en prijzen** voor uw API Management-serviceinstantie.

![Tabblad Schalen][api-management-scale-service]

Voor de locatie die u wilt verwijderen, opent u het contextmenu met de **...** knop aan de rechterkant van de tabel. Selecteer de optie **Verwijderen.**

Bevestig de verwijdering en klik op **Opslaan** om de wijzigingen toe te passen.

## <a name="route-api-calls-to-regional-backend-services"></a><a name="route-backend"> </a>Route API-aanroepen naar regionale backendservices

Azure API Management beschikt over slechts één URL van de backendservice. Hoewel er Azure API Management-exemplaren zijn in verschillende regio's, stuurt de API-gateway nog steeds aanvragen door naar dezelfde backendservice, die slechts in één regio wordt geïmplementeerd. In dit geval komt de prestatiewinst alleen van reacties die zijn opgeslagen in Azure API Management in een regio die specifiek is voor de aanvraag, maar het contact opnemen met de backend over de hele wereld kan nog steeds leiden tot hoge latentie.

Als u de geografische distributie van uw systeem volledig wilt benutten, moet u backendservices hebben geïmplementeerd in dezelfde regio's als Azure API Management-exemplaren. Vervolgens u `@(context.Deployment.Region)` met behulp van beleid en eigenschap het verkeer doorsturen naar lokale exemplaren van uw backend.

1. Navigeer naar uw Azure API Management-exemplaar en klik in het linkermenu op **API's.**
2. Selecteer de gewenste API.
3. Klik op **Code-editor** in de vervolgkeuzelijst pijl in de **binnenkomende verwerking**.

    ![API-codeeditor](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. Gebruik `set-backend` het gecombineerd `choose` met voorwaardelijke beleidsregels om `<inbound> </inbound>` een goed routeringsbeleid in het gedeelte van het bestand te maken.

    Het onderstaande XML-bestand werkt bijvoorbeeld voor regio's in West-VS en Oost-Azië:

    ```xml
    <policies>
        <inbound>
            <base />
            <choose>
                <when condition="@("West US".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-us.com/" />
                </when>
                <when condition="@("East Asia".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-asia.com/" />
                </when>
                <otherwise>
                    <set-backend-service base-url="http://contoso-other.com/" />
                </otherwise>
            </choose>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```

> [!TIP]
> U ook uw backendservices vooraan staan met [Azure Traffic Manager,](https://azure.microsoft.com/services/traffic-manager/)de API-aanroepen doorverwijzen naar de Traffic Manager en de routering automatisch oplossen.

## <a name="use-custom-routing-to-api-management-regional-gateways"></a><a name="custom-routing"> </a>Aangepaste routering gebruiken voor regionale gateways van API-beheer

API-beheer leidt de aanvragen naar een regionale _gateway_ op basis van [de laagste latentie.](../traffic-manager/traffic-manager-routing-methods.md#performance) Hoewel het niet mogelijk is om deze instelling in API-beheer te overschrijven, u uw eigen Traffic Manager gebruiken met aangepaste routeringsregels.

1. Maak uw eigen [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/).
1. Als u een aangepast domein gebruikt, [gebruikt u het met de Verkeersbeheerservice](../traffic-manager/traffic-manager-point-internet-domain.md) in plaats van de API-beheerservice.
1. [Configureer de regionale eindpunten API Beheer in Traffic Manager](../traffic-manager/traffic-manager-manage-endpoints.md). De regionale eindpunten volgen `https://<service-name>-<region>-01.regional.azure-api.net`bijvoorbeeld `https://contoso-westus2-01.regional.azure-api.net`het URL-patroon van .
1. [De regionale statuseindpunten apibeheer configureren in Traffic Manager](../traffic-manager/traffic-manager-monitoring.md). De regionale statuseindpunten volgen `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef`bijvoorbeeld `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef`het URL-patroon van .
1. Geef [de routeringsmethode](../traffic-manager/traffic-manager-routing-methods.md) van de verkeersbeheerder op.

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png
[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png
[create an api management service instance]: get-started-create-service-instance.md
[get started with azure api management]: get-started-create-service-instance.md
[deploy an api management service instance to a new region]: #add-region
[delete an api management service instance from a region]: #remove-region
[unit]: https://azure.microsoft.com/pricing/details/api-management/
[premium]: https://azure.microsoft.com/pricing/details/api-management/
