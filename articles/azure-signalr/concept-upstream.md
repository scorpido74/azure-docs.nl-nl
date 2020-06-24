---
title: Upstream-instellingen in de Azure signalerings service
description: Een inleiding in de upstream-instellingen en het protocol voor upstream-berichten
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: chenyl
ms.openlocfilehash: 7434e8796ddcd89968a0ffa0328a823d635f51c9
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/18/2020
ms.locfileid: "84988609"
---
# <a name="upstream-settings"></a>Upstream-instellingen

Upstream is een functie waarmee de signaal service berichten en verbindings gebeurtenissen kan verzenden naar een set eind punten in de serverloze modus. Upstream kan worden gebruikt voor het aanroepen van een hub-methode van clients in de serverloze modus en laat eind punten een melding ontvangen wanneer client verbindingen zijn verbonden of verbroken.

> [!NOTE]
> Alleen serverloze modus kan upstream-instellingen configureren.

## <a name="upstream-settings-details"></a>Details van de upstream-instellingen

De upstream-instellingen bestaan uit een lijst met volg orde gevoelige items. Elk item bestaat uit een `URL Template` , waarmee wordt aangegeven waar berichten worden verzonden naar een set `Rules` en `Authentication` configuraties. Wanneer de opgegeven gebeurtenis plaatsvindt, wordt het item op `Rules` één in de juiste volg orde gecontroleerd en worden er berichten verzonden naar de eerste overeenkomende URL van de upstream van het item.

### <a name="url-template-settings"></a>URL-sjabloon instellingen

De URL kan worden para meters voor het ondersteunen van verschillende patronen. Er zijn drie vooraf gedefinieerde para meters:

|Vooraf gedefinieerde para meter|Beschrijving|
|---------|---------|
|hub| Hub is een concept van de signaal sterkte. Een hub is een isolatie-eenheid, het bereik van de levering van gebruikers en berichten is beperkt tot een hub|
|rubriek| Categorie kan een van de volgende waarden hebben: <ul><li>**verbindingen**: levens duur van de verbinding. Deze gebeurtenis wordt gestart wanneer een client verbinding is verbonden of verbroken. Inclusief *verbonden* en niet- *verbonden* gebeurtenissen</li><li>**berichten**: wordt geactiveerd wanneer clients een hub-methode aanroepen. Inclusief alle andere gebeurtenissen behalve die in de categorie *verbindingen*</li></ul>|
|gebeurtenislog| Voor *bericht* categorie is gebeurtenis het *doel* in het [aanroep bericht](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) dat clients verzenden. Voor de categorie *verbindingen* wordt alleen *verbinding* gemaakt met en de *verbinding is verbroken* .|

Deze vooraf gedefinieerde para meters kunnen worden gebruikt in de `URL Pattern` para meters en worden vervangen door een opgegeven waarde bij het evalueren van de URL van de upstream. Bijvoorbeeld 
```
http://host.com/{hub}/api/{category}/{event}
```
Wanneer een client verbinding in hub ' chat ' is verbonden, wordt een bericht verzonden naar de URL:
```
http://host.com/chat/api/connections/connected
```
Wanneer een client in hub een `chat` hub-methode aanroept `broadcast` , wordt een bericht verzonden naar de URL:
```
http://host.com/chat/api/messages/broadcast
```

### <a name="rules-settings"></a>Instellingen voor regels

U kunt regels voor de regels voor de *hub*, *categorie regels* en *gebeurtenis regels* afzonderlijk instellen. De overeenkomende regel ondersteunt drie indelingen. Neem de *gebeurtenis regels* als voor beeld:
- Gebruik sterretje (*) om gebeurtenissen te vergelijken.
- Gebruikers-komma (,) om lid te worden van meerdere gebeurtenissen. Bijvoorbeeld, `connected, disconnected` komt overeen met *verbonden* en *losgekoppelde*gebeurtenissen.
- De volledige gebeurtenis naam moet overeenkomen met de gebeurtenis. Bijvoorbeeld, `connected` komt overeen *met verbonden* gebeurtenis.

### <a name="authentication-settings"></a>Verificatie-instellingen

U kunt *verificatie* voor elk item van de upstream-instellingen afzonderlijk configureren. Wanneer de *verificatie*is geconfigureerd, wordt er een token ingesteld in de *verificatie* header van het upstream-bericht. De huidige, seingevings service ondersteunt het volgende verificatie type
- Geen
- ManagedIdentity

Wanneer u *ManagedIdentity*selecteert, moet u de beheerde identiteit in de signaal service vooraf inschakelen en optioneel een *resource*opgeven. Zie [Managed identiteiten for Azure signalering service](howto-use-managed-identity.md) voor meer informatie.

## <a name="create-upstream-settings"></a>Upstream-instellingen maken

### <a name="create-upstream-settings-via-azure-portal"></a>Upstream-instellingen maken via Azure Portal

