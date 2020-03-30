---
title: Toegang tot Azure Event Hubs verifiëren met handtekeningen voor gedeelde toegang
description: In dit artikel ziet u hoe u de toegang tot bronnen voor gebeurtenishubs verifiëren met behulp van handtekeningen voor gedeelde toegang.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: spelluru
ms.openlocfilehash: d17026dba26b3c1cb846d60967180c29563c425d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74545592"
---
# <a name="authenticate-access-to-event-hubs-resources-using-shared-access-signatures-sas"></a>Toegang tot Bronnen voor gebeurtenishubs verifiëren met behulp van gedeelde toegangshandtekeningen (SAS)
Met de handtekening van gedeelde toegang (SAS) hebt u gedetailleerde controle over het type toegang dat u verleent aan de clients die de handtekening voor gedeelde toegang hebben. Hier volgen enkele besturingselementen die u in een SAS instellen: 

- Het interval waarop de SAS geldig is, inclusief de begintijd en de vervaldatum.
- De door de SAS verleende machtigingen. Een SAS voor een naamruimte voor gebeurtenishubs kan bijvoorbeeld de luistertoestemming verlenen, maar niet de verzendtoestemming.
- Alleen clients die geldige referenties presenteren, kunnen gegevens naar een gebeurtenishub verzenden.
- Een client kan zich niet voordoen als een andere client.
- Een rougeclient kan worden geblokkeerd voor het verzenden van gegevens naar een gebeurtenishub.

In dit artikel wordt de toegang tot Event Hubs-bronnen met SAS geauthenticeren. Zie [dit artikel](authorize-access-shared-access-signature.md)voor meer informatie over het **toestaan van** toegang tot bronnen van gebeurtenishubs met SAS. 

> [!NOTE]
> Microsoft raadt u aan om Azure AD-referenties waar mogelijk te gebruiken als best practice voor beveiliging, in plaats van de gedeelde toegangshandtekeningen te gebruiken, die gemakkelijker kunnen worden gecompromitteerd. Hoewel u shared access signatures (SAS) blijven gebruiken om fijnmazige toegang tot uw Event Hubs-bronnen te verlenen, biedt Azure AD vergelijkbare mogelijkheden zonder dat u SAS-tokens hoeft te beheren of zich zorgen hoeft te maken over het intrekken van een gecompromitteerde SAS.
> 
> Zie [Toegang tot gebeurtenishubs autoriseren met Azure AD](authorize-access-azure-active-directory.md)voor meer informatie over azure AD-integratie in Azure Event Hubs. 


## <a name="configuring-for-sas-authentication"></a>Configureren voor SAS-verificatie
U de regel voor gedeelde toegangsautorisatie van EventHubs configureren op een naamruimte voor gebeurtenishubs of een entiteit (gebeurtenishub-instantie of Kafka-onderwerp in een gebeurtenishubs voor naamruimte met Kafka). Het configureren van een regel voor gedeelde toegangsautorisatie voor een consumentengroep wordt momenteel niet ondersteund, maar u regels gebruiken die zijn geconfigureerd voor een naamruimte of entiteit om de toegang tot consumentengroep te beveiligen. 

In de volgende afbeelding ziet u hoe de autorisatieregels van toepassing zijn op voorbeeldentiteiten. 

![Autorisatieregel configureren](./media/authenticate-shared-access-signature/configure-sas-authorization-rule.png)

In dit voorbeeld heeft het voorbeeld naamruimte gebeurtenishubs (ExampleNamespace) twee entiteiten: eh1 en topic1. De autorisatieregels worden zowel op entiteitsniveau als op naamruimteniveau gedefinieerd.  

De autorisatieregels manageRuleNS, sendRuleNS en listenRuleNS zijn van toepassing op zowel gebeurtenishub-instantie eh1 als onderwerp t1. De autorisatieregel listenRegel-eh en sendRule-eh zijn alleen van toepassing op gebeurtenishub-instantie eh1 en de regel sendRuleT-autorisatie geldt alleen voor onderwerp1. 

Bij het gebruik van de autorisatieregel sendRuleNS kunnen clienttoepassingen zowel naar eh1 als naar topic1 worden verzonden. Wanneer de autorisatieregel sendRuleT wordt gebruikt, wordt gedetailleerde toegang tot onderwerp1 alleen afgedwongen en daarom kunnen clienttoepassingen die deze regel gebruiken voor toegang nu niet naar eh1, maar alleen naar onderwerp1 worden verzonden.

