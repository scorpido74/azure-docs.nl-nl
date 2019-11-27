---
title: Goedgekeurde client-apps met voorwaardelijke toegang-Azure Active Directory
description: Meer informatie over het vereisen van goedgekeurde client-apps voor toegang tot Cloud app met voorwaardelijke toegang in Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: c173d0e17166911e28fea3d1c5820879d17af4a8
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381124"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>Procedure: goedgekeurde client-apps vereisen voor toegang tot Cloud app met voorwaardelijke toegang 

Uw werk nemers gebruiken mobiele apparaten voor zowel privé-als werk taken. Hoewel uw werk nemers productief kunnen zijn, moet u ook gegevens verlies voor komen. Met Azure Active Directory (Azure AD) voorwaardelijke toegang kunt u de toegang tot uw Cloud-apps beperken tot goedgekeurde client-apps waarmee uw bedrijfs gegevens kunnen worden beveiligd.  

In dit onderwerp wordt uitgelegd hoe u toegangs beleid voor voor waarden configureert waarvoor goedgekeurde client-apps zijn vereist.

## <a name="overview"></a>Overzicht

Met [voorwaardelijke toegang van Azure AD](overview.md)kunt u nauw keurig afstemmen hoe geautoriseerde gebruikers toegang hebben tot uw resources. U kunt bijvoorbeeld de toegang tot uw Cloud-apps beperken tot vertrouwde apparaten.

