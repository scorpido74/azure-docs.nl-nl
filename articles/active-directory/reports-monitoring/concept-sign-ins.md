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
ms.openlocfilehash: 2910933e2c57a8bc80a220726462b02915c4a8eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246514"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Aanmeldactiviteitenrapporten in Azure Active Directory Portal

De rapportagearchitectuur in Azure Active Directory (Azure AD) bestaat uit de volgende onderdelen:

- **Activiteit** 
    - **Aanmeldingsprogramma's** : informatie over het gebruik van beheerde toepassingen en aanmeldingsactiviteiten voor gebruikers.
    - **Controlelogboeken** - [Auditlogs](concept-audit-logs.md) bieden systeemactiviteitsinformatie over gebruikers en groepsbeheer, beheerde toepassingen en directoryactiviteiten.
- **Beveiliging** 
    - **Riskante aanmeldingen** - Een [riskante aanmelding](concept-risky-sign-ins.md) is een indicator voor een aanmeldingspoging door iemand die niet de rechtmatige eigenaar van een gebruikersaccount is.
    - **Gebruikers gemarkeerd voor risico** - Een [riskante gebruiker](concept-user-at-risk.md) is een indicator voor een gebruikersaccount die mogelijk is gecompromitteerd.

Dit artikel geeft u een overzicht van het aanmeldingsrapport.

## <a name="prerequisites"></a>Vereisten

### <a name="who-can-access-the-data"></a>Wie heeft er toegang tot de gegevens?

* Gebruikers in de rollen Beveiligingsbeheerder, Beveiligingslezer, Global Reader en Report Reader
* Globale beheerders
* Alle gebruiker (niet-beheerders) hebben toegang tot hun eigen aanmeldingen 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Welke Azure AD-licentie heb ik nodig voor toegang tot aanmeldingsactiviteiten?

- Het aanmeldingsactiviteitsrapport is beschikbaar in [alle edities van Azure AD.](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data)

- Als u toegang wilt krijgen tot de aanmeldingsgegevens met behulp van een API, moet uw tenant een [Azure Active Directory Premium-licentie](../fundamentals/active-directory-get-started-premium.md) hebben die eraan is gekoppeld.



## <a name="sign-ins-report"></a>Aanmeldingenrapport

Het rapport aanmeldingen van gebruikers geeft antwoorden op de volgende vragen:

* Wat is het aanmeldingspatroon van een gebruiker?
* Hoeveel gebruikers hebben zich gedurende een week aangemeld?
* Wat is de status van deze aanmeldingen?

