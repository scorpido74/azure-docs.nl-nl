---
title: Naslaginformatie over de sdk voor meeslepende lezers
titleSuffix: Azure Cognitive Services
description: De Immersive Reader SDK bevat een JavaScript-bibliotheek waarmee u de Immersive Reader in uw toepassing integreren.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: b20a3e6dd3b32b183bbf34dbefd76f0e4cd56b99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76156400"
---
# <a name="immersive-reader-sdk-reference-guide"></a>Gids voor meeslepende reader SDK

De Immersive Reader SDK bevat een JavaScript-bibliotheek waarmee u de Immersive Reader in uw toepassing integreren.

## <a name="functions"></a>Functions

De SDK legt de functies bloot:

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>launchAsync

Lanceert de Immersive `iframe` Reader in een in uw webapplicatie.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

### <a name="parameters"></a>Parameters

| Name | Type | Beschrijving |
| ---- | ---- |------------ |
| `token` | tekenreeks | Het Azure AD-verificatietoken. |
| `subdomain` | tekenreeks | Het aangepaste subdomein van uw Immersive Reader-bron in Azure. |
| `content` | [Inhoud](#content) | Een object met de inhoud die moet worden weergegeven in de meeslepende lezer. |
| `options` | [Opties](#options) | Opties voor het configureren van bepaalde gedragingen van de immersive reader. Optioneel. |

### <a name="returns"></a>Retourneert

Retourneert een `Promise<LaunchResponse>`, die wordt opgelost wanneer de meeslepende lezer wordt geladen. De `Promise` oplost [`LaunchResponse`](#launchresponse) om een object.

### <a name="exceptions"></a>Uitzonderingen

De `Promise` geretourneerde wordt geweigerd [`Error`](#error) met een object als de meeslepende lezer niet laadt. Zie de [foutcodes](#error-codes)voor meer informatie .

## <a name="close"></a>sluiten

Sluit de meeslepende lezer.

Een voorbeeld van een voorbeeld van deze functie ```hideExitButton: true``` is als de afsluitknop is verborgen door in te stellen in [opties](#options). Vervolgens kan een andere knop (bijvoorbeeld de pijl terug ```close``` van een mobiele kop) deze functie aanroepen wanneer erop wordt geklikt.

```typescript
close(): void;
```

## <a name="renderbuttons"></a>renderknoppen

Deze functie stijlen en updates van het document Immersive Reader knop elementen. Als ```options.elements``` wordt geleverd, dan zal ```options.elements```deze functie knoppen binnen renderen. Anders worden de knoppen weergegeven in de elementen van ```immersive-reader-button```het document die de klasse hebben.

Deze functie wordt automatisch aangeroepen door de SDK wanneer het venster wordt geladen.

Zie [Optionele kenmerken](#optional-attributes) voor meer weergaveopties.

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

### <a name="parameters"></a>Parameters

| Name | Type | Beschrijving |
| ---- | ---- |------------ |
| `options` | [Opties voor renderknoppen](#renderbuttonsoptions) | Opties voor het configureren van bepaalde gedragingen van de functie renderButtons. Optioneel. |

## <a name="types"></a>Typen

### <a name="content"></a>Inhoud

Bevat de inhoud die moet worden weergegeven in de meeslepende lezer.

```typescript
{
    title?: string;    // Title text shown at the top of the Immersive Reader (optional)
    chunks: Chunk[];   // Array of chunks
}
```

### <a name="chunk"></a>Segment

Een enkel stuk gegevens, die zal worden doorgegeven aan de inhoud van de meeslepende lezer.

```typescript
{
    content: string;        // Plain text string
    lang?: string;          // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
    mimeType?: string;      // MIME type of the content (optional). Currently 'text/plain', 'application/mathml+xml', and 'text/html' are supported. Defaults to 'text/plain' if not specified.
}
```

### <a name="launchresponse"></a>LaunchResponse LaunchResponse LaunchResponse LaunchResponse

Bevat het antwoord van `ImmersiveReader.launchAsync`de oproep naar .

```typescript
{
    container: HTMLDivElement;    // HTML element which contains the Immersive Reader iframe
    sessionId: string;            // Globally unique identifier for this session, used for debugging
}
```

### <a name="cookiepolicy-enum"></a>CookieBeleid enum

Een enum dat wordt gebruikt om het beleid voor het cookiegebruik van de Immersive Reader in te stellen. Zie [opties](#options).

```typescript
enum CookiePolicy { Disable, Enable }
```

#### <a name="supported-mime-types"></a>Ondersteunde MIME-typen

| MIME-type | Beschrijving |
| --------- | ----------- |
| tekst/vlakte | Platte tekst. |
| text/html | HTML-inhoud. [Meer informatie](#html-support)|
| toepassing/mathml+xml | Wiskundige markuptaal (MathML). [Meer informatie](./how-to/display-math.md).
| application/vnd.openxmlformats-officedocument.wordprocessingml.document | Microsoft Word .docx-indelingsdocument.

### <a name="html-support"></a>HTML-ondersteuning

| HTML | Ondersteunde inhoud |
| --------- | ----------- |
| Tekenstijlen | Vet, Cursief, Onderstrepen, Code, Doorhalen, Superscript, Subscript |
| Niet-geordende lijsten | Schijf, Cirkel, Vierkant |
| Geordende lijsten | Decimaal, Upper-Alpha, Lower-Alpha, Upper-Roman, Lower-Roman |
| Hyperlinks | Binnenkort beschikbaar |

Niet-ondersteunde tags worden vergelijkbaar weergegeven. Afbeeldingen en tabellen worden momenteel niet ondersteund.

### <a name="options"></a>Opties

Bevat eigenschappen die bepaalde gedragingen van de meeslepende lezer configureren.

```typescript
{
    uiLang?: string;           // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;          // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number;         // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean;      // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
    onExit?: () => any;        // Executes when the Immersive Reader exits
    customDomain?: string;     // Reserved for internal use. Custom domain where the Immersive Reader webapp is hosted (default is null).
    allowFullscreen?: boolean; // The ability to toggle fullscreen (default is true).
    hideExitButton?: boolean;  // Whether or not to hide the Immersive Reader's exit button arrow (default is false). This should only be true if there is an alternative mechanism provided to exit the Immersive Reader (e.g a mobile toolbar's back arrow).
    cookiePolicy?: CookiePolicy; // Setting for the Immersive Reader's cookie usage (default is CookiePolicy.Disable). It's the responsibility of the host application to obtain any necessary user consent in accordance with EU Cookie Compliance Policy.
}
```

### <a name="renderbuttonsoptions"></a>Opties voor renderknoppen

Opties voor het renderen van de knoppen Immersive Reader.

```typescript
{
    elements: HTMLDivElement[];    // Elements to render the Immersive Reader buttons in
}
```

### <a name="error"></a>Fout

Bevat informatie over de fout.

```typescript
{
    code: string;    // One of a set of error codes
    message: string; // Human-readable representation of the error
}
```

#### <a name="error-codes"></a>Foutcodes

| Code | Beschrijving |
| ---- | ----------- |
| BadArgument (BadArgument) | Geleverd argument is ongeldig, zie `message` voor details. |
| Time-out | De immersive reader kan niet worden geladen binnen de opgegeven time-out. |
| TokenExpired | Het meegeleverde token is verlopen. |
| Smoorklep | De gesprekslimiet is overschreden. |

## <a name="launching-the-immersive-reader"></a>Lancering van de meeslepende lezer

De SDK biedt standaard styling voor de knop voor het starten van de Immersive Reader. Gebruik `immersive-reader-button` het kenmerk klasse om deze styling in te schakelen. Zie [dit artikel](./how-to-customize-launch-button.md) voor meer details.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Optionele kenmerken

Gebruik de volgende kenmerken om het uiterlijk van de knop te configureren.

| Kenmerk | Beschrijving |
| --------- | ----------- |
| `data-button-style` | Hiermee stelt u de stijl van de knop in. Kan `icon`worden `text`, `iconAndText`, of . Standaard instellingen `icon`voor . |
| `data-locale` | Hiermee stelt u de landint in. Bijvoorbeeld `en-US` of `fr-FR`. Standaardwaarden in `en`het Engels . |
| `data-icon-px-size` | Hiermee stelt u de grootte van het pictogram in pixels in. Standaard naar 20px. |

## <a name="browser-support"></a>Browserondersteuning

Gebruik de meest recente versies van de volgende browsers voor de beste ervaring met de Immersive Reader.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>Volgende stappen

* Ontdek de [Immersive Reader SDK op GitHub](https://github.com/microsoft/immersive-reader-sdk)
* [Snelstart: maak een web-app die de Immersive Reader (C#)](./quickstart.md)
