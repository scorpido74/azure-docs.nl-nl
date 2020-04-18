---
title: Hoe synchronisatie werkt in Azure AD Domain Services | Microsoft Documenten
description: Lees hoe het synchronisatieproces werkt voor objecten en referenties van een Azure AD-tenant of on-premises Active Directory Domain Services-omgeving tot een beheerd Azure Active Directory Domain Services-domein.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 38ed48df4d681543cc30daccf46b98635d973b89
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639911"
---
# <a name="how-objects-and-credentials-are-synchronized-in-an-azure-ad-domain-services-managed-domain"></a>Hoe objecten en referenties worden gesynchroniseerd in een beheerd Azure AD Domain Services-domein

Objecten en referenties in een ad DS-beheerd domein (Azure Active Directory Domain Services) kunnen lokaal binnen het domein worden gemaakt of worden gesynchroniseerd vanuit een Azure Active Directory-tenant (Azure AD). Wanneer u Azure AD DS voor het eerst implementeert, wordt een automatische eenrichtingssynchronisatie geconfigureerd en begonnen met het repliceren van de objecten uit Azure AD. Deze synchronisatie in één richting blijft op de achtergrond worden uitgevoerd om het door Azure AD DS beheerde domein up-to-date te houden met eventuele wijzigingen van Azure AD. Er vindt geen synchronisatie plaats van Azure AD DS terug naar Azure AD.

In een hybride omgeving kunnen objecten en referenties uit een on-premises AD DS-domein worden gesynchroniseerd met Azure AD met Azure AD met Azure AD Connect. Zodra deze objecten zijn gesynchroniseerd met Azure AD, worden deze objecten en referenties met de automatische achtergrondsynchronisatie beschikbaar gemaakt voor toepassingen met behulp van het beheerde Azure AD DS-domein.

In het volgende diagram ziet u hoe synchronisatie werkt tussen Azure AD DS, Azure AD en een optionele on-premises AD DS-omgeving:

