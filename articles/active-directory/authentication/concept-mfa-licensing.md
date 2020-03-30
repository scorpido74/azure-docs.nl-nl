---
title: Azure Multi-Factor Authentication-versies en verbruiksplannen
description: Meer informatie over de Azure Multi-factor Authentication-client en verschillende beschikbare methoden en versies.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e74a7ab0c003aaf9d90211484b39f8322cd9c329
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77647999"
---
# <a name="features-and-licenses-for-azure-multi-factor-authentication"></a>Functies en licenties voor Azure Multi-Factor Authentication

Om gebruikersaccounts in uw organisatie te beschermen, moet meervoudige verificatie worden gebruikt. Deze functie is vooral belangrijk voor accounts met bevoorrechte toegang tot bronnen. Basisfuncties voor meervoudige verificatie zijn zonder extra kosten beschikbaar voor Office 365- en Azure Active Directory-beheerders (Azure AD). Als u de functies voor uw beheerders wilt upgraden of multifactorauthenticatie wilt uitbreiden naar de rest van uw gebruikers, u Azure Multi-Factor Authentication op verschillende manieren aanschaffen.

> [!IMPORTANT]
> In dit artikel worden de verschillende manieren beschreven waarop Azure Multi-Factor Authentication kan worden gelicentieerd en gebruikt. Zie de [prijspagina azure multi-factor authentication voor](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)specifieke informatie over prijzen en facturering.

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Beschikbare versies van Azure Multi-Factor Authentication

Azure Multi-Factor Authentication kan op een aantal verschillende manieren worden gebruikt en gelicentieerd, afhankelijk van de behoeften van uw organisatie. Mogelijk hebt u al recht op Azure Multi-Factor Authentication, afhankelijk van de Azure AD-, Office 365-, EMS- of Microsoft 365-licentie die u momenteel hebt. In de volgende tabel worden de verschillende manieren beschreven om Azure Multi-Factor Authentication te krijgen en enkele van de functies en use cases voor elk.

