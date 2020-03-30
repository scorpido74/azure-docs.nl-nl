---
title: Azure IoT DPS IP-verbindingsfilters | Microsoft Documenten
description: IP-filtering gebruiken om verbindingen van specifieke IP-adressen naar uw Azure IoT DPS-exemplaar te blokkeren. U verbindingen blokkeren vanaf afzonderlijke of bereiken van IP-adressen.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 0b13040b39ed491ec4fee4d6922d41f086edeeb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284913"
---
# <a name="use-ip-filters"></a>IP-filters gebruiken

Beveiliging is een belangrijk aspect van elke IoT-oplossing. Soms moet u expliciet de IP-adressen opgeven van waaruit apparaten verbinding kunnen maken als onderdeel van uw beveiligingsconfiguratie. Met de *IP-filterfunctie* voor een Azure IoT Hub Device Provisioning Service (DPS) u regels configureren voor het weigeren of accepteren van verkeer van specifieke IPv4-adressen.

## <a name="when-to-use"></a>Wanneer gebruikt u dit?

Er zijn twee specifieke use-cases waarin het nuttig is om verbindingen met een DPS-eindpunt te blokkeren vanaf bepaalde IP-adressen:

* Uw DPS mag alleen verkeer ontvangen van een bepaald bereik van IP-adressen en al het andere afwijzen. U gebruikt bijvoorbeeld uw DPS met [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) om privéverbindingen te maken tussen een DPS en uw apparaten.

* U moet verkeer van IP-adressen weigeren die door de DPS-beheerder als verdacht zijn aangemerkt.

## <a name="how-filter-rules-are-applied"></a>Hoe filterregels worden toegepast

De IP-filterregels worden toegepast op DPS-instantieniveau. Daarom zijn de IP-filterregels van toepassing op alle verbindingen van apparaten en back-end apps met behulp van een ondersteund protocol.

Elke verbindingspoging van een IP-adres dat overeenkomt met een weigerende IP-regel in uw DPS-exemplaar, ontvangt een ongeautoriseerde status code en beschrijving van 401. In het antwoordbericht wordt de IP-regel niet vermeld.

## <a name="default-setting"></a>Standaardinstelling

Standaard is het **IP-filterraster** in de portal voor DPS leeg. Deze standaardinstelling betekent dat uw DPS verbindingen accepteert vanaf elk IP-adres. Deze standaardinstelling is gelijk aan een regel die het IP-adresbereik 0.0.0/0/0 accepteert.

