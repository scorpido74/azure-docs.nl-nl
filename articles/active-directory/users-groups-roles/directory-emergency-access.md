---
title: Beheer beheer beheer van beheerbeheeraccounts voor noodgevallen - Azure AD | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u noodtoegangsaccounts gebruiken om te voorkomen dat u per ongeluk wordt uitgesloten van uw Azure AD-organisatie (Azure AD) van Azure Directory.
services: active-directory
author: markwahl-msft
manager: daveba
ms.author: curtand
ms.date: 11/08/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80ab7e0603f63fb395832b0da887916dc032c3bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74028131"
---
# <a name="manage-emergency-access-accounts-in-azure-ad"></a>Noodtoegangsaccounts beheren in Azure AD

Het is belangrijk dat u voorkomt dat u per ongeluk wordt uitgesloten van uw Azure Active Directory-organisatie (Azure AD) omdat u het account van een andere gebruiker niet aanmelden of activeren als beheerder. U de impact van onbedoeld gebrek aan beheerderstoegang beperken door twee of meer *noodtoegangsaccounts* in uw organisatie te maken.

Noodtoegangsaccounts zijn zeer geprivilegieerd en worden niet toegewezen aan specifieke personen. Noodtoegangsaccounts zijn beperkt tot nood- of 'break glass'-scenario's waarbij normale administratieve accounts niet kunnen worden gebruikt. We raden u aan een doel te handhaven om het gebruik van noodaccounten te beperken tot alleen de tijden waarop dit absoluut noodzakelijk is.

In dit artikel vindt u richtlijnen voor het beheren van noodtoegangsaccounts in Azure AD.

## <a name="why-use-an-emergency-access-account"></a>Waarom een noodtoegangsaccount gebruiken

Een organisatie moet mogelijk in de volgende situaties een noodtoegangsaccount gebruiken:

- De gebruikersaccounts zijn gefedereerd en federatie is momenteel niet beschikbaar vanwege een onderbreking van het mobiele netwerk of een uitval van de identiteitsprovider. Als de host van de identiteitsprovider in uw omgeving bijvoorbeeld is gedaald, kunnen gebruikers zich mogelijk niet aanmelden wanneer Azure AD wordt omgeleid naar hun identiteitsprovider.
- De beheerders zijn geregistreerd via Azure Multi-Factor Authentication en al hun afzonderlijke apparaten zijn niet beschikbaar of de service is niet beschikbaar. Gebruikers kunnen multifactorverificatie mogelijk niet voltooien om een rol te activeren. Een storing in het mobiele netwerk verhindert bijvoorbeeld dat ze telefoongesprekken kunnen beantwoorden of sms-berichten kunnen ontvangen, de enige twee verificatiemechanismen die ze voor hun apparaat hebben geregistreerd.
- De persoon met de meest recente toegang tot globale beheerders heeft de organisatie verlaten. Azure AD voorkomt dat het laatste Globale Administrator-account wordt verwijderd, maar voorkomt niet dat het account on-premises wordt verwijderd of uitgeschakeld. In beide situaties kan de organisatie het account niet kunnen herstellen.
- Onvoorziene omstandigheden zoals een noodsituatie bij natuurrampen, waarbij een mobiele telefoon of andere netwerken mogelijk niet beschikbaar zijn. 

## <a name="create-emergency-access-accounts"></a>Accounts voor noodtoegang maken

Maak twee of meer noodtoegangsaccounts. Deze accounts moeten alleen in de \*cloud accounts zijn die het .onmicrosoft.com-domein gebruiken en die niet worden gefedeerd of gesynchroniseerd vanuit een on-premises omgeving.

Bij het configureren van deze accounts moet aan de volgende vereisten worden voldaan:

