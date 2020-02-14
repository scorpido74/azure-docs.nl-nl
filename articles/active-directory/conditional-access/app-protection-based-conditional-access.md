---
title: App-beveiligings beleid met voorwaardelijke toegang-Azure Active Directory
description: Meer informatie over het vereisen van app-beveiligings beleid voor toegang tot Cloud-apps met voorwaardelijke toegang in Azure Active Directory.
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
ms.openlocfilehash: 3c853ef3a5a40381aba4e1c13eaf9ad7d8653170
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186621"
---
# <a name="require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>Beveiligings beleid voor apps vereisen voor toegang tot Cloud-apps met voorwaardelijke toegang (preview)

Uw werknemers gebruiken mobiele apparaten voor zowel privé- als werktaken. Hoewel uw werk nemers productief kunnen zijn, moet u ook gegevens verlies voor komen. Met Azure Active Directory (Azure AD) voorwaardelijke toegang kunt u uw bedrijfs gegevens beveiligen door de toegang tot uw Cloud-apps te beperken. Gebruik eerst client-apps met een app-beveiligings beleid.

In dit artikel wordt uitgelegd hoe u beleid voor voorwaardelijke toegang kunt configureren waarvoor een app-beveiligings beleid is vereist voordat toegang tot gegevens wordt verleend.

## <a name="overview"></a>Overzicht

Met [voorwaardelijke toegang van Azure AD](overview.md)kunt u nauw keurig afstemmen hoe geautoriseerde gebruikers toegang hebben tot uw resources. U kunt bijvoorbeeld de toegang tot uw Cloud-apps beperken tot vertrouwde apparaten.

