---
title: Overzicht van Azure Active Directory Domain Services | Microsoft Documenten
description: Lees in dit overzicht wat Azure Active Directory Domain Services biedt en hoe u deze in uw organisatie gebruiken om identiteitsservices te leveren aan toepassingen en services in de cloud.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: ea0fa0e9d4e475a8496d1ee52b4cdfea11a13d8d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76544101"
---
# <a name="what-is-azure-active-directory-domain-services"></a>Wat is Azure Active Directory Domain Services?

Azure Active Directory Domain Services (Azure AD DS) biedt beheerde domeinservices zoals domeinjoin, groepsbeleid, LDAP (Lightweight Directory Access Protocol) en Kerberos / NTLM-verificatie die volledig compatibel is met Windows Server Active Directory. U gebruikt deze domeinservices zonder dat u domeincontrollers in de cloud hoeft te implementeren, beheren en patchen. Azure AD DS integreert met uw bestaande Azure AD-tenant, waardoor gebruikers zich kunnen aanmelden met hun bestaande referenties. U ook bestaande groepen en gebruikersaccounts gebruiken om de toegang tot bronnen te beveiligen, wat zorgt voor een soepelere lift-and-shift van on-premises resources naar Azure.

Azure AD DS repliceert identiteitsgegevens van Azure AD, dus werkt met Azure AD-tenants die alleen in de cloud zijn of gesynchroniseerd met een on-premises AD DS-omgeving (Active Directory Domain Services). Dezelfde set Azure AD DS-functies bestaat voor beide omgevingen.

* Als u een bestaande on-premises AD DS-omgeving hebt, u gebruikersaccountgegevens synchroniseren om gebruikers een consistente identiteit te bieden.
* Voor cloud-only omgevingen hebt u geen traditionele on-premises AD DS-omgeving nodig om de gecentraliseerde identiteitsservices van Azure AD DS te gebruiken.

In de volgende video vindt u een overzicht van hoe Azure AD DS integreert met uw toepassingen en workloads om identiteitsservices in de cloud te leveren:

<br />

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

## <a name="common-ways-to-provide-identity-solutions-in-the-cloud"></a>Veelvoorkomende manieren om identiteitsoplossingen in de cloud te bieden