- De noodtoegangsaccounts mogen niet worden gekoppeld aan een individuele gebruiker in de organisatie. Zorg ervoor dat uw accounts niet zijn gekoppeld aan mobiele telefoons die door werknemers zijn geleverd, hardwaretokens die reizen met individuele werknemers of andere werknemersspecifieke referenties. Deze voorzorgsmaatregel heeft betrekking op gevallen waarin een individuele werknemer onbereikbaar is wanneer de referentie nodig is. Het is belangrijk om ervoor te zorgen dat geregistreerde apparaten worden bewaard op een bekende, veilige locatie die meerdere middelen heeft om te communiceren met Azure AD.
- Het verificatiemechanisme dat wordt gebruikt voor een account voor noodtoegang moet verschillen van het verificatiemechanisme dat wordt gebruikt door uw andere beheerdersaccounts, waaronder andere accounts voor noodtoegang.  Als uw normale administratoraanmelding bijvoorbeeld via on-premises MFA is, is Azure MFA een ander mechanisme.  Als Azure MFA echter het primaire onderdeel van de verificatie voor uw beheerdersaccounts is, moet u overwegen om hiervoor een andere benadering te hanteren, zoals het gebruik van voorwaardelijke toegang met een MFA-provider van derden.
- Het apparaat of de referentie mag niet verlopen of in het bereik van geautomatiseerde opruiming als gevolg van gebrek aan gebruik.  
- U moet de roltoewijzing globale beheerder permanent maken voor uw noodtoegangsaccounts. 

### <a name="exclude-at-least-one-account-from-phone-based-multi-factor-authentication"></a>Ten minste één account uitsluiten van telefonische multifactorauthenticatie

Om het risico op een aanval als gevolg van een gecompromitteerd wachtwoord te verminderen, raadt Azure AD u aan multifactorauthenticatie voor alle individuele gebruikers te vereisen. Deze groep omvat beheerders en alle anderen (bijvoorbeeld financiële functionarissen) waarvan de gecompromitteerde account een aanzienlijke impact zou hebben.

Ten minste één van uw noodtoegangsaccounts mag echter niet hetzelfde multi-factor authenticatiemechanisme hebben als uw andere niet-noodaccounts. Dit omvat multifactorauthenticatieoplossingen van derden. Als u een beleid voor voorwaardelijke toegang hebt om [multifactorauthenticatie voor elke beheerder](../authentication/howto-mfa-userstates.md) voor Azure AD en andere verbonden software-as-a-service-apps (SaaS) te vereisen, moet u noodtoegangsaccounts uitsluiten van deze vereiste en in plaats daarvan een ander mechanisme configureren. Bovendien moet u ervoor zorgen dat de accounts geen multi-factor authenticatiebeleid per gebruiker hebben.

### <a name="exclude-at-least-one-account-from-conditional-access-policies"></a>Ten minste één account uitsluiten van beleid voor voorwaardelijke toegang

Tijdens een noodsituatie wilt u niet dat een beleid uw toegang om een probleem op te lossen mogelijk blokkeert. Ten minste één account voor noodtoegang moet worden uitgesloten van alle beleid voor voorwaardelijke toegang. Als u een [basislijnbeleid](../conditional-access/baseline-protection.md)hebt ingeschakeld, moet u uw noodtoegangsaccounts uitsluiten.

## <a name="federation-guidance"></a>Federatiebegeleiding

Een extra optie voor organisaties die AD Domain Services en ADFS of vergelijkbare identiteitsprovider gebruiken om te reageren op Azure AD, is het configureren van een account voor noodtoegang waarvan de MFA-claim door die identiteitsprovider kan worden geleverd.  Het account voor noodtoegang kan bijvoorbeeld worden ondersteund door een certificaat en sleutelpaar, zoals een account dat is opgeslagen op een smartcard.  Wanneer die gebruiker is geverifieerd naar AD, kan ADFS een claim aan Azure AD leveren, waaruit blijkt dat de gebruiker aan de MFA-vereisten heeft voldaan.  Zelfs met deze aanpak moeten organisaties nog steeds cloudgebaseerde noodtoegangsaccounts hebben voor het geval federatie niet kan worden ingesteld. 

## <a name="store-account-credentials-safely"></a>Accountgegevens veilig opslaan

Organisaties moeten ervoor zorgen dat de referenties voor noodtoegangsaccounts alleen worden beveiligd en alleen bekend zijn bij personen die gemachtigd zijn om ze te gebruiken. Sommige klanten gebruiken een smartcard en anderen gebruiken wachtwoorden. Een wachtwoord voor een noodtoegangsaccount wordt meestal gescheiden in twee of drie delen, geschreven op afzonderlijke stukken papier en opgeslagen in veilige, brandwerende kluizen die zich op veilige, afzonderlijke locaties bevinden.

