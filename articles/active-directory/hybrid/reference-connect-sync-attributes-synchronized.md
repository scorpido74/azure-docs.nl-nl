---
title: Kenmerken gesynchroniseerd met Azure AD Connect | Microsoft Documenten
description: Hier worden de kenmerken weergegeven die zijn gesynchroniseerd met Azure Active Directory.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c2bb36e0-5205-454c-b9b6-f4990bcedf51
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/24/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a82766be01476890bbf18b518ce21febe0d07f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253609"
---
# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Azure AD Connect-synchronisatie: kenmerken die zijn gesynchroniseerd met Azure Active Directory
In dit onderwerp worden de kenmerken weergegeven die worden gesynchroniseerd met azure AD Connect-synchronisatie.  
De kenmerken worden gegroepeerd in de gerelateerde Azure AD-app.

## <a name="attributes-to-synchronize"></a>Kenmerken die moeten worden gesynchroniseerd
Een veelvoorkomende vraag *is wat de lijst is met minimumkenmerken om te synchroniseren.* De standaard- en aanbevolen aanpak is om de standaardkenmerken te behouden, zodat een volledige GAL (Global Address List) in de cloud kan worden gebouwd en om alle functies in Office 365-workloads te krijgen. In sommige gevallen zijn er enkele kenmerken die uw organisatie niet wilt synchroniseren met de cloud, omdat deze kenmerken gevoelige of PII-gegevens (persoonlijk identificeerbare informatie) bevatten, zoals in dit voorbeeld:  
![slechte eigenschappen](./media/reference-connect-sync-attributes-synchronized/badextensionattribute.png)

Begin in dit geval met de lijst met kenmerken in dit onderwerp en identificeer de kenmerken die gevoelige of PII-gegevens bevatten en niet kunnen worden gesynchroniseerd. Schakel deze kenmerken vervolgens uit tijdens de installatie met [Azure AD-app en kenmerkfiltering](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering).

> [!WARNING]
> Bij het deselecteren van kenmerken moet u voorzichtig zijn en alleen de selectie van deze kenmerken absoluut niet mogelijk om te synchroniseren. Het niet selecteren van andere kenmerken kan een negatieve invloed hebben op functies.
>
>

## <a name="office-365-proplus"></a>Office 365 ProPlus
| Kenmerknaam | Gebruiker | Opmerking |
| --- |:---:| --- |
| accountIngeschakeld |X |Hiermee bepaalt u of een account is ingeschakeld. |
| Cn |X | |
| displayName |X | |
| objectSID |X |mechanische eigenschap. AD-gebruikers-id die wordt gebruikt om de synchronisatie tussen Azure AD en AD te behouden. |
| pwdLastSet |X |mechanische eigenschap. Gebruikt om te weten wanneer al uitgegeven tokens ongeldig te maken. Gebruikt door zowel wachtwoord hash sync, pass-through authenticatie en federatie. |
|samAccountName|X| |
| sourceAnchor bronAnker |X |mechanische eigenschap. Onveranderlijke id om de relatie tussen ADDS en Azure AD te onderhouden. |
| gebruikLocatie |X |mechanische eigenschap. Het land/de regio van de gebruiker. Gebruikt voor licentietoewijzing. |
| userPrincipalName |X |UPN is de inlog-id voor de gebruiker. Meestal hetzelfde als [mail] waarde. |