Selecteer in het menu [Azure portal](https://portal.azure.com) Azure **Active Directory**of zoek naar En selecteer Azure **Active Directory** op een pagina.

![Azure Active Directory selecteren](./media/concept-sign-ins/select-azure-active-directory.png "Azure Active Directory")

Selecteer **onder Controleren**de optie **Aanmeldingen** om het [rapport Aanmeldingen te openen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns).

![Aanmeldingsactiviteit](./media/concept-sign-ins/monitoring-sign-ins-in-azure-active-directory.png "Aanmeldingsactiviteit")

Het kan tot twee uur duren voordat sommige aanmeldingsrecords in het portaal worden weergegeven.

> [!IMPORTANT]
> In het aanmeldingsrapport worden alleen de **interactieve** aanmeldingen weergegeven, dat wil zeggen aanmeldingen waarbij een gebruiker zich handmatig aanmeldt met zijn gebruikersnaam en wachtwoord. Niet-interactieve aanmeldingen, zoals service-to-service-verificatie, worden niet weergegeven in het aanmeldingsrapport. 

Een aanmeldingslogboek heeft een standaardlijstweergave die het volgende laat zien:

- De aanmeldingsdatum
- De gerelateerde gebruiker
- De toepassing die de gebruiker heeft aangemeld bij
- De aanmeldingsstatus
- De status van de risicodetectie
- De status van de vereiste voor meervoudige verificatie (MFA)

![Aanmeldingsactiviteit](./media/concept-sign-ins/sign-in-activity.png "Aanmeldingsactiviteit")

U kunt de lijstweergave aanpassen door te klikken op **Kolommen** op de werkbalk.

![Aanmeldingsactiviteit](./media/concept-sign-ins/19.png "Aanmeldingsactiviteit")

In het dialoogvenster **Kolommen** hebt u toegang tot de selecteerbare kenmerken. In een aanmeldingsrapport u geen velden hebben die meer dan één waarde hebben voor een bepaald aanmeldingsverzoek als kolom. Dit geldt bijvoorbeeld voor verificatiegegevens, gegevens over voorwaardelijke toegang en netwerklocatie.   

![Aanmeldingsactiviteit](./media/concept-sign-ins/columns.png "Aanmeldingsactiviteit")

Selecteer een item in de lijstweergave voor meer gedetailleerde informatie.

![Aanmeldingsactiviteit](./media/concept-sign-ins/basic-sign-in.png "Aanmeldingsactiviteit")

> [!NOTE]
> Klanten kunnen nu probleemvoorwaardenbeleid oplossen via alle aanmeldingsrapporten. Door op het tabblad **Voorwaardelijke toegang** te klikken voor een aanmeldingsrecord, kunnen klanten de status Voorwaardelijke toegang controleren en de details bekijken van de beleidsregels die van toepassing zijn op de aanmelding en het resultaat voor elk beleid.
> Zie [de veelgestelde vragen over CA-informatie in alle aanmeldingen](reports-faq.md#conditional-access)voor meer informatie.



## <a name="filter-sign-in-activities"></a>Aanmeldactiviteiten filteren

Ten eerste, het beperken van de gerapporteerde gegevens tot een niveau dat voor u werkt. Ten tweede filtert u aanmeldingen met datumveld als standaardfilter. Azure AD biedt u een breed scala aan extra filters die u instellen:

![Aanmeldingsactiviteit](./media/concept-sign-ins/04.png "Aanmeldingsactiviteit")

**Aanvraag ID** - De ID van het verzoek dat u de zorg over.

**Gebruiker** - De naam of de gebruikersnaam (UPN) van de gebruiker waar u om geeft.

**Toepassing** - De naam van de doeltoepassing.
 
**Status** - De aanmeldingsstatus waar u om geeft:

- Geslaagd

- Fout

- Onderbroken


**IP-adres** - Het IP-adres van het apparaat dat wordt gebruikt om verbinding te maken met uw tenant.

De **locatie** - de locatie waar de verbinding is gestart vanuit:

- Plaats

- Staat / Provincie

- Land/regio


**Resource** - De naam van de service die wordt gebruikt voor de aanmelding.


**Resource-id** - de id van de service die wordt gebruikt voor de aanmelding.


**Client-app** - Het type client-app dat wordt gebruikt om verbinding te maken met uw tenant:

![Clientapp-filter](./media/concept-sign-ins/client-app-filter.png)


|Name|Moderne verificatie|Beschrijving|
|---|:-:|---|
|Geverifieerde SMTP| |Gebruikt door POP en IMAP client's om e-mailberichten te verzenden.|
|Autodiscover| |Wordt gebruikt door Outlook- en EAS-clients om postvakken in Exchange Online te vinden en te verbinden.|
|Exchange ActiveSync| |Dit filter toont alle aanmeldingspogingen waarbij het EAS-protocol is geprobeerd.|
|Browser|![Selecteren](./media/concept-sign-ins/check.png)|Toont alle aanmeldingspogingen van gebruikers die webbrowsers gebruiken|
|Exchange ActiveSync| | Toont alle aanmeldingspogingen van gebruikers met client-apps die Exchange ActiceSync gebruiken om verbinding te maken met Exchange Online|
|Exchange Online PowerShell| |Wordt gebruikt om verbinding te maken met Exchange Online met externe PowerShell. Als u basisverificatie voor Exchange Online PowerShell blokkeert, moet u de Exchange Online PowerShell-module gebruiken om verbinding te maken. Zie Verbinding [maken met Exchange Online PowerShell met behulp van meervoudige verificatie](https://docs.microsoft.com/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)voor instructies.|
|Exchange-webservices| |Een programmeerinterface die wordt gebruikt door Outlook, Outlook voor Mac en apps van derden.|
|IMAP4| |Een oudere e-mailclient die IMAP gebruikt om e-mail op te halen.|
|MAPI over HTTP| |Gebruikt door Outlook 2010 en hoger.|
|Mobiele apps en desktopclients|![Selecteren](./media/concept-sign-ins/check.png)|Toont alle aanmeldingspogingen van gebruikers die mobiele apps en desktopclients gebruiken.|
|Offline adresboek| |Een kopie van adreslijstverzamelingen die worden gedownload en gebruikt door Outlook.|
|Outlook Anywhere (RPC via HTTP)| |Gebruikt door Outlook 2016 en eerder.|
|Outlook-service| |Wordt gebruikt door de mail- en agenda-app voor Windows 10.|
|POP3| |Een oudere e-mailclient die POP3 gebruikt om e-mail op te halen.|
|Webservices rapporteren| |Wordt gebruikt om rapportgegevens op te halen in Exchange Online.|
|Andere clients| |Toont alle aanmeldingspogingen van gebruikers waarbij de client-app niet of onbekend is opgenomen.|



**Besturingssysteem** - Het besturingssysteem dat op het apparaat wordt gebruikt, wordt aangemeld bij uw tenant. 


**Apparaatbrowser** - Als de verbinding vanuit een browser is gestart, u met dit veld filteren op browsernaam.


**Correlatie-ID** - De correlatie-ID van de activiteit.




**Voorwaardelijke toegang** - De status van de toegepaste regels voor voorwaardelijke toegang

- **Niet toegepast**: Geen beleid toegepast op de gebruiker en toepassing tijdens het aanmelden.

- **Succes**: Een of meer voorwaardelijke toegangsbeleidsregels die tijdens het aanmelden op de gebruiker en toepassing worden toegepast (maar niet noodzakelijkerwijs de andere voorwaarden). 

- **Fout**: Een of meer voorwaardelijke toegangsbeleidsregels zijn toegepast en is niet voldaan tijdens het aanmelden.









## <a name="download-sign-in-activities"></a>Aanmeldactiviteiten downloaden

Klik **op** de optie Downloaden om een CSV- of JSON-bestand te maken van de meest recente 250.000 records. Begin met [het downloaden van de aanmeldingsgegevens](quickstart-download-sign-in-report.md) als u ermee wilt werken buiten de Azure-portal.  

![Downloaden](./media/concept-sign-ins/71.png "Download")

> [!IMPORTANT]
> Het aantal records dat u downloaden, wordt beperkt door het [azure Active Directory-rapportbehoudbeleid](reference-reports-data-retention.md).  


## <a name="sign-ins-data-shortcuts"></a>Snelkoppelingen naar aanmeldingen

Azure AD en de Azure-portal bieden u beide extra toegangspunten voor aanmeldingen:

- Het overzicht van de beveiliging van de identiteit
- Gebruikers
- Groepen
- Bedrijfstoepassingen

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Gebruikers aanmelden gegevens in de beveiliging van de beveiliging van identiteit

De aanmeldingsgrafiek van de gebruiker op de overzichtspagina **beveiliging van de identiteit** toont wekelijkse aggregaties van aanmeldingen. De standaardwaarde voor de periode is 30 dagen.

![Aanmeldingsactiviteit](./media/concept-sign-ins/06.png "Aanmeldingsactiviteit")

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
- Date
- MFA vereist
- Aanmeldingsstatus

> [!NOTE]
> IP-adressen worden zodanig uitgegeven dat er geen definitief verband bestaat tussen een IP-adres en dat de computer met dat adres zich fysiek bevindt. Het toewijzen van IP-adressen wordt bemoeilijkt door het feit dat mobiele providers en VPN's IP-adressen uitcentrale pools uitgeven die vaak ver verwijderd zijn van de plaats waar het clientapparaat daadwerkelijk wordt gebruikt. Momenteel in Azure AD-rapporten is het converteren van IP-adres naar een fysieke locatie een beste inspanning op basis van traces, registergegevens, reverse look ups en andere informatie.

Op de pagina **Gebruikers** krijgt u een volledig overzicht van alle aanmeldingen van gebruikers door in de sectie **Activiteit** op **Aanmelden** te klikken.

![Aanmeldingsactiviteit](./media/concept-sign-ins/08.png "Aanmeldingsactiviteit")

## <a name="usage-of-managed-applications"></a>Het gebruik van beheerde toepassingen

Met een toepassingsgerichte weergave van uw aanmeldingsgegevens kunt u antwoord vinden op vragen zoals:

* Wie gebruikt mijn toepassingen?
* Wat zijn de top drie toepassingen in uw organisatie?
* Hoe gaat het met mijn nieuwste applicatie?

Het toegangspunt tot deze gegevens is de top drie toepassingen in uw organisatie. De gegevens worden opgenomen in het rapport van de laatste 30 dagen in de sectie **Overzicht** onder **Enterprise-toepassingen**.

![Aanmeldingsactiviteit](./media/concept-sign-ins/10.png "Aanmeldingsactiviteit")

De app-gebruik grafieken wekelijkse aggregaties van aanmeldingen voor uw top drie toepassingen in een bepaalde periode. De standaard ingestelde periode is 30 dagen.

![Aanmeldingsactiviteit](./media/concept-sign-ins/graph-chart.png "Aanmeldingsactiviteit")

Als u wilt, kunt u de focus instellen op een specifieke toepassing.

![Rapportage](./media/concept-sign-ins/single-app-usage-graph.png "Rapportage")

Als u op een dag in de appgebruikgrafiek klikt, ziet u een gedetailleerd overzicht van de aanmeldactiviteiten.

Met de optie **Aanmeldingen** krijgt u een volledig overzicht van alle aanmeldingsgebeurtenissen voor uw toepassingen.

## <a name="office-365-activity-logs"></a>Office 365-activiteitslogboeken

U Office 365-activiteitslogboeken bekijken vanuit het [Microsoft 365-beheercentrum](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Houd rekening met het punt dat office 365-activiteitslogboeken en AZURE AD-activiteitslogboeken een aanzienlijk aantal directorybronnen delen. Alleen het Microsoft 365-beheercentrum biedt een volledig overzicht van de office 365-activiteitslogboeken. 

U de Office 365-activiteitslogboeken ook programmatisch openen met behulp van de [OFFICE 365 Management API's.](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview)

## <a name="next-steps"></a>Volgende stappen

* [Foutcodes van aanmeldingsactiviteitsrapport](reference-sign-ins-error-codes.md)
* [Azure AD-beleid voor het bewaren van gegevens](reference-reports-data-retention.md)
* [Latenlaten azure AD-rapport](reference-reports-latencies.md)