1. Ga naar de Azure signalerings service.
2. Klik in de *instellingen* Bland en Schakel *Service modus* in op *serverloos*. De *upstream-instellingen* worden als volgt weer gegeven:

    :::image type="content" source="media/concept-upstream/upstream-portal.png" alt-text="Upstream-instellingen":::

3. Vul de url's in naar het URL-patroon van de *upstream*. vervolgens wordt de standaard waarde weer gegeven in de instellingen van de *hub* .
4. Als u instellingen wilt instellen, zoals regels voor de *hub*, *gebeurtenis regels*, *categorie regels* en *upstream-verificatie*, klikt u op de waarde van *hub-regels*. Een pagina waarop u instellingen kunt bewerken, zoals hieronder wordt weer gegeven:

    :::image type="content" source="media/concept-upstream/upstream-detail-portal.png" alt-text="Upstream-instellingen":::

5. Als u een *upstream-verificatie*wilt instellen, moet u eerst beheerde identiteit hebben ingeschakeld en vervolgens *Managed Identity gebruiken* onder *upstream-verificatie*. Afhankelijk van uw behoeften kunt u opties selecteren onder *verificatie resource-id*. Zie [beheerde identiteit inschakelen](howto-use-managed-identity.md) voor meer informatie.

### <a name="create-upstream-settings-via-arm-template"></a>Upstream-instellingen maken via ARM-sjabloon

Als u upstream-instellingen wilt maken met behulp van een [Resource Manager-sjabloon](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview), stelt u `upstream` de eigenschap in op de `properties` eigenschap. De volgende code fragmenten laten zien hoe u de `upstream` eigenschap kunt instellen voor het maken en bijwerken van de instellingen voor de upstream.

```JSON
{
  "properties": {
    "upstream": {
      "templates": [
        {
          "UrlTemplate": "http://host.com/{hub}/api/{category}/{event}",
          "EventPattern": "*",
          "HubPattern": "*",
          "CategoryPattern": "*",
          "Auth": {
            "Type": "ManagedIdentity",
            "ManagedIdentity": {
              "Resource": "<resource>"
            }
          }
        }
      ]
    }
  }
}
```

## <a name="signalr-serverless-protocol"></a>Seingevings server zonder protocol

De seingevings service stuurt berichten naar eind punten die de volgende protocollen volgen.

### <a name="method"></a>Methode

POST

### <a name="request-header"></a>Aanvraagheader

|Name |Beschrijving|
|---------|---------|
|X-ASRS-Connection-ID |De verbindings-id voor de client verbinding|
|X-ASRS-hub |De hub waartoe de client verbinding behoort|
|X-ASRS-categorie |De categorie waartoe het bericht behoort|
|X-ASRS-Event |De gebeurtenis waarvan het bericht deel uitmaakt|
|X-ASRS-hand tekening |Een HMAC die voor validatie wordt gebruikt. Zie [hand tekening](#signature) voor meer informatie.|
|X-ASRS-User-claims |Een groep claims van de client verbinding|
|X-ASRS-User-ID |De gebruikers-id van de client die het bericht verzendt|
|X-ASRS-client-query |De query van de aanvraag wanneer clients verbinding maken met de service|
|Verificatie |Een optioneel token bij gebruik van *ManagedIdentity* |

### <a name="request-body"></a>Aanvraagbody

#### <a name="connected"></a>Verbonden

Content-type: Application/JSON

#### <a name="disconnected"></a>Ontkoppeld

Inhouds type:`application/json`

|Naam  |Type  |Description  |
|---------|---------|---------|
|Fout |tekenreeks |Het fout bericht van de verbinding is verbroken. Leeg wanneer de verbindingen worden gesloten zonder fout|

#### <a name="invocation-message"></a>Aanroep bericht

Content-type: `application/json` of`application/x-msgpack`

|Naam  |Type  |Beschrijving  |
|---------|---------|---------|
|InvocationId |tekenreeks | Een optionele teken reeks vertegenwoordigt een aanroep bericht. Details zoeken in [aanroepen](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocations).|
|Doel |tekenreeks | Hetzelfde als *gebeurtenis* en dezelfde als het *doel* in het [aanroep bericht](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding). |
|Argumenten |Matrix van object |Een matrix met argumenten die moeten worden toegepast op de methode waarnaar wordt verwezen in target. |

### <a name="signature"></a>Handtekening

De service berekent de SHA256-code voor de `X-ASRS-Connection-Id` waarde met zowel de primaire toegangs sleutel als de secundaire toegangs sleutel als de `HMAC` sleutel. deze wordt in de `X-ASRS-Signature` header ingesteld wanneer HTTP-aanvragen worden verzonden naar de upstream:
```
Hex_encoded(HMAC_SHA256(accessKey, connection-id))
```

## <a name="next-steps"></a>Volgende stappen

- [Beheerde identiteiten gebruiken voor de Azure signalerings service](howto-use-managed-identity.md)
- [Ontwikkeling en configuratie van Azure Functions met Azure SignalR Service](signalr-concept-serverless-development-config.md)
