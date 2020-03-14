---
title: IP-verbindings filters van Azure IoT DPS | Microsoft Docs
description: IP-filtering gebruiken om verbindingen van specifieke IP-adressen te blok keren naar uw Azure IoT DPS-exemplaar. U kunt verbindingen van afzonderlijke IP-adressen of bereiken blok keren.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 0b13040b39ed491ec4fee4d6922d41f086edeeb2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284913"
---
# <a name="use-ip-filters"></a>IP-filters gebruiken

Beveiliging is een belang rijk aspect van elke IoT-oplossing. Soms moet u expliciet de IP-adressen opgeven waarvan apparaten verbinding kunnen maken als onderdeel van uw beveiligings configuratie. Met de *IP-filter* functie voor Azure IOT hub Device PROVISIONING service (DPS) kunt u regels configureren voor het afwijzen of accepteren van verkeer van specifieke IPv4-adressen.

## <a name="when-to-use"></a>Wanneer gebruikt u dit?

Er zijn twee specifieke situaties waarin het nuttig is om verbindingen met een DPS-eind punt van bepaalde IP-adressen te blok keren:

* Uw DPS mag alleen verkeer ontvangen van een opgegeven bereik van IP-adressen en alle andere gegevens weigeren. U gebruikt bijvoorbeeld uw DPS met [Azure Express route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) om particuliere verbindingen te maken tussen een DPS en uw apparaten.

* U moet het verkeer afwijzen van IP-adressen die als verdacht zijn geïdentificeerd door de DPS-beheerder.

## <a name="how-filter-rules-are-applied"></a>Hoe regels worden toegepast

De IP-filter regels worden toegepast op het niveau van het DPS-exemplaar. De IP-filter regels gelden daarom voor alle verbindingen van apparaten en back-end-apps met behulp van elk ondersteund protocol.

Elke verbindings poging van een IP-adres dat overeenkomt met een afwijzings-IP-regel in uw DPS-exemplaar ontvangt een niet-geautoriseerde 401-status code en een beschrijving. De IP-regel wordt niet vermeld in het antwoord bericht.

## <a name="default-setting"></a>Standaardinstelling

Het **IP-filter** raster in de portal voor DPS is standaard leeg. Deze standaard instelling betekent dat uw DPS verbindingen van elk IP-adres accepteert. Deze instelling is gelijk aan een regel waarmee het 0.0.0.0/0 IP-adresbereik accepteert.