## <a name="exchange-online"></a>Exchange Online
| Kenmerknaam | Gebruiker | Contactpersoon | Groep | Opmerking |
| --- |:---:|:---:|:---:| --- |
| accountIngeschakeld |X | | |Hiermee bepaalt u of een account is ingeschakeld. |
| Assistent |X |X | | |
| altRecipient (AltRecipient) |X | | |Vereist Azure AD Connect build 1.1.552.0 of erna. |
| authOrig authorig authOrig |X |X |X | |
| c |X |X | | |
| Cn |X | |X | |
| Co |X |X | | |
| bedrijf |X |X | | |
| landCode |X |X | | |
| department |X |X | | |
| description | | |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensieAttribuut1 |X |X |X | |
| extensieAttribuut10 |X |X |X | |
| extensieAttribuut11 |X |X |X | |
| extensieAttribuut12 |X |X |X | |
| extensieAttribuut13 |X |X |X | |
| extensieAttribuut14 |X |X |X | |
| extensieAttribuut15 |X |X |X | |
| extensieAttribuut2 |X |X |X | |
| extensieAttribuut3 |X |X |X | |
| extensieAttribuut4 |X |X |X | |
| extensieAttribuut5 |X |X |X | |
| extensieAttribuut6 |X |X |X | |
| extensieAttribuut7 |X |X |X | |
| extensieAttribuut8 |X |X |X | |
| extensieAttribuut9 |X |X |X | |
| faxnummer |X |X | | |
| givenName |X |X | | |
| homePhone (homePhone) |X |X | | |
| Info |X |X |X |Dit kenmerk wordt momenteel niet verbruikt voor groepen. |
| Initialen |X |X | | |
| l |X |X | | |
| legacyExchangeDN |X |X |X | |
| e-mailNickname |X |X |X | |
| beheerdDoor | | |X | |
| manager |X |X | | |
| lid | | |X | |
| mobiel |X |X | | |
| msDS-HABSeniorityIndex |X |X |X | |
| msDS-FoneticDisplayName |X |X |X | |
| msExchArchiveGUID |X | | | |
| msExchArchiveName |X | | | |
| msExchAssistantNaam |X |X | | |
| msExchAuditAdmin |X | | | |
| msExchAuditDelegate |X | | | |
| msExchAuditDelegateAdmin |X | | | |
| msExchAuditOwner |X | | | |
| msExchBlockedSendersHash |X |X | | |
| msExchBypassAudit |X | | | |
| msExchBypassModerationLink | | |X |Beschikbaar in Azure AD Connect-versie 1.1.524.0 |
| msExchCoManagedByLink | | |X | |
| msExchDelegateListLink |X | | | |
| msExchELCExpirySuspensionEnd |X | | | |
| msExchELCExpirySuspensionStart |X | | | |
| msExchELCBrievenbusVlaggen |X | | | |
| msExchEnableModeration |X | |X | |
| msExchExtensionCustomAttribute1 |X |X |X |Dit kenmerk wordt momenteel niet verbruikt door Exchange Online. |
| msExchExtensionCustomAttribute2 |X |X |X |Dit kenmerk wordt momenteel niet verbruikt door Exchange Online. |
| msExchExtensionCustomAttribute3 |X |X |X |Dit kenmerk wordt momenteel niet verbruikt door Exchange Online. |
| msExchExtensionCustomAttribute4 |X |X |X |Dit kenmerk wordt momenteel niet verbruikt door Exchange Online. |
| msExchExtensionCustomAttribute5 |X |X |X |Dit kenmerk wordt momenteel niet verbruikt door Exchange Online. |
| msExchHideFromAddressLists |X |X |X | |
| msExchImmutableID |X | | | |
| msExchLitigationHoldDate |X |X |X | |
| msExchLitigationHoldOwner |X |X |X | |
| msExchMailboxAuditIngeschakeld |X | | | |
| msExchMailboxAuditLogAgeLimit |X | | | |
| msExchMailboxGuid |X | | | |
| msExchModeratedByLink |X |X |X | |
| msExchModerationFlags |X |X |X | |
| msExchRecipientDisplayType |X |X |X | |
| msExchRecipientTypeDetails |X |X |X | |
| msExchRemoteRecipientType |X | | | |
| msExchRequireAuthToSendTo |X |X |X | |
| msExchResourceCapacity |X | | | |
| msExchResourceDisplay |X | | | |
| msExchResourceMetaData |X | | | |
| msExchResourceSearchProperties |X | | | |
| msExchRetentionCommentaar |X |X |X | |
| msExchRetentionURL |X |X |X | |
| msExchSafeRecipientsHash |X |X | | |
| msExchSafeSendersHash |X |X | | |
| msExchSenderHintTranslations |X |X |X | |
| msExchTeamMailboxVerlopen |X | | | |
| msExchTeamMailboxEigenaren |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| msExchUserHoldPolicies |X | | | |
| msOrg-IsOrganizational | | |X | |
| objectSID |X | |X |mechanische eigenschap. AD-gebruikers-id die wordt gebruikt om de synchronisatie tussen Azure AD en AD te behouden. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelefoon |X |X | | |
| otherHomePhone |X |X | | |
| andereTelefoon |X |X | | |
| Pager |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| Postcode |X |X | | |
| proxyAddresses |X |X |X | |
| openbaarAfgevaardigden |X |X |X | |
| pwdLastSet |X | | |mechanische eigenschap. Gebruikt om te weten wanneer al uitgegeven tokens ongeldig te maken. Wordt gebruikt door zowel wachtwoordsynchronisatie als federatie. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| sn |X |X | | |
| sourceAnchor bronAnker |X |X |X |mechanische eigenschap. Onveranderlijke id om de relatie tussen ADDS en Azure AD te onderhouden. |
| st |X |X | | |
| streetAddress |X |X | | |
| Targetaddress |X |X | | |
| telefoonAssistent |X |X | | |
| telefoonNummer |X |X | | |
| miniatuurfoto |X |X | | |
| titel |X |X | | |
| unauthOrig unauthOrig |X |X |X | |
| gebruikLocatie |X | | |mechanische eigenschap. Het land/de regio van de gebruiker. Gebruikt voor licentietoewijzing. |
| userCertificate |X |X | | |
| userPrincipalName |X | | |UPN is de inlog-id voor de gebruiker. Meestal hetzelfde als [mail] waarde. |
| userSMIMECertificaten |X |X | | |
| wWWHomePage |X |X | | |

