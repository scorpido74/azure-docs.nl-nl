---
title: Verouderde verificatieprotocollen blokkeren in Azure AD
description: Meer informatie over hoe en waarom organisaties verouderde verificatieprotocollen moeten blokkeren
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55ce0233fdefb8360376e94c0baafabe4c62ced7
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309198"
---
# <a name="blocking-legacy-authentication"></a>Verouderde verificatie blokkeren
 
Azure Active Directory (Azure AD) ondersteunt een breed scala aan verificatieprotocollen, waaronder verouderde verificatie, om uw gebruikers eenvoudig toegang te geven tot uw cloud-apps. Legacy-verificatie is een term die verwijst naar een verificatieverzoek van:

- Oudere Office-clients die geen moderne verificatie gebruiken (bijvoorbeeld Office 2010-client)
- Elke client die oudere e-mailprotocollen gebruikt, zoals IMAP/SMTP/POP3

Vandaag de dag is het merendeel van alle compromitterende aanmeldingspogingen afkomstig van legacy-verificatie. Legacy-verificatie biedt geen ondersteuning voor multi-factor authenticatie (MFA). Zelfs als u een MFA-beleid in uw map hebt ingeschakeld, kan een slechte actor zich verifiëren met behulp van een verouderd protocol en MFA omzeilen. De beste manier om uw account te beschermen tegen kwaadaardige verificatieverzoeken die door oudere protocollen worden gedaan, is door deze pogingen helemaal te blokkeren.

## <a name="identify-legacy-authentication-use"></a>Identificeer het gebruik van verouderde verificatie

Voordat u oudere verificatie in uw directory blokkeren, moet u eerst begrijpen of uw gebruikers apps hebben die verouderde verificatie gebruiken en hoe deze van invloed is op uw algemene directory. Azure AD-aanmeldingslogboeken kunnen worden gebruikt om te begrijpen of u verouderde verificatie gebruikt.

1. Navigeer naar de **Azure portal** >Azure Active **Directory-aanmeldingen** **Azure Active Directory** >.
1. Voeg de kolom **Client-app** toe als deze niet wordt weergegeven door op de > **kolom** **Kolommenclientapp**te klikken .
1. Filter op **client-app**  > controleert alle opties **voor verouderde verificatieclients.**
1. Filteren op > **statussucces**. **Status** 
1. Vouw uw datumbereik indien nodig uit met het filter **Datum.**

Filteren toont alleen succesvolle aanmeldingspogingen die zijn uitgevoerd door de geselecteerde verouderde verificatieprotocollen. Als u op elke afzonderlijke aanmeldingspoging klikt, ziet u meer details. In de kolom Client-app of het veld Client-app onder het tabblad Basisgegevens na het selecteren van een afzonderlijke rij gegevens wordt aangegeven welk verouderd verificatieprotocol is gebruikt. Deze logboeken geven aan welke gebruikers nog afhankelijk zijn van verouderde verificatie en welke toepassingen verouderde protocollen gebruiken om verificatieverzoeken in te dienen. Voor gebruikers die niet in deze logboeken worden weergegeven en waarvan is bevestigd dat ze geen verouderde verificatie gebruiken, implementeert u een beleid voor voorwaardelijke toegang of schakelt u het beleid basislijn in: blokkeer alleen verouderde verificatie voor deze gebruikers.

## <a name="moving-away-from-legacy-authentication"></a>Afstappen van verouderde verificatie 

Zodra u een beter idee hebt van wie legacy-verificatie in uw directory gebruikt en welke toepassingen ervan afhankelijk zijn, is de volgende stap het upgraden van uw gebruikers om moderne verificatie te gebruiken. Moderne verificatie is een methode voor identiteitsbeheer die veiligere gebruikersverificatie en autorisatie biedt. Als u een MFA-beleid op uw directory hebt, zorgt moderne verificatie ervoor dat de gebruiker wordt gevraagd om MFA wanneer dat nodig is. Het is het veiligere alternatief voor verouderde verificatieprotocollen.

