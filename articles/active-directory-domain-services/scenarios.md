---
title: Algemene implementatie scenario's voor Azure AD Domain Services | Microsoft Docs
description: Meer informatie over een aantal algemene scenario's en use-cases voor Azure Active Directory Domain Services om waarde te bieden en te voldoen aan de behoeften van uw bedrijf.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: iainfou
ms.openlocfilehash: 6f81bc2ccf11cbcc3621dc1149879864c88cf0cf
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69980505"
---
# <a name="common-use-cases-and-scenarios-for-azure-active-directory-domain-services"></a>Veelvoorkomend gebruik: cases en scenario's voor Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) biedt beheerde domein Services, zoals domein deelname, groeps beleid, LDAP en Kerberos/NTLM-verificatie. Azure AD DS is geïntegreerd met uw bestaande Azure AD-Tenant, waardoor gebruikers zich kunnen aanmelden met hun bestaande referenties. U gebruikt deze domein Services zonder dat u domein controllers in de Cloud hoeft te implementeren, beheren en te patchen. Dit biedt een soepelere lift-en-verschuiving van on-premises resources naar Azure.

In dit artikel vindt u een overzicht van een aantal algemene bedrijfs scenario's waarbij Azure AD DS een waarde biedt en aan die behoeften voldoet.

## <a name="secure-administration-of-azure-virtual-machines"></a>Veilig beheer van virtuele Azure-machines