Wanneer u bestaande workloads migreert naar de cloud, kunnen directorybewuste toepassingen LDAP gebruiken voor lees- of schrijftoegang tot een on-premises AD DS-map. Toepassingen die op Windows Server worden uitgevoerd, worden doorgaans geïmplementeerd op virtuele machines (VM's) die zijn verbonden met domeintoepassingen, zodat ze veilig kunnen worden beheerd met groepsbeleid. Om eindgebruikers te authenticeren, kunnen de toepassingen ook vertrouwen op windows-geïntegreerde verificatie, zoals Kerberos- of NTLM-verificatie.

IT-beheerders gebruiken vaak een van de volgende oplossingen om een identiteitsservice te bieden aan toepassingen die in Azure worden uitgevoerd:

* Configureer een site-to-site VPN-verbinding tussen workloads die worden uitgevoerd in Azure en een on-premises AD DS-omgeving.
    * De on-premises domeincontrollers bieden vervolgens authenticatie via de VPN-verbinding.
* Maak replicadomeincontrollers met Behulp van Virtuele Azure-machines (VM's) om het AD DS-domein / forest uit te breiden van on-premises.
    * De domeincontrollers die op Azure VM's worden uitgevoerd, bieden verificatie en repliceren directorygegevens tussen de on-premises AD DS-omgeving.
* Implementeer een zelfstandige AD DS-omgeving in Azure met behulp van domeincontrollers die worden uitgevoerd op Azure VM's.
    * De domeincontrollers die worden uitgevoerd op Azure VM's bieden verificatie, maar er is geen directory-informatie gerepliceerd vanuit een on-premises AD DS-omgeving.

Met deze benaderingen maken VPN-verbindingen met de on-premises directory toepassingen kwetsbaar voor tijdelijke netwerkstoringen of storingen. Als u domeincontrollers implementeert met VM's in Azure, moet het IT-team de VM's beheren en vervolgens beveiligen, patchen, controleren, back-ups maken en deze oplossen.

Azure AD DS biedt alternatieven voor de noodzaak om VPN-verbindingen te maken terug naar een on-premises AD DS-omgeving of VM's in Azure uit te voeren en te beheren om identiteitsservices te bieden. Als beheerde service vermindert Azure AD DS de complexiteit om een geïntegreerde identiteitsoplossing te maken voor zowel hybride als cloud-only omgevingen.

## <a name="azure-ad-ds-features-and-benefits"></a>Azure AD DS-functies en -voordelen

Azure AD DS is volledig compatibel met een traditionele AD DS-omgeving voor bewerkingen zoals domeinjoin, secure LDAP (LDAPS), Groepsbeleid en DNS-beheer en LDAP-bindings- en leesondersteuning om identiteitsservices te leveren aan toepassingen en VM's in de cloud. LDAP-schrijfondersteuning is beschikbaar voor objecten die zijn gemaakt in het beheerde Azure AD DS-domein, maar niet voor resources die zijn gesynchroniseerd vanuit Azure AD. De volgende functies van Azure AD DS vereenvoudigen de implementatie- en beheerbewerkingen:

* **Vereenvoudigde implementatie-ervaring:** Azure AD DS is ingeschakeld voor uw Azure AD-tenant met één wizard in de Azure-portal.
* **Geïntegreerd met Azure AD:** Gebruikersaccounts, groepslidmaatschappen en referenties zijn automatisch beschikbaar via uw Azure AD-tenant. Nieuwe gebruikers, groepen of wijzigingen in kenmerken van uw Azure AD-tenant of uw on-premises AD DS-omgeving worden automatisch gesynchroniseerd met Azure AD DS.
    * Accounts in externe mappen die zijn gekoppeld aan uw Azure AD zijn niet beschikbaar in Azure AD DS. Referenties zijn niet beschikbaar voor die externe mappen, dus kan niet worden gesynchroniseerd met een Azure AD DS beheerd domein.
* **Gebruik uw bedrijfsreferenties/wachtwoorden:** Wachtwoorden voor gebruikers in Azure AD DS zijn hetzelfde als in uw Azure AD-tenant. Gebruikers kunnen hun bedrijfsreferenties gebruiken om machines voor het deelnemen aan het domein aan te sluiten, zich interactief of via extern bureaublad aan te melden en zich te verifiëren tegen het beheerde Azure AD DS-domein.
* **NTLM- en Kerberos-verificatie:** Met ondersteuning voor NTLM- en Kerberos-verificatie u toepassingen implementeren die afhankelijk zijn van windows-geïntegreerde verificatie.
* **Hoge beschikbaarheid:** Azure AD DS bevat meerdere domeincontrollers, die een hoge beschikbaarheid bieden voor uw beheerde domein. Deze hoge beschikbaarheid garandeert service-uptime en veerkracht tegen storingen.
    * In regio's die [Azure Availability Zones][availability-zones]ondersteunen, worden deze domeincontrollers ook verdeeld over zones voor extra tolerantie.

Enkele belangrijke aspecten van een door Azure AD DS beheerd domein zijn het volgende:

* Het azure AD DS-beheerde domein is een op zichzelf staand domein. Het is geen uitbreiding van een on-premises domein.
    * Indien nodig u eenmalige uitgaande forestvertrouwensrelaties maken, van Azure AD DS tot een on-premises AD DS-omgeving. Zie [Resourceforestconcepten en -functies voor Azure AD DS voor][ forest-trusts]meer informatie.
* Uw IT-team hoeft domeincontrollers voor dit door Azure AD DS beheer-beheerde domein niet te beheren, patchen of te controleren.

Voor hybride omgevingen waarop AD DS on-premises wordt uitgevoerd, hoeft u ad-replicatie niet te beheren naar het beheerde Azure AD DS-domein. Gebruikersaccounts, groepslidmaatschappen en referenties uit uw on-premises directory worden gesynchroniseerd met Azure AD via [Azure AD Connect][azure-ad-connect]. Deze gebruikersaccounts, groepslidmaatschappen en referenties zijn automatisch beschikbaar binnen het beheerde Azure AD DS-domein.

## <a name="how-does-azure-ad-ds-work"></a>Hoe werkt Azure AD DS?

Als u identiteitsservices wilt leveren, maakt Azure een AD DS-exemplaar op een virtueel netwerk naar keuze. Achter de schermen wordt een paar Windows Server-domeincontrollers gemaakt die worden uitgevoerd op Azure VM's. U hoeft deze domeincontrollers niet te beheren, configureren of bijwerken. Het Azure-platform beheert de domeincontrollers als onderdeel van de Azure AD DS-service.

Het beheerde Azure AD DS-domein is geconfigureerd om een eenrichtingssynchronisatie uit Te voeren vanuit Azure AD om toegang te bieden tot een centrale set gebruikers, groepen en referenties. U resources rechtstreeks maken in het beheerde Azure AD DS-domein, maar ze worden niet gesynchroniseerd met Azure AD. Toepassingen, services en VM's in Azure die verbinding maken met dit virtuele netwerk kunnen vervolgens gebruik maken van algemene AD DS-functies zoals domeinjoin, groepsbeleid, LDAP en Kerberos / NTLM-verificatie.

In een hybride omgeving met een on-premises AD DS-omgeving synchroniseert [Azure AD Connect][azure-ad-connect] identiteitsgegevens met Azure AD, die vervolgens wordt gesynchroniseerd met Azure AD DS.

![Synchronisatie in Azure AD Domain Services met Azure AD en on-premises Active Directory Domain Services met AD Connect](./media/active-directory-domain-services-design-guide/sync-topology.png)

Als u Azure AD DS in actie wilt zien, bekijken we een paar voorbeelden:

* [Azure AD DS voor hybride organisaties](#azure-ad-ds-for-hybrid-organizations)
* [Azure AD DS voor cloudorganisaties](#azure-ad-ds-for-cloud-only-organizations)

### <a name="azure-ad-ds-for-hybrid-organizations"></a>Azure AD DS voor hybride organisaties

Veel organisaties hebben een hybride infrastructuur die zowel cloud- als on-premises toepassingsworkloads bevat. Oudere toepassingen die zijn gemigreerd naar Azure als onderdeel van een lift- en shiftstrategie, kunnen traditionele LDAP-verbindingen gebruiken om identiteitsgegevens te verstrekken. Om deze hybride infrastructuur te ondersteunen, kunnen identiteitsgegevens uit een on-premises AD DS-omgeving worden gesynchroniseerd met een Azure AD-tenant. Azure AD DS voorziet deze verouderde toepassingen in Azure vervolgens van een identiteitsbron, zonder dat de toepassingsconnectiviteit moet worden geconfigureerd en beheren naar on-premises directoryservices.

Laten we eens kijken naar een voorbeeld voor Litware Corporation, een hybride organisatie die zowel on-premises als Azure-resources uitvoert:

![Azure Active Directory Domain Services voor een hybride organisatie die on-premises synchronisatie bevat](./media/overview/synced-tenant.png)

* Toepassingen en serverworkloads waarvoor domeinservices nodig zijn, worden geïmplementeerd in een virtueel netwerk in Azure.
    * Dit kunnen oudere toepassingen zijn die zijn gemigreerd naar Azure als onderdeel van een lift- en shiftstrategie.
* Als u identiteitsgegevens van hun on-premises directory wilt synchroniseren met hun Azure AD-tenant, implementeert Litware Corporation [Azure AD Connect.][azure-ad-connect]
    * Identiteitsgegevens die worden gesynchroniseerd, omvatten gebruikersaccounts en groepslidmaatschappen.
* Het IT-team van Litware maakt Azure AD DS in dit Azure AD-tenant in dit of een virtueel netwerk met één peered mogelijk.
* Toepassingen en VM's die zijn geïmplementeerd in het virtuele Azure-netwerk kunnen vervolgens Azure AD DS-functies gebruiken, zoals domeinjoin, LDAP-read, LDAP-binding, NTLM- en Kerberos-verificatie en groepsbeleid.

> [!IMPORTANT]
> Azure AD Connect mag alleen worden geïnstalleerd en geconfigureerd voor synchronisatie met on-premises AD DS-omgevingen. Azure AD Connect wordt niet geïnstalleerd in een door Azure AD DS beheerd domein om objecten te synchroniseren met Azure AD.

### <a name="azure-ad-ds-for-cloud-only-organizations"></a>Azure AD DS voor cloudorganisaties

Een Azure AD-tenant met alleen cloudgegevens heeft geen on-premises identiteitsbron. Gebruikersaccounts en groepslidmaatschappen worden bijvoorbeeld rechtstreeks in Azure AD gemaakt en beheerd.

Laten we nu eens kijken naar een voorbeeld voor Contoso, een cloudorganisatie die Azure AD gebruikt voor identiteit. Alle gebruikersidentiteiten, hun referenties en groepslidmaatschappen worden gemaakt en beheerd in Azure AD. Er is geen extra configuratie van Azure AD Connect om identiteitsgegevens te synchroniseren vanuit een on-premises directory.

![Azure Active Directory Domain Services voor een cloudorganisatie zonder on-premises synchronisatie](./media/overview/cloud-only-tenant.png)

* Toepassingen en serverworkloads waarvoor domeinservices nodig zijn, worden geïmplementeerd in een virtueel netwerk in Azure.
* Het IT-team van Contoso maakt Azure AD DS in dit Azure AD-tenant in dit of een virtueel netwerk met één peered mogelijk.
* Toepassingen en VM's die zijn geïmplementeerd in het virtuele Azure-netwerk kunnen vervolgens Azure AD DS-functies gebruiken, zoals domeinjoin, LDAP-read, LDAP-binding, NTLM- en Kerberos-verificatie en groepsbeleid.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over Azure AD DS met andere identiteitsoplossingen en hoe synchronisatie werkt:

* [Azure AD DS vergelijken met Azure AD, Active Directory Domain Services op Azure VM's en on-premises Active Directory Domain Services][compare]
* [Informatie over hoe Azure AD Domain Services synchroniseert met uw Azure AD-map][synchronization]

Maak om aan de slag [te gaan een door Azure AD DS beheerd domein met behulp van de Azure-portal.][tutorial-create]

<!-- INTERNAL LINKS -->
[compare]: compare-identity-solutions.md
[synchronization]: synchronization.md
[tutorial-create]: tutorial-create-instance.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md
[password-hash-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md
[availability-zones]: ../availability-zones/az-overview.md
[forest-trusts]: concepts-resource-forest.md
