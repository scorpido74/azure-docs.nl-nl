---
title: Enter prise Security met Azure AD DS-Azure HDInsight
description: Meer informatie over het instellen en configureren van een HDInsight-Enterprise Security Package cluster met behulp van Azure Active Directory Domain Services.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18
ms.date: 10/02/2019
ms.openlocfilehash: 01de2173ee5d55f24f97e1d057e96d93a56c2af0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435977"
---
# <a name="enterprise-security-package-configurations-with-azure-active-directory-domain-services-in-hdinsight"></a>Enterprise Security Package configuraties met Azure Active Directory Domain Services in HDInsight

Enterprise Security Package-clusters (ESP) bieden toegang op meerdere gebruikers in azure HDInsight-clusters. HDInsight-clusters met ESP zijn verbonden met een domein, zodat domein gebruikers hun domein referenties kunnen gebruiken om zich te verifiëren bij de clusters en big data-taken uit te voeren.

In dit artikel leert u hoe u een HDInsight-cluster met ESP kunt configureren met behulp van Azure Active Directory Domain Services (Azure AD DS).

> [!NOTE]  
> ESP is algemeen beschikbaar in HDInsight 3,6 en 4,0 voor deze cluster typen: Apache Spark, Interactive, Hadoop en HBase. ESP voor het Apache Kafka-cluster type wordt in Preview weer gegeven met de best mogelijke ondersteuning. ESP-clusters die zijn gemaakt vóór de ESP-GA-datum (1 oktober 2018) worden niet ondersteund.

## <a name="enable-azure-ad-ds"></a>Azure AD DS inschakelen

> [!NOTE]  
> Alleen Tenant beheerders hebben de bevoegdheid om Azure AD DS in te scha kelen. Als de cluster opslag is Azure Data Lake Storage Gen1 of Gen2, moet u Azure Multi-Factor Authentication alleen uitschakelen voor gebruikers die toegang moeten hebben tot het cluster met behulp van basis-Kerberos-authenticatie. 
>
> U kunt met behulp van [vertrouwde IP-adressen](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) of [voorwaardelijke toegang](../../active-directory/conditional-access/overview.md) multi-factor Authentication uitschakelen voor *specifieke gebruikers wanneer* ze toegang hebben tot het IP-bereik voor het virtuele netwerk van het HDInsight-cluster. Als u voorwaardelijke toegang gebruikt, zorgt u ervoor dat het eind punt van de Active Directory service in ingeschakeld is op het virtuele HDInsight-netwerk.
>
> Als de cluster opslag Azure Blob-opslag is, moet u Multi-Factor Authentication niet uitschakelen.

Het inschakelen van Azure AD DS is een vereiste voordat u een HDInsight-cluster met ESP kunt maken. Zie [Azure Active Directory Domain Services inschakelen met behulp van de Azure Portal](../../active-directory-domain-services/tutorial-create-instance.md)voor meer informatie. 

Als Azure AD DS is ingeschakeld, beginnen alle gebruikers en objecten standaard met het synchroniseren van Azure Active Directory (Azure AD) naar Azure AD DS. De lengte van de synchronisatie bewerking is afhankelijk van het aantal objecten in azure AD. De synchronisatie kan enkele dagen duren voor honderd duizenden objecten. 

De domein naam die u met Azure AD DS gebruikt, moet 39 tekens of minder zijn om met HDInsight te kunnen werken.

U kunt ervoor kiezen om alleen de groepen te synchroniseren die toegang nodig hebben tot de HDInsight-clusters. Deze optie om alleen bepaalde groepen te synchroniseren, wordt *scoped Synchronization*genoemd. Zie voor instructies [scoped Synchronization from Azure AD configureren voor uw beheerde domein](../../active-directory-domain-services/scoped-synchronization.md).

Wanneer u beveiligd LDAP inschakelt, plaatst u de domein naam in de onderwerpnaam en de alternatieve naam voor het onderwerp in het certificaat. Als uw domein naam bijvoorbeeld *contoso100.onmicrosoft.com*is, zorgt u ervoor dat de naam van de certificaat houder en de alternatieve naam van het onderwerp bestaat. Zie [Configure secure LDAP for a Azure AD DS Managed Domain](../../active-directory-domain-services/tutorial-configure-ldaps.md)(Engelstalig) voor meer informatie. 

In het volgende voor beeld wordt een zelfondertekend certificaat gemaakt. De domein naam *contoso100.onmicrosoft.com* bevindt zich in `Subject` (object naam) en `DnsName` (alternatieve naam voor het onderwerp).

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>De status van Azure AD DS controleren
Bekijk de status van Azure Active Directory Domain Services door de **status** te selecteren in de categorie **beheren** . Zorg ervoor dat de status van de Azure-AD DS groen is (wordt uitgevoerd) en de synchronisatie is voltooid.

