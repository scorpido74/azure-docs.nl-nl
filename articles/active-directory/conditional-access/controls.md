---
title: Toegangs beheer in Azure Active Directory voorwaardelijke toegang
description: Meer informatie over hoe toegangs beheer in Azure Active Directory voorwaardelijke toegang werkt.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: cac92da744b3d5b7aeaa325c7cc564a3d7e2abdd
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380811"
---
# <a name="what-are-access-controls-in-azure-active-directory-conditional-access"></a>Wat zijn toegangs beheer in Azure Active Directory voorwaardelijke toegang?

Met [Azure Active Directory (Azure AD) voorwaardelijke toegang](../active-directory-conditional-access-azure-portal.md)kunt u bepalen hoe geautoriseerde gebruikers toegang hebben tot uw Cloud-apps. In een beleid voor voorwaardelijke toegang definieert u het antwoord ("doe dit") om de reden voor het activeren van uw beleid ("wanneer dit gebeurt").

![Beheer](./media/controls/10.png)

In de context van voorwaardelijke toegang

- "**Wanneer dit gebeurt**" worden **voor waarden** genoemd
- "**Then dit**" heet **Access controls**

De combi natie van een voorwaarde instructie met uw Besturings elementen vertegenwoordigt een beleid voor voorwaardelijke toegang.

![Beheer](./media/controls/61.png)

Elk besturings element is een vereiste waaraan de persoon of het systeem moet voldoen of een beperking van wat de gebruiker kan doen na het aanmelden.

Er zijn twee typen besturings elementen:

- **Besturings elementen toewijzen** aan poort toegang
- **Sessie besturings elementen** -om de toegang binnen een sessie te beperken

In dit onderwerp worden de verschillende besturings elementen beschreven die beschikbaar zijn in voorwaardelijke toegang voor Azure AD. 

## <a name="grant-controls"></a>Besturings elementen toewijzen

Met besturings elementen voor Grant kunt u de toegang geheel blok keren of toegang toestaan met aanvullende vereisten door de gewenste besturings elementen te selecteren. Voor meerdere besturings elementen kunt u het volgende vereisen:

- Alle geselecteerde besturings elementen waaraan moet worden voldaan (*en*)
- Eén geselecteerd besturings element waaraan moet worden voldaan (*of*)

![Beheer](./media/controls/18.png)

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication

U kunt dit besturings element gebruiken om multi-factor Authentication te vereisen voor toegang tot de opgegeven Cloud-app. Dit besturings element ondersteunt de volgende multi-factor-providers:

- Azure Multi-Factor Authentication
- Een on-premises multi-factor Authentication-provider, gecombineerd met Active Directory Federation Services (AD FS).

Het gebruik van multi-factor Authentication helpt resources te beschermen tegen toegang door een niet-geautoriseerde gebruiker die mogelijk toegang heeft verkregen tot de primaire referenties van een geldige gebruiker.

### <a name="compliant-device"></a>Compatibel apparaat

U kunt beleids regels voor voorwaardelijke toegang configureren op basis van apparaten. Het doel van een op apparaten gebaseerd beleid voor voorwaardelijke toegang is om alleen toegang te verlenen aan de geselecteerde Cloud-apps van [beheerde apparaten](require-managed-devices.md). Het vereisen van een apparaat dat moet worden gemarkeerd als compatibel is een optie die u nodig hebt om de toegang tot beheerde apparaten te beperken. Een apparaat kan worden gemarkeerd als compatibel door intune (voor elk besturings systeem van een apparaat) of door het MDM-systeem van derden voor Windows 10-apparaten. MDM-systemen van derden voor apparaten met een ander besturings systeem dan Windows 10 worden niet ondersteund. 

Uw apparaat moet worden geregistreerd bij Azure AD voordat het kan worden gemarkeerd als compatibel. Als u een apparaat wilt registreren, hebt u drie opties: 

- Azure AD-geregistreerde apparaten
- Azure AD-gekoppelde apparaten  
- Hybride Azure AD-gekoppelde apparaten

Deze drie opties worden beschreven in het artikel [Wat is een apparaat-id?](../devices/overview.md)

Zie voor meer informatie [beheerde apparaten vereisen voor toegang tot Cloud-apps met voorwaardelijke toegang](require-managed-devices.md).

### <a name="hybrid-azure-ad-joined-device"></a>Hybride Azure AD-apparaat toegevoegd

Het vereisen van een hybride Azure AD-apparaat dat is gekoppeld, is een andere optie die u nodig hebt voor het configureren van op apparaten gebaseerd beleid voor voorwaardelijke toegang. Deze vereiste verwijst naar Windows-Desk tops, laptops en zakelijke tablets die deel uitmaken van een on-premises Active Directory. Als deze optie is geselecteerd, verleent het beleid voor voorwaardelijke toegang toegang tot toegangs pogingen die zijn gemaakt met apparaten die zijn gekoppeld aan uw on-premises Active Directory en uw Azure Active Directory.  

