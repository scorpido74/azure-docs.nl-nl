---
title: Client-apps in beleid voor voorwaardelijke toegang-Azure Active Directory
description: ''
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9afc25c906ecd3b7807e6bf3e0763ac1673ebd99
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76544052"
---
# <a name="conditional-access-cloud-apps-and-actions"></a>Voorwaardelijke toegang: Cloud-apps en-acties

Cloud-apps of-acties is een belang rijk onderdeel van een beleid voor voorwaardelijke toegang. Met beleid voor voorwaardelijke toegang kunnen beheerders besturings elementen aan specifieke toepassingen of acties toewijzen. 

- Beheerders kunnen kiezen uit de lijst met toepassingen met ingebouwde micro soft-toepassingen en [Azure AD Integrated-toepassingen](../manage-apps/what-is-application-management.md) , waaronder galerie, niet-galerie en toepassingen die zijn gepubliceerd via [toepassings proxy](../manage-apps/what-is-application-proxy.md).
- Beheerders kunnen ervoor kiezen om beleid te definiëren dat niet is gebaseerd op een Cloud toepassing maar bij een gebruikers actie. De enige ondersteunde actie is het registreren van beveiligings gegevens (preview), waardoor voorwaardelijke toegang kan leiden tot het afdwingen van besturings elementen voor de [registratie van gecombineerde beveiligings gegevens](../authentication/howto-registration-mfa-sspr-combined.md).

![Beleid voor voorwaardelijke toegang definiëren en Cloud-apps opgeven](./media/concept-conditional-access-cloud-apps/conditional-access-define-policy-specify-cloud-apps.png)

## <a name="microsoft-cloud-applications"></a>Micro soft-Cloud toepassingen

Veel van de bestaande micro soft-Cloud toepassingen zijn opgenomen in de lijst met toepassingen waaruit u kunt kiezen. 

Beheerders kunnen een beleid voor voorwaardelijke toegang toewijzen aan de volgende Cloud-apps van micro soft. Sommige apps zoals Office 365 (preview) en Microsoft Azure Management omvatten meerdere verwante onderliggende apps of services. De volgende lijst is niet limitatief en kan worden gewijzigd.

- [Office 365 (preview-versie)](#office-365-preview)
- Azure Analysis Services
- Azure DevOps
- [Azure SQL Database en het Data Warehouse](../../sql-database/sql-database-conditional-access.md)
- Dynamics CRM Online
- Micro soft Application Insights Analytics
- [Microsoft Azure Information Protection](https://docs.microsoft.com/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Microsoft Azure beheer](#microsoft-azure-management)
- Abonnements beheer Microsoft Azure
- Microsoft Cloud App Security
- Micro soft commerce tools Access Control-Portal
- Micro soft commerce tools-verificatie service
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Inschrijving Microsoft Intune](https://docs.microsoft.com/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft PowerApps
- Micro soft Search in Bing
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Office 365 Exchange Online
- Office 365 share point online
- Office 365 Yammer
- Office Delve
- Office Sway
- Outlook Groups
- Power BI-service
- Project Online
- Skype voor Bedrijven Online
- Virtueel particulier netwerk (VPN)
- Windows Defender ATP

### <a name="office-365-preview"></a>Office 365 (preview-versie)

Office 365 biedt productiviteits-en samenwerkings Services op basis van de Cloud, zoals Exchange, share point en micro soft teams. Office 365 Cloud Services zijn diep geïntegreerd om soepele en samen werkende ervaringen te garanderen. Deze integratie kan leiden tot Verwar ring bij het maken van beleid als sommige apps, zoals micro soft-teams, afhankelijk zijn van andere onderdelen, zoals share point of Exchange.

De Office 365-app (preview) maakt het mogelijk om deze services in één keer te richten. We raden u aan de nieuwe app Office 365 (preview) te gebruiken, in plaats van afzonderlijke Cloud-apps te richten. Gericht op deze groep toepassingen helpt problemen te voor komen die zich kunnen voordoen als gevolg van inconsistent beleid en afhankelijkheden.

Beheerders kunnen ervoor kiezen om specifieke apps uit te sluiten van het beleid als ze dit willen doen door de Office 365 (preview)-app op te nemen en de specifieke apps van hun keuze in het beleid uit te sluiten.

Belangrijkste toepassingen die zijn opgenomen in de client-app voor Office 365 (preview):

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Stream
   - Microsoft To-Do
   - Microsoft Teams
   - Office 365 Exchange Online
   - Office 365 share point online
   - Office 365 Search Service
   - Office 365 Yammer
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - PowerApps
   - Skype voor Bedrijven Online
   - Sway

### <a name="microsoft-azure-management"></a>Microsoft Azure beheer

De Microsoft Azure-beheer toepassing bevat meerdere onderliggende services. 

   - Azure Portal
   - Azure Resource Manager provider
   - Klassieke implementatie model-Api's
   - Azure PowerShell
   - Beheerders portal voor Visual Studio-abonnementen
   - Azure DevOps
   - Azure Data Factory Portal

> [!NOTE]
> De Microsoft Azure-beheer toepassing is van toepassing op Azure PowerShell, die de Azure Resource Manager API aanroept. Het is niet van toepassing op Azure AD Power shell, die Microsoft Graph aanroept.

## <a name="other-applications"></a>Andere toepassingen

Naast de micro soft-apps kunnen beheerders een door Azure AD geregistreerde toepassing toevoegen aan beleid voor voorwaardelijke toegang. Deze toepassingen kunnen bestaan uit: 

- Toepassingen die zijn gepubliceerd via [Azure AD-toepassingsproxy](../manage-apps/what-is-application-proxy.md)
- [Toepassingen die zijn toegevoegd vanuit de galerie](../manage-apps/add-application-portal.md)
- [Aangepaste toepassingen die niet voor komt in de galerie](../manage-apps/add-non-gallery-app.md)
- [Verouderde toepassingen die zijn gepubliceerd via app Delivery controllers en Networks](../manage-apps/secure-hybrid-access.md)

## <a name="user-actions"></a>Gebruikersacties

Gebruikers acties zijn taken die door een gebruiker kunnen worden uitgevoerd. De enige bewerking die momenteel wordt ondersteund, is het **registreren van beveiligings gegevens (preview)** , waardoor het beleid voor voorwaardelijke toegang kan worden afgedwongen wanneer gebruikers die zijn ingeschakeld voor de gecombineerde registratie poging hun beveiligings gegevens te registreren. Meer informatie vindt u in het artikel, [registratie van gecombineerde beveiligings gegevens (preview)](../authentication/concept-registration-mfa-sspr-combined.md).

## <a name="next-steps"></a>Volgende stappen

- [Onderdelen van beleid voor voorwaardelijke toegang](concept-conditional-access-policies.md)
- [Client toepassings afhankelijkheden](service-dependencies.md)
- [Microsoft Intune: MFA vereisen voor apparaatregistratie](https://docs.microsoft.com/intune/enrollment/multi-factor-authentication)
