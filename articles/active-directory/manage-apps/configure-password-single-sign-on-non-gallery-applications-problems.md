---
title: Problemen met het configureren van wachtwoord SSO voor een niet-galerij-apps
description: Veelvoorkomende problemen die optreden wanneer u een malige aanmelding (SSO) voor wachtwoordinstellingen configureert voor aangepaste apps die zich niet in de Azure AD-toepassingsgalerie bevinden.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed8bafe7f5bc28cf37205107f8ab6dd5cdb4907c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274135"
---
# <a name="problems-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Problemen met het configureren van een enkele aanmelding voor een wachtwoordvoormelding voor een niet-galerietoepassing

In dit artikel worden veelvoorkomende problemen beschreven die kunnen optreden wanneer u *een sso (Password Single Sign-on)* configureert voor een niet-galerij-app.

## <a name="capture-sign-in-fields-for-an-app"></a>Aanmeldingsvelden vastleggen voor een app

Het vastleggen van aanmeldingsvelden wordt alleen ondersteund voor aanmeldingspagina's met HTML-ingeschakelde pagina's. Het wordt niet ondersteund voor niet-standaard aanmeldingspagina's, zoals pagina's die Adobe Flash of andere niet-HTML-technologieën gebruiken.

Er zijn twee manieren om aanmeldingsvelden vast te leggen voor uw aangepaste apps:

- **Automatische aanmeldingsveldopname** werkt goed bij de meeste html-inlogpagina's, *als ze bekende DIV-id's gebruiken* voor de gebruikersnaam- en wachtwoordvelden. De HTML op de pagina wordt geschraapt om DIV-id's te vinden die aan bepaalde criteria voldoen. Die metagegevens worden opgeslagen, zodat deze later opnieuw kunnen worden afgespeeld in de app.

- **Handmatige aanmeldingsveldopname** wordt gebruikt als de *app-leverancier de aanmeldingsinvoervelden niet labelt.* Handmatig vastleggen wordt ook gebruikt als de leverancier *meerdere velden rendert die niet automatisch kunnen worden gedetecteerd.* Azure Active Directory (Azure AD) kan gegevens opslaan voor zoveel velden als op de aanmeldingspagina, als u deze vertelt waar die velden zich op de pagina bevinden.

Als automatische aanmeldingsveldopname niet werkt, probeert u de handmatige optie.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Aanmeldingsvelden voor een app automatisch vastleggen

Voer de volgende stappen uit om sso op basis van wachtwoorden te configureren met behulp van automatische aanmeldingsveldopname:

1. Open de [Azure Portal](https://portal.azure.com/). Meld u aan als globale beheerder of co-beheerder.

2. Selecteer **alle services** om de Azure AD-extensie te openen in het navigatiedeelvenster aan de linkerkant.

3. Typ **Azure Active Directory** in het zoekvak van het filter en selecteer vervolgens Azure Active **Directory**.

4. Selecteer **Bedrijfstoepassingen** in het Azure AD-navigatiedeelvenster.

5. Selecteer **Alle toepassingen** om een lijst met uw apps weer te geven.

   > [!NOTE]
   > Als u de gewenste app niet ziet, gebruikt u het besturingselement **Filter** boven aan de lijst **Alle toepassingen.** Stel de optie **Weergeven** in op 'Alle toepassingen'.

6. Selecteer de app die u voor SSO wilt configureren.

7. Nadat de app is geladen, selecteert u **Eén aanmelding** in het navigatiedeelvenster aan de linkerkant.

8. Selecteer **de aanmeldingsmodus op basis van een wachtwoord.**

9. Voer de **aanmeldings-URL**in, de URL van de pagina waar gebruikers hun gebruikersnaam en wachtwoord invoeren om zich aan te melden. *Zorg ervoor dat de aanmeldingsvelden zichtbaar zijn op de pagina voor de URL die u opgeeft.*

10. Selecteer **Opslaan**.

    De pagina wordt automatisch geschraapt voor de invoervakken voor gebruikersnaam en wachtwoord. U Azure AD nu gebruiken om wachtwoorden veilig naar die app te verzenden met behulp van de browserextensie Access Panel.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Aanmeldingsvelden handmatig vastleggen voor een app

Als u aanmeldingsvelden handmatig wilt vastleggen, moet u de browserextensie Access Panel hebben geïnstalleerd. Uw browser kan ook niet worden uitgevoerd in *de inPrivate-,* *incognito-* of *privémodus.*

Zie de extensie [App-extensie Voor het installeren](#install-the-access-panel-browser-extension) van de extensie Van het toegangspaneel browser van dit artikel om de extensie te installeren.

Voer de volgende stappen uit om sso op basis van een wachtwoord voor een app te configureren met behulp van handmatige aanmeldingsveldopname:

1. Open de [Azure Portal](https://portal.azure.com/). Meld u aan als globale beheerder of co-beheerder.

2. Selecteer **alle services** om de Azure AD-extensie te openen in het navigatiedeelvenster aan de linkerkant.

3. Typ **Azure Active Directory** in het zoekvak van het filter en selecteer vervolgens Azure Active **Directory**.

4. Selecteer **Bedrijfstoepassingen** in het Azure AD-navigatiedeelvenster.

5. Selecteer **Alle toepassingen** om een lijst met uw apps weer te geven.

   > [!NOTE] 
   > Als u de gewenste app niet ziet, gebruikt u het besturingselement **Filter** boven aan de lijst **Alle toepassingen.** Stel de optie **Weergeven** in op 'Alle toepassingen'.

6. Selecteer de app die u voor SSO wilt configureren.

7. Nadat de app is geladen, selecteert u **Eén aanmelding** in het navigatiedeelvenster aan de linkerkant.

8. Selecteer **de aanmeldingsmodus op basis van een wachtwoord.**

9. Voer de **aanmeldings-URL**in, de pagina waarop gebruikers hun gebruikersnaam en wachtwoord invoeren om zich aan te melden. *Zorg ervoor dat de aanmeldingsvelden zichtbaar zijn op de pagina voor de URL die u opgeeft.*

10. Selecteer **Instellingen voor het configureren van * &lt;appnaam&gt; * Wachtwoord Met eenmalige aanmelding**.

11. Selecteer **Aanmeldingsvelden handmatig detecteren**.

14. Selecteer **Ok**.

15. Selecteer **Opslaan**.

16. Volg de instructies voor het gebruik van het Toegangspaneel.

## <a name="troubleshoot-problems"></a>Problemen oplossen

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Ik krijg een foutmelding 'We konden geen aanmeldingsvelden vinden op die URL'

U krijgt deze foutmelding wanneer automatische detectie van aanmeldingsvelden mislukt. Als u het probleem wilt oplossen, probeert u handmatige aanmeldingsvelddetectie. Zie de [aanmeldingsvelden handmatig vastleggen voor een toepassingssectie](#manually-capture-sign-in-fields-for-an-app) van dit artikel.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>Ik krijg een fout 'Kan geen enkele aanmeldingsconfiguratie opslaan'

Zelden mislukt het bijwerken van de SSO-configuratie. Als u dit probleem wilt oplossen, probeert u de configuratie opnieuw op te slaan.

Als u de fout blijft krijgen, opent u een ondersteuningsaanvraag. Voeg de informatie op die wordt beschreven in de [meldingsgegevens van](#view-portal-notification-details) de portal bekijken en [Stuur meldingsgegevens naar een ondersteuningstechnicus om hulpsecties](#send-notification-details-to-a-support-engineer-to-get-help) van dit artikel te ontvangen.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Ik kan aanmeldingsvelden voor mijn app niet handmatig detecteren

Mogelijk ziet u het volgende gedrag wanneer handmatige detectie niet werkt:

- Het handmatige opnameproces bleek te werken, maar de vastgelegde velden zijn niet correct.

- De juiste velden worden niet gemarkeerd wanneer het opnameproces wordt uitgevoerd.

- Het opnameproces brengt u naar de aanmeldingspagina van de app zoals verwacht, maar er gebeurt niets.

- Handmatig vastleggen lijkt te werken, maar SSO gebeurt niet wanneer gebruikers vanuit het Access Panel naar de app navigeren.

Als u een van deze problemen ondervindt, doe dan de volgende dingen:

- Zorg ervoor dat u de nieuwste versie van de browserextensie Access Panel *hebt geïnstalleerd en ingeschakeld.* Zie de sectie [Browserextensie Access Panel installeren](#install-the-access-panel-browser-extension) van dit artikel.

- Zorg ervoor dat uw browser tijdens het vastleggen niet in de *incognito-,* *inPrivate-* of *privémodus* staat. De extensie Access Panel wordt niet ondersteund in deze modi.

- Zorg ervoor dat uw gebruikers zich niet vanuit het Access Panel bij de app proberen aan te melden in de *incognito-,* *inPrivate-* of *privémodus*.

- Probeer het handmatige opnameproces opnieuw. Zorg ervoor dat de rode markeringen zich over de juiste velden bevinden.

- Als het handmatige vastleggen niet meer reageert of als de aanmeldingspagina niet reageert, probeert u het handmatige opnameproces opnieuw. Maar deze keer, na het voltooien van het proces, druk op de F12-toets om de ontwikkelaarsconsole van uw browser te openen. Selecteer het **tabblad console.** Typ **window.location="*&lt;de aanmeldings-URL&gt;die u hebt opgegeven bij het configureren van de app*"** en druk op Enter. Dit dwingt een pagina omleiding die het vastleggen proces eindigt en slaat de velden die zijn vastgelegd.

### <a name="contact-support"></a>Contact opnemen met ondersteuning

Als u nog steeds problemen hebt, opent u een aanvraag met Microsoft Support. Beschrijf wat je hebt geprobeerd. Voeg de details toe die worden beschreven in de [meldingsgegevens van](#view-portal-notification-details) de portal bekijken en [Stuur meldingsgegevens naar een ondersteuningstechnicus om helpsecties](#send-notification-details-to-a-support-engineer-to-get-help) van dit artikel te ontvangen (indien van toepassing).

## <a name="install-the-access-panel-browser-extension"></a>De browserextensie Access Panel installeren

Volg deze stappen:

1. Open [Access Panel](https://myapps.microsoft.com) in een ondersteunde browser. Meld u aan bij Azure AD als *gebruiker*.

2. Selecteer **de wachtwoord-SSO-toepassing** in het Access-paneel.

3. Wanneer u wordt gevraagd de software te installeren, selecteert u **Nu installeren**.

4. Je wordt doorgestuurd naar een downloadpagina voor je browser. Kies ervoor om de extensie toe te **voegen.**

5. Als u wordt gevraagd, selecteert u **Inschakelen** of **Toestaan**.

6. Start na de installatie de browser opnieuw op.

7. Meld u aan bij het toegangspaneel. Kijk of u uw apps met SSO-wachtwoord openen.

U de browserextensie voor Chrome en Firefox ook rechtstreeks downloaden via deze links:

-   [Chrome Access Panel-extensie](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox Access Panel-extensie](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="view-portal-notification-details"></a>Details van de portalmelding weergeven

Voer de volgende stappen uit om de details van een portalmelding te bekijken:

1. Selecteer het pictogram **Meldingen** (de bel) in de rechterbovenhoek van de Azure-portal.

2. Selecteer een melding met een *foutstatus.* (Ze hebben een rode "!".)

   > [!NOTE]
   > U geen meldingen selecteren die zich in de status *Geslaagd* of *In uitvoering bevinden.*

3. Het deelvenster **Meldingsdetails** wordt geopend. Lees de informatie om meer te weten te komen over het probleem.

5. Als u nog steeds hulp nodig hebt, deelt u de informatie met een ondersteuningstechnicus of de productgroep. Selecteer het **kopieerpictogram** rechts van het vak **Fout kopiëren** om de meldingsgegevens te kopiëren om te delen.

## <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Stuur meldingsgegevens naar een ondersteuningstechnicus om hulp te krijgen

Het is belangrijk dat u *alle* details die in deze sectie worden vermeld, deelt met ondersteuning, zodat ze u snel kunnen helpen. Als u deze wilt opnemen, u een schermafbeelding maken of **Fout kopiëren**selecteren.

In de volgende informatie wordt uitgelegd wat elk meldingsitem betekent en worden voorbeelden gegeven.

### <a name="essential-notification-items"></a>Essentiële meldingsitems

- **Benaming**: de beschrijvende titel van de kennisgeving.

   Voorbeeld: *Instellingen voor toepassingsproxy*

- **Beschrijving**: wat is er gebeurd als gevolg van de operatie.

   Voorbeeld: *de ingevoerde interne URL wordt al gebruikt door een andere toepassing.*

- **Meldings-ID**: de unieke id van de melding.

    Voorbeeld: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **Clientaanvraag-id:** de specifieke aanvraag-id die uw browser heeft gemaakt.

    Voorbeeld: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **Time Stamp UTC**: de tijdstempel van wanneer de melding plaatsvond, in UTC.

    Voorbeeld: *2017-03-23T19:50:43.7583681Z*

- **Interne transactie-ID:** de interne ID die wordt gebruikt om de fout in onze systemen op te zoeken.

    Voorbeeld: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN**: de gebruiker die de bewerking heeft uitgevoerd.

    Voorbeeld: *tperkins\@f128.info*

- **Tenant-id:** de unieke id van de tenant waarvan de gebruiker die de bewerking heeft uitgevoerd, lid is van.

    Voorbeeld: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **Gebruikersnaam- id:** de unieke id van de gebruiker die de bewerking heeft uitgevoerd.

    Voorbeeld: *17f84be4-51f8-483a-b533-383791227a99*

### <a name="detailed-notification-items"></a>Gedetailleerde kennisgevingsitems

- **Weergavenaam**: (kan leeg zijn) een meer gedetailleerde weergavenaam voor de fout.

    Voorbeeld: *Instellingen voor toepassingsproxy*

- **Status**: de specifieke status van de kennisgeving.

    Voorbeeld: *Mislukt*

- **Object-ID**: (kan leeg zijn) de object-ID waartegen de bewerking is uitgevoerd.

   Voorbeeld: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Bijzonderheden**: de gedetailleerde beschrijving van wat er als gevolg van de operatie is gebeurd.

    Voorbeeld: *Interne<https://bing.com/>url ' ' is ongeldig omdat deze al in gebruik is.*

- **Fout kopiëren:** hiermee u het **kopieerpictogram** rechts van het tekstvak **Met fout kopiëren** selecteren om de meldingsgegevens te kopiëren om te helpen bij de ondersteuning.

    Voorbeeld:```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Volgende stappen
[Eén aanmelding bij uw apps bieden met Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)
