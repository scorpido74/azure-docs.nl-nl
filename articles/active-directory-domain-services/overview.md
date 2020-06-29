---
title: Overzicht van Azure Active Directory Domain Services | Microsoft Docs
description: In dit overzicht leert u wat Azure Active Directory Domain Services biedt en hoe u dit in uw organisatie kunt gebruiken om identiteitsservices te bieden voor toepassingen en services in de cloud.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 06/08/2020
ms.author: iainfou
ms.openlocfilehash: 472ff9de069e7d95cb1753a6b05830649806d2fc
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84734551"
---
# <a name="what-is-azure-active-directory-domain-services"></a>Wat is Azure Active Directory Domain Services?

Azure Active Directory Domain Services (Azure AD DS) biedt beheerde domeinservices zoals domeindeelname, groepsbeleid, Lightweight Directory Access Protocol (LDAP) en Kerberos/NTLM-verificatie. U gebruikt deze domeinservices zonder domeincontrollers (DC’s) in de cloud te hoeven implementeren, beheren en patchen.

Een beheerd domein is een DNS-naamruimte en bijbehorende directory. Het beheerde domein integreert met uw bestaande Azure AD-tenant, waardoor gebruikers zich kunnen aanmelden met hun bestaande referenties. U kunt ook bestaande groepen en gebruikersaccounts gebruiken om toegang tot resources te beveiligen, wat een soepelere lift-and-shift van on-premises resources naar Azure biedt.

Azure AD DS kan met uw bestaande Azure AD-tenant worden geïntegreerd. Dankzij deze integratie kunnen gebruikers zich met hun bestaande referenties aanmelden bij services en toepassingen die zijn verbonden met het beheerde domein. U kunt ook bestaande groepen en gebruikersaccounts gebruiken om toegang tot resources te beveiligen. Deze functies bieden een soepeler lift-and-shift van on-premises resources naar Azure.

> [!div class="nextstepaction"]
> [Maak eerst een beheerd Azure AD DS-domein met behulp van Azure Portal][tutorial-create]

In Azure AD DS worden identiteitsgegevens van Azure AD gerepliceerd, zodat het werkt met Azure AD-tenants die alleen in de cloud bestaan of die zijn gesynchroniseerd met een on-premises Active Directory Domain Services-omgeving (AD DS). Dezelfde set Azure AD DS-functies bestaat voor beide omgevingen.

* Als u over een bestaande on-premises AD DS-omgeving beschikt, kunt u gebruikersaccountgegevens synchroniseren om gebruikers een consistente identiteit te bieden. Zie [Synchronisatie van objecten en referenties in een beheerd domein][synchronization] voor meer informatie.
* Voor omgevingen die alleen in de cloud bestaan, hebt u geen traditionele on-premises AD DS-omgeving nodig om de gecentraliseerde identiteitsservices van Azure AD DS te gebruiken.

Zie [Beheerconcepten voor gebruikersaccounts, wachtwoorden en beheer in Azure AD DS][administration-concepts] voor meer informatie over het beheer van een beheerd domein.

In de volgende video ziet u een overzicht van de manier waarop Azure AD DS kan worden geïntegreerd met uw toepassingen en workloads om identiteitsservices in de cloud te bieden:

<br />

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

## <a name="common-ways-to-provide-identity-solutions-in-the-cloud"></a>Algemene manieren om identiteitsoplossingen in de cloud te bieden

Wanneer u bestaande workloads naar de cloud migreert, wordt voor toepassingen met een mappenstructuur mogelijk LDAP gebruikt voor lees- of schrijftoegang tot een on-premises AD DS-map. Toepassingen die op Windows Server worden uitgevoerd, worden doorgaans geïmplementeerd op met een domein samengevoegde virtuele machines (VM's) zodat ze veilig kunnen worden beheerd met behulp van groepsbeleid. Voor het verifiëren van eindgebruikers kunnen de toepassingen ook afhankelijk zijn van met Windows geïntegreerde verificatie, zoals Kerberos of NTLM-verificatie.

IT-beheerders gebruiken vaak een van de volgende oplossingen om een identiteitsservice te bieden voor toepassingen die worden uitgevoerd in Azure:

* Configureer een site-naar-site VPN-verbinding tussen workloads die worden uitgevoerd in Azure en een on-premises AD DS-omgeving.
    * De on-premises domeincontrollers bieden vervolgens verificatie via de VPN-verbinding.
* Maak replica's van domeincontrollers met behulp van virtuele Azure-machines (VM's) om het AD DS-domein/forest uit te breiden van on-premises.
    * De domeincontrollers die worden uitgevoerd op virtuele Azure-machines bieden verificatie en repliceren mapgegevens tussen de on-premises AD DS-omgeving.