Als u één set AD-referenties wilt gebruiken, kunnen virtuele Azure-machines (Vm's) worden toegevoegd aan een door Azure AD DS beheerd domein. Deze aanpak vermindert problemen met het beheer van referenties, zoals het onderhouden van lokale beheerders accounts op elke virtuele machine of het scheiden van accounts en wacht woorden tussen omgevingen.

Vm's die zijn gekoppeld aan een door Azure AD DS beheerd domein kunnen ook worden beheerd en beveiligd met behulp van groepsbeleid. Vereiste beveiligings basislijnen kunnen worden toegepast op Vm's om ze te vergren delen in overeenstemming met de beveiligings richtlijnen van het bedrijf. U kunt bijvoorbeeld de mogelijkheden van groeps beleids beheer gebruiken om de typen toepassingen te beperken die kunnen worden gestart op de virtuele machine.

![Gestroomlijnd beheer van virtuele Azure-machines](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Laten we eens kijken naar een gemeen schappelijk voorbeeld scenario. Als servers en andere infra structuur het einde van de levens duur bereiken, wil Contoso toepassingen die momenteel on-premises worden gehost, verplaatsen naar de Cloud. Het huidige IT-standaard mandaat dat servers die bedrijfs toepassingen hosten, moeten lid zijn van een domein en worden beheerd met groeps beleid. De IT-beheerder van Contoso zou de voor keur geven aan Vm's die in azure zijn geïmplementeerd om het beheer eenvoudiger te maken, terwijl gebruikers zich kunnen aanmelden met hun bedrijfs referenties. Bij het lid van een domein kunnen Vm's ook worden geconfigureerd om te voldoen aan de vereiste beveiligings basislijnen met behulp van groepsbeleid. Contoso zou het voor keur hebben om hun eigen domein controllers in azure te implementeren, te controleren en te beheren.

Azure AD DS is in het bijzonder geschikt voor deze gebruiks case. Met een beheerd domein van Azure AD DS kunt u virtuele machines koppelen, een enkele set referenties gebruiken en groeps beleid Toep assen. Als een beheerd domein hoeft u de domein controllers niet zelf te configureren en te onderhouden.

### <a name="deployment-notes"></a>Implementatie opmerkingen

De volgende overwegingen met betrekking tot de implementatie zijn van toepassing op dit voor beeld-Case:

* In azure AD DS Managed domains worden standaard één platte organisatie-eenheid (OE) gebruikt. Alle virtuele machines die lid zijn van een domein, bevinden zich in één organisatie-eenheid. U kunt desgewenst aangepaste organisatie-eenheden maken.
* Azure AD DS maakt gebruik van een ingebouwde GPO voor de containers gebruikers en computers. Voor extra besturings elementen kunt u aangepaste groeps beleidsobjecten maken en deze op aangepaste organisatie-eenheden richten.
* Azure AD DS ondersteunt het basis-schema voor AD-computer objecten. U kunt het schema van het computer object niet uitbreiden.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-bind-authentication"></a>On-premises toepassingen gebruiken die gebruikmaken van LDAP-bindings verificatie

Als voorbeeld scenario heeft Contoso een on-premises toepassing die veel jaren geleden van een ISV is gekocht. De toepassing bevindt zich momenteel in de onderhouds modus door de ISV en het aanvragen van wijzigingen aan de toepassing is prohibitively duur. Deze toepassing heeft een webfrontend waarmee gebruikers referenties worden verzameld met behulp van een webformulier en vervolgens gebruikers verifieert door een LDAP-binding uit te voeren op de on-premises AD DS omgeving.

![LDAP bind](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso wil deze toepassing migreren naar Azure. De toepassing moet blijven werken, zonder dat u wijzigingen hoeft aan te brengen. Daarnaast moeten gebruikers zich kunnen verifiëren met hun bestaande bedrijfs referenties en zonder extra training. Het moet transparant zijn voor eind gebruikers waarop de toepassing wordt uitgevoerd.

Voor dit scenario kunnen toepassingen met Azure AD DS LDAP-bindingen uitvoeren als onderdeel van het verificatie proces. Bestaande on-premises toepassingen kunnen in azure overstappen en verschuiven en kunnen gebruikers naadloos verifiëren zonder enige wijziging in de configuratie of gebruikers ervaring.

### <a name="deployment-notes"></a>Implementatie opmerkingen

De volgende overwegingen met betrekking tot de implementatie zijn van toepassing op dit voor beeld-Case:

* Zorg ervoor dat de toepassing niet aan de directory hoeft te wijzigen/schrijven. LDAP-schrijf toegang tot een beheerd domein van Azure AD DS wordt niet ondersteund.
* U kunt wacht woorden niet rechtstreeks wijzigen voor een beheerd domein van Azure AD DS. Eind gebruikers kunnen hun wacht woord wijzigen met behulp van de self-service voor wachtwoord wijziging van Azure AD of de on-premises Directory. Deze wijzigingen worden vervolgens automatisch gesynchroniseerd en beschikbaar in het door Azure AD DS beheerde domein.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-read-to-access-the-directory"></a>On-premises toepassingen die gebruikmaken van LDAP, gebruiken om toegang te krijgen tot de adres lijst

Net als bij het vorige voorbeeld scenario gaan we ervan uitgaan dat contoso een on-premises LOB-toepassing (line-of-Business) heeft die bijna een jaar geleden is ontwikkeld. Deze toepassing is Directory-Aware en is ontworpen voor het gebruik van LDAP (Lightweight Directory Access Protocol) om informatie/kenmerken over gebruikers van AD DS te lezen. De toepassing wijzigt geen kenmerken of op een andere manier naar de map schrijven.

Contoso wil deze toepassing migreren naar Azure en buiten gebruik stellen op de lokale hardware die momenteel als host fungeert voor deze toepassing. De toepassing kan niet worden herschreven voor het gebruik van moderne Directory-Api's, zoals de op REST gebaseerde Azure AD-Graph API. Er is een lift-en Shift-optie gewenst waarbij de toepassing kan worden gemigreerd om te worden uitgevoerd in de Cloud, zonder code te wijzigen of de toepassing te herschrijven.

Om u te helpen bij dit scenario kunnen toepassingen met Azure AD DS LDAP-lees bewerkingen uitvoeren op het beheerde domein om de benodigde kenmerk gegevens op te halen. De toepassing hoeft niet opnieuw te worden geschreven, dus met een lift-en-verschuiving in azure kunnen gebruikers de app blijven gebruiken zonder dat er sprake is van een wijziging in de locatie waar deze wordt uitgevoerd.

### <a name="deployment-notes"></a>Implementatie opmerkingen

De volgende overwegingen met betrekking tot de implementatie zijn van toepassing op dit voor beeld-Case:

* Zorg ervoor dat de toepassing niet aan de directory hoeft te wijzigen/schrijven. LDAP-schrijf toegang tot een beheerd domein van Azure AD DS wordt niet ondersteund.
* Zorg ervoor dat de toepassing geen aangepast/uitgebreid Active Directory schema nodig heeft. Schema-uitbrei dingen worden niet ondersteund in azure AD DS.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure"></a>Een on-premises service of daemon-toepassing migreren naar Azure

Sommige toepassingen bevatten meerdere lagen, waarbij een van de lagen een geauthenticeerde aanroep naar een backend-laag, zoals een Data Base, moet uitvoeren. AD-service accounts worden vaak gebruikt in deze scenario's. Wanneer u toepassingen in azure opneemt, kunt u met Azure AD DS de service accounts op dezelfde manier blijven gebruiken. U kunt ervoor kiezen om hetzelfde service account te gebruiken dat is gesynchroniseerd vanuit uw on-premises Directory naar Azure AD of een aangepaste organisatie-eenheid te maken en vervolgens een afzonderlijk service account in die organisatie-eenheid te maken. Met beide benaderingen blijven toepassingen op dezelfde manier werken om geverifieerde aanroepen naar andere lagen en services te maken.

![Service account met behulp van WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

In dit voorbeeld scenario heeft Contoso een aangepaste software kluis toepassing die een web-front-end, een SQL-Server en een back-end-FTP-server bevat. Geïntegreerde Windows-verificatie met Service accounts verifieert de web-front-end naar de FTP-server. De web-front-end is ingesteld om te worden uitgevoerd als een service account. De back-endserver is geconfigureerd om toegang te verlenen van het service account voor de web-front-end. Contoso wil niet dat hun eigen domein controller-Vm's in de cloud worden geïmplementeerd en beheerd om deze toepassing naar Azure te verplaatsen.

Voor dit scenario kunnen de servers die als host fungeren voor de web-front-end, SQL Server en de FTP-server worden gemigreerd naar Azure-Vm's en lid worden van een door Azure AD DS beheerd domein. De Vm's kunnen vervolgens hetzelfde service account in hun on-premises Directory gebruiken voor de verificatie doeleinden van de app, die wordt gesynchroniseerd via Azure AD met behulp van Azure AD Connect.

### <a name="deployment-notes"></a>Implementatie opmerkingen

De volgende overwegingen met betrekking tot de implementatie zijn van toepassing op dit voor beeld-Case:

* Zorg ervoor dat de toepassingen een gebruikers naam en wacht woord voor verificatie gebruiken. Verificatie op basis van een certificaat of Smart Card wordt niet ondersteund door Azure AD DS.
* U kunt wacht woorden niet rechtstreeks wijzigen voor een beheerd domein van Azure AD DS. Eind gebruikers kunnen hun wacht woord wijzigen met behulp van de self-service voor wachtwoord wijziging van Azure AD of de on-premises Directory. Deze wijzigingen worden vervolgens automatisch gesynchroniseerd en beschikbaar in het door Azure AD DS beheerde domein.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Windows Server-implementaties van extern bureau blad-Services in azure

U kunt Azure AD DS gebruiken om beheerde domein services te bieden aan extern bureau blad-servers die zijn geïmplementeerd in Azure. Zie [Azure AD Domain Services integreren met uw RDS-implementatie][windows-rds]voor meer informatie over dit implementatie scenario.

## <a name="domain-joined-hdinsight-clusters-preview"></a>HDInsight-clusters die zijn gekoppeld aan een domein (preview-versie)

U kunt een Azure HDInsight-cluster instellen dat is gekoppeld aan een door Azure AD DS beheerd domein waarvoor Apache zwerver is ingeschakeld. Deze functie is momenteel beschikbaar als preview-product. U kunt Hive-beleid maken en Toep assen via Apache zwerver, en gebruikers, zoals gegevens wetenschappers, toestaan verbinding te maken met hive met behulp van ODBC-hulpprogram ma's zoals Excel of tableau. We blijven werken om andere workloads, zoals HBase, Spark en Storm, toe te voegen aan HDInsight die lid is van een domein.

Zie [HDInsight-clusters die zijn gekoppeld aan een domein configureren][hdinsight] voor meer informatie over dit implementatie scenario.

## <a name="next-steps"></a>Volgende stappen

[Een Azure Active Directory Domain Services-exemplaar maken en configureren][tutorial-create-instance] om aan de slag te gaan

<!-- INTERNAL LINKS -->
[hdinsight]: ../hdinsight/domain-joined/apache-domain-joined-configure.md
[tutorial-create-instance]: tutorial-create-instance.md

<!-- EXTERNAL LINKS -->
[windows-rds]: /windows-server/remote/remote-desktop-services/rds-azure-adds
