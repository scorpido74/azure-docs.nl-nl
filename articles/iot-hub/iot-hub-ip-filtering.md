---
title: IP-verbindings filters van Azure IoT Hub | Microsoft Docs
description: IP-filtering gebruiken om verbindingen van specifieke IP-adressen voor uw Azure IoT-hub te blok keren. U kunt verbindingen van afzonderlijke IP-adressen of reeksen IP-adressen blokkeren.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/25/2020
ms.author: robinsh
ms.openlocfilehash: 501b609d745e0a86bc1e00bccae54bb4f6e49376
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545273"
---
# <a name="use-ip-filters"></a>IP-filters gebruiken

Beveiliging is een belang rijk aspect van elke IoT-oplossing op basis van Azure IoT Hub. Soms moet u expliciet de IP-adressen opgeven waarvan apparaten verbinding kunnen maken als onderdeel van uw beveiligingsconfiguratie. Met de *IP-filter* functie kunt u regels configureren voor het afwijzen of accepteren van verkeer van specifieke IPv4-adressen.

## <a name="when-to-use"></a>Wanneer gebruikt u dit?

Er zijn twee specifieke use-cases wanneer het handig is om de IoT Hub-eind punten voor bepaalde IP-adressen te blok keren:

* Uw IoT-hub mag alleen verkeer ontvangen van een opgegeven bereik van IP-adressen en alle andere gegevens weigeren. U gebruikt bijvoorbeeld uw IoT-hub met [Azure Express route](../expressroute/expressroute-faqs.md#supported-services) om particuliere verbindingen te maken tussen een IOT-hub en uw on-premises infra structuur.

* U moet het verkeer afwijzen van IP-adressen die zijn geïdentificeerd als verdacht door de IoT hub-beheerder.

## <a name="how-filter-rules-are-applied"></a>Hoe filterregels worden toegepast

De IP-filter regels worden toegepast op het IoT Hub service niveau. Daarom zijn de IP-filter regels van toepassing op alle verbindingen van apparaten en back-end-apps met behulp van elk ondersteund protocol. Clients die rechtstreeks vanuit het [ingebouwde Event hub-compatibele eind punt](iot-hub-devguide-messages-read-builtin.md) lezen (niet via de IOT hub Connection String), zijn echter niet gebonden aan de IP-filter regels. 

Elke verbindings poging van een IP-adres dat overeenkomt met een afwijzings-IP-regel in uw IoT-hub ontvangt een niet-geautoriseerde 401-status code en een beschrijving. De IP-regel wordt niet vermeld in het antwoordbericht. Het afwijzen van IP-adressen kan ertoe leiden dat andere Azure-Services, zoals Azure Stream Analytics, Azure Virtual Machines of het Device Explorer in Azure Portal, niet werken met de IoT-hub.

> [!NOTE]
> Als u Azure Stream Analytics (ASA) moet gebruiken om berichten te lezen van een IoT hub waarvoor IP-filter is ingeschakeld, gebruikt u de Event Hub compatibele naam en het eind punt van uw IoT-hub om hand matig een [Event hubs stream-invoer](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs) toe te voegen aan de ASA.

## <a name="default-setting"></a>Standaardinstelling

Het **IP-filter** raster in de portal voor een IOT-hub is standaard leeg. Deze standaard instelling betekent dat uw hub verbindingen van elk IP-adres accepteert. Deze standaardinstelling komt overeen met een regel die het IP-adresbereik 0.0.0.0/0 accepteert.

Als u de pagina IP-filter instellingen wilt weer geven, selecteert u **netwerken** , **open bare toegang** en kiest u **geselecteerde IP-bereiken** :

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-default.png" alt-text="IoT Hub standaard instellingen voor IP-filter":::

## <a name="add-or-edit-an-ip-filter-rule"></a>Een IP-filterregel toevoegen of bewerken

Als u een IP-filterregel wilt toevoegen, selecteert u **+ IP-filterregel toevoegen** .

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-add-rule.png" alt-text="IoT Hub standaard instellingen voor IP-filter":::

Vul de velden in nadat u **IP-filterregel toevoegen** hebt geselecteerd.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png" alt-text="IoT Hub standaard instellingen voor IP-filter":::

* Geef een **naam** op voor de IP-filterregel. Dit moet een unieke, niet-hoofdlettergevoelige tekenreeks van maximaal 128 tekens zijn. Alleen de ASCII 7-bits alfanumerieke tekens plus `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` worden geaccepteerd.

* Geef één IPv4-adres op of een blok met IP-adressen in CIDR-notatie. Bijvoorbeeld in CIDR-notatie staat 192.168.100.0/22 voor de 1024 IPv4-adressen van 192.168.100.0 tot en met 192.168.103.255.

* Selecteer **Toestaan** of **Blokkeren** als de **actie** voor de IP-filterregel.

Nadat u de velden hebt ingevuld, selecteert u **Opslaan** om de regel op te slaan. U ziet een waarschuwing dat de update wordt uitgevoerd.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png" alt-text="IoT Hub standaard instellingen voor IP-filter":::

De optie **Toevoegen** wordt uitgeschakeld wanneer u het maximum van 10 IP-filterregels bereikt.

Als u een bestaande regel wilt bewerken, selecteert u de gegevens die u wilt wijzigen, brengt u de wijziging aan en selecteert u **Opslaan** om uw bewerking op te slaan.

## <a name="delete-an-ip-filter-rule"></a>Een IP-filterregel verwijderen

Als u een IP-filterregel wilt verwijderen, selecteert u het prullenbakpictogram op die rij en selecteert u **Opslaan** . De regel wordt verwijderd en de wijziging wordt opgeslagen.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-delete-rule.png" alt-text="IoT Hub standaard instellingen voor IP-filter":::

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>IP-filters ophalen en bijwerken met behulp van Azure CLI

De IP-filters van uw IoT Hub kunnen worden opgehaald en bijgewerkt via [Azure cli](/cli/azure/).

Als u de huidige IP-filters van uw IoT Hub wilt ophalen, voert u de volgende handelingen uit:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Hiermee wordt een JSON-object geretourneerd waarin uw bestaande IP-filters worden weer gegeven onder de `properties.ipFilterRules` sleutel:

```json
{
...
    "properties": {
        "ipFilterRules": [
        {
            "action": "Reject",
            "filterName": "MaliciousIP",
            "ipMask": "6.6.6.6/6"
        },
        {
            "action": "Allow",
            "filterName": "GoodIP",
            "ipMask": "131.107.160.200"
        },
        ...
        ],
    },
...
}
```

Als u een nieuw IP-filter voor uw IoT Hub wilt toevoegen, voert u de volgende handelingen uit:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

Als u een bestaand IP-filter in uw IoT Hub wilt verwijderen, voert u de volgende handelingen uit:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

Houd er rekening mee dat `<ipFilterIndexToRemove>` moet overeenkomen met de volg orde van IP-filters in de IOT hub `properties.ipFilterRules` .

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>IP-filters ophalen en bijwerken met behulp van Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

De IP-filters van uw IoT Hub kunnen worden opgehaald en ingesteld via [Azure PowerShell](/powershell/azure/).

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.ipFilterRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='MaliciousIP'; 'action'='Reject'; 'ipMask'='6.6.6.6/6'}

