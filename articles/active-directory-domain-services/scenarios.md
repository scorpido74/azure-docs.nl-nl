---
title: Veelvoorkomende implementatiescenario's voor Azure AD-domeinservices | Microsoft Documenten
description: Meer informatie over enkele veelvoorkomende scenario's en use-cases voor Azure Active Directory Domain Services om waarde te bieden en te voldoen aan zakelijke behoeften.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: ac67ef64ca4850c6e805b5314ace856114d889a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77917227"
---
# <a name="common-use-cases-and-scenarios-for-azure-active-directory-domain-services"></a>Algemene use-cases en scenario's voor Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) biedt beheerde domeinservices zoals domeinjoin, groepsbeleid, LDAP (Lightweight Directory Access Protocol) en Kerberos / NTLM-verificatie. Azure AD DS integreert met uw bestaande Azure AD-tenant, waardoor gebruikers zich kunnen aanmelden met hun bestaande referenties. U gebruikt deze domeinservices zonder dat u domeincontrollers in de cloud hoeft te implementeren, beheren en patchen, wat zorgt voor een soepelere lift-and-shift van on-premises resources naar Azure.

In dit artikel worden enkele veelvoorkomende bedrijfsscenario's beschreven waarin Azure AD DS waarde biedt en aan die behoeften voldoet.

## <a name="secure-administration-of-azure-virtual-machines"></a>Veilig beheer van virtuele Azure-machines

