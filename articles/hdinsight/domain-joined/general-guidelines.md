---
title: Algemene richtlijnen voor bedrijfsbeveiliging in Azure HDInsight
description: Enkele aanbevolen procedures die de implementatie en het beheer van het Enterprise Security Package eenvoudiger moeten maken.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: be6c1fdc5deb6d541656c198469822dae0a5f7c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77463204"
---
# <a name="enterprise-security-general-information-and-guidelines-in-azure-hdinsight"></a>Algemene informatie en richtlijnen voor bedrijfsbeveiliging in Azure HDInsight

Bij het implementeren van een beveiligd HDInsight-cluster zijn er enkele aanbevolen procedures die de implementatie en het clusterbeheer eenvoudiger moeten maken. Enkele algemene informatie en richtlijnen worden hier besproken.

## <a name="use-of-secure-cluster"></a>Gebruik van beveiligde cluster

### <a name="recommended"></a>Aanbevolen

* Cluster zal worden gebruikt door meerdere gebruikers op hetzelfde moment.
* Gebruikers hebben verschillende niveaus van toegang tot dezelfde gegevens.

### <a name="not-necessary"></a>Niet nodig

* U gaat alleen geautomatiseerde taken uitvoeren (zoals een account voor één gebruiker), een standaardcluster is goed genoeg.
* U de gegevens importeren met behulp van een standaardcluster en hetzelfde opslagaccount gebruiken op een ander beveiligd cluster waar gebruikers analysetaken kunnen uitvoeren.

## <a name="use-of-local-account"></a>Gebruik van lokale account

* Als u een gedeeld gebruikersaccount of een lokaal account gebruikt, is het moeilijk te bepalen wie het account heeft gebruikt om de config of service te wijzigen.
* Het gebruik van lokale accounts is problematisch wanneer gebruikers geen deel meer uitmaken van de organisatie.

## <a name="ranger"></a>Ranger

### <a name="policies"></a>Beleidsregels

* Ranger gebruikt **Deny** standaard als beleid.

* Wanneer toegang tot gegevens wordt gemaakt via een service waarbij autorisatie is ingeschakeld:
  * Ranger autorisatie plugin wordt ingeroepen en gezien de context van het verzoek.
  * Ranger past het beleid toe dat is geconfigureerd voor de service. Als het Beleid van de Ranger mislukt, wordt de toegangscontrole uitgesteld naar het bestandssysteem. Sommige services zoals MapReduce controleren alleen of het bestand /map eigendom is van dezelfde gebruiker die de aanvraag indient. Services zoals Hive, controleer op eigendomsovereenkomst of`rwx`de juiste bestandssysteemmachtigingen ( ).

* Voor Hive moet de gebruiker naast de machtigingen voor het maken / `rwx`bijwerken / verwijderen machtigingen hebben, ook machtigingen hebben op de map over opslag en alle submappen.

* Beleid kan worden toegepast op groepen (bij voorkeur) in plaats van individuen.

* Ranger authorizer zal alle Ranger-beleidsregels voor die service voor elke aanvraag evalueren. Deze evaluatie kan van invloed zijn op de tijd die nodig is om de taak of query te accepteren.

### <a name="storage-access"></a>Opslagtoegang

* Als het opslagtype WASB is, is er geen OAuth-token betrokken.
* Als Ranger de autorisatie heeft uitgevoerd, wordt de opslagtoegang uitgevoerd met de beheerde identiteit.
* Als Ranger geen autorisatie heeft uitgevoerd, wordt de opslagtoegang uitgevoerd met het OAuth-token van de gebruiker.

### <a name="hierarchical-name-space"></a>Hiërarchische naamruimte

Wanneer hiërarchische naamruimte niet is ingeschakeld:

* Er zijn geen overgenomen machtigingen.
* De enige bestandssysteemmachtiging die werkt, is de XXXX RBAC-rol **opslaggegevens,** die rechtstreeks in azure-portal aan de gebruiker moet worden toegewezen.

