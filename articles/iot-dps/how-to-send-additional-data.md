---
title: Een payload overbrengen tussen apparaat en Azure Device Provisioning Service
description: In dit document wordt beschreven hoe u een payload overbrengen tussen apparaat en DPS (Device Provisioning Service)
author: menchi
ms.author: menchi
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: de6bb3fe0879a69467283e93a04a355876a02cba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246684"
---
# <a name="how-to-transfer-a-payload-between-device-and-dps"></a>Hoe een payload over te dragen tussen apparaat en DPS
Soms heeft DPS meer gegevens van apparaten nodig om ze goed in te richten op de juiste IoT-hub en moet die gegevens door het apparaat worden verstrekt. Omgekeerd kan DPS gegevens naar het apparaat retourneren om de logica aan de clientzijde te vergemakkelijken. 

## <a name="when-to-use-it"></a>Wanneer te gebruiken
Deze functie kan worden gebruikt als een verbetering voor [aangepaste toewijzing.](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies) U wilt bijvoorbeeld uw apparaten toewijzen op basis van het apparaatmodel zonder menselijke tussenkomst. In dit geval gebruikt u [aangepaste toewijzing.](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies) U het apparaat configureren om de modelgegevens te rapporteren als onderdeel van de [oproep voor het registerapparaat.](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice) DPS zal de payload van het apparaat doorgeven aan de aangepaste toewijzing webhook. En uw functie kan beslissen naar welke IoT-hub dit apparaat gaat wanneer het apparaatmodelinformatie ontvangt. Evenzo, als de webhook wil wat gegevens terug te keren naar het apparaat, zal het doorgeven van de gegevens terug als een string in de webhook reactie.  

## <a name="device-sends-data-payload-to-dps"></a>Apparaat stuurt gegevens payload naar DPS
Wanneer uw apparaat een [oproep voor een registerapparaat](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice) naar DPS verzendt, kan de registeroproep worden verbeterd om andere velden in het lichaam te nemen. Het lichaam ziet er als volgt uit: 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “payload”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>DPS retourneert gegevens naar het apparaat
Als het aangepaste toewijzingsbeleid webhook bepaalde gegevens naar het apparaat wil retourneren, zal het de gegevens teruggeven als een tekenreeks in de webhook-respons. De verandering is in de payload sectie hieronder. 
   ```
   { 
       "iotHubHostName": "sample-iot-hub-1.azure-devices.net", 
       "initialTwin": { 
           "tags": { 
               "tag1": true 
               }, 
               "properties": { 
                   "desired": { 
                       "tag2": true 
                    } 
                } 
            }, 
        "payload": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>SDK-ondersteuning
Deze functie is beschikbaar in C, C#, JAVA en Node.js [client SDKs](https://docs.microsoft.com/azure/iot-dps/).  

## <a name="next-steps"></a>Volgende stappen
* Ontwikkelen met behulp van de [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) voor Azure IoT Hub en Azure IoT Hub Device Provisioning Service
