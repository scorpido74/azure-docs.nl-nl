---
title: Bedrijfs beveiliging met Azure AD-Azure HDInsight
description: Meer informatie over het instellen en configureren van een HDInsight-Enterprise Security Package cluster met behulp van Azure Active Directory Domain Services.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18
ms.date: 10/02/2019
ms.openlocfilehash: d31de590d8dc40ff3d5c1dfab060ee4f045ba4d1
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044670"
---
# <a name="enterprise-security-package-configurations-with-azure-active-directory-domain-services-in-hdinsight"></a>Enterprise Security Package configuraties met Azure Active Directory Domain Services in HDInsight

Enterprise Security Package-clusters (ESP) bieden toegang van meerdere gebruikers op Azure HDInsight-clusters. HDInsight-clusters met ESP zijn verbonden met een domein, zodat domein gebruikers hun domein referenties kunnen gebruiken om zich te verifiëren bij de clusters en big data-taken uit te voeren.

In dit artikel leert u hoe u een HDInsight-cluster met ESP kunt configureren met behulp van Azure Active Directory Domain Services (Azure AD DS).

> [!NOTE]  
> ESP is algemeen beschikbaar in HDInsight 3,6 en 4,0 voor cluster typen: Apache Spark, Interactive, Hadoop en HBase. ESP voor Apache Kafka cluster type is in Preview met best mogelijke ondersteuning. ESP-clusters die zijn gemaakt vóór ESP-GA-datum (1 oktober 2018) worden niet ondersteund.

## <a name="enable-azure-ad-ds"></a>Azure AD-DS inschakelen

> [!NOTE]  
> Alleen Tenant beheerders hebben de bevoegdheid om Azure AD-DS in te scha kelen. Als de cluster opslag is Azure Data Lake Storage (ADLS) gen1 of Gen2, moet u Multi-Factor Authentication (MFA) alleen uitschakelen voor gebruikers die toegang moeten hebben tot het cluster met behulp van basis-Kerberos-verificaties. U kunt met behulp van vertrouwde IP- [adressen](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) of [voorwaardelijke toegang](../../active-directory/conditional-access/overview.md) voor specifieke gebruikers alleen MFA uitschakelen als ze toegang hebben tot het VNET-IP-bereik van het HDInsight-cluster. Als u voorwaardelijke toegang gebruikt, moet u ervoor zorgen dat het AD-service-eind punt is ingeschakeld in het HDInsight-VNET.
>
> Als de cluster opslag Azure Blob Storage (WASB) is, moet u MFA niet uitschakelen.

Het inschakelen van AzureAD-DS is een vereiste voordat u een HDInsight-cluster met ESP kunt maken. Zie [Azure Active Directory Domain Services inschakelen met behulp van de Azure Portal](../../active-directory-domain-services/tutorial-create-instance.md)voor meer informatie. 

Als Azure AD-DS is ingeschakeld, beginnen alle gebruikers en objecten standaard met het synchroniseren van Azure Active Directory (AAD) naar Azure AD DS. De lengte van de synchronisatie bewerking is afhankelijk van het aantal objecten in azure AD. De synchronisatie kan enkele dagen duren voor honderd duizenden objecten. 

De domein naam die u met Azure AD-DS gebruikt, mag Maxi maal 39 tekens lang zijn, om te kunnen werken met HDInsight.

U kunt ervoor kiezen om alleen de groepen te synchroniseren die toegang nodig hebben tot de HDInsight-clusters. Deze optie om alleen bepaalde groepen te synchroniseren, wordt *scoped Synchronization*genoemd. Zie [de scoped Synchronization from Azure AD configureren voor instructies in uw beheerde domein](../../active-directory-domain-services/scoped-synchronization.md) .

Wanneer u beveiligd LDAP inschakelt, plaatst u de domein naam in de onderwerpnaam en de alternatieve naam voor het onderwerp in het certificaat. Als uw domein naam bijvoorbeeld *contoso100.onmicrosoft.com*is, zorgt u ervoor dat de naam van de certificaat houder en de alternatieve naam van het onderwerp bestaat. Zie [secure LDAP configureren voor een beheerd domein van Azure AD-DS](../../active-directory-domain-services/tutorial-configure-ldaps.md)voor meer informatie. Hieronder ziet u een voor beeld van het maken van een zelfondertekend certificaat en de domein naam (*contoso100.onmicrosoft.com*) in de onderwerpnaam en DnsName (alternatieve naam voor het onderwerp):

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>De Azure AD-DS-status controleren
Bekijk de status van uw Azure Active Directory Domain Services door de **status** te selecteren onder de categorie **beheren** . Zorg ervoor dat de status van Azure AD-DS groen is (wordt uitgevoerd) en de synchronisatie is voltooid.

