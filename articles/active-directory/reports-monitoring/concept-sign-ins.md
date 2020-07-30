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
ms.date: 03/24/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81daa7696c645c1353cdd5e6d3cbca90ac4f03da
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87388115"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Aanmeldactiviteitenrapporten in Azure Active Directory Portal

De rapportage architectuur in Azure Active Directory (Azure AD) bestaat uit de volgende onderdelen:

- **Activiteit** 
    - **Aanmeldingen** : informatie over het gebruik van beheerde toepassingen en aanmeldings activiteiten voor gebruikers.
    - **Audit logboeken**  -  [Audit logboeken](concept-audit-logs.md) bevatten informatie over de systeem activiteit van gebruikers en groeps beheer, beheerde toepassingen en Directory-activiteiten.
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

- Het rapport voor de aanmeldings activiteit is beschikbaar in [alle edities van Azure AD](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data).

- Als u toegang wilt tot de aanmeldings gegevens met behulp van een API, moet aan uw Tenant een [Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) -licentie zijn gekoppeld.



## <a name="sign-ins-report"></a>Aanmeldingenrapport

Het rapport gebruikers aanmeldingen bevat antwoorden op de volgende vragen:

* Wat is het aanmeldingspatroon van een gebruiker?
* Hoeveel gebruikers hebben zich gedurende een week aangemeld?
* Wat is de status van deze aanmeldingen?