In deze sectie vindt u een stapsgewijs overzicht van het bijwerken van uw omgeving naar moderne verificatie. Lees de onderstaande stappen voordat u een beleid voor het blokkeren van verouderde verificatiein uw organisatie inschakelt.

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>Stap 1: Moderne verificatie inschakelen in uw directory

De eerste stap in het inschakelen van moderne verificatie is ervoor te zorgen dat uw directory moderne verificatie ondersteunt. Moderne verificatie is standaard ingeschakeld voor mappen die op of na 1 augustus 2017 zijn gemaakt. Als uw map vóór deze datum is gemaakt, moet u handmatig moderne verificatie voor uw map inschakelen met de volgende stappen:

1. Controleer of uw directory al moderne `Get-CsOAuthConfiguration` verificatie ondersteunt door te worden uitgevoerd vanuit de [Skype voor Bedrijven Online PowerShell-module.](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell)
1. Als uw opdracht `OAuthServers` een lege eigenschap retourneert, wordt Moderne verificatie uitgeschakeld. Werk de instelling bij `Set-CsOAuthConfiguration`om moderne verificatie in te schakelen met behulp van . Als `OAuthServers` uw woning een vermelding bevat, bent u klaar om te gaan.

Zorg ervoor dat u deze stap voltooit voordat u verder gaat. Het is van cruciaal belang dat uw directoryconfiguraties eerst worden gewijzigd omdat ze bepalen welk protocol door alle Office-clients wordt gebruikt. Zelfs als u Office-clients gebruikt die moderne verificatie ondersteunen, worden deze standaard gebruikt voor het gebruik van oudere protocollen als moderne verificatie is uitgeschakeld in uw directory.

### <a name="step-2-office-applications"></a>Stap 2: Office-toepassingen

Zodra u moderne verificatie in uw map hebt ingeschakeld, u beginnen met het bijwerken van toepassingen door moderne verificatie voor Office-clients in te schakelen. Office 2016 of hoger clients ondersteunen standaard moderne verificatie. Er zijn geen extra stappen nodig.

Als u Office 2013 Windows-clients of ouder gebruikt, raden we u aan te upgraden naar Office 2016 of hoger. Zelfs nadat de vorige stap van het inschakelen van moderne verificatie in uw directory is voltooid, blijven de oudere Office-toepassingen oudere verificatieprotocollen gebruiken. Als u Office 2013-clients gebruikt en niet onmiddellijk upgraden naar Office 2016 of hoger, voert u de stappen in het volgende artikel uit om [Moderne verificatie voor Office 2013 in te schakelen op Windows-apparaten.](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication) Om uw account te beschermen terwijl u oudere verificatie gebruikt, raden we u aan sterke wachtwoorden in uw directory te gebruiken. Bekijk [azure AD-wachtwoordbeveiliging](../authentication/concept-password-ban-bad.md) om zwakke wachtwoorden in uw directory te verbieden.

Office 2010 biedt geen ondersteuning voor moderne verificatie. U moet alle gebruikers met Office 2010 upgraden naar een recentere versie van Office. We raden u aan een upgrade naar Office 2016 of hoger te maken, omdat deze standaard verouderde verificatie blokkeert.

Als u macOS gebruikt, raden we u aan een upgrade naar Office voor Mac 2016 of hoger te maken. Als u de native mailclient gebruikt, moet u macOS-versie 10.14 of hoger op alle apparaten hebben.

### <a name="step-3-exchange-and-sharepoint"></a>Stap 3: Exchange en SharePoint

Voor Outlook-clients in Windows om moderne verificatie te gebruiken, moet Exchange Online ook moderne verificatie zijn ingeschakeld. Als moderne verificatie is uitgeschakeld voor Exchange Online, gebruiken Windows-outlook-clients die moderne verificatie ondersteunen (Outlook 2013 of hoger) basisverificatie om verbinding te maken met Exchange Online-postvakken.