| Als u een gebruiker bent van | Mogelijkheden en use cases |
| --- | --- |
| EMS of Microsoft 365 E3 en E5 | EMS E3 of Microsoft 365 E3 (inclusief EMS en Office 365), inclusief Azure AD Premium P1. EMS E5 of Microsoft 365 E5 bevat Azure AD Premium P2. U dezelfde functies voor voorwaardelijke toegang gebruiken die in de volgende secties zijn vermeld om gebruikers multi-factor authenticatie te bieden. |
| Azure AD Premium P1 | U [Voorwaardelijke toegang voor Azure AD](../conditional-access/overview.md) gebruiken om gebruikers te vragen om meervoudige verificatie tijdens bepaalde scenario's of gebeurtenissen die aan uw bedrijfsvereisten voldoen. |
| Azure AD Premium P2 | Biedt de sterkste beveiligingspositie en verbeterde gebruikerservaring. Hiermee voegt [voorwaardelijke toegang op basis van risico's](../conditional-access/howto-conditional-access-policy-risk.md) toe aan de Azure AD Premium P1-functies die zich aanpassen aan de patronen van de gebruiker en multi-factor authenticatieprompts minimaliseert. |
| Office 365 Business Premium, E3 of E5 | Azure Multi-Factor Authentication is ingeschakeld of uitgeschakeld voor alle gebruikers, voor alle aanmeldingsgebeurtenissen. Er is geen mogelijkheid om alleen multi-factor authenticatie in te schakelen voor een subset van gebruikers, of alleen onder bepaalde scenario's. Beheer is via de Office 365-portal. Upgrade naar Azure AD Premium P1 of P2 voor een verbeterde gebruikerservaring en gebruik Voorwaardelijke toegang. Zie [Secure Office 365-bronnen met meervoudige verificatie](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6)voor meer informatie. |
| Azure AD Free | U [beveiligingsstandaardinstellingen](../fundamentals/concept-fundamentals-security-defaults.md) gebruiken om meervoudige verificatie voor alle gebruikers in te schakelen, elke keer dat er een verificatieverzoek wordt ingediend. U hebt geen gedetailleerde controle over ingeschakelde gebruikers of scenario's, maar het biedt wel die extra beveiligingsstap.<br /> Zelfs wanneer beveiligingsstandaardinstellingen niet worden gebruikt om meervoudige verificatie voor iedereen in te schakelen, kunnen gebruikers die de azure *AD Global Administrator-rol* toegewezen hebben, worden geconfigureerd om multifactorauthenticatie te gebruiken. Deze functie van de gratis laag zorgt ervoor dat de kritieke beheerdersaccounts worden beschermd door meervoudige verificatie. |

## <a name="feature-comparison-of-versions"></a>Functievergelijking van versies

In de volgende tabel vindt u een lijst met de functies die beschikbaar zijn in de verschillende versies van Azure Multi-Factor Authentication. Plan uw behoeften voor het beveiligen van gebruikersverificatie en bepaal vervolgens welke aanpak aan deze vereisten voldoet. Hoewel Azure AD Free bijvoorbeeld beveiligingsstandaards biedt die Azure Multi-Factor Authentication bieden, kan alleen de mobiele authenticator-app worden gebruikt voor de verificatieprompt, niet voor een telefoongesprek of sms. Deze aanpak kan een beperking zijn als u er niet voor zorgen dat de mobiele verificatie-app is geïnstalleerd op het persoonlijke apparaat van een gebruiker.

| Functie | Azure AD-vrij - Standaardinstellingen voor beveiliging | Azure AD Gratis - Azure AD Global Administrators | Office 365 Business Premium, E3 of E5 | Azure AD Premium P1 of P2 |
| --- |:---:|:---:|:---:|:---:|
| Azure AD-tenantbeheerdersaccounts beveiligen met MFA | ● | ● (*alleen Azure AD Global Administrator-accounts)* | ● | ● |
| Mobiele app als tweede factor | ● | ● | ● | ● |
| Telefoongesprek als tweede factor | | ● | ● | ● |
| SMS als tweede factor | | ● | ● | ● |
| Beheerbeheer over verificatiemethoden | | ● | ● | ● |
| Fraudewaarschuwing | | | | ● |
| MFA-rapporten | | | | ● |
| Aangepaste begroeting voor telefoongesprekken | | | | ● |
| Aangepaste beller-ID voor telefoongesprekken | | | | ● |
| Goedgekeurde IP-adressen | | | | ● |
| MFA herinneren voor vertrouwde apparaten | | ● | ● | ● |
| MFA voor on-premises toepassingen | | | | ● |

> [!IMPORTANT]
> Vanaf maart 2019 zijn telefoongespreksopties niet langer beschikbaar voor Azure Multi-Factor Authentication en Azure Self-Service Password Reset-gebruikers in Azure AD Free / proeftenants. SMS-berichten worden niet beïnvloed door deze wijziging. Telefoongesprekken blijven beschikbaar voor gebruikers in Azure AD Premium P1- of P2-tenants of -toepassingen of Office 365 Business Premium, E3 of E5.

## <a name="purchase-and-enable-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication aanschaffen en inschakelen

Als u Azure Multi-Factor Authentication wilt gebruiken, registreert u zich voor of koopt u een in aanmerking komende Azure AD-laag. Azure AD wordt geleverd in vier edities : Gratis, Office 365 apps-editie (voor Office 365 Business Premium E3- of E5-klanten), Premium P1 en Premium P2.

De Gratis editie is inbegrepen bij een Azure-abonnement. Zie het [gedeelte hieronder](#azure-ad-free-tier) voor informatie over het gebruik van beveiligingsstandaardaat of het beveiligen van accounts met de rol Azure AD *Global Administrator.*

De Azure AD Premium-edities zijn beschikbaar via uw Microsoft-vertegenwoordiger, het [Open Volume License Program](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx)en het Cloud Solution [Providers-programma.](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409) Azure- en Office 365-abonnees kunnen ook Azure Active Directory Premium P1 en P2 online kopen. [Log hier in](https://portal.office.com/Commerce/Catalog.aspx) om te kopen.

> [!IMPORTANT]
> Licenties op basis van consumptie zijn vanaf 1 september 2018 niet meer beschikbaar voor nieuwe klanten. Bestaande klanten die het op verbruik gebaseerde model gebruiken, kunnen gebruik blijven maken van een ingeschakelde gebruiker of per facturering per verificatie.

Nadat u de vereiste Azure AD-laag hebt gekocht, [kunt u Azure Multi-Factor Authentication plannen en implementeren.](howto-mfa-getstarted.md)

### <a name="azure-ad-free-tier"></a>Azure AD-vrije laag

Alle gebruikers in een Azure AD Free-tenant kunnen Azure Multi-Factor-verificatie gebruiken via beveiligingsstandaarden. Met deze beveiligingsstandaarden u Azure Multi-Factor-verificatie inschakelen voor alle gebruikers, telkens wanneer ze zich aanmelden. De mobiele verificatie-app is de enige methode die kan worden gebruikt voor Azure Multi-Factor Authentication bij het gebruik van Azure AD Free-beveiligingsstandaards.

* [Meer informatie over Azure AD-beveiligingsstandaards](../fundamentals/concept-fundamentals-security-defaults.md)
* [Beveiligingsstandaardinstellingen inschakelen voor gebruikers in Azure AD Free](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

Als u Azure Multi-Factor Authentication niet wilt inschakelen voor alle gebruikers en elke aanmeldingsgebeurtenis, u er in plaats daarvan voor kiezen om alleen gebruikersaccounts te beveiligen met de rol *Azure AD Global Administrator.* Deze aanpak biedt extra verificatieprompts voor kritieke beheerdersaccounts. U schakelt Azure Multi-Factor Authentication op een van de volgende manieren in, afhankelijk van het type account dat u gebruikt:

* Als u een Microsoft-account gebruikt, [registreert u zich voor meervoudige verificatie.](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)
* Als u geen Microsoft-account gebruikt, [schakelt u meervoudige verificatie in voor een gebruiker of groep in Azure AD.](howto-mfa-userstates.md)

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Multi-Factor Authentication-prijzen](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)voor meer informatie over de kosten.