Selecteer in het menu [Azure Portal](https://portal.azure.com) **Azure Active Directory**of zoek en selecteer **Azure Active Directory** op een wille keurige pagina.

![Selecteer Azure Active Directory](./media/concept-sign-ins/select-azure-active-directory.png "Azure Active Directory")

Onder **bewaking**selecteert u **aanmeldingen** om het [rapport aanmeldingen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)te openen.

![Aanmeldings activiteit](./media/concept-sign-ins/monitoring-sign-ins-in-azure-active-directory.png "Aanmeldingsactiviteit")

Het kan tot twee uur duren voordat sommige registratie records worden weer gegeven in de portal.

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

Het dialoog venster **kolommen** biedt u toegang tot de selecteerbaar kenmerken. In een aanmeldings rapport kunt u geen velden hebben met meer dan één waarde voor een bepaalde aanmeldings aanvraag als kolom. Dit is bijvoorbeeld waar voor verificatie Details, gegevens van voorwaardelijke toegang en de netwerk locatie.   

![Aanmeldings activiteit](./media/concept-sign-ins/columns.png "Aanmeldingsactiviteit")

Selecteer een item in de lijst weergave voor meer gedetailleerde informatie.

![Aanmeldings activiteit](./media/concept-sign-ins/basic-sign-in.png "Aanmeldingsactiviteit")

> [!NOTE]
> Klanten kunnen nu het beleid voor voorwaardelijke toegang oplossen via alle aanmeldings rapporten. Door op het tabblad **voorwaardelijke toegang** voor een aanmeldings record te klikken, kunnen klanten de status van de voorwaardelijke toegang controleren en de Details bekijken van de beleids regels die zijn toegepast op de aanmelding en het resultaat van elk beleid.
> Raadpleeg de [Veelgestelde vragen over ca-informatie in alle aanmeldingen](reports-faq.md#conditional-access)voor meer informatie.



## <a name="filter-sign-in-activities"></a>Aanmeldactiviteiten filteren

U moet eerst de gerapporteerde gegevens beperken tot een niveau dat geschikt is voor u. Ten tweede filtert u aanmeldings gegevens met een datum veld als standaard filter. Azure AD biedt u een breed scala aan extra filters die u kunt instellen:

![Aanmeldings activiteit](./media/concept-sign-ins/04.png "Aanmeldingsactiviteit")

**Aanvraag-id** : de id van de aanvraag die u vindt.

**Gebruiker** -de naam of de User Principal Name (UPN) van de gebruiker die u bezorgt.

**Toepassing** -de naam van de doel toepassing.
 
**Status** -de aanmeldings status die u bevalt:

- Geslaagd

- Fout

- Onderbroken


**IP-adres** : het IP-adres van het apparaat dat wordt gebruikt om verbinding te maken met uw Tenant.

De **locatie** -de locatie van de verbinding vanaf:

- Plaats

- Provincie

- Land/regio


**Resource** -de naam van de service die wordt gebruikt voor de aanmelding.


**Resource-id** : de id van de service die wordt gebruikt voor de aanmelding.


**Client-app** : het type client-app dat wordt gebruikt om verbinding te maken met uw Tenant:

![Client-App-filter](./media/concept-sign-ins/client-app-filter.png)


|Naam|Moderne verificatie|Beschrijving|
|---|:-:|---|
|Geverifieerde SMTP| |Wordt gebruikt door de POP-en IMAP-client om e-mail berichten te verzenden.|
|Autodis cover| |Wordt gebruikt door Outlook-en EAS-clients om post vakken in Exchange Online te vinden en er verbinding mee te maken.|
|Exchange ActiveSync| |Dit filter geeft alle aanmeldings pogingen weer waarin is geprobeerd het EAS-protocol uit te voeren.|
|Browser|![Selecteren](./media/concept-sign-ins/check.png)|Toont alle aanmeldings pogingen van gebruikers met behulp van webbrowsers|
|Exchange ActiveSync| | Geeft alle aanmeldings pogingen van gebruikers met client-apps met behulp van Exchange ActiceSync om verbinding te maken met Exchange Online|
|Exchange Online Power shell| |Wordt gebruikt om verbinding te maken met Exchange Online met externe Power shell. Als u basis verificatie voor Exchange Online Power shell blokkeert, moet u de Exchange Online Power shell-module gebruiken om verbinding te maken. Zie [verbinding maken met Exchange Online Power shell met multi-factor Authentication](https://docs.microsoft.com/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)voor instructies.|
|Exchange-webservices| |Een programmeer interface die wordt gebruikt door Outlook, Outlook voor Mac en apps van derden.|
|IMAP4| |Een verouderde e-mailclient die gebruikmaakt van IMAP om e-mail op te halen.|
|MAPI via HTTP| |Gebruikt door Outlook 2010 en hoger.|
|Mobiele apps en desktop-clients|![Selecteren](./media/concept-sign-ins/check.png)|Toont alle aanmeldings pogingen van gebruikers die mobiele apps en desktop-clients gebruiken.|
|Offline adresboek| |Een kopie van de adres lijst verzamelingen die worden gedownload en gebruikt door Outlook.|
|Outlook Anywhere (RPC via HTTP)| |Gebruikt door Outlook 2016 en eerder.|
|Outlook-service| |Wordt gebruikt door de mail-en agenda-app voor Windows 10.|
|POP3| |Een legacy-mailclient die gebruikmaakt van POP3 om e-mail op te halen.|
|Reporting Web Services| |Wordt gebruikt om rapport gegevens op te halen in Exchange Online.|
|Andere clients| |Toont alle aanmeldings pogingen van gebruikers, waarbij de client-app niet is opgenomen of onbekend.|



**Besturings systeem** : het besturings systeem dat wordt uitgevoerd op het apparaat dat wordt gebruikt voor aanmelding bij uw Tenant. 


**Browser voor apparaten** : als de verbinding is gestart vanuit een browser, kunt u in dit veld filteren op browser naam.


**Correlatie-id** : de correlatie-id van de activiteit.




**Voorwaardelijke toegang** : de status van de toegepaste regels voor voorwaardelijke toegang

- **Niet toegepast**: tijdens het aanmelden wordt er geen beleid toegepast op de gebruiker en de toepassing.

- **Geslaagd**: een of meer beleids regels voor voorwaardelijke toegang die worden toegepast op de gebruiker en toepassing (maar niet noodzakelijkerwijs de andere voor waarden) tijdens het aanmelden. 

- **Probleem**: bij de aanmelding is voldaan aan de gebruikers-en toepassings voorwaarde van ten minste één beleid voor voorwaardelijke toegang en granting Controls is niet voldaan of ingesteld om de toegang te blok keren.









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

- Gebruikers-id
- Gebruiker
- Gebruikersnaam
- Toepassings-id
- Toepassing
- Client
- Locatie
- Het IP-adres
- Date
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

![Rapportage](./media/concept-sign-ins/single-app-usage-graph.png "Rapportage")

Als u op een dag in de appgebruikgrafiek klikt, ziet u een gedetailleerd overzicht van de aanmeldactiviteiten.

Met de optie **Aanmeldingen** krijgt u een volledig overzicht van alle aanmeldingsgebeurtenissen voor uw toepassingen.

## <a name="office-365-activity-logs"></a>Office 365-activiteiten logboeken

U kunt activiteiten logboeken van Office 365 bekijken vanuit het [Microsoft 365-beheer centrum](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Denk na over het punt dat, Office 365-activiteit en Azure AD-activiteiten Logboeken een aanzienlijk aantal Directory bronnen delen. Alleen het Microsoft 365-beheer centrum biedt een volledig overzicht van de activiteiten logboeken van Office 365. 

U kunt de activiteiten logboeken van Office 365 ook programmatisch openen met behulp van de [office 365-beheer-api's](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Volgende stappen

* [Fout codes voor aanmeldings activiteiten rapport](reference-sign-ins-error-codes.md)
* [Beleid voor het bewaren van Azure AD-gegevens](reference-reports-data-retention.md)
* [Vertragingen van Azure AD-rapporten](reference-reports-latencies.md)

