---
title: Bedrijfsbeveiliging met Azure AD DS - Azure HDInsight
description: Meer informatie over het instellen en configureren van een HDInsight Enterprise Security Package-cluster met Azure Active Directory Domain Services.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18
ms.date: 02/03/2020
ms.openlocfilehash: cf239cbf69f3816e5ec03e07e2bd5fe370308f22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272836"
---
# <a name="enterprise-security-package-configurations-with-azure-active-directory-domain-services-in-hdinsight"></a>Configuraties van bedrijfsbeveiligingspakketten met Azure Active Directory Domain Services in HDInsight

ESP-clusters (Enterprise Security Package) bieden multiuser-toegang op Azure HDInsight-clusters. HDInsight-clusters met ESP zijn verbonden met een domein, zodat domeingebruikers hun domeinreferenties kunnen gebruiken om met de clusters te verifiëren en big data-taken uit te voeren.

In dit artikel leert u hoe u een HDInsight-cluster configureert met ESP met Azure Active Directory Domain Services (Azure AD DS).

> [!NOTE]  
> ESP is algemeen beschikbaar in HDInsight 3.6 en 4.0 voor deze clustertypen: Apache Spark, Interactive, Hadoop en HBase. ESP voor het clustertype Apache Kafka is in preview met alleen ondersteuning voor de beste inspanning. ESP-clusters die zijn gemaakt vóór de ESP GA-datum (1 oktober 2018) worden niet ondersteund.

## <a name="enable-azure-ad-ds"></a>Azure AD DS inschakelen

