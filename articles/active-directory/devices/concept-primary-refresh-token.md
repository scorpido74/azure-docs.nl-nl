---
title: Primaire vernieuwingstoken (PRT) en Azure AD - Azure Active Directory
description: Wat is de rol van en hoe beheren we het Primary Refresh Token (PRT) in Azure Active Directory?
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a237ad35d9d5d8abee784926563d972d0ee95f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672635"
---
# <a name="what-is-a-primary-refresh-token"></a>Wat is een teken voor primaire vernieuwing?

Een Primary Refresh Token (PRT) is een belangrijk artefact van Azure AD-verificatie op Windows 10-, iOS- en Android-apparaten. Het is een JSON Web Token (JWT) speciaal uitgegeven aan Microsoft first party token brokers om single sign-on (SSO) in te schakelen in de toepassingen die op die apparaten worden gebruikt. In dit artikel geven we details over hoe een PRT wordt uitgegeven, gebruikt en beschermd op Windows 10-apparaten.

In dit artikel wordt ervan uitgegaan dat u de verschillende apparaatstatussen die beschikbaar zijn in Azure AD en hoe eenmalige aanmelding werkt in Windows 10 al begrijpt. Zie het artikel [Wat is apparaatbeheer in Azure Active Directory voor](overview.md) meer informatie over apparaten in Azure AD?

## <a name="key-terminology-and-components"></a>Belangrijkste terminologie en componenten

De volgende Windows-componenten spelen een belangrijke rol bij het aanvragen en gebruiken van een PRT:

* **Cloud Authentication Provider** (CloudAP): CloudAP is de moderne verificatieprovider voor Windows-aanmelding, die controleert of gebruikers zich aanmelden bij een Windows 10-apparaat. CloudAP biedt een plug-inframework waarop identiteitsproviders kunnen voortbouwen om verificatie naar Windows mogelijk te maken met behulp van de referenties van die identiteitsprovider.
* **Web Account Manager** (WAM): WAM is de standaard tokenbroker op Windows 10-apparaten. WAM biedt ook een plugin-framework waarop identiteitsproviders kunnen voortbouwen en SSO kunnen inschakelen voor hun toepassingen die afhankelijk zijn van die identiteitsprovider.
* **Azure AD CloudAP-plug-in**: een Azure AD-specifieke plug-in die is gebouwd op het CloudAP-framework, die gebruikersreferenties met Azure AD verifieert tijdens het aanmelden van Windows.
* **Azure AD WAM-plug-in**: een Azure AD-specifieke plug-in die is gebouwd op het WAM-framework, waarmee SSO kan worden gebruikt voor toepassingen die afhankelijk zijn van Azure AD voor verificatie.
* **Dsreg**: een Azure AD-specifieke component op Windows 10, die het apparaatregistratieproces voor alle apparaatstatussen verwerkt.
* **Trusted Platform Module** (TPM): Een TPM is een hardwarecomponent die in een apparaat is ingebouwd en die hardwarematige beveiligingsfuncties biedt voor gebruikers- en apparaatgeheimen. Meer details zijn te vinden in het artikel [Trusted Platform Module Technology Overview](/windows/security/information-protection/tpm/trusted-platform-module-overview).

## <a name="what-does-the-prt-contain"></a>Wat bevat de PRT?

Een PRT bevat claims die doorgaans zijn opgenomen in een Azure AD-vernieuwingstoken. Daarnaast zijn er een aantal apparaat-specifieke claims opgenomen in de PRT. De verschillen zijn als volgt:

* **Apparaat-ID**: Een PRT wordt uitgegeven aan een gebruiker op een specifiek apparaat. De apparaat-ID-claim `deviceID` bepaalt het apparaat waarop de PRT is uitgegeven aan de gebruiker. Deze claim wordt later uitgegeven aan tokens die via de PRT zijn verkregen. De apparaat-ID-claim wordt gebruikt om de autorisatie voor voorwaardelijke toegang te bepalen op basis van de apparaatstatus of -naleving.
* **Sessiesleutel**: De sessiesleutel is een versleutelde symmetrische sleutel die wordt gegenereerd door de Azure AD-verificatieservice die is uitgegeven als onderdeel van de PRT. De sessiesleutel fungeert als bewijs van bezit wanneer een PRT wordt gebruikt om tokens voor andere toepassingen te verkrijgen.

### <a name="can-i-see-whats-in-a-prt"></a>Kan ik zien wat er in een PRT zit?

Een PRT is een ondoorzichtige blob die vanuit Azure AD wordt verzonden waarvan de inhoud niet bekend is bij clientonderdelen. Je niet zien wat er in een PRT zit.

## <a name="how-is-a-prt-issued"></a>Hoe wordt een PRT uitgegeven?

Apparaatregistratie is een vereiste voor apparaatverificatie in Azure AD. Een PRT wordt alleen op geregistreerde apparaten aan gebruikers uitgegeven. Zie het artikel [Windows Hello voor Bedrijven en apparaatregistratie voor](/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration)meer diepgaande informatie over apparaatregistratie. Tijdens apparaatregistratie genereert de dsreg-component twee sets cryptografische sleutelparen:

* Apparaatsleutel (dkpub/dkpriv)
* Transportsleutel (tkpub/tkpriv)

De privésleutels zijn gebonden aan de TPM van het apparaat als het apparaat een geldige en werkende TPM heeft, terwijl de openbare sleutels tijdens het apparaatregistratieproces naar Azure AD worden verzonden. Deze sleutels worden gebruikt om de apparaatstatus te valideren tijdens PRT-aanvragen.

De PRT wordt uitgegeven tijdens gebruikersverificatie op een Windows 10-apparaat in twee scenario's:

* **Azure AD is lid geworden** of Hybride Azure AD is **lid**geworden: er wordt een PRT uitgegeven tijdens Windows-aanmelding wanneer een gebruiker zich aanmeldt met zijn organisatiereferenties. Er wordt een PRT uitgegeven met alle door Windows 10 ondersteunde referenties, bijvoorbeeld wachtwoord en Windows Hello for Business. In dit scenario is Azure AD CloudAP-plug-in de primaire autoriteit voor de PRT.
* **Azure AD-geregistreerd apparaat**: een PRT wordt uitgegeven wanneer een gebruiker een secundair werkaccount toevoegt aan zijn Windows 10-apparaat. Gebruikers kunnen een account op twee verschillende manieren aan Windows 10 toevoegen -  
   * Een account toevoegen via de prompt **gebruik dit account op dit apparaat** nadat u zich hebt aanmelden bij een app (bijvoorbeeld Outlook)
   * Een account toevoegen via **Instellingen** > **Accounts** > **Access Work of School** > **Connect**

In azure AD-beheerde apparaatscenario's is de Azure AD WAM-plug-in de primaire autoriteit voor de PRT, omdat Windows-aanmelding niet plaatsvindt met dit Azure AD-account.

> [!NOTE]
> Identiteitsproviders van derden moeten het WS-Trust-protocol ondersteunen om PRT-uitgifte op Windows 10-apparaten mogelijk te maken. Zonder WS-Trust kan PRT niet worden uitgegeven aan gebruikers op Hybrid Azure AD of Azure AD-apparaten

## <a name="what-is-the-lifetime-of-a-prt"></a>Wat is de levensduur van een PRT?

Eenmaal uitgegeven, is een PRT 14 dagen geldig en wordt continu vernieuwd zolang de gebruiker het apparaat actief gebruikt.  

## <a name="how-is-a-prt-used"></a>Hoe wordt een PRT gebruikt?

Een PRT wordt gebruikt door twee belangrijke componenten in Windows:

* **Azure AD CloudAP-plug-in**: Tijdens windows-aanmelding vraagt de Azure AD CloudAP-plug-in een PRT van Azure AD aan met behulp van de referenties die door de gebruiker zijn verstrekt. Het caches ook de PRT in te schakelen om in te loggen in de cache wanneer de gebruiker geen toegang tot een internetverbinding hebben.
* **Azure AD WAM-plug-in**: Wanneer gebruikers toegang proberen te krijgen tot toepassingen, gebruikt de Azure AD WAM-plug-in de PRT om SSO in Te schakelen op Windows 10. Azure AD WAM-plug-in gebruikt de PRT om vernieuwings- en toegangstokens aan te vragen voor toepassingen die afhankelijk zijn van WAM voor tokenaanvragen. Het maakt het ook mogelijk SSO op browsers door het injecteren van de PRT in browser verzoeken. Browser SSO in Windows 10 wordt ondersteund op Microsoft Edge (native) en Chrome (via de Windows 10-accounts of Office Online-extensie).

## <a name="how-is-a-prt-renewed"></a>Hoe wordt een PRT vernieuwd?

Een PRT wordt vernieuwd in twee verschillende methoden:

* **Azure AD CloudAP-plug-in om de 4 uur**: De CloudAP-plug-in vernieuwt de PRT elke 4 uur tijdens het aanmelden van Windows. Als de gebruiker gedurende die tijd geen internetverbinding heeft, verlengt de CloudAP-plug-in de PRT nadat het apparaat is verbonden met het internet.
* **Azure AD WAM-plug-in tijdens app-tokenaanvragen:** de WAM-plug-in maakt SSO op Windows 10-apparaten mogelijk door stille tokenaanvragen voor toepassingen in te schakelen. De WAM-plug-in kan de PRT tijdens deze tokenaanvragen op twee verschillende manieren vernieuwen:
   * Een app vraagt in stilte WAM om een toegangstoken, maar er is geen vernieuwingstoken beschikbaar voor die app. In dit geval gebruikt WAM de PRT om een token voor de app aan te vragen en krijgt het een nieuwe PRT terug in het antwoord.
   * Een app vraagt WAM aan voor een toegangstoken, maar het PRT is ongeldig of Azure AD vereist extra autorisatie (bijvoorbeeld Azure Multi-Factor Authentication). In dit scenario initieert WAM een interactieve aanmelding waarbij de gebruiker zich opnieuw moet verifiëren of aanvullende verificatie moet bieden en wordt een nieuwe PRT uitgegeven voor succesvolle verificatie.

### <a name="key-considerations"></a>Belangrijkste overwegingen

* Een PRT wordt alleen uitgegeven en vernieuwd tijdens native app-verificatie. Een PRT wordt niet verlengd of uitgegeven tijdens een browsersessie.
* In Azure AD samengevoegde en hybride Azure AD-apparaten is de CloudAP-plug-in de primaire autoriteit voor een PRT. Als een PRT wordt vernieuwd tijdens een op WAM gebaseerde tokenaanvraag, wordt de PRT teruggestuurd naar CloudAP-plug-in, die de geldigheid van de PRT met Azure AD verifieert voordat deze wordt geaccepteerd.

## <a name="how-is-the-prt-protected"></a>Hoe wordt de PRT beschermd?

Een PRT wordt beschermd door deze te binden aan het apparaat waarop de gebruiker zich heeft aangemeld. Azure AD en Windows 10 maken PRT-bescherming mogelijk via de volgende methoden:

* **Tijdens het eerste inloggen**: Tijdens het eerste inloggen wordt een PRT uitgegeven door het ondertekenen van aanvragen met behulp van de apparaatsleutel die cryptografisch wordt gegenereerd tijdens de registratie van het apparaat. Op een apparaat met een geldige en functionerende TPM wordt de apparaatsleutel beveiligd door de TPM om schadelijke toegang te voorkomen. Er wordt geen PRT uitgegeven als de bijbehorende handtekening van de apparaatsleutel niet kan worden gevalideerd.
* **Tijdens tokenaanvragen en verlenging:** Wanneer een PRT wordt uitgegeven, geeft Azure AD ook een versleutelde sessiesleutel uit aan het apparaat. Het is versleuteld met de ov-sleutel (tkpub) gegenereerd en verzonden naar Azure AD als onderdeel van apparaatregistratie. Deze sessiesleutel kan alleen worden gedecodeerd door de privétransportsleutel (tkpriv) die door de TPM is beveiligd. De sessiesleutel is de POP-toets (Proof-of-Possession) voor aanvragen die naar Azure AD worden verzonden.  De sessiesleutel wordt ook beschermd door de TPM en geen enkele andere OS-component heeft er toegang toe. Tokenverzoeken of PRT-vernieuwingsaanvragen worden veilig ondertekend door deze sessiesleutel via de TPM en kunnen daarom niet worden geknoeid. Azure AD maakt alle aanvragen van het apparaat ongeldig die niet zijn ondertekend door de bijbehorende sessiesleutel.

Door het beveiligen van deze sleutels met de TPM, kwaadaardige acteurs kunnen niet stelen van de toetsen, noch replay de PRT elders als de TPM is ontoegankelijk, zelfs als een aanvaller fysiek bezit van het apparaat heeft.  Het gebruik van een TPM verbetert dus de beveiliging van Azure AD Joined, Hybrid Azure AD en Azure AD-geregistreerde apparaten tegen diefstal van referenties. Voor prestaties en betrouwbaarheid is TPM 2.0 de aanbevolen versie voor alle Azure AD-apparaatregistratiescenario's op Windows 10.

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>Hoe worden app-tokens en browsercookies beschermd?

**App-tokens:** Wanneer een app token aanvraagt via WAM, geeft Azure AD een vernieuwingstoken en een toegangstoken uit. WAM retourneert echter alleen het toegangstoken naar de app en beveiligt het vernieuwingstoken in de cache door het te versleutelen met de DPAPI-toets (Data Protection Application Programming Interface) van de gebruiker. WAM gebruikt het vernieuwingstoken veilig door aanvragen te ondertekenen met de sessiesleutel om verdere toegangstokens uit te geven. De DPAPI-toets wordt beveiligd door een op Azure AD gebaseerde symmetrische sleutel in Azure AD zelf. Wanneer het apparaat het gebruikersprofiel moet decoderen met de DPAPI-sleutel, biedt Azure AD de DPAPI-sleutel die is versleuteld met de sessiesleutel, die CloudAP-plug-in TPM vraagt om te decoderen. Deze functionaliteit zorgt voor consistentie in het beveiligen van vernieuwingstokens en voorkomt dat toepassingen hun eigen beschermingsmechanismen implementeren.  

**Browsercookies**: In Windows 10 ondersteunt Azure AD browser SSO in Internet Explorer en Microsoft Edge native of in Google Chrome via de Windows 10-accountextensie. De beveiliging is niet alleen gebouwd om de cookies te beschermen, maar ook om de eindpunten waarnaar de cookies worden verzonden. Browsercookies worden op dezelfde manier beschermd als een PRT, door gebruik te maken van de sessiesleutel om de cookies te ondertekenen en te beschermen.

Wanneer een gebruiker een browserinteractie initieert, roept de browser (of extensie) een COM-native clienthost aan. De native clienthost zorgt ervoor dat de pagina afkomstig is van een van de toegestane domeinen. De browser kan andere parameters naar de native clienthost sturen, waaronder een nonce, maar de native clienthost garandeert validatie van de hostnaam. De native clienthost vraagt een PRT-cookie aan bij CloudAP-plug-in, die deze maakt en ondertekent met de door TPM beveiligde sessiesleutel. Aangezien de PRT-cookie wordt ondertekend door de sessiesleutel, kan er niet mee worden geknoeid. Deze PRT-cookie is opgenomen in de aanvraagheader voor Azure AD om het apparaat te valideren waar het vandaan komt. Als u de Chrome-browser gebruikt, kan alleen de extensie die expliciet is gedefinieerd in het manifest van de native clienthost, worden aanroepen om willekeurige extensies te verhinderen deze verzoeken te doen. Zodra Azure AD de PRT-cookie valideert, geeft deze een sessiecookie uit aan de browser. Deze sessiecookie bevat ook dezelfde sessiesleutel met een PRT. Tijdens volgende aanvragen wordt de sessiesleutel gevalideerd door de cookie effectief aan het apparaat te binden en herhalingen van elders te voorkomen.

## <a name="when-does-a-prt-get-an-mfa-claim"></a>Wanneer krijgt een PRT een MFA claim?

Een PRT kan een multi-factor authenticatie (MFA) claim krijgen in specifieke scenario's. Wanneer een MFA-gebaseerde PRT wordt gebruikt om tokens voor toepassingen aan te vragen, wordt de MFA-claim overgedragen naar die app-tokens. Deze functionaliteit biedt gebruikers een naadloze ervaring door voor elke app die deze nodig heeft een MFA-uitdaging te voorkomen. Een PRT kan een MFA-claim op de volgende manieren krijgen:

* **Meld u aan bij Windows Hello for Business:** Windows Hello for Business vervangt wachtwoorden en gebruikt cryptografische sleutels om sterke tweestapsverificatie te bieden. Windows Hello for Business is specifiek voor een gebruiker op een apparaat en vereist zelf MFA voor inlevering. Wanneer een gebruiker zich aanmeldt bij Windows Hello for Business, krijgt de PRT van de gebruiker een MFA-claim. Dit scenario is ook van toepassing op gebruikers die inloggen met smartcards als smartcardverificatie een MFA-claim van ADFS produceert.
   * Aangezien Windows Hello for Business wordt beschouwd als multi-factor authenticatie, wordt de MFA-claim bijgewerkt wanneer de PRT zelf wordt vernieuwd, zodat de MFA-duur voortdurend wordt verlengd wanneer gebruikers zich aanmelden bij WIndows Hello for Business
* **MFA tijdens WAM interactieve aanmelding**: Tijdens een tokenaanvraag via WAM, als een gebruiker MFA moet doen om toegang te krijgen tot de app, wordt de PRT die tijdens deze interactie wordt vernieuwd, bedrukt met een MFA-claim.
   * In dit geval wordt de MFA-claim niet continu bijgewerkt, dus de MFA-duur is gebaseerd op de levensduur die is ingesteld op de map.
   * Wanneer een eerdere bestaande PRT en RT worden gebruikt voor toegang tot een app, worden de PRT en RT beschouwd als het eerste bewijs van authenticatie. Een nieuwe AT zal nodig zijn met een tweede bewijs en een ingeprent MFA claim. Dit zal ook een nieuwe PRT en RT.
* **MFA tijdens apparaatregistratie:** Als een beheerder zijn apparaatinstellingen in Azure AD heeft geconfigureerd om [MFA te verplichten apparaten te registreren,](device-management-azure-portal.md#configure-device-settings)moet de gebruiker MFA doen om de registratie te voltooien. Tijdens dit proces heeft de PRT die aan de gebruiker wordt uitgegeven de MFA-claim verkregen tijdens de registratie. Deze mogelijkheid is alleen van toepassing op de gebruiker die de join-bewerking heeft uitgevoerd, niet op andere gebruikers die zich bij dat apparaat aanmelden.
   * Net als bij de wam interactieve aanmelding wordt de MFA-claim niet continu bijgewerkt, dus de MFA-duur is gebaseerd op de levensduur die is ingesteld op de map.

Windows 10 onderhoudt een partitielijst met PWT's voor elke referentie. Er is dus een PRT voor elk van Windows Hello for Business, wachtwoord of smartcard. Deze partitionering zorgt ervoor dat MFA-claims worden geïsoleerd op basis van de gebruikte referenties en niet worden gemengd tijdens tokenaanvragen.

## <a name="how-is-a-prt-invalidated"></a>Hoe wordt een PRT ongeldig verklaard?

Een PRT wordt ongeldig verklaard in de volgende scenario's:

* **Ongeldige gebruiker**: Als een gebruiker wordt verwijderd of uitgeschakeld in Azure AD, wordt zijn PRT ongeldig verklaard en kan deze niet worden gebruikt om tokens voor toepassingen te verkrijgen. Als een verwijderde of uitgeschakelde gebruiker zich al eerder bij een apparaat heeft aangemeld, meldt u zich in de cache aan totdat CloudAP op de hoogte is van de ongeldige status. Zodra CloudAP vaststelt dat de gebruiker ongeldig is, blokkeert deze latere aanmeldingen. Een ongeldige gebruiker wordt automatisch geblokkeerd van aanmelden bij nieuwe apparaten die hun referenties niet in de cache hebben opgeslagen.
* **Ongeldig apparaat:** Als een apparaat wordt verwijderd of uitgeschakeld in Azure AD, wordt de PRT die op dat apparaat is verkregen ongeldig verklaard en kan het niet worden gebruikt om tokens voor andere toepassingen te verkrijgen. Als een gebruiker al is aangemeld bij een ongeldig apparaat, kan deze persoon dit blijven doen. Maar alle tokens op het apparaat zijn ongeldig en de gebruiker heeft geen SSO om alle bronnen van dat apparaat.
* **Wachtwoordwijziging:** Nadat een gebruiker zijn wachtwoord heeft gewijzigd, wordt de PRT die met het vorige wachtwoord is verkregen, ongeldig gemaakt door Azure AD. Wachtwoordwijziging resulteert in de gebruiker krijgt een nieuwe PRT. Deze ongeldigheid kan op twee verschillende manieren gebeuren:
   * Als de gebruiker zich aanmeldt bij Windows met zijn of haar nieuwe wachtwoord, verwijdert CloudAP de oude PRT en verzoekt u Azure AD om een nieuwe PRT uit te geven met hun nieuwe wachtwoord. Als de gebruiker geen internetverbinding heeft, kan het nieuwe wachtwoord niet worden gevalideerd, Windows kan de gebruiker verplichten zijn oude wachtwoord in te voeren.
   * Als een gebruiker zich heeft aangemeld met zijn oude wachtwoord of zijn wachtwoord heeft gewijzigd nadat hij zich bij Windows heeft aangemeld, wordt de oude PRT gebruikt voor op WAM gebaseerde tokenaanvragen. In dit scenario wordt de gebruiker gevraagd om opnieuw te verifiëren tijdens de WAM-tokenaanvraag en wordt een nieuwe PRT uitgegeven.
* **TPM-problemen:** Soms kan de TPM van een apparaat haperen of mislukken, wat leidt tot ontoegankelijkheid van sleutels die door de TPM zijn beveiligd. In dit geval is het apparaat niet in staat om een PRT te krijgen of tokens aan te vragen met behulp van een bestaande PRT, omdat het niet kan bewijzen dat het bezit van de cryptografische sleutels in bezit is. Als gevolg hiervan wordt elke bestaande PRT ongeldig verklaard door Azure AD. Wanneer Windows 10 een fout detecteert, wordt een herstelstroom gestart om het apparaat opnieuw te registreren met nieuwe cryptografische sleutels. Met Hybrid Azure Ad join, net als de eerste registratie, het herstel gebeurt in stilte zonder input van de gebruiker. Voor azure AD-geregistreerde apparaten of Azure AD moet het herstel worden uitgevoerd door een gebruiker die beheerdersbevoegdheden op het apparaat heeft. In dit scenario wordt de herstelstroom geïnitieerd door een Windows-prompt die de gebruiker ertoe brengt het apparaat te herstellen.

## <a name="detailed-flows"></a>Gedetailleerde stromen

De volgende diagrammen illustreren de onderliggende details bij het uitgeven, vernieuwen en gebruiken van een PRT om een toegangstoken voor een toepassing aan te vragen. Bovendien beschrijven deze stappen ook hoe de bovengenoemde beveiligingsmechanismen tijdens deze interacties worden toegepast.

### <a name="prt-issuance-during-first-sign-in"></a>PRT-uitgifte tijdens eerste aanmelding

![PRT uitgifte tijdens eerste teken in gedetailleerde stroom](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> In Azure AD-apparaten gebeurt deze uitwisseling synchroon om een PRT uit te geven voordat de gebruiker zich kan aanmelden bij Windows. In hybride Azure AD-apparaten is on-premises Active Directory de primaire autoriteit. Dus, de gebruiker is alleen te wachten tot ze een TGT kunnen verwerven om in te loggen, terwijl de PRT-uitgifte asynchroon gebeurt. Dit scenario is niet van toepassing op door Azure AD geregistreerde apparaten, aangezien aanmelding geen Azure AD-referenties gebruikt.

| Stap | Beschrijving |
| :---: | --- |
| A | De gebruiker voert zijn wachtwoord in het teken in De Gebruikersinterface. LogonUI geeft de referenties in een auth-buffer door aan LSA, die deze op zijn beurt intern doorgeeft aan CloudAP. CloudAP stuurt dit verzoek door naar de CloudAP-plug-in. |
| B | CloudAP-plug-in initieert een realm discovery-verzoek om de identiteitsprovider voor de gebruiker te identificeren. Als de tenant van de gebruiker een federatieprovider heeft ingesteld, retourneert Azure AD het MEX-eindpunt (Metadata Exchange) van de federatieprovider. Als dit niet het zo is, retourneert Azure AD dat de gebruiker wordt beheerd, wat aangeeft dat de gebruiker kan verifiëren met Azure AD. |
| C | Als de gebruiker wordt beheerd, krijgt CloudAP de nonce van Azure AD. Als de gebruiker wordt gefedereerd, vraagt CloudAP-plug-in een SAML-token van de federatieprovider met de referenties van de gebruiker. Zodra het SAML-token wordt ontvangen, vraagt het een nonce van Azure AD. |
| D | CloudAP-plug-in construeert de verificatieaanvraag met de referenties van de gebruiker, nonce en een broker-scope, ondertekent het verzoek met de apparaatsleutel (dkpriv) en stuurt deze naar Azure AD. In een federatieve omgeving gebruikt CloudAP-plug-in het SAML-token dat is geretourneerd door de federatieprovider in plaats van de referenties van de gebruiker. |
| E | Azure AD valideert de gebruikersreferenties, de nonce en de apparaathandtekening, controleert of het apparaat geldig is in de tenant en geeft de versleutelde PRT uit. Samen met de PRT geeft Azure AD ook een symmetrische sleutel uit, de sessiesleutel die door Azure AD is versleuteld met behulp van de transportsleutel (tkpub). Daarnaast is de sessiesleutel ook ingebed in de PRT. Deze sessiesleutel fungeert als de Proof-of-possession (PoP) sleutel voor volgende verzoeken bij de PRT. |
| F | CloudAP-plug-in geeft de versleutelde PRT- en Sessiesleutel door aan CloudAP. CloudAP vraagt de TPM om de sessiesleutel te decoderen met behulp van de Transport-sleutel (tkpriv) en deze opnieuw te versleutelen met de eigen sleutel van de TPM. CloudAP slaat de versleutelde sessiesleutel op in de cache, samen met de PRT. |

### <a name="prt-renewal-in-subsequent-logons"></a>PRT-vernieuwing in latere aanmeldingen

![PRT-vernieuwing in latere aanmeldingen](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| Stap | Beschrijving |
| :---: | --- |
| A | De gebruiker voert zijn wachtwoord in het teken in De Gebruikersinterface. LogonUI geeft de referenties in een auth-buffer door aan LSA, die deze op zijn beurt intern doorgeeft aan CloudAP. CloudAP stuurt dit verzoek door naar de CloudAP-plug-in. |
| B | Als de gebruiker zich eerder bij de gebruiker heeft aangemeld, start Windows aanmelding in de cache en valideert het referenties om de gebruiker aan te melden. Elke 4 uur initieert de CloudAP-plug-in prt-vernieuwing asynchroon. |
| C | CloudAP-plug-in initieert een realm discovery-verzoek om de identiteitsprovider voor de gebruiker te identificeren. Als de tenant van de gebruiker een federatieprovider heeft ingesteld, retourneert Azure AD het MEX-eindpunt (Metadata Exchange) van de federatieprovider. Als dit niet het zo is, retourneert Azure AD dat de gebruiker wordt beheerd, wat aangeeft dat de gebruiker kan verifiëren met Azure AD. |
| D | Als de gebruiker wordt gefedereerd, vraagt CloudAP-plug-in een SAML-token van de federatieprovider met de referenties van de gebruiker. Zodra het SAML-token wordt ontvangen, vraagt het een nonce van Azure AD. Als de gebruiker wordt beheerd, krijgt CloudAP de nonce rechtstreeks van Azure AD. |
| E | CloudAP-plug-in construeert de verificatieaanvraag met de referenties van de gebruiker, nonce en de bestaande PRT, ondertekent de aanvraag met de sessiesleutel en stuurt deze naar Azure AD. In een federatieve omgeving gebruikt CloudAP-plug-in het SAML-token dat is geretourneerd door de federatieprovider in plaats van de referenties van de gebruiker. |
| F | Azure AD valideert de handtekening van de sessiesleutel door deze te vergelijken met de sessiesleutel die is ingesloten in de PRT, valideert de nonce en controleert of het apparaat geldig is in de tenant en geeft een nieuwe PRT uit. Zoals eerder gezien, wordt de PRT opnieuw vergezeld van de sessiesleutel versleuteld met Transport-sleutel (tkpub). |
| G | CloudAP-plug-in geeft de versleutelde PRT- en Sessiesleutel door aan CloudAP. CloudAP vraagt de TPM om de sessiesleutel te decoderen met behulp van de Transport-sleutel (tkpriv) en deze opnieuw te versleutelen met de eigen sleutel van de TPM. CloudAP slaat de versleutelde sessiesleutel op in de cache, samen met de PRT. |

### <a name="prt-usage-during-app-token-requests"></a>PRT-gebruik tijdens app-tokenaanvragen

![PRT-gebruik tijdens app-tokenaanvragen](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| Stap | Beschrijving |
| :---: | --- |
| A | Een toepassing (bijvoorbeeld Outlook, OneNote enz.) initieert een tokenaanvraag voor WAM. WAM vraagt op zijn beurt de Azure AD WAM-plug-in om de tokenaanvraag te onderhouden. |
| B | Als er al een Vernieuwingstoken voor de toepassing beschikbaar is, gebruikt de Azure AD WAM-plug-in het om een toegangstoken aan te vragen. Om een bewijs van apparaatbinding te leveren, ondertekent de WAM-plug-in het verzoek met de sessiesleutel. Azure AD valideert de sessiesleutel en geeft een toegangstoken en een nieuw vernieuwingstoken voor de app uit, versleuteld met de sessiesleutel. WAM plugin vraagt Cloud AP plugin om de tokens te decoderen, die op zijn beurt, vraagt de TPM te decoderen met behulp van de sessie-sleutel, wat resulteert in WAM plugin krijgen van beide tokens. Vervolgens biedt WAM-plug-in alleen het toegangstoken tot de toepassing, terwijl het vernieuwingstoken opnieuw wordt versleuteld met DPAPI en het in de eigen cache wordt opgeslagen  |
| C |  Als een Vernieuwingstoken voor de toepassing niet beschikbaar is, gebruikt de Azure AD WAM-plug-in de PRT om een toegangstoken aan te vragen. Om het bewijs van bezit te leveren, tekent WAM plugin het verzoek met de PRT met de Sessie-toets. Azure AD valideert de handtekening van de sessiesleutel door deze te vergelijken met de sessiesleutel die is ingesloten in de PRT, controleert of het apparaat geldig is en geeft een toegangstoken en een vernieuwingstoken voor de toepassing uit. Bovendien kan Azure AD een nieuwe PRT uitgeven (op basis van de vernieuwingscyclus), allemaal versleuteld door de sessiesleutel. |
| D | WAM plugin vraagt Cloud AP plugin om de tokens te decoderen, die op zijn beurt, vraagt de TPM te decoderen met behulp van de sessie-sleutel, wat resulteert in WAM plugin krijgen van beide tokens. Vervolgens biedt WAM-plug-in alleen het toegangstoken tot de toepassing, terwijl het vernieuwingstoken opnieuw wordt versleuteld met DPAPI en het in de eigen cache wordt opgeslagen. WAM plugin zal gebruik maken van de refresh token gaan vooruit voor deze toepassing. WAM plugin geeft ook de nieuwe PRT terug aan Cloud AP plugin, die de PRT valideert met Azure AD voordat deze wordt bijgewerkt in de eigen cache. Cloud AP plugin zal gebruik maken van de nieuwe PRT in de toekomst. |
| E | WAM biedt het nieuw uitgegeven toegangstoken tot WAM, dat op zijn beurt het teruggeeft naar de oproeptoepassing|

### <a name="browser-sso-using-prt"></a>Browser SSO met PRT

![Browser SSO met PRT](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| Stap | Beschrijving |
| :---: | --- |
| A | Gebruiker logt in bij Windows met hun referenties om een PRT te krijgen. Zodra de gebruiker de browser, browser (of extensie) opent, worden de URL's uit het register geladen. |
| B | Wanneer een gebruiker een AZURE AD-aanmeldings-URL opent, valideert de browser of extensie de URL met de URL die uit het register is verkregen. Als ze overeenkomen, roept de browser de native clienthost aan voor het verkrijgen van een token. |
| C | De native clienthost valideert dat de URL's behoren tot de Microsoft-identiteitsproviders (Microsoft-account of Azure AD), haalt een nonce uit de URL en belt naar CloudAP-plug-in om een PRT-cookie te krijgen. |
| D | De CloudAP-plug-in maakt de PRT-cookie, meldt zich aan met de TPM-gebonden sessiesleutel en stuurt deze terug naar de native clienthost. Omdat de cookie is ondertekend door de sessiesleutel, kan er niet mee worden geknoeid. |
| E | De native clienthost retourneert deze PRT-cookie naar de browser, die deze bevat als onderdeel van de aanvraagheader x-ms-RefreshTokenCredential en aanvraagtokens van Azure AD. |
| F | Azure AD valideert de handtekening van de sessiesleutel op de PRT-cookie, valideert de nonce, controleert of het apparaat geldig is in de tenant en geeft een ID-token uit voor de webpagina en een versleutelde sessiecookie voor de browser. |

## <a name="next-steps"></a>Volgende stappen

Zie het artikel [Probleemoplossing hybride Azure Active Directory is lid geworden van Windows 10- en Windows Server 2016-apparaten](troubleshoot-hybrid-join-windows-current.md)voor meer informatie over het oplossen van PRT-gerelateerde problemen.