## <a name="sharepoint-online"></a>SharePoint Online
| Kenmerknaam | Gebruiker | Contactpersoon | Groep | Opmerking |
| --- |:---:|:---:|:---:| --- |
| accountIngeschakeld |X | | |Hiermee bepaalt u of een account is ingeschakeld. |
| authOrig authorig authOrig |X |X |X | |
| c |X |X | | |
| Cn |X | |X | |
| Co |X |X | | |
| bedrijf |X |X | | |
| landCode |X |X | | |
| department |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensieAttribuut1 |X |X |X | |
| extensieAttribuut10 |X |X |X | |
| extensieAttribuut11 |X |X |X | |
| extensieAttribuut12 |X |X |X | |
| extensieAttribuut13 |X |X |X | |
| extensieAttribuut14 |X |X |X | |
| extensieAttribuut15 |X |X |X | |
| extensieAttribuut2 |X |X |X | |
| extensieAttribuut3 |X |X |X | |
| extensieAttribuut4 |X |X |X | |
| extensieAttribuut5 |X |X |X | |
| extensieAttribuut6 |X |X |X | |
| extensieAttribuut7 |X |X |X | |
| extensieAttribuut8 |X |X |X | |
| extensieAttribuut9 |X |X |X | |
| faxnummer |X |X | | |
| givenName |X |X | | |
| hideDLMembership | | |X | |
| homephone |X |X | | |
| Info |X |X |X | |
| Initialen |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| mail |X |X |X | |
| mailnickname |X |X |X | |
| beheerdDoor | | |X | |
| manager |X |X | | |
| lid | | |X | |
| middleName |X |X | | |
| mobiel |X |X | | |
| msExchTeamMailboxVerlopen |X | | | |
| msExchTeamMailboxEigenaren |X | | | |
| msExchTeamMailboxSharePointLinkedBy |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| objectSID |X | |X |mechanische eigenschap. AD-gebruikers-id die wordt gebruikt om de synchronisatie tussen Azure AD en AD te behouden. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelefoon |X |X | | |
| otherHomePhone |X |X | | |
| otherIpPhone |X |X | | |
| otherMobile |X |X | | |
| otherPager |X |X | | |
| andereTelefoon |X |X | | |
| Pager |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| Postcode |X |X | | |
| postOfficeBox |X |X | |Dit kenmerk wordt momenteel niet verbruikt door SharePoint Online. |
| voorkeurTaal |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mechanische eigenschap. Gebruikt om te weten wanneer al uitgegeven tokens ongeldig te maken. Gebruikt door zowel wachtwoord hash sync, pass-through authenticatie en federatie. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| sn |X |X | | |
| sourceAnchor bronAnker |X |X |X |mechanische eigenschap. Onveranderlijke id om de relatie tussen ADDS en Azure AD te onderhouden. |
| st |X |X | | |
| streetAddress |X |X | | |
| Targetaddress |X |X | | |
| telefoonAssistent |X |X | | |
| telefoonNummer |X |X | | |
| miniatuurfoto |X |X | | |
| titel |X |X | | |
| unauthOrig unauthOrig |X |X |X | |
| url |X |X | | |
| gebruikLocatie |X | | |mechanische eigenschap. Het land/de regio van de gebruiker
. Gebruikt voor licentietoewijzing. |
| userPrincipalName |X | | |UPN is de inlog-id voor de gebruiker. Meestal hetzelfde als [mail] waarde. |
| wWWHomePage |X |X | | |