![Azure Active Directory Domain Services status](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>Een beheerde identiteit maken en autoriseren

Een door de **gebruiker toegewezen beheerde identiteit** wordt gebruikt om Domain Services-bewerkingen te vereenvoudigen en te beveiligen. Wanneer u de functie voor het beheren van de HDInsight Domain Services-rol toewijst aan de beheerde identiteit, kan deze Domain Services-bewerkingen lezen, maken, wijzigen en verwijderen. Bepaalde Domain Services-bewerkingen, zoals het maken van organisatie-eenheden en service-principals, zijn nodig voor de HDInsight-Enterprise Security Package. Beheerde identiteiten kunnen in elk abonnement worden gemaakt. Zie [beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over beheerde identiteiten in het algemeen. Zie [beheerde identiteiten in azure hdinsight](../hdinsight-managed-identities.md)voor meer informatie over de werking van beheerde identiteiten in azure hdinsight.

Als u ESP-clusters wilt instellen, moet u een door de gebruiker toegewezen beheerde identiteit maken als u er nog geen hebt. Zie een [rol maken, weer geven, verwijderen of toewijzen aan een door de gebruiker toegewezen beheerde identiteit met behulp van de Azure Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) voor instructies. Wijs vervolgens de rol van **HDInsight Domain Services-Inzender** toe aan de beheerde identiteit in azure AD-DS Access Control (Aad-DS-beheer bevoegdheden zijn vereist om deze roltoewijzing te maken).

![Toegangs beheer Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Als u de functie voor het toewijzen van de **HDInsight Domain Services-Inzender** hebt, zorgt u ervoor dat deze identiteit gemachtigd is om Domain Services-bewerkingen uit te voeren, zoals het maken van organisatie-eenheden, het verwijderen van organisatie-eenheden, enzovoort op het Aad-DS-domein.

Zodra de beheerde identiteit is gemaakt en de juiste rol heeft gekregen, kan de AAD-DS-beheerder instellen wie deze beheerde identiteit mag gebruiken. Als u gebruikers wilt instellen voor de beheerde identiteit, moet de beheerder de beheerde identiteit selecteren in de portal en vervolgens op **Access Control (IAM)** onder **overzicht**klikken. Wijs vervolgens aan de rechter kant de rol **Managed Identity-operator** toe aan de gebruikers of groepen die HDInsight ESP-clusters willen maken. De AAD-DS-beheerder kan deze rol bijvoorbeeld toewijzen aan de groep **MarketingTeam** voor de beheerde identiteit **sjmsi** , zoals weer gegeven in de volgende afbeelding. Dit zorgt ervoor dat de juiste personen in de organisatie toegang hebben tot het gebruik van deze beheerde identiteit voor het maken van ESP-clusters.

![Roltoewijzing beheerde identiteits operator voor HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="networking-considerations"></a>Aandachtspunten voor netwerken

> [!NOTE]  
> Azure AD-DS moet worden geïmplementeerd in een op Azure Resource Manager gebaseerd vNET. Klassieke virtuele netwerken worden niet ondersteund voor Azure AD-DS. Zie [Azure Active Directory Domain Services inschakelen met behulp van de Azure Portal](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network)voor meer informatie.

Nadat u Azure AD-DS hebt ingeschakeld, wordt een lokale Domain Name Service (DNS)-server uitgevoerd op de AD-Virtual Machines (Vm's). Configureer uw Azure AD-DS-Virtual Network (VNET) voor het gebruik van deze aangepaste DNS-servers. Als u de juiste IP-adressen wilt vinden, selecteert u **Eigenschappen** onder de categorie **beheren** en bekijkt u de IP-adressen onder **IP-adres op Virtual Network**.

![IP-adressen voor lokale DNS-servers zoeken](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

Wijzig de configuratie van de DNS-servers in azure AD-DS VNET voor gebruik van deze aangepaste Ip's door **DNS-servers** te selecteren onder de categorie **instellingen** . Klik vervolgens op het keuze rondje naast **aangepast**, voer het eerste IP-adres in het onderstaande tekstvak in en klik op **Opslaan**. Voeg aan de hand van dezelfde stappen extra IP-adressen toe.

![De VNET DNS-configuratie bijwerken](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

Het is eenvoudiger om zowel het Azure AD-DS-exemplaar als het HDInsight-cluster in hetzelfde virtuele Azure-netwerk te plaatsen. Als u van plan bent verschillende VNETs te gebruiken, moet u deze virtuele netwerken peeren zodat de domein controller zichtbaar is voor virtuele machines van HDI. Zie [peering van virtuele netwerken](../../virtual-network/virtual-network-peering-overview.md)voor meer informatie. 

Nadat de VNETs zijn gekoppeld, configureert u de HDInsight VNET voor het gebruik van een aangepaste DNS-server en voert u de persoonlijke Ip's van Azure AD-DS in als de DNS-server adressen. Wanneer beide VNETs dezelfde DNS-servers gebruiken, wordt uw aangepaste domein naam omgezet in het juiste IP-adres en is deze bereikbaar vanaf HDInsight. Als uw domein naam bijvoorbeeld `contoso.com` na deze stap, moet `ping contoso.com` worden omgezet naar de juiste Azure AD-DS-IP.

![Aangepaste DNS-servers configureren voor gepeerde VNET](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Als u regels voor netwerk beveiligings groepen (NSG) in uw HDInsight-subnet gebruikt, moet u de [vereiste ip's](../hdinsight-management-ip-addresses.md) toestaan voor zowel binnenkomend als uitgaand verkeer.

Als **u wilt testen** of uw netwerk correct is ingesteld, voegt u een virtuele Windows-machine toe aan het HDInsight VNET/subnet en pingt u de domein naam (deze moet worden omgezet in een IP-adres) en voert u **Ldp. exe** uit om toegang te krijgen tot het Azure AD-DS-domein. **Voeg deze Windows-VM vervolgens toe aan het domein om te bevestigen** dat alle vereiste RPC-aanroepen tussen de client en de server zijn geslaagd. U kunt **nslookup** ook gebruiken om de netwerk toegang te bevestigen voor uw opslag account of een externe data base die u kunt gebruiken (bijvoorbeeld externe Hive-metastore of zwerver DB).
Zorg ervoor dat alle [vereiste poorten](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) in de white list van de Aad-DS-subnetten worden uitgevoerd als Aad-DS wordt beveiligd door een NSG. Als het domein dat lid is van deze Windows-VM is voltooid, kunt u door gaan met de volgende stap en ESP-clusters maken.

## <a name="create-a-hdinsight-cluster-with-esp"></a>Een HDInsight-cluster maken met ESP

Nadat u de vorige stappen op de juiste manier hebt ingesteld, is de volgende stap het maken van het HDInsight-cluster met ESP ingeschakeld. Wanneer u een HDInsight-cluster maakt, kunt u Enterprise Security Package inschakelen op het tabblad **beveiliging en netwerk** . Als u liever een Azure Resource Manager-sjabloon gebruikt voor implementatie, gebruikt u de portal ervaring eenmaal en downloadt u de vooraf ingevulde sjabloon op de pagina **evalueren en maken** voor toekomstig gebruik.

> [!NOTE]  
> De eerste zes tekens van de ESP-cluster namen moeten uniek zijn in uw omgeving. Als u bijvoorbeeld meerdere ESP-clusters in verschillende VNETs hebt, kiest u een naam Convension die ervoor zorgt dat de eerste zes tekens op de cluster namen uniek zijn.

![Domein validatie van Azure HDInsight Enter prise-beveiligings pakket](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

Zodra u ESP hebt ingeschakeld, worden veelvoorkomende onjuiste configuratie-items die betrekking hebben op Azure AD-DS, automatisch gedetecteerd en gevalideerd. Nadat u deze fouten hebt opgelost, kunt u door gaan met de volgende stap:

![Domein validatie van Azure HDInsight Enter prise-beveiligings pakket is mislukt](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

Wanneer u een HDInsight-cluster met ESP maakt, moet u de volgende para meters opgeven:

- **Gebruiker van Cluster beheerder**: Kies een beheerder voor uw cluster uit uw gesynchroniseerde Azure AD-DS. Dit domein account moet al worden gesynchroniseerd en beschikbaar zijn in azure AD-DS.

- **Cluster toegangs groepen**: de beveiligings groepen waarvan de gebruikers die u wilt synchroniseren en toegang tot het cluster, moeten beschikbaar zijn in azure AD-DS. Bijvoorbeeld HiveUsers-groep. Zie [een groep maken en leden toevoegen in azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)voor meer informatie.

- **URL van LDAPS**: een voor beeld is `ldaps://contoso.com:636`.

De beheerde identiteit die u hebt gemaakt, kunt u kiezen in vanuit de door de gebruiker toegewezen vervolg keuzelijst beheerde identiteit bij het maken van een nieuw cluster.

![Beheerde identiteit van Azure HDInsight ESP Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>Volgende stappen

* Zie [configure Apache Hive policies for HDInsight clusters with ESP](apache-domain-joined-run-hive.md)(Engelstalig) voor het configureren van Hive-beleid en het uitvoeren van Hive-query's.
* Zie [SSH gebruiken met Apache Hadoop op basis van Linux in hdinsight via Linux, UNIX of OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)voor het gebruik van SSH om verbinding te maken met hdinsight-clusters met ESP.
