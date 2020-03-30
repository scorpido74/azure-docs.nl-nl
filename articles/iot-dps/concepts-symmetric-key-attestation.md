---
title: Azure IoT Hub Device Provisioning Service - Symmetrische sleutelattest
description: Dit artikel biedt een conceptueel overzicht van symmetrische sleutelattest met behulp van IoT Device Provisioning Service (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 0e3d343c0a68dd527e4e8e8d23e5b3843a216a78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271510"
---
# <a name="symmetric-key-attestation"></a>Attestation met behulp van een symmetrische sleutel

In dit artikel wordt het identiteitsattestationproces beschreven bij het gebruik van symmetrische sleutels met de Apparaatinrichtingsservice. 

Symmetrische sleutelattest is een eenvoudige benadering voor het verifiëren van een apparaat met een apparaatvoorzieningsservice-instantie. Deze attestmethode vertegenwoordigt een "Hello world"-ervaring voor ontwikkelaars die nieuw zijn in het inrichten van apparaten of geen strenge beveiligingsvereisten hebben. Apparaatattest met behulp van een [TPM](concepts-tpm-attestation.md) of een [X.509-certificaat](concepts-security.md#x509-certificates) is veiliger en moet worden gebruikt voor strengere beveiligingsvereisten.

Symmetrische sleutelinschrijvingen bieden ook een geweldige manier voor oudere apparaten, met beperkte beveiligingsfunctionaliteit, om via Azure IoT naar de cloud te stappen. Zie Hoe symmetrische sleutels met oudere apparaten te gebruiken voor meer informatie over symmetrische sleutelattest met oudere [apparaten.](how-to-legacy-device-symm-key.md)


## <a name="symmetric-key-creation"></a>Symmetrische sleutelcreatie

Standaard maakt de Apparaatinrichtingsservice nieuwe symmetrische sleutels met een standaardlengte van 32 bytes wanneer nieuwe inschrijvingen worden opgeslagen met de optie **Sleutels automatisch genereren** ingeschakeld.

![Symmetrische toetsen automatisch genereren](./media/concepts-symmetric-key-attestation/auto-generate-keys.png)

U ook uw eigen symmetrische toetsen voor inschrijvingen opgeven door deze optie uit te schakelen. Wanneer u uw eigen symmetrische toetsen opgeeft, moeten uw sleutels een sleutellengte hebben tussen 16 bytes en 64 bytes. Ook moeten symmetrische toetsen worden geleverd in geldige Base64-indeling.



## <a name="detailed-attestation-process"></a>Gedetailleerd attestproces

Symmetrische sleutelattestation met de Device Provisioning Service wordt uitgevoerd met dezelfde [beveiligingstokens](../iot-hub/iot-hub-devguide-security.md#security-token-structure) die worden ondersteund door IoT-hubs om apparaten te identificeren. Deze beveiligingstokens zijn [SAS-tokens (Shared Access Signature).](../service-bus-messaging/service-bus-sas.md) 

SAS-tokens hebben een gehashte *handtekening* die is gemaakt met behulp van de symmetrische sleutel. De handtekening wordt opnieuw gemaakt door de Apparaatinrichtingsservice om te controleren of een beveiligingstoken dat tijdens de attestation wordt gepresenteerd, authentiek is of niet.

SAS-tokens hebben het volgende formulier:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Hier zijn de onderdelen van elk token:

| Waarde | Beschrijving |
| --- | --- |
| {handtekening} |Een HMAC-SHA256 signature string. Voor afzonderlijke inschrijvingen wordt deze handtekening geproduceerd met behulp van de symmetrische toets (primair of secundair) om de hash uit te voeren. Voor inschrijvingsgroepen wordt een sleutel die is afgeleid van de inschrijvingsgroepsleutel gebruikt om de hash uit te voeren. De hash wordt uitgevoerd op een `URL-encoded-resourceURI + "\n" + expiry`bericht van het formulier: . **Belangrijk:** De sleutel moet worden gedecodeerd vanaf basis64 voordat deze wordt gebruikt om de HMAC-SHA256-berekening uit te voeren. Ook moet het handtekeningresultaat zijn url-gecodeerd. |
| {resourceURI} |URI van het registratieeindpunt dat toegankelijk is met dit token, te beginnen met scope-ID voor het exemplaar Apparaatinrichtingsservice. Bijvoorbeeld: `{Scope ID}/registrations/{Registration ID}` |
| {verlopen} |UTF8-tekenreeksen voor het aantal seconden sinds het tijdperk 00:00:00 GMT op 1 januari 1970. |
| {URL-encoded-resourceURI} |URL-codering met kleine letters van de kleine resource URI |
| {policyName} |De naam van het beleid voor gedeelde toegang waarnaar dit token verwijst. De beleidsnaam die wordt gebruikt bij het inrichten met symmetrische sleutelattest is **registratie**. |

Wanneer een apparaat getuigt van een individuele inschrijving, gebruikt het apparaat de symmetrische sleutel die is gedefinieerd in de afzonderlijke inschrijvingsvermelding om de gehashte handtekening voor het SAS-token te maken.

Zie [Beveiligingstokens](../iot-hub/iot-hub-devguide-security.md#security-token-structure)voor codevoorbeelden waarmee een SAS-token wordt gemaakt.

Het maken van beveiligingstokens voor symmetrische sleutelattest wordt ondersteund door de Azure IoT C SDK. Zie [Een gesimuleerd apparaat met symmetrische sleutels inrichten voor](quick-create-simulated-device-symm-key.md)een voorbeeld dat de Azure IoT C SDK gebruikt om te getuigen van een afzonderlijke inschrijving.


## <a name="group-enrollments"></a>Groepsinschrijvingen

De symmetrische sleutels voor groepsinschrijvingen worden niet rechtstreeks gebruikt door apparaten bij het inrichten. In plaats daarvan apparaten die behoren tot een inschrijvinggroep bepaling met behulp van een afgeleide apparaatsleutel. 

Ten eerste wordt een unieke registratie-ID gedefinieerd voor elk apparaat dat getuigt van een inschrijvingsgroep. Geldige tekens voor de registratie-id zijn alfanumerieke kleine letters en streepjes ('-'). Deze registratie-ID moet iets unieks zijn dat het apparaat identificeert. Een verouderd apparaat biedt bijvoorbeeld mogelijk geen ondersteuning voor veel beveiligingsfuncties. Het verouderde apparaat heeft mogelijk alleen een MAC-adres of serienummer beschikbaar om dat apparaat op unieke wijze te identificeren. In dat geval kan een registratie-id bestaan uit het MAC-adres en het serienummer dat vergelijkbaar is met het volgende:

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Dit exacte voorbeeld wordt gebruikt in het artikel [Hoe oudere apparaten in te richten met behulp van symmetrische sleutels.](how-to-legacy-device-symm-key.md)

Zodra een registratie-ID voor het apparaat is gedefinieerd, wordt de symmetrische sleutel voor de inschrijvingsgroep gebruikt om een [HMAC-SHA256-hash](https://wikipedia.org/wiki/HMAC) van de registratie-id te berekenen om een afgeleide apparaatsleutel te produceren. De hashing van de registratie-ID kan worden uitgevoerd met de volgende C# code:

```csharp
using System; 
using System.Security.Cryptography; 
using System.Text;  

public static class Utils 
{ 
    public static string ComputeDerivedSymmetricKey(byte[] masterKey, string registrationId) 
    { 
        using (var hmac = new HMACSHA256(masterKey)) 
        { 
            return Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(registrationId))); 
        } 
    } 
} 
```

```csharp
String deviceKey = Utils.ComputeDerivedSymmetricKey(Convert.FromBase64String(masterKey), registrationId);
```

De resulterende apparaatsleutel wordt vervolgens gebruikt om een SAS-token te genereren dat moet worden gebruikt voor attestation. Elk apparaat in een inschrijvingsgroep moet getuigen van een beveiligingstoken dat is gegenereerd uit een unieke afgeleide sleutel. De symmetrische sleutel van de inschrijvingsgroep kan niet direct worden gebruikt voor attestation.

#### <a name="installation-of-the-derived-device-key"></a>Installatie van de afgeleide apparaatsleutel

Idealiter worden de apparaatsleutels afgeleid en geïnstalleerd in de fabriek. Deze methode garandeert dat de groepssleutel nooit wordt opgenomen in software die op het apparaat wordt geïmplementeerd. Wanneer het apparaat een MAC-adres of serienummer krijgt toegewezen, kan de sleutel worden afgeleid en in het apparaat worden geïnjecteerd, maar de fabrikant kiest ervoor om het op te slaan.

Houd rekening met het volgende diagram met een tabel met apparaatsleutels die in een fabriek zijn gegenereerd door elke registratie-id van elk apparaat te hashing met de groepsinschrijvingssleutel **(K).** 

![Apparaatsleutels toegewezen vanuit een fabriek](./media/concepts-symmetric-key-attestation/key-diversification.png)

De identiteit van elk apparaat wordt weergegeven door de registratie-ID en afgeleide apparaatsleutel die in de fabriek is geïnstalleerd. De apparaatsleutel wordt nooit gekopieerd naar een andere locatie en de groepssleutel wordt nooit op een apparaat opgeslagen.

Als de apparaatsleutels niet in de fabriek zijn geïnstalleerd, moet een [hardwarebeveiligingsmodule HSM](concepts-security.md#hardware-security-module) worden gebruikt om de identiteit van het apparaat veilig op te slaan.

## <a name="next-steps"></a>Volgende stappen

Nu u kennis hebt van de verklaring van symmetrische sleutels, raadpleegt u de volgende artikelen voor meer informatie:

* [Snelstartgids: een gesimuleerd apparaat inrichten met symmetrische sleutels](quick-create-simulated-device-symm-key.md)
* [Meer informatie over de concepten in auto-provisioning](./concepts-auto-provisioning.md)
* [Aan de slag met automatisch inrichten](./quick-setup-auto-provision.md) 