![Standaard IP-filter instellingen voor IoT DPS](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Een IP-filter regel toevoegen of bewerken

Als u een IP-filter regel wilt toevoegen, selecteert u **+ IP-filter regel toevoegen**.

![Een IP-filter regel toevoegen aan een IoT-DPS](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

Wanneer u **IP-filter regel toevoegen**selecteert, vult u de velden in.

![Nadat u een IP-filter regel toevoegen hebt geselecteerd](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* Geef een **naam** op voor de IP-filter regel. Dit moet een unieke, hoofdletter gevoelige, alfanumerieke teken reeks van Maxi maal 128 tekens lang zijn. Alleen de ASCII 7-bits alfanumerieke tekens plus `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` worden geaccepteerd.

* Geef één IPv4-adres of een blok met IP-adressen in CIDR-notatie op. Bijvoorbeeld in CIDR-notatie 192.168.100.0/22 staat voor de IPv4-adressen van 1024 van 192.168.100.0 naar 192.168.103.255.

* Selecteer **toestaan** of **blok keren** als de **actie** voor de IP-filter regel.

Nadat u de velden hebt ingevuld, selecteert u **Opslaan** om de regel op te slaan. U ziet een waarschuwing dat de update wordt uitgevoerd.

![Melding over het opslaan van een IP-filter regel](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

De optie **toevoegen** is uitgeschakeld wanneer u het maximum van 10 IP-filter regels bereikt.

Als u een bestaande regel wilt bewerken, selecteert u de gegevens die u wilt wijzigen, brengt u de wijziging aan en selecteert u **Opslaan** om uw bewerking op te slaan.

> [!NOTE]
> Het afwijzen van IP-adressen kan verhinderen dat andere Azure-Services communiceren met het DPS-exemplaar.

## <a name="delete-an-ip-filter-rule"></a>Een IP-filter regel verwijderen

Als u een IP-filter regel wilt verwijderen, selecteert u het prullenbak pictogram op die rij en selecteert u vervolgens **Opslaan**. De regel wordt verwijderd en de wijziging wordt opgeslagen.

![Een IP-filter regel van een IoT-DPS verwijderen](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>IP-filter regels in code bijwerken

U kunt het IP-filter van DPS ophalen en wijzigen met behulp van het REST-eind punt van de Azure-resource provider. Zie `properties.ipFilterRules` in de [methode createorupdate](https://docs.microsoft.com/rest/api/iot-dps/iotdpsresource/createorupdate).

Het bijwerken van de IP-filter regels van DPS wordt momenteel niet ondersteund met Azure CLI of Azure PowerShell, maar kan worden uitgevoerd met Azure Resource Manager sjablonen. Zie [Azure Resource Manager sjablonen](../azure-resource-manager/templates/overview.md) voor meer informatie over het gebruik van Resource Manager-sjablonen. De volgende sjabloon voorbeelden laten zien hoe u DPS IP-filter regels kunt maken, bewerken en verwijderen.

### <a name="add-an-ip-filter-rule"></a>Een IP-filter regel toevoegen

In het volgende sjabloon voorbeeld wordt een nieuwe IP-filter regel gemaakt met de naam ' AllowAll ', waarmee al het verkeer wordt geaccepteerd.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", 
    "contentVersion": "1.0.0.0", 
    "parameters": {
        "iotDpsName": {
            "type": "string",
            "defaultValue": "[resourceGroup().name]",
            "minLength": 3,
            "metadata": {
                "description": "Specifies the name of the IoT DPS service."
            }
        }, 
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for Iot DPS resource."
            }
        }        
    }, 
    "variables": {
        "iotDpsApiVersion": "2020-01-01"
    }, 
    "resources": [
        {
            "type": "Microsoft.Devices/provisioningServices",
            "apiVersion": "[variables('iotDpsApiVersion')]",
            "name": "[parameters('iotDpsName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "IpFilterRules": [
                    {
                        "FilterName": "AllowAll",
                        "Action": "Accept",
                        "ipMask": "0.0.0.0/0"
                    }
                ]
            }            
        }
    ]
}
```

Werk de IP-filter regel kenmerken van de sjabloon bij op basis van uw vereisten.

| Kenmerk                | Beschrijving |
| ------------------------ | ----------- |
| **FilterName**           | Geef een naam op voor de IP-filter regel. Dit moet een unieke, hoofdletter gevoelige, alfanumerieke teken reeks van Maxi maal 128 tekens lang zijn. Alleen de ASCII 7-bits alfanumerieke tekens plus {'-', ': ', '/', '\', ', ' + ', '% ', ' _ ', ' # ', ' * ', '? ',!, ' (', ') ', ', ' = ', ' @ ', '; ', '} ', worden geaccepteerd. |
| **Actie**               | Geaccepteerde waarden **accepteren** of **weigeren** als actie voor de IP-filter regel. |
| **ipMask**               | Geef één IPv4-adres of een blok met IP-adressen in CIDR-notatie op. Bijvoorbeeld in CIDR-notatie 192.168.100.0/22 staat voor de IPv4-adressen van 1024 van 192.168.100.0 naar 192.168.103.255. |


### <a name="update-an-ip-filter-rule"></a>Een IP-filter regel bijwerken

In het volgende sjabloon voorbeeld wordt de IP-filter regel met de naam ' AllowAll ', zoals eerder weer gegeven, bijgewerkt om al het verkeer af te wijzen.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
                "IpFilterRules": [ 
                    { 
                        "FilterName": "AllowAll", 
                        "Action": "Reject", 
                        "ipMask": "0.0.0.0/0" 
                    } 
                ] 
            }             
        } 
    ] 
}
```

### <a name="delete-an-ip-filter-rule"></a>Een IP-filter regel verwijderen

In het volgende sjabloon voorbeeld worden alle IP-filter regels voor het DPS-exemplaar verwijderd.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
            }             
        } 
    ] 
}
```



## <a name="ip-filter-rule-evaluation"></a>Evaluatie van IP-filter

IP-filter regels worden in volg orde toegepast en de eerste regel die overeenkomt met het IP-adres, bepaalt de accepteren of afwijzen.

Als u bijvoorbeeld adressen in het bereik 192.168.100.0/22 wilt accepteren en alle andere gegevens wilt afwijzen, moet de eerste regel in het raster het adres bereik 192.168.100.0/22 accepteren. De volgende regel moet alle adressen weigeren met behulp van het adresbereik 0.0.0.0/0.

U kunt de volg orde van de IP-filter regels in het raster wijzigen door te klikken op de drie verticale puntjes aan het begin van een rij en met slepen en neerzetten.

Klik op **Opslaan**als u de nieuwe IP-filter regel wilt opslaan.

![Wijzig de volg orde van de IP-filter regels van uw DPS](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het beheren van DPS:

* [Informatie over de IP-adressen van IoT DPS](iot-dps-understand-ip-address.md)
* [DPS configureren met de Azure CLI](how-to-manage-dps-with-cli.md)
* [Toegang tot DPS beheren](how-to-control-access.md)
