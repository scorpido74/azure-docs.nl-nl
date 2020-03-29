---
title: Rapporten voor het opnieuw instellen van selfservicewachtwoorden - Azure Active Directory
description: Rapportage over gebeurtenissen voor het opnieuw instellen van azure AD-zelfservicewachtwoorden
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27d219f46f82f4a74cb5dee0a6b11b673a4a3509
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76155004"
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Reporting options for Azure AD password management (Rapportageopties voor Azure AD-wachtwoordbeheer)

Na de implementatie willen veel organisaties weten hoe of of selfservice wachtwoordreset (SSPR) echt wordt gebruikt. De rapportagefunctie die Azure Active Directory (Azure AD) biedt, helpt u vragen te beantwoorden met vooraf gebouwde rapporten. Als u een licentie hebt, u ook aangepaste query's maken.

![Rapportage over SSPR met behulp van de controlelogboeken in Azure AD][Reporting]

De volgende vragen kunnen worden beantwoord door de rapporten die in de [Azure-portal](https://portal.azure.com/)aanwezig zijn:

> [!NOTE]
> U moet [een globale beheerder](../users-groups-roles/directory-assign-admin-roles.md)zijn en u moet zich aanmelden voor deze gegevens die namens uw organisatie moeten worden verzameld. Als u zich wilt aanmelden, moet u ten minste één keer het tabblad **Rapportage** of de controlelogboeken bezoeken. Tot die tijd worden er geen gegevens verzameld voor uw organisatie.
>

* Hoeveel mensen hebben zich geregistreerd voor het opnieuw instellen van het wachtwoord?
* Wie heeft zich geregistreerd voor het opnieuw instellen van het wachtwoord?
* Welke gegevens registreren mensen?
* Hoeveel mensen resetten hun wachtwoorden in de afgelopen zeven dagen?
* Wat zijn de meest voorkomende methoden die gebruikers of beheerders gebruiken om hun wachtwoorden opnieuw in te stellen?
* Wat zijn veelvoorkomende problemen waarmee gebruikers of beheerders worden geconfronteerd wanneer ze proberen het wachtwoord opnieuw in te stellen?
* Welke beheerders stellen hun eigen wachtwoorden regelmatig opnieuw in?
* Is er een verdachte activiteit gaande met wachtwoord reset?

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>How to view Password Management Reports (Rapporten voor wachtwoordbeheer bekijken in Azure Portal)

In de Azure-portalervaring hebben we de manier verbeterd waarop u de registratieactiviteit voor het opnieuw instellen van wachtwoorden en het opnieuw instellen van wachtwoorden weergeven. Gebruik de volgende stappen om de registratiegebeurtenissen voor het opnieuw instellen van wachtwoorden en het opnieuw instellen van wachtwoorden te vinden:

1. Blader naar [Azure Portal](https://portal.azure.com).
2. Selecteer **Alle services** in het linkerdeelvenster.
3. Zoek naar **Azure Active Directory** in de lijst met services en selecteer deze.
4. Selecteer **Gebruikers** in de sectie Beheren.
5. Selecteer **Controlelogboeken** in **het** gebruikersblad. Dit toont u alle controlegebeurtenissen die zich hebben voorgedaan tegen alle gebruikers in uw directory. U deze weergave filteren om alle wachtwoordgerelateerde gebeurtenissen te bekijken.
6. Selecteer in het menu **Filter** boven aan het deelvenster de vervolgkeuzelijst **Service** en wijzig deze in het servicetype **Selfservice Password Management.**
7. De lijst eventueel verder filteren door de specifieke **activiteit te** kiezen waarin u geïnteresseerd bent.

### <a name="converged-registration-preview"></a>Geconvergeerde registratie (voorbeeld)

Als u deelneemt aan de openbare preview van geconvergeerde registratie, wordt informatie over gebruikersactiviteit in de controlelogboeken gevonden onder > **beveiligingsverificatiemethoden.** **Security**

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Beschrijving van de rapportkolommen in de Azure-portal

In de volgende lijst wordt elk van de rapportkolommen in de Azure-portal in detail uitgelegd:

* **Gebruiker:** de gebruiker die een registratiebewerking voor het opnieuw instellen van het wachtwoord heeft geprobeerd.
* **Rol**: De rol van de gebruiker in de map.
* **Datum en tijd:** de datum en het tijdstip van de poging.
* **Gegevens geregistreerd:** de verificatiegegevens die de gebruiker heeft verstrekt tijdens de registratie van het opnieuw instellen van wachtwoorden.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>Beschrijving van de rapportwaarden in de Azure-portal

In de volgende tabel worden de verschillende waarden beschreven die u voor elke kolom in de Azure-portal instellen:

| Kolom | Toegestane waarden en hun betekenissen |
| --- | --- |
| Geregistreerde gegevens |**Alternatieve e-mail:** de gebruiker heeft een alternatieve e-mail of verificatie-e-mail gebruikt om te verifiëren.<p><p>**Office-telefoon**: De gebruiker heeft een kantoortelefoon gebruikt om te verifiëren.<p>**Mobiele telefoon**: De gebruiker gebruikte een mobiele telefoon of verificatietelefoon om te verifiëren.<p>**Beveiligingsvragen:** De gebruiker heeft beveiligingsvragen gebruikt om te verifiëren.<p>**Elke combinatie van de vorige methoden, bijvoorbeeld alternatieve e-mail + mobiele telefoon:** Treedt op wanneer een twee-gate beleid is opgegeven en geeft aan welke twee methoden de gebruiker gebruikt om authenticatie hun wachtwoord reset verzoek. |

## <a name="self-service-password-management-activity-types"></a>Activiteitstypen voor selfservicewachtwoordbeheer

De volgende activiteitstypen worden weergegeven in de categorie **selfservice-wachtwoordbeheer-controlegebeurtenis:**

* [Geblokkeerd voor selfservice wachtwoord reset:](#activity-type-blocked-from-self-service-password-reset)Geeft aan dat een gebruiker geprobeerd heeft om een wachtwoord opnieuw in te stellen, een specifieke poort te gebruiken of een telefoonnummer meer dan vijf keer in 24 uur te valideren.
* [Wachtwoord wijzigen (selfservice):](#activity-type-change-password-self-service)geeft aan dat een gebruiker een vrijwillige of gedwongen (wegens verlopen) wachtwoordwijziging heeft uitgevoerd.
* [Wachtwoord opnieuw instellen (per beheerder):](#activity-type-reset-password-by-admin)geeft aan dat een beheerder namens een gebruiker van de Azure-portal een wachtwoordreset heeft uitgevoerd.
* [Wachtwoord opnieuw instellen (selfservice):](#activity-type-reset-password-self-service)geeft aan dat een gebruiker zijn wachtwoord opnieuw instelt vanaf de [azure AD-wachtwoordresetportal.](https://passwordreset.microsoftonline.com)
* Voortgang van de voortgang van de [wachtwoordresetvan](#activity-type-self-serve-password-reset-flow-activity-progress)het wachtwoord: geeft elke specifieke stap aan die een gebruiker doorgeeft, zoals het passeren van een specifieke verificatiepoort voor wachtwoordreset, als onderdeel van het wachtwoordresetproces.
* [Gebruikersaccount ontgrendelen (selfservice)](#activity-type-unlock-a-user-account-self-service)): geeft aan dat een gebruiker zijn Active Directory-account heeft ontgrendeld zonder zijn wachtwoord opnieuw in te stellen via de [Azure AD-wachtwoordresetportal](https://passwordreset.microsoftonline.com) door de Active Directory-functie van accountontgrendeling zonder opnieuw te resetten.
* [Gebruiker geregistreerd voor self-service wachtwoord reset:](#activity-type-user-registered-for-self-service-password-reset)Geeft aan dat een gebruiker alle vereiste informatie heeft geregistreerd om zijn wachtwoord opnieuw in te kunnen zetten in overeenstemming met het momenteel opgegeven tenant wachtwoordresetbeleid.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Activiteitstype: geblokkeerd voor het opnieuw instellen van selfservicewachtwoorden

In de volgende lijst wordt deze activiteit in detail uitgelegd:

* **Activiteitsbeschrijving:** geeft aan dat een gebruiker heeft geprobeerd een wachtwoord opnieuw in te stellen, een specifieke poort te gebruiken of een telefoonnummer in 24 uur in totaal meer dan vijf keer te valideren.
* **Activiteitsactor:** de gebruiker die is beperkt door het uitvoeren van extra resetbewerkingen. De gebruiker kan een eindgebruiker of beheerder zijn.
* **Activiteitsdoel:** de gebruiker die werd beperkt door het uitvoeren van extra resetbewerkingen. De gebruiker kan een eindgebruiker of beheerder zijn.
* **Activiteitsstatus**:
  * _Succes:_ geeft aan dat een gebruiker is beperkt door het uitvoeren van extra resets, het proberen van extra verificatiemethoden of het valideren van extra telefoonnummers voor de komende 24 uur.
* **Reden voor het mislukken van de activiteitsstatus:** niet van toepassing.

### <a name="activity-type-change-password-self-service"></a>Activiteitstype: wachtwoord wijzigen (selfservice)

In de volgende lijst wordt deze activiteit in detail uitgelegd:

* **Activiteitsbeschrijving:** geeft aan dat een gebruiker een vrijwillige of gedwongen wachtwoordwijziging heeft uitgevoerd (als gevolg van het verlopen).
* **Activiteitsactor**: de gebruiker die zijn wachtwoord heeft gewijzigd. De gebruiker kan een eindgebruiker of beheerder zijn.
* **Activiteitsdoel:** de gebruiker die zijn wachtwoord heeft gewijzigd. De gebruiker kan een eindgebruiker of beheerder zijn.
* **Activiteitsstatussen**:
  * _Succes:_ geeft aan dat een gebruiker zijn wachtwoord heeft gewijzigd.
  * _Fout:_ geeft aan dat een gebruiker zijn wachtwoord niet kan wijzigen. U de rij selecteren om de **redencategorie Activiteitsstatus** te bekijken om meer te weten te komen over de reden van de fout.
* **Reden voor het mislukken van de activiteitsstatus:**
  * _FuzzyPolicyViolationInvalidPassword_: De gebruiker selecteerde een wachtwoord dat automatisch werd verboden omdat de Microsoft Banned Password Detection-mogelijkheden het te vaak of vooral zwak vonden.

### <a name="activity-type-reset-password-by-admin"></a>Activiteitstype: wachtwoord opnieuw instellen (per beheerder)

In de volgende lijst wordt deze activiteit in detail uitgelegd:

* **Activiteitsbeschrijving:** geeft aan dat een beheerder namens een gebruiker van de Azure-portal een wachtwoordreset heeft uitgevoerd.
* **Activiteitsactor:** de beheerder die de wachtwoordreset heeft uitgevoerd namens een andere eindgebruiker of beheerder. Moet een wachtwoordbeheerder, gebruikersbeheerder of helpdeskbeheerder zijn.
* **Activiteitsdoel:** de gebruiker wiens wachtwoord is gereset. De gebruiker kan een eindgebruiker of een andere beheerder zijn.
* **Activiteitsstatussen**:
  * _Succes:_ geeft aan dat een beheerder het wachtwoord van een gebruiker opnieuw heeft ingesteld.
  * _Fout:_ geeft aan dat een beheerder het wachtwoord van een gebruiker niet kan wijzigen. U de rij selecteren om de **redencategorie Activiteitsstatus** te bekijken om meer te weten te komen over de reden van de fout.

### <a name="activity-type-reset-password-self-service"></a>Activiteitstype: Wachtwoord opnieuw instellen (selfservice)

In de volgende lijst wordt deze activiteit in detail uitgelegd:

* **Activiteitsbeschrijving:** geeft aan dat een gebruiker zijn wachtwoord opnieuw heeft ingesteld vanuit de [azure AD-wachtwoordresetportal](https://passwordreset.microsoftonline.com).
* **Activiteitsactor:** de gebruiker die zijn wachtwoord opnieuw instelt. De gebruiker kan een eindgebruiker of beheerder zijn.
* **Activiteitsdoel:** de gebruiker die zijn wachtwoord opnieuw instelt. De gebruiker kan een eindgebruiker of beheerder zijn.
* **Activiteitsstatussen**:
  * _Succes_: geeft aan dat een gebruiker zijn eigen wachtwoord opnieuw instelt.
  * _Fout:_ geeft aan dat een gebruiker zijn eigen wachtwoord niet opnieuw kan instellen. U de rij selecteren om de **redencategorie Activiteitsstatus** te bekijken om meer te weten te komen over de reden van de fout.
* **Reden voor het mislukken van de activiteitsstatus:**
  * _FuzzyPolicyViolationInvalidPassword_: De beheerder selecteerde een wachtwoord dat automatisch werd geblokkeerd omdat de Microsoft Banned Password Detection-mogelijkheden het te vaak of vooral zwak vonden.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Activiteitstype: voortgang van de voortgang van de wachtwoordterugloop

In de volgende lijst wordt deze activiteit in detail uitgelegd:

* **Activiteitsbeschrijving**: Geeft elke specifieke stap aan die een gebruiker doorgeeft (zoals het passeren van een specifieke verificatiepoort voor wachtwoordreset) als onderdeel van het wachtwoordresetproces.
* **Activiteitsactor:** de gebruiker die een deel van de wachtwoordresetstroom heeft uitgevoerd. De gebruiker kan een eindgebruiker of beheerder zijn.
* **Activiteitsdoel:** de gebruiker die een deel van de wachtwoordresetstroom heeft uitgevoerd. De gebruiker kan een eindgebruiker of beheerder zijn.
* **Activiteitsstatussen**:
  * _Succes:_ geeft aan dat een gebruiker een specifieke stap van de wachtwoordresetstroom heeft voltooid.
  * _Fout:_ geeft aan dat een specifieke stap van de wachtwoordresetstroom is mislukt. U de rij selecteren om de **redencategorie Activiteitsstatus** te bekijken om meer te weten te komen over de reden van de fout.
* Redenen voor de **activiteitsstatus**: Zie de volgende tabel voor [alle redenen van de toegestane resetactiviteitsstatus](#description-of-the-report-columns-in-the-azure-portal).

### <a name="activity-type-unlock-a-user-account-self-service"></a>Activiteitstype: een gebruikersaccount ontgrendelen (selfservice)

In de volgende lijst wordt deze activiteit in detail uitgelegd:

* **Activiteitsbeschrijving:** geeft aan dat een gebruiker zijn Active Directory-account heeft ontgrendeld zonder zijn wachtwoord opnieuw in te stellen via de [azure AD-wachtwoordresetportal](https://passwordreset.microsoftonline.com) door de Active Directory-functie van accountontgrendeling zonder opnieuw te resetten.
* **Activiteitsactor**: de gebruiker die zijn account heeft ontgrendeld zonder zijn wachtwoord opnieuw in te stellen. De gebruiker kan een eindgebruiker of beheerder zijn.
* **Activiteitsdoel:** de gebruiker die zijn account heeft ontgrendeld zonder zijn wachtwoord opnieuw in te stellen. De gebruiker kan een eindgebruiker of beheerder zijn.
* **Toegestane activiteitsstatussen:**
  * _Succes_: Geeft aan dat een gebruiker met succes zijn eigen account heeft ontgrendeld.
  * _Fout:_ geeft aan dat een gebruiker zijn account niet heeft ontgrendeld. U de rij selecteren om de **redencategorie Activiteitsstatus** te bekijken om meer te weten te komen over de reden van de fout.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Activiteitstype: gebruiker geregistreerd voor selfservice wachtwoord reset

In de volgende lijst wordt deze activiteit in detail uitgelegd:

* **Activiteitsbeschrijving**: Geeft aan dat een gebruiker alle vereiste informatie heeft geregistreerd om zijn wachtwoord opnieuw in te kunnen stellen in overeenstemming met het momenteel opgegeven beleid voor het opnieuw instellen van het tenantwachtwoord. 
* **Activiteitsactor**: de gebruiker die zich heeft geregistreerd voor het opnieuw instellen van het wachtwoord. De gebruiker kan een eindgebruiker of beheerder zijn.
* **Activiteitsdoel:** de gebruiker die zich heeft geregistreerd voor het opnieuw instellen van het wachtwoord. De gebruiker kan een eindgebruiker of beheerder zijn.
* **Toegestane activiteitsstatussen:**
  * _Succes_: geeft aan dat een gebruiker zich heeft geregistreerd voor het opnieuw instellen van wachtwoorden in overeenstemming met het huidige beleid. 
  * _Fout:_ geeft aan dat een gebruiker zich niet kan registreren voor het opnieuw instellen van het wachtwoord. U de rij selecteren om de **redencategorie Activiteitsstatus** te bekijken om meer te weten te komen over de reden van de fout.

     >[!NOTE]
     >Als u niet mislukt, kan een gebruiker zijn eigen wachtwoord niet opnieuw instellen. Het betekent dat ze het registratieproces niet hebben voltooid. Als er niet-geverifieerde gegevens op hun account staan die juist zijn, zoals een telefoonnummer dat niet is gevalideerd, ook al hebben ze dit telefoonnummer niet geverifieerd, kunnen ze het nog steeds gebruiken om hun wachtwoord opnieuw in te stellen.

## <a name="next-steps"></a>Volgende stappen

* [SSPR- en MFA-gebruik en inzichtenrapportage](howto-authentication-methods-usage-insights.md)
* [Hoe kan ik een geslaagde implementatie van SSPR voltooien?](howto-sspr-deployment.md)
* [Uw wachtwoord opnieuw instellen of wijzigen](../user-help/active-directory-passwords-update-your-own-password.md).
* [Registreer voor self-service wachtwoord reset](../user-help/active-directory-passwords-reset-register.md).
* [Hebt u een vraag over licenties?](concept-sspr-licensing.md)
* [Welke gegevens worden gebruikt door selfservice voor wachtwoordherstel en welke gegevens moet u voor uw gebruikers invullen?](howto-sspr-authenticationdata.md)
* [Welke verificatiemethoden zijn beschikbaar voor gebruikers?](concept-sspr-howitworks.md#authentication-methods)
* [Wat zijn de beleidsopties bij selfservice voor wachtwoordherstel?](concept-sspr-policy.md)
* [Wat is Wachtwoord terugschrijven en waarom is dit van belang?](howto-sspr-writeback.md)
* [Wat zijn alle opties in selfservice voor wachtwoordherstel en wat houden ze in?](concept-sspr-howitworks.md)
* [Ik denk dat er iets kapot is. Hoe los ik SSPR op?](active-directory-passwords-troubleshoot.md)
* [Ik heb een vraag die nog niet is beantwoord](active-directory-passwords-faq.md)

[Reporting]: ./media/howto-sspr-reporting/sspr-reporting.png "Voorbeeld van sspr-activiteitscontrolelogboeken in Azure AD"