U kunt [intune-beveiligings beleid voor apps](https://docs.microsoft.com/intune/app-protection-policy) gebruiken om uw bedrijfs gegevens te beveiligen. Voor het intune-app-beveiligings beleid is geen oplossing voor Mobile Device Management (MDM) vereist. U kunt de gegevens van uw bedrijf beveiligen met of zonder de registratie van apparaten in een oplossing voor Apparaatbeheer.

Azure Active Directory voorwaardelijke toegang beperkt de toegang tot uw Cloud-apps tot client toepassingen die door intune zijn gerapporteerd aan Azure AD als er een beveiligings beleid voor apps wordt ontvangen. U kunt bijvoorbeeld de toegang tot Exchange Online beperken tot de Outlook-app die een intune-beleid voor app-beveiliging heeft.

In de terminologie van voorwaardelijke toegang zijn deze client-apps bekend als beleid dat wordt beveiligd met een *app-beveiligings beleid*.  

![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/05.png)

Zie [vereisten voor app-beveiligings beleid](concept-conditional-access-grant.md)voor een lijst met door beleid beveiligde client-apps.

U kunt beleid voor voorwaardelijke toegang op basis van een app-beveiliging combi neren met andere beleids regels, zoals [op apparaten gebaseerd beleid voor voorwaardelijke toegang](require-managed-devices.md). Op deze manier kunt u flexibiliteit bieden bij het beveiligen van gegevens voor zowel persoonlijke als zakelijke apparaten.

> [!NOTE]
> App-beveiligings beleid voor voorwaardelijke toegang kan niet worden toegepast op B2B-gebruikers omdat de uitnodigende organisatie geen zicht baarheid heeft in de thuis organisatie van de B2B-gebruiker.

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>Voor delen van de vereiste voor voorwaardelijke toegang op basis van app-beveiliging

Net als bij naleving die wordt gerapporteerd door intune voor iOS en Android voor een beheerd apparaat, meldt intune nu aan bij Azure AD als er een beleid voor app-beveiliging wordt toegepast. Voorwaardelijke toegang kan dit beleid gebruiken als toegangs controle. Dit nieuwe beleid voor voorwaardelijke toegang, het beleid voor app-beveiliging, verhoogt de beveiliging. Het wordt beschermd tegen beheerders fouten, zoals:

- Gebruikers die geen intune-licentie hebben.
- Gebruikers die geen intune-beveiligings beleid voor apps kunnen ontvangen.
- Apps voor het intune-app-beveiligings beleid die niet zijn geconfigureerd voor het ontvangen van een beleid.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u bekend bent met:

- De [vereiste voor het app-beveiligings beleid](concept-conditional-access-grant.md).
- De [vereiste](concept-conditional-access-grant.md)voor de client-app.
- De basis concepten van [voorwaardelijke toegang in azure Active Directory](overview.md).
- [Een beleid voor voorwaardelijke toegang configureren](app-based-mfa.md).

## <a name="prerequisites"></a>Vereisten

Als u een beleid voor voorwaardelijke toegang op basis van app-beveiliging wilt maken, moet u het volgende doen:

- Beschikken over een Enterprise Mobility + Security of een Azure Active Directory Premium-abonnement en intune.
- Zorg ervoor dat de gebruikers een licentie hebben voor Enterprise Mobility + Security of Azure AD + intune.
- Zorg ervoor dat de client-app in intune is geconfigureerd voor het ontvangen van een beveiligings beleid voor apps.
- Zorg ervoor dat de gebruikers zijn geconfigureerd in intune om een intune-beveiligings beleid voor apps te ontvangen.

## <a name="app-protection-based-policy-for-exchange-online"></a>Beleid op basis van app-beveiliging voor Exchange Online

Dit scenario bestaat uit een beleid voor voorwaardelijke toegang op basis van app-beveiliging voor toegang tot Exchange Online.

### <a name="scenario-playbook"></a>Scenario Playbook

In dit scenario wordt ervan uitgegaan dat een gebruiker:

- Hiermee configureert u een e-mail met een systeem eigen e-mail toepassing op iOS of Android om verbinding te maken met Exchange.
- Hiermee ontvangt u een e-mail bericht dat aangeeft dat toegang alleen beschikbaar is via de Outlook-app.
- Hiermee wordt de toepassing gedownload met de koppeling.
- Hiermee opent u de Outlook-toepassing en meldt u zich aan met Azure AD-referenties.
- Wordt gevraagd of u de **app Microsoft Authenticator** of de **intune-bedrijfsportal** wilt installeren om door te gaan.
- Installeert de toepassing en keert terug naar de Outlook-app om door te gaan.
- Wordt gevraagd om een apparaat te registreren.
- Kan een intune-beveiligings beleid voor apps ontvangen.
- Kan toegang krijgen tot e-mail.

Een beveiligings beleid voor apps in intune moet op de toepassing zijn om toegang te krijgen tot Bedrijfs gegevens. Het beleid kan de gebruiker vragen om de toepassing opnieuw te starten of een extra pincode te gebruiken. Dit is het geval als de beleids regels zijn geconfigureerd voor de toepassing en het platform.

### <a name="configuration"></a>Configuratie

**Stap 1: een beleid voor voorwaardelijke toegang voor Azure AD configureren voor Exchange Online**

Configureer voor het beleid voor voorwaardelijke toegang in deze stap de volgende onderdelen:

![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/01.png)

1. Voer de naam van het beleid voor voorwaardelijke toegang in.
1. Selecteer onder **toewijzingen**in **gebruikers en groepen**ten minste één gebruiker of groep voor elk beleid voor voorwaardelijke toegang.
1. Selecteer **Office 365 Exchange Online**in **Cloud-apps**.

   ![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/07.png)

1. In **voor waarden**configureert u **platform platforms** en **client-apps (preview-versie)** :
   1. Selecteer **Android** en **IOS**in **device platforms**.

      ![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/03.png)

   1. Selecteer in **client-apps (preview)** **mobiele apps en desktop-clients** en **moderne verificatie-clients**.

      ![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/91.png)

1. Selecteer **app-beveiligings beleid vereisen (preview)** onder **besturings elementen voor toegang**.

   ![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/05.png)

**Stap 2: een beleid voor voorwaardelijke toegang voor Azure AD configureren voor Exchange Online met ActiveSync (EAS)**

Configureer voor het beleid voor voorwaardelijke toegang in deze stap de volgende onderdelen:

![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/06.png)

1. Voer de naam van het beleid voor voorwaardelijke toegang in.
1. Selecteer onder **toewijzingen**in **gebruikers en groepen**ten minste één gebruiker of groep voor elk beleid voor voorwaardelijke toegang.
1. Selecteer **Office 365 Exchange Online**in **Cloud-apps**.

   ![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/07.png)

1. In **voor waarden**configureert u **client-apps (preview-versie)** . 

   1. Selecteer in **client-apps (preview)** de optie **Mobile apps en desktop-clients** en **Exchange ActiveSync-clients**.

      ![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/92.png)

   1. Selecteer **app-beveiligings beleid vereisen (preview)** onder **besturings elementen voor toegang**.

      ![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/05.png)

**Stap 3: het beveiligings beleid voor apps in intune configureren voor iOS-en Android-client toepassingen**

![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/09.png)

Zie [apps en gegevens beveiligen met Microsoft intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)voor meer informatie.

## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>Beleid op basis van app-beveiliging of compatibel apparaatbeleid voor Exchange Online

Dit scenario bestaat uit een beleid voor voorwaardelijke toegang op basis van een app-beveiliging of compatibel apparaat voor toegang tot Exchange Online.

### <a name="scenario-playbook"></a>Scenario Playbook

In dit scenario wordt ervan uitgegaan dat:
 
- Een gebruiker is al Inge schreven, met of zonder zakelijke apparaten.
- Gebruikers die niet zijn Inge schreven en geregistreerd bij Azure AD met behulp van een app-beveiligde toepassing, moeten een apparaat registreren voor toegang tot resources.
- Geregistreerde gebruikers die gebruikmaken van de app beveiligde toepassing hoeven het apparaat niet opnieuw te registreren.
- De gebruiker kan een intune-beveiligings beleid voor apps ontvangen als het niet is inge schreven.
- De gebruiker heeft toegang tot e-mail met Outlook en een intune-beveiligings beleid voor apps als dit niet is inge schreven.
- De gebruiker heeft toegang tot e-mail met Outlook als het apparaat is inge schreven.

### <a name="configuration"></a>Configuratie

**Stap 1: een beleid voor voorwaardelijke toegang voor Azure AD configureren voor Exchange Online**

Configureer voor het beleid voor voorwaardelijke toegang in deze stap de volgende onderdelen:

![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/62.png)

1. Voer de naam van het beleid voor voorwaardelijke toegang in.
1. Selecteer onder **toewijzingen**in **gebruikers en groepen**ten minste één gebruiker of groep voor elk beleid voor voorwaardelijke toegang.
1. Selecteer **Office 365 Exchange Online**in **Cloud-apps**. 

   ![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/07.png)

1. Configureer onder **voor waarden** **platformen** en **client-apps (preview-versie)** . 
 
   1. Selecteer **Android** en **IOS**in **device platforms**.

      ![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/03.png)

   1. Selecteer in **client-apps (preview)** **mobiele apps en desktop-clients** en **moderne verificatie-clients**.

      ![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/91.png)

5. Selecteer onder **besturings elementen voor toegang**de volgende opties:
   - **Vereisen dat het apparaat wordt gemarkeerd als compatibel**
   - **Beveiligings beleid voor apps vereisen (preview-versie)**
   - **Een van de geselecteerde besturings elementen vereisen**   
 
      ![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/11.png)

**Stap 2: een beleid voor voorwaardelijke toegang voor Azure AD configureren voor Exchange Online met ActiveSync**

Configureer voor het beleid voor voorwaardelijke toegang in deze stap de volgende onderdelen:

![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/06.png)

1. Voer de naam van het beleid voor voorwaardelijke toegang in.
1. Selecteer onder **toewijzingen**in **gebruikers en groepen**ten minste één gebruiker of groep voor elk beleid voor voorwaardelijke toegang.
1. Selecteer **Office 365 Exchange Online**in **Cloud-apps**. 

   ![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/07.png)

1. In **voor waarden**configureert u **client-apps (preview-versie)** . 

   Selecteer in **client-apps (preview)** de optie **Mobile apps en desktop-clients** en **Exchange ActiveSync-clients**.

   ![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/92.png)

1. Selecteer onder **besturings elementen voor toegang**de volgende opties:
   - **Vereisen dat het apparaat wordt gemarkeerd als compatibel**
   - **Beveiligings beleid voor apps vereisen (preview-versie)**
   - **Een van de geselecteerde besturings elementen vereisen**

      ![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/11.png)

**Stap 3: het beveiligings beleid voor apps in intune configureren voor iOS-en Android-client toepassingen**

![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/09.png)

Zie [apps en gegevens beveiligen met Microsoft intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)voor meer informatie.

## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>Beleid op basis van app-beveiliging en compatibel apparaatbeleid voor Exchange Online

Dit scenario bestaat uit een beleid voor voorwaardelijke toegang op basis van een app-beveiliging en een compatibel apparaat voor toegang tot Exchange Online.

### <a name="scenario-playbook"></a>Scenario Playbook

In dit scenario wordt ervan uitgegaan dat een gebruiker:
 
- Hiermee configureert u een e-mail met een systeem eigen e-mail toepassing op iOS of Android om verbinding te maken met Exchange.
- Hiermee ontvangt u een e-mail bericht dat aangeeft dat het apparaat moet worden inge schreven voor toegang.
- Downloadt Intune-bedrijfsportal en meldt zich aan bij de portal.
- Controleert e-mail en wordt gevraagd om de Outlook-app te gebruiken.
- Hiermee downloadt u de Outlook-app.
- Hiermee opent u de Outlook-app en voert u de referenties in die in de registratie worden gebruikt.
- Kan een intune-beveiligings beleid voor apps ontvangen.
- Kan toegang krijgen tot e-mail met Outlook en een intune-beveiligings beleid voor apps.

Het beveiligings beleid voor apps in intune wordt geactiveerd voordat toegang tot Bedrijfs gegevens wordt verleend. Het beleid kan de gebruiker vragen om de toepassing opnieuw te starten of een extra pincode te gebruiken. Dit is het geval als de beleids regels zijn geconfigureerd voor de toepassing en het platform.

### <a name="configuration"></a>Configuratie

**Stap 1: een beleid voor voorwaardelijke toegang voor Azure AD configureren voor Exchange Online**

Configureer voor het beleid voor voorwaardelijke toegang in deze stap de volgende onderdelen:

![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/01.png)

1. Voer de naam van het beleid voor voorwaardelijke toegang in.
1. Selecteer onder **toewijzingen**in **gebruikers en groepen**ten minste één gebruiker of groep voor elk beleid voor voorwaardelijke toegang.
1. Selecteer **Office 365 Exchange Online**in **Cloud-apps**. 

   ![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/07.png)

1. Configureer onder **voor waarden** **platformen** en **client-apps (preview-versie)** . 
   1. Selecteer **Android** en **IOS**in **device platforms**.

      ![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/03.png)

   1. Selecteer in **client-apps (preview)** **mobiele apps en desktop-clients** en **moderne verificatie-clients**.

      ![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/91.png)

1. Selecteer onder **besturings elementen voor toegang**de volgende opties:
   - **Vereisen dat het apparaat wordt gemarkeerd als compatibel**
   - **Beveiligings beleid voor apps vereisen (preview-versie)**
   - **Alle geselecteerde besturings elementen vereisen**   
 
      ![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/13.png)

**Stap 2: een beleid voor voorwaardelijke toegang voor Azure AD configureren voor Exchange Online met ActiveSync**

Configureer voor het beleid voor voorwaardelijke toegang in deze stap de volgende onderdelen:

![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/06.png)

1. Voer de naam van het beleid voor voorwaardelijke toegang in.
1. Selecteer onder **toewijzingen**in **gebruikers en groepen**ten minste één gebruiker of groep voor elk beleid voor voorwaardelijke toegang.
1. Selecteer **Office 365 Exchange Online**in **Cloud-apps**. 

   ![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/07.png)

1. In **voor waarden**configureert u **client-apps (preview-versie)** . 

   Selecteer in **client-apps (preview)** de optie **Mobile apps en desktop-clients** en **Exchange ActiveSync-clients**.

   ![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/92.png)

1. Selecteer onder **besturings elementen voor toegang**de volgende opties:
   - **Vereisen dat het apparaat wordt gemarkeerd als compatibel**
   - **Beveiligings beleid voor apps vereisen (preview-versie)**
   - **Alle geselecteerde besturings elementen vereisen**   
 
      ![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/13.png)

**Stap 3: het beveiligings beleid voor apps in intune configureren voor iOS-en Android-client toepassingen**

![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/09.png)

Zie [apps en gegevens beveiligen met Microsoft intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)voor meer informatie.

## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>App-beveiliging of beleid op basis van apps voor Exchange Online en share point online

Dit scenario bestaat uit een beleid voor app-beveiliging of goedgekeurde apps voor toegang tot Exchange Online en share point online.

### <a name="scenario-playbook"></a>Scenario Playbook

In dit scenario wordt ervan uitgegaan dat een gebruiker:

- Hiermee configureert u client toepassingen die zich bevinden in de lijst met apps die ondersteuning bieden voor de vereiste app-beveiligings beleid of de vereiste apps.  
- Maakt gebruik van client toepassingen die voldoen aan de vereisten voor het app-beveiligings beleid en kunnen een intune-beveiligings beleid voor apps ontvangen.
- Maakt gebruik van client toepassingen die voldoen aan de beleids vereisten voor goedgekeurde apps die ondersteuning bieden voor intune-beleid voor app-beveiliging.
- Hiermee opent u de toepassing voor toegang tot e-mail of documenten.
- Hiermee opent u de Outlook-toepassing en meldt u zich aan met Azure AD-referenties.
- Wordt gevraagd of Microsoft Authenticator moet worden geïnstalleerd voor iOS-gebruik of Intune-bedrijfsportal voor Android-gebruik als deze nog niet zijn geïnstalleerd.
- De toepassing wordt geïnstalleerd en kan worden teruggestuurd naar de Outlook-app om door te gaan.
- Wordt gevraagd om een apparaat te registreren.
- Kan een intune-beveiligings beleid voor apps ontvangen.
- Kan toegang krijgen tot e-mail met Outlook en een intune-beveiligings beleid voor apps.
- Heeft toegang tot sites en documenten met een app die niet aan de vereisten voor het app-beveiligings beleid voldoet, maar die worden vermeld in de goedgekeurde app-vereiste.

Er zijn beveiligings beleidsregels voor intune-apps vereist voordat toegang tot Bedrijfs gegevens wordt verleend. Het beleid kan de gebruiker vragen om de toepassing opnieuw te starten of een extra pincode te gebruiken. Dit is het geval als de beleids regels zijn geconfigureerd voor de toepassing en het platform.

**Opmerkingen**

- U kunt dit scenario gebruiken als u zowel app-beveiliging als op apps gebaseerd beleid voor voorwaardelijke toegang wilt ondersteunen.
- In deze *or* -beleids regel worden apps met een vereisten voor app-beveiligings beleid eerst geëvalueerd voor toegang voordat de vereiste client-apps zijn vereist.

### <a name="configuration"></a>Configuratie

**Stap 1: een beleid voor voorwaardelijke toegang voor Azure AD configureren voor Exchange Online en share point online**

Configureer voor het beleid voor voorwaardelijke toegang in deze stap de volgende onderdelen:

![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/62.png)

1. Voer de naam van het beleid voor voorwaardelijke toegang in.
1. Selecteer onder **toewijzingen**in **gebruikers en groepen**ten minste één gebruiker of groep voor elk beleid voor voorwaardelijke toegang.
1. Selecteer in **Cloud**-apps **Office 365 Exchange online** en **Office 365 share point online**. 

   ![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/02.png)

1. Configureer onder **voor waarden** **platformen** en **client-apps (preview-versie)** . 
   1. Selecteer **Android** en **IOS**in **device platforms**.

      ![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/03.png)

   1. Selecteer in **client-apps (preview)** **mobiele apps en desktop-clients** en **moderne verificatie-clients**.

      ![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/91.png)

1. Selecteer onder **besturings elementen voor toegang**de volgende opties:
   - **Goedgekeurde client-app vereisen**
   - **Beveiligings beleid voor apps vereisen (preview-versie)**
   - **Een van de geselecteerde besturings elementen vereisen**
 
      ![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/12.png)

**Stap 2: het beveiligings beleid voor apps in intune configureren voor iOS-en Android-client toepassingen**

![Voorwaardelijke toegang](./media/app-protection-based-conditional-access/09.png)

Zie [apps en gegevens beveiligen met Microsoft intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Als u wilt weten hoe u een beleid voor voorwaardelijke toegang kunt configureren, raadpleegt u [MFA vereisen voor specifieke apps met voorwaardelijke toegang Azure Active Directory](app-based-mfa.md).
- Als u klaar bent voor het configureren van beleid voor voorwaardelijke toegang voor uw omgeving, raadpleegt u [Aanbevolen procedures voor voorwaardelijke toegang in azure Active Directory](best-practices.md).
