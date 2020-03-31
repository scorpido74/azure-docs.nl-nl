---
title: Beveiligingseindpunten in de Dienst voor inrichting van IoT-apparaten | Microsoft Documenten
description: Concepten - hoe u de toegang tot De Voorzieningsservice (DPS) van IoT-apparaten (DPS) voor backend-apps beheren. Bevat informatie over beveiligingstokens.
author: wesmc7777
manager: philmea
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: wesmc
ms.openlocfilehash: 2a7e0932d226b1533c039b8529c2c11de06cf525
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79285147"
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Toegang tot Azure IoT Hub Device Provisioning Service beheren

In dit artikel worden de opties beschreven voor het beveiligen van uw IoT Device Provisioning-service. De inrichtingsservice gebruikt *machtigingen* om toegang te verlenen tot elk eindpunt. Machtigingen beperken de toegang tot een service-instantie op basis van functionaliteit.

In dit artikel wordt beschreven:

* De verschillende machtigingen die u verlenen aan een backend-app om toegang te krijgen tot uw inrichtingsservice.
* Het verificatieproces en de tokens die worden gebruikt om machtigingen te verifiëren.

### <a name="when-to-use"></a>Wanneer gebruikt u dit?

U moet over de juiste machtigingen beschikken om toegang te krijgen tot een van de eindpunten van de inrichtingsservice. Een backend-app moet bijvoorbeeld een token bevatten met beveiligingsreferenties, samen met elk bericht dat naar de service wordt verzonden.

## <a name="access-control-and-permissions"></a>Toegangsbeheer en machtigingen

