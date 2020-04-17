---
title: Cloud-apps of -acties in beleid voor voorwaardelijke toegang - Azure Active Directory
description: Wat zijn cloud-apps of -acties in een Azure AD Conditional Access-beleid
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b15b022726c09ccbaf9674775d114c8dd1916e1d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457294"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>Voorwaardelijke toegang: cloud-apps of -acties

Cloud-apps of -acties zijn een belangrijk signaal in een beleid voor voorwaardelijke toegang. Met beleid voor voorwaardelijke toegang kunnen beheerders besturingselementen toewijzen aan specifieke toepassingen of acties.

- Beheerders kunnen kiezen uit de lijst met toepassingen die ingebouwde Microsoft-toepassingen bevatten en alle [geïntegreerde Azure AD-toepassingen,](../manage-apps/what-is-application-management.md) waaronder galerie, niet-galerij en toepassingen die zijn gepubliceerd via [Application Proxy.](../manage-apps/what-is-application-proxy.md)
- Beheerders kunnen ervoor kiezen om beleid niet te definiëren op basis van een cloudtoepassing, maar op een gebruikersactie. De enige ondersteunde actie is Beveiligingsgegevens registreren (voorbeeld), waardoor voorwaardelijke toegang controles rond de [gecombineerde registratie-ervaring van beveiligingsgegevens](../authentication/howto-registration-mfa-sspr-combined.md)kan worden uitgevoerd.

![Een beleid voor voorwaardelijke toegang definiëren en cloud-apps opgeven](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Microsoft-cloudtoepassingen

Veel van de bestaande Microsoft-cloudtoepassingen zijn opgenomen in de lijst met toepassingen waaruit u kiezen. 

Beheerders kunnen een beleid voor voorwaardelijke toegang toewijzen aan de volgende cloud-apps van Microsoft. Sommige apps, zoals de Office 365 (preview) en Microsoft Azure Management, bevatten meerdere gerelateerde onderliggende apps of services. De volgende lijst is niet volledig en kan worden gewijzigd.

- [Office 365 (voorbeeld)](#office-365-preview)
- Azure Analysis Services
- Azure DevOps
- [Azure SQL Database en Data Warehouse](../../sql-database/sql-database-conditional-access.md)
- Dynamics CRM Online
- Analyse van Microsoft Application Insights
- [Microsoft Azure-informatiebeveiliging](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Microsoft Azure-beheer](#microsoft-azure-management)
- Microsoft Azure-abonnementsbeheer
- Microsoft Cloud App Security
- Access Control Portal voor Microsoft Commerce-hulpprogramma's
- Verificatieservice voor Microsoft Commerce-hulpprogramma's
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Microsoft Intune-inschrijving](/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Zoeken in Bing
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Office Delve
- Office Sway
- Outlook-groepen
- Power BI-service
- Project Online
- Skype voor Bedrijven Online
- Virtual Private Network (VPN)
- Windows Defender ATP

### <a name="office-365-preview"></a>Office 365 (voorbeeld)

Office 365 biedt cloudgebaseerde productiviteits- en samenwerkingsservices zoals Exchange, SharePoint en Microsoft Teams. Office 365-cloudservices zijn sterk geïntegreerd om soepele en collaboratieve ervaringen te garanderen. Deze integratie kan verwarring veroorzaken bij het maken van beleid, omdat sommige apps, zoals Microsoft Teams, afhankelijk zijn van andere apps, zoals SharePoint of Exchange.

De Office 365 (preview) app maakt het mogelijk om deze services in één keer te targeten. We raden u aan de nieuwe Office 365-app (preview) te gebruiken in plaats van afzonderlijke cloud-apps te targeten. Door deze groep toepassingen te targeten, u problemen voorkomen die zich kunnen voordoen als gevolg van inconsistent beleid en afhankelijkheden.

Beheerders kunnen ervoor kiezen om specifieke apps uit te sluiten van het beleid als ze dat willen door de Office 365 -app (preview) op te sluiten en de specifieke apps van hun keuze in het beleid uit te sluiten.

Belangrijke toepassingen die zijn opgenomen in de client-app Office 365 (preview):

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Stream
   - Microsoft To-Do
   - Microsoft Teams
   - Office 365 Exchange Online
   - Office 365 SharePoint Online
   - Office 365-zoekservice
   - Office 365 Yammer
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - PowerApps
   - Skype voor Bedrijven Online
   - Sway

### <a name="microsoft-azure-management"></a>Microsoft Azure-beheer

De Microsoft Azure Management-toepassing bevat meerdere onderliggende services. 

   - Azure Portal
   - Azure Resource Manager-provider
   - Klassieke implementatiemodel API's
   - Azure PowerShell
   - Beheerdersportal voor Visual Studio-abonnementen
   - Azure DevOps
   - Azure Data Factory-portal

> [!NOTE]
> De Microsoft Azure Management-toepassing is van toepassing op Azure PowerShell, dat de Azure Resource Manager API aanroept. Het is niet van toepassing op Azure AD PowerShell, dat Microsoft Graph aanroept.

## <a name="other-applications"></a>Andere toepassingen

Naast de Microsoft-apps kunnen beheerders elke Azure AD-geregistreerde toepassing toevoegen aan het beleid voor voorwaardelijke toegang. Deze aanvragen kunnen bestaan uit: 

- Toepassingen die zijn gepubliceerd via [Azure AD-toepassingsproxy](../manage-apps/what-is-application-proxy.md)
- [Toepassingen toegevoegd vanuit de galerie](../manage-apps/add-application-portal.md)
- [Aangepaste toepassingen die niet in de galerie zijn](../manage-apps/add-non-gallery-app.md)
- [Verouderde toepassingen gepubliceerd via app delivery controllers en netwerken](../manage-apps/secure-hybrid-access.md)

## <a name="user-actions"></a>Gebruikersacties

Gebruikersacties zijn taken die door een gebruiker kunnen worden uitgevoerd. De enige momenteel ondersteunde actie is **Beveiligingsgegevens registreren**, waarmee het beleid voor voorwaardelijke toegang kan worden gehandhaafd wanneer gebruikers die zijn ingeschakeld voor gecombineerde registratie, proberen hun beveiligingsgegevens te registreren. Meer informatie is te vinden in het artikel, [Gecombineerde security informatie registratie](../authentication/concept-registration-mfa-sspr-combined.md).

## <a name="next-steps"></a>Volgende stappen

- [Voorwaardelijke toegang: voorwaarden](concept-conditional-access-conditions.md)

- [Gemeenschappelijk beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)
- [Clienttoepassingsafhankelijkheden](service-dependencies.md)
