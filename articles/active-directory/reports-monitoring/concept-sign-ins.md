---
title: Aanmeldactiviteitenrapporten in Azure Active Directory Portal | Microsoft Docs
description: Ontdek de aanmeldactiviteitenrapporten in de Azure Active Directory Portal
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 12/09/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc996c7b5d8a63834f548689c83f7a72685120d2
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951170"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Aanmeldactiviteitenrapporten in Azure Active Directory Portal

De rapportage architectuur in Azure Active Directory (Azure AD) bestaat uit de volgende onderdelen:

- **Activiteit** 
    - **Aanmeldingen** : informatie over het gebruik van beheerde toepassingen en aanmeldings activiteiten voor gebruikers.
    - **Audit logboeken** - [audit logboeken](concept-audit-logs.md) bevatten informatie over de systeem activiteit van gebruikers en groeps beheer, beheerde toepassingen en Directory-activiteiten.
- **Beveiliging** 
    - **Risk ante aanmeldingen** : een [Risk ante aanmelding](concept-risky-sign-ins.md) is een indicator voor een aanmeldings poging door iemand die niet de rechtmatige eigenaar van een gebruikers account is.
    - **Gebruikers die zijn gemarkeerd voor risico** : een [Risk ante gebruiker](concept-user-at-risk.md) is een indicator voor een gebruikers account dat mogelijk is aangetast.

In dit artikel vindt u een overzicht van het rapport met aanmeldingen.

## <a name="prerequisites"></a>Vereisten

### <a name="who-can-access-the-data"></a>Wie heeft er toegang tot de gegevens?

* Gebruikers in de beveiligings beheerder, beveiligings lezer, globale lezer en rapport lezers rollen
* Globale beheerders
* Alle gebruiker (niet-beheerders) hebben toegang tot hun eigen aanmeldingen 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Welke Azure AD-licentie heb ik nodig voor toegang tot aanmeldingsactiviteiten?

* Uw tenant moet beschikken over een eraan gekoppelde Azure AD Premium-licentie om het complete rapport van aanmeldingsactiviteiten te kunnen zien. Zie [Aan de slag met Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) om uw versie van Azure Active Directory te upgraden. Het duurt enkele dagen voordat de gegevens in de rapporten worden weer gegeven nadat u een upgrade hebt uitgevoerd naar een Premium-licentie zonder gegevens activiteiten vóór de upgrade.

## <a name="sign-ins-report"></a>Aanmeldingenrapport

Het rapport gebruikers aanmeldingen bevat antwoorden op de volgende vragen:

* Wat is het aanmeldingspatroon van een gebruiker?
* Hoeveel gebruikers hebben zich gedurende een week aangemeld?
* Wat is de status van deze aanmeldingen?