U kunt [intune-beveiligings beleid voor apps](https://docs.microsoft.com/intune/app-protection-policy) gebruiken om uw bedrijfs gegevens te beveiligen. Voor het intune-app-beveiligings beleid is geen MDM-oplossing (Mobile-Device Management) vereist, waarmee u de gegevens van uw bedrijf kunt beveiligen met of zonder inschrijving van apparaten in een oplossing voor Apparaatbeheer.

Met Azure Active Directory voorwaardelijke toegang kunt u de toegang tot uw Cloud-apps beperken tot client-apps die ondersteuning bieden voor intune-beveiligings beleid voor apps. U kunt bijvoorbeeld de toegang tot Exchange Online beperken tot de Outlook-app.

In de terminologie van voorwaardelijke toegang worden deze client-apps aangeduid als **goedgekeurde client-apps**.  

![Voorwaardelijke toegang](./media/app-based-conditional-access/05.png)

Zie voor een lijst met goedgekeurde client-apps [goedgekeurde client-app-vereiste](technical-reference.md#approved-client-app-requirement).

U kunt op apps gebaseerd beleid voor voorwaardelijke toegang combi neren met andere beleids regels, zoals op [apparaten gebaseerd beleid voor voorwaardelijke toegang](require-managed-devices.md) om flexibiliteit te bieden bij het beveiligen van gegevens voor zowel persoonlijke als zakelijke apparaten.

## <a name="before-you-begin"></a>Voordat u begint

In dit onderwerp wordt ervan uitgegaan dat u bekend bent met:

- De [goedgekeurde](technical-reference.md#approved-client-app-requirement) technische Naslag informatie voor app-vereisten voor clients.
- De basis concepten van [voorwaardelijke toegang in azure Active Directory](overview.md).
- [Een beleid voor voorwaardelijke toegang configureren](app-based-mfa.md).
- De [migratie van beleids regels voor voorwaardelijke toegang](best-practices.md#policy-migration).

## <a name="prerequisites"></a>Vereisten

Als u een op apps gebaseerd beleid voor voorwaardelijke toegang wilt maken, moet u een Enterprise Mobility + Security of een Azure Active Directory Premium-abonnement hebben en moeten de gebruikers een licentie hebben voor EMS of Azure AD. 

## <a name="exchange-online-policy"></a>Beleid voor Exchange Online 

Dit scenario bestaat uit een op apps gebaseerd beleid voor voorwaardelijke toegang voor toegang tot Exchange Online.

### <a name="scenario-playbook"></a>Scenario Playbook

In dit scenario wordt ervan uitgegaan dat een gebruiker:

- Hiermee configureert u een e-mail met een systeem eigen e-mail toepassing op iOS of Android om verbinding te maken met Exchange
- Hiermee wordt een e-mail bericht ontvangen dat aangeeft dat toegang alleen beschikbaar is via de Outlook-app
- Hiermee wordt de toepassing gedownload met de koppeling
- Hiermee opent u de Outlook-toepassing en meldt u zich aan met de Azure AD-referenties
- Wordt gevraagd of u verificator (iOS) of Bedrijfsportal (Android) wilt installeren om door te gaan
- De toepassing wordt geïnstalleerd en kan worden teruggestuurd naar de Outlook-app om door te gaan
- Wordt gevraagd een apparaat te registreren
- Kan toegang krijgen tot e-mail

Elk intune-app-beveiligings beleid wordt geactiveerd op het moment dat de toegang tot Bedrijfs gegevens wordt gemaakt en kan de gebruiker vragen om de toepassing opnieuw op te starten, een extra pincode gebruiken, enzovoort (indien geconfigureerd voor de toepassing en het platform).

### <a name="configuration"></a>Configuratie 

**Stap 1: een beleid voor voorwaardelijke toegang voor Azure AD configureren voor Exchange Online**

Voor het beleid voor voorwaardelijke toegang in deze stap moet u de volgende onderdelen configureren:

1. De **naam** van het beleid voor voorwaardelijke toegang.
1. **Gebruikers en groepen**: voor elk voorwaardelijk toegangs beleid moet ten minste één gebruiker of groep zijn geselecteerd.
1. **Cloud-apps:** Als Cloud-apps moet u **Office 365 Exchange Online**selecteren.
1. **Voor waarden:** Als **voor waarden**, moet u **platform platforms** en **client-apps**configureren:
   1. Als **apparaat platforms**selecteert u **Android** en **IOS**.
   1. Als **client-apps (preview)** selecteert u **mobiele apps en bureau blad-apps** en **moderne verificatie-clients**.
1. Als **toegangs beheer**moet u **goedgekeurde client-app (preview) vereisen** .

   ![Voorwaardelijke toegang](./media/app-based-conditional-access/05.png)

**Stap 2: een beleid voor voorwaardelijke toegang voor Azure AD configureren voor Exchange Online met Active Sync (EAS)**

Voor het beleid voor voorwaardelijke toegang in deze stap moet u de volgende onderdelen configureren:

1. De **naam** van het beleid voor voorwaardelijke toegang.
1. **Gebruikers en groepen**: voor elk voorwaardelijk toegangs beleid moet ten minste één gebruiker of groep zijn geselecteerd.
1. **Cloud-apps:** Als Cloud-apps moet u **Office 365 Exchange Online**selecteren.
1. **Voor waarden:** Als **voor waarden**, moet u **client-apps (preview-versie)** configureren. 
   1. Als **client-apps (preview)** , selecteert u **mobiele apps en desktop-clients** en **Exchange ActiveSync-clients**.
   1. Als **toegangs beheer**moet u **goedgekeurde client-app (preview) vereisen** .

      ![Voorwaardelijke toegang](./media/app-based-conditional-access/05.png)

**Stap 3: het intune-beveiligings beleid voor apps configureren voor iOS-en Android-client toepassingen**

Zie [apps en gegevens beveiligen met Microsoft intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) voor meer informatie.

## <a name="exchange-online-and-sharepoint-online-policy"></a>Beleid voor Exchange Online en share point online

Dit scenario bestaat uit een voorwaardelijke toegang met beleid voor het beheren van mobiele apps voor toegang tot Exchange Online en share point online met goedgekeurde apps.

### <a name="scenario-playbook"></a>Scenario Playbook

In dit scenario wordt ervan uitgegaan dat een gebruiker:

- Probeert de share point-app te gebruiken om verbinding te maken en om hun bedrijfs sites weer te geven
- Probeert zich aan te melden met dezelfde referenties als de Outlook-app-referenties
- Hoeft niet opnieuw te worden geregistreerd en kan toegang krijgen tot de resources

### <a name="configuration"></a>Configuratie

**Stap 1: een beleid voor voorwaardelijke toegang voor Azure AD configureren voor Exchange Online en share point online**

Voor het beleid voor voorwaardelijke toegang in deze stap moet u de volgende onderdelen configureren:

1. De **naam** van het beleid voor voorwaardelijke toegang.
1. **Gebruikers en groepen**: voor elk voorwaardelijk toegangs beleid moet ten minste één gebruiker of groep zijn geselecteerd.
1. **Cloud-apps:** Als Cloud-apps moet u **office 365 Exchange Online** en **Office 365 share point online**selecteren. 
1. **Voor waarden:** Als **voor waarden**, moet u **platform platforms** en **client-apps**configureren:
   1. Als **apparaat platforms**selecteert u **Android** en **IOS**.
   1. Als **client-apps (preview)** selecteert u **mobiele apps en desktop-clients** en **moderne verificatie-clients**.
1. Als **toegangs beheer**moet u **goedgekeurde client-app (preview) vereisen** .

   ![Voorwaardelijke toegang](./media/app-based-conditional-access/05.png)

**Stap 2: een beleid voor voorwaardelijke toegang voor Azure AD configureren voor Exchange Online met Active Sync (EAS)**

Voor het beleid voor voorwaardelijke toegang in deze stap moet u de volgende onderdelen configureren:

1. De **naam** van het beleid voor voorwaardelijke toegang.
1. **Gebruikers en groepen**: voor elk voorwaardelijk toegangs beleid moet ten minste één gebruiker of groep zijn geselecteerd.
1. **Cloud-apps:** Als Cloud-apps moet u **Office 365 Exchange Online**selecteren. Online 
1. **Voor waarden:** Als **voor waarden**, moet u **client-apps**configureren:
   1. Als **client-apps (preview)** , selecteert u **mobiele apps en desktop-clients** en **Exchange ActiveSync-clients**.
   1. Als **toegangs beheer**moet u **goedgekeurde client-app (preview) vereisen** .

      ![Voorwaardelijke toegang](./media/app-based-conditional-access/05.png)

**Stap 3: het intune-beveiligings beleid voor apps configureren voor iOS-en Android-client toepassingen**

![Voorwaardelijke toegang](./media/app-based-conditional-access/09.png)

Zie [apps en gegevens beveiligen met Microsoft intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) voor meer informatie.

## <a name="app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Beleid op basis van apps of compatibele apparaten voor Exchange Online en share point online

Dit scenario bestaat uit een beleid voor voorwaardelijke toegang op basis van een app of compatibel apparaat voor toegang tot Exchange Online.

### <a name="scenario-playbook"></a>Scenario Playbook

In dit scenario wordt ervan uitgegaan dat:
 
- Sommige gebruikers zijn al Inge schreven (met of zonder Bedrijfs apparaten)
- Gebruikers die niet zijn Inge schreven en geregistreerd bij Azure AD met behulp van een app-beveiligde toepassing, moeten een apparaat registreren voor toegang tot resources
- Geregistreerde gebruikers die gebruikmaken van de app beveiligde toepassing hoeven het apparaat niet opnieuw te registreren

### <a name="configuration"></a>Configuratie

**Stap 1: een beleid voor voorwaardelijke toegang voor Azure AD configureren voor Exchange Online en share point online**

Voor het beleid voor voorwaardelijke toegang in deze stap moet u de volgende onderdelen configureren:

1. De **naam** van het beleid voor voorwaardelijke toegang.
1. **Gebruikers en groepen**: voor elk voorwaardelijk toegangs beleid moet ten minste één gebruiker of groep zijn geselecteerd.
1. **Cloud-apps:** Als Cloud-apps moet u **office 365 Exchange Online** en **Office 365 share point online**selecteren. 
1. **Voor waarden:** Als **voor waarden**, moet u **platform platforms** en **client-apps**configureren. 
   1. Als **apparaat platforms**selecteert u **Android** en **IOS**.
   1. Als **client-apps (preview)** selecteert u **mobiele apps en desktop-clients** en **moderne verificatie-clients**.
1. Als **besturings elementen voor toegang**moet u het volgende selecteren:
   - **Vereisen dat het apparaat wordt gemarkeerd als compatibel**
   - **Goedgekeurde client-app vereisen (preview-versie)**
   - **Een van de geselecteerde besturings elementen vereisen**   
 
      ![Voorwaardelijke toegang](./media/app-based-conditional-access/11.png)

**Stap 2: een beleid voor voorwaardelijke toegang voor Azure AD configureren voor Exchange Online met Active Sync (EAS)**

Voor het beleid voor voorwaardelijke toegang in deze stap moet u de volgende onderdelen configureren:

1. De **naam** van het beleid voor voorwaardelijke toegang.
1. **Gebruikers en groepen**: voor elk voorwaardelijk toegangs beleid moet ten minste één gebruiker of groep zijn geselecteerd.
1. **Cloud-apps:** Als Cloud-apps moet u **Office 365 Exchange Online**selecteren. 
1. **Voor waarden:** Als **voor waarden**, moet u **client-apps**configureren. 
   1. Als **client-apps (preview)** , selecteert u **mobiele apps en desktop-clients** en **Exchange ActiveSync-clients**.
1. Als **toegangs beheer**moet u **goedgekeurde client-app (preview) vereisen** .
 
   ![Voorwaardelijke toegang](./media/app-based-conditional-access/11.png)

**Stap 3: het intune-beveiligings beleid voor apps configureren voor iOS-en Android-client toepassingen**

![Voorwaardelijke toegang](./media/app-based-conditional-access/09.png)

Zie [apps en gegevens beveiligen met Microsoft intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) voor meer informatie.

## <a name="app-based-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Beleid op basis van apps en compatibele apparaten voor Exchange Online en share point online

Dit scenario bestaat uit een op apps gebaseerd beleid voor voorwaardelijke toegang voor toegang tot Exchange Online.

### <a name="scenario-playbook"></a>Scenario Playbook

In dit scenario wordt ervan uitgegaan dat een gebruiker:
 
- Hiermee configureert u een e-mail met een systeem eigen e-mail toepassing op iOS of Android om verbinding te maken met Exchange
- Hiermee wordt een e-mail bericht ontvangen dat aangeeft dat het apparaat moet worden inge schreven voor toegang
- Down load de bedrijfs portal en meldt zich aan bij de bedrijfs portal
- Controleert e-mail en wordt gevraagd om de Outlook-app te gebruiken
- De Outlook-app downloaden
- Hiermee opent u de Outlook-app en voert u de referenties in die in de inschrijving worden gebruikt
- Gebruiker kan toegang krijgen tot e-mail

Het beveiligings beleid voor apps in intune wordt geactiveerd op het moment dat de toegang tot de Bedrijfs gegevens wordt geopend en de gebruiker wordt mogelijk gevraagd de toepassing opnieuw op te starten. gebruik een extra pincode, enzovoort (indien geconfigureerd voor de toepassing en het platform)

### <a name="configuration"></a>Configuratie

**Stap 1: een beleid voor voorwaardelijke toegang voor Azure AD configureren voor Exchange Online en share point online**

Voor het beleid voor voorwaardelijke toegang in deze stap moet u de volgende onderdelen configureren:

1. De **naam** van het beleid voor voorwaardelijke toegang.
1. **Gebruikers en groepen**: voor elk voorwaardelijk toegangs beleid moet ten minste één gebruiker of groep zijn geselecteerd.
1. **Cloud-apps:** Als Cloud-apps moet u **office 365 Exchange Online** en **Office 365 share point online**selecteren. 
1. **Voor waarden:** Als **voor waarden**, moet u **platform platforms** en **client-apps**configureren. 
   1. Als **apparaat platforms**selecteert u **Android** en **IOS**.
   1. Als **client-apps (preview)** selecteert u **mobiele apps en bureau blad-apps** en **moderne verificatie-clients**.
1. Als **besturings elementen voor toegang**moet u het volgende selecteren:
   - **Vereisen dat het apparaat wordt gemarkeerd als compatibel**
   - **Goedgekeurde client-app vereisen (preview-versie)**
   - **Alle geselecteerde besturings elementen vereisen**   
 
      ![Voorwaardelijke toegang](./media/app-based-conditional-access/13.png)

**Stap 2: een beleid voor voorwaardelijke toegang voor Azure AD configureren voor Exchange Online met Active Sync (EAS)**

Voor het beleid voor voorwaardelijke toegang in deze stap moet u de volgende onderdelen configureren:

1. De **naam** van het beleid voor voorwaardelijke toegang.
1. **Gebruikers en groepen**: voor elk voorwaardelijk toegangs beleid moet ten minste één gebruiker of groep zijn geselecteerd.
1. **Cloud-apps:** Als Cloud-apps moet u **Office 365 Exchange Online**selecteren. 
1. **Voor waarden:** Als **voor waarden**, moet u **client-apps (preview-versie)** configureren. 
   1. Als **client-apps (preview)** , selecteert u **mobiele apps en desktop-clients** en **Exchange ActiveSync-clients**.

   ![Voorwaardelijke toegang](./media/app-based-conditional-access/92.png)

1. Als **besturings elementen voor toegang**moet u het volgende selecteren:
   - **Vereisen dat het apparaat wordt gemarkeerd als compatibel**
   - **Goedgekeurde client-app vereisen (preview-versie)**
   - **Alle geselecteerde besturings elementen vereisen**   

**Stap 3: het intune-beveiligings beleid voor apps configureren voor iOS-en Android-client toepassingen**

![Voorwaardelijke toegang](./media/app-based-conditional-access/09.png)

Zie [apps en gegevens beveiligen met Microsoft intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Als u wilt weten hoe u een beleid voor voorwaardelijke toegang kunt configureren, raadpleegt u [MFA vereisen voor specifieke apps met voorwaardelijke toegang Azure Active Directory](app-based-mfa.md).

Als u klaar bent voor het configureren van beleid voor voorwaardelijke toegang voor uw omgeving, raadpleegt u de [Aanbevolen procedures voor voorwaardelijke toegang in azure Active Directory](best-practices.md). 
