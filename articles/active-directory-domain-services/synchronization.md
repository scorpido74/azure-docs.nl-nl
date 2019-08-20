---
title: 'Azure Active Directory Domain Services: Synchronisatie in beheerde domeinen | Microsoft Docs'
description: Synchronisatie begrijpen in een Azure Active Directory Domain Services beheerd domein
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 1c52ac967d241f31d96988fa5ead8b4e049f6f4c
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617102"
---
# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Synchronisatie in een door Azure AD Domain Services beheerd domein
In het volgende diagram ziet u hoe synchronisatie werkt in Azure AD Domain Services beheerde domeinen.

![Synchronisatie in Azure AD Domain Services](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>Synchronisatie van uw on-premises Directory naar uw Azure AD-Tenant
Azure AD Connect Sync wordt gebruikt voor het synchroniseren van gebruikers accounts, groepslid maatschappen en referentie-hashes voor uw Azure AD-Tenant. Kenmerken van gebruikers accounts, zoals de UPN-en on-premises SID (beveiligings-id), worden gesynchroniseerd. Als u Azure AD Domain Services gebruikt, worden verouderde referentie-hashes die zijn vereist voor NTLM-en Kerberos-verificatie ook gesynchroniseerd met uw Azure AD-Tenant.

Als u write-back configureert, worden wijzigingen in uw Azure AD-adres lijst weer gesynchroniseerd met uw on-premises Active Directory. Als u uw wacht woord bijvoorbeeld wijzigt met Azure AD selfservice voor wachtwoord beheer, wordt het gewijzigde wacht woord bijgewerkt in uw on-premises AD-domein.

> [!NOTE]
> Gebruik altijd de nieuwste versie van Azure AD Connect om ervoor te zorgen dat u oplossingen hebt voor alle bekende bugs.
>
>

## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>Synchronisatie van uw Azure AD-Tenant naar uw beheerde domein
Gebruikers accounts, groepslid maatschappen en referentie-hashes worden gesynchroniseerd vanuit uw Azure AD-Tenant naar uw Azure AD Domain Services beheerde domein. Dit synchronisatie proces wordt automatisch uitgevoerd. U hoeft dit synchronisatie proces niet te configureren, te controleren of te beheren. Het kan een paar uur duren voordat de initiële synchronisatie is uitgevoerd, afhankelijk van het aantal objecten in uw Azure AD-adres lijst. Nadat de eerste synchronisatie is voltooid, duurt het ongeveer 20-30 minuten voordat wijzigingen die zijn aangebracht in azure AD, worden bijgewerkt in uw beheerde domein. Dit synchronisatie-interval is van toepassing op wijzigingen in het wacht woord of wijzigingen in kenmerken die zijn gemaakt in azure AD.

Het synchronisatie proces is ook eenrichtings/onwaar. Uw beheerde domein is grotendeels alleen-lezen, behalve voor aangepaste organisatie-eenheden die u maakt. Daarom kunt u geen wijzigingen aanbrengen in gebruikers kenmerken, gebruikers wachtwoorden of groepslid maatschappen binnen het beheerde domein. Als gevolg hiervan is er geen omgekeerde synchronisatie van wijzigingen van uw beheerde domein naar uw Azure AD-Tenant.

## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Synchronisatie vanuit een on-premises omgeving met meerdere forests
Veel organisaties hebben een redelijk complexe on-premises identiteits infrastructuur die bestaat uit meerdere account forests. Azure AD Connect ondersteunt het synchroniseren van gebruikers, groepen en referentie-hashes van omgevingen met meerdere forests naar uw Azure AD-Tenant.

Uw Azure AD-Tenant daarentegen is een veel eenvoudiger en platte naam ruimte. Om gebruikers in staat te stellen om op betrouw bare wijze toegang te krijgen tot toepassingen die door Azure AD worden beveiligd, lost u UPN-conflicten op tussen gebruikers accounts in Uw door Azure AD Domain Services beheerde domein draagt dicht lijkt op de Azure AD-Tenant. U ziet een platte OE-structuur in uw beheerde domein. Alle gebruikers accounts en-groepen worden opgeslagen in de container AADDC gebruikers, ondanks dat ze worden gesynchroniseerd vanuit verschillende on-premises domeinen of forests. Mogelijk hebt u on-premises een hiërarchische OE-structuur geconfigureerd. Uw beheerde domein heeft nog steeds een eenvoudige platte OE-structuur.

## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>Uitsluitingen-wat niet is gesynchroniseerd met uw beheerde domein
De volgende objecten of kenmerken zijn niet gesynchroniseerd met uw Azure AD-Tenant of uw beheerde domein:

* **Uitgesloten kenmerken:** U kunt ervoor kiezen om bepaalde kenmerken uit te sluiten van synchronisatie met uw Azure AD-Tenant vanuit uw on-premises domein met Azure AD Connect. Deze uitgesloten kenmerken zijn niet beschikbaar in uw beheerde domein.
* **Groeps beleid:** Groeps beleid dat in uw on-premises domein is geconfigureerd, is niet gesynchroniseerd met uw beheerde domein.
* **SYSVOL-share:** Op dezelfde manier wordt de inhoud van de SYSVOL-share op uw on-premises domein niet gesynchroniseerd met uw beheerde domein.
* **Computer objecten:** Computer objecten voor computers die zijn gekoppeld aan uw on-premises domein, worden niet gesynchroniseerd met uw beheerde domein. Deze computers hebben geen vertrouwens relatie met uw beheerde domein en behoren alleen tot uw on-premises domein. In uw beheerde domein vindt u alleen computer objecten voor computers die u expliciet aan het beheerde domein hebt toegevoegd.
* **SidHistory-kenmerken voor gebruikers en groepen:** De primaire gebruikers-en primaire groeps-Sid's van uw on-premises domein worden gesynchroniseerd met uw beheerde domein. Bestaande SidHistory-kenmerken voor gebruikers en groepen worden echter niet gesynchroniseerd van uw on-premises domein naar uw beheerde domein.
* **Structuren van organisatie-eenheden (OE):** Organisatie-eenheden gedefinieerd in uw on-premises domein worden niet gesynchroniseerd met uw beheerde domein. Uw beheerde domein bevat twee ingebouwde organisatie-eenheden. Uw beheerde domein heeft standaard een platte OE-structuur. U kunt er echter voor kiezen om [een aangepaste OE in uw beheerde domein te maken](create-ou.md).

## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>Hoe specifieke kenmerken worden gesynchroniseerd met uw beheerde domein
De volgende tabel bevat enkele algemene kenmerken en beschrijft hoe ze worden gesynchroniseerd met uw beheerde domein.

| Kenmerk in uw beheerde domein | Source | Opmerkingen |
|:--- |:--- |:--- |
| UPN |Het UPN-kenmerk van de gebruiker in uw Azure AD-Tenant |Het UPN-kenmerk van uw Azure AD-Tenant wordt gesynchroniseerd naar uw beheerde domein. De meest betrouw bare manier om u aan te melden bij uw beheerde domein is met behulp van uw UPN. |
| SAMAccountName |Het mailnickname-kenmerk van de gebruiker in uw Azure AD-Tenant of automatisch gegenereerd |Het kenmerk SAMAccountName wordt afgeleid van het kenmerk mailnickname in uw Azure AD-Tenant. Als meerdere gebruikers accounts hetzelfde mailnickname-kenmerk hebben, wordt de SAMAccountName automatisch gegenereerd. Als de mailnickname of het UPN-voor voegsel van de gebruiker langer is dan 20 tekens, wordt de SAMAccountName automatisch gegenereerd om te voldoen aan de limiet van 20 tekens voor SAMAccountName-kenmerken. |
| Wachtwoorden |Het wacht woord van de gebruiker van uw Azure AD-Tenant |Referentie-hashes die zijn vereist voor NTLM-of Kerberos-verificatie (ook wel aanvullende referenties genoemd), worden gesynchroniseerd vanuit uw Azure AD-Tenant. Als uw Azure AD-Tenant een gesynchroniseerde Tenant is, worden deze referenties vanaf uw on-premises domein gebrond. |
| Primaire gebruikers-en groeps-SID |Automatisch gegenereerd |De primaire SID voor gebruikers-en groeps accounts wordt automatisch gegenereerd in uw beheerde domein. Dit kenmerk komt niet overeen met de primaire gebruiker/groeps-SID van het object in uw on-premises AD-domein. Dit komt niet overeen omdat het beheerde domein een andere SID-naam ruimte heeft dan uw on-premises domein. |
| SID-geschiedenis voor gebruikers en groepen |On-premises primaire gebruikers-en groeps-SID |Het kenmerk SidHistory voor gebruikers en groepen in uw beheerde domein is ingesteld op overeenkomen met de overeenkomstige primaire gebruiker of groeps-SID in uw on-premises domein. Deze functie helpt bij het gemakkelijker maken van til en Shift van on-premises toepassingen naar het beheerde domein, omdat u geen ACL-bronnen hoeft te hergebruiken. |

> [!NOTE]
> **Meld u aan bij het beheerde domein met de UPN-indeling:** Het kenmerk SAMAccountName kan automatisch worden gegenereerd voor sommige gebruikers accounts in uw beheerde domein. Als meerdere gebruikers hetzelfde mailnickname-kenmerk hebben of gebruikers over lange UPN-voor voegsels beschikken, kunnen de SAMAccountName voor deze gebruikers automatisch worden gegenereerd. Daarom is de SAMAccountName-indeling (bijvoorbeeld ' CONTOSO\dee ') niet altijd een betrouw bare manier om u aan te melden bij het domein. Automatisch gegenereerde SAMAccountName van gebruikers kan verschillen van het UPN-voor voegsel. Gebruik de UPN-indeling (bijvoorbeelddee@contoso.com) om u op betrouw bare wijze aan te melden bij het beheerde domein.

### <a name="attribute-mapping-for-user-accounts"></a>Kenmerk toewijzing voor gebruikers accounts
In de volgende tabel ziet u hoe specifieke kenmerken voor gebruikers objecten in uw Azure AD-Tenant worden gesynchroniseerd met de bijbehorende kenmerken in uw beheerde domein.

| Gebruikers kenmerk in uw Azure AD-Tenant | Gebruikers kenmerk in uw beheerde domein |
|:--- |:--- |
| accountEnabled |userAccountControl (Hiermee wordt de ACCOUNT_DISABLED-bit ingesteld of gewist) |
| city |l |
| regio |CO |
| Afdeling |Afdeling |
| displayName |displayName |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| Functie |titel |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName (kan soms automatisch worden gegenereerd) |
| mobiele |mobiele |
| id |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |Sid |
| passwordPolicies |userAccountControl (Hiermee wordt de DONT_EXPIRE_PASSWORD-bit ingesteld of gewist) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| Postcode |Postcode |
| preferredLanguage |preferredLanguage |
| toestand |St |
| streetAddress |streetAddress |
| Achternaam |SN |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>Kenmerk toewijzing voor groepen
In de volgende tabel ziet u hoe specifieke kenmerken voor groeps objecten in uw Azure AD-Tenant worden gesynchroniseerd met de bijbehorende kenmerken in uw beheerde domein.

| Groeps kenmerk in uw Azure AD-Tenant | Groeps kenmerk in uw beheerde domein |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (kan soms automatisch worden gegenereerd) |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| id |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |Sid |
| securityEnabled |groupType |

## <a name="password-hash-synchronization-and-security-considerations"></a>Synchronisatie van wacht woord-hash en beveiligings overwegingen
Als u Azure AD Domain Services inschakelt, worden in uw Azure AD-adres lijst hashes voor NTLM & die compatibel zijn met Kerberos, gegenereerd en opgeslagen. 

Voor bestaande Cloud gebruikers accounts, omdat Azure AD nooit wacht woorden met lees bare tekst opslaat, kunnen deze hashes niet automatisch worden gegenereerd. Daarom vereist micro soft [Cloud-gebruikers om hun wacht woorden opnieuw in te stellen of te wijzigen](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) zodat hun wacht woord-hashes kunnen worden gegenereerd en opgeslagen in azure AD. Voor alle Cloud gebruikers accounts die zijn gemaakt in azure AD na het inschakelen van Azure AD Domain Services, worden de wacht woord-hashes gegenereerd en opgeslagen in de NTLM-en Kerberos-compatibele indelingen. 

Voor gebruikers accounts die zijn gesynchroniseerd vanuit on-premises AD met Azure AD Connect-synchronisatie, moet u [Azure AD Connect configureren voor het synchroniseren van wacht woord-hashes in de indelingen NTLM en Kerberos-compatibel](active-directory-ds-getting-started-password-sync-synced-tenant.md).

De NTLM-en Kerberos-compatibele wachtwoord hashes worden altijd op een versleutelde manier opgeslagen in azure AD. Deze hashes worden zodanig versleuteld dat alleen Azure AD Domain Services toegang heeft tot de ontsleutelings sleutels. Geen enkele andere service of een ander onderdeel in azure AD heeft toegang tot de versleutelings sleutels. De versleutelings sleutels zijn uniek per Azure AD-Tenant. Azure AD Domain Services synchroniseert de wacht woord-hashes in de domein controllers voor uw beheerde domein. Deze wacht woord-hashes worden opgeslagen en beveiligd op deze domein controllers, vergelijkbaar met de manier waarop wacht woorden worden opgeslagen en beveiligd op Windows Server AD-domein controllers. De schijven voor deze beheerde domein controllers worden op rest versleuteld.

## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>Objecten die niet zijn gesynchroniseerd met uw Azure AD-Tenant vanuit uw beheerde domein
Zoals beschreven in een voor gaande sectie van dit artikel, is er geen synchronisatie van uw beheerde domein terug naar uw Azure AD-Tenant. U kunt ervoor kiezen om [een aangepaste organisatie-eenheid (OE)](create-ou.md) in uw beheerde domein te maken. Verder kunt u andere organisatie-eenheden, gebruikers, groepen of service accounts maken binnen deze aangepaste organisatie-eenheden. Geen van de objecten die zijn gemaakt in aangepaste organisatie-eenheden, worden teruggesynchroniseerd naar uw Azure AD-Tenant. Deze objecten zijn alleen beschikbaar voor gebruik binnen uw beheerde domein. Deze objecten zijn daarom niet zichtbaar met Azure AD Power shell-cmdlets, Azure AD Graph API of met de Azure AD-beheer interface.

## <a name="related-content"></a>Gerelateerde inhoud
* [Implementatie scenario's-Azure AD Domain Services](scenarios.md)
* [Netwerk overwegingen voor Azure AD Domain Services](network-considerations.md)
* [Aan de slag met Azure AD Domain Services](tutorial-create-instance.md)