### <a name="default-hdfs-permissions"></a>StandaardhDFS-machtigingen

* Gebruikers hebben standaard geen toegang tot **/** de map op HDFS (ze moeten in de rol van de opslagblob-eigenaar staan om toegang te krijgen om te slagen).
* Voor de map met tijdelijke bestanden voor mapreduce en `sticky _wx` anderen wordt een gebruikersspecifieke map gemaakt en machtigingen verstrekt. Gebruikers kunnen eronder bestanden en mappen maken, maar kunnen niet naar andere items kijken.

### <a name="url-auth"></a>URL auth

Als de url auth is ingeschakeld:

* De config zal bevatten welke voorvoegsels zijn `adl://`bedekt in de url auth (zoals ).
* Als de toegang voor deze url is, controleert Ranger of de gebruiker in de lijst met toegestane gegevens staat.
* Ranger zal geen van de fijnkorrelige beleid controleren.

## <a name="resource-groups"></a>Resourcegroepen

Gebruik een nieuwe brongroep voor elk cluster, zodat u onderscheid maken tussen clusterbronnen.

## <a name="nsgs-firewalls-and-internal-gateway"></a>NSGs, firewalls en interne gateway

* Gebruik netwerkbeveiligingsgroepen (NSG's) om virtuele netwerken te vergrendelen.
* Gebruik firewall om uitgaande toegangsbeleidsregels te verwerken.
* Gebruik de interne gateway die niet open is voor het openbare internet.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) is de cloudgebaseerde service voor identiteits- en toegangsbeheer van Microsoft.

### <a name="policies"></a>Beleidsregels

* Schakel beleid voor voorwaardelijke toegang uit met behulp van het beleid op basis van IP-adres. Dit vereist dat serviceeindpunten worden ingeschakeld op de VNET's waar de clusters worden geïmplementeerd. Als u een externe service voor MFA gebruikt (iets anders dan AAD), werkt het ip-adresbeleid niet

* `AllowCloudPasswordValidation`beleid is vereist voor federatieve gebruikers. Aangezien HDInsight de gebruikersnaam /wachtwoord rechtstreeks gebruikt om tokens van Azure AD te krijgen, moet dit beleid worden ingeschakeld voor alle federatieve gebruikers.

* Serviceeindpunten inschakelen als u een omleiding voor voorwaardelijke toegang nodig hebt met vertrouwde IP's.

### <a name="groups"></a>Groepen

* Implementeer altijd clusters met een groep.
* Gebruik Azure AD om groepslidmaatschappen te beheren (eenvoudiger dan proberen de afzonderlijke services in het cluster te beheren).

### <a name="user-accounts"></a>Gebruikersaccounts

* Gebruik voor elk scenario een uniek gebruikersaccount. Gebruik bijvoorbeeld een account voor importeren, gebruik een andere voor query- of andere verwerkingstaken.
* Gebruik op groepen gebaseerd Ranger-beleid in plaats van individueel beleid.
* Heb een plan voor het beheren van gebruikers die geen toegang meer zouden moeten hebben tot clusters.

## <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services

[Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md) (Azure AD DS) biedt beheerde domeinservices zoals domeinjoin, groepsbeleid, LDAP (Lightweight Directory Access Protocol) en Kerberos / NTLM-verificatie die volledig compatibel is met Windows Server Active Directory.

Azure AD DS is vereist voor beveiligde clusters om lid te worden van een domein.
HDInsight kan niet afhankelijk zijn van on-premise domeincontrollers of aangepaste domeincontrollers, omdat het te veel foutpunten, het delen van referenties, DNS-machtigingen enzovoort introduceert. Zie [veelgestelde vragen van Azure AD DS voor](../../active-directory-domain-services/faqs.md)meer informatie .

### <a name="azure-ad-ds-instance"></a>Azure AD DS-exemplaar

* De instantie maken `.onmicrosoft.com domain`met de . Op deze manier zijn er niet meerdere DNS-servers die het domein bedienen.
* Maak een zelfondertekend certificaat voor de LDAPS en upload het naar Azure AD DS.
* Gebruik een peered virtueel netwerk voor het implementeren van clusters (wanneer u een aantal teams hebt die HDInsight ESP-clusters implementeren, is dit handig). Dit zorgt ervoor dat u geen poorten (NSG's) hoeft te openen op het virtuele netwerk met domeincontroller.
* Configureer de DNS voor het virtuele netwerk correct (de Azure AD DS-domeinnaam moet worden opgelost zonder dat er bestandsvermeldingen zijn ingeschakeld).
* Als u uitgaand verkeer beperkt, moet u ervoor zorgen dat u de [firewallondersteuning in HDInsight](../hdinsight-restrict-outbound-traffic.md) hebt gelezen

### <a name="properties-synced-from-azure-ad-to-azure-ad-ds"></a>Eigenschappen die zijn gesynchroniseerd van Azure AD naar Azure AD DS

* Azure AD connect synchroniseert van on-premise met Azure AD.
* Azure AD DS synchroniseert vanuit Azure AD.

Azure AD DS synchroniseert objecten van Azure AD periodiek. Het Azure AD DS-blad op de Azure-portal geeft de synchronisatiestatus weer. Tijdens elke fase van synchronisatie kunnen unieke eigenschappen in conflict raken en een andere naam krijgen. Besteed aandacht aan de toewijzing van de eigenschap van Azure AD naar Azure AD DS.

Zie Azure [AD UserPrincipalName-populatie](../../active-directory/hybrid/plan-connect-userprincipalname.md)en [hoe Azure AD DS-synchronisatie werkt](../../active-directory-domain-services/synchronization.md)voor meer informatie .

### <a name="password-hash-sync"></a>Wachtwoordhashsynchronisatie

* Wachtwoorden worden anders gesynchroniseerd dan andere objecttypen. Alleen niet-omkeerbare wachtwoordhashes worden gesynchroniseerd in Azure AD en Azure AD DS
* On-premise naar Azure AD moet worden ingeschakeld via AD Connect
* Azure AD naar Azure AD DS-synchronisatie is automatisch (latencies zijn minder dan 20 minuten).
* Wachtwoordhashes worden alleen gesynchroniseerd als er een gewijzigd wachtwoord is. Wanneer u wachtwoordhashsynchronisatie inschakelt, worden alle bestaande wachtwoorden niet automatisch gesynchroniseerd omdat ze onomkeerbaar worden opgeslagen. Wanneer u het wachtwoord wijzigt, worden wachtwoordhashes gesynchroniseerd.

### <a name="computer-objects-location"></a>Locatie computerobjecten

Elk cluster is gekoppeld aan één enkele organisatie. Een interne gebruiker is ingericht in de ORGANISATIE. Alle knooppunten zijn domein samengevoegd in dezelfde ou.

### <a name="active-directory-administrative-tools"></a>Beheerhulpprogramma's voor Active Directory

Zie [Hulpprogramma's](../../active-directory-domain-services/tutorial-create-management-vm.md)voor beheer van de installatie voor stappen voor het installeren van de beheerhulpprogramma's voor Active Directory op een Windows Server-vm.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="cluster-creation-fails-repeatedly"></a>Clustercreatie mislukt herhaaldelijk

Meest voorkomende redenen:

* DNS-configuratie is niet correct, domeinverzameling van clusterknooppunten mislukt.
* NSG's zijn te beperkend, waardoor domeinjoin wordt voorkomen.
* Managed Identity heeft niet voldoende machtigingen.
* Clusternaam is niet uniek voor de eerste zes tekens (met een ander livecluster of met een verwijderd cluster).

## <a name="next-steps"></a>Volgende stappen

* [Configuraties van bedrijfsbeveiligingspakketten met Azure Active Directory Domain Services in HDInsight](./apache-domain-joined-configure-using-azure-adds.md)

* [Azure Active Directory-gebruikers synchroniseren met een HDInsight-cluster.](../hdinsight-sync-aad-users-to-cluster.md)
