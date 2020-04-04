---
title: bestand opnemen
description: bestand opnemen
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 03/30/2020
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: 7fd716be397d9ef6b9d6132cd4470f653f3cea0f
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655955"
---
## <a name="for-users-in-your-directory"></a>Voor gebruikers in uw directory

Volg deze stappen als u wilt dat gebruikers in uw directory dit toegangspakket kunnen aanvragen. Wanneer u het aanvraagbeleid definieert, u afzonderlijke gebruikers of vaker groepen gebruikers opgeven. Uw organisatie kan bijvoorbeeld al een groep hebben, zoals **Alle werknemers.**  Als die groep wordt toegevoegd in het beleid voor gebruikers die toegang kunnen aanvragen, kan elk lid van die groep toegang aanvragen.

1. Klik **in** de sectie Gebruikers die toegang kunnen aanvragen op Gebruikers in uw **map.**

    Wanneer u deze optie selecteert, worden nieuwe opties weergegeven om verder te verfijnen wie in uw map dit toegangspakket kan aanvragen.

    ![Toegangspakket - Aanvragen - Voor gebruikers in uw directory](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. Selecteer één van de volgende opties:

    |  |  |
    | --- | --- |
    | **Specifieke gebruikers en groepen** | Kies deze optie als u alleen de gebruikers en groepen in uw map wilt die u opgeeft, dit toegangspakket kunnen aanvragen. |
    | **Alle leden (met uitzondering van gasten)** | Kies deze optie als u wilt dat alle leden in uw directory dit toegangspakket kunnen aanvragen. Deze optie bevat geen gastgebruikers die u mogelijk hebt uitgenodigd in uw directory. |
    | **Alle gebruikers (inclusief gasten)** | Kies deze optie als u wilt dat alle leden en gastgebruikers in uw directory dit toegangspakket kunnen aanvragen. |

    Gastgebruikers verwijzen naar externe gebruikers die zijn uitgenodigd in uw directory met [Azure AD B2B.](../articles/active-directory/b2b/what-is-b2b.md) Zie [Wat zijn de standaardgebruikersmachtigingen in Azure Active Directory voor](../articles/active-directory/fundamentals/users-default-permissions.md)meer informatie over de verschillen tussen leden gebruikers en gastgebruikers.

1. Als u **Specifieke gebruikers en groepen**hebt geselecteerd, klikt u op Gebruikers en groepen **toevoegen**.

1. Selecteer in het deelvenster Gebruikers en groepen selecteren de gebruikers en groepen die u wilt toevoegen.

    ![Toegangspakket - Aanvragen - Gebruikers en groepen selecteren](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. Klik **op Selecteren** om de gebruikers en groepen toe te voegen.

1. Ga naar de [sectie Goedkeuring.](#approval)

## <a name="for-users-not-in-your-directory"></a>Voor gebruikers die niet in uw map staan

 **Gebruikers die niet in uw map staan,** verwijzen naar gebruikers die zich in een andere Azure AD-map of -domein bevinden. Deze gebruikers zijn mogelijk nog niet uitgenodigd in uw directory. Azure AD-mappen moeten zijn geconfigureerd om uitnodigingen in **samenwerkingsbeperkingen**toe te staan. Zie [Externe samenwerking inschakelen en beheren wie gasten kan uitnodigen](../articles/active-directory/b2b/delegate-invitations.md)voor meer informatie.

> [!NOTE]
> Er wordt een gastgebruikersaccount gemaakt voor een gebruiker die nog niet in uw directory is geplaatst en waarvan het verzoek is goedgekeurd of automatisch is goedgekeurd. De gast wordt uitgenodigd, maar ontvangt geen uitnodigingsmail. In plaats daarvan ontvangen ze een e-mail wanneer hun toegangspakkettoewijzing wordt geleverd. Als die gastgebruiker later geen toegangspakkettoewijzingen meer heeft, omdat zijn laatste opdracht is verlopen of geannuleerd, wordt dat gastgebruikersaccount geblokkeerd voor aanmelding en vervolgens verwijderd. Als u gastgebruikers voor onbepaalde tijd in uw directory wilt laten blijven, zelfs als ze geen toegangspakkettoewijzingen hebben, u de instellingen voor uw rechtenbeheerconfiguratie wijzigen. Zie Eigenschappen van een Azure [Active Directory B2B-samenwerkingsgebruiker](../articles/active-directory/b2b/user-properties.md)voor meer informatie over het gastgebruikersobject.

Voer deze stappen uit als u gebruikers die niet in uw map staan wilt toestaan dit toegangspakket aan te vragen:

1. Klik **in** de sectie Gebruikers die toegang kunnen aanvragen op Voor gebruikers die niet in uw map **staan.**

    Wanneer u deze optie selecteert, worden nieuwe opties weergegeven.

    ![Toegangspakket - Aanvragen - Voor gebruikers die niet in uw directory staan](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. Selecteer één van de volgende opties:

    |  |  |
    | --- | --- |
    | **Specifieke verbonden organisaties** | Kies deze optie als u wilt kiezen uit een lijst met organisaties die uw beheerder eerder heeft toegevoegd. Alle gebruikers van de geselecteerde organisaties kunnen dit toegangspakket aanvragen. |
    | **Alle verbonden organisaties** | Kies deze optie als alle gebruikers van al uw verbonden organisaties dit toegangspakket kunnen aanvragen. |
    | **Alle gebruikers (Alle verbonden organisaties + nieuwe externe gebruikers)** | Kies deze optie als alle gebruikers van al uw verbonden organisaties dit toegangspakket kunnen aanvragen en dat de lijstinstellingen voor B2B toestaan of weigeren voorrang moeten krijgen voor elke nieuwe externe gebruiker. |

    Een verbonden organisatie is een externe Azure AD-map of -domein waarmee u een relatie hebt.

1. Als u **Specifieke verbonden organisaties hebt**geselecteerd, klikt u op Mappen **toevoegen** om te selecteren in een lijst met verbonden organisaties die uw beheerder eerder heeft toegevoegd.

1. Typ de naam of domeinnaam om te zoeken naar een eerder verbonden organisatie.

    ![Toegangspakket - Aanvragen - Mappen selecteren](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    Als de organisatie waarmee u wilt samenwerken niet in de lijst staat, u uw beheerder vragen deze toe te voegen als een verbonden organisatie. Zie [Een verbonden organisatie toevoegen](../articles/active-directory/governance/entitlement-management-organization.md)voor meer informatie.

1. Nadat u al uw verbonden organisaties hebt geselecteerd, klikt u op **Selecteren**.

    > [!NOTE]
    > Alle gebruikers van de geselecteerde verbonden organisaties kunnen dit toegangspakket aanvragen. Dit geldt ook voor gebruikers in Azure AD uit alle subdomeinen die aan de organisatie zijn gekoppeld, tenzij deze domeinen worden geblokkeerd door de lijst met Azure B2B-toegestane of weigeren. Zie [Uitnodigingen voor B2B-gebruikers van specifieke organisaties toestaan of blokkeren](../articles/active-directory/b2b/allow-deny-list.md)voor meer informatie.

1. Ga naar de [sectie Goedkeuring.](#approval)

## <a name="none-administrator-direct-assignments-only"></a>None

Voer deze stappen uit als u toegangsaanvragen wilt omzeilen en beheerders toestaan om specifieke gebruikers rechtstreeks aan dit toegangspakket toe te wijzen. Gebruikers hoeven het toegangspakket niet aan te vragen. U nog steeds levenscyclusinstellingen instellen, maar er zijn geen aanvraaginstellingen.

1. Klik **in** de sectie Gebruikers die toegang kunnen aanvragen op Geen **(alleen directe toewijzingen voor beheerders**.

    ![Toegangspakket - Aanvragen - Alleen directe toewijzingen van beheerders](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    Nadat u het toegangspakket hebt gemaakt, u specifieke interne en externe gebruikers rechtstreeks toewijzen aan het toegangspakket. Als u een externe gebruiker opgeeft, wordt er een gastgebruikersaccount gemaakt in uw directory. Zie [Toewijzingen voor een toegangspakket weergeven, toevoegen en verwijderen voor](../articles/active-directory/governance/entitlement-management-access-package-assignments.md)informatie over het rechtstreeks toewijzen van een gebruiker.

1. Ga naar de sectie [Aanvragen inschakelen.](#enable-requests)

## <a name="approval"></a>Goedkeuring

In de sectie Goedkeuring geeft u op of een goedkeuring vereist is wanneer gebruikers dit toegangspakket aanvragen. De goedkeuringsinstellingen werken op de volgende manier:

- Slechts één van de geselecteerde fiatteurs of fallback-fiatteurs hoeft een aanvraag voor goedkeuring in één fase goed te keuren. 
- Slechts één van de geselecteerde fiatteurs van elke fase hoeft een aanvraag voor goedkeuring in twee fasen goed te keuren.
- De fiatteur kan een manager, interne sponsor of externe sponsor zijn, afhankelijk van wie het beleid is voor de toegang.
- Goedkeuring van elke geselecteerde goedkeurder is niet vereist voor één- of tweetrapsgoedkeuring.
- Het goedkeuringsbesluit is gebaseerd op de goedkeuringsinstantie die het verzoek het eerst beoordeelt.

Bekijk de volgende video voor een demonstratie van het toevoegen van goedkeurders aan een aanvraagbeleid:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4cZfg]

Bekijk de volgende video voor een demonstratie van het toevoegen van een goedkeuring in meerdere fasen aan een aanvraagbeleid:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4d1Jw]

Voer de volgende stappen uit om de goedkeuringsinstellingen voor aanvragen voor het toegangspakket op te geven:

1. Als u goedkeuring wilt vereisen voor aanvragen van de geselecteerde gebruikers, stelt u de **schakel van de optie Goedkeuring vereisen** in op **Ja**. Of als u aanvragen automatisch wilt laten goedkeuren, stelt u de schakel in op **Nee**.

1. Als u gebruikers wilt verplichten een rechtvaardiging te geven om het toegangspakket aan te vragen, stelt u de **schakelschakelaar Voor aanvragen of rechtvaardiging** in op **Ja**.
    
1. Bepaal nu of aanvragen een enkele of tweetrapsgoedkeuring vereisen. Stel de **schakelin Hoeveel fasen** in schakelop **1** voor goedkeuring in één fase of stel de schakel in op **2** voor goedkeuring in 2 fasen.

    ![Toegangspakket - Aanvragen - Goedkeuringsinstellingen](./media/active-directory-entitlement-management-request-policy/approval.png)

Gebruik de volgende stappen om fiattes toe te voegen nadat u hebt geselecteerd hoeveel fasen u nodig hebt: 

### <a name="single-stage-approval"></a>Goedkeuring in één fase

1. Voeg de **eerste keursman toe:**
    
    Als het beleid is ingesteld om de toegang voor gebruikers in uw map te regelen, u **Manager selecteren als fiatteur**. Of voeg een specifieke gebruiker toe door op **Goeden toevoegen te** klikken nadat u specifieke goedkeurders selecteren in het vervolgkeuzemenu hebt geselecteerd.
    
    ![Toegangspakket - Aanvragen - Voor gebruikers in directory - Eerste goedkeuringsinstantie](./media/active-directory-entitlement-management-request-policy/approval-single-stage-first-approver-manager.png)

    Als dit beleid is ingesteld om de toegang te regelen voor gebruikers die niet in uw directory staan, u **Externe sponsor** of **Interne sponsor**selecteren. Of voeg een specifieke gebruiker toe door te klikken op **Goederen** of groepen toevoegen onder Specifieke fiatteurs kiezen.
    
    ![Toegangspakket - Aanvragen - Voor gebruikers buiten directory - Eerste goedkeuringsinstantie](./media/active-directory-entitlement-management-request-policy/out-directory-first-approver.png)
    
1. Als u **Manager** als eerste goedkeurder hebt geselecteerd, klikt u op **Terugval toevoegen** om een of meer gebruikers of groepen in uw map te selecteren als een terugvalgoedgever. Fallback-fiatteurs ontvangen de aanvraag als het beheer van de rechten de manager voor de gebruiker die toegang aanvraagt, niet kan vinden.

    De manager wordt gevonden door het beheer van rechten met behulp van het kenmerk **Manager.** Het kenmerk bevindt zich in het profiel van de gebruiker in Azure AD. Zie [De profielgegevens van een gebruiker toevoegen of bijwerken met Azure Active Directory](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md)voor meer informatie.

1. Als u **Specifieke fiatteurs kiezen hebt**geselecteerd, klikt u op **Fiatteurs toevoegen** om een of meer gebruikers of groepen in uw map te selecteren om fiatteurs te zijn.

1. In het vak onder **besluit moet worden gemaakt in hoeveel dagen?**, het aantal dagen dat een goedkeurder heeft om een verzoek om dit toegangspakket te herzien.

    Als een aanvraag niet binnen deze periode wordt goedgekeurd, wordt deze automatisch geweigerd. De gebruiker moet een ander verzoek indienen voor het toegangspakket.

1. Als u wilt dat fiatteurs een rechtvaardiging voor hun beslissing moeten geven, stelt u Een rechtvaardiging van de keursgoedkeuring voor **Ja**in.

    De rechtvaardiging is zichtbaar voor andere fiatteurs en de aanvrager.

### <a name="2-stage-approval"></a>Goedkeuring in 2 fasen

Als u een goedkeuring in twee fasen hebt geselecteerd, moet u een tweede goedkeuring toevoegen.

1. Voeg de **tweede keursman toe:** 
    
    Als de gebruikers zich in uw map bevinden, voegt u een specifieke gebruiker toe als tweede goedkeurder door op **Fiatjes toevoegen** te klikken onder Specifieke fiatjes kiezen.

    ![Toegangspakket - Aanvragen - Voor gebruikers in directory - Tweede goedkeuringsformulier](./media/active-directory-entitlement-management-request-policy/in-directory-second-approver.png)

    Als de gebruikers zich niet in uw map bevinden, selecteert u **Interne sponsor** of **externe sponsor** als tweede fiatteur. Nadat u de goedkeurder hebt geselecteerd, voegt u de terugvalgoeds toe.

    ![Toegangspakket - Aanvragen - Voor gebruikers buiten directory - Tweede goedkeuringsformulier](./media/active-directory-entitlement-management-request-policy/out-directory-second-approver.png) 

1. Geef op hoeveel dagen de tweede goedkeurder het verzoek moet goedkeuren in het vak onder **besluit moet worden ingediend in hoeveel dagen?**. 

1. Schakel de schakelschakelaar Voor goedkeuring seinen vereisen in op **Ja** of **Nee**.

### <a name="alternate-approvers"></a>Alternatieve fiatteurs

U alternatieve fiatteurs opgeven, vergelijkbaar met het opgeven van de eerste en tweede fiatteurs die aanvragen kunnen goedkeuren. Als u alternatieve fiatteurs hebt, zorgt u ervoor dat de aanvragen worden goedgekeurd of geweigerd voordat ze verlopen (time-out). U alternatieve fiatteurs de eerste keursman en tweede keurgever voor goedkeuring in twee fasen vermelden. 

Door alternatieve fiatteurs op te geven, wordt in het geval dat de eerste of tweede fiatteurs het verzoek niet konden goedkeuren of weigeren, het in behandeling zijnde verzoek doorgestuurd naar de alternatieve fiatteurs, volgens het doorstuurschema dat u hebt opgegeven tijdens de beleidsinstelling. Ze ontvangen een e-mail om het in behandeling zijnde verzoek goed te keuren of te weigeren.

Nadat het verzoek is doorgestuurd naar de alternatieve fiatteurs, kunnen de eerste of tweede fiatteurs het verzoek nog steeds goedkeuren of weigeren. Alternatieve fiatteurs gebruiken dezelfde Mijn Access-site om het in behandeling zijnde verzoek goed te keuren of te weigeren.

We kunnen personen of groepen mensen opnoemen als fiatteurs en alternatieve fiatteurs. Zorg ervoor dat u verschillende sets mensen opgeeft om de eerste, tweede en alternatieve fiatteurs te zijn.
Als u bijvoorbeeld Alice en Bob als eerste goedkeurder(s) vermeldt, vermeldt u Carol en Dave als de alternatieve fiatteurs. Gebruik de volgende stappen om alternatieve fiatteurs toe te voegen aan een toegangspakket:

1. Klik onder de eerste goedgekeurde, tweede instantie of beide op **Geavanceerde aanvraaginstellingen weergeven**.

    ![Toegangspakket - Beleid - Geavanceerde aanvraaginstellingen weergeven](./media/active-directory-entitlement-management-request-policy/alternate-approvers-click-advanced-request.png)

1. Instellen **Als er geen actie wordt ondernomen, wordt u doorsturen naar alternatieve fiatteurs?** **Yes**

1. Klik **op Alternatieve fiatjes toevoegen** en selecteer de alternatieve keurgever(s) in de lijst.

    ![Toegangspakket - Beleid - Alternatieve goedkeurders toevoegen](./media/active-directory-entitlement-management-request-policy/alternate-approvers-add.png)

1. Zet in het vak **Doorsturen naar alternatieve keurgever(s) na het** vak Aantal dagen in het aantal dagen dat de fiatteurs een aanvraag moeten goedkeuren of weigeren. Als er geen fiatteurs het verzoek vóór de aanvraagduur hebben goedgekeurd of geweigerd, vervalt het verzoek (time-out) en moet de gebruiker een ander verzoek voor het toegangspakket indienen. 

    Aanvragen kunnen alleen worden doorgestuurd naar alternatieve fiatteurs een dag nadat de aanvraagduur de helft van de levensduur heeft bereikt. In dit voorbeeld is de duur van de aanvraag 14 dagen. Dus, de aanvraag duur bereikt half-life op dag 7. Dus het verzoek kan niet eerder dan dag 8 worden doorgestuurd. Aanvragen kunnen ook niet worden doorgestuurd op de laatste dag van de aanvraagduur. Dus in het voorbeeld, de laatste het verzoek kan worden doorgestuurd is dag 13.

## <a name="enable-requests"></a>Aanvragen inschakelen

1. Als u wilt dat het toegangspakket onmiddellijk beschikbaar wordt gesteld voor gebruikers in het verzoekbeleid om te vragen, verplaatst u de schakelschakelaar inschakelen naar **Ja**.

    U het in de toekomst altijd inschakelen nadat u klaar bent met het maken van het toegangspakket.

    Als u **Geen (alleen beheerdirecte toewijzingen van beheerders)** hebt geselecteerd en u inschakelt op **Nee,** kunnen beheerders dit toegangspakket niet rechtstreeks toewijzen.

    ![Toegangspakket - Beleid- Beleidsinstelling inschakelen](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. Klik op **Volgende**.