> [!NOTE]  
> Alleen tenantbeheerders hebben de bevoegdheden om Azure AD DS in te schakelen. Als de clusteropslag Azure Data Lake Storage Gen1 of Gen2 is, moet u Azure Multi-Factor Authentication alleen uitschakelen voor gebruikers die toegang moeten krijgen tot het cluster met behulp van basiskerberos-verificatie. 
>
> U [vertrouwde IP's](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) of [voorwaardelijke toegang alleen](../../active-directory/conditional-access/overview.md) gebruiken om multifactorauthenticatie voor specifieke gebruikers uit te schakelen wanneer ze toegang hebben tot het IP-bereik voor het virtuele netwerk van het HDInsight-cluster. *only* Als u Voorwaardelijke toegang gebruikt, controleert u of het eindpunt van de Active Directory-service is ingeschakeld in het virtuele HDInsight-netwerk.
>
> Als de clusteropslag Azure Blob-opslag is, schakelt u multifactorverificatie niet uit.

Het inschakelen van Azure AD DS is een vereiste voordat u een HDInsight-cluster met ESP maken. Zie [Azure Active Directory Domain Services inschakelen met behulp van de Azure-portal](../../active-directory-domain-services/tutorial-create-instance.md)voor meer informatie. 

Wanneer Azure AD DS is ingeschakeld, beginnen alle gebruikers en objecten standaard te synchroniseren van Azure Active Directory (Azure AD) naar Azure AD DS. De lengte van de synchronisatiebewerking is afhankelijk van het aantal objecten in Azure AD. De synchronisatie kan enkele dagen duren voor honderdduizenden objecten. 

De domeinnaam die u met Azure AD DS gebruikt, moet 39 tekens of minder zijn om met HDInsight te kunnen werken.

U ervoor kiezen om alleen de groepen te synchroniseren die toegang nodig hebben tot de HDInsight-clusters. Deze optie om alleen bepaalde groepen te synchroniseren, wordt *scoped synchronisatie*genoemd. Zie [Scopesynchronisatie configureren van Azure AD naar uw beheerde domein](../../active-directory-domain-services/scoped-synchronization.md)voor instructies.

Wanneer u beveiligde LDAP inschakelt, plaatst u de domeinnaam in de onderwerpnaam en de alternatieve naam van het onderwerp in het certificaat. Als uw domeinnaam bijvoorbeeld *contoso100.onmicrosoft.com*is, controleert u of de exacte naam bestaat in de naam van het certificaatonderwerp en de alternatieve naam van het onderwerp. Zie [Beveiligde LDAP configureren voor een door Azure AD DS beheerd domein voor](../../active-directory-domain-services/tutorial-configure-ldaps.md)meer informatie. 

In het volgende voorbeeld wordt een zelfondertekend certificaat gesmaakt. De domeinnaam *contoso100.onmicrosoft.com* is `Subject` in zowel `DnsName` (onderwerp naam) en (onderwerp alternatieve naam).

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>Status azure AD DS-status controleren
Bekijk de status van Azure Active Directory Domain Services door **Status** te selecteren in de categorie **Beheren.** Controleer of de status van Azure AD DS groen (actief) is en dat de synchronisatie is voltooid.

![Azure AD DS-status](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>Een beheerde identiteit maken en autoriseren

U een *door de gebruiker toegewezen beheerde identiteit* gebruiken om domeinservices te vereenvoudigen en te beveiligen. Wanneer u de rol **van HDInsight Domain Services-bijdrager aan** de beheerde identiteit toewijst, kan deze domeinservices lezen, maken, wijzigen en verwijderen. 

Bepaalde domeinservices, zoals het maken van OUs en serviceprincipals, zijn nodig voor HDInsight Enterprise Security Package. U beheerde identiteiten maken in elk abonnement. Zie [Beheerde identiteiten voor Azure-resources voor](../../active-directory/managed-identities-azure-resources/overview.md)meer informatie over beheerde identiteiten in het algemeen. Zie [Beheerde identiteiten in Azure HDInsight](../hdinsight-managed-identities.md)voor meer informatie over hoe beheerde identiteiten werken in Azure HDInsight.

Als u ESP-clusters wilt instellen, maakt u een door de gebruiker toegewezen beheerde identiteit als u die nog niet hebt. Zie Voor instructies [Een rol maken, aanbieden, verwijderen of toewijzen aan een door de gebruiker toegewezen beheerde identiteit met behulp van de Azure-portal.](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) 

Wijs vervolgens de rol **van HDInsight Domain Services Contributor** toe aan de beheerde identiteit in **Access-besturingselement** voor Azure AD DS. U hebt Azure AD DS-beheerdersrechten nodig om deze roltoewijzing uit te voeren.

![Azure Active Directory Domain Services Access-besturingselement](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Het toewijzen van de rol **VAN HDInsight Domain Services-bijdrager** zorgt ervoor dat deze identiteit de juiste (namens) toegang heeft tot het uitvoeren van domeinservices op het Azure AD DS-domein. Deze bewerkingen omvatten het maken en verwijderen van OU's.

Nadat de beheerde identiteit is gemaakt en de juiste rol heeft gekregen, kan de Azure AD DS-beheerder instellen wie deze beheerde identiteit kan gebruiken. De beheerder selecteert eerst de beheerde identiteit in de portal en selecteert vervolgens **Toegangsbeheer (IAM)** onder **Overzicht**. Vervolgens wijst de beheerder aan de rechterkant de rol **Managed Identity Operator** toe aan de gebruikers of groepen die HDInsight ESP-clusters willen maken. 

De Azure AD DS-beheerder kan deze rol bijvoorbeeld toewijzen aan de **MarketingTeam-groep** voor de **door SJMSI** beheerde identiteit, zoals in de volgende afbeelding wordt weergegeven. Deze opdracht zorgt ervoor dat de juiste mensen in de organisatie de beheerde identiteit kunnen gebruiken om ESP-clusters te maken.

![Roltoewijzing van hdinsight Managed Identity Operator](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="network-considerations"></a>Overwegingen voor het netwerk

> [!NOTE]  
> Azure AD DS moet worden geïmplementeerd in een virtueel azure-netwerk op basis van Azure Resource Manager. Klassieke virtuele netwerken worden niet ondersteund voor Azure AD DS. Zie [Azure Active Directory Domain Services inschakelen met behulp van de Azure-portal](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network)voor meer informatie.

Nadat u Azure AD DS hebt ingeschakeld, wordt een DNS-server (Local Domain Name System) uitgevoerd op de Virtuele Active Directory-machines (VM's). Configureer uw azure AD DS-virtuele netwerk om deze aangepaste DNS-servers te gebruiken. Als u de juiste IP-adressen wilt zoeken, selecteert u **Eigenschappen** in de categorie **Beheren** en kijkt u onder **IP-adres op virtueel netwerk**.

![IP-adressen zoeken voor lokale DNS-servers](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

Wijzig de configuratie van de DNS-servers in het virtuele Azure AD DS-netwerk om deze aangepaste IP's te gebruiken door **DNS-servers** te selecteren in de categorie **Instellingen.** Selecteer vervolgens de optie **Aangepast,** voer het eerste IP-adres in het tekstvak in en selecteer **Opslaan**. Voeg meer IP-adressen toe met dezelfde stappen.

![De DNS-configuratie van het virtuele netwerk bijwerken](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

Het is eenvoudiger om zowel het Azure AD DS-exemplaar als het HDInsight-cluster in hetzelfde Virtuele Azure-netwerk te plaatsen. Als u van plan bent verschillende virtuele netwerken te gebruiken, moet u deze virtuele netwerken peeren, zodat de domeincontroller zichtbaar is voor HDInsight-VM's. Zie [Virtueel netwerkpeeren](../../virtual-network/virtual-network-peering-overview.md)voor meer informatie. 

Nadat de virtuele netwerken zijn peered, configureert u het virtuele HDInsight-netwerk om een aangepaste DNS-server te gebruiken en voert u de privé-IP's van Azure AD DS in als dns-serveradressen. Wanneer beide virtuele netwerken dezelfde DNS-servers gebruiken, wordt uw aangepaste domeinnaam op het juiste IP-adres opgelost en is deze bereikbaar via HDInsight. Als uw domeinnaam `contoso.com`bijvoorbeeld na deze stap `ping contoso.com` wordt opgelost naar het juiste Azure AD DS-IP.

![Aangepaste DNS-servers configureren voor een virtueel netwerk met peered](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Als u NSG-regels (Network Security Group) gebruikt in uw HDInsight-subnet, moet u de [vereiste IP's](../hdinsight-management-ip-addresses.md) toestaan voor zowel inkomend als uitgaand verkeer.

Als u wilt testen of uw netwerk correct is ingesteld, sluit u een Windows-vm aan bij het virtuele hdinsight-netwerk/subnet en pingt u de domeinnaam. (Het moet oplossen tot een IP.) Voer **ldp.exe uit** om toegang te krijgen tot het Azure AD DS-domein. Sluit je vervolgens aan bij deze Windows VM in het domein om te bevestigen dat alle vereiste RPC-oproepen slagen tussen de client en de server. 

U **nslookup** ook gebruiken om de toegang tot het netwerk tot uw opslagaccount of een externe database die u gebruiken (bijvoorbeeld externe Hive-metastore of Ranger DB) te bevestigen. Zorg ervoor dat alle [vereiste poorten](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) zijn toegestaan in de NSG-regels van het Azure AD DS-subnet, als een NSG azure AD DS helpt beveiligen. Als de domeintoetreding van deze Windows VM succesvol is, u doorgaan naar de volgende stap en ESP-clusters maken.

## <a name="create-an-hdinsight-cluster-with-esp"></a>Een HDInsight-cluster maken met ESP

Nadat u de vorige stappen correct hebt ingesteld, is de volgende stap het maken van het HDInsight-cluster met ESP ingeschakeld. Wanneer u een HDInsight-cluster maakt, u het Enterprise Security Package inschakelen op het tabblad **Beveiliging + netwerken.** Als u liever een Azure Resource Manager-sjabloon gebruikt voor implementatie, gebruikt u de portalervaring één keer en downloadt u de vooraf ingevulde sjabloon op de pagina **Controleren + maken** voor toekomstig hergebruik. 

U de functie [HDInsight ID Broker](identity-broker.md) ook inschakelen tijdens het maken van het cluster. Met de functie ID Broker u zich aanmelden bij Ambari met behulp van Multi-Factor Authentication en de vereiste Kerberos-tickets krijgen zonder dat u wachtwoordhashes in Azure AD DS nodig hebt.

> [!NOTE]  
> De eerste zes tekens van de ESP-clusternamen moeten uniek zijn in uw omgeving. Als u bijvoorbeeld meerdere ESP-clusters in verschillende virtuele netwerken hebt, kiest u een naamgevingsconventie die ervoor zorgt dat de eerste zes tekens op de clusternamen uniek zijn.

![Domeinvalidatie voor Azure HDInsight Enterprise Security Package](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

Nadat u ESP hebt ingeschakeld, worden veelvoorkomende foutconfiguraties met betrekking tot Azure AD DS automatisch gedetecteerd en gevalideerd. Nadat u deze fouten hebt opgelost, u doorgaan met de volgende stap.

![Azure HDInsight Enterprise Security Package is mislukt voor domeinvalidatie](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

Wanneer u een HDInsight-cluster met ESP maakt, moet u de volgende parameters opgeven:

- **Clusterbeheergebruiker:** kies een beheerder voor uw cluster uit uw gesynchroniseerde AZURE AD DS-exemplaar. Dit domeinaccount moet al gesynchroniseerd en beschikbaar zijn in Azure AD DS.

- **Clustertoegangsgroepen:** de beveiligingsgroepen waarvan u de gebruikers wilt synchroniseren en toegang tot het cluster wilt hebben, moeten beschikbaar zijn in Azure AD DS. Een voorbeeld is de HiveUsers-groep. Zie [Een groep maken en leden toevoegen in Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)voor meer informatie.

- **LDAPS URL**: `ldaps://contoso.com:636`Een voorbeeld is .

De beheerde identiteit die u hebt gemaakt, kan worden gekozen uit de vervolgkeuzelijst **Beheerde identiteit** die door de gebruiker is toegewezen wanneer u een nieuw cluster maakt.

![Beheerde identiteit van Azure HDInsight ESP Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>Volgende stappen

* Zie [Apache Hive-beleid configureren voor HDInsight-clusters configureren met ESP](apache-domain-joined-run-hive.md)voor het configureren van Hive-beleid en het uitvoeren van Hive-query's.
* Zie [SSH gebruiken met Het op Linux gebaseerde Apache Hadoop op HDInsight van Linux, Unix of OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight)voor het gebruik van SSH om verbinding te maken met HDInsight-clusters.
