---
title: Ip-verbindingsfilters voor Azure IoT Hub | Microsoft Documenten
description: IP-filtering gebruiken om verbindingen van specifieke IP-adressen te blokkeren voor uw Azure IoT-hub. U verbindingen blokkeren vanaf afzonderlijke of bereiken van IP-adressen.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/22/2017
ms.author: robinsh
ms.openlocfilehash: a6bd8a766f3205358a65ef2fd0816643e4261cab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68414280"
---
# <a name="use-ip-filters"></a>IP-filters gebruiken

Beveiliging is een belangrijk aspect van elke IoT-oplossing op basis van Azure IoT Hub. Soms moet u expliciet de IP-adressen opgeven van waaruit apparaten verbinding kunnen maken als onderdeel van uw beveiligingsconfiguratie. Met de *IP-filterfunctie* u regels configureren voor het weigeren of accepteren van verkeer van specifieke IPv4-adressen.

## <a name="when-to-use"></a>Wanneer gebruikt u dit?

Er zijn twee specifieke use-cases wanneer het nuttig is om de IoT Hub-eindpunten voor bepaalde IP-adressen te blokkeren:

* Uw IoT-hub mag alleen verkeer ontvangen van een bepaald aantal IP-adressen en al het andere afwijzen. U gebruikt bijvoorbeeld uw IoT-hub met [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) om privéverbindingen te maken tussen een IoT-hub en uw on-premises infrastructuur.

* U moet verkeer van IP-adressen weigeren die door de IoT-hubbeheerder als verdacht zijn aangemerkt.

## <a name="how-filter-rules-are-applied"></a>Hoe filterregels worden toegepast

De IP-filterregels worden toegepast op het serviceniveau van IoT Hub. Daarom zijn de IP-filterregels van toepassing op alle verbindingen van apparaten en back-end apps met behulp van een ondersteund protocol.

Elke verbindingspoging van een IP-adres dat overeenkomt met een weigerende IP-regel in uw IoT-hub, ontvangt een ongeautoriseerde statuscode en -beschrijving van 401. In het antwoordbericht wordt de IP-regel niet vermeld.

## <a name="default-setting"></a>Standaardinstelling

Standaard is het **IP-filterraster** in de portal voor een IoT-hub leeg. Deze standaardinstelling betekent dat uw hub verbindingen accepteert vanaf elk IP-adres. Deze standaardinstelling is gelijk aan een regel die het IP-adresbereik 0.0.0/0/0 accepteert.

![Standaard IP-filterinstellingen voor IoT Hub](./media/iot-hub-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Een IP-filterregel toevoegen of bewerken

Als u een IP-filterregel wilt toevoegen, selecteert u **+ IP-filterregel toevoegen**.

![Een IP-filterregel toevoegen aan een IoT-hub](./media/iot-hub-ip-filtering/ip-filter-add-rule.png)

Nadat u **IP-filterregel toevoegen hebt**geselecteerd, vult u de velden in.

![Na het selecteren van een IP-filterregel toevoegen](./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png)

* Geef een **naam** op voor de IP-filterregel. Dit moet een unieke, case-ongevoelige, alfanumerieke string tot 128 tekens lang zijn. Alleen de ASCII 7-bits `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` alfanumerieke tekens plus worden geaccepteerd.

* Geef één IPv4-adres of een blok IP-adressen op in CIDR-notatie. In CIDR-notatie 192.168.100.0/22 staat bijvoorbeeld de 1024 IPv4-adressen van 192.168.100.0 tot 192.168.103.255.

* Selecteer **Toestaan** of **Blokkeren** als **actie** voor de IP-filterregel.

Nadat u de velden hebt ingevuld, selecteert u **Opslaan** om de regel op te slaan. U ziet een waarschuwing waarin wordt gemeld dat de update aan de gang is.

![Melding over het opslaan van een IP-filterregel](./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png)

De optie **Toevoegen** is uitgeschakeld wanneer u het maximum van 10 IP-filterregels bereikt.

Als u een bestaande regel wilt bewerken, selecteert u de gegevens die u wilt wijzigen, wijzigt u de wijziging en selecteert **u Opslaan** om uw bewerking op te slaan.

> [!NOTE]
> Als u IP-adressen weigert, kan worden voorkomen dat andere Azure Services (zoals Azure Stream Analytics, Azure Virtual Machines of Device Explorer in de portal) interactie hebben met de IoT-hub.

> [!WARNING]
> Als u Azure Stream Analytics (ASA) gebruikt om berichten te lezen vanaf een IoT-hub met IP-filtering ingeschakeld, gebruikt u de naam en het eindpunt van uw IoT-hub voor gebeurtenissenhub in de ASA-verbindingstekenreeks.

## <a name="delete-an-ip-filter-rule"></a>Een IP-filterregel verwijderen

Als u een IP-filterregel wilt verwijderen, selecteert u het pictogram prullenbak in die rij en selecteert u **Opslaan**. De regel wordt verwijderd en de wijziging wordt opgeslagen.

![Een IP-filterregel voor IoT Hub verwijderen](./media/iot-hub-ip-filtering/ip-filter-delete-rule.png)

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>IP-filters ophalen en bijwerken met Azure CLI

De IP-filters van uw IoT Hub kunnen worden opgehaald en bijgewerkt via [Azure CLI.](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

Voer het als nodig op om de huidige IP-filters van uw IoT-hub op te halen:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Hiermee wordt een JSON-object retourneren waarin uw `properties.ipFilterRules` bestaande IP-filters onder de sleutel worden vermeld:

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

Voer het volgende uit om een nieuw IP-filter voor uw IoT-hub toe te voegen:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

Voer het als u een bestaand IP-filter in uw IoT-hub wilt verwijderen:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

Houd `<ipFilterIndexToRemove>` er rekening mee dat moet overeenkomen met het `properties.ipFilterRules`bestellen van IP-filters in uw IoT Hub's.

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>IP-filters ophalen en bijwerken met Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

De IP-filters van uw IoT Hub kunnen worden opgehaald en ingesteld via [Azure PowerShell.](/powershell/azure/overview)

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

## <a name="update-ip-filter-rules-using-rest"></a>IP-filterregels bijwerken met REST

U ook het IP-filter van uw IoT Hub ophalen en wijzigen met behulp van het REST-eindpunt van azure resource Provider. Zie `properties.ipFilterRules` in [createorupdate methode](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate).

## <a name="ip-filter-rule-evaluation"></a>Evaluatie van IP-filterregels

IP-filterregels worden op volgorde toegepast en de eerste regel die overeenkomt met het IP-adres bepaalt de actie Accepteren of weigeren.

Als u bijvoorbeeld adressen in het bereik 192.168.100.0/22 wilt accepteren en al het andere wilt afwijzen, moet de eerste regel in het raster het adresbereik 192.168.100.0/22 accepteren. De volgende regel moet alle adressen afwijzen met het bereik 0.0.0.0/0.

U de volgorde van uw IP-filterregels in het raster wijzigen door op de drie verticale stippen aan het begin van een rij te klikken en slepen en neerzetten te gebruiken.

Als u de nieuwe IP-filterregelorder wilt opslaan, klikt u op **Opslaan**.

![De volgorde van uw IP-filterregels voor IoT Hub wijzigen](./media/iot-hub-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Volgende stappen

Zie:

* [Controle van bewerkingen](iot-hub-operations-monitoring.md)
* [IoT Hub-statistieken](iot-hub-metrics.md)
