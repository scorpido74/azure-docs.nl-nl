---
title: Problemen met het configureren van SSO van wacht woord voor een niet-galerie-apps
description: Veelvoorkomende problemen die zich voordoen wanneer u eenmalige aanmelding voor een wacht woord (SSO) configureert voor aangepaste apps die zich niet in de Azure AD-toepassings galerie bevinden.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9620a6ad584f20a0956e6a29c89609d79832f4d2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763445"
---
# <a name="problems-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Problemen met het configureren van eenmalige aanmelding met een wacht woord voor een toepassing buiten de galerie

In dit artikel worden veelvoorkomende problemen beschreven die zich kunnen voordoen wanneer u *eenmalige aanmelding* (SSO) voor een niet-galerij-app configureert.

## <a name="capture-sign-in-fields-for-an-app"></a>Aanmeldings velden vastleggen voor een app

Het vastleggen van het registratie veld wordt alleen ondersteund voor aanmeldings pagina's met HTML-functionaliteit. Het wordt niet ondersteund voor niet-standaard aanmeldings pagina's, zoals die voor Adobe Flash of andere technologieën zonder HTML-functionaliteit.

Er zijn twee manieren om aanmeldings velden voor uw aangepaste apps vast te leggen:

- **Automatische registratie van het registratie veld** werkt goed met de meeste HTML-aanmeldings pagina's, *als ze bekende div-id's gebruiken* voor de velden gebruikers naam en wacht woord. De HTML-code op de pagina wordt opgevallen om DIV-Id's te vinden die overeenkomen met bepaalde criteria. Die meta gegevens worden opgeslagen zodat deze later opnieuw kunnen worden afgespeeld naar de app.

- **Hand matige registratie van** het registratie veld wordt gebruikt als de leverancier *van de app geen label heeft voor de aanmeldings invoer velden*. Hand matige vastleg ging wordt ook gebruikt als de leverancier *meerdere velden rendert die niet automatisch kunnen worden gedetecteerd*. Met Azure Active Directory (Azure AD) kunnen gegevens worden opgeslagen voor net zoveel velden als op de aanmeldings pagina, als u het weet waar deze velden zich op de pagina bevinden.

Over het algemeen kunt u de optie hand matig gebruiken als het vastleggen van automatische aanmeldings velden niet werkt.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Aanmeldings velden automatisch vastleggen voor een app

Voer de volgende stappen uit om eenmalige aanmelding op basis van een wacht woord te configureren met behulp van automatische registratie van het registratie veld:

1. Open de [Azure Portal](https://portal.azure.com/). Meld u aan als globale beheerder of co-beheerder.

2. Selecteer in het navigatie deel venster aan de linkerkant **alle services** om de Azure AD-extensie te openen.

3. Typ **Azure Active Directory** in het vak Zoek opdracht filteren en selecteer vervolgens **Azure Active Directory**.

4. Selecteer **bedrijfs toepassingen** in het navigatie deel venster van Azure AD.

5. Selecteer **alle toepassingen** om een lijst met uw apps weer te geven.

   > [!NOTE]
   > Als u de gewenste app niet ziet, gebruikt u het **filter** besturings element boven aan de lijst **alle toepassingen** . Stel de optie **weer geven** in op alle toepassingen.

6. Selecteer de app die u wilt configureren voor eenmalige aanmelding.

7. Nadat de app is geladen, selecteert u **eenmalige aanmelding** in het navigatie deel venster aan de linkerkant.

8. Selecteer **aanmeldings modus op basis van wacht woorden** .

9. Voer de **aanmeldings-URL**in. Dit is de URL van de pagina waar gebruikers hun gebruikers naam en wacht woord invoeren om zich aan te melden. *Zorg ervoor dat de aanmeldings velden op de pagina worden weer gegeven voor de URL die u opgeeft*.

10. Selecteer **Opslaan**.

    De pagina wordt automatisch uitgewisseld voor de invoer vakken gebruikers naam en wacht woord. U kunt Azure AD nu gebruiken voor het veilig verzenden van wacht woorden naar die app met behulp van de browser uitbreiding van het toegangs venster.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Aanmeldings velden hand matig vastleggen voor een app

Als u hand matig aanmeldings velden wilt vastleggen, moet u de browser uitbreiding van het toegangs venster hebben geïnstalleerd. Uw browser kan ook niet worden uitgevoerd in de modus *InPrivate*, *incognito*of *Private* .

Zie de sectie [de browser uitbreiding van het toegangs venster installeren](#install-the-access-panel-browser-extension) in dit artikel voor informatie over het installeren van de uitbrei ding.

Voer de volgende stappen uit om SSO op basis van een wacht woord te configureren voor een app door gebruik te maken van hand matige registratie van het registratie veld:

1. Open de [Azure Portal](https://portal.azure.com/). Meld u aan als globale beheerder of co-beheerder.

2. Selecteer in het navigatie deel venster aan de linkerkant **alle services** om de Azure AD-extensie te openen.

3. Typ **Azure Active Directory** in het vak Zoek opdracht filteren en selecteer vervolgens **Azure Active Directory**.

4. Selecteer **bedrijfs toepassingen** in het navigatie deel venster van Azure AD.

5. Selecteer **alle toepassingen** om een lijst met uw apps weer te geven.

   > [!NOTE] 
   > Als u de gewenste app niet ziet, gebruikt u het **filter** besturings element boven aan de lijst **alle toepassingen** . Stel de optie **weer geven** in op alle toepassingen.

6. Selecteer de app die u wilt configureren voor eenmalige aanmelding.

7. Nadat de app is geladen, selecteert u **eenmalige aanmelding** in het navigatie deel venster aan de linkerkant.

8. Selecteer **aanmeldings modus op basis van wacht woorden** .

9. Voer de **aanmeldings-URL**in. Dit is de pagina waar gebruikers hun gebruikers naam en wacht woord invoeren om zich aan te melden. *Zorg ervoor dat de aanmeldings velden op de pagina worden weer gegeven voor de URL die u opgeeft*.

10. Selecteer **instellingen * &lt; &gt; * voor wacht woord voor eenmalige aanmelding configureren**.

11. Selecteer **aanmeldings velden hand matig detecteren**.

14. Selecteer **OK**.

15. Selecteer **Opslaan**.

16. Volg de instructies voor het gebruik van het toegangs venster.

## <a name="troubleshoot-problems"></a>Problemen oplossen

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Ik krijg de fout ' kan geen aanmeldings velden vinden bij die URL '

Dit fout bericht wordt weer gegeven wanneer automatische detectie van aanmeldings velden mislukt. Om het probleem op te lossen, kunt u hand matig de detectie van het aanmeldings veld proberen. Zie de [aanmeldings velden hand matig vastleggen voor een toepassings](#manually-capture-sign-in-fields-for-an-app) sectie van dit artikel.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>Ik krijg de fout ' kan de configuratie voor eenmalige aanmelding niet opslaan '

Zelden: het bijwerken van de SSO-configuratie mislukt. Sla de configuratie opnieuw op om dit probleem op te lossen.

Als u de fout blijft ontvangen, opent u een ondersteunings aanvraag. Neem de informatie op die wordt beschreven in de details van de [Portal-melding weer geven](#view-portal-notification-details) en [Verzend meldings gegevens naar een ondersteunings technicus om Help](#send-notification-details-to-a-support-engineer-to-get-help) -onderwerpen van dit artikel te verkrijgen.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Ik kan de aanmeldings velden voor mijn app niet hand matig detecteren

U kunt de volgende problemen waarnemen wanneer hand matige detectie niet werkt:

- Het hand matige vastleg proces leek te werken, maar de vastgelegde velden zijn niet juist.

- De juiste velden worden niet gemarkeerd wanneer het vastleg proces wordt uitgevoerd.

- Tijdens het vastleggen gaat u naar de aanmeldings pagina van de app zoals verwacht, maar er gebeurt niets.

- Hand matige vastleg ging lijkt te werken, maar SSO gebeurt niet wanneer gebruikers vanuit het toegangs venster naar de app navigeren.

Als u een van deze problemen ondervindt, doet u het volgende:

- Zorg ervoor dat u de nieuwste versie van de browser uitbreiding van het toegangs venster hebt *geïnstalleerd en ingeschakeld*. Zie de sectie [de browser uitbreiding van het toegangs venster installeren](#install-the-access-panel-browser-extension) van dit artikel.

- Zorg ervoor dat uw browser tijdens het vastleggen geen *incognito*, *InPrivate*of *Private* modus heeft. De uitbrei ding van het toegangs paneel wordt niet ondersteund in deze modus.

- Zorg ervoor dat uw gebruikers zich niet aanmelden bij de app vanuit het toegangs venster in *incognito*, *InPrivate*of *Private*.

- Probeer het hand matige vastleg proces opnieuw. Zorg ervoor dat de rode markeringen zich in de juiste velden bevinden.

- Als het hand matige vastleg proces niet meer reageert of als de aanmeldings pagina niet reageert, kunt u het hand matige vastleg proces opnieuw proberen. Maar deze keer, na het volt ooien van het proces, drukt u op de F12-toets om de ontwikkelaars console van uw browser te openen. Selecteer het tabblad **console** . Typ **Window. Location = "* &lt; de aanmeldings-URL die u hebt opgegeven bij het configureren van &gt; de app*"** en druk op ENTER. Hiermee wordt een omleiding van pagina's afgedwongen waarmee het proces wordt beëindigd en worden de opgenomen velden opgeslagen.

### <a name="contact-support"></a>Contact opnemen met de ondersteuning

Als u nog steeds problemen ondervindt, opent u een aanvraag met Microsoft Ondersteuning. Beschrijf wat u hebt geprobeerd. Neem de details op die worden beschreven in de details van de [Portal-melding weer geven](#view-portal-notification-details) en [Verzend meldings gegevens naar een ondersteunings technicus om Help](#send-notification-details-to-a-support-engineer-to-get-help) -onderwerpen van dit artikel te ontvangen (indien van toepassing).

## <a name="install-the-access-panel-browser-extension"></a>De browser uitbreiding van het toegangs venster installeren

Volg deze stappen:

1. Open [het toegangs venster](https://myapps.microsoft.com) in een ondersteunde browser. Meld u als *gebruiker*aan bij Azure AD.

2. Selecteer **wacht woord-SSO-toepassing** in het toegangs venster.

3. Wanneer u wordt gevraagd de software te installeren, selecteert u **nu installeren**.

4. U wordt omgeleid naar een download pagina voor uw browser. Kies voor het **toevoegen** van de extensie.

5. Als u hierom wordt gevraagd, selecteert u **inschakelen** of **toestaan**.

6. Nadat de installatie is gestart, start u de browser opnieuw.

7. Meld u aan bij het toegangs venster. Controleer of u uw apps met een wacht woord-SSO-functionaliteit kunt openen.

U kunt de browser uitbreiding voor Chrome en Firefox ook rechtstreeks downloaden via de volgende koppelingen:

-   [Uitbrei ding Chrome toegangs paneel](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensie voor het toegangs paneel van Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="view-portal-notification-details"></a>Details van portal melding weer geven

Voer de volgende stappen uit om de details van een portal melding te bekijken:

1. Selecteer het pictogram **meldingen** (de Bell) in de rechter bovenhoek van de Azure Portal.

2. Selecteer een melding waarin een *fout* status wordt weer gegeven. (Ze hebben een rode '! '.)

   > [!NOTE]
   > U kunt geen meldingen selecteren die de status *geslaagd* of *in uitvoering* hebben.

3. Het deel venster met de **meldings Details** wordt geopend. Lees de informatie voor meer informatie over het probleem.

5. Als u nog hulp nodig hebt, kunt u de informatie delen met een ondersteunings technicus of de product groep. Selecteer het **Kopieer** pictogram rechts van het vak **Kopieer fout** om de meldings details te kopiëren om te delen.

## <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Verzend Details van de melding naar een ondersteunings technicus om hulp te krijgen

Het is belang rijk dat u *alle* details die in deze sectie worden vermeld, deelt met ondersteuning zodat u snel aan de slag kunt. Als u deze wilt vastleggen, kunt u een scherm opname maken of een **Kopieer fout**selecteren.

De volgende informatie legt uit wat elk meldings item betekent en biedt voor beelden.

### <a name="essential-notification-items"></a>Essentiële meldings items

- **Titel**: de beschrijvende titel van de melding.

   Voor beeld: *proxy-instellingen* van de toepassing

- **Beschrijving**: wat is er gebeurd als gevolg van de bewerking.

   Voor beeld: de *ingevoerde interne URL wordt al gebruikt door een andere toepassing.*

- **Meldings-id**: de unieke id van de melding.

    Voor beeld: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **Client aanvraag-id**: de specifieke aanvraag-id die uw browser heeft gemaakt.

    Voor beeld: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **Tijds tempel UTC**: de tijds tempel van wanneer de melding is opgetreden, in UTC.

    Voor beeld: *2017-03-23T19:50:43.7583681 z*

- **Interne trans actie-id**: de interne id die wordt gebruikt om de fout op onze systemen op te zoeken.

    Voor beeld: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN**: de gebruiker die de bewerking heeft uitgevoerd.

    Voor beeld: *tperkins \@ f128.info*

- **Tenant-id**: de unieke id van de Tenant waarvan de gebruiker die de bewerking heeft uitgevoerd, lid is.

    Voor beeld: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **Gebruikers object-id**: de unieke id van de gebruiker die de bewerking heeft uitgevoerd.

    Voor beeld: *17f84be4-51f8-483a-b533-383791227a99*

### <a name="detailed-notification-items"></a>Gedetailleerde meldings items

- **Weergave naam**: (mag leeg zijn) een meer gedetailleerde weergave naam voor de fout.

    Voor beeld: *proxy-instellingen* van de toepassing

- **Status**: de specifieke status van de melding.

    Voor beeld: *mislukt*

- **Object-id**: (kan leeg zijn) de object-id waarmee de bewerking is uitgevoerd.

   Voor beeld: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Details**: de gedetailleerde beschrijving van wat er is gebeurd als gevolg van de bewerking.

    Voor beeld: *interne URL ' <https://bing.com/> ' is ongeldig omdat deze al in gebruik is.*

- **Kopieer fout**: Hiermee kunt u het **Kopieer pictogram** rechts van het tekstvak **Kopieer fout** selecteren om de details van de melding te kopiëren om de ondersteuning te helpen.

    Hierbij```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Volgende stappen
[Eenmalige aanmelding bieden voor uw apps met toepassings proxy](application-proxy-configure-single-sign-on-with-kcd.md)
