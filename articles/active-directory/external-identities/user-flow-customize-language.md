---
title: Taal aanpassing in azure AD-gebruikers stromen
description: Meer informatie over het aanpassen van de taal ervaring in uw gebruikers stromen.
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/06/2020
ms.author: mimart
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: abd6423ebbdba11cd1b0e0c2d00cfd36aa745e72
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87908493"
---
# <a name="language-customization-in-azure-active-directory-preview"></a>Taal aanpassing in Azure Active Directory (preview-versie)

> [!NOTE]
> Aanmelden via self-service is een open bare preview-functie van Azure Active Directory. Zie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over Previews.

Door de taal aanpassing in Azure Active Directory (Azure AD) kan uw gebruikers stroom voorzien van verschillende talen die aan de behoeften van uw gebruiker voldoen. Micro soft biedt de vertalingen voor [36 talen](#supported-languages). Zelfs als uw ervaring slechts voor één taal wordt gegeven, kunt u de kenmerk namen aanpassen op de pagina kenmerk verzameling.

## <a name="how-language-customization-works"></a>De werking van taal aanpassing

Taal aanpassing is standaard ingeschakeld voor gebruikers die zich aanmelden om een consistente registratie-ervaring te garanderen. U kunt talen gebruiken om de teken reeksen te wijzigen die worden weer gegeven aan gebruikers als onderdeel van het kenmerk verzamelings proces tijdens het aanmelden.

> [!NOTE]
> Als u aangepaste gebruikers kenmerken gebruikt, moet u uw eigen vertalingen opgeven. Zie [uw teken reeksen aanpassen](#customize-your-strings)voor meer informatie.

## <a name="customize-your-strings"></a>Uw teken reeksen aanpassen

Door taal aanpassing kunt u een wille keurige teken reeks in uw gebruikers stroom aanpassen.

1. Meld u als een Azure AD-administrator aan bij de [Azure Portal](https://portal.azure.com).
2. Onder **Azure-Services**selecteert u **Azure Active Directory**.
3. Selecteer in het linkermenu **externe identiteiten**.
4. **Gebruikers stromen selecteren (preview-versie)**.
3. Selecteer de gebruikers stroom die u wilt inschakelen voor vertalingen.
4. Selecteer **talen**.
5. Selecteer op de pagina **talen** voor de gebruikers stroom de taal die u wilt aanpassen.
6. Vouw de **pagina kenmerk verzameling**uit.
7. Selecteer **standaard waarden downloaden** (of **down loads negeren** als u deze taal eerder hebt bewerkt).

Deze stappen geven u een JSON-bestand dat u kunt gebruiken om te beginnen met het bewerken van uw teken reeksen.

### <a name="change-any-string-on-the-page"></a>Een wille keurige teken reeks op de pagina wijzigen

1. Open het JSON-bestand dat u hebt gedownload van de vorige instructies in een JSON-editor.
1. Zoek het element dat u wilt wijzigen. U kunt zoeken naar `StringId` de teken reeks die u zoekt, of zoeken naar het `Value` kenmerk dat u wilt wijzigen.
1. Werk het `Value` kenmerk bij met wat u wilt weer geven.
1. Voor elke teken reeks die u wilt wijzigen in, wijzigt u `Override` in `true` .
1. Sla het bestand op en upload uw wijzigingen. (U kunt het upload beheer vinden op dezelfde locatie als waar u het JSON-bestand hebt gedownload.)

> [!IMPORTANT]
> Als u een teken reeks moet overschrijven, moet u ervoor zorgen dat u de `Override` waarde instelt op `true` . Als de waarde niet wordt gewijzigd, wordt de vermelding genegeerd.

### <a name="change-extension-attributes"></a>Extensie kenmerken wijzigen

Als u de teken reeks voor een aangepast gebruikers kenmerk wilt wijzigen of een wilt toevoegen aan de JSON, heeft dit de volgende notatie:

```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

Vervang door `<ExtensionAttribute>` de naam van uw aangepaste gebruikers kenmerk.

Vervang door `<ExtensionAttributeValue>` de nieuwe teken reeks die moet worden weer gegeven.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Een lijst met waarden opgeven met behulp van LocalizedCollections

Als u een lijst met waarden voor antwoorden wilt opgeven, moet u een `LocalizedCollections` kenmerk maken. `LocalizedCollections`is een matrix van `Name` en- `Value` paren. De volg orde van de items is de volg orde waarin ze worden weer gegeven. Gebruik de volgende indeling om toe te voegen `LocalizedCollections` :

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId`is het gebruikers kenmerk waarvan dit `LocalizedCollections` kenmerk een antwoord is.
* `Name`is de waarde die wordt weer gegeven voor de gebruiker.
* `Value`is wat wordt geretourneerd in de claim wanneer deze optie is geselecteerd.

### <a name="upload-your-changes"></a>Uw wijzigingen uploaden

1. Nadat u de wijzigingen in het JSON-bestand hebt voltooid, gaat u terug naar de Tenant.
1. Selecteer **gebruikers stromen** en klik op de gebruikers stroom die u wilt inschakelen voor vertalingen.
1. Selecteer **talen**.
1. Selecteer de taal waarin u wilt vertalen.
1. Selecteer de **pagina kenmerk verzameling**.
1. Selecteer het mappictogram en selecteer het JSON-bestand dat u wilt uploaden.

De wijzigingen worden automatisch opgeslagen in de stroom van uw gebruikers.

## <a name="additional-information"></a>Aanvullende informatie

### <a name="page-ui-customization-labels-as-overrides"></a>Labels voor pagina-UI aanpassen als onderdrukkingen

Wanneer u taal aanpassing inschakelt, worden uw vorige wijzigingen voor labels met de aanpassing van de pagina-UI persistent gemaakt in een JSON-bestand voor Engels (en). U kunt door gaan met het wijzigen van uw labels en andere teken reeksen door taal resources te uploaden bij het aanpassen van de taal.

### <a name="up-to-date-translations"></a>Up-to-date vertalingen

Micro soft streeft ernaar de meest recente vertalingen te leveren voor uw gebruik. Micro soft verbetert de vertalingen voortdurend en houdt ze op de naleving voor u. Micro soft identificeert bugs en wijzigingen in globale terminologie en maakt updates die naadloos werken in uw gebruikers stroom.

### <a name="support-for-right-to-left-languages"></a>Ondersteuning voor talen die van rechts naar links worden geschreven

Micro soft biedt momenteel geen ondersteuning voor talen die van rechts naar links worden geschreven. U kunt dit doen door aangepaste land instellingen te gebruiken en CSS te gebruiken voor het wijzigen van de manier waarop de teken reeksen worden weer gegeven. Als u deze functie nodig hebt, kunt u hieraan stemmen voor [Azure feedback](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Vertalingen van de sociale ID-provider

Micro soft biedt de `ui_locales` para meter OIDC aan sociale aanmeldingen. Maar sommige aanbieders van sociale identiteiten, waaronder Facebook en Google, voldoen niet aan deze providers.

### <a name="browser-behavior"></a>Browser gedrag

Chrome en Firefox hebben beide aanvragen voor de ingestelde taal. Als het een ondersteunde taal is, wordt deze weer gegeven vóór de standaard waarde. Micro soft Edge vraagt momenteel geen taal aan en gaat rechtstreeks naar de standaard taal.

## <a name="supported-languages"></a>Ondersteunde talen

Azure AD biedt ondersteuning voor de volgende talen. De talen van de gebruikers stroom worden geleverd door Azure AD. De multi-factor Authentication (MFA)-meldingen talen worden door [Azure MFA](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)verschaft.

| Taal              | Taalcode | Gebruikersstromen         | MFA-meldingen  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Arabisch                | p.a.            | ![nee](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Bulgaars             | bg            | ![nee](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Bengalese                | bn            | ![ja](./media/user-flow-customize-language/yes.png) | ![nee](./media/user-flow-customize-language/no.png) |
| Catalaans               | certificering            | ![nee](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Tsjechisch                 | Support            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Deens                | da            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Duits                | de            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Grieks                 | ei            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Engels               | nl            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Spaans               | Ja            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Ests              | et            | ![nee](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Baskisch                | EU            | ![nee](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Fins               | fi            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Frans                | fr            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Galicisch              | boekhoud            | ![nee](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Gujarati              | gu            | ![ja](./media/user-flow-customize-language/yes.png) | ![nee](./media/user-flow-customize-language/no.png) |
| Hebreeuws                | Hij            | ![nee](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Hindi                 | Hallo            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Kroatisch              | uur            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Hongaars             | hu            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Indonesisch            | id            | ![nee](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Italiaans               | it            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Japans              | ja            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Kazachs                | kk            | ![nee](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Kannada               | kn            | ![ja](./media/user-flow-customize-language/yes.png) | ![nee](./media/user-flow-customize-language/no.png) |
| Koreaans                | ko            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Litouws            | lt            | ![nee](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Lets               | LV            | ![nee](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Malajalam             | ml            | ![ja](./media/user-flow-customize-language/yes.png) | ![nee](./media/user-flow-customize-language/no.png) |
| Marathi               | Dhr            | ![ja](./media/user-flow-customize-language/yes.png) | ![nee](./media/user-flow-customize-language/no.png) |
| Maleisisch                 | Mevrouw            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Noors Bokmål      | NB            | ![ja](./media/user-flow-customize-language/yes.png) | ![nee](./media/user-flow-customize-language/no.png) |
| Nederlands                 | nl            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Noors             | nee            | ![nee](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Punjabi               | PA            | ![ja](./media/user-flow-customize-language/yes.png) | ![nee](./media/user-flow-customize-language/no.png) |
| Pools                | pl            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Portuguese - Brazil   | pt-br         | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Portugees - Portugal | pt-pt         | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Roemeens              | Roll            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Russisch               | ru            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Slowaaks                | SK            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Sloveens             | lineaire            | ![nee](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Servisch - Cyrillisch    | SR-Cryl-CS    | ![nee](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Servisch - Latijn       | SR-latn-cs    | ![nee](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Zweeds               | sv            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Tamil                 | &            | ![ja](./media/user-flow-customize-language/yes.png) | ![nee](./media/user-flow-customize-language/no.png) |
| Telugu                | Ken            | ![ja](./media/user-flow-customize-language/yes.png) | ![nee](./media/user-flow-customize-language/no.png) |
| Thai                  | e            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Turks               | formulieren            | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Oekraïens             | rijk            | ![nee](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Vietnamees            | beschreven            | ![nee](./media/user-flow-customize-language/no.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Chinese - vereenvoudigd  | zh-Hans       | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |
| Chinese - traditioneel | zh-hant       | ![ja](./media/user-flow-customize-language/yes.png) | ![ja](./media/user-flow-customize-language/yes.png) |