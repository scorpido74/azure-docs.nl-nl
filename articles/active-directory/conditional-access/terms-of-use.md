---
title: Gebruiksvoorwaarden - Azure Active Directory | Microsoft Documenten
description: Ga aan de slag met Azure Active Directory-gebruiksvoorwaarden om informatie aan werknemers of gasten te presenteren voordat ze toegang krijgen.
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2f06a7c88a7c17f5f93201192664c2d4a97564e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480960"
---
# <a name="azure-active-directory-terms-of-use"></a>Gebruiksvoorwaarden voor Azure Active Directory

De gebruiksvoorwaarden van Azure AD bieden een eenvoudige methode die organisaties kunnen gebruiken om informatie aan eindgebruikers te presenteren. Deze presentatie zorgt ervoor dat gebruikers relevante disclaimers voor juridische vereisten of nalevingsvereisten te zien krijgen. In dit artikel wordt beschreven hoe u aan de slag met de gebruiksvoorwaarden.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Overzichtsvideo's

De volgende video geeft een snel overzicht van de gebruiksvoorwaarden.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Zie voor extra video's:
- [Gebruiksvoorwaarden implementeren in Azure Active Directory](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Gebruiksvoorwaarden uitrollen in Azure Active Directory](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>Wat kan ik doen met gebruiksvoorwaarden?

De gebruiksvoorwaarden van Azure AD hebben de volgende mogelijkheden:

- Verplicht werknemers of gasten om uw gebruiksvoorwaarden te accepteren voordat ze toegang krijgen.
- Verplicht medewerkers of gasten om uw gebruiksvoorwaarden op elk apparaat te accepteren voordat ze toegang krijgen.
- Verplicht medewerkers of gasten om uw gebruiksvoorwaarden te accepteren volgens een terugkerend schema.
- Verplicht werknemers of gasten om uw gebruiksvoorwaarden te accepteren voordat ze beveiligingsgegevens registreren in Azure Multi-Factor Authentication (MFA).
- Werknemers verplichten om uw gebruiksvoorwaarden te accepteren voordat ze beveiligingsgegevens registreren in Azure AD self-service password reset (SSPR).
- Presenteer algemene gebruiksvoorwaarden voor alle gebruikers in uw organisatie.
- Specifieke gebruiksvoorwaarden presenteren op basis van gebruikerskenmerken (bijvoorbeeld. artsen versus verpleegkundigen of binnenlandse werknemers versus werknemers in het buitenland) met behulp van [dynamische groepen](../users-groups-roles/groups-dynamic-membership.md).
- Presenteer specifieke gebruiksvoorwaarden bij toegang tot toepassingen met een hoge bedrijfsimpact, zoals Salesforce.
- Presenteer gebruiksvoorwaarden in verschillende talen.
- Vermeld wie wel of niet heeft ingestemd met uw gebruiksvoorwaarden.
- Assisteer bij het voldoen aan de privacyregelgeving.
- Een logboek weergeven van gebruiksvoorwaarden voor naleving en controle.
- Gebruiksvoorwaarden maken en beheren met behulp van [Microsoft Graph API's](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement) (momenteel in preview).

## <a name="prerequisites"></a>Vereisten

Als u de gebruiksvoorwaarden van Azure AD wilt gebruiken en configureren, moet u het als nog even hebben:

- Abonnement op Azure AD Premium P1, P2, EMS E3 of EMS E5.
   - Als u niet over een van deze abonnementen beschikt, kunt u [Azure AD Premium downloaden](../fundamentals/active-directory-get-started-premium.md) of [Azure AD Premium uitproberen](https://azure.microsoft.com/trial/get-started-active-directory/).
- Een van de volgende beheerdersaccounts voor de directory die u wilt configureren:
   - Globale beheerder
   - Beveiligingsbeheer
   - Beheerder van voorwaardelijke toegang

## <a name="terms-of-use-document"></a>Document met gebruiksrechtovereenkomst

De gebruiksvoorwaarden van Azure AD gebruiken de PDF-indeling om inhoud weer te geven. Het PDF-bestand kan van alles bevatten, zoals bestaande contracten, zodat u tijdens het aanmelden van gebruikers eindgebruikersovereenkomsten kunt verzamelen. Om gebruikers op mobiele apparaten te ondersteunen, is de aanbevolen tekengrootte in de PDF 24 punten.

## <a name="add-terms-of-use"></a>Gebruiksvoorwaarden toevoegen

Zodra u uw gebruiksvoorwaardendocument hebt afgerond, gebruikt u de volgende procedure om het toe te voegen.

1. Meld u aan bij Azure als globale beheerder, beveiligingsbeheerder of beheerder van voorwaardelijke toegang.
1. Navigeer naar **Gebruiksrechtovereenkomst** op [https://aka.ms/catou](https://aka.ms/catou).

   ![Voorwaardelijke toegang - Gebruiksvoorwaarden blade](./media/terms-of-use/tou-blade.png)

1. Klik op **Nieuwe voorwaarden**.

   ![Venster nieuwe gebruiksvoorwaarden om uw gebruiksvoorwaarden op te geven](./media/terms-of-use/new-tou.png)

1. Voer in het vak **Naam** een naam in voor de gebruiksvoorwaarden die worden gebruikt in de Azure-portal.
1. Voer in het vak **Weergavenaam** een titel in die gebruikers zien wanneer ze zich aanmelden.
1. Voor **het document Met gebruiksvoorwaarden**bladert u naar uw definitieve gebruiksvoorwaarden PDF en selecteert u het.
1. Selecteer de taal voor uw gebruiksvoorwaardendocument. Via de taaloptie kunt u meerdere gebruiksvoorwaarden uploaden, elk met een andere taal. Welke versie van de gebruiksvoorwaarden aan een eindgebruiker wordt weergegeven, is gebaseerd op de browservoorkeuren.
1. Als u wilt dat eindgebruikers de gebruiksvoorwaarden bekijken voordat ze worden geaccepteerd, stelt **u Gebruikers in om de gebruiksvoorwaarden uit te breiden** naar **Aan.**
1. Als u wilt dat eindgebruikers uw gebruiksvoorwaarden accepteren op elk apparaat waartoe ze toegang hebben, stelt **u Gebruikers in om op elk apparaat toestemming** te geven voor **Aan.** Gebruikers kunnen worden verplicht om extra toepassingen te installeren als deze optie is ingeschakeld. Zie [gebruiksvoorwaarden per apparaat](#per-device-terms-of-use)voor meer informatie .
1. Als u de gebruiksvoorwaarden wilt verlopen in een planning, stelt u **De toestemming voor verlopen in** met **Op**. Wanneer u bent ingesteld op Aan, worden twee extra planningsinstellingen weergegeven.

   ![Instellingen voor toestemming verlopen om de begindatum, frequentie en duur in te stellen](./media/terms-of-use/expire-consents.png)

1. Gebruik de **instellingen Voor verlopen en** **Frequentie** om de planning voor gebruiksvoorwaarden op te geven. In de volgende tabel ziet u het resultaat voor een paar voorbeeldinstellingen:

   | Verlopen vanaf | Frequency | Resultaat |
   | --- | --- | --- |
   | De datum van vandaag  | Maandelijks | Vanaf vandaag moeten gebruikers de gebruiksvoorwaarden accepteren en vervolgens elke maand opnieuw accepteren. |
   | Datum in de toekomst  | Maandelijks | Vanaf vandaag moeten gebruikers de gebruiksvoorwaarden accepteren. Wanneer de toekomstige datum plaatsvindt, verlopen toestemmingen en moeten gebruikers elke maand opnieuw accepteren.  |

   Als u bijvoorbeeld het verlopen vanaf 1 **januari** en de frequentie instelt op **Maandelijks,** u als voorbeeld dat er verloop kan optreden voor twee gebruikers:

   | Gebruiker | Eerst datum accepteren | Eerste vervaldatum | Tweede vervaldatum | Derde vervaldatum |
   | --- | --- | --- | --- | --- |
   | Alice | 1 jan | 1 februari | 1 maart | 1 april |
   | Bob | 15 jan | 1 februari | 1 maart | 1 april |

1. Gebruik de **instelling Duur voordat u opnieuw wordt geaccepteerd (dagen)** om het aantal dagen op te geven voordat de gebruiker de gebruiksvoorwaarden opnieuw moet accepteren. Hierdoor kunnen gebruikers hun eigen schema volgen. Als u bijvoorbeeld de duur instelt op **30** dagen, u als gevolg meer voorkomen voor twee gebruikers:

   | Gebruiker | Eerst datum accepteren | Eerste vervaldatum | Tweede vervaldatum | Derde vervaldatum |
   | --- | --- | --- | --- | --- |
   | Alice | 1 jan | 31 jan | 2 maart | 1 april |
   | Bob | 15 jan | 14 februari | 16 maart | 15 april |

   Het is mogelijk om de **verlopen toestemmingen** en duur te gebruiken **voordat opnieuw acceptatie (dagen)** instellingen samen vereist, maar meestal gebruikt u het ene of het andere.

1. Gebruik **onder Voorwaardelijke toegang**de lijst Met voorwaardelijke **toegang-beleidssjabloon afdwingen** om de sjabloon te selecteren om de gebruiksvoorwaarden af te dwingen.

   ![Vervolgkeuzelijst Voorwaardelijke toegang om een beleidssjabloon te selecteren](./media/terms-of-use/conditional-access-templates.png)

   | Template | Beschrijving |
   | --- | --- |
   | **Toegang tot cloud-apps voor alle gasten** | Er wordt een beleid voor voorwaardelijke toegang gemaakt voor alle gasten en alle cloud-apps. Dit beleid heeft gevolgen voor de Azure-portal. Zodra dit is gemaakt, moet u zich mogelijk afmelden en aanmelden. |
   | **Toegang tot cloud-apps voor alle gebruikers** | Er wordt een conditional access-beleid gemaakt voor alle gebruikers en alle cloud-apps. Dit beleid heeft gevolgen voor de Azure-portal. Zodra dit is gemaakt, moet u zich afmelden en aanmelden. |
   | **Aangepast beleid** | Selecteer de gebruikers, groepen en apps waarop deze gebruiksvoorwaarden worden toegepast. |
   | **Beleid voor voorwaardelijke toegang later maken** | Deze gebruiksvoorwaarden worden weergegeven in de lijst met subsidiebesturingselementen bij het maken van een beleid voor voorwaardelijke toegang. |

   >[!IMPORTANT]
   >Beleidscontroles voor voorwaardelijke toegang (inclusief gebruiksvoorwaarden) ondersteunen geen handhaving op serviceaccounts. We raden u aan alle serviceaccounts uit te sluiten van het beleid voor voorwaardelijke toegang.

    Aangepaste voorwaardelijke toegangsbeleidsregels maken gebruiksvoorwaarden mogelijk, tot een specifieke cloudtoepassing of groep gebruikers. Zie [Snelstart: gebruiksvoorwaarden vereisen voordat u toegang krijgt tot cloud-apps.](require-tou.md)

1. Klik **op Maken**.

   Als u een aangepaste sjabloon voor voorwaardelijke toegang hebt geselecteerd, verschijnt er een nieuw scherm waarmee u het aangepaste beleid voor voorwaardelijke toegang maken.

   ![Nieuw deelvenster Voorwaardelijke toegang als u de aangepaste beleidssjabloon Voorwaardelijke toegang hebt gekozen](./media/terms-of-use/custom-policy.png)

   U zou nu uw nieuwe gebruiksvoorwaarden moeten zien.

   ![Nieuwe gebruiksvoorwaarden vermeld in de gebruiksvoorwaarden](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Rapport weergeven van wie heeft geaccepteerd en geweigerd

In de Gebruiksrechtovereenkomst-blade ziet u de aantallen gebruikers die al dan niet hebben geaccepteerd. Deze tellingen en die worden geaccepteerd/geweigerd worden opgeslagen voor de levensduur van de gebruiksvoorwaarden.

1. Meld u aan bij Azure en navigeer naar **Gebruiksrechtovereenkomst** op [https://aka.ms/catou](https://aka.ms/catou).

   ![Gebruiksvoorwaarden blade vermelding van het aantal gebruikers tonen zijn geaccepteerd en geweigerd](./media/terms-of-use/view-tou.png)

1. Klik voor een gebruiksvoorwaarden op de nummers onder **Geaccepteerd** of **Geweigerd** om de huidige status voor gebruikers weer te geven.

   ![Gebruiksvoorwaarden geven een deelvenster met de gebruikers die hebben geaccepteerd](./media/terms-of-use/accepted-tou.png)

1. Als u de geschiedenis voor een individuele gebruiker wilt bekijken, klikt u op de ellips (**... )** en vervolgens **geschiedenis weergeven**.

   ![Contextmenu Geschiedenis weergeven voor een gebruiker](./media/terms-of-use/view-history-menu.png)

   In het deelvenster Weergavegeschiedenis ziet u een geschiedenis van alle accepteren, weigeringen en vervaldatums.

   ![Het deelvenster Geschiedenis weergeven bevat de geschiedenis die accepteert, weigert en afloopt voor een gebruiker](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Azure AD-controlelogboeken weergeven

Als u extra activiteit wilt weergeven, bevatten de gebruiksvoorwaarden van Azure AD controlelogboeken. Elke toestemming van de gebruiker activeert een gebeurtenis in de controlelogboeken die **gedurende 30 dagen**wordt opgeslagen. U kunt deze logboeken bekijken in de portal of downloaden als CSV-bestand.

Ga als volgt te werk om aan de slag te gaan met Azure AD-controlelogboeken:

1. Meld u aan bij Azure en navigeer naar **Gebruiksrechtovereenkomst** op [https://aka.ms/catou](https://aka.ms/catou).
1. Selecteer een gebruiksvoorwaarden.
1. Klik op **Auditlogboeken weergeven**.

   ![Gebruiksvoorwaarden blade met de optie Controlelogboeken weergeven gemarkeerd](./media/terms-of-use/audit-tou.png)

1. In het scherm Azure AD-controlelogboeken u de gegevens filteren met behulp van de meegeleverde lijsten om specifieke controlelogboekgegevens te targeten.

   U kunt ook op **Downloaden** klikken om de informatie te downloaden naar een CSV-bestand voor lokaal gebruik.

   ![Azure AD-controle registreert schermvermeldingsdatum, doelbeleid, geïnitieerd door en activiteit](./media/terms-of-use/audit-logs-tou.png)

   Als u op een logboek klikt, verschijnt er een deelvenster met extra activiteitsgegevens.

   ![Activiteitsdetails voor een logboek met activiteit, activiteitsstatus, geïnitieerd door, doelbeleid](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Hoe gebruiksvoorwaarden er voor gebruikers uitziet

Zodra een gebruiksvoorwaarden zijn gemaakt en afgedwongen, zien gebruikers, die in het bereik zijn, het volgende scherm tijdens het aanmelden.

![Gebruiksvoorwaarden die worden weergegeven wanneer een gebruiker zich aanmeldt](./media/terms-of-use/user-tou.png)

Gebruikers kunnen de gebruiksvoorwaarden bekijken en, indien nodig, knoppen gebruiken om in en uit te zoomen.

![Weergave van de gebruiksvoorwaarden met zoomknoppen](./media/terms-of-use/zoom-buttons.png)

In het volgende scherm ziet u hoe de gebruiksvoorwaarden er op mobiele apparaten uitzien.

![Gebruiksvoorwaarden die worden weergegeven wanneer een gebruiker zich aanmeldt op een mobiel apparaat](./media/terms-of-use/mobile-tou.png)

Gebruikers hoeven de gebruiksvoorwaarden slechts één keer te accepteren en zien de gebruiksvoorwaarden niet meer bij latere aanmeldingen.

### <a name="how-users-can-review-their-terms-of-use"></a>Hoe gebruikers hun gebruiksvoorwaarden kunnen bekijken

Gebruikers kunnen de gebruiksvoorwaarden bekijken en bekijken die ze hebben geaccepteerd met behulp van de volgende procedure.

1. Log hier [https://myapps.microsoft.com](https://myapps.microsoft.com)in
1. Klik in de rechterbovenhoek op uw naam en selecteer **Profiel**.

   ![MyApps-site met het deelvenster van de gebruiker geopend](./media/terms-of-use/tou14.png)

1. Klik in uw profielpagina op **Gebruiksvoorwaarden controleren**.

   ![Profielpagina voor een gebruiker met de koppeling Gebruiksvoorwaarden controleren](./media/terms-of-use/tou13a.png)

1. Vanaf hier kunt u de gebruiksrechtovereenkomst bekijken die u hebt geaccepteerd.

## <a name="edit-terms-of-use-details"></a>Gebruiksvoorwaarden bewerken

U enkele details over de gebruiksvoorwaarden bewerken, maar u een bestaand document niet wijzigen. In de volgende procedure wordt beschreven hoe u de details bewerken.

1. Meld u aan bij Azure en navigeer naar **Gebruiksrechtovereenkomst** op [https://aka.ms/catou](https://aka.ms/catou).
1. Selecteer de gebruiksvoorwaarden die u wilt bewerken.
1. Klik **op Termen bewerken**.
1. Wijzig in het deelvenster Gebruiksvoorwaarden bewerken de naam, de weergavenaam of vereisen dat gebruikers waarden uitbreiden.

   Als er andere instellingen zijn die u wilt wijzigen, zoals PDF-document, vereisen gebruikers toestemming te geven op elk apparaat, toestemmingen, duur voor heraanvaarding of beleid voor voorwaardelijke toegang te vervallen, moet u een nieuwe gebruiksvoorwaarden maken.

   ![Gebruiksvoorwaarden venster met naam en uitvouwopties bewerken](./media/terms-of-use/edit-tou.png)

1. Klik **op Opslaan** om de wijzigingen op te slaan.

   Zodra u uw wijzigingen hebt opgeslagen, hoeven gebruikers deze bewerkingen niet meer te accepteren.

## <a name="add-a-terms-of-use-language"></a>Een gebruiksvoorwaarden toevoegen

In de volgende procedure wordt beschreven hoe u een gebruikstaal toevoegen.

1. Meld u aan bij Azure en navigeer naar **Gebruiksrechtovereenkomst** op [https://aka.ms/catou](https://aka.ms/catou).
1. Selecteer de gebruiksvoorwaarden die u wilt bewerken.
1. Klik in het detailvenster op het tabblad **Talen.**

   ![Gebruiksvoorwaarden geselecteerd en het tabblad Talen weergeven in het detailvenster](./media/terms-of-use/languages-tou.png)

1. Klik **op Taal toevoegen**.
1. Upload in het taalvenster Gebruiksvoorwaarden toevoegen uw gelokaliseerde PDF en selecteer de taal.

   ![Taalvenster met gebruiksvoorwaarden toevoegen met opties om gelokaliseerde PDF's te uploaden](./media/terms-of-use/language-add-tou.png)

1. Klik **op Toevoegen** om de taal toe te voegen.

## <a name="per-device-terms-of-use"></a>Gebruiksvoorwaarden per apparaat

Met **de toestemming van gebruikers op elke apparaatinstelling** u eindgebruikers verplichten uw gebruiksvoorwaarden te accepteren op elk apparaat waartoe ze toegang hebben. De eindgebruiker moet zijn apparaat registreren in Azure AD. Wanneer het apparaat wordt geregistreerd, wordt de apparaat-ID gebruikt om de gebruiksvoorwaarden op elk apparaat af te dwingen.

Hier is een lijst van de ondersteunde platforms en software.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Overige |
> | --- | --- | --- | --- | --- |
> | **Systeemeigen app** | Ja | Ja | Ja |  |
> | **Microsoft Edge** | Ja | Ja | Ja |  |
> | **Internet Explorer** | Ja | Ja | Ja |  |
> | **Chrome (met extensie)** | Ja | Ja | Ja |  |

Gebruiksvoorwaarden per apparaat hebben de volgende beperkingen:

- Een apparaat kan slechts aan één tenant worden samengevoegd.
- Een gebruiker moet machtigingen hebben om zich bij zijn apparaat aan te sluiten.
- De Intune Enrollment-app wordt niet ondersteund.
- Azure AD B2B-gebruikers worden niet ondersteund.

Als het apparaat van de gebruiker niet is aangesloten, ontvangt hij een bericht dat hij/zij moet deelnemen aan het apparaat. Hun ervaring zal afhankelijk zijn van het platform en de software.

### <a name="join-a-windows-10-device"></a>Een apparaat met Windows 10 toevoegen

Als een gebruiker Windows 10 en Microsoft Edge gebruikt, ontvangt deze gebruiker een bericht dat vergelijkbaar is met het volgende om zich bij zijn apparaat aan te [sluiten.](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device)

![Windows 10 en Microsoft Edge - Bericht waarin wordt aangegeven dat uw apparaat moet zijn geregistreerd](./media/terms-of-use/per-device-win10-edge.png)

Als ze Chrome gebruiken, wordt hen gevraagd de [windows 10-accountsextensie](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)te installeren.

### <a name="register-an-ios-device"></a>Een iOS-apparaat registreren

Als een gebruiker een iOS-apparaat gebruikt, wordt deze persoon gevraagd de [Microsoft Authenticator-app](https://apps.apple.com/us/app/microsoft-authenticator/id983156458)te installeren.

### <a name="register-an-android-device"></a>Een Android-apparaat registreren

Als een gebruiker een Android-apparaat gebruikt, wordt deze persoon gevraagd de [Microsoft Authenticator-app](https://play.google.com/store/apps/details?id=com.azure.authenticator)te installeren.

### <a name="browsers"></a>Browsers

Als een gebruiker een browser gebruikt die niet wordt ondersteund, wordt hem of haar gevraagd een andere browser te gebruiken.

![Bericht dat aangeeft dat uw apparaat moet worden geregistreerd, maar browser wordt niet ondersteund](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Gebruiksvoorwaarden verwijderen

U oude gebruiksvoorwaarden verwijderen via de volgende procedure.

1. Meld u aan bij Azure en navigeer naar **Gebruiksrechtovereenkomst** op [https://aka.ms/catou](https://aka.ms/catou).
1. Selecteer de gebruiksrechtovereenkomst die u wilt verwijderen.
1. Klik op **Gebruiksvoorwaarden verwijderen**.
1. Klik in het bericht waarin u wordt gevraagd of u wilt doorgaan op **Ja**.

   ![Bericht waarin om bevestiging wordt gevraagd om gebruiksvoorwaarden te verwijderen](./media/terms-of-use/delete-tou.png)

   U mag uw gebruiksvoorwaarden niet meer zien.

## <a name="deleted-users-and-active-terms-of-use"></a>Verwijderde gebruikers en actieve gebruiksvoorwaarden

Standaard blijft een verwijderde gebruiker gedurende 30 dagen in Azure AD aanwezig met de status Verwijderd. In deze periode kan de gebruiker eventueel door een beheerder worden teruggezet. Na dertig dagen wordt die gebruiker definitief verwijderd. Bovendien kan een globale beheerder met behulp van de Azure Active Directory-portal [een onlangs verwijderde gebruiker](../fundamentals/active-directory-users-restore.md) expliciet permanent verwijderen voordat die periode is bereikt. Een gebruiker is permanent verwijderd, latere gegevens over die gebruiker worden verwijderd uit de actieve gebruiksvoorwaarden. Controle-informatie over verwijderde gebruikers blijft in het auditlogboek aanwezig.

## <a name="policy-changes"></a>Beleidswijzigingen

Het beleid voor voorwaardelijke toegang wordt onmiddellijk van kracht. Wanneer dit gebeurt, zal de beheerder beginnen met het zien van "sad clouds" of "Azure AD token issues". De beheerder moet zich afmelden en opnieuw inloggen om aan het nieuwe beleid te voldoen.

> [!IMPORTANT]
> Gebruikers in het bereik moeten zich af- en aanmelden om te voldoen aan een nieuw beleid:
>
> - een beleid voor voorwaardelijke toegang is ingeschakeld op een gebruiksvoorwaarden
> - of als er een tweede gebruiksrechtovereenkomst is gemaakt

## <a name="b2b-guests-preview"></a>B2B-gasten (Preview)

De meeste organisaties hebben een proces voor hun werknemers om in te stemmen met de gebruiksvoorwaarden van hun organisatie en privacyverklaringen. Maar hoe u dezelfde toestemmingen afdwingen voor B2B-gasten (Azure AD business-to-business) wanneer ze worden toegevoegd via SharePoint of Teams? Met voorwaardelijke toegang en gebruiksvoorwaarden u een beleid rechtstreeks afdwingen ten opzichte van B2B-gastgebruikers. Tijdens de uitnodigingsstroom wordt de gebruiker de gebruiksvoorwaarden gepresenteerd. Deze ondersteuning is momenteel in preview.

Gebruiksvoorwaarden worden alleen weergegeven wanneer de gebruiker een gastaccount heeft in Azure AD. SharePoint Online heeft momenteel een [ad hoc ervaring met het delen van ontvangers](/sharepoint/what-s-new-in-sharing-in-targeted-release) om een document of een map te delen waarvoor de gebruiker geen gastaccount hoeft te hebben. In dit geval wordt een gebruiksvoorwaarden niet weergegeven.

![Deelvenster Gebruikers en groepen - Tabblad opnemen met de optie Alle gastgebruikers ingeschakeld](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>Ondersteuning voor cloud-apps (Voorbeeld)

Gebruiksvoorwaarden kunnen worden gebruikt voor verschillende cloud-apps, zoals Azure Information Protection en Microsoft Intune. Deze ondersteuning is momenteel in preview.

### <a name="azure-information-protection"></a>Azure Information Protection

U een beleid voor voorwaardelijke toegang configureren voor de Azure Information Protection-app en een gebruiksvoorwaarden vereisen wanneer een gebruiker toegang heeft tot een beveiligd document. Dit zal leiden tot een gebruiksvoorwaarden voordat een gebruiker voor de eerste keer toegang krijgt tot een beveiligd document.

![Deelvenster Cloud-apps met Microsoft Azure Information Protection-app geselecteerd](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Microsoft Intune-inschrijving

U een beleid voor voorwaardelijke toegang configureren voor de Microsoft Intune-inschrijvings-app en een gebruiksvoorwaarden vereisen voordat een apparaat in Intune wordt inschrijving. Zie Lees [de juiste termenoplossing voor uw organisatieblogbericht voor](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409)meer informatie.

![Deelvenster Cloud-apps met Microsoft Intune-app geselecteerd](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> De Intune Enrollment-app wordt niet ondersteund voor [gebruiksvoorwaarden per apparaat.](#per-device-terms-of-use)

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**V: Hoe zie ik wanneer/of een gebruiker een gebruiksvoorwaarden heeft geaccepteerd?**<br />
A: Klik op het gebruiksblad op het blad onder **Geaccepteerd**. U de acceptatieactiviteit ook weergeven of doorzoeken in de Azure AD-controlelogboeken. Zie Rapport weergeven van wie heeft geaccepteerd en geweigerd en [Azure AD-controlelogboeken weergeven](#view-azure-ad-audit-logs).

**V: hoe lang worden gegevens opgeslagen?**<br />
A: De gebruiker telt in de gebruiksvoorwaarden rapport en die geaccepteerd / geweigerd worden opgeslagen voor de levensduur van de gebruiksvoorwaarden. De Azure AD-controlelogboeken worden 30 dagen bewaard.

**V: Waarom zie ik een ander aantal toestemmingen in het gebruiksvoorwaardenrapport versus de Azure AD-controlelogboeken?**<br />
A: Het rapport over de gebruiksvoorwaarden wordt opgeslagen gedurende de levensduur van die gebruiksvoorwaarden, terwijl de Azure AD-controlelogboeken gedurende 30 dagen worden opgeslagen. In het gebruiksvoorwaardenrapport worden ook alleen de huidige toestemmingsstatus van de gebruikers weergegeven. Als een gebruiker bijvoorbeeld weigert en vervolgens accepteert, wordt in het gebruiksvoorwaardenrapport alleen weergegeven dat de gebruiker accepteert. Als u de geschiedenis wilt zien, u de Azure AD-controlelogboeken gebruiken.

**V: Als ik de details voor een gebruiksvoorwaarden bewerk, moeten gebruikers dan opnieuw accepteren?**<br />
A: Nee, als een beheerder de gegevens voor een gebruiksvoorwaarden bewerkt (naam, weergavenaam, gebruikers verplichten uit te vouwen of een taal toe te voegen), vereist dit niet dat gebruikers de nieuwe voorwaarden opnieuw accepteren.

**V: Kan ik een bestaand gebruiksvoorwaardendocument bijwerken?**<br />
A: Momenteel u een bestaand gebruiksvoorwaardendocument niet bijwerken. Als u een gebruiksvoorwaardendocument wilt wijzigen, moet u een nieuwe gebruiksvoorwaardenmaken.

**V: Als hyperlinks in de gebruiksvoorwaarden van pdf-document staan, kunnen eindgebruikers er dan op klikken?**<br />
A: Ja, eindgebruikers kunnen hyperlinks naar extra pagina's selecteren, maar koppelingen naar secties in het document worden niet ondersteund.

**V: Kan een gebruiksvoorwaarden meerdere talen ondersteunen?**<br />
A: Ja. Momenteel zijn er 108 verschillende talen die een beheerder kan configureren voor één gebruiksvoorwaarden. Een beheerder kan meerdere PDF-documenten uploaden en deze documenten taggen met een overeenkomstige taal (tot 108). Wanneer eindgebruikers zich aanmelden, bekijken we hun browsertaalvoorkeur en geven we het overeenkomende document weer. Als er geen overeenkomst is, geven we het standaarddocument weer, het eerste document dat wordt geüpload.

**V: Wanneer worden de gebruiksvoorwaarden geactiveerd?**<br />
A: De gebruiksrechtovereenkomst wordt geactiveerd tijdens het aanmelden.

**V: Voor welke toepassingen kan ik een gebruiksrechtovereenkomst implementeren?**<br />
A: U een beleid voor voorwaardelijke toegang voor de bedrijfstoepassingen maken met behulp van moderne verificatie. Zie [Bedrijfstoepassingen](./../manage-apps/view-applications-portal.md) voor meer informatie.

**V: Kan ik meerdere gebruiksvoorwaarden toevoegen aan een bepaalde gebruiker of app?**<br />
A: Ja, door meerdere voorwaardelijke toegangsbeleidsregels te maken die zijn gericht op die groepen of toepassingen. Als een gebruiker valt in het bereik van meerdere gebruiksvoorwaarden, accepteren ze één gebruiksvoorwaarden tegelijk.

**V: Wat gebeurt er als een gebruiker de gebruiksrechtovereenkomst afwijst?**<br />
A: De gebruiker krijgt dan geen toegang tot de toepassing. De gebruiker zou zich opnieuw moeten aanmelden en de voorwaarden moeten accepteren om toegang te krijgen.

**V: Is het mogelijk om een gebruiksvoorwaarden die eerder zijn geaccepteerd, niet te accepteren?**<br />
A: Je [eerder geaccepteerde gebruiksvoorwaarden bekijken,](#how-users-can-review-their-terms-of-use)maar op dit moment is er geen manier om accepteren te accepteren.

**V: Wat gebeurt er als ik ook de algemene voorwaarden van Intune gebruik?**<br />
A: Als u zowel de gebruiksvoorwaarden van Azure AD als de algemene voorwaarden van [Intune](/intune/terms-and-conditions-create)hebt geconfigureerd, moet de gebruiker beide accepteren. Zie De oplossing [De juiste voorwaarden voor uw organisatieblogbericht kiezen voor](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409)meer informatie.

**V: Welke eindpunten gebruiken de gebruiksvoorwaarden voor verificatie?**<br />
A: Gebruiksvoorwaarden maken gebruik van de https://tokenprovider.termsofuse.identitygovernance.azure.com https://account.activedirectory.windowsazure.comvolgende eindpunten voor verificatie: en . Als uw organisatie een lijst met URL's voor inschrijving heeft, moet u deze eindpunten toevoegen aan uw lijst met toegestane punten, samen met de Azure AD-eindpunten voor aanmelden.

## <a name="next-steps"></a>Volgende stappen

- [Snelstart: vereisen dat gebruiksvoorwaarden worden geaccepteerd voordat u toegang krijgt tot cloud-apps](require-tou.md)
- [Aanbevolen procedures voor voorwaardelijke toegang in Azure Active Directory](best-practices.md)