## <a name="generate-a-shared-access-signature-token"></a>Een token voor gedeelde toegangshandtekeningen genereren 
Elke client die toegang heeft tot de naam van een naam van een autorisatieregel naam en een van de ondertekeningssleutels kan een SAS-token genereren. Het token wordt gegenereerd door een tekenreeks in de volgende indeling te maken:

- `se`– Token vervaldatum direct. Gehele getal dat seconden sinds tijdvak 00:00:00 UTC op 1 Januari 1970 (unix tijdvak) weergeeft wanneer het teken verloopt
- `skn`– Naam van de autorisatieregel, dat is de SAS-sleutelnaam.
- `sr`– URI van de resource die wordt geopend.
- `sig`– Handtekening.

De handtekeningtekenreeks is de SHA-256 hash die is berekend over de resource URI (bereik zoals beschreven in de vorige sectie) en de tekenreeksweergave van het token-vervaldatummoment, gescheiden door CRLF.

De hash-berekening lijkt op de volgende pseudo-code en retourneert een hashwaarde van 256 bits/32 byte. 

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Het token bevat de niet-gehashte waarden, zodat de ontvanger de hash opnieuw kan berekenen met dezelfde parameters, om te controleren of de uitgever in het bezit is van een geldige ondertekeningssleutel.

De bron URI is de volledige URI van de Service Bus-bron waartoe toegang wordt geclaimd. Bijvoorbeeld, http://<namespace><entityPath> .servicebus.windows.net/ `sb://<namespace>.servicebus.windows.net/<entityPath>;` of `http://contoso.servicebus.windows.net/eventhubs/eh1`dat wil zeggen, .

De URI moet door procent gecodeerd zijn.

De regel voor gedeelde toegangsautorisatie die wordt gebruikt voor ondertekening, moet worden geconfigureerd op de entiteit die is opgegeven door deze URI of door een van de hiërarchische ouders. Bijvoorbeeld, `http://contoso.servicebus.windows.net/eventhubs/eh1` of `http://contoso.servicebus.windows.net` in het vorige voorbeeld.

Een SAS-token is geldig voor <resourceURI> alle bronnen die vooraf zijn gekoppeld aan de gebruikte tekenreeks.

