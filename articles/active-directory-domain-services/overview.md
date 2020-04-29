---
title: Overzicht van Azure Active Directory Domain Services | Microsoft Docs
description: In dit overzicht leert u wat Azure Active Directory Domain Services biedt en hoe u het in uw organisatie kunt gebruiken om identiteits services te bieden aan toepassingen en services in de Cloud.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 0f5f890b4f32961e00fb30316a1dc7c88ef93a45
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80654846"
---
# <a name="what-is-azure-active-directory-domain-services"></a>Wat is Azure Active Directory Domain Services?

Azure Active Directory Domain Services (Azure AD DS) biedt beheerde domein Services, zoals domein deelname, groeps beleid, LDAP (Lightweight Directory Access Protocol) en Kerberos/NTLM-verificatie die volledig compatibel is met Windows Server Active Directory. U gebruikt deze domein Services zonder dat u domein controllers hoeft te implementeren, te beheren en te patchen in de Cloud. Azure AD DS is geïntegreerd met uw bestaande Azure AD-Tenant, waardoor gebruikers zich kunnen aanmelden met hun bestaande referenties. U kunt ook bestaande groepen en gebruikers accounts gebruiken om de toegang tot bronnen te beveiligen. Dit biedt een soepelere lift-en Shift-capaciteit van on-premises resources naar Azure.

Als u aan de slag wilt gaan, [maakt u een door Azure AD DS beheerd domein met behulp van de Azure Portal][tutorial-create].

Azure AD DS repliceert identiteits gegevens van Azure AD, dus werkt met Azure AD-tenants die alleen in de Cloud zijn of die zijn gesynchroniseerd met een on-premises Active Directory Domain Services-omgeving (AD DS). Voor beide omgevingen is dezelfde set met Azure AD DS-functies beschikbaar.

* Als u een bestaande on-premises AD DS omgeving hebt, kunt u gegevens van gebruikers accounts synchroniseren om een consistente identiteit voor gebruikers te bieden.
* Voor Cloud omgevingen hebt u geen traditionele on-premises AD DS omgeving nodig voor het gebruik van de gecentraliseerde identiteits services van Azure AD DS.

De volgende video biedt een overzicht van de manier waarop Azure AD DS integreert met uw toepassingen en workloads voor het leveren van identiteits Services in de Cloud:

<br />

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

## <a name="common-ways-to-provide-identity-solutions-in-the-cloud"></a>Gebruikelijke manieren om identiteits oplossingen te bieden in de Cloud

