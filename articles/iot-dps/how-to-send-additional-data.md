---
title: Aanvullende gegevens overdragen tussen apparaten en Azure Device Provisioning Service
description: In dit document wordt beschreven hoe u aanvullende gegevens overbrengt tussen Device Provisioning Service (DPS)
author: menchi
ms.author: menchi
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: e9482f7069616d61efb98f66590ce33cfe3cf350
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974850"
---
# <a name="how-to-transfer-additional-data-between-device-and-dps"></a>Aanvullende gegevens overdragen tussen apparaat en DPS
DPS heeft soms meer gegevens nodig om apparaten correct in te richten in de juiste IoT Hub en die gegevens moeten door het apparaat worden geleverd. Omgekeerd kan DPS gegevens naar het apparaat retour neren om logica aan de client zijde te vergemakkelijken. 

## <a name="when-to-use-it"></a>Wanneer te gebruiken
Deze functie kan worden gebruikt als een uitbrei ding voor [aangepaste toewijzing](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies). U wilt bijvoorbeeld uw apparaten toewijzen op basis van het model apparaat zonder menselijke tussen komst. In dit geval gebruikt u [aangepaste toewijzing](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies). U kunt het apparaat zo configureren dat de model gegevens worden gerapporteerd als onderdeel van de registratie van het [apparaat](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice). De gegevens van het apparaat worden door DPS doorgestuurd naar de aangepaste toewijzings-webhook. En uw functie kan bepalen welke IoT Hub dit apparaat gaat gebruiken wanneer het apparaat-model informatie ontvangt. Op dezelfde manier kan de webhook gegevens retourneren naar het apparaat door de gegevens als een tekenreeks in een webhookantwoord op te nemen.  

## <a name="device-sends-data-to-dps"></a>Apparaat verzendt gegevens naar DPS
Wanneer uw apparaat een [aanroep van een registratie-apparaat](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice) naar DPS verzendt, kan de registratie oproep worden uitgebreid om andere velden in de hoofd tekst te maken. De hoofd tekst ziet er als volgt uit: 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “interfaces”: “TODO: get how interfaces are reported by devices from PnP folks.”, 
       “data”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>DPS retourneert gegevens naar het apparaat
Als de webhook voor het aangepaste toewijzings beleid sommige gegevens naar het apparaat wil retour neren, worden de gegevens weer gegeven als een teken reeks in het antwoord van de webhook. De wijziging bevindt zich in de returnData-sectie hieronder. 
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
        "returnData": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>SDK-ondersteuning
Deze functie is beschikbaar in de client C#- [sdk's](https://docs.microsoft.com/azure/iot-dps/)C,, Java en node. js.  

## <a name="next-steps"></a>Volgende stappen
* Ontwikkelen met behulp van de [Azure IOT SDK]( https://github.com/Azure/azure-iot-sdks) voor Azure IOT hub en Azure IOT hub Device Provisioning Service