![Status van Azure AD DS](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>Een beheerde identiteit maken en autoriseren

U kunt een door de *gebruiker toegewezen beheerde identiteit* gebruiken om de bewerkingen voor Domain Services te vereenvoudigen en te helpen beveiligen. Wanneer u de functie voor het beheren van de **HDInsight Domain Services** -rol toewijst aan de beheerde identiteit, kan deze Domain Services-bewerkingen lezen, maken, wijzigen en verwijderen. 

Bepaalde Domain Services-bewerkingen, zoals het maken van organisatie-eenheden en service-principals, zijn vereist voor HDInsight-Enterprise Security Package. U kunt in elk abonnement beheerde identiteiten maken. Zie [beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over beheerde identiteiten in het algemeen. Zie [beheerde identiteiten in azure hdinsight](../hdinsight-managed-identities.md)voor meer informatie over de werking van beheerde identiteiten in azure hdinsight.

Als u ESP-clusters wilt instellen, moet u een door de gebruiker toegewezen beheerde identiteit maken als u er nog geen hebt. Zie voor instructies [een rol maken, weer geven, verwijderen of toewijzen aan een door de gebruiker toegewezen beheerde identiteit met behulp van de Azure Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 

Wijs vervolgens de rol van **HDInsight Domain Services-Inzender** toe aan de beheerde identiteit in **toegangs beheer** voor Azure AD DS. U hebt Azure AD DS-beheerders bevoegdheden nodig om deze roltoewijzing te kunnen maken.

![Toegangs beheer Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Als u de functie voor het toewijzen van de **HDInsight Domain Services-Inzender** hebt, zorgt u ervoor dat deze identiteit de juiste (namens) toegang heeft om Domain Services-bewerkingen uit te voeren op het Azure AD DS-domein. Deze bewerkingen zijn onder andere het maken en verwijderen van organisatie-eenheden.

Nadat de beheerde identiteit is gemaakt en de juiste rol heeft gekregen, kan de Azure AD DS-beheerder instellen wie deze beheerde identiteit kan gebruiken. Eerst selecteert de beheerder de beheerde identiteit in de portal en selecteert vervolgens **Access Control (IAM)** in het **overzicht**. Aan de rechter kant wijst de beheerder de rol **Managed Identity-operator** toe aan de gebruikers of groepen die HDInsight ESP-clusters willen maken. 

De Azure AD DS-beheerder kan bijvoorbeeld deze rol toewijzen aan de groep **MarketingTeam** voor de beheerde identiteit **sjmsi** , zoals weer gegeven in de volgende afbeelding. Deze toewijzing zorgt ervoor dat de juiste personen in de organisatie de beheerde identiteit kunnen gebruiken om ESP-clusters te maken.

![Roltoewijzing beheerde identiteits operator voor HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="network-considerations"></a>Aandachtspunten voor netwerken

> [!NOTE]  
> Azure AD DS moet worden geïmplementeerd in een op Azure Resource Manager gebaseerd virtueel netwerk. Klassieke virtuele netwerken worden niet ondersteund voor Azure-AD DS. Zie [Azure Active Directory Domain Services inschakelen met behulp van de Azure Portal](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network)voor meer informatie.

Nadat u Azure AD DS hebt ingeschakeld, wordt een lokale Domain Name System (DNS)-server uitgevoerd op de Active Directory virtuele machines (Vm's). Configureer uw Azure AD DS virtuele netwerk voor het gebruik van deze aangepaste DNS-servers. Als u de juiste IP-adressen wilt vinden, selecteert u **Eigenschappen** in de categorie **beheren** en kijkt u onder **IP-adres op virtueel netwerk**.

![IP-adressen voor lokale DNS-servers zoeken](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

Wijzig de configuratie van de DNS-servers in het virtuele netwerk van Azure AD DS om deze aangepaste IP-adressen te gebruiken door **DNS-servers** te selecteren in de categorie **instellingen** . Vervolgens selecteert u de optie **aangepast** , voert u het eerste IP-adres in het tekstvak in en selecteert u **Opslaan**. Voeg meer IP-adressen toe aan de hand van dezelfde stappen.

![De DNS-configuratie van het virtuele netwerk bijwerken](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

Het is eenvoudiger om zowel het Azure AD DS-exemplaar als het HDInsight-cluster in hetzelfde virtuele Azure-netwerk te plaatsen. Als u andere virtuele netwerken wilt gebruiken, moet u deze virtuele netwerken koppelen zodat de domein controller zichtbaar is voor HDInsight Vm's. Zie [peering van virtuele netwerken](../../virtual-network/virtual-network-peering-overview.md)voor meer informatie. 

Nadat de virtuele netwerken zijn gekoppeld, configureert u het virtuele HDInsight-netwerk voor het gebruik van een aangepaste DNS-server en voert u de Azure AD DS privé Ip's in als de DNS-server adressen. Als beide virtuele netwerken dezelfde DNS-servers gebruiken, wordt uw aangepaste domein naam omgezet in het juiste IP-adres en is deze bereikbaar vanaf HDInsight. Als uw domein naam bijvoorbeeld `contoso.com`is, moet `ping contoso.com` na deze stap worden omgezet naar het juiste Azure AD DS IP-adres.

![Aangepaste DNS-servers configureren voor een gekoppeld virtueel netwerk](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Als u regels voor netwerk beveiligings groepen (NSG) in uw HDInsight-subnet gebruikt, moet u de [vereiste ip's](../hdinsight-management-ip-addresses.md) toestaan voor zowel binnenkomend als uitgaand verkeer.

Als u wilt testen of uw netwerk correct is ingesteld, voegt u een virtuele Windows-machine toe aan het HDInsight-netwerk/subnet en pingt u de domein naam. (Het moet worden omgezet in een IP-adres.) Voer **Ldp. exe** uit om toegang te krijgen tot het Azure AD DS-domein. Voeg deze Windows-VM vervolgens toe aan het domein om te bevestigen dat alle vereiste RPC-aanroepen tussen de client en de server zijn geslaagd. 

U kunt **nslookup** ook gebruiken om de netwerk toegang tot uw opslag account of een externe data base te bevestigen die u mogelijk gebruikt (bijvoorbeeld externe Hive-metastore of zwerver DB). Zorg ervoor dat alle [vereiste poorten](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) zijn toegestaan in de NSG-regels van het Azure AD DS-subnet, als een NSG helpt bij het beveiligen van Azure AD DS. Als het domein dat lid is van deze Windows-VM is voltooid, kunt u door gaan met de volgende stap en ESP-clusters maken.

## <a name="create-an-hdinsight-cluster-with-esp"></a>Een HDInsight-cluster maken met ESP

Nadat u de vorige stappen op de juiste manier hebt ingesteld, is de volgende stap het maken van het HDInsight-cluster met ESP ingeschakeld. Wanneer u een HDInsight-cluster maakt, kunt u Enterprise Security Package inschakelen op het tabblad **beveiliging en netwerk** . Als u liever een Azure Resource Manager-sjabloon gebruikt voor implementatie, gebruikt u de portal ervaring eenmaal en downloadt u de vooraf opgestelde sjabloon op de pagina **evalueren en maken** voor toekomstig gebruik. 

U kunt de functie [HDINSIGHT id Broker](identity-broker.md) ook inschakelen tijdens het maken van het cluster. Met de functie ID Broker kunt u zich aanmelden bij Ambari met behulp van Multi-Factor Authentication en de vereiste Kerberos-tickets ophalen zonder dat wacht woord-hashes nodig zijn in azure AD DS.

> [!NOTE]  
> De eerste zes tekens van de ESP-cluster namen moeten uniek zijn in uw omgeving. Als u bijvoorbeeld meerdere ESP-clusters in verschillende virtuele netwerken hebt, kiest u een naam Conventie die ervoor zorgt dat de eerste zes tekens op de cluster namen uniek zijn.

![Domein validatie voor Azure HDInsight Enterprise Security Package](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

Nadat u ESP hebt ingeschakeld, worden veelvoorkomende onjuiste configuratie-items die betrekking hebben op Azure AD DS automatisch gedetecteerd en gevalideerd. Nadat u deze fouten hebt opgelost, kunt u door gaan met de volgende stap.

![Domein validatie van Azure HDInsight Enterprise Security Package is mislukt](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

Wanneer u een HDInsight-cluster met ESP maakt, moet u de volgende para meters opgeven:

- **Gebruiker van Cluster beheerder**: Kies een beheerder voor uw cluster uit uw gesynchroniseerde Azure AD DS-exemplaar. Dit domein account moet al worden gesynchroniseerd en beschikbaar zijn in azure AD DS.

- **Cluster toegangs groepen**: de beveiligings groepen waarvan u de gebruikers wilt synchroniseren en die toegang hebben tot het cluster, moeten beschikbaar zijn in azure AD DS. Een voor beeld is de groep HiveUsers. Zie [een groep maken en leden toevoegen in azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)voor meer informatie.

- **URL van LDAPS**: een voor beeld is `ldaps://contoso.com:636`.

De beheerde identiteit die u hebt gemaakt, kan worden gekozen in de vervolg keuzelijst door de **gebruiker toegewezen beheerde identiteit** wanneer u een nieuw cluster maakt.

![Beheerde identiteit van Azure HDInsight ESP Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>Volgende stappen

* Zie [configure Apache Hive policies for HDInsight clusters with ESP](apache-domain-joined-run-hive.md)(Engelstalig) voor het configureren van Hive-beleid en het uitvoeren van Hive-query's.
* Zie [SSH gebruiken met Apache Hadoop op basis van Linux in hdinsight via Linux, UNIX of OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)voor het gebruik van SSH om verbinding te maken met hdinsight-clusters met ESP.