## <a name="teams-and-skype-for-business-online"></a>Teams en Skype voor Bedrijven Online
| Kenmerknaam | Gebruiker | Contactpersoon | Groep | Opmerking |
| --- |:---:|:---:|:---:| --- |
| accountIngeschakeld |X | | |Hiermee bepaalt u of een account is ingeschakeld. |
| c |X |X | | |
| Cn |X | |X | |
| Co |X |X | | |
| bedrijf |X |X | | |
| department |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| faxnummer |X |X |X | |
| givenName |X |X | | |
| homephone |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| mail |X |X |X | |
| e-mailNickname |X |X |X | |
| beheerdDoor | | |X | |
| manager |X |X | | |
| lid | | |X | |
| mobiel |X |X | | |
| msExchHideFromAddressLists |X |X |X | |
| msRTCSIP-ApplicationOptions |X | | | |
| msRTCSIP-DeploymentLocator |X |X | | |
| msRTCSIP-Line |X |X | | |
| msRTCSIP-OptionFlags |X |X | | |
| msRTCSIP-OwnerUrn |X | | | |
| msRTCSIP-PrimaryUserAddress |X |X | | |
| msRTCSIP-UserEnabled |X |X | | |
| objectSID |X | |X |mechanische eigenschap. AD-gebruikers-id die wordt gebruikt om de synchronisatie tussen Azure AD en AD te behouden. |
| andereTelefoon |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| Postcode |X |X | | |
| voorkeurTaal |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mechanische eigenschap. Gebruikt om te weten wanneer al uitgegeven tokens ongeldig te maken. Gebruikt door zowel wachtwoord hash sync, pass-through authenticatie en federatie. |
| sn |X |X | | |
| sourceAnchor bronAnker |X |X |X |mechanische eigenschap. Onveranderlijke id om de relatie tussen ADDS en Azure AD te onderhouden. |
| st |X |X | | |
| streetAddress |X |X | | |
| telefoonNummer |X |X | | |
| miniatuurfoto |X |X | | |
| titel |X |X | | |
| gebruikLocatie |X | | |mechanische eigenschap. Het land/de regio van de gebruiker. Gebruikt voor licentietoewijzing. |
| userPrincipalName |X | | |UPN is de inlog-id voor de gebruiker. Meestal hetzelfde als [mail] waarde. |
| wWWHomePage |X |X | | |

## <a name="azure-rms"></a>Azure RMS
| Kenmerknaam | Gebruiker | Contactpersoon | Groep | Opmerking |
| --- |:---:|:---:|:---:| --- |
| accountIngeschakeld |X | | |Hiermee bepaalt u of een account is ingeschakeld. |
| Cn |X | |X |Algemene naam of alias. Meestal het voorvoegsel van [e-mail] waarde. |
| displayName |X |X |X |Een tekenreeks die de naam vertegenwoordigt die vaak wordt weergegeven als de vriendelijke naam (achternaam voornaam). |
| mail |X |X |X |volledig e-mailadres. |
| lid | | |X | |
| objectSID |X | |X |mechanische eigenschap. AD-gebruikers-id die wordt gebruikt om de synchronisatie tussen Azure AD en AD te behouden. |
| proxyAddresses |X |X |X |mechanische eigenschap. Wordt gebruikt door Azure AD. Bevat alle secundaire e-mailadressen voor de gebruiker. |
| pwdLastSet |X | | |mechanische eigenschap. Gebruikt om te weten wanneer al uitgegeven tokens ongeldig te maken. |
| sourceAnchor bronAnker |X |X |X |mechanische eigenschap. Onveranderlijke id om de relatie tussen ADDS en Azure AD te onderhouden. |
| gebruikLocatie |X | | |mechanische eigenschap. Het land/de regio van de gebruiker. Gebruikt voor licentietoewijzing. |
| userPrincipalName |X | | |Deze UPN is de inlog-id voor de gebruiker. Meestal hetzelfde als [mail] waarde. |