Als u wachtwoorden gebruikt, moet u ervoor zorgen dat de accounts sterke wachtwoorden hebben die het wachtwoord niet verlopen. Idealiter moeten de wachtwoorden ten minste 16 tekens lang en willekeurig gegenereerd.

## <a name="monitor-sign-in-and-audit-logs"></a>Aanmeldings- en controlelogboeken controleren

Organisaties moeten de aanmeldings- en controlelogboekactiviteit van de noodaccounts controleren en meldingen naar andere beheerders activeren. Wanneer u de activiteit op break glass-accounts controleert, u controleren of deze accounts alleen worden gebruikt voor het testen of noodsituaties. U Azure Log Analytics gebruiken om de aanmeldingslogboeken te controleren en e-mail- en sms-waarschuwingen naar uw beheerders te activeren wanneer u glazen accounts aanmeldt.

### <a name="prerequisites"></a>Vereisten

1. [Azure AD-aanmeldingslogboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics) naar Azure Monitor verzenden.

### <a name="obtain-object-ids-of-the-break-glass-accounts"></a>Object-iD's van de breakglass-accounts verkrijgen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een account dat is toegewezen aan de functie Gebruikersbeheerder.
1. Selecteer **Azure Active Directory** > **Users**.
1. Zoek naar het break-glass-account en selecteer de naam van de gebruiker.
1. Kopieer en sla het kenmerk Object-ID op, zodat u het later gebruiken.
1. Herhaal eerdere stappen voor een tweede break-glass-account.

### <a name="create-an-alert-rule"></a>Een waarschuwingsregel maken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een account dat is toegewezen aan de rol Monitorbijdrage bewaken in Azure Monitor.
1. Selecteer **Alle services",** typ "log analytics" in Zoeken en selecteer **vervolgens Log Analytics-werkruimten**.
1. Selecteer een werkruimte.
1. Selecteer in uw werkruimte de**nieuwe waarschuwingsregel** **waarschuwingen** > .
    1. Controleer **onder Resource**of het abonnement het abonnement is waarmee u de waarschuwingsregel wilt koppelen.
    1. Selecteer **Onder Voorwaarde**de optie **Toevoegen**.
    1. Selecteer **Aangepaste logboekzoekopdracht** onder **De naam van het signaal**.
    1. Voer **onder Zoekopdracht**de volgende query in, waarbij de object-id's van de twee breakglass-accounts worden ingevoegd.
        > [!NOTE]
        > Voeg voor elk extra breakglass-account dat u wilt opnemen een andere "of UserId== "ObjectGuid"" toe aan de query.

        ![De object-geïdentificeerde accounts van de breukglasaccounts toevoegen aan een waarschuwingsregel](./media/directory-emergency-access/query-image1.png)

    1. Voer **onder Waarschuwingslogica**het volgende in:

        - Gebaseerd op: Aantal resultaten
        - Operator: Groter dan
        - Drempelwaarde: 0

    1. Selecteer **onder Geëvalueerd op basis**van : Selecteer de periode **(in minuten)** voor hoe lang u de query wilt uitvoeren en de **frequentie (in minuten)** voor hoe vaak u de query wilt uitvoeren. De frequentie moet lager zijn dan of gelijk zijn aan de periode.

        ![waarschuwingslogica](./media/directory-emergency-access/alert-image2.png)

    1. Selecteer **Done**. U nu de geschatte maandelijkse kosten van deze waarschuwing bekijken.
