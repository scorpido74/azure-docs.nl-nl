---
title: Webhook-activiteit in Azure Data Factory
description: De webhook-activiteit gaat niet door met de uitvoering van de pijplijn totdat de gekoppelde gegevensset wordt gevalideerd met bepaalde criteria die de gebruiker opgeeft.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 4056550ae0a71138d136878fc7e3aa5f6f8f4180
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417875"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Webhook-activiteit in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Een webhook-activiteit kan de uitvoering van pijplijnen regelen via uw aangepaste code. Met de webhook-activiteit kan de code van klanten een eindpunt aanroepen en een terugbel-URL doorgeven. De pijplijnrun wacht op de aanroep van de callback voordat deze overgaat tot de volgende activiteit.

## <a name="syntax"></a>Syntaxis

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```

## <a name="type-properties"></a>Eigenschappen typen

Eigenschap | Beschrijving | Toegestane waarden | Vereist
-------- | ----------- | -------------- | --------
**Naam** | De naam van de webhook activiteit. | Tekenreeks | Ja |
**Type** | Moet worden ingesteld op "WebHook". | Tekenreeks | Ja |
**Methode** | De REST API-methode voor het doeleindpunt. | Tekenreeks. Het ondersteunde type is "POST". | Ja |
**Url** | Het doeleindpunt en -pad. | Een tekenreeks of een expressie met de **resultaattekstTypewaarde** van een tekenreeks. | Ja |
**Headers** | Kopteksten die naar de aanvraag worden verzonden. Hier is een voorbeeld dat de taal en `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`het type op een verzoek instelt: . | Een tekenreeks of een expressie met de **resultaattekstTypewaarde** van een tekenreeks. | Ja. Een `Content-Type` header `"headers":{ "Content-Type":"application/json"}` zoals is vereist. |
**Lichaam** | Vertegenwoordigt de payload die naar het eindpunt wordt verzonden. | Geldige JSON of een expressie met de **resultaatTypewaarde** van JSON. Zie [Payload-schema aanvragen](https://docs.microsoft.com/azure/data-factory/control-flow-web-activity#request-payload-schema) voor het schema van de payload van de aanvraag. | Ja |
**Verificatie** | De verificatiemethode die wordt gebruikt om het eindpunt aan te roepen. Ondersteunde typen zijn "Basic" en "ClientCertificate". Zie [Verificatie](https://docs.microsoft.com/azure/data-factory/control-flow-web-activity#authentication) voor meer informatie. Als verificatie niet vereist is, sluit u deze eigenschap uit. | Een tekenreeks of een expressie met de **resultaattekstTypewaarde** van een tekenreeks. | Nee |
**timeout** | Hoe lang de activiteit wacht tot de callback is opgegeven door **callBackUri.** De standaardwaarde is 10 minuten ("00:10:00"). Waarden hebben de TimeSpan-indeling *d*. *hh*:*mm*:*ss*. | Tekenreeks | Nee |
**Status rapporteren bij terugbellen** | Hiermee kan een gebruiker de mislukte status van een webhook-activiteit melden. | Booleaans | Nee |

## <a name="authentication"></a>Verificatie

Een webhookactiviteit ondersteunt de volgende verificatietypen.

### <a name="none"></a>Geen

Als verificatie niet vereist is, moet u de **eigenschap verificatie** niet opnemen.

### <a name="basic"></a>Basic

Geef de gebruikersnaam en het wachtwoord op die u wilt gebruiken met basisverificatie.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Clientcertificaat

Geef de door Base64 gecodeerde inhoud van een PFX-bestand en een wachtwoord op.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Beheerde identiteit

Gebruik de beheerde identiteit van de gegevensfabriek om de resource URI op te geven waarvoor het toegangstoken wordt aangevraagd. Als u de API voor `https://management.azure.com/`Azure Resource Management wilt aanroepen, gebruikt u . Zie het [overzicht beheerde identiteiten voor Azure-bronnen voor](/azure/active-directory/managed-identities-azure-resources/overview)meer informatie over hoe beheerde identiteiten werken.

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Als uw gegevensfabriek is geconfigureerd met een Git-opslagplaats, moet u uw referenties opslaan in Azure Key Vault om basisverificatie of clientcertificaatverificatie te gebruiken. Azure Data Factory slaat geen wachtwoorden op in Git.

## <a name="additional-notes"></a>Aanvullende opmerkingen

Data Factory geeft de extra eigenschap **callBackUri** door in de body die naar het URL-eindpunt wordt verzonden. Data Factory verwacht dat deze URI wordt ingeroepen vóór de opgegeven time-outwaarde. Als de URI niet wordt aangeroepen, mislukt de activiteit met de status 'TimedOut'.

De webhook-activiteit mislukt wanneer de aanroep naar het aangepaste eindpunt mislukt. Elk foutbericht kan worden toegevoegd aan de terugbelinstantie en wordt in een latere activiteit gebruikt.

Voor elke REST API-aanroep geeft de client een keer een uitkijk als het eindpunt niet binnen een minuut reageert. Dit gedrag is standaard HTTP best practice. Voer een 202-patroon in om dit probleem op te lossen. In het huidige geval retourneert het eindpunt 202 (Geaccepteerd) en de clientpolls.

De time-out van één minuut op het verzoek heeft niets te maken met de time-out van de activiteit. De laatste wordt gebruikt om te wachten op de callback opgegeven door **callbackUri**.

Het lichaam dat wordt doorgegeven aan de callback URI moet geldig zijn JSON. Stel `Content-Type` de `application/json`koptekst in op .

Wanneer u de **eigenschap Rapportstatus op de eigenschap Callback** gebruikt, moet u de volgende code aan de instantie toevoegen wanneer u de terugroepmaakt:

```json
{
    "Output": {
        // output object is used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity is marked as failed
}
```

## <a name="next-steps"></a>Volgende stappen

Bekijk de volgende control flow-activiteiten die worden ondersteund door Data Factory:

- [If Condition Activity](control-flow-if-condition-activity.md)
- [Activiteit uitvoeren van pijplijn](control-flow-execute-pipeline-activity.md)
- [Voor elke activiteit](control-flow-for-each-activity.md)
- [Activiteit ophalen van metagegevens](control-flow-get-metadata-activity.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)
- [Web Activity](control-flow-web-activity.md)
- [Until-activiteit](control-flow-until-activity.md)
