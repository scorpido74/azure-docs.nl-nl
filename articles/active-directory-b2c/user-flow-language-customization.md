---
title: Taalaanpassing in Azure Active Directory B2C
description: Meer informatie over het aanpassen van de taalervaring in uw gebruikersstromen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5b3af812b2b78c276b5345b9b19226e6e1dba80b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185757"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Taalaanpassing in Azure Active Directory B2C

Met het aanpassen van talen in Azure Active Directory B2C (Azure AD B2C) kan uw gebruikersstroom aan verschillende talen voldoen die aan de behoeften van uw klant voldoen. Microsoft biedt de vertalingen voor [36 talen,](#supported-languages)maar u ook uw eigen vertalingen voor elke taal. Zelfs als uw ervaring slechts voor één taal is bedoeld, u elke tekst op de pagina's aanpassen.

## <a name="how-language-customization-works"></a>Hoe taalaanpassing werkt

U gebruikt taalaanpassing om te selecteren in welke talen uw gebruikersstroom beschikbaar is. Nadat de functie is ingeschakeld, u `ui_locales`de parameter querytekenreeks en de queryreeks opgeven vanuit uw toepassing. Wanneer u een aanmelding doet in Azure AD B2C, wordt uw pagina vertaald naar de landinplaats die u hebt aangegeven. Dit type configuratie geeft u volledige controle over de talen in uw gebruikersstroom en negeert de taalinstellingen van de browser van de klant.

U hebt mogelijk niet dat niveau van controle nodig over welke talen uw klant ziet. Als u geen `ui_locales` parameter verstrekt, wordt de ervaring van de klant bepaald door de instellingen van de browser. U nog steeds bepalen naar welke talen uw gebruikersstroom wordt vertaald door deze toe te voegen als een ondersteunde taal. Als de browser van een klant is ingesteld om een taal weer te geven die u niet wilt ondersteunen, wordt in plaats daarvan de taal weergegeven die u als standaard hebt geselecteerd in ondersteunde culturen.

* **ui-landtaal:** Nadat u taalaanpassing hebt ingeschakeld, wordt uw gebruikersstroom vertaald naar de taal die hier is opgegeven.
* **Browsergevraagde taal:** Als `ui_locales` er geen parameter is opgegeven, wordt uw gebruikersstroom vertaald naar de door de browser gevraagde taal, *als de taal wordt ondersteund.*
* **Standaardtaal voor beleid**: Als de browser geen taal opgeeft of als deze een taal opgeeft die niet wordt ondersteund, wordt de gebruikersstroom vertaald naar de standaardtaal van de gebruikersstroom.

> [!NOTE]
> Als u aangepaste gebruikerskenmerken gebruikt, moet u uw eigen vertalingen verstrekken. Zie [Uw tekenreeksen aanpassen](#customize-your-strings)voor meer informatie.

## <a name="support-requested-languages-for-ui_locales"></a>Gevraagde talen voor ondersteuning voor ui_locales

Beleid dat is gemaakt vóór de algemene beschikbaarheid van taalaanpassing, moet deze functie eerst inschakelen. Beleidsregels en gebruikersstromen die zijn gemaakt nadat taalaanpassing standaard is ingeschakeld.

Wanneer u taalaanpassing inschakelt op een gebruikersstroom, u `ui_locales` de taal van de gebruikersstroom beheren door de parameter toe te voegen.

1. Selecteer in uw Azure AD B2C-tenant **gebruikersstromen**.
1. Klik op de gebruikersstroom die u wilt inschakelen voor vertalingen.
1. Selecteer **Talen**.
1. Selecteer **Taalaanpassing inschakelen**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Selecteren welke talen in uw gebruikersstroom zijn ingeschakeld

Schakel een set talen in waarnaar uw gebruikersstroom wordt vertaald `ui_locales` wanneer de browser daarom vraagt zonder de parameter.

1. Controleer of uw gebruikersstroom taalaanpassing heeft ingeschakeld op basis van eerdere instructies.
1. Selecteer **op** de pagina Talen voor de gebruikersstroom een taal die u wilt ondersteunen.
1. Wijzig **ingeschakeld** in het deelvenster Eigenschappen in **Ja**.
1. Selecteer **Opslaan** boven aan het deelvenster Eigenschappen.

>[!NOTE]
>Als `ui_locales` er geen parameter wordt opgegeven, wordt de pagina alleen vertaald naar de browsertaal van de klant als deze is ingeschakeld.
>

## <a name="customize-your-strings"></a>Uw tekenreeksen aanpassen

Met taalaanpassing u elke tekenreeks in uw gebruikersstroom aanpassen.

1. Controleer of uw gebruikersstroom taalaanpassing heeft ingeschakeld uit de vorige instructies.
1. Selecteer **op** de pagina Talen voor de gebruikersstroom de taal die u wilt aanpassen.
1. Selecteer onder **bestanden met paginaniveau bronnen**de pagina die u wilt bewerken.
1. Selecteer **Standaardinstellingen downloaden** (of **Overschrijft downloaden** als u deze taal eerder hebt bewerkt).

Met deze stappen u een JSON-bestand gebruiken waarmee u beginnen met het bewerken van uw tekenreeksen.

### <a name="change-any-string-on-the-page"></a>Een tekenreeks op de pagina wijzigen

1. Open het JSON-bestand dat is gedownload van eerdere instructies in een JSON-editor.
1. Zoek het element dat u wilt wijzigen. U kunt `StringId` voor de tekenreeks die u zoekt `Value` vinden of zoeken naar het kenmerk dat u wilt wijzigen.
1. Werk `Value` het kenmerk bij met wat u wilt weergeven.
1. Voor elke tekenreeks die u `Override` wilt `true`wijzigen, verandert u in .
1. Sla het bestand op en upload uw wijzigingen. (U het uploadbesturingselement vinden op dezelfde plaats als waar u het JSON-bestand hebt gedownload.)

> [!IMPORTANT]
> Als u een tekenreeks moet overschrijven, `Override` moet `true`u de waarde instellen op . Als de waarde niet wordt gewijzigd, wordt de vermelding genegeerd.

### <a name="change-extension-attributes"></a>Extensiekenmerken wijzigen

Als u de tekenreeks voor een aangepast gebruikerskenmerk wilt wijzigen of als u er een wilt toevoegen aan de JSON, is deze in de volgende indeling:

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

Vervang `<ExtensionAttribute>` door de naam van uw aangepaste gebruikerskenmerk.

Vervang `<ExtensionAttributeValue>` door de nieuwe tekenreeks die moet worden weergegeven.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Een lijst met waarden opgeven met gelokaliseerde collecties

Als u een setlijst met waarden voor reacties wilt `LocalizedCollections` opgeven, moet u een kenmerk maken. `LocalizedCollections`is een `Name` array `Value` van en paren. De volgorde voor de artikelen is de volgorde waarin ze worden weergegeven. Als `LocalizedCollections`u wilt toevoegen, gebruikt u de volgende indeling:

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

* `ElementId`is het kenmerk `LocalizedCollections` van de gebruiker waarop dit kenmerk een antwoord is.
* `Name`is de waarde die aan de gebruiker wordt getoond.
* `Value`is wat wordt geretourneerd in de claim wanneer deze optie is geselecteerd.

### <a name="upload-your-changes"></a>Uw wijzigingen uploaden

1. Nadat u de wijzigingen in uw JSON-bestand hebt voltooid, gaat u terug naar uw B2C-tenant.
1. Selecteer **Gebruikersstromen** en klik op de gebruikersstroom die u wilt inschakelen voor vertalingen.
1. Selecteer **Talen**.
1. Selecteer de taal naar wie u wilt vertalen.
1. Selecteer de pagina waarop u vertalingen wilt verstrekken.
1. Selecteer het mappictogram en selecteer het JSON-bestand dat u wilt uploaden.

De wijzigingen worden automatisch opgeslagen in uw gebruikersstroom.

## <a name="customize-the-page-ui-by-using-language-customization"></a>De pagina-gebruikersinterface aanpassen met behulp van taalaanpassing

Er zijn twee manieren om uw HTML-inhoud te lokaliseren. Een manier is om [taalaanpassing](user-flow-language-customization.md)in te schakelen. Als u deze functie inschakelt, kan Azure AD `ui-locales`B2C de parameter OpenID Connect doorsturen naar uw eindpunt. Uw inhoudsserver kan deze parameter gebruiken om aangepaste HTML-pagina's op te geven die taalspecifiek zijn.

U ook inhoud uit verschillende plaatsen halen op basis van de landinplaats die wordt gebruikt. In het eindpunt met CORS u een mapstructuur instellen om inhoud voor specifieke talen te hosten. U belt de juiste als u de `{Culture:RFC5646}`waarde van de wildcard gebruikt. Stel dat dit uw aangepaste pagina URI is:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

U de `fr`pagina laden in. Wanneer de pagina HTML- en CSS-inhoud ophaalt, wordt deze verwijderd uit:

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Aangepaste talen toevoegen

U ook talen toevoegen waarvoor Microsoft momenteel geen vertalingen verstrekt. U moet de vertalingen voor alle tekenreeksen in de gebruikersstroom verstrekken. Taal- en landcodes zijn beperkt tot die in de ISO 639-1-standaard.

1. Selecteer in uw Azure AD B2C-tenant **gebruikersstromen**.
2. Klik op de gebruikersstroom waar u aangepaste talen wilt toevoegen en klik vervolgens op **Talen**.
3. Selecteer **Aangepaste taal toevoegen** boven aan de pagina.
4. Bepaal in het contextvenster dat wordt geopend welke taal u vertalingen verstrekt door een geldige landnummercode in te voeren.
5. Voor elke pagina u een set overschrijvingen voor Engels downloaden en aan de vertalingen werken.
6. Nadat u klaar bent met de JSON-bestanden, u ze voor elke pagina uploaden.
7. Selecteer **Inschakelen**en uw gebruikersstroom kan deze taal nu weergeven voor uw gebruikers.
8. Sla de taal op.

>[!IMPORTANT]
>U moet de aangepaste talen inschakelen of overschrijvingen uploaden voordat u opslaan.

## <a name="additional-information"></a>Aanvullende informatie

### <a name="page-ui-customization-labels-as-overrides"></a>Pagina-gebruikersinterface-aanpassingslabels als overschrijvingen

Wanneer u taalaanpassing inschakelt, blijven uw eerdere bewerkingen voor labels met behulp van aanpassing van de pagina-gebruikersinterface bestaan in een JSON-bestand voor Engels (nl). U uw labels en andere tekenreeksen blijven wijzigen door taalbronnen te uploaden in taalaanpassing.

### <a name="up-to-date-translations"></a>Up-to-date vertalingen

Microsoft streeft ernaar de meest up-to-date vertalingen voor uw gebruik te leveren. Microsoft verbetert voortdurend vertalingen en houdt ze voor u in overeenstemming. Microsoft identificeert bugs en wijzigingen in de algemene terminologie en brengt updates aan die naadloos werken in uw gebruikersstroom.

### <a name="support-for-right-to-left-languages"></a>Ondersteuning voor talen van rechts naar links

Microsoft biedt momenteel geen ondersteuning voor talen van rechts naar links. U dit bereiken door aangepaste landinstellingen te gebruiken en CSS te gebruiken om de weergave van de tekenreeksen te wijzigen. Als je deze functie nodig hebt, stem je ervoor op [Azure Feedback.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag)

### <a name="social-identity-provider-translations"></a>Vertalingen van sociale identiteitsaanbieders

Microsoft biedt `ui_locales` de OIDC-parameter aan sociale aanmeldingen. Maar sommige aanbieders van sociale identiteit, waaronder Facebook en Google, eren ze niet.

### <a name="browser-behavior"></a>Browsergedrag

Chrome en Firefox vragen allebei om hun ingestelde taal. Als het een ondersteunde taal is, wordt deze vóór de standaardinstelling weergegeven. Microsoft Edge vraagt momenteel geen taal aan en gaat rechtstreeks naar de standaardtaal.

## <a name="supported-languages"></a>Ondersteunde talen

Azure AD B2C bevat ondersteuning voor de volgende talen. Gebruikersstroomtalen worden geleverd door Azure AD B2C. De multi-factor authentication (MFA) meldingstalen worden geleverd door [Azure MFA](../active-directory/authentication/concept-mfa-howitworks.md).

| Taal              | Taalcode | Gebruikersstromen         | MFA-meldingen  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Arabisch                | Ar            | ![nee](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Bulgaars             | Bg            | ![nee](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Bangla                | Bn            | ![ja](./media/user-flow-language-customization/yes.png) | ![nee](./media/user-flow-language-customization/no.png) |
| Catalaans               | Ca            | ![nee](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Tsjechisch                 | Cs            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Deens                | da            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Duits                | de            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Grieks                 | El            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Engels               | nl            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Spaans               | Ja            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Ests              | Et            | ![nee](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Baskisch                | Eu            | ![nee](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Fins               | fi            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Frans                | fr            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Galicisch              | Gl            | ![nee](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Gujarati              | Gu            | ![ja](./media/user-flow-language-customization/yes.png) | ![nee](./media/user-flow-language-customization/no.png) |
| Hebreeuws                | Hge            | ![nee](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Hindi                 | Hallo            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Kroatisch              | uur            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Hongaars             | Hu            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Indonesisch            | id            | ![nee](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Italiaans               | it            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Japans              | ja            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Kazachs                | Kk            | ![nee](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Kannada               | Kn            | ![ja](./media/user-flow-language-customization/yes.png) | ![nee](./media/user-flow-language-customization/no.png) |
| Koreaans                | Ko            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Litouws            | Lt            | ![nee](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Lets               | Lv            | ![nee](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Malajalam             | Ml            | ![ja](./media/user-flow-language-customization/yes.png) | ![nee](./media/user-flow-language-customization/no.png) |
| Marathi               | Heer            | ![ja](./media/user-flow-language-customization/yes.png) | ![nee](./media/user-flow-language-customization/no.png) |
| Maleisisch                 | Mevrouw            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Noorse Bokmal      | nb nb            | ![ja](./media/user-flow-language-customization/yes.png) | ![nee](./media/user-flow-language-customization/no.png) |
| Nederlands                 | nl            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Norwegian             | nee            | ![nee](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Punjabi               | Pa            | ![ja](./media/user-flow-language-customization/yes.png) | ![nee](./media/user-flow-language-customization/no.png) |
| Pools                | Pl            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Portuguese - Brazil   | pt-br         | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Portugees - Portugal | pt-pt         | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Roemeens              | Ro            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Russisch               | ru            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Slowaaks                | Sk            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Sloveens             | Sl            | ![nee](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Servisch - Cyrillisch    | sr-cryl-cs    | ![nee](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Servisch - Latijn       | sr-latn-cs    | ![nee](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Zweeds               | sv            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Tamil                 | Ta            | ![ja](./media/user-flow-language-customization/yes.png) | ![nee](./media/user-flow-language-customization/no.png) |
| Telugu                | te            | ![ja](./media/user-flow-language-customization/yes.png) | ![nee](./media/user-flow-language-customization/no.png) |
| Thai                  | Th            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Turks               | Tr            | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Oekraïens             | Uk            | ![nee](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Vietnamees            | Vi            | ![nee](./media/user-flow-language-customization/no.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Chinees - Vereenvoudigd  | zh-hans zh-hans       | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
| Chinees - Traditioneel | zh-hant zh-hant       | ![ja](./media/user-flow-language-customization/yes.png) | ![ja](./media/user-flow-language-customization/yes.png) |
