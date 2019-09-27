---
title: Naslag Gids voor insluitende lezers SDK
titleSuffix: Azure Cognitive Services
description: Naslag informatie voor de insluitende lezer-SDK
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: b25a002cb1e2563ab97a2081c6b6a05362b66779
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338518"
---
# <a name="immersive-reader-sdk-reference"></a>Naslag Gids voor insluitende lezers SDK

De insluitende lezer SDK is een Java script-bibliotheek waarmee u de insluitende lezer kunt integreren in uw webtoepassing.

# <a name="functions"></a>Functies

De SDK biedt de volgende functies beschikbaar:

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>launchAsync

Hiermee wordt de insluitende lezer gestart binnen een `iframe` in uw webtoepassing.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<HTMLDivElement>;
```

### <a name="parameters"></a>Parameters

| Name | Type | Description |
| ---- | ---- |------------ |
| `token` | string | Het Azure AD-verificatie token. Zie de [instructies voor Azure AD-verificatie](./azure-active-directory-authentication.md). |
| `subdomain` | string | Het aangepaste subdomein van uw insluitende lezer-resource in Azure. Zie de [instructies voor Azure AD-verificatie](./azure-active-directory-authentication.md). |
| `content` | [Inhoud](#content) | Een object met de inhoud die in de insluitende lezer moet worden weer gegeven. |
| `options` | [Opties](#options) | Opties voor het configureren van bepaald gedrag van de insluitende lezer. Optioneel. |

### <a name="returns"></a>Retourneert

Retourneert een `Promise<HTMLDivElement>`, die wordt omgezet wanneer de insluitende lezer wordt geladen. De `Promise` wordt omgezet naar een `div`-element waarvan alleen een onderliggend element een `iframe`-onderdeel is dat de pagina voor de insluitende lezer bevat.

### <a name="exceptions"></a>Uitzonderingen

De geretourneerde `Promise` wordt afgewezen met een [`Error`-](#error) object als de insluitende lezer niet kan worden geladen. Zie de [fout codes](#error-codes)voor meer informatie.

## <a name="close"></a>sluiten

Hiermee wordt de insluitende lezer gesloten.

Een voor beeld van een use-case voor deze functie is als u de knop Afsluiten verborgen door ```hideExitButton: true``` in [Opties](#options)in te stellen. Vervolgens kan een andere knop (bijvoorbeeld de pijl-terug van een mobiele koptekst) deze ```close```-functie aanroepen wanneer erop wordt geklikt.

```typescript
close(): void;
```

## <a name="renderbuttons"></a>renderButtons

Deze functie stijlen en werkt de elementen van de insluitende lezer van het document bij. Als ```options.elements``` wordt gegeven, zal deze functie knoppen in ```options.elements``` weer geven. Anders worden de knoppen weer gegeven in de elementen van het document die de klasse ```immersive-reader-button``` hebben.

Deze functie wordt automatisch aangeroepen door de SDK wanneer het venster wordt geladen.

Zie [optionele kenmerken](#optional-attributes) voor meer rendering-opties.

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

### <a name="parameters"></a>Parameters

| Name | Type | Description |
| ---- | ---- |------------ |
| `options` | [RenderButtonsOptions](#renderbuttonsoptions) | Opties voor het configureren van bepaald gedrag van de functie renderButtons. Optioneel. |

## <a name="types"></a>Typen

### <a name="content"></a>Inhoud

Bevat de inhoud die in de insluitende lezer moet worden weer gegeven.

```typescript
{
    title?: string;    // Title text shown at the top of the Immersive Reader (optional)
    chunks: Chunk[];   // Array of chunks
}
```

### <a name="chunk"></a>Gedeelde

Eén gegevens segment dat wordt door gegeven aan de inhoud van de insluitende lezer.

```typescript
{
    content: string;        // Plain text string
    lang?: string;          // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
    mimeType?: string;      // MIME type of the content (optional). Currently 'text/plain', 'application/mathml+xml', and 'text/html' are supported. Defaults to 'text/plain' if not specified.
}
```

#### <a name="supported-mime-types"></a>Ondersteunde MIME-typen

| MIME-type | Description |
| --------- | ----------- |
| tekst/zonder opmaak | Tekst zonder opmaak. |
| tekst/HTML | HTML-inhoud. [Meer informatie](#html-support)|
| Application/MathML + XML | MathML (wiskundige Markup Language). [Meer informatie](https://developer.mozilla.org/en-US/docs/Web/MathML).
| application/vnd. openxmlformats-officedocument. WordprocessingML. document | Micro soft Word. docx-indelings document.

### <a name="html-support"></a>HTML-ondersteuning
| HTML-CODE | Ondersteunde inhoud |
| --------- | ----------- |
| Lettertype stijlen | Vet, cursief, onderstrepen, code, doorhaling, Super script |
| Niet-geordende lijsten | Schijf, cirkel, vier kant |
| Geordende lijsten | Decimaal, hoofd letter, kleine letter alfa, bovenste Romeins, kleine letter Romeins |
| Link | Binnenkort beschikbaar |

Niet-ondersteunde labels worden weer gegeven comparably. Afbeeldingen en tabellen worden momenteel niet ondersteund.

### <a name="options"></a>Opties

Bevat eigenschappen die bepaald gedrag van de insluitende lezer configureren.

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
}
```

### <a name="renderbuttonsoptions"></a>RenderButtonsOptions

Opties voor het weer geven van de knoppen voor insluitende lezer.

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

| Code | Description |
| ---- | ----------- |
| BadArgument | Het opgegeven argument is ongeldig. Zie `message` voor meer informatie. |
| Time-out | De insluitende lezer kan niet worden geladen binnen de opgegeven time-out. |
| TokenExpired | Het opgegeven token is verlopen. |
| Beperkt | De limiet voor de aanroep frequentie is overschreden. |

## <a name="launching-the-immersive-reader"></a>De insluitende lezer starten

De SDK biedt standaard stijlen voor de knop voor het starten van de insluitende lezer. Gebruik het klasse-kenmerk `immersive-reader-button` om deze stijl in te scha kelen.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Optionele kenmerken

Gebruik de volgende kenmerken om het uiterlijk van de knop te configureren.

| Kenmerk | Description |
| --------- | ----------- |
| `data-button-style` | Hiermee wordt de stijl van de knop ingesteld. `icon`Kan, `text`of. `iconAndText` Standaard ingesteld op `icon`. |
| `data-locale` | Hiermee stelt u de land instelling. Bijvoorbeeld, `en-US` of `fr-FR`. De standaard instelling is Engels `en`. |
| `data-icon-px-size` | Hiermee stelt u de grootte van het pictogram in pixels. De standaard waarde is 20px. |

## <a name="browser-support"></a>Browser ondersteuning

Gebruik de meest recente versies van de volgende browsers voor de beste ervaring met de insluitende lezer.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>Volgende stappen

* De [insluitende lezer-SDK op github](https://github.com/microsoft/immersive-reader-sdk) verkennen
* [Snelstart: Een web-app maken die de insluitende lezerC#() ](./quickstart.md) start