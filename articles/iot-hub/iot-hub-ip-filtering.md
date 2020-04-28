---
title: IP-verbindings filters van Azure IoT Hub | Microsoft Docs
description: IP-filtering gebruiken om verbindingen van specifieke IP-adressen voor uw Azure IoT-hub te blok keren. U kunt verbindingen van afzonderlijke IP-adressen of bereiken blok keren.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/22/2017
ms.author: robinsh
ms.openlocfilehash: a6bd8a766f3205358a65ef2fd0816643e4261cab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "68414280"
---
# <a name="use-ip-filters"></a>IP-filters gebruiken

Beveiliging is een belang rijk aspect van elke IoT-oplossing op basis van Azure IoT Hub. Soms moet u expliciet de IP-adressen opgeven waarvan apparaten verbinding kunnen maken als onderdeel van uw beveiligings configuratie. Met de *IP-filter* functie kunt u regels configureren voor het afwijzen of accepteren van verkeer van specifieke IPv4-adressen.

## <a name="when-to-use"></a>Wanneer gebruikt u dit?

Er zijn twee specifieke use-cases wanneer het handig is om de IoT Hub-eind punten voor bepaalde IP-adressen te blok keren:

* Uw IoT-hub mag alleen verkeer ontvangen van een opgegeven bereik van IP-adressen en alle andere gegevens weigeren. U gebruikt bijvoorbeeld uw IoT-hub met [Azure Express route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) om particuliere verbindingen te maken tussen een IOT-hub en uw on-premises infra structuur.

* U moet het verkeer afwijzen van IP-adressen die zijn geïdentificeerd als verdacht door de IoT hub-beheerder.

## <a name="how-filter-rules-are-applied"></a>Hoe filter regels worden toegepast

De IP-filter regels worden toegepast op het IoT Hub service niveau. De IP-filter regels gelden daarom voor alle verbindingen van apparaten en back-end-apps met behulp van elk ondersteund protocol.

Elke verbindings poging van een IP-adres dat overeenkomt met een afwijzings-IP-regel in uw IoT-hub ontvangt een niet-geautoriseerde 401-status code en een beschrijving. De IP-regel wordt niet vermeld in het antwoord bericht.

## <a name="default-setting"></a>Standaardinstelling

Het **IP-filter** raster in de portal voor een IOT-hub is standaard leeg. Deze standaard instelling betekent dat uw hub verbindingen van elk IP-adres accepteert. Deze standaard instelling komt overeen met een regel die het IP-adres bereik 0.0.0.0/0 accepteert.

![IoT Hub standaard instellingen voor IP-filter](./media/iot-hub-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Een IP-filter regel toevoegen of bewerken

Als u een IP-filter regel wilt toevoegen, selecteert u **+ IP-filter regel toevoegen**.

![Een IP-filter regel toevoegen aan een IoT-hub](./media/iot-hub-ip-filtering/ip-filter-add-rule.png)

Wanneer u **IP-filter regel toevoegen**selecteert, vult u de velden in.

![Nadat u een IP-filter regel toevoegen hebt geselecteerd](./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png)

* Geef een **naam** op voor de IP-filter regel. Dit moet een unieke, hoofdletter gevoelige, alfanumerieke teken reeks van Maxi maal 128 tekens lang zijn. Alleen de ASCII 7-bits alfanumerieke tekens `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` plus worden geaccepteerd.

* Geef één IPv4-adres of een blok met IP-adressen in CIDR-notatie op. Bijvoorbeeld in CIDR-notatie 192.168.100.0/22 staat voor de IPv4-adressen van 1024 van 192.168.100.0 naar 192.168.103.255.

* Selecteer **toestaan** of **blok keren** als de **actie** voor de IP-filter regel.

Nadat u de velden hebt ingevuld, selecteert u **Opslaan** om de regel op te slaan. U ziet een waarschuwing dat de update wordt uitgevoerd.

![Melding over het opslaan van een IP-filter regel](./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png)

De optie **toevoegen** is uitgeschakeld wanneer u het maximum van 10 IP-filter regels bereikt.

Als u een bestaande regel wilt bewerken, selecteert u de gegevens die u wilt wijzigen, brengt u de wijziging aan en selecteert u **Opslaan** om uw bewerking op te slaan.

> [!NOTE]
> Het afwijzen van IP-adressen kan verhinderen dat andere Azure-Services (zoals Azure Stream Analytics, Azure Virtual Machines of de Device Explorer in de portal) communiceren met de IoT-hub.

> [!WARNING]
> Als u Azure Stream Analytics (ASA) gebruikt voor het lezen van berichten van een IoT-hub waarvoor IP-filtering is ingeschakeld, gebruikt u de Event hub-compatibele naam en het eind punt van uw IoT Hub in het ASA-connection string.

## <a name="delete-an-ip-filter-rule"></a>Een IP-filter regel verwijderen

Als u een IP-filter regel wilt verwijderen, selecteert u het prullenbak pictogram op die rij en selecteert u vervolgens **Opslaan**. De regel wordt verwijderd en de wijziging wordt opgeslagen.

![Een IoT Hub IP-filter regel verwijderen](./media/iot-hub-ip-filtering/ip-filter-delete-rule.png)

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>IP-filters ophalen en bijwerken met behulp van Azure CLI

De IP-filters van uw IoT Hub kunnen worden opgehaald en bijgewerkt via [Azure cli](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

Als u de huidige IP-filters van uw IoT Hub wilt ophalen, voert u de volgende handelingen uit:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Hiermee wordt een JSON-object geretourneerd waarin uw bestaande IP-filters worden weer `properties.ipFilterRules` gegeven onder de sleutel:

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

Houd er `<ipFilterIndexToRemove>` rekening mee dat moet overeenkomen met de volg orde van IP `properties.ipFilterRules`-filters in de IOT hub.

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>IP-filters ophalen en bijwerken met behulp van Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

De IP-filters van uw IoT Hub kunnen worden opgehaald en ingesteld via [Azure PowerShell](/powershell/azure/overview).

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

U kunt ook het IP-filter van uw IoT Hub ophalen en wijzigen met behulp van het REST-eind punt van de Azure-resource provider. Zie `properties.ipFilterRules` de [methode createorupdate](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate).

## <a name="ip-filter-rule-evaluation"></a>Evaluatie van IP-filter regel

IP-filter regels worden in volg orde toegepast en de eerste regel die overeenkomt met het IP-adres, bepaalt de accepteren of afwijzen.

Als u bijvoorbeeld adressen in het bereik 192.168.100.0/22 wilt accepteren en alle andere gegevens wilt afwijzen, moet de eerste regel in het raster het adres bereik 192.168.100.0/22 accepteren. De volgende regel moet alle adressen afwijzen met behulp van het bereik 0.0.0.0/0.

U kunt de volg orde van de IP-filter regels in het raster wijzigen door te klikken op de drie verticale puntjes aan het begin van een rij en met slepen en neerzetten.

Klik op **Opslaan**als u de nieuwe IP-filter regel wilt opslaan.

![De volg orde van de IoT Hub IP-filter regels wijzigen](./media/iot-hub-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de mogelijkheden van IoT Hub:

* [Controle van bewerkingen](iot-hub-operations-monitoring.md)
* [IoT Hub metrische gegevens](iot-hub-metrics.md)
