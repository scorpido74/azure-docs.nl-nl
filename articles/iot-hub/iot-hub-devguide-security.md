---
title: Informatie over Azure IoT Hub-beveiliging | Microsoft Documenten
description: Handleiding voor ontwikkelaars - hoe u de toegang tot IoT Hub voor apparaat-apps en back-end-apps beheren. Bevat informatie over beveiligingstokens en ondersteuning voor X.509-certificaten.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: b7f9ac7e6e7049a3b744151bc9cb05115fbac935
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729219"
---
# <a name="control-access-to-iot-hub"></a>Toegang tot IoT Hub regelen

In dit artikel worden de opties beschreven voor het beveiligen van uw IoT-hub. IoT Hub gebruikt *machtigingen* om toegang te verlenen tot elk IoT-hubeindpunt. Machtigingen beperken de toegang tot een IoT-hub op basis van functionaliteit.

Dit artikel introduceert:

* De verschillende machtigingen die u verlenen aan een apparaat of back-end-app om toegang te krijgen tot uw IoT-hub.
* Het verificatieproces en de tokens die worden gebruikt om machtigingen te verifiëren.
* Referenties scopen om de toegang tot specifieke bronnen te beperken.
* IoT Hub-ondersteuning voor X.509-certificaten.
* Aangepaste apparaatverificatiemechanismen die gebruikmaken van bestaande apparaatidentiteitsregisters of verificatieschema's.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

U moet over de juiste machtigingen beschikken om toegang te krijgen tot een van de IoT Hub-eindpunten. Een apparaat moet bijvoorbeeld een token bevatten met beveiligingsreferenties, samen met elk bericht dat naar IoT Hub wordt verzonden.

## <a name="access-control-and-permissions"></a>Toegangsbeheer en machtigingen