Wanneer u bestaande workloads migreert naar de Cloud, kunnen Directory-toepassingen gebruikmaken van LDAP voor lees-of schrijf toegang tot een on-premises AD DS Directory. Toepassingen die worden uitgevoerd op Windows Server, worden doorgaans geïmplementeerd op virtuele machines (Vm's) die lid zijn van een domein, zodat ze veilig kunnen worden beheerd met behulp van groepsbeleid. Voor de verificatie van eind gebruikers kunnen de toepassingen ook gebruikmaken van geïntegreerde Windows-verificatie, zoals Kerberos of NTLM-verificatie.

IT-beheerders gebruiken vaak een van de volgende oplossingen om een identiteits service te bieden aan toepassingen die worden uitgevoerd in Azure:

* Configureer een site-naar-site-VPN-verbinding tussen workloads die worden uitgevoerd in Azure en een on-premises AD DS omgeving.
    * De on-premises domein controllers bieden vervolgens verificatie via de VPN-verbinding.
* Maak replica domein controllers met behulp van virtuele machines van Azure (Vm's) om het AD DS domein of forest van on-premises uit te breiden.
    * De domein controllers die worden uitgevoerd op virtuele machines van Azure bieden verificatie en repliceren Directory gegevens tussen de on-premises AD DS omgeving.
* Implementeer een zelfstandige AD DS omgeving in azure met behulp van domein controllers die worden uitgevoerd op virtuele machines van Azure.
    * De domein controllers die worden uitgevoerd op virtuele machines van Azure bieden verificatie, maar er zijn geen Directory gegevens gerepliceerd vanuit een on-premises AD DS omgeving.

Met deze benaderingen kunnen VPN-verbindingen naar de on-premises Directory toepassingen kwetsbaar maken voor tijdelijke netwerk storingen of storingen. Als u domein controllers implementeert met behulp van Vm's in azure, moet het IT-team de Vm's beheren, vervolgens beveiligen, patches, back-ups maken en problemen oplossen.

Azure AD DS biedt alternatieven voor het maken van VPN-verbindingen naar een on-premises AD DS omgeving of voor het uitvoeren en beheren van virtuele machines in azure om identiteits services te bieden. Als beheerde service beperkt Azure AD DS de complexiteit van het maken van een geïntegreerde identiteits oplossing voor zowel hybride als Cloud omgevingen.

## <a name="azure-ad-ds-features-and-benefits"></a>Azure AD DS-functies en-voor delen

Om identiteits services te bieden aan toepassingen en virtuele machines in de Cloud, is Azure AD DS volledig compatibel met een traditionele AD DS omgeving voor bewerkingen, zoals domein deelname, secure LDAP (LDAPS), groepsbeleid, DNS-beheer en ondersteuning voor LDAP-bindingen en lezen. Ondersteuning voor LDAP-schrijf bewerkingen is beschikbaar voor objecten die zijn gemaakt in het door Azure AD DS beheerde domein, maar niet voor resources die zijn gesynchroniseerd vanuit Azure AD.

Voor meer informatie over uw identiteits opties kunt u [azure AD DS vergelijken met Azure AD, Active Directory Domain Services op virtuele machines van Azure en Active Directory Domain Services on-premises][compare].

Met de volgende functies van Azure AD DS worden de implementatie-en beheer bewerkingen vereenvoudigd:

* **Vereenvoudigde implementatie-ervaring:** Azure AD DS is ingeschakeld voor uw Azure AD-Tenant met behulp van één wizard in de Azure Portal.
* **Geïntegreerd met Azure AD:** Gebruikers accounts, groepslid maatschappen en referenties zijn automatisch beschikbaar vanuit uw Azure AD-Tenant. Nieuwe gebruikers, groepen of wijzigingen in kenmerken van uw Azure AD-Tenant of uw on-premises AD DS omgeving worden automatisch gesynchroniseerd naar Azure AD DS.
    * Accounts in externe mappen die zijn gekoppeld aan uw Azure AD zijn niet beschikbaar in azure AD DS. Referenties zijn niet beschikbaar voor deze externe directory's en kunnen dus niet worden gesynchroniseerd in een door Azure AD DS beheerd domein.
* **Uw bedrijfs referenties/wacht woorden gebruiken:** Wacht woorden voor gebruikers in azure AD DS zijn hetzelfde als in uw Azure AD-Tenant. Gebruikers kunnen hun bedrijfs referenties gebruiken om computers toe te voegen aan een domein, interactief of via extern bureau blad aan te melden en te verifiëren met behulp van het door Azure AD DS beheerde domein.
* **NTLM-en Kerberos-verificatie:** Met ondersteuning voor NTLM-en Kerberos-verificatie kunt u toepassingen implementeren die afhankelijk zijn van geïntegreerde Windows-authenticatie.
* **Hoge Beschik baarheid:** Azure AD DS bevat meerdere domein controllers die hoge Beschik baarheid bieden voor uw beheerde domein. Deze hoge Beschik baarheid garandeert de uptime van de service en de flexibiliteit van fouten.
    * In regio's die [Azure-beschikbaarheidszones][availability-zones]ondersteunen, worden deze domein controllers ook gedistribueerd over zones voor extra tolerantie.

Enkele belang rijke aspecten van een door Azure AD DS beheerd domein zijn onder andere:

* Het beheerde domein van Azure AD DS is een zelfstandig domein. Het is geen uitbrei ding van een on-premises domein.
    * Als dat nodig is, kunt u eenrichtings uitgaande forest-vertrouwens relaties maken op basis van Azure AD DS naar een on-premises AD DS omgeving. Zie [concepten en functies van het resource-forest voor Azure AD DS][ forest-trusts]voor meer informatie.
* Uw IT-team hoeft geen domein controllers te beheren, te patchen of te controleren voor dit door Azure AD DS beheerde domein.

Voor hybride omgevingen die on-premises worden AD DS uitgevoerd, hoeft u AD-replicatie niet te beheren naar het beheerde domein van Azure AD DS. Gebruikers accounts, groepslid maatschappen en referenties van uw on-premises Directory worden via [Azure AD Connect][azure-ad-connect]naar Azure AD gesynchroniseerd. Deze gebruikers accounts, groepslid maatschappen en referenties zijn automatisch beschikbaar in het door Azure AD DS beheerde domein.

## <a name="how-does-azure-ad-ds-work"></a>Hoe werkt Azure AD DS?

Voor het leveren van identiteits Services maakt Azure een AD DS-exemplaar in een virtueel netwerk van uw keuze. Achter de schermen worden er een paar Windows Server-domein controllers gemaakt die worden uitgevoerd op virtuele Azure-machines. U hoeft deze domein controllers niet te beheren, te configureren of bij te werken. Het Azure-platform beheert de domein controllers als onderdeel van de Azure AD DS-service.

Het beheerde domein van Azure AD DS is geconfigureerd voor het uitvoeren van een eenrichtings synchronisatie vanuit Azure AD om toegang te bieden tot een centrale set gebruikers, groepen en referenties. U kunt resources rechtstreeks maken in het beheerde domein van Azure AD DS, maar ze worden niet gesynchroniseerd met Azure AD. Toepassingen, services en virtuele machines in azure die verbinding maken met dit virtuele netwerk kunnen vervolgens gebruikmaken van algemene AD DS functies, zoals domein deelname, groeps beleid, LDAP en Kerberos/NTLM-verificatie.

In een hybride omgeving met een on-premises AD DS omgeving, [Azure AD Connect][azure-ad-connect] synchroniseert identiteits gegevens met Azure AD, die vervolgens worden gesynchroniseerd met Azure AD DS.

![Synchronisatie in Azure AD Domain Services met Azure AD en on-premises Active Directory Domain Services met AD Connect](./media/active-directory-domain-services-design-guide/sync-topology.png)

Voor een overzicht van de Azure-AD DS in actie, bekijken we een paar voor beelden:

* [Azure-AD DS voor hybride organisaties](#azure-ad-ds-for-hybrid-organizations)
* [Azure AD DS voor Cloud organisaties](#azure-ad-ds-for-cloud-only-organizations)

### <a name="azure-ad-ds-for-hybrid-organizations"></a>Azure-AD DS voor hybride organisaties

Veel organisaties voeren een hybride infra structuur uit met zowel Cloud-als on-premises toepassings workloads. Oudere toepassingen die naar Azure worden gemigreerd als onderdeel van een lift-en Shift-strategie, kunnen gebruikmaken van traditionele LDAP-verbindingen om identiteits gegevens op te geven. Ter ondersteuning van deze hybride infra structuur kunnen identiteits gegevens van een on-premises AD DS omgeving worden gesynchroniseerd met een Azure AD-Tenant. Azure AD DS levert deze verouderde toepassingen vervolgens in azure met een identiteits bron, zonder dat u de connectiviteit van de toepassing opnieuw hoeft te configureren en te beheren voor on-premises Directory Services.

Laten we eens kijken naar een voor beeld van Litware Corporation, een hybride organisatie die zowel on-premises als Azure-resources uitvoert:

![Azure Active Directory Domain Services voor een hybride organisatie die on-premises synchronisatie bevat](./media/overview/synced-tenant.png)

* Toepassingen en server werkbelastingen waarvoor domein Services zijn vereist, worden geïmplementeerd in een virtueel netwerk in Azure.
    * Dit kan bestaan uit verouderde toepassingen die naar Azure zijn gemigreerd als onderdeel van een lift-en Shift-strategie.
* Om identiteits gegevens van hun on-premises Directory te synchroniseren met hun Azure AD-Tenant, implementeert litware Corporation [Azure AD Connect][azure-ad-connect].
    * De identiteits gegevens die worden gesynchroniseerd, zijn onder andere gebruikers accounts en groepslid maatschappen.
* Het IT-team van Litware maakt Azure AD DS voor hun Azure AD-Tenant in dit of een peered virtueel netwerk.
* Toepassingen en Vm's die zijn geïmplementeerd in het virtuele netwerk van Azure kunnen vervolgens Azure AD DS-functies gebruiken, zoals domein deelname, LDAP-lees-, LDAP-binding, NTLM-en Kerberos-verificatie en groepsbeleid.

> [!IMPORTANT]
> Azure AD Connect mag alleen worden geïnstalleerd en geconfigureerd voor synchronisatie met on-premises AD DS omgevingen. Het is niet mogelijk om Azure AD Connect te installeren in een beheerd domein van Azure AD DS om objecten terug te synchroniseren naar Azure AD.

### <a name="azure-ad-ds-for-cloud-only-organizations"></a>Azure AD DS voor Cloud organisaties

Een Azure AD-Tenant in de Cloud heeft geen on-premises identiteits bron. Gebruikers accounts en groepslid maatschappen worden bijvoorbeeld rechtstreeks in azure AD gemaakt en beheerd.

Laten we nu eens kijken naar een voor beeld van contoso, een Cloud organisatie die Azure AD gebruikt voor identiteit. Alle gebruikers-id's, hun referenties en groepslid maatschappen worden gemaakt en beheerd in azure AD. Er is geen aanvullende configuratie van Azure AD Connect voor het synchroniseren van identiteits gegevens uit een on-premises Directory.

![Azure Active Directory Domain Services voor een Cloud organisatie zonder on-premises synchronisatie](./media/overview/cloud-only-tenant.png)

* Toepassingen en server werkbelastingen waarvoor domein Services zijn vereist, worden geïmplementeerd in een virtueel netwerk in Azure.
* Het IT-team van Contoso maakt Azure AD DS voor hun Azure AD-Tenant in dit of een peered virtueel netwerk.
* Toepassingen en Vm's die zijn geïmplementeerd in het virtuele netwerk van Azure kunnen vervolgens Azure AD DS-functies gebruiken, zoals domein deelname, LDAP-lees-, LDAP-binding, NTLM-en Kerberos-verificatie en groepsbeleid.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over Azure AD DS vergelijkt met andere identiteits oplossingen en hoe synchronisatie werkt:

* [Azure-AD DS vergelijken met Azure AD, Active Directory Domain Services op virtuele machines van Azure en on-premises Active Directory Domain Services][compare]
* [Meer informatie over hoe Azure AD Domain Services synchroniseert met uw Azure AD-adres lijst][synchronization]

Als u aan de slag wilt gaan, [maakt u een door Azure AD DS beheerd domein met behulp van de Azure Portal][tutorial-create].

<!-- INTERNAL LINKS -->
[compare]: compare-identity-solutions.md
[synchronization]: synchronization.md
[tutorial-create]: tutorial-create-instance.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md
[password-hash-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md
[availability-zones]: ../availability-zones/az-overview.md
[forest-trusts]: concepts-resource-forest.md