![Synchronisatieoverzicht voor een beheerd Azure AD Domain Services-domein](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-azure-ad-to-azure-ad-ds"></a>Synchronisatie van Azure AD naar Azure AD DS

Gebruikersaccounts, groepslidmaatschappen en referentieshebben worden op één manier gesynchroniseerd van Azure AD naar Azure AD DS. Dit synchronisatieproces is automatisch. U hoeft dit synchronisatieproces niet te configureren, te controleren of te beheren. De eerste synchronisatie kan enkele uren tot een paar dagen duren, afhankelijk van het aantal objecten in de Azure AD-map. Nadat de eerste synchronisatie is voltooid, worden wijzigingen die zijn aangebracht in Azure AD, zoals wachtwoord- of kenmerkwijzigingen, vervolgens automatisch gesynchroniseerd met Azure AD DS.

Wanneer een gebruiker is gemaakt in Azure AD, worden deze niet gesynchroniseerd met Azure AD DS totdat deze zijn wachtwoord in Azure AD heeft gewijzigd. Dit wachtwoordwijzigingsproces zorgt ervoor dat de wachtwoordhashes voor Kerberos- en NTLM-verificatie worden gegenereerd en opgeslagen in Azure AD. De wachtwoordhashes zijn nodig om een gebruiker in Azure AD DS te verifiëren.

Het synchronisatieproces is een manier / unidirectionele door het ontwerp. Er is geen omgekeerde synchronisatie van wijzigingen van Azure AD DS terug naar Azure AD. Een door Azure AD DS beheerd domein is grotendeels alleen-lezen, behalve voor aangepaste ok's die u maken. U geen wijzigingen aanbrengen in gebruikerskenmerken, gebruikerswachtwoorden of groepslidmaatschappen binnen een door Azure AD DS beheerd domein.

## <a name="attribute-synchronization-and-mapping-to-azure-ad-ds"></a>Synchronisatie en toewijzing van kenmerk aan Azure AD DS

In de volgende tabel worden enkele algemene kenmerken weergegeven en hoe deze zijn gesynchroniseerd met Azure AD DS.

| Kenmerk in Azure AD DS | Bron | Opmerkingen |
|:--- |:--- |:--- |
| UPN | *UpN-kenmerk* van gebruiker in Azure AD-tenant | Het kenmerk UPN van de Azure AD-tenant wordt gesynchroniseerd met Azure AD DS. De meest betrouwbare manier om u aan te melden bij een door Azure AD DS beheerd domein is het UPN. |
| Samaccountname | Kenmerk *e-mailNickname* van gebruiker in Azure AD-tenant of automatisch gegenereerde | Het kenmerk *SAMAccountName* is afkomstig van het kenmerk *mailNickname* in de Azure AD-tenant. Als meerdere gebruikersaccounts hetzelfde *kenmerk van e-mailNickname* hebben, wordt de *SAMAccountName* automatisch gegenereerd. Als het *e-mailNickname-* of *UPN-voorvoegsel* van de gebruiker langer is dan 20 tekens, wordt de *SAMAccountName* automatisch gegenereerd om te voldoen aan de limiet van 20 tekens voor *SAMAccountName-kenmerken.* |
| Wachtwoorden | Het wachtwoord van de gebruiker van de Azure AD-tenant | Verouderde wachtwoordhashes die nodig zijn voor NTLM- of Kerberos-verificatie worden gesynchroniseerd vanuit de Azure AD-tenant. Als de Azure AD-tenant is geconfigureerd voor hybride synchronisatie met Azure AD Connect, zijn deze wachtwoordhashes afkomstig uit de on-premises AD DS-omgeving. |
| Primaire gebruiker/groep SID | Automatisch gegenereerd | De primaire SID voor gebruikers-/groepsaccounts wordt automatisch gegenereerd in Azure AD DS. Dit kenmerk komt niet overeen met de primaire sid van het object voor de primaire gebruiker/groep in een on-premises AD DS-omgeving. Deze mismatch is omdat het door Azure AD DS beheerde domein een andere SID-naamruimte heeft dan het on-premises AD DS-domein. |
| SID-geschiedenis voor gebruikers en groepen | On-premises primaire gebruiker en groep SID | Het kenmerk *SidHistory* voor gebruikers en groepen in Azure AD DS is ingesteld op de overeenkomen met de bijbehorende primaire gebruiker of groep SID in een on-premises AD DS-omgeving. Deze functie helpt het verhogen en verplaatsen van on-premises toepassingen naar Azure AD DS eenvoudiger te maken omdat u acl-resources niet opnieuw hoeft te gebruiken. |

> [!TIP]
> **Aanmelden bij het beheerde domein met de UPN-indeling** Het kenmerk *SAMAccountName,* zoals `AADDSCONTOSO\driley`, kan automatisch worden gegenereerd voor sommige gebruikersaccounts in een door Azure AD DS beheerd domein. De automatisch gegenereerde *SAMAccountName* van gebruikers kan afwijken van hun UPN-voorvoegsel, dus het is niet altijd een betrouwbare manier om in te loggen.
>
> Als meerdere gebruikers bijvoorbeeld hetzelfde *kenmerk mailNickname* hebben of als gebruikers te lange UPN-voorvoegsels hebben, kan de *SAMAccountName* voor deze gebruikers automatisch worden gegenereerd. Gebruik de UPN-indeling, zoals `driley@aaddscontoso.com`, om u op betrouwbare wijze aan te melden bij een door Azure AD DS beheerd domein.

### <a name="attribute-mapping-for-user-accounts"></a>Toewijzing van kenmerken voor gebruikersaccounts

In de volgende tabel ziet u hoe specifieke kenmerken voor gebruikersobjecten in Azure AD worden gesynchroniseerd met bijbehorende kenmerken in Azure AD DS.

| Gebruikerskenmerk in Azure AD | Gebruikerskenmerk in Azure AD DS |
|:--- |:--- |
| accountIngeschakeld |userAccountControl (stelt of wist de ACCOUNT_DISABLED bit) |
| city |l |
| land |Co |
| department |department |
| displayName |displayName |
| facsimileTelefoonnummer |facsimileTelefoonnummer |
| givenName |givenName |
| jobTitel |titel |
| mail |mail |
| e-mailNickname |msDS-AzureADMailNickname |
| e-mailNickname |SAMAccountName (kan soms automatisch worden gegenereerd) |
| mobiel |mobiel |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |Sidhistory |
| wachtwoordBeleid |userAccountControl (stelt of wist de DONT_EXPIRE_PASSWORD bit) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| Postcode |Postcode |
| voorkeurTaal |voorkeurTaal |
| state |st |
| streetAddress |streetAddress |
| surname |sn |
| telefoonNummer |telefoonNummer |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>Toewijzing van kenmerken voor groepen

In de volgende tabel ziet u hoe specifieke kenmerken voor groepsobjecten in Azure AD worden gesynchroniseerd met bijbehorende kenmerken in Azure AD DS.

| Groepskenmerk in Azure AD | Groepskenmerk in Azure AD DS |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (kan soms automatisch worden gegenereerd) |
| mail |mail |
| e-mailNickname |msDS-AzureADMailNickname |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |Sidhistory |
| beveiligingIngeschakeld |groupType |

## <a name="synchronization-from-on-premises-ad-ds-to-azure-ad-and-azure-ad-ds"></a>Synchronisatie van on-premises AD DS naar Azure AD en Azure AD DS

Azure AD Connect wordt gebruikt om gebruikersaccounts, groepslidmaatschappen en referenties te synchroniseren van een on-premises AD DS-omgeving naar Azure AD. Kenmerken van gebruikersaccounts zoals de UPN en on-premises beveiligingsid (SID) worden gesynchroniseerd. Als u zich wilt aanmelden met Azure AD DS, worden verouderde wachtwoordhashes die nodig zijn voor NTLM- en Kerberos-verificatie ook gesynchroniseerd met Azure AD.

> [!IMPORTANT]
> Azure AD Connect mag alleen worden geïnstalleerd en geconfigureerd voor synchronisatie met on-premises AD DS-omgevingen. Azure AD Connect wordt niet geïnstalleerd in een door Azure AD DS beheerd domein om objecten te synchroniseren met Azure AD.

Als u terugschrijftekst configureert, worden wijzigingen van Azure AD gesynchroniseerd met de on-premises AD DS-omgeving. Als een gebruiker bijvoorbeeld zijn wachtwoord wijzigt met Azure AD selfservicewachtwoordbeheer, wordt het wachtwoord opnieuw bijgewerkt in de on-premises AD DS-omgeving.

> [!NOTE]
> Gebruik altijd de nieuwste versie van Azure AD Connect om ervoor te zorgen dat u oplossingen hebt voor alle bekende bugs.

### <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Synchronisatie vanuit een on-premises omgeving met meerdere bossen

Veel organisaties hebben een vrij complexe on-premises AD DS-omgeving die meerdere bossen omvat. Azure AD Connect ondersteunt het synchroniseren van gebruikers, groepen en referenties van multiforestomgevingen tot Azure AD.

Azure AD heeft een veel eenvoudigere en platte naamruimte. Als u gebruikers in staat wilt stellen op betrouwbare wijze toegang te krijgen tot toepassingen die zijn beveiligd door Azure AD, u UPN-conflicten tussen gebruikersaccounts in verschillende forests oplossen. Azure AD DS-beheerde domeinen gebruiken een platte ou-structuur, vergelijkbaar met Azure AD. Alle gebruikersaccounts en -groepen worden opgeslagen in de container *AADDC-gebruikers,* ondanks dat ze zijn gesynchroniseerd vanuit verschillende on-premises domeinen of forests, zelfs als u on-premises een hiërarchische organisatie-eenheid hebt geconfigureerd. Het door Azure AD DS beheerde domein vlakt alle hiërarchische OU-structuren af.

Zoals eerder beschreven, is er geen synchronisatie van Azure AD DS terug naar Azure AD. U [een aangepaste organisatie-eenheid (OU) maken](create-ou.md) in Azure AD DS en vervolgens gebruikers, groepen of serviceaccounts binnen die aangepaste gegevens. Geen van de objecten die zijn gemaakt in aangepaste O's worden gesynchroniseerd met Azure AD. Deze objecten zijn alleen beschikbaar binnen het beheerde Azure AD DS-domein en zijn niet zichtbaar met Azure AD PowerShell-cmdlets, Microsoft Graph API of met de Azure AD-beheergebruikersinterface.

## <a name="what-isnt-synchronized-to-azure-ad-ds"></a>Wat is niet gesynchroniseerd met Azure AD DS

De volgende objecten of kenmerken worden niet gesynchroniseerd van een on-premises AD DS-omgeving naar Azure AD of Azure AD DS:

* **Uitgesloten kenmerken:** U ervoor kiezen bepaalde kenmerken uit te sluiten van synchronisatie naar Azure AD van een on-premises AD DS-omgeving met Azure AD Connect. Deze uitgesloten kenmerken zijn dan niet beschikbaar in Azure AD DS.
* **Groepsbeleid:** Groepsbeleid dat is geconfigureerd in een on-premises AD DS-omgeving, wordt niet gesynchroniseerd met Azure AD DS.
* **Sysvol-map:** De inhoud van de *map Sysvol* in een on-premises AD DS-omgeving is niet gesynchroniseerd met Azure AD DS.
* **Computerobjecten:** Computerobjecten voor computers die zijn gekoppeld aan een on-premises AD DS-omgeving, worden niet gesynchroniseerd met Azure AD DS. Deze computers hebben geen vertrouwensrelatie met het beheerde Azure AD DS-domein en behoren alleen tot de on-premises AD DS-omgeving. In Azure AD DS worden alleen computerobjecten weergegeven voor computers die expliciet zijn verbonden met het beheerde domein.
* **SidHistory-kenmerken voor gebruikers en groepen:** De primaire gebruikers- en primaire groep-ID's uit een on-premises AD DS-omgeving worden gesynchroniseerd met Azure AD DS. Bestaande *SidHistory-kenmerken* voor gebruikers en groepen worden echter niet gesynchroniseerd van de on-premises AD DS-omgeving naar Azure AD DS.
* **Organisatie-eenheden (OU)-structuren:** Organisatie-eenheden die zijn gedefinieerd in een on-premises AD DS-omgeving, worden niet gesynchroniseerd met Azure AD DS. Er zijn twee ingebouwde O's in Azure AD DS- een voor gebruikers en een voor computers. Het door Azure AD DS beheerde domein heeft een platte OU-structuur. U ervoor kiezen om [een aangepaste organisatie-eenheid in uw beheerde domein](create-ou.md)te maken.

## <a name="password-hash-synchronization-and-security-considerations"></a>Wachtwoordhashsynchronisatie en beveiligingsoverwegingen

Wanneer u Azure AD DS inschakelt, zijn verouderde wachtwoordhashes voor NTLM + Kerberos-verificatie vereist. Azure AD slaat geen wachtwoorden met duidelijke tekst op, dus deze hashes kunnen niet automatisch worden gegenereerd voor bestaande gebruikersaccounts. Eenmaal gegenereerd en opgeslagen, worden NTLM- en Kerberos-compatibele wachtwoordhashes altijd versleuteld opgeslagen in Azure AD.

De versleutelingssleutels zijn uniek voor elke Azure AD-tenant. Deze hashes zijn zodanig versleuteld dat alleen Azure AD DS toegang heeft tot de decryptiesleutels. Geen enkele andere service of component in Azure AD heeft toegang tot de decryptiesleutels.

Verouderde wachtwoordhashes worden vervolgens gesynchroniseerd vanuit Azure AD naar de domeincontrollers voor een door Azure AD DS beheerd domein. De schijven voor deze beheerde domeincontrollers in Azure AD DS worden in rust versleuteld. Deze wachtwoordhashes worden opgeslagen en beveiligd op deze domeincontrollers vergelijkbaar met hoe wachtwoorden worden opgeslagen en beveiligd in een on-premises AD DS-omgeving.

Voor Azure AD-omgevingen met alleen de cloud [moeten gebruikers hun wachtwoord opnieuw instellen/wijzigen](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) om de vereiste wachtwoordhashes te kunnen genereren en opslaan in Azure AD. Voor elk cloudgebruikersaccount dat in Azure AD is gemaakt nadat azure AD Domain Services is ingesloten, worden de wachtwoordhashes gegenereerd en opgeslagen in de compatibele NTLM- en Kerberos-compatibele indelingen. Alle cloudgebruikersaccounts moeten hun wachtwoord wijzigen voordat ze worden gesynchroniseerd met Azure AD DS.

Voor hybride gebruikersaccounts die zijn gesynchroniseerd vanuit de on-premises AD DS-omgeving met Azure AD Connect, moet u [Azure AD Connect configureren om wachtwoordhashes te synchroniseren in de compatibele NTLM- en Kerberos-compatibele indelingen.](tutorial-configure-password-hash-sync.md)

## <a name="next-steps"></a>Volgende stappen

Zie [Hoe wachtwoordhashsynchronisatie werkt met Azure AD Connect](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md?context=/azure/active-directory-domain-services/context/azure-ad-ds-context)voor meer informatie over de specifieke kenmerken van wachtwoordsynchronisatie.

Maak [een beheerd domein om](tutorial-create-instance.md)aan de slag te gaan met Azure AD DS.