> [!NOTE]
> U genereert een toegangstoken voor gebeurtenishubs met behulp van beleid voor gedeelde toegang. Zie [Beleid voor toegangsautorisatie voor gedeelde toegang](authorize-access-shared-access-signature.md#shared-access-authorization-policies)voor meer informatie .

### <a name="generating-a-signaturetoken-from-a-policy"></a>Een handtekening(token) genereren vanuit een beleid 
Volgende sectie toont het genereren van een SAS-token met behulp van beleid voor handtekening van gedeelde toegang,

#### <a name="nodejs"></a>Node.js

```javascript
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
```

#### <a name="java"></a>Java

```java
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```
#### <a name="php"></a>PHP

```php
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
    $targetUri = strtolower(rawurlencode(strtolower($uri))); 
    $expires = time();  
    $expiresInMins = 60; 
    $week = 60*60*24*7;
    $expires = $expires + $week; 
    $toSign = $targetUri . "\n" . $expires; 
    $signature = rawurlencode(base64_encode(hash_hmac('sha256',             
     $toSign, $sasKeyValue, TRUE))); 
    
    $token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires .      "&skn=" . $sasKeyName; 
    return $token; 
}
```

#### <a name="c"></a>C#

```csharp
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## <a name="authenticating-event-hubs-publishers-with-sas"></a>Uitgevers van Event Hubs authenticeren met SAS 
Een gebeurtenisuitgever definieert een virtueel eindpunt voor een gebeurtenishub. De uitgever kan alleen worden gebruikt om berichten naar een gebeurtenishub te verzenden en geen berichten te ontvangen.

Een gebeurtenishub heeft doorgaans één uitgever per client in dienst. Alle berichten die naar een van de uitgevers van een gebeurtenishub worden verzonden, zijn in de wachtrij in die gebeurtenishub. Uitgevers maken fijnmazige toegangscontrole mogelijk.

Elke Client Event Hubs krijgt een uniek token toegewezen, dat naar de client wordt geüpload. De tokens worden zo geproduceerd dat elk uniek token toegang verleent aan verschillende unieke uitgevers. Een client met een token kan slechts naar één uitgever en geen andere uitgever worden verzonden. Als meerdere clients hetzelfde token delen, deelt elk van hen de uitgever.

Alle tokens worden toegewezen met SAS-sleutels. Normaal gesproken zijn alle tokens ondertekend met dezelfde sleutel. Klanten zijn zich niet bewust van de sleutel, waardoor clients geen tokens kunnen produceren. Clients werken op dezelfde tokens totdat ze verlopen.

Als u bijvoorbeeld autorisatieregels wilt definiëren die beperkt zijn tot alleen verzenden/publiceren naar gebeurtenishubs, moet u een machtigingsregel voor verzenden definiëren. Dit kan worden gedaan op een naamruimteniveau of meer gedetailleerd bereik geven aan een bepaalde entiteit (instantie gebeurtenishubs of een onderwerp). Een client of een toepassing die is scoped met een dergelijke gedetailleerde toegang wordt genoemd, Event Hubs uitgever. Hiervoor volgt u de volgende stappen:

1. Maak een SAS-sleutel op de entiteit die u wilt publiceren om het **verzendbereik** toe te wijzen. Zie [Beleid voor beheerde toegangsautorisatie](authorize-access-shared-access-signature.md#shared-access-authorization-policies)voor meer informatie .
2. Genereer een SAS-token met een vervaldatum voor een specifieke uitgever met behulp van de sleutel die in stap1 is gegenereerd.

    ```csharp
    var sasToken = SharedAccessSignatureTokenProvider.GetPublisherSharedAccessSignature(
                new Uri("Service-Bus-URI"),
                "eventub-name",
                "publisher-name",
                "sas-key-name",
                "sas-key",
                TimeSpan.FromMinutes(30));
    ```
3. Geef het token aan de uitgeverclient, die alleen kan worden verzonden naar de entiteit en de uitgever waartoe token toegang verleent.

    Zodra het token verloopt, verliest de client zijn toegang om naar de entiteit te verzenden/publiceren. 


> [!NOTE]
> Hoewel het niet wordt aanbevolen, is het mogelijk om apparaten uit te rusten met tokens die toegang verlenen tot een gebeurtenishub of een naamruimte. Elk apparaat dat dit token bevat, kan berichten rechtstreeks naar die gebeurtenishub verzenden. Bovendien kan het apparaat niet op de zwarte lijst worden geplaatst van het verzenden naar die gebeurtenishub.
> 
> Het wordt altijd aanbevolen om specifieke en gedetailleerde scopes te geven.

> [!IMPORTANT]
> Zodra de tokens zijn gemaakt, wordt elke client voorzien van zijn eigen unieke token.
>
> Wanneer de client gegevens naar een gebeurtenishub verzendt, wordt de aanvraag tags met het token. Om te voorkomen dat een aanvaller het token afluistert en steelt, moet de communicatie tussen de client en de gebeurtenishub plaatsvinden via een versleuteld kanaal.
> 
> Als een token wordt gestolen door een aanvaller, kan de aanvaller zich voordoen als de client waarvan het token is gestolen. Door een uitgever op de zwarte lijst te zetten, wordt die client onbruikbaar totdat deze een nieuw token ontvangt dat een andere uitgever gebruikt.


## <a name="authenticating-event-hubs-consumers-with-sas"></a>Event Hubs-consumenten authenticeren met SAS 
Als u back-endtoepassingen wilt verifiëren die worden gegenereerd uit de gegevens die worden gegenereerd door producenten van Gebeurtenishubs, vereist gebeurtenishubs tokenverificatie dat zijn clients de **beheerrechten** of de **luisterrechten** hebben die zijn toegewezen aan de naamruimte of gebeurtenishub-instantie of -onderwerp van gebeurtenishubs. Gegevens worden verbruikt via Event Hubs met behulp van consumentengroepen. Hoewel het SAS-beleid u gedetailleerd bereik biedt, wordt dit bereik alleen gedefinieerd op entiteitsniveau en niet op het niveau van de consument. Dit betekent dat de bevoegdheden die zijn gedefinieerd op naamruimteniveau of het gebeurtenishub-exemplaar of onderwerpniveau worden toegepast op de consumentengroepen van die entiteit.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Autoriseren met Behulp van SAS](authenticate-shared-access-signature.md)
- [Machtigen met het gebruik van Role-base access control (RBAC)](authenticate-shared-access-signature.md)
- [Meer informatie over eventhubs](event-hubs-about.md)

Zie de volgende gerelateerde artikelen:

- [Aanvragen voor Azure Event Hubs verifiëren vanuit een toepassing met Azure Active Directory](authenticate-application.md)
- [Een beheerde identiteit verifiëren met Azure Active Directory om toegang te krijgen tot gebeurtenishubsbronnen](authenticate-managed-identity.md)
- [Toegang tot gebeurtenishubsresources autoriseren met Azure Active Directory](authorize-access-azure-active-directory.md)
- [Toegang tot bronnen van gebeurtenishubs autoriseren met behulp van gedeelde toegangshandtekeningen](authorize-access-shared-access-signature.md)