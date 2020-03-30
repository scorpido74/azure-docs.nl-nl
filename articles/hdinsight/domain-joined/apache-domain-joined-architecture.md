---
title: Azure HDInsight-architectuur met Enterprise Security Package
description: Meer informatie over het plannen van Azure HDInsight-beveiliging met enterprise security pakket.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: omidm
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/11/2020
ms.openlocfilehash: 452a3b04637126b40aca907178bebd6f74ec4481
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365768"
---
# <a name="use-enterprise-security-package-in-hdinsight"></a>Enterprise Security Package in HDInsight gebruiken

Het standaard Azure HDInsight-cluster is een cluster met één gebruiker. Het is geschikt voor de meeste bedrijven die kleinere applicatieteams hebben die grote dataworkloads bouwen. Elke gebruiker kan een speciaal cluster op aanvraag maken en vernietigen wanneer het niet meer nodig is.

Veel ondernemingen zijn overgestapt op een model waarin IT-teams clusters beheren en meerdere toepassingsteams clusters delen. Deze grotere ondernemingen hebben toegang voor meerdere gebruikers nodig tot elk cluster in Azure HDInsight.

HDInsight vertrouwt op een populaire identiteitsprovider - Active Directory - op een beheerde manier. Door HDInsight te integreren met [Azure Active Directory Domain Services (Azure AD DS),](../../active-directory-domain-services/overview.md)hebt u toegang tot de clusters met behulp van uw domeinreferenties.