U op de volgende manieren [machtigingen](#iot-hub-permissions) verlenen:

* **Beleid voor gedeelde toegang op IoT-hubniveau**. Beleid voor gedeelde toegang kan elke combinatie van [machtigingen](#iot-hub-permissions)verlenen. U beleidsregels definiëren in de [Azure-portal](https://portal.azure.com), programmatisch met behulp van de [IoT Hub Resource REST API's](/rest/api/iothub/iothubresource)of met behulp van het [AZ Iot-hubbeleid](/cli/azure/iot/hub/policy?view=azure-cli-latest) CLI. Een nieuw gemaakte IoT-hub heeft het volgende standaardbeleid:
  
  | Beleid voor gedeelde toegang | Machtigingen |
  | -------------------- | ----------- |
  | iothubowner | Alle toestemming |
  | service | **ServiceConnect-machtigingen** |
  | apparaat | **DeviceConnect-machtigingen** |
  | registerLezen | **RegisterLees-machtigingen** |
  | registerReadWrite | **RegisterLees-** en **RegisterWrite-machtigingen** |

* **Beveiligingsreferenties per apparaat**. Elke IoT-hub bevat een [identiteitsregister](iot-hub-devguide-identity-registry.md) Voor elk apparaat in dit identiteitsregister u beveiligingsreferenties configureren waarmee **DeviceConnect-machtigingen** worden toegekend die zijn ingesteld op de bijbehorende apparaateindpunten.

Bijvoorbeeld in een typische IoT-oplossing:

* De component apparaatbeheer maakt gebruik van het *registerReadWrite-beleid.*
* De gebeurtenisprocessorcomponent maakt gebruik van het *servicebeleid.*
* De component bedrijfslogica voor runtime-apparaten maakt gebruik van het *servicebeleid.*
* Afzonderlijke apparaten maken verbinding met referenties die zijn opgeslagen in het identiteitsregister van de IoT-hub.

> [!NOTE]
> Zie [machtigingen](#iot-hub-permissions) voor gedetailleerde informatie.

## <a name="authentication"></a>Verificatie

In Azure IoT Hub wordt toegang verleend aan eindpunten door een token te verifiëren op basis van de gedeelde toegangsbeleidsregels en beveiligingsreferenties van het identiteitsregister.

Beveiligingsreferenties, zoals symmetrische sleutels, worden nooit over de draad verzonden.

> [!NOTE]
> De Azure IoT Hub-bronprovider is beveiligd via uw Azure-abonnement, net als alle providers in Azure [Resource Manager.](../azure-resource-manager/management/overview.md)

Zie [IoT Hub-beveiligingstokens](iot-hub-devguide-security.md#security-tokens)voor meer informatie over het maken en gebruiken van beveiligingstokens.

### <a name="protocol-specifics"></a>Protocoldetails

Elk ondersteund protocol, zoals MQTT, AMQP en HTTPS, transporteert tokens op verschillende manieren.

Bij gebruik van MQTT heeft het CONNECT-pakket `{iothubhostname}/{deviceId}` de deviceId als clientid, in het veld Gebruikersnaam en een SAS-token in het veld Wachtwoord. `{iothubhostname}`de volledige CName van de IoT-hub (bijvoorbeeld contoso.azure-devices.net).

Bij gebruik van [AMQP](https://www.amqp.org/)ondersteunt IoT Hub [SASL PLAIN](https://tools.ietf.org/html/rfc4616) en [AMQP Claims-Based-Security](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc).

Als u AMQP-claimgebaseerde beveiliging gebruikt, geeft de standaard aan hoe u deze tokens verzendt.

Voor SASL PLAIN kan de **gebruikersnaam** zijn:

* `{policyName}@sas.root.{iothubName}`als u IoT-tokens op hubniveau gebruikt.
* `{deviceId}@sas.{iothubname}`als u tokens met apparaatscoped gebruikt.

In beide gevallen bevat het wachtwoordveld het token, zoals beschreven in [IoT Hub-beveiligingstokens.](iot-hub-devguide-security.md#security-tokens)

HTTPS implementeert verificatie door een geldig token op te nemen in de **koptekst Autorisatieaanvraag.**

#### <a name="example"></a>Voorbeeld

Gebruikersnaam (DeviceId is hoofdlettergevoelig):`iothubname.azure-devices.net/DeviceId`

Wachtwoord (U een SAS-token genereren met de opdracht [AZ Iot-hub generate-sas-token](/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-generate-sas-token)of de [Azure IoT-hulpprogramma's voor Visuele Studiocode:](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> De [Azure IoT SDKs](iot-hub-devguide-sdks.md) genereren automatisch tokens wanneer ze verbinding maken met de service. In sommige gevallen ondersteunen de Azure IoT SDK's niet alle protocollen of alle verificatiemethoden.

### <a name="special-considerations-for-sasl-plain"></a>Speciale overwegingen voor SASL PLAIN

Wanneer u SASL PLAIN met AMQP gebruikt, kan een client die verbinding maakt met een IoT-hub één token gebruiken voor elke TCP-verbinding. Wanneer het token verloopt, wordt de verbinding met de TCP-verbinding verbroken van de service en wordt een nieuwe verbinding geactiveerd. Dit gedrag is weliswaar niet problematisch voor een back-end-app, maar is schadelijk voor een apparaat-app om de volgende redenen:

* Gateways maken meestal verbinding namens veel apparaten. Bij het gebruik van SASL PLAIN moeten ze een duidelijke TCP-verbinding maken voor elk apparaat dat verbinding maakt met een IoT-hub. Dit scenario verhoogt het verbruik van energie- en netwerkbronnen aanzienlijk en verhoogt de latentie van elke apparaatverbinding.

* Apparaten met beperkte resources worden negatief beïnvloed door het toegenomen gebruik van resources om na het verlopen van een token opnieuw verbinding te maken.

## <a name="scope-iot-hub-level-credentials"></a>Referenties op IoT-hubniveau van scope

U het beveiligingsbeleid op IoT-hubniveau beheren door tokens te maken met een beperkte bron-URI. Het eindpunt voor het verzenden van device-to-cloud-berichten vanaf een apparaat is bijvoorbeeld **/devices/{deviceId}/berichten/gebeurtenissen.** U ook een gedeeld toegangsbeleid op IoT-hubniveau gebruiken met **DeviceConnect-machtigingen** om een token te ondertekenen waarvan de resourceURI **/devices/{deviceId}** is. Met deze benadering wordt een token gemaakt dat alleen bruikbaar is om berichten te verzenden namens **apparaatid.**

Dit mechanisme is vergelijkbaar met het [uitgeversbeleid van Event Hubs](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab)en stelt u in staat aangepaste verificatiemethoden te implementeren.

## <a name="security-tokens"></a>Beveiligingstokens

IoT Hub gebruikt beveiligingstokens om apparaten en services te verifiëren om te voorkomen dat sleutels op de draad worden verzonden. Bovendien zijn beveiligingstokens beperkt in tijdgeldigheid en reikwijdte. [Azure IoT SDKs](iot-hub-devguide-sdks.md) genereren automatisch tokens zonder speciale configuratie. Sommige scenario's vereisen wel dat u beveiligingstokens rechtstreeks genereert en gebruikt. Dergelijke scenario's omvatten:

* Het directe gebruik van de MQTT- of AMQP- of HTTPS-oppervlakken.

* De implementatie van het tokenservicepatroon, zoals uitgelegd in [aangepaste apparaatverificatie.](iot-hub-devguide-security.md#custom-device-and-module-authentication)

Met IoT Hub kunnen apparaten zich ook verifiëren met IoT Hub met [X.509-certificaten.](iot-hub-devguide-security.md#supported-x509-certificates)

### <a name="security-token-structure"></a>Structuur voor beveiligingstoken

U gebruikt beveiligingstokens om tijdgebonden toegang tot apparaten en services te verlenen aan specifieke functionaliteit in IoT Hub. Om autorisatie te krijgen om verbinding te maken met IoT Hub, moeten apparaten en services beveiligingstokens verzenden die zijn ondertekend met een gedeelde toegang of symmetrische sleutel. Deze sleutels worden opgeslagen met een apparaatidentiteit in het identiteitsregister.

Een token dat is ondertekend met een gedeelde toegangssleutel geeft toegang tot alle functionaliteit die is gekoppeld aan de machtigingen voor het beleid voor gedeelde toegang. Een token dat is ondertekend met de symmetrische sleutel van een apparaatidentiteit, verleent alleen de **DeviceConnect-toestemming** voor de bijbehorende apparaatidentiteit.

Het beveiligingstoken heeft de volgende indeling:

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Hier zijn de verwachte waarden:

| Waarde | Beschrijving |
| --- | --- |
| {handtekening} |Een HMAC-SHA256 handtekening string `{URL-encoded-resourceURI} + "\n" + expiry`van het formulier: . **Belangrijk:** De sleutel wordt gedecodeerd vanaf base64 en wordt gebruikt als sleutel om de HMAC-SHA256-berekening uit te voeren. |
| {resourceURI} |URI-voorvoegsel (per segment) van de eindpunten die toegankelijk zijn met dit token, te beginnen met de hostnaam van de IoT-hub (geen protocol). Bijvoorbeeld: `myHub.azure-devices.net/devices/device1` |
| {verlopen} |UTF8-tekenreeksen voor het aantal seconden sinds het tijdperk 00:00:00 GMT op 1 januari 1970. |
| {URL-encoded-resourceURI} |URL-codering met kleine letters van de kleine resource URI |
| {policyName} |De naam van het beleid voor gedeelde toegang waarnaar dit token verwijst. Afwezig als het token verwijst naar apparaatregisterreferenties. |

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
    var hmac = crypto.createHmac('sha256', Buffer.from(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
};
```

Ter vergelijking:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import parse
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((parse.quote_plus(uri)), int(ttl))
    print sign_key
    signature = b64encode(HMAC(b64decode(key), sign_key.encode('utf-8'), sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl))
    }

    if policy_name is not None:
        rawtoken['skn'] = policy_name

    return 'SharedAccessSignature ' + parse.urlencode(rawtoken)
```

Hieronder volgen de installatie-instructies voor de voorwaarden.

[!INCLUDE [Iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]


De functionaliteit in C# om een beveiligingstoken te genereren is:

```csharp
using System;
using System.Globalization;
using System.Net;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;

public static string generateSasToken(string resourceUri, string key, string policyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = WebUtility.UrlEncode(resourceUri) + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}", WebUtility.UrlEncode(resourceUri), WebUtility.UrlEncode(signature), expiry);

    if (!String.IsNullOrEmpty(policyName))
    {
        token += "&skn=" + policyName;
    }

    return token;
}

```


> [!NOTE]
> Aangezien de tijdsgeldigheid van het token is gevalideerd op IoT Hub-machines, moet de drift op de klok van de machine die het token genereert minimaal zijn.

### <a name="use-sas-tokens-in-a-device-app"></a>SAS-tokens gebruiken in een apparaat-app

Er zijn twee manieren om **DeviceConnect-machtigingen** te verkrijgen met IoT Hub met beveiligingstokens: gebruik een [symmetrische apparaatsleutel uit het identiteitsregister](#use-a-symmetric-key-in-the-identity-registry)of gebruik een [gedeelde toegangssleutel](#use-a-shared-access-policy).

Vergeet niet dat alle functionaliteit die toegankelijk is vanaf `/devices/{deviceId}`apparaten wordt blootgesteld door het ontwerp op eindpunten met voorvoegsel .

> [!IMPORTANT]
> De enige manier waarop IoT Hub een specifiek apparaat verifieert, is het gebruik van de identiteitssleutel van het apparaat. In gevallen waarin een beleid voor gedeelde toegang wordt gebruikt om toegang te krijgen tot apparaatfunctionaliteit, moet de oplossing het onderdeel dat het beveiligingstoken uitgeeft als een vertrouwde subcomponent beschouwen.

De apparaatgerichte eindpunten zijn (ongeacht het protocol):

| Eindpunt | Functionaliteit |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Stuur device-to-cloud berichten. |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |Ontvang berichten van cloud tot apparaat. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Een symmetrische sleutel gebruiken in het identiteitsregister

Wanneer u de symmetrische sleutel van een apparaatidentiteit gebruikt`skn`om een token te genereren, wordt het element policyName ( ) van het token weggelaten.

Een token dat is gemaakt om toegang te krijgen tot alle apparaatfunctionaliteit, moet bijvoorbeeld de volgende parameters hebben:

* resource URI: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* ondertekeningssleutel: elke symmetrische `{device id}` sleutel voor de identiteit,
* geen beleidsnaam,
* eventuele verlooptijd.

Een voorbeeld met de vorige functie Node.js is:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

Het resultaat, dat toegang verleent tot alle functionaliteit voor apparaat1, zou zijn:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> Het is mogelijk om een SAS-token te genereren met de OPDRACHT [AZ Iot-hub generate-sas-token](/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-generate-sas-token)of de [Azure IoT-tools voor Visual Studio Code.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

### <a name="use-a-shared-access-policy"></a>Een beleid voor gedeelde toegang gebruiken

Wanneer u een token maakt vanuit een `skn` beleid voor gedeelde toegang, stelt u het veld in op de naam van het beleid. Dit beleid moet de **Toestemming van DeviceConnect** verlenen.

De twee belangrijkste scenario's voor het gebruik van beleid voor gedeelde toegang voor toegang tot apparaatfunctionaliteit zijn:

* [cloudprotocolgateways](iot-hub-devguide-endpoints.md),
* [tokenservices](iot-hub-devguide-security.md#custom-device-and-module-authentication) die worden gebruikt om aangepaste verificatieschema's te implementeren.

Aangezien het beleid voor gedeelde toegang mogelijk toegang kan verlenen tot verbinding als elk apparaat, is het belangrijk om de juiste bron URI te gebruiken bij het maken van beveiligingstokens. Deze instelling is vooral belangrijk voor tokenservices, die het token moeten gebruiken voor een specifiek apparaat met behulp van de bron URI. Dit punt is minder relevant voor protocolgateways omdat ze al verkeer bemiddelen voor alle apparaten.

Als voorbeeld maakt een tokenservice met het vooraf gemaakte beleid voor gedeelde toegang, **het zogenaamde apparaat,** een token met de volgende parameters:

* resource URI: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* ondertekeningssleutel: een van `device` de sleutels van het beleid,
* beleidsnaam: `device`,
* eventuele verlooptijd.

Een voorbeeld met de vorige functie Node.js is:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Het resultaat, dat toegang verleent tot alle functionaliteit voor apparaat1, zou zijn:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

Een protocolgateway kan hetzelfde token gebruiken voor alle `myhub.azure-devices.net/devices`apparaten en de bron URI eenvoudig instellen op .

### <a name="use-security-tokens-from-service-components"></a>Beveiligingstokens van servicecomponenten gebruiken

Servicecomponenten kunnen alleen beveiligingstokens genereren met behulp van beleid voor gedeelde toegang waarbij de juiste machtigingen worden verleend, zoals eerder is uitgelegd.

Hier zijn de servicefuncties die op de eindpunten worden weergegeven:

| Eindpunt | Functionaliteit |
| --- | --- |
| `{iot hub host name}/devices` |Apparaatidentiteiten maken, bijwerken, ophalen en verwijderen. |
| `{iot hub host name}/messages/events` |Ontvang device-to-cloud berichten. |
| `{iot hub host name}/servicebound/feedback` |Ontvang feedback voor berichten van cloud naar apparaat. |
| `{iot hub host name}/devicebound` |Cloud-to-device-berichten verzenden. |

Een service die wordt genereren met behulp van het vooraf gemaakte beleid voor gedeelde toegang, **registerRead** genaamd, maakt bijvoorbeeld een token met de volgende parameters:

* resource URI: `{IoT hub name}.azure-devices.net/devices`,
* ondertekeningssleutel: een van `registryRead` de sleutels van het beleid,
* beleidsnaam: `registryRead`,
* eventuele verlooptijd.

```javascript
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'registryRead';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Het resultaat, dat toegang zou verlenen om alle identiteiten van het apparaat te lezen, zou zijn:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="supported-x509-certificates"></a>Ondersteunde X.509-certificaten

U elk X.509-certificaat gebruiken om een apparaat met IoT Hub te verifiëren door een certificaatduimafdruk of een certificaatautoriteit (CA) te uploaden naar Azure IoT Hub. Verificatie met certificaatduimafdrukken controleert of de gepresenteerde duimafdruk overeenkomt met de geconfigureerde duimafdruk. Verificatie met behulp van certificaatautoriteit valideert de certificaatketen. Hoe dan ook, TLS handshake vereist dat het apparaat een geldig certificaat en een persoonlijke sleutel heeft. Raadpleeg de TLS-specificatie voor meer informatie, bijvoorbeeld: [RFC 5246 - The Transport Layer Security (TLS) Protocol Version 1.2](https://tools.ietf.org/html/rfc5246/).

Ondersteunde certificaten zijn:

* **Een bestaand X.509-certificaat**. Aan een apparaat is mogelijk al een X.509-certificaat gekoppeld. Het apparaat kan dit certificaat gebruiken om te verifiëren met IoT Hub. Werkt met duimafdruk of CA-verificatie. 

* **CA-ondertekend X.509-certificaat**. Als u een apparaat wilt identificeren en verifiëren met IoT Hub, u een X.509-certificaat gebruiken dat is gegenereerd en ondertekend door een certificeringsinstantie (CA). Werkt met duimafdruk of CA-verificatie.

* **Een zelf gegenereerd en zelf ondertekend X-509 certificaat.** Een apparaatfabrikant of interne deployer kan deze certificaten genereren en de bijbehorende privésleutel (en certificaat) op het apparaat opslaan. U hiervoor hulpprogramma's zoals [OpenSSL](https://www.openssl.org/) en [Windows SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) gebruiken. Werkt alleen met verificatie van duimafdruk. 

Een apparaat kan een X.509-certificaat of een beveiligingstoken gebruiken voor verificatie, maar niet beide.

Zie [Apparaatverificatie met X.509 CA-certificaten](iot-hub-x509ca-overview.md)voor meer informatie over verificatie met behulp van certificaatautoriteit.

### <a name="register-an-x509-certificate-for-a-device"></a>Een X.509-certificaat registreren voor een apparaat

De [Azure IoT Service SDK voor C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service) (versie 1.0.8+) ondersteunt het registreren van een apparaat dat een X.509-certificaat gebruikt voor verificatie. Andere API's zoals import/export van apparaten ondersteunen ook X.509-certificaten.

U de OPDRACHT CLI-extensie az [iot-hub-apparaatidentiteit](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) ook gebruiken om X.509-certificaten voor apparaten te configureren.

### <a name="c-support"></a>C\# Ondersteuning

De klasse **RegistryManager** biedt een programmatische manier om een apparaat te registreren. Met name de methoden **AddDeviceAsync** en **UpdateDeviceAsync** stellen u in staat om een apparaat te registreren en bij te werken in het identiteitsregister van de IoT Hub. Deze twee methoden nemen een **apparaatinstantie** als invoer. De klasse **Apparaat** bevat een eigenschap **Verificatie** waarmee u primaire en secundaire duimafdrukken van het X.509-certificaat opgeven. De duimafdruk vertegenwoordigt een SHA256 hash van het X.509 certificaat (opgeslagen met behulp van binaire DER codering). U hebt de mogelijkheid om een primaire duimafdruk of een secundaire duimafdruk of beide op te geven. Primaire en secundaire duimafdrukken worden ondersteund voor het verwerken van certificaatrolloverscenario's.

Hier is een\# voorbeeld c-code fragment om een apparaat te registreren met behulp van een X.509 certificaat duimafdruk:

```csharp
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "B4172AB44C28F3B9E117648C6F7294978A00CDCBA34A46A1B8588B3F7D82C4F1"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

### <a name="use-an-x509-certificate-during-run-time-operations"></a>Een X.509-certificaat gebruiken tijdens runtime-bewerkingen

De [Azure IoT-apparaat SDK voor .NET](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device) (versie 1.0.11+) ondersteunt het gebruik van X.509-certificaten.

### <a name="c-support"></a>C\# Ondersteuning

De klasse **DeviceAuthenticationWithX509Certificate** ondersteunt het maken van **DeviceClient-exemplaren** met behulp van een X.509-certificaat. Het X.509-certificaat moet zich in de PFX-indeling (ook wel PKCS #12) hebben, die de privésleutel bevat.

Hier is een voorbeeld code fragment:

```csharp
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-and-module-authentication"></a>Aangepaste apparaat- en moduleverificatie

U het [identiteitsregister](iot-hub-devguide-identity-registry.md) van de IoT Hub gebruiken om beveiligingsreferenties per apparaat/module en toegangsbeheer te configureren met behulp van [tokens.](iot-hub-devguide-security.md#security-tokens) Als een IoT-oplossing al een aangepast identiteitsregister en/of verificatieschema heeft, u overwegen een *tokenservice* te maken om deze infrastructuur te integreren met IoT Hub. Op deze manier u andere IoT-functies in uw oplossing gebruiken.

Een tokenservice is een aangepaste cloudservice. Het maakt gebruik van een IoT Hub *gedeelde toegangsbeleid* met **DeviceConnect** of **ModuleConnect-machtigingen** om tokens met *een apparaatof* *modulete maken.* Met deze tokens kunnen een apparaat en module verbinding maken met uw IoT-hub.

![Stappen van het tokenservicepatroon](./media/iot-hub-devguide-security/tokenservice.png)

Hier volgen de belangrijkste stappen van het tokenservicepatroon:

1. Maak een iot-hub-toegangsbeleid voor gedeelde delen met **DeviceConnect-** of **ModuleConnect-machtigingen** voor uw IoT-hub. U dit beleid maken in de [Azure-portal](https://portal.azure.com) of programmatisch. De tokenservice gebruikt dit beleid om de tokens te ondertekenen die worden gemaakt.

2. Wanneer een apparaat/module toegang nodig heeft tot uw IoT-hub, vraagt het om een ondertekend token van uw tokenservice. Het apparaat kan verifiëren met uw aangepaste identiteitsregister/verificatieschema om de identiteit van het apparaat/de module te bepalen die de tokenservice gebruikt om het token te maken.

3. De tokenservice retourneert een token. Het token wordt `/devices/{deviceId}` gemaakt `/devices/{deviceId}/module/{moduleId}` `resourceURI`met `deviceId` of als , met `moduleId` als het apparaat wordt geverifieerd of als de module wordt geverifieerd. De tokenservice gebruikt het beleid voor gedeelde toegang om het token te construeren.

4. Het apparaat/module gebruikt het token rechtstreeks met de IoT-hub.

> [!NOTE]
> U de .NET-klasse [SharedAccessSignatureBuilder](https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx) of de [Java-klasse IotHubServiceSasToken](/java/api/com.microsoft.azure.sdk.iot.service.auth.iothubservicesastoken) gebruiken om een token in uw tokenservice te maken.

De tokenservice kan de vervaldatum van het token naar wens instellen. Wanneer het token verloopt, wordt de verbinding tussen het apparaat en de module verbroken. Vervolgens moet het apparaat/module een nieuw token aanvragen bij de tokenservice. Een korte vervaldatum verhoogt de belasting op zowel het apparaat/de module als de tokenservice.

Als u een apparaat/module wilt verbinden met uw hub, moet u deze nog steeds toevoegen aan het identiteitsregister van de IoT Hub, ook al gebruikt het een token en geen sleutel om verbinding te maken. Daarom u toegangsbeheer per apparaat/per module blijven gebruiken door apparaat-/module-identiteiten in het [identiteitsregister](iot-hub-devguide-identity-registry.md)in te schakelen of uit te schakelen. Deze aanpak vermindert de risico's van het gebruik van tokens met lange vervaltijden.

### <a name="comparison-with-a-custom-gateway"></a>Vergelijking met een aangepaste gateway

Het tokenservicepatroon is de aanbevolen manier om een aangepast identiteitsregister/verificatieschema met IoT Hub te implementeren. Dit patroon wordt aanbevolen omdat IoT Hub het grootste deel van het oplossingsverkeer blijft verwerken. Als het aangepaste verificatieschema echter zo verweven is met het protocol, hebt u mogelijk een *aangepaste gateway* nodig om al het verkeer te verwerken. Een voorbeeld van een dergelijk scenario is het gebruik van [Tls (Transport Layer Security) en vooraf gedeelde sleutels (PSK's).](https://tools.ietf.org/html/rfc4279) Zie het artikel over de [protocolgateway](iot-hub-protocol-gateway.md) voor meer informatie.

## <a name="reference-topics"></a>Referentieonderwerpen:

De volgende referentieonderwerpen geven u meer informatie over het beheren van toegang tot uw IoT-hub.

## <a name="iot-hub-permissions"></a>IoT Hub-machtigingen

In de volgende tabel worden de machtigingen weergegeven die u gebruiken om de toegang tot uw IoT-hub te beheren.

| Machtiging | Opmerkingen |
| --- | --- |
| **RegisterLezen** |Verleent leestoegang tot het identiteitsregister. Zie [Identiteitsregister](iot-hub-devguide-identity-registry.md)voor meer informatie . <br/>Deze toestemming wordt gebruikt door back-end cloudservices. |
| **RegisterReadWrite** |Subsidies lezen en schrijven toegang tot het identiteitsregister. Zie [Identiteitsregister](iot-hub-devguide-identity-registry.md)voor meer informatie . <br/>Deze toestemming wordt gebruikt door back-end cloudservices. |
| **ServiceConnect** |Verleent toegang tot cloudservicegerichte communicatie- en monitoringeindpunten. <br/>Verleent toestemming om device-to-cloudberichten te ontvangen, cloud-to-device-berichten te verzenden en de bijbehorende leveringsbevestigingen op te halen. <br/>Verleent toestemming om leveringsbevestigingen voor bestandsuploads op te halen. <br/>Verleent toestemming om toegang te krijgen tot tweelingen om tags en gewenste eigenschappen bij te werken, gerapporteerde eigenschappen op te halen en query's uit te voeren. <br/>Deze toestemming wordt gebruikt door back-end cloudservices. |
| **DeviceConnect** |Verleent toegang tot apparaatgerichte eindpunten. <br/>Verleent toestemming om device-to-cloudberichten te verzenden en berichten van cloud naar apparaat te ontvangen. <br/>Verleent toestemming om het uploaden van bestanden vanaf een apparaat uit te voeren. <br/>Verleent toestemming om apparaat twee gewenste eigenschapsmeldingen te ontvangen en apparaat tweeling gerapporteerde eigenschappen bij te werken. <br/>Verleent toestemming om bestandsuploads uit te voeren. <br/>Deze toestemming wordt gebruikt door apparaten. |

## <a name="additional-reference-material"></a>Aanvullend referentiemateriaal

Andere referentieonderwerpen in de IoT Hub-ontwikkelaarshandleiding zijn:

* [IoT Hub-eindpunten](iot-hub-devguide-endpoints.md) beschrijven de verschillende eindpunten die elke IoT-hub blootlegt voor run-time- en beheerbewerkingen.

* [Beperking en quota](iot-hub-devguide-quotas-throttling.md) beschrijven de quota en beperkingsgedragingen die van toepassing zijn op de IoT Hub-service.

* [Azure IoT-apparaat en service-SDK's](iot-hub-devguide-sdks.md) bevat de verschillende taal-SDK's die u gebruiken wanneer u zowel apparaat- als service-apps ontwikkelt die met IoT Hub werken.

* [IoT Hub-querytaal](iot-hub-devguide-query-language.md) beschrijft de querytaal die u gebruiken om informatie op te halen uit IoT Hub over uw apparaattweeling en taken.

* [IoT Hub MQTT-ondersteuning](iot-hub-mqtt-support.md) biedt meer informatie over IoT Hub-ondersteuning voor het MQTT-protocol.

* [RFC 5246 - Het TLS-protocol versie 1.2 (Transport Layer Security)](https://tools.ietf.org/html/rfc5246/) bevat meer informatie over TLS-verificatie.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u de toegang tot IoT Hub beheren, bent u mogelijk geïnteresseerd in de volgende onderwerpen voor IoT Hub-ontwikkelaars:

* [Apparaattweelingen gebruiken om status en configuraties te synchroniseren](iot-hub-devguide-device-twins.md)
* [Een directe methode op een apparaat aanroepen](iot-hub-devguide-direct-methods.md)
* [Taken op meerdere apparaten plannen](iot-hub-devguide-jobs.md)

Als u enkele concepten wilt uitproberen die in dit artikel worden beschreven, raadpleegt u de volgende IoT Hub-zelfstudies:

* [Aan de slag met Azure IoT Hub](quickstart-send-telemetry-node.md)
* [Cloud-naar-device-berichten verzenden met IoT Hub](iot-hub-csharp-csharp-c2d.md)
* [IoT Hub-berichten van device-to-cloud verwerken](tutorial-routing.md)