U op de volgende manieren [machtigingen](#device-provisioning-service-permissions) verlenen:

* **Beleid voor beheerde toegangsautorisatie**. Beleid voor gedeelde toegang kan elke combinatie van [machtigingen](#device-provisioning-service-permissions)verlenen. U beleidsregels definiëren in de [Azure-portal][lnk-management-portal]of programmatisch gebruiken met de [API's voor de voorzieningen van de voorzieningsservice][lnk-resource-provider-apis]. Een nieuw gemaakte inrichtingsservice heeft het volgende standaardbeleid:

* **provisioningserviceowner**: Beleid met alle machtigingen.

> [!NOTE]
> Zie [machtigingen](#device-provisioning-service-permissions) voor gedetailleerde informatie.

## <a name="authentication"></a>Authentication

Azure IoT Hub Device Provisioning Service verleent toegang tot eindpunten door een token te verifiëren aan de hand van het beleid voor gedeelde toegang. Beveiligingsreferenties, zoals symmetrische sleutels, worden nooit over de draad verzonden.

> [!NOTE]
> De resourceprovider voor apparaatinrichtingsservice is beveiligd via uw Azure-abonnement, net als alle providers in Azure [Resource Manager.][lnk-azure-resource-manager]

Zie de volgende sectie voor meer informatie over het maken en gebruiken van beveiligingstokens.

HTTP is het enige ondersteunde protocol en implementeert verificatie door een geldig token op te nemen in de header **Autorisatieaanvraag.**

#### <a name="example"></a>Voorbeeld
```csharp
SharedAccessSignature sr = 
   mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`\
```

> [!NOTE]
> De [Azure IoT Device Provisioning Service SDKs][lnk-sdks] genereren automatisch tokens wanneer u verbinding maakt met de service.

## <a name="security-tokens"></a>Beveiligingstokens

De Apparaatvoorzieningsservice gebruikt beveiligingstokens om services te verifiëren om te voorkomen dat sleutels op de draad worden verzonden. Bovendien zijn beveiligingstokens beperkt in tijdgeldigheid en reikwijdte. [Azure IoT Device Provisioning Service SDKs][lnk-sdks] genereert automatisch tokens zonder speciale configuratie. Sommige scenario's vereisen wel dat u beveiligingstokens rechtstreeks genereert en gebruikt. Dergelijke scenario's omvatten het directe gebruik van het HTTP-oppervlak.

### <a name="security-token-structure"></a>Structuur voor beveiligingstoken

U gebruikt beveiligingstokens om tijdgebonden toegang te verlenen voor services tot specifieke functionaliteit in de IoT Device Provisioning Service. Om autorisatie te krijgen om verbinding te maken met de inrichtingsservice, moeten services beveiligingstokens verzenden die zijn ondertekend met een gedeelde toegang of symmetrische sleutel.

Een token dat is ondertekend met een gedeelde toegangssleutel geeft toegang tot alle functionaliteit die is gekoppeld aan de machtigingen voor het beleid voor gedeelde toegang. 

Het beveiligingstoken heeft de volgende indeling:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Hier zijn de verwachte waarden:

| Waarde | Beschrijving |
| --- | --- |
| {handtekening} |Een HMAC-SHA256 handtekening string `{URL-encoded-resourceURI} + "\n" + expiry`van het formulier: . **Belangrijk:** De sleutel wordt gedecodeerd vanaf base64 en wordt gebruikt als sleutel om de HMAC-SHA256-berekening uit te voeren.|
| {verlopen} |UTF8-tekenreeksen voor het aantal seconden sinds het tijdperk 00:00:00 GMT op 1 januari 1970. |
| {URL-encoded-resourceURI} | URL-codering met kleine letters van de kleine-casebron URI. URI-voorvoegsel (per segment) van de eindpunten die met dit token toegankelijk zijn, te beginnen met de hostnaam van de IoT Device Provisioning Service (geen protocol). Bijvoorbeeld `mydps.azure-devices-provisioning.net`. |
| {policyName} |De naam van het beleid voor gedeelde toegang waarnaar dit token verwijst. |

**Opmerking op voorvoegsel**: Het URI-voorvoegsel wordt berekend per segment en niet per teken. Bijvoorbeeld `/a/b` is een voorvoegsel voor, `/a/b/c` maar niet voor `/a/bc`.

Het volgende node.js-fragment toont een functie genaamd **generateSasToken** die het token berekent op basis van de ingangen. `resourceUri, signingKey, policyName, expiresInMins` In de volgende secties wordt beschreven hoe u de verschillende ingangen voor de verschillende tokenusecases initialiseren.

```javascript
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires + "&skn="+ policyName;
    return token;
};
```

Ter vergelijking:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import quote_plus, urlencode
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
    print sign_key
    signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl)),
        'skn' : policy_name
    }

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

> [!NOTE]
> Aangezien de tijdsgeldigheid van het token is gevalideerd op IoT Device Provisioning Service-machines, moet de drift op de klok van de machine die het token genereert minimaal zijn.

### <a name="use-security-tokens-from-service-components"></a>Beveiligingstokens van servicecomponenten gebruiken

Servicecomponenten kunnen alleen beveiligingstokens genereren met behulp van beleid voor gedeelde toegang waarbij de juiste machtigingen worden verleend, zoals eerder is uitgelegd.

Hier zijn de servicefuncties die op de eindpunten worden weergegeven:

| Eindpunt | Functionaliteit |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |Biedt apparaatinschrijvingsbewerkingen met de Service Apparaatinrichting. |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |Biedt bewerkingen voor het beheren van apparaatinschrijvingsgroepen. |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |Biedt bewerkingen voor het ophalen en beheren van de status van apparaatregistraties. |


Een service die wordt gegenereerd met een vooraf gemaakt beleid voor gedeelde toegang met de naam **inschrijving,** maakt bijvoorbeeld een token met de volgende parameters:

* resource URI: `{mydps}.azure-devices-provisioning.net`,
* ondertekeningssleutel: een van `enrollmentread` de sleutels van het beleid,
* beleidsnaam: `enrollmentread`,
* elke vervaldatum.backn

![Een beleid voor gedeelde toegang maken voor uw apparaatinrichtingsserviceinstantie in de portal][img-add-shared-access-policy]

```javascript
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Het resultaat, dat toegang zou verlenen om alle inschrijvingsrecords te lezen, zou zijn:

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>Referentieonderwerpen:

De volgende referentieonderwerpen geven u meer informatie over het beheren van toegang tot uw IoT-apparaatinrichtingsservice.

### <a name="device-provisioning-service-permissions"></a>Machtigingen voor apparaatinrichtingsservice

In de volgende tabel worden de machtigingen weergegeven die u gebruiken om de toegang tot uw IoT-apparaatinrichtingsservice te beheren.

| Machtiging | Opmerkingen |
| --- | --- |
| **ServiceConfig (ServiceConfig)** |Verleent toegang om de serviceconfiguraties te wijzigen. <br/>Deze toestemming wordt gebruikt door backend cloudservices. |
| **InschrijvingLezen** |Verleent leestoegang tot de apparaatinschrijvingen en inschrijvingsgroepen. <br/>Deze toestemming wordt gebruikt door backend cloudservices. |
| **InschrijvingSchrijven** |Verleent schrijftoegang tot de apparaatinschrijvingen en inschrijvingsgroepen. <br/>Deze toestemming wordt gebruikt door backend cloudservices. |
| **RegistratieStatusRead** |Verleent leestoegang tot de status van de registratie van het apparaat. <br/>Deze toestemming wordt gebruikt door backend cloudservices. |
| **RegistratieStatusWrite**  |Hiermee wordt de toegang tot de registratiestatus van het apparaat verwijderd. <br/>Deze toestemming wordt gebruikt door backend cloudservices. |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/management/overview.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iot-dps/