## <a name="intune"></a>Intune
| Kenmerknaam | Gebruiker | Contactpersoon | Groep | Opmerking |
| --- |:---:|:---:|:---:| --- |
| accountIngeschakeld |X | | |Hiermee bepaalt u of een account is ingeschakeld. |
| c |X |X | | |
| Cn |X | |X | |
| description |X |X |X | |
| displayName |X |X |X | |
| mail |X |X |X | |
| mailnickname |X |X |X | |
| lid | | |X | |
| objectSID |X | |X |mechanische eigenschap. AD-gebruikers-id die wordt gebruikt om de synchronisatie tussen Azure AD en AD te behouden. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mechanische eigenschap. Gebruikt om te weten wanneer al uitgegeven tokens ongeldig te maken. Gebruikt door zowel wachtwoord hash sync, pass-through authenticatie en federatie. |
| sourceAnchor bronAnker |X |X |X |mechanische eigenschap. Onveranderlijke id om de relatie tussen ADDS en Azure AD te onderhouden. |
| gebruikLocatie |X | | |mechanische eigenschap. Het land/de regio van de gebruiker. Gebruikt voor licentietoewijzing. |
| userPrincipalName |X | | |UPN is de inlog-id voor de gebruiker. Meestal hetzelfde als [mail] waarde. |

## <a name="dynamics-crm"></a>Dynamics CRM
| Kenmerknaam | Gebruiker | Contactpersoon | Groep | Opmerking |
| --- |:---:|:---:|:---:| --- |
| accountIngeschakeld |X | | |Hiermee bepaalt u of een account is ingeschakeld. |
| c |X |X | | |
| Cn |X | |X | |
| Co |X |X | | |
| bedrijf |X |X | | |
| landCode |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| faxnummer |X |X | | |
| givenName |X |X | | |
| l |X |X | | |
| beheerdDoor | | |X | |
| manager |X |X | | |
| lid | | |X | |
| mobiel |X |X | | |
| objectSID |X | |X |mechanische eigenschap. AD-gebruikers-id die wordt gebruikt om de synchronisatie tussen Azure AD en AD te behouden. |
| physicalDeliveryOfficeName |X |X | | |
| Postcode |X |X | | |
| voorkeurTaal |X | | | |
| pwdLastSet |X | | |mechanische eigenschap. Gebruikt om te weten wanneer al uitgegeven tokens ongeldig te maken. Gebruikt door zowel wachtwoord hash sync, pass-through authenticatie en federatie. |
| sn |X |X | | |
| sourceAnchor bronAnker |X |X |X |mechanische eigenschap. Onveranderlijke id om de relatie tussen ADDS en Azure AD te onderhouden. |
| st |X |X | | |
| streetAddress |X |X | | |
| telefoonNummer |X |X | | |
| titel |X |X | | |
| gebruikLocatie |X | | |mechanische eigenschap. Het land/de regio van de gebruiker. Gebruikt voor licentietoewijzing. |
| userPrincipalName |X | | |UPN is de inlog-id voor de gebruiker. Meestal hetzelfde als [mail] waarde. |

## <a name="3rd-party-applications"></a>Toepassingen van derden
Deze groep is een set kenmerken die worden gebruikt als de minimale kenmerken die nodig zijn voor een algemene werkbelasting of toepassing. Het kan worden gebruikt voor een werkbelasting die niet in een andere sectie of voor een niet-Microsoft-app wordt vermeld. Het wordt expliciet gebruikt voor het volgende:

* Yammer (alleen gebruiker wordt verbruikt)
* [Hybrid Business-to-Business (B2B) cross-org samenwerkingsscenario's aangeboden door bronnen zoals SharePoint](https://go.microsoft.com/fwlink/?LinkId=747036)

Deze groep is een set kenmerken die kunnen worden gebruikt als de Azure AD-map niet wordt gebruikt om Office 365, Dynamics of Intune te ondersteunen. Het heeft een kleine set kernkenmerken.

| Kenmerknaam | Gebruiker | Contactpersoon | Groep | Opmerking |
| --- |:---:|:---:|:---:| --- |
| accountIngeschakeld |X | | |Hiermee bepaalt u of een account is ingeschakeld. |
| Cn |X | |X | |
| displayName |X |X |X | |
| employeeID |X |  |  | |
| givenName |X |X | | |
| mail |X | |X | |
| beheerdDoor | | |X | |
| mailNickName |X |X |X | |
| lid | | |X | |
| objectSID |X | | |mechanische eigenschap. AD-gebruikers-id die wordt gebruikt om de synchronisatie tussen Azure AD en AD te behouden. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mechanische eigenschap. Gebruikt om te weten wanneer al uitgegeven tokens ongeldig te maken. Gebruikt door zowel wachtwoord hash sync, pass-through authenticatie en federatie. |
| sn |X |X | | |
| sourceAnchor bronAnker |X |X |X |mechanische eigenschap. Onveranderlijke id om de relatie tussen ADDS en Azure AD te onderhouden. |
| gebruikLocatie |X | | |mechanische eigenschap. Het land/de regio van de gebruiker. Gebruikt voor licentietoewijzing. |
| userPrincipalName |X | | |UPN is de inlog-id voor de gebruiker. Meestal hetzelfde als [mail] waarde. |

## <a name="windows-10"></a>Windows 10
Een met Windows 10-domein verbonden computer(apparaat) synchroniseert sommige kenmerken met Azure AD. Zie Apparaten verbinden met [domein verbinden met Azure AD voor Windows 10-ervaringen voor](../active-directory-azureadjoin-devices-group-policy.md)meer informatie over de scenario's. Deze kenmerken worden altijd gesynchroniseerd en Windows 10 wordt niet weergegeven als een app die u de selecteren. Een windows 10-domeinaangesloten computer wordt geïdentificeerd door het kenmerk userCertificate te laten invullen.

| Kenmerknaam | Apparaat | Opmerking |
| --- |:---:| --- |
| accountIngeschakeld |X | |
| deviceTrustType |X |Hardcoded-waarde voor computers die zijn verbonden met een domein. |
| displayName |X | |
| ms-DS-CreatorSID |X |Ook wel registeredOwnerReference genoemd. |
| objectGUID |X |Ook wel deviceID genoemd. |
| objectSID |X |Ook wel onPremisesSecurityIdentifier genoemd. |
| operatingSystem |X |Ook wel deviceOSType genoemd. |
| operatingSystemVersion |X |Ook wel deviceOSVersion genoemd. |
| userCertificate |X | |

Deze kenmerken voor **de gebruiker** komen bovenop de andere apps die u hebt geselecteerd.  

| Kenmerknaam | Gebruiker | Opmerking |
| --- |:---:| --- |
| domeinFQDN |X |Ook wel dnsDomainName genoemd. Bijvoorbeeld contoso.com. |
| domainNetBios domainNetBios |X |Ook wel netBiosName genoemd. Bijvoorbeeld CONTOSO. |
| msDS-KeyCredentialLink |X |Zodra de gebruiker is ingeschreven in Windows Hello voor Bedrijven. | 

## <a name="exchange-hybrid-writeback"></a>Hybride terugschrijftekst van Exchange
Deze kenmerken worden teruggeschreven van Azure AD naar on-premises Active Directory wanneer u **exchange-hybride**selecteert. Afhankelijk van uw Exchange-versie kunnen minder kenmerken worden gesynchroniseerd.

| Kenmerknaam (on-premises AD) | Kenmerknaam (Gebruikersinterface verbinden) | Gebruiker | Contactpersoon | Groep | Opmerking |
| --- |:---:|:---:|:---:| --- |---|
| msDS-ExternalDirectoryObjectID| ms-DS-External-Directory-Object-Id |X | | |Afgeleid van cloudAnchor in Azure AD. Dit kenmerk is nieuw in Exchange 2016 en Windows Server 2016 AD. |
| msExchArchiveStatus| ms-Exch-ArchiefStatus |X | | |Online archief: Stelt klanten in staat om e-mail te archiveren. |
| msExchBlockedSendersHash| ms-Exch-BlockedSendersHash |X | | |Filteren: schrijft on-premises filtering en online veilige en geblokkeerde afzendergegevens van clients terug. |
| msExchSafeRecipientsHash| ms-Exch-SafeRecipientsHash  |X | | |Filteren: schrijft on-premises filtering en online veilige en geblokkeerde afzendergegevens van clients terug. |
| msExchSafeSendersHash| ms-Exch-SafeSendersHash  |X | | |Filteren: schrijft on-premises filtering en online veilige en geblokkeerde afzendergegevens van clients terug. |
| msExchUCVoiceMailInstellingen| ms-Exch-UCVoiceMailInstellingen |X | | |Unified Messaging (UM) inschakelen - Online voicemail: wordt gebruikt door de integratie van Microsoft Lync Server om aan Lync Server on-premises aan te geven dat de gebruiker voicemail in onlineservices heeft. |
| msExchUserHoldPolicies| ms-Exch-UserHoldPolicies |X | | |Litigation Hold: stelt cloudservices in staat om te bepalen welke gebruikers onder Litigation Hold staan. |
| proxyAddresses| proxyAddresses |X |X |X |Alleen het x500-adres van Exchange Online wordt ingevoegd. |
| openbaarAfgevaardigden| ms-Exch-Public-Delegates  |X | | |Hiermee kan een Exchange Online-postvak worden verleend SendOnBehalfTo-rechten aan gebruikers met on-premises Exchange-postvak. Vereist Azure AD Connect build 1.1.552.0 of erna. |

## <a name="exchange-mail-public-folder"></a>Openbare map van Exchange Mail
Deze kenmerken worden gesynchroniseerd van on-premises Active Directory naar Azure AD wanneer u selecteert om **de openbare map van Exchange Mail**in te schakelen.

| Kenmerknaam | Openbare map | Opmerking |
| --- | :---:| --- |
| displayName | X |  |
| mail | X |  |
| msExchRecipientTypeDetails | X |  |
| objectGUID | X |  |
| proxyAddresses | X |  |
| Targetaddress | X |  |

## <a name="device-writeback"></a>Apparaat terugschrijven
Apparaatobjecten worden gemaakt in Active Directory. Deze objecten kunnen apparaten zijn die zijn aangesloten bij Azure AD of windows 10-computers met domeinverbonden.

| Kenmerknaam | Apparaat | Opmerking |
| --- |:---:| --- |
| altSecurityIdentities |X | |
| displayName |X | |
| dn |X | |
| msDS-CloudAnchor |X | |
| msDS-DeviceID |X | |
| MSDS-DeviceObjectVersion |X | |
| msDS-DeviceOSType |X | |
| msDS-DeviceOSVersie |X | |
| msDS-DevicePhysicalIDs |X | |
| msDS-KeyCredentialLink |X |Alleen met het AD-schema van Windows Server 2016 |
| msDS-IsCompliant |X | |
| msDS-IsEnabled |X | |
| msDS-IsManaged |X | |
| msDS-geregistreerde eigenaar |X | |

## <a name="notes"></a>Opmerkingen
* Wanneer u een alternatieve id gebruikt, wordt de on-premises kenmerkuserPrincipalName gesynchroniseerd met het Azure AD-kenmerk onPremisesUserPrincipalName. Het kenmerk Alternatieve id, bijvoorbeeld e-mail, wordt gesynchroniseerd met de Azure AD-kenmerk userPrincipalName.
* In de bovenstaande lijsten is het objecttype **Gebruiker** ook van toepassing op het objecttype **iNetOrgPerson**.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over de [synchronisatieconfiguratie van Azure AD Connect.](how-to-connect-sync-whatis.md)

Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