De virtuele machines (VM's) in HDInsight zijn domein verbonden met uw opgegeven domein. Dus, alle diensten die draaien op HDInsight (Apache Ambari, Apache Hive server, Apache Ranger, Apache Spark thrift server, en anderen) werken naadloos voor de geverifieerde gebruiker. Beheerders kunnen vervolgens een sterk autorisatiebeleid maken door Apache Ranger te gebruiken om op rollen gebaseerde toegangscontrole te bieden voor bronnen in het cluster.

## <a name="integrate-hdinsight-with-active-directory"></a>HDInsight integreren met Active Directory

Open-source Apache Hadoop vertrouwt op het Kerberos-protocol voor authenticatie en beveiliging. Daarom worden HDInsight-clusterknooppunten met Enterprise Security Package (ESP) gekoppeld aan een domein dat wordt beheerd door Azure AD DS. Kerberos-beveiliging is geconfigureerd voor de Hadoop-componenten op het cluster.

De volgende dingen worden automatisch gemaakt:

- Een serviceprincipal voor elk Hadoop-onderdeel
- Een machineprincipal voor elke machine die is verbonden met het domein
- Een organisatie-eenheid (OU) voor elk cluster om deze service- en machineprincipals op te slaan

Om samen te vatten, moet u een omgeving instellen met:

- Een Active Directory-domein (beheerd door Azure AD DS). **De domeinnaam moet 39 tekens of minder zijn om met Azure HDInsight te werken.**
- Secure LDAP (LDAPS) ingeschakeld in Azure AD DS.
- Goede netwerkconnectiviteit van het virtuele HDInsight-netwerk naar het virtuele Azure AD DS-netwerk, als u afzonderlijke virtuele netwerken voor hen kiest. Een VM in het virtuele HDInsight-netwerk moet een zichtlijn hebben naar Azure AD DS via virtuele netwerkpeering. Als HDInsight en Azure AD DS in hetzelfde virtuele netwerk worden geïmplementeerd, wordt de connectiviteit automatisch geleverd en is er geen verdere actie nodig.

## <a name="set-up-different-domain-controllers"></a>Verschillende domeincontrollers instellen

HDInsight ondersteunt momenteel alleen Azure AD DS als de hoofddomeincontroller die het cluster gebruikt voor Kerberos-communicatie. Maar andere complexe Active Directory-instellingen zijn mogelijk, zolang een dergelijke installatie leidt tot het inschakelen van Azure AD DS voor HDInsight-toegang.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services

[Azure AD DS](../../active-directory-domain-services/overview.md) biedt een beheerd domein dat volledig compatibel is met Windows Server Active Directory. Microsoft zorgt voor het beheren, patchen en bewaken van het domein in een zeer beschikbare (HA) setup. U uw cluster implementeren zonder u zorgen te maken over het onderhouden van domeincontrollers.

Gebruikers, groepen en wachtwoorden worden gesynchroniseerd vanuit Azure AD. Met de eenrichtingssynchronisatie van uw Azure AD-exemplaar naar Azure AD DS kunnen gebruikers zich aanmelden bij het cluster met dezelfde bedrijfsreferenties.

Zie [HDInsight-clusters configureren met ESP configureren met Azure AD DS](./apache-domain-joined-configure-using-azure-adds.md)voor meer informatie.

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>On-premises Active Directory of Active Directory op IaaS VM's

Als u een on-premises Active Directory-instantie of complexere Active Directory-instellingen voor uw domein hebt, u deze identiteiten synchroniseren met Azure AD met Azure AD Connect. U azure AD DS vervolgens inschakelen op die Active Directory-tenant.

Omdat Kerberos afhankelijk is van wachtwoordhashes, moet u [wachtwoordhashsynchronisatie inschakelen op Azure AD DS.](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)

Als u federatie met Active Directory Federation Services (AD FS) gebruikt, moet u wachtwoordhashsynchronisatie inschakelen. (Voor een aanbevolen installatie, zie [deze video](https://youtu.be/qQruArbu2Ew).) Wachtwoordhashsynchronisatie helpt bij noodherstel in het geval uw AD FS-infrastructuur uitvalt en het helpt ook om bescherming met gelekte referenties te bieden. Zie [Wachtwoordhashsynchronisatie inschakelen met Azure AD Connect-synchronisatie](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)voor meer informatie.

Het gebruik van on-premises Active Directory of Active Directory op IaaS VM's alleen, zonder Azure AD en Azure AD DS, is geen ondersteunde configuratie voor HDInsight-clusters met ESP.

Als federatie wordt gebruikt en wachtwoordhashes correct worden gesynchroniseerd, maar u verificatiefouten krijgt, controleert u of verificatie van het cloudwachtwoord is ingeschakeld voor de PowerShell-serviceprincipal. Als dit niet het zo is, moet u een [HRD-beleid (Home Realm Discovery)](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) instellen voor uw Azure AD-tenant. Ga als het gaat om het HRD-beleid te controleren en in te stellen:

1. Installeer de preview [Azure AD PowerShell-module](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2).

   ```powershell
   Install-Module AzureAD
   ```

2. Maak verbinding met referenties van globale beheerder (tenantbeheerder).

   ```powershell
   Connect-AzureAD
   ```

3. Controleer of de Microsoft Azure PowerShell-serviceprincipal al is gemaakt.

   ```powershell
   Get-AzureADServicePrincipal -SearchString "Microsoft Azure Powershell"
   ```

4. Als deze niet bestaat, maakt u de serviceprincipal.

   ```powershell
   $powershellSPN = New-AzureADServicePrincipal -AppId 1950a258-227b-4e31-a9cf-717495945fc2
   ```

5. Het beleid aan deze serviceprincipal maken en koppelen.

   ```powershell
    # Determine whether policy exists
    Get-AzureADPolicy | Where {$_.DisplayName -eq "EnableDirectAuth"}

    # Create if not exists
    $policy = New-AzureADPolicy `
        -Definition @('{"HomeRealmDiscoveryPolicy":{"AllowCloudPasswordValidation":true}}') `
        -DisplayName "EnableDirectAuth" `
        -Type "HomeRealmDiscoveryPolicy"

    # Determine whether a policy for the service principal exist
    Get-AzureADServicePrincipalPolicy `
        -Id $powershellSPN.ObjectId

    # Add a service principal policy if not exist
    Add-AzureADServicePrincipalPolicy `
        -Id $powershellSPN.ObjectId `
        -refObjectID $policy.ID
   ```

## <a name="next-steps"></a>Volgende stappen

- [HDInsight-clusters configureren met ESP](apache-domain-joined-configure-using-azure-adds.md)
- [Apache Hive-beleid configureren voor HDInsight-clusters met ESP](apache-domain-joined-run-hive.md)
- [HDInsight-clusters beheren met ESP](apache-domain-joined-manage.md)