* Implementeer een zelfstandige AD DS-omgeving in Azure met behulp van domeincontrollers die worden uitgevoerd op virtuele Azure-machines.
    * De domeincontrollers die worden uitgevoerd op virtuele Azure-machines bieden verificatie, maar er worden geen mapgegevens gerepliceerd vanuit een on-premises AD DS-omgeving.

Met deze methoden maken VPN-verbindingen met de on-premises map toepassingen kwetsbaar voor tijdelijke netwerkstoringen of uitval. Als u domeincontrollers implementeert met behulp van virtuele machines in Azure, moet het IT-team de virtuele machines beheren en deze vervolgens beveiligen, patchen en bewaken, er een back-up van maken en problemen oplossen.

Azure AD DS biedt alternatieven voor het moeten maken van VPN-verbindingen met een on-premises AD DS-omgeving of het uitvoeren en beheren van virtuele machines in Azure om identiteitsservices te bieden. Als beheerde service zorgt Azure AD DS ervoor dat het maken van een geïntegreerde identiteitsoplossing voor zowel hybride omgevingen als omgevingen die alleen in de cloud bestaan minder complex is.

> [!div class="nextstepaction"]
> [Azure AD DS vergelijken met Azure AD en zelfbeheerde AD DS op virtuele Azure-machines of on-premises][compare]

## <a name="how-does-azure-ad-ds-work"></a>Hoe werkt Azure AD DS?

Voor het bieden van identiteitsservices maakt Azure een beheerd AD DS-domein op een virtueel netwerk van uw keuze. Achter de schermen worden twee Windows Server-domeincontrollers gemaakt die worden uitgevoerd op virtuele Azure-machines. U hoeft deze domeincontrollers niet te beheren, te configureren of bij te werken. Het Azure-platform beheert de domeincontrollers als onderdeel van de Azure AD DS-service.

Het beheerde domein wordt geconfigureerd om een synchronisatie in één richting van Azure AD uit te voeren om toegang te bieden tot een centrale set gebruikers, groepen en referenties. U kunt resources rechtstreeks in het beheerde domein maken, maar ze worden niet terug gesynchroniseerd naar Azure AD. Voor toepassingen, services en virtuele machines in Azure die verbinding maken met dit virtuele netwerk kunnen vervolgens algemene AD DS-functies worden gebruikt, zoals domeindeelname, groepsbeleid, LDAP en Kerberos/NTLM-verificatie.

In een hybride omgeving met een on-premises AD DS-omgeving synchroniseert [Azure AD Connect][azure-ad-connect] identiteitsgegevens met Azure AD, die vervolgens worden gesynchroniseerd naar Azure AD DS.

![Synchronisatie in Azure AD-domeinservices met Azure AD en on-premises Active Directory Domain Services met behulp van AD Connect](./media/active-directory-domain-services-design-guide/sync-topology.png)

Laten we een paar voorbeelden bekijken van Azure AD DS in actie:

* [Azure AD DS voor hybride organisaties](#azure-ad-ds-for-hybrid-organizations)
* [Azure AD DS voor organisaties die alleen de cloud gebruiken](#azure-ad-ds-for-cloud-only-organizations)

### <a name="azure-ad-ds-for-hybrid-organizations"></a>Azure AD DS voor hybride organisaties

Veel organisaties voeren een hybride infrastructuur uit met toepassingsworkloads in zowel de cloud als on-premises. Voor verouderde toepassingen die naar Azure zijn gemigreerd als onderdeel van een lift-and-shift-strategie worden mogelijk traditionele LDAP-verbindingen gebruikt om identiteitsgegevens te bieden. Ter ondersteuning van deze hybride infrastructuur kunnen identiteitsgegevens van een on-premises AD DS-omgeving worden gesynchroniseerd naar een Azure AD-tenant. Azure AD DS biedt deze verouderde toepassingen vervolgens in Azure met een identiteitsbron, zonder toepassingsconnectiviteit weer naar on-premises mapservices te hoeven configureren en te beheren.

Laten we eens kijken naar een voorbeeld voor Litware Corporation, een hybride organisatie die zowel on-premises resources als Azure-resources uitvoert:

![Azure Active Directory Domain Services voor een hybride organisatie met on-premises synchronisatie](./media/overview/synced-tenant.png)

* Toepassingen en serverworkloads waarvoor domeinservices moeten worden geïmplementeerd in een virtueel netwerk in Azure.
    * Dit kunnen verouderde toepassingen zijn die naar Azure zijn gemigreerd als onderdeel van een lift-and-shift-strategie.
* Voor het synchroniseren van identiteitsgegevens van hun on-premises map naar hun Azure AD-tenant implementeert Litware Corporation [Azure AD Connect][azure-ad-connect].
    * Identiteitsgegevens die worden gesynchroniseerd, zijn onder andere gebruikersaccounts en groepslidmaatschappen.
* Het IT-team van Litware schakelt Azure AD DS in voor hun Azure AD-tenant in dit, of in een gepeerd, virtueel netwerk.
* Toepassingen en virtuele machines die in het virtuele Azure-netwerk zijn geïmplementeerd, kunnen vervolgens gebruik maken van Azure AD DS-functies zoals domeindeelname, LDAP-leesbewerkingen, LDAP-bindingen, NTLM- en Kerberos-verificatie en groepsbeleid.

> [!IMPORTANT]
> Azure AD Connect moet alleen worden geïnstalleerd en geconfigureerd voor synchronisatie met on-premises AD DS-omgevingen. Het installeren van Azure AD Connect in een beheerd domein om objecten weer naar Azure AD te synchroniseren, wordt niet ondersteund.

### <a name="azure-ad-ds-for-cloud-only-organizations"></a>Azure AD DS voor organisaties die alleen de cloud gebruiken

Een Azure AD-tenant die alleen in de cloud bestaat, beschikt niet over een on-premises identiteitsbron. Gebruikersaccounts en groepslidmaatschappen worden bijvoorbeeld rechtstreeks in Azure AD gemaakt en beheerd.

Laten we eens kijken naar een voorbeeld voor Contoso, een organisatie die alleen de cloud gebruikt en die Azure AD voor identiteiten gebruikt. Alle gebruikersidentiteiten, hun referenties en groepslidmaatschappen worden gemaakt en beheerd in Azure AD. Er is geen extra configuratie van Azure AD Connect nodig om identiteitsgegevens van een on-premises map te synchroniseren.

![Azure Active Directory Domain Services voor een organisatie die alleen de cloud gebruikt zonder on-premises synchronisatie](./media/overview/cloud-only-tenant.png)

* Toepassingen en serverworkloads waarvoor domeinservices moeten worden geïmplementeerd in een virtueel netwerk in Azure.
* Het IT-team van Contoso schakelt Azure AD DS in voor hun Azure AD-tenant in dit, of in een gepeerd, virtueel netwerk.
* Toepassingen en virtuele machines die in het virtuele Azure-netwerk zijn geïmplementeerd, kunnen vervolgens gebruik maken van Azure AD DS-functies zoals domeindeelname, LDAP-leesbewerkingen, LDAP-bindingen, NTLM- en Kerberos-verificatie en groepsbeleid.

## <a name="azure-ad-ds-features-and-benefits"></a>Azure AD DS-functies en -voordelen

Om identiteitsservices te bieden voor toepassingen en virtuele machines in de cloud is Azure AD DS volledig compatibel met een traditionele AD DS-omgeving voor bewerkingen zoals domeindeelname, beveiligde LDAP (LDAPS), groepsbeleid, DNS-beheer en ondersteuning voor LDAP-bindingen en -leesbewerkingen. LDAP-schrijfondersteuning is beschikbaar voor objecten die in het beheerde Azure AD DS-domein zijn gemaakt, maar niet voor resources die zijn gesynchroniseerd vanuit Azure AD.

Voor meer informatie over uw identiteit opties kunt u [Azure AD DS vergelijken met Azure AD, Active Directory Domain Services op virtuele Azure-machines en on-premises Active Directory Domain Services][compare].

De volgende functies van Azure AD DS zorgen voor eenvoudigere implementatie- en beheerbewerkingen:

* **Vereenvoudigde implementatie-ervaring:** Azure AD DS wordt ingeschakeld voor uw Azure AD-tenant met behulp van één wizard in Azure Portal.
* **Geïntegreerd met Azure AD:** Gebruikersaccounts, groepslidmaatschappen en referenties worden automatisch beschikbaar via uw Azure AD-tenant. Nieuwe gebruikers en groepen, of wijzigingen in kenmerken van uw Azure AD-tenant of uw on-premises AD DS-omgeving, worden automatisch gesynchroniseerd naar Azure AD DS.
    * Accounts in externe mappen die aan uw Azure AD zijn gekoppeld, zijn niet beschikbaar in Azure AD DS. Referenties zijn niet beschikbaar voor die externe mappen, dus deze kunnen niet worden gesynchroniseerd naar een beheerd Azure AD DS-domein.
* **Gebruik uw zakelijke referenties/wachtwoorden:** Wachtwoorden voor gebruikers in Azure AD DS zijn dezelfde als in uw Azure AD-tenant. Gebruikers kunnen hun zakelijke referenties gebruiken om domeindeelname op apparaten te gebruiken, zich interactief of via een extern bureaublad aan te melden en zich te verifiëren bij het beheerde Azure AD DS-domein.
* **NTLM- en Kerberos-verificatie:** Met ondersteuning voor NTLM- en Kerberos-verificatie kunt u toepassingen implementeren die afhankelijk zijn van met Windows geïntegreerde verificatie.
* **Hoge beschikbaarheid:** Azure AD DS bevat meerdere domeincontrollers, die hoge beschikbaarheid voor uw beheerde domein bieden. Door deze hoge beschikbaarheid is uptime van de service en weerstand tegen fouten gegarandeerd.
    * In regio's die ondersteuning bieden voor [Azure-beschikbaarheidszones][availability-zones] worden deze domeincontrollers ook gedistribueerd over meerdere zones voor extra flexibiliteit.

Belangrijke aspecten van een beheerd Azure AD DS-domein zijn onder andere:

* Het beheerde Azure AD DS-domein is een zelfstandig domein. Het domein is geen extensie van een on-premises domein.
    * Waar nodig kunt u uitgaande forestvertrouwensrelaties in één richting maken van Azure AD DS naar een on-premises AD DS-omgeving. Zie [Resourceforestconcepten en -functies voor Azure AD DS][ forest-trusts] voor meer informatie.
* Uw IT-team hoeft geen domeincontrollers voor dit beheerde Azure AD DS-domein te beheren, patchen of bewaken.

Voor hybride omgevingen waarin AD DS on-premises wordt uitgevoerd, hoeft u geen AD-replicatie naar het beheerde Azure AD DS-domein te beheren. Gebruikersaccounts, groepslidmaatschappen en referenties van uw on-premises map worden gesynchroniseerd naar Azure AD via [Azure AD Connect][azure-ad-connect]. Deze gebruikersaccounts, groepslidmaatschappen en referenties worden automatisch beschikbaar in het beheerde Azure AD DS-domein.


## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over het verschil tussen Azure AD DS en andere identiteitsoplossingen en de werking van synchronisatie:

* [Azure AD DS vergelijken met Azure AD, Active Directory Domain Services op virtuele Azure-machines en on-premises Active Directory Domain Services][compare]
* [Meer informatie over de manier waarop Azure AD-domeinservices met uw Azure AD-map worden gesynchroniseerd][synchronization]

[Maak eerst een beheerd domein met behulp van de Azure-portal][tutorial-create].

<!-- INTERNAL LINKS -->
[compare]: compare-identity-solutions.md
[synchronization]: synchronization.md
[tutorial-create]: tutorial-create-instance.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md
[password-hash-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md
[availability-zones]: ../availability-zones/az-overview.md
[forest-trusts]: concepts-resource-forest.md
[administration-concepts]: administration-concepts.md
[synchronization]: synchronization.md