Als u één set AD-referenties wilt gebruiken, kunnen virtuele Azure-machines (VM's) worden samengevoegd met een door Azure AD DS beheerd domein. Deze aanpak vermindert problemen met het beheer van referenties, zoals het onderhouden van lokale beheerdersaccounts op elke vm of afzonderlijke accounts en wachtwoorden tussen omgevingen.

VM's die zijn verbonden met een door Azure AD DS beheerd domein, kunnen ook worden beheerd en beveiligd met groepsbeleid. Vereiste beveiligingsbasislijnen kunnen worden toegepast op VM's om ze te vergrendelen in overeenstemming met de richtlijnen voor bedrijfsbeveiliging. U bijvoorbeeld groepsbeleidsbeheermogelijkheden gebruiken om de typen toepassingen te beperken die op de VM kunnen worden gestart.

![Gestroomlijnd beheer van virtuele Azure-machines](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Laten we eens kijken naar een gemeenschappelijk voorbeeldscenario. Nu servers en andere infrastructuur aan het einde van de levensduur zijn, wil Contoso applicaties die momenteel on-premises worden gehost, verplaatsen naar de cloud. Hun huidige IT-standaard mandaten dat servers die bedrijfstoepassingen hosten, moeten worden samengevoegd en beheerd met groepsbeleid. De IT-beheerder van Contoso geeft er de voorkeur aan om vm's voor het domein te sluiten die in Azure zijn geïmplementeerd om beheer eenvoudiger te maken, omdat gebruikers zich vervolgens kunnen aanmelden met hun bedrijfsreferenties. Wanneer er een domein is gekoppeld, kunnen VM's ook worden geconfigureerd om te voldoen aan vereiste beveiligingsbasislijnen met groepsbeleidsobjecten (GPO's). Contoso zou liever niet hun eigen domeincontrollers implementeren, monitoren en beheren in Azure.

Azure AD DS past prima bij deze use-case. Met een door Azure AD DS beheerd domein u VM's voor domein-joinen, één set referenties gebruiken en groepsbeleid toepassen. Als beheerd domein hoeft u de domeincontrollers niet zelf te configureren en te onderhouden.

### <a name="deployment-notes"></a>Implementatienotities

De volgende implementatieoverwegingen zijn van toepassing op deze voorbeeldgebruiksaanvraag:

* Azure AD DS-beheerde domeinen gebruiken standaard één platte organisatie-eenheidsstructuur. Alle vm's met domeinverbonden zijn in één eenheid. Indien gewenst u aangepaste OE's maken.
* Azure AD DS maakt gebruik van een ingebouwde GPO voor de containers voor gebruikers en computers. Voor extra controle u aangepaste GPO's maken en deze targeten op aangepaste Ok's.
* Azure AD DS ondersteunt het schema van het basis-AD-computerobject. U het schema van het computerobject niet uitbreiden.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-bind-authentication"></a>On-premises on-premises toepassingen voor lift- en shifttoepassingen die LDAP-bindingen gebruiken

Als voorbeeldscenario heeft Contoso een on-premises applicatie die vele jaren geleden bij een ISV is gekocht. De toepassing is momenteel in onderhoudsmodus door de ISV en het aanvragen van wijzigingen in de toepassing is onbetaalbaar. Deze toepassing heeft een webgebaseerde frontend dat gebruikersreferenties verzamelt met behulp van een webformulier en vervolgens gebruikers verifieert door een LDAP-binding uit te voeren voor de on-premises AD DS-omgeving.

![LDAP-binding](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso wil deze toepassing migreren naar Azure. De toepassing moet blijven werken zoals-is, zonder wijzigingen nodig. Bovendien moeten gebruikers zich kunnen verifiëren met behulp van hun bestaande bedrijfsreferenties en zonder aanvullende training. Het moet transparant zijn voor eindgebruikers waar de toepassing wordt uitgevoerd.

In dit scenario kan met Azure AD DS toepassingen LDAP-bindingen uitvoeren als onderdeel van het verificatieproces. Verouderde on-premises toepassingen kunnen worden opgeheven en verschuiven naar Azure en gebruikers naadloos blijven verifiëren zonder enige wijziging in de configuratie of gebruikerservaring.

### <a name="deployment-notes"></a>Implementatienotities

De volgende implementatieoverwegingen zijn van toepassing op deze voorbeeldgebruiksaanvraag:

* Zorg ervoor dat de toepassing niet hoeft te wijzigen / schrijven naar de directory. LDAP-schrijftoegang tot een door Azure AD DS beheerd domein wordt niet ondersteund.
* U wachtwoorden niet rechtstreeks wijzigen in een door Azure AD DS beheerd domein. Eindgebruikers kunnen hun wachtwoord wijzigen via het mechanisme voor het wijzigen van het selfservicewachtwoord van Azure AD of tegen de on-premises directory. Deze wijzigingen worden vervolgens automatisch gesynchroniseerd en beschikbaar in het beheerde Azure AD DS-domein.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-read-to-access-the-directory"></a>On-premises on-premises toepassingen voor lift- en shifttoepassingen die LDAP-lezen gebruiken om toegang te krijgen tot de map

Net als het vorige voorbeeldscenario, laten we aannemen dat Contoso een on-premises line-of-business (LOB)-toepassing heeft die bijna tien jaar geleden is ontwikkeld. Deze toepassing is directory aware en is ontworpen om LDAP te gebruiken om informatie / attributen over gebruikers van AD DS te lezen. De toepassing wijzigt geen kenmerken of schrijft op een andere manier naar de map.

Contoso wil deze toepassing migreren naar Azure en de verouderde on-premises hardware die momenteel deze toepassing host, met pensioen laten gaan. De toepassing kan niet worden herschreven om moderne directory-API's te gebruiken, zoals de OP REST gebaseerde Microsoft Graph API. Een lift-and-shift-optie is gewenst waar de toepassing kan worden gemigreerd om in de cloud te worden uitgevoerd, zonder de code te wijzigen of de toepassing te herschrijven.

Om u te helpen bij dit scenario, laat Azure AD DS toepassingen LDAP-reads uitvoeren tegen het beheerde domein om de attribuutgegevens te krijgen die het nodig heeft. De toepassing hoeft niet te worden herschreven, dus een lift-and-shift in Azure laat gebruikers de app blijven gebruiken zonder te beseffen dat er een verandering is in waar deze wordt uitgevoerd.

### <a name="deployment-notes"></a>Implementatienotities

De volgende implementatieoverwegingen zijn van toepassing op deze voorbeeldgebruiksaanvraag:

* Zorg ervoor dat de toepassing niet hoeft te wijzigen / schrijven naar de directory. LDAP-schrijftoegang tot een door Azure AD DS beheerd domein wordt niet ondersteund.
* Zorg ervoor dat de toepassing geen aangepast/uitgebreid Active Directory-schema nodig heeft. Schema-extensies worden niet ondersteund in Azure AD DS.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure"></a>Een on-premises service of daemon-toepassing migreren naar Azure

Sommige toepassingen omvatten meerdere lagen, waarbij een van de lagen geverifieerde oproepen naar een backendlaag moet uitvoeren, zoals een database. AD-serviceaccounts worden vaak gebruikt in deze scenario's. Wanneer u toepassingen optilt naar Azure, u met Azure AD DS serviceaccounts op dezelfde manier blijven gebruiken. U ervoor kiezen om hetzelfde serviceaccount te gebruiken dat is gesynchroniseerd vanuit uw on-premises directory met Azure AD of een aangepaste organisatie-eenheid te maken en vervolgens een apart serviceaccount in die organisatieorganisatie te maken. Met beide benaderingen blijven toepassingen op dezelfde manier functioneren om geverifieerde gesprekken te voeren naar andere lagen en services.

![Serviceaccount met WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

In dit voorbeeldscenario heeft Contoso een op maat gemaakte softwarevaulttoepassing met een webfront-end, een SQL-server en een backend FTP-server. Windows-geïntegreerde verificatie met serviceaccounts verifieert de webfront-end naar de FTP-server. De webfront-end is ingesteld om als serviceaccount te worden uitgevoerd. De backendserver is geconfigureerd om toegang vanaf het serviceaccount voor de webfront-end te autoriseren. Contoso wil geen eigen VM's voor domeincontrollerin de cloud implementeren en beheren om deze toepassing naar Azure te verplaatsen.

Voor dit scenario kunnen de servers die de webfront-end, SQL-server en de FTP-server hosten, worden gemigreerd naar Azure VM's en worden samengevoegd naar een door Azure AD DS beheerd domein. De VM's kunnen vervolgens hetzelfde serviceaccount gebruiken in hun on-premises directory voor de verificatiedoeleinden van de app, die wordt gesynchroniseerd via Azure AD met Azure AD Connect.

### <a name="deployment-notes"></a>Implementatienotities

De volgende implementatieoverwegingen zijn van toepassing op deze voorbeeldgebruiksaanvraag:

* Zorg ervoor dat de toepassingen een gebruikersnaam en wachtwoord gebruiken voor verificatie. Verificatie op basis van certificaten of smartcardwordt niet ondersteund door Azure AD DS.
* U wachtwoorden niet rechtstreeks wijzigen in een door Azure AD DS beheerd domein. Eindgebruikers kunnen hun wachtwoord wijzigen via het mechanisme voor het wijzigen van het selfservicewachtwoord van Azure AD of tegen de on-premises directory. Deze wijzigingen worden vervolgens automatisch gesynchroniseerd en beschikbaar in het beheerde Azure AD DS-domein.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Implementaties van Extern bureaublad-bureaubladservices van Windows Server in Azure

U Azure AD DS gebruiken om beheerde domeinservices te leveren voor externe desktopservers die in Azure zijn geïmplementeerd. Zie voor meer informatie over dit implementatiescenario [hoe u Azure AD Domain Services integreert met uw RDS-implementatie.][windows-rds]

## <a name="domain-joined-hdinsight-clusters"></a>Met domein verbonden HDInsight-clusters

U een Azure HDInsight-cluster instellen dat is gekoppeld aan een door Azure AD DS beheerd domein met Apache Ranger ingeschakeld. U Hive-beleid maken en toepassen via Apache Ranger en gebruikers, zoals gegevenswetenschappers, in staat stellen verbinding te maken met Hive met behulp van ODBC-gebaseerde tools zoals Excel of Tableau. We blijven werken aan andere workloads, zoals HBase, Spark en Storm, aan hdinsight met domeinverbonden.

Zie voor meer informatie over dit implementatiescenario hoe u [hdInsight-clusters met domeinverbinding configureert][hdinsight]

## <a name="next-steps"></a>Volgende stappen

Een Azure Active Directory Domain Services-exemplaar maken en configureren om aan de slag te [gaan, een Azure Active Directory Domain Services-exemplaar maken en configureren][tutorial-create-instance]

<!-- INTERNAL LINKS -->
[hdinsight]: ../hdinsight/domain-joined/apache-domain-joined-configure.md
[tutorial-create-instance]: tutorial-create-instance.md

<!-- EXTERNAL LINKS -->
[windows-rds]: /windows-server/remote/remote-desktop-services/rds-azure-adds