Begin met [Azure Portal](https://portal.azure.com). Als u het rapport aanmeldingen wilt openen, selecteert u **aanmeldingen**en gaat u verder met de **bewaking.** Het kan tot twee uur duren voordat sommige registratie records worden weer gegeven in de portal.

![Aanmeldings activiteit](./media/concept-sign-ins/reporting-azure-sign-in-screen.png "Aanmeldingsactiviteit")

> [!IMPORTANT]
> Het rapport met aanmeldingen bevat alleen de **interactieve** aanmeldingen, dat wil zeggen, aanmeldingen waarbij een gebruiker zich hand matig aanmeldt met de gebruikers naam en het wacht woord. Niet-interactieve aanmeldingen, zoals service-naar-service-verificatie, worden niet weer gegeven in het rapport aanmeldingen. 

Een aanmeldingslogboek heeft een standaardlijstweergave die het volgende laat zien:

- De aanmeldingsdatum
- De gerelateerde gebruiker
- De toepassing waarmee de gebruiker zich heeft aangemeld
- De aanmeldingsstatus
- De status van de risicodetectie
- De status van de vereiste voor meervoudige verificatie (MFA)

![Aanmeldings activiteit](./media/concept-sign-ins/sign-in-activity.png "Aanmeldingsactiviteit")

U kunt de lijstweergave aanpassen door te klikken op **Kolommen** op de werkbalk.

![Aanmeldings activiteit](./media/concept-sign-ins/19.png "Aanmeldingsactiviteit")

Het dialoog venster **kolommen** biedt u toegang tot de selecteerbaar kenmerken. In een aanmeldings rapport kunt u alleen kenmerken hebben die geen een-op-veel-relatie hebben met andere kenmerken als kolom.

![Aanmeldings activiteit](./media/concept-sign-ins/columns.png "Aanmeldingsactiviteit")

Selecteer een item in de lijst weergave voor meer gedetailleerde informatie.

![Aanmeldings activiteit](./media/concept-sign-ins/basic-sign-in.png "Aanmeldingsactiviteit")

> [!NOTE]
> Klanten kunnen nu het beleid voor voorwaardelijke toegang oplossen via alle aanmeldings rapporten. Door op het tabblad **voorwaardelijke toegang** voor een aanmeldings record te klikken, kunnen klanten de status van de voorwaardelijke toegang controleren en de Details bekijken van de beleids regels die zijn toegepast op de aanmelding en het resultaat van elk beleid.
> Raadpleeg de [Veelgestelde vragen over ca-informatie in alle aanmeldingen](reports-faq.md#conditional-access)voor meer informatie.



## <a name="filter-sign-in-activities"></a>Aanmeldactiviteiten filteren

U moet eerst de gerapporteerde gegevens beperken tot een niveau dat geschikt is voor u. Ten tweede filtert u aanmeldings gegevens met een datum veld als standaard filter. Azure AD biedt u een breed scala aan extra filters die u kunt instellen.

![Aanmeldings activiteit](./media/concept-sign-ins/04.png "Aanmeldingsactiviteit")

Met het filter **Gebruiker** kunt u de naam of de UPN (User Principal Name) van de gewenste gebruiker opgeven.

Met het filter **Toepassing** kunt u de naam van de gewenste toepassing opgeven.

Met het filter **Aanmeldingsstatus** kunt u selecteren:

- Alles
- Geslaagd
- Fout

Met het filter voor **voorwaardelijke toegang** kunt u de CA-beleids status voor de aanmelding selecteren:

- Alles
- Niet toegepast
- Geslaagd
- Fout

Met het filter **Datum** kunt u een tijdsbestek opgeven voor de geretourneerde gegevens.  
Mogelijke waarden zijn:

- Eén maand
- 7 dagen
- 24 uur
- Aangepast tijdsinterval

Wanneer u een aangepast tijdsbestek selecteert, kunt u een begintijd en eindtijd configureren.

Als u extra velden toevoegt aan uw aanmeldingsweergave, worden deze velden automatisch toegevoegd aan de lijst met filters. Als u bijvoorbeeld het veld **Client-app** aan uw lijst toevoegt, krijgt u ook een andere filteroptie waarmee u de volgende filters kunt instellen:  
![Aanmeldings activiteit](./media/concept-sign-ins/12.png "Aanmeldingsactiviteit")

- **Browser**  
    Dit filter toont alle gebeurtenissen waarbij aanmeldings pogingen zijn gedaan met behulp van browser stromen.
- **Exchange ActiveSync (ondersteund)**  
    Dit filter toont alle aanmeldings pogingen waarbij het protocol Exchange ActiveSync (EAS) is geprobeerd van ondersteunde platforms zoals iOS, Android en Windows Phone.
- **Exchange ActiveSync (niet ondersteund)**  
    Dit filter toont alle aanmeldings pogingen waarbij het EAS-protocol is geprobeerd uit niet-ondersteunde platformen, zoals Linux distributies.
- **Mobile apps-en desktop-clients** Het filter toont alle aanmeldings pogingen die geen browser stromen gebruiken. Bijvoorbeeld mobiele apps vanaf elk platform met behulp van een protocol of van desktop-client-apps, zoals Office op Windows of MacOS.
  
- **Andere clients**
    - **IMAP**  
        Een verouderde e-mailclient die gebruikmaakt van IMAP om e-mail op te halen.
    - **MAPI**  
        Office 2013, waarbij ADAL is ingeschakeld en gebruikmaakt van MAPI.
    - **Oude Office-clients**  
        Office 2013 in de standaard configuratie waarbij ADAL niet is ingeschakeld en gebruikmaakt van MAPI of Office 2016 waarbij ADAL is uitgeschakeld.
    - **POP**  
        Een legacy-mailclient die gebruikmaakt van POP3 om e-mail op te halen.
    - **SMTP**  
        Een verouderde e-mailclient die gebruikmaakt van SMTP om e-mail te verzenden.

## <a name="download-sign-in-activities"></a>Aanmeldactiviteiten downloaden

Klik op de **Download** optie om een CSV-of JSON-bestand te maken van de meest recente 250.000 records. Begin met [het downloaden van de gegevens van de aanmeldingen](quickstart-download-sign-in-report.md) als u wilt werken buiten de Azure Portal.  

![Downloaden](./media/concept-sign-ins/71.png "Downloaden")

> [!IMPORTANT]
> Het aantal records dat u kunt downloaden, is beperkt door het [Bewaar beleid](reference-reports-data-retention.md)voor de Azure Active Directory-rapport.  


## <a name="sign-ins-data-shortcuts"></a>Gegevens snelkoppelingen voor aanmeldingen

Azure AD en de Azure Portal bieden u extra toegangs punten voor het registreren van gegevens:

- Het overzicht van identiteits beveiligings beveiliging
- Gebruikers
- Groepen
- Bedrijfstoepassingen

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Gebruikers aanmeldingen gegevens in identiteits beveiliging beveiligen

De aanmeldings grafiek van gebruikers op de overzichts pagina voor **identiteits beveiligings beveiliging** bevat wekelijkse aggregaties van aanmeldingen. De standaard waarde voor de tijds periode is 30 dagen.

![Aanmeldings activiteit](./media/concept-sign-ins/06.png "Aanmeldingsactiviteit")

Als u in de aanmeldingsgrafiek op een dag klikt, ziet u een overzicht van de aanmeldingsactiviteiten voor die dag.

Elke rij in de lijst met aanmeldingsactiviteiten geeft het volgende weer:

* Wie heeft zich aangemeld?
* Welke toepassing was het aanmeldingsdoel?
* Wat is de status van de aanmelding?
* Wat is de MFA-status van de aanmelding?

Door op een item te klikken, krijgt u meer informatie over de aanmelding:

- Gebruikers-ID
- Gebruiker
- Gebruikersnaam
- Toepassings-id
- Toepassing
- Client
- Locatie
- IP-adres
- Datum
- MFA vereist
- Aanmeldingsstatus

> [!NOTE]
> IP-adressen worden zodanig uitgegeven dat er geen definitieve verbinding is tussen een IP-adres en waar de computer met dat adres zich fysiek bevindt. Het toewijzen van IP-adressen is gecompliceerd door het feit dat mobiele providers en Vpn's IP-adressen van centrale Pools uitgeven die vaak zeer veel van elkaar zijn waar het client apparaat daad werkelijk wordt gebruikt. Op dit moment in azure AD-rapporten is het converteren van een IP-adres naar een fysieke locatie een beste werk op basis van traceringen, register gegevens, het terugdraaien van de ups en andere informatie.

Op de pagina **Gebruikers** krijgt u een volledig overzicht van alle aanmeldingen van gebruikers door in de sectie **Activiteit** op **Aanmelden** te klikken.

![Aanmeldings activiteit](./media/concept-sign-ins/08.png "Aanmeldingsactiviteit")

## <a name="usage-of-managed-applications"></a>Het gebruik van beheerde toepassingen

Met een toepassingsgerichte weergave van uw aanmeldingsgegevens kunt u antwoord vinden op vragen zoals:

* Wie gebruikt mijn toepassingen?
* Wat zijn de belangrijkste drie toepassingen in uw organisatie?
* Hoe wordt mijn nieuwste toepassing uitgevoerd?

Het ingangs punt voor deze gegevens is de drie belangrijkste toepassingen in uw organisatie. De gegevens bevinden zich in het rapport van de afgelopen 30 dagen in het gedeelte **overzicht** onder **bedrijfs toepassingen**.

![Aanmeldings activiteit](./media/concept-sign-ins/10.png "Aanmeldingsactiviteit")

De grafieken voor het gebruik van de app en wekelijkse aggregaties van aanmeldingen voor uw eerste drie toepassingen in een bepaalde periode. De standaard ingestelde periode is 30 dagen.

![Aanmeldings activiteit](./media/concept-sign-ins/graph-chart.png "Aanmeldingsactiviteit")

Als u wilt, kunt u de focus instellen op een specifieke toepassing.

![Rapportage](./media/concept-sign-ins/single-app-usage-graph.png "Reporting")

Als u op een dag in de appgebruikgrafiek klikt, ziet u een gedetailleerd overzicht van de aanmeldactiviteiten.

Met de optie **Aanmeldingen** krijgt u een volledig overzicht van alle aanmeldingsgebeurtenissen voor uw toepassingen.

## <a name="office-365-activity-logs"></a>Office 365-activiteiten logboeken

U kunt activiteiten logboeken van Office 365 bekijken vanuit het [Microsoft 365-beheer centrum](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Denk na over het punt dat, Office 365-activiteit en Azure AD-activiteiten Logboeken een aanzienlijk aantal Directory bronnen delen. Alleen het Microsoft 365-beheer centrum biedt een volledig overzicht van de activiteiten logboeken van Office 365. 

U kunt de activiteiten logboeken van Office 365 ook programmatisch openen met behulp van de [office 365-beheer-api's](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Volgende stappen

* [Fout codes voor aanmeldings activiteiten rapport](reference-sign-ins-error-codes.md)
* [Beleid voor het bewaren van Azure AD-gegevens](reference-reports-data-retention.md)
* [Vertragingen van Azure AD-rapporten](reference-reports-latencies.md)