# Add your new IP filter rule
$iothubResource.Properties.ipFilterRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.ipFilterRules = @($iothubResource.Properties.ipFilterRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>IP-filter regels bijwerken met REST

U kunt ook het IP-filter van uw IoT Hub ophalen en wijzigen met behulp van het REST-eind punt van de Azure-resource provider. Zie `properties.ipFilterRules` in de [createorupdate-methode](/rest/api/iothub/iothubresource/createorupdate).

## <a name="ip-filter-rule-evaluation"></a>Evaluatie van IP-filterregel

IP-filterregels worden op volgorde toegepast en de eerste regel die overeenkomt met het IP-adres bepaalt de actie accepteren of afwijzen.

Als u bijvoorbeeld adressen in het bereik 192.168.100.0/22 wilt accepteren en de rest wilt afwijzen, moet de eerste regel in het raster het adresbereik 192.168.100.0/22 accepteren. De volgende regel moet alle adressen afwijzen met behulp van het bereik 0.0.0.0/0.

U kunt de volgorde van de IP-filterregels in het raster wijzigen door te klikken op de drie verticale puntjes aan het begin van een rij en met slepen en neerzetten.

Als u de nieuwe IP-filterregel wilt opslaan, klikt u op **Opslaan** .

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-rule-order.png" alt-text="IoT Hub standaard instellingen voor IP-filter":::

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de mogelijkheden van IoT Hub:

* [IoT Hub bewaken](monitor-iot-hub.md)