Zie [Azure Active Directory op apparaat gebaseerd beleid voor voorwaardelijke toegang instellen](require-managed-devices.md)voor meer informatie.

### <a name="approved-client-app"></a>Goedgekeurde client-app

Omdat uw werk nemers mobiele apparaten gebruiken voor zowel privé-als werk taken, kunt u de Bedrijfs gegevens die toegankelijk zijn met apparaten beveiligen, zelfs als ze niet door u worden beheerd.
U kunt [intune-beveiligings beleid voor apps](https://docs.microsoft.com/intune/app-protection-policy) gebruiken om de gegevens van uw bedrijf onafhankelijk van elke MDM-oplossing (Mobile-Device Management) te beveiligen.

Met goedgekeurde client-apps kunt u een client-app vereisen die toegang probeert te krijgen tot uw Cloud-apps voor het ondersteunen van [intune-beveiligings beleid voor apps](https://docs.microsoft.com/intune/app-protection-policy). U kunt bijvoorbeeld de toegang tot Exchange Online beperken tot de Outlook-app. Een beleid voor voorwaardelijke toegang dat goedgekeurde client-apps vereist, wordt ook wel [op apps gebaseerd beleid voor voorwaardelijke toegang](app-based-conditional-access.md)genoemd. Zie voor een lijst met ondersteunde goedgekeurde client-apps de [vereiste goedgekeurde client-app](technical-reference.md#approved-client-app-requirement).

### <a name="app-protection-policy-preview"></a>Beveiligings beleid voor apps (preview-versie)

Omdat uw werk nemers mobiele apparaten gebruiken voor zowel privé-als werk taken, kunt u de Bedrijfs gegevens die toegankelijk zijn met apparaten beveiligen, zelfs als ze niet door u worden beheerd.
U kunt [intune-beveiligings beleid voor apps](https://docs.microsoft.com/intune/app-protection-policy) gebruiken om de gegevens van uw bedrijf onafhankelijk van elke MDM-oplossing (Mobile-Device Management) te beveiligen.

Met het app-beveiligings beleid kunt u de toegang beperken tot client toepassingen die zijn gerapporteerd aan Azure AD heeft het [intune-beveiligings beleid voor apps](https://docs.microsoft.com/intune/app-protection-policy)ontvangen. U kunt bijvoorbeeld de toegang tot Exchange Online beperken tot de Outlook-app die een intune-beleid voor app-beveiliging heeft. Een beleid voor voorwaardelijke toegang dat het beveiligings beleid voor apps vereist, wordt ook wel het [beleid voor voorwaardelijke toegang op basis van apps beveiliging](app-protection-based-conditional-access.md)genoemd. 

Het apparaat moet worden geregistreerd bij Azure AD voordat een toepassing kan worden gemarkeerd als beveiligd beleid.

Zie [vereisten voor app-beveiligings beleid](technical-reference.md#app-protection-policy-requirement)voor een lijst met ondersteunde, door beleid beveiligde client-apps.

### <a name="terms-of-use"></a>Gebruiksvoorwaarden

U kunt een gebruiker in uw Tenant verplichten om toestemming te geven voor de gebruiks voorwaarden alvorens toegang te krijgen tot een resource. Als beheerder kunt u gebruiks voorwaarden configureren en aanpassen door een PDF-document te uploaden. Als een gebruiker binnen het bereik van dit besturings element valt, wordt de toegang tot een toepassing alleen verleend als de gebruiks voorwaarden zijn overeengekomen.

## <a name="custom-controls-preview"></a>Aangepaste besturings elementen (preview-versie)

Aangepaste besturings elementen zijn een functie van de Azure Active Directory Premium P1-editie. Wanneer u aangepaste besturings elementen gebruikt, worden uw gebruikers omgeleid naar een compatibele service om te voldoen aan aanvullende vereisten buiten Azure Active Directory. Om aan dit besturings element te voldoen, wordt de browser van een gebruiker omgeleid naar de externe service, worden eventuele vereiste verificatie-of validatie activiteiten uitgevoerd en wordt vervolgens teruggeleid naar Azure Active Directory. Azure Active Directory controleert het antwoord en, als de gebruiker is geverifieerd of gevalideerd, wordt de gebruiker in de stroom voor voorwaardelijke toegang voortgezet.

Met deze besturings elementen wordt het gebruik van bepaalde externe of aangepaste services als besturings elementen voor voorwaardelijke toegang toegestaan en worden de mogelijkheden van voorwaardelijke toegang in het algemeen uitgebreid.

Providers die momenteel een compatibele service aanbieden, zijn onder andere:

- [Duo-beveiliging](https://duo.com/docs/azure-ca)
- [Entrust Datacard](https://www.entrustdatacard.com/products/authentication/intellitrust)
- [GSMA](https://mobileconnect.io/azure/)
- [Ping-identiteit](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html)
- RSA
- [SecureAuth](https://docs.secureauth.com/pages/viewpage.action?pageId=47238992#)
- [Silverfort](https://www.silverfort.io/company/using-silverfort-mfa-with-azure-active-directory/)
- [Symantec VIP](https://help.symantec.com/home/VIP_Integrate_with_Azure_AD)
- [Thales (Gemalto)](https://resources.eu.safenetid.com/help/AzureMFA/Azure_Help/Index.htm)
- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

Neem rechtstreeks contact op met de provider voor meer informatie over deze services.

### <a name="creating-custom-controls"></a>Aangepaste besturings elementen maken

Als u een aangepast besturings element wilt maken, moet u eerst contact opnemen met de provider die u wilt gebruiken. Elke niet-micro soft-provider heeft een eigen proces en vereisten om zich aan te melden, te abonneren of anderszins deel te nemen aan de service, en om aan te geven dat u wilt integreren met voorwaardelijke toegang. Op dat moment geeft de provider u een blok gegevens in JSON-indeling. Met deze gegevens kan de provider en de voorwaardelijke toegang samen werken voor uw Tenant, wordt het nieuwe besturings element gemaakt en wordt gedefinieerd hoe voorwaardelijke toegang kan zien of uw gebruikers verificatie hebben uitgevoerd met de provider.

Aangepaste besturings elementen kunnen niet worden gebruikt met automatisering van identiteits beveiliging waarvoor multi-factor Authentication is vereist of voor het verhogen van rollen in privileged Identity Manager (PIM).

Kopieer de JSON-gegevens en plak deze in het bijbehorende tekstvak. Breng geen wijzigingen aan in de JSON, tenzij u de wijziging die u aanbrengt expliciet begrijpt. Als u een wijziging aanbrengt, kan de verbinding tussen de provider en micro soft worden verbroken en kunnen u en uw gebruikers uw accounts vergren delen.

De optie voor het maken van een aangepast besturings element bevindt zich in de sectie **beheren** van de pagina **voorwaardelijke toegang** .

![Beheer](./media/controls/82.png)

Als u op **nieuw aangepast besturings element**klikt, wordt een Blade geopend met een tekstvak voor de JSON-gegevens van uw besturings element.  

![Beheer](./media/controls/81.png)

### <a name="deleting-custom-controls"></a>Aangepaste besturings elementen verwijderen

Als u een aangepast besturings element wilt verwijderen, moet u er eerst voor zorgen dat het niet wordt gebruikt in een beleid voor voorwaardelijke toegang. Eenmaal voltooid:

1. Ga naar de lijst met aangepaste besturings elementen
1. Klik op...  
1. Selecteer **Verwijderen**.

### <a name="editing-custom-controls"></a>Aangepaste besturings elementen bewerken

Als u een aangepast besturings element wilt bewerken, moet u het huidige besturings element verwijderen en een nieuw besturings element maken met de bijgewerkte gegevens.

## <a name="session-controls"></a>Sessie besturings elementen

Sessie besturings elementen maken beperkte ervaring in een Cloud-app mogelijk. De sessie besturings elementen worden afgedwongen door Cloud-apps en zijn afhankelijk van aanvullende informatie van Azure AD naar de app over de sessie.

![Beheer](./media/controls/31.png)

### <a name="use-app-enforced-restrictions"></a>Door apps afgedwongen beperkingen gebruiken

U kunt dit besturings element gebruiken om te vereisen dat Azure AD apparaatgegevens doorgeeft aan de geselecteerde Cloud-apps. De apparaatgegevens maken het mogelijk om te bepalen of een verbinding tot stand wordt gebracht vanaf een apparaat dat compatibel is met of een domein. Dit besturings element ondersteunt alleen share point online en Exchange Online als geselecteerde Cloud-apps. Als deze functie is ingeschakeld, gebruikt de Cloud-app de apparaatgegevens om gebruikers, afhankelijk van de status van het apparaat, te voorzien van een beperkte of volledige ervaring.

Raadpleeg voor meer informatie:

- [Beperkte toegang inschakelen met share point online](https://aka.ms/spolimitedaccessdocs)
- [Beperkte toegang inschakelen met Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="next-steps"></a>Volgende stappen

- Als u wilt weten hoe u een beleid voor voorwaardelijke toegang kunt configureren, raadpleegt u [MFA vereisen voor specifieke apps met voorwaardelijke toegang Azure Active Directory](app-based-mfa.md).
- Als u klaar bent voor het configureren van beleid voor voorwaardelijke toegang voor uw omgeving, raadpleegt u de [Aanbevolen procedures voor voorwaardelijke toegang in azure Active Directory](best-practices.md).