1. Selecteer een actiegroep van gebruikers die door de waarschuwing op de hoogte moet worden gesteld. Zie [Een actiegroep maken](#create-an-action-group)als u er een wilt maken.
1. Als u de e-mailmelding wilt aanpassen die naar de leden van de actiegroep is verzonden, selecteert u acties onder **Acties aanpassen**.
1. Geef **onder Waarschuwingsgegevens**de naam van de waarschuwingsregel op en voeg een optionele beschrijving toe.
1. Stel het **ernstniveau** van de gebeurtenis in. We raden u aan deze in te stellen op **Kritiek(Sev 0).**
1. Onder **Regel inschakelen bij het maken,** laat deze instellen als **ja**.
1. Als u waarschuwingen een tijdje wilt uitschakelen, schakelt u het selectievakje **Waarschuwingen onderdrukken** in en voert u de wachttijd in voordat u opnieuw waarschuwt en selecteert u **Opslaan**.
1. Klik **op Waarschuwingsregel maken**.

### <a name="create-an-action-group"></a>Een actiegroep maken

1. Selecteer **Een actiegroep maken**.

    ![een actiegroep voor meldingsacties maken](./media/directory-emergency-access/action-group-image3.png)

1. Voer de naam van de actiegroep en een korte naam in.
1. Controleer de abonnements- en brongroep.
1. Selecteer onder actietype **E-mail/SMS/Push/Voice**.
1. Voer een actienaam in, zoals **Globale beheerder melden**.
1. Selecteer het **actietype** als **e-mail/sms/push/spraak**.
1. Selecteer **Details bewerken** om de meldingsmethoden te selecteren die u wilt configureren en voer de vereiste contactgegevens in en selecteer **Ok** om de details op te slaan.
1. Voeg eventuele extra acties toe die u wilt activeren.
1. Selecteer **OK**.

## <a name="validate-accounts-regularly"></a>Accounts regelmatig valideren

Wanneer u medewerkers traint om noodtoegangsaccounts te gebruiken en de noodtoegangsaccounts te valideren, volgt u op gezette tijden de volgende stappen:

- Zorg ervoor dat beveiligingsmedewerkers zich ervan bewust zijn dat de accountcontroleactiviteit aan de gang is.
- Zorg ervoor dat het noodpauzeglasproces om deze accounts te gebruiken gedocumenteerd en actueel is.
- Zorg ervoor dat beheerders en beveiligingsfunctionarissen die deze stappen mogelijk moeten uitvoeren tijdens een noodsituatie, worden getraind in het proces.
- Werk de accountgegevens, met name wachtwoorden, bij voor uw noodtoegangsaccounts en valideer vervolgens dat de noodtoegangsaccounts zich kunnen aanmelden en administratieve taken kunnen uitvoeren.
- Zorg ervoor dat gebruikers multi-factorauthenticatie of selfservicewachtwoordreset (SSPR) niet hebben geregistreerd op het apparaat of persoonlijke gegevens van een individuele gebruiker. 
- Als de accounts zijn geregistreerd voor multifactorauthenticatie op een apparaat, voor gebruik tijdens het aanmelden of functieactivering, moet u ervoor zorgen dat het apparaat toegankelijk is voor alle beheerders die het mogelijk moeten gebruiken tijdens een noodsituatie. Controleer ook of het apparaat kan communiceren via ten minste twee netwerkpaden die geen algemene foutmodus delen. Het apparaat kan bijvoorbeeld communiceren met het internet via zowel het draadloze netwerk van een faciliteit als een netwerk van mobiele provideren.

Deze stappen moeten regelmatig worden uitgevoerd en voor belangrijke wijzigingen:

- Ten minste elke 90 dagen
- Wanneer er onlangs een verandering is in IT-personeel, zoals een baanwijziging, een vertrek of een nieuwe
- Wanneer de Azure AD-abonnementen in de organisatie zijn gewijzigd

## <a name="next-steps"></a>Volgende stappen

- [Bevoegde toegang beveiligen voor hybride implementaties en cloudimplementaties in Azure AD](directory-admin-roles-secure.md)
- [Gebruikers toevoegen met Azure AD](../fundamentals/add-users-azure-active-directory.md) en de nieuwe gebruiker toewijzen aan de rol Globale [beheerder](../fundamentals/active-directory-users-assign-role-azure-portal.md)
- [Aanmelden voor Azure AD Premium](../fundamentals/active-directory-get-started-premium.md), als u zich nog niet hebt aangemeld
- [Verificatie in twee stappen voor een gebruiker vereisen](../authentication/howto-mfa-userstates.md)
- [Aanvullende beveiligingen configureren voor globale beheerders in Office 365](https://docs.microsoft.com/office365/enterprise/protect-your-global-administrator-accounts), als u Office 365 gebruikt
- [Start een toegangscontrole van globale beheerders](../privileged-identity-management/pim-how-to-start-security-review.md) en breng bestaande globale beheerders over naar meer specifieke [beheerdersrollen](directory-assign-admin-roles.md)