SharePoint Online is standaard ingeschakeld voor moderne verificatie. Voor mappen die na 1 augustus 2017 zijn gemaakt, is moderne verificatie standaard ingeschakeld in Exchange Online. Als u moderne verificatie echter eerder hebt uitgeschakeld of gebruikt u een map die vóór deze datum is gemaakt, voert u de stappen in het volgende artikel uit om moderne verificatie in Exchange Online in te [schakelen.](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)

### <a name="step-4-skype-for-business"></a>Stap 4: Skype voor Bedrijven

Om verouderde verificatieverzoeken van Skype voor Bedrijven te voorkomen, is het noodzakelijk om moderne verificatie voor Skype voor Bedrijven Online in te schakelen. Voor mappen die na 1 augustus 2017 zijn gemaakt, is moderne verificatie voor Skype voor Bedrijven standaard ingeschakeld.

We raden u aan over te stappen naar Microsoft Teams, dat standaard moderne verificatie ondersteunt. Als u op dit moment echter niet migreren, moet u moderne verificatie voor Skype voor Bedrijven Online inschakelen, zodat Skype voor Bedrijven-clients moderne verificatie gaan gebruiken. Volg de stappen in dit artikel [Skype voor Bedrijven-topologieën die worden ondersteund met moderne verificatie](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported)om moderne verificatie voor Skype voor Bedrijven in te schakelen.

Naast het inschakelen van moderne verificatie voor Skype voor Bedrijven Online, raden we u aan moderne verificatie voor Exchange Online in te schakelen wanneer u moderne verificatie voor Skype voor Bedrijven inschakelt. Met dit proces u de status van moderne verificatie in Exchange Online en Skype voor Bedrijven online synchroniseren en worden meerdere aanmeldingsprompts voor Skype voor Bedrijven-clients voorkomen.

### <a name="step-5-using-mobile-devices"></a>Stap 5: Mobiele apparaten gebruiken

Toepassingen op uw mobiele apparaat moeten ook verouderde verificatie blokkeren. We raden u aan Outlook voor Mobiel te gebruiken. Outlook voor Mobiel ondersteunt standaard moderne verificatie en voldoet aan andere mfa-basislijnbeveiligingsbeleid.

Als u de native iOS-e-mailclient wilt gebruiken, moet u iOS-versie 11.0 of hoger uitvoeren om ervoor te zorgen dat de e-mailclient is bijgewerkt om verouderde verificatie te blokkeren.

### <a name="step-6-on-premises-clients"></a>Stap 6: On-premises klanten

Als u een hybride klant bent die Exchange Server on-premises gebruikt en Skype voor Bedrijven on-premises, moeten beide services worden bijgewerkt om moderne verificatie in te schakelen. Wanneer u moderne verificatie in een hybride omgeving gebruikt, u gebruikers nog steeds on-premises verifiëren. Het verhaal van het autoriseren van hun toegang tot bronnen (bestanden of e-mails) verandert.

Voordat u beginnen met het inschakelen van moderne verificatie on-premises, moet u er zeker van zijn dat u aan de vereisten hebt voldaan. U bent nu klaar om moderne verificatie on-premises in te schakelen.

Stappen voor het inschakelen van moderne verificatie zijn te vinden in de volgende artikelen:

* [Exchange Server on-premises configureren om hybride moderne verificatie te gebruiken](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [Moderne verificatie (ADAL) gebruiken met Skype voor Bedrijven](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="next-steps"></a>Volgende stappen

- [Exchange Server on-premises configureren om hybride moderne verificatie te gebruiken](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
- [Moderne verificatie (ADAL) gebruiken met Skype voor Bedrijven](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)
- [Verouderde verificatie blokkeren](../conditional-access/block-legacy-authentication.md)