![Standaard IP-filterinstellingen voor IoT DPS](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Een IP-filterregel toevoegen of bewerken

Als u een IP-filterregel wilt toevoegen, selecteert u **+ IP-filterregel toevoegen**.

![Een IP-filterregel toevoegen aan een IoT-DPS](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

Nadat u **IP-filterregel toevoegen hebt**geselecteerd, vult u de velden in.

![Na het selecteren van een IP-filterregel toevoegen](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* Geef een **naam** op voor de IP-filterregel. Dit moet een unieke, case-ongevoelige, alfanumerieke string tot 128 tekens lang zijn. Alleen de ASCII 7-bits `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` alfanumerieke tekens plus worden geaccepteerd.

* Geef één IPv4-adres of een blok IP-adressen op in CIDR-notatie. In CIDR-notatie 192.168.100.0/22 staat bijvoorbeeld de 1024 IPv4-adressen van 192.168.100.0 tot 192.168.103.255.

* Selecteer **Toestaan** of **Blokkeren** als **actie** voor de IP-filterregel.

Nadat u de velden hebt ingevuld, selecteert u **Opslaan** om de regel op te slaan. U ziet een waarschuwing waarin wordt gemeld dat de update aan de gang is.

![Melding over het opslaan van een IP-filterregel](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

De optie **Toevoegen** is uitgeschakeld wanneer u het maximum van 10 IP-filterregels bereikt.

Als u een bestaande regel wilt bewerken, selecteert u de gegevens die u wilt wijzigen, wijzigt u de wijziging en selecteert **u Opslaan** om uw bewerking op te slaan.

> [!NOTE]
> Als u IP-adressen afwijst, kan voorkomen dat andere Azure Services interactie hebben met het DPS-exemplaar.

## <a name="delete-an-ip-filter-rule"></a>Een IP-filterregel verwijderen

Als u een IP-filterregel wilt verwijderen, selecteert u het pictogram prullenbak in die rij en selecteert u **Opslaan**. De regel wordt verwijderd en de wijziging wordt opgeslagen.

![Een IP-filterregel van IoT-DPS verwijderen](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>IP-filterregels bijwerken in code

U uw DPS IP-filter ophalen en wijzigen met behulp van het REST-eindpunt van azure resource Provider. Zie `properties.ipFilterRules` in [createorupdate methode](https://docs.microsoft.com/rest/api/iot-dps/iotdpsresource/createorupdate).

Het bijwerken van DPS IP-filterregels wordt momenteel niet ondersteund met Azure CLI of Azure PowerShell, maar kan worden uitgevoerd met Azure Resource Manager-sjablonen. Zie [Azure Resource Manager-sjablonen](../azure-resource-manager/templates/overview.md) voor richtlijnen voor het gebruik van Resource Manager-sjablonen. In de volgende sjabloonvoorbeelden is te zien hoe u DPS IP-filterregels maakt, bewerkt en verwijdert.

### <a name="add-an-ip-filter-rule"></a>Een IP-filterregel toevoegen

In het volgende sjabloonvoorbeeld wordt een nieuwe IP-filterregel gemaakt met de naam 'AllowAll' die al het verkeer accepteert.

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

Werk de IP-filterregelkenmerken van de sjabloon bij op basis van uw vereisten.

| Kenmerk                | Beschrijving |
| ------------------------ | ----------- |
| **Filternaam**           | Geef een naam op voor de IP-filterregel. Dit moet een unieke, case-ongevoelige, alfanumerieke string tot 128 tekens lang zijn. Alleen de ASCII 7-bits alfanumerieke tekens plus {'-', ':', '/',\'',, '', '+', '%', '_', '#', '*', '?', '?', '',,,,,,,,', '', '',worden geaccepteerd. |
| **Actie**               | Geaccepteerde waarden zijn **Accepteren** of **Weigeren** als de actie voor de IP-filterregel. |
| **ipMask (ipMask)**               | Geef één IPv4-adres of een blok IP-adressen op in CIDR-notatie. In CIDR-notatie 192.168.100.0/22 staat bijvoorbeeld de 1024 IPv4-adressen van 192.168.100.0 tot 192.168.103.255. |


### <a name="update-an-ip-filter-rule"></a>Een IP-filterregel bijwerken

In het volgende sjabloonvoorbeeld wordt de IP-filterregel met de naam 'AllowAll' bijgewerkt om al het verkeer te weigeren.

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

### <a name="delete-an-ip-filter-rule"></a>Een IP-filterregel verwijderen

In het volgende sjabloonvoorbeeld worden alle IP-filterregels voor het DPS-exemplaar verwijderd.

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



## <a name="ip-filter-rule-evaluation"></a>Evaluatie van IP-filterregels

IP-filterregels worden op volgorde toegepast en de eerste regel die overeenkomt met het IP-adres bepaalt de actie Accepteren of weigeren.

Als u bijvoorbeeld adressen in het bereik 192.168.100.0/22 wilt accepteren en al het andere wilt afwijzen, moet de eerste regel in het raster het adresbereik 192.168.100.0/22 accepteren. De volgende regel moet alle adressen afwijzen met het bereik 0.0.0.0/0.

U de volgorde van uw IP-filterregels in het raster wijzigen door op de drie verticale stippen aan het begin van een rij te klikken en slepen en neerzetten te gebruiken.

Als u de nieuwe IP-filterregelorder wilt opslaan, klikt u op **Opslaan**.

![De volgorde van uw IP-filterregels van DPS wijzigen](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Volgende stappen

Zie het volgende om het beheer van DPS verder te verkennen:

* [IoT DPS-IP-adressen begrijpen](iot-dps-understand-ip-address.md)
* [DPS configureren met de Azure CLI](how-to-manage-dps-with-cli.md)
* [Toegang tot DPS beheren](how-to-control-access.md)
