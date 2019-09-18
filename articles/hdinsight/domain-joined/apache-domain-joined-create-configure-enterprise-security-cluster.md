---
title: Enterprise Security Package clusters maken en configureren in azure HDInsight
description: Meer informatie over het maken en configureren van Enterprise Security Package clusters in azure HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/09/2019
ms.openlocfilehash: 482e3e534486a226783e2377c10b074a087e67ed
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71037303"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Enterprise Security Package clusters maken en configureren in azure HDInsight

Met de Enterprise Security Package voor Azure HDInsight hebt u toegang tot op Active Directory gebaseerde verificatie, ondersteuning voor meerdere gebruikers en toegangs beheer op basis van rollen voor uw Apache Hadoop-clusters in Azure. Met HDInsight ESP-clusters kunnen organisaties die voldoen aan het strikte beveiligings beleid voor ondernemingen, gevoelige gegevens veilig verwerken.

Het doel van deze hand leiding is om de benodigde bronnen correct te configureren, zodat on-premises gebruikers zich kunnen aanmelden bij een HDInsight-cluster dat is ingeschakeld voor ESP. In dit artikel worden de stappen beschreven die nodig zijn om een Enterprise Security Package ingeschakeld Azure HDInsight-cluster te maken. De stappen hebben betrekking op het maken van een Windows IaaS-VM met Active Directory & DNS (Domain Name Services) is ingeschakeld. Deze server fungeert als vervanging voor uw **werkelijke** on-premises omgeving en biedt u de mogelijkheid om de installatie-en configuratie stappen door te lopen zodat u ze later in uw eigen omgeving kunt herhalen. Deze hand leiding helpt u ook bij het maken van een hybride identiteits omgeving met wachtwoord hash synchroniseren met Azure Active Directory.

Deze hand leiding is bedoeld als aanvulling op het [gebruik van Enterprise Security package in HDInsight](apache-domain-joined-architecture.md)

Voordat u dit proces in uw eigen omgeving gaat gebruiken, moet u Setup Active Directory en Domain Name Services (DNS). Schakel ook Azure Active Directory-en synchronisatie van on-premises gebruikers accounts in op Azure Active Directory.

![Diagram van Azure Active Directory architectuur](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-on-premises-environment"></a>Een on-premises omgeving maken

Overzicht: In deze sectie gebruikt u een sjabloon voor snelle implementatie van Azure voor het maken van nieuwe Vm's, het configureren van DNS (Domain Name Services) en een nieuw AD-forest.

1. Ga naar een [Azure VM maken met een nieuw AD-forest](https://azure.microsoft.com/resources/templates/active-directory-new-domain/)om de sjabloon voor snelle implementatie te bekijken.

1. Klik op **implementeren naar Azure**.
1. Meld u aan bij uw Azure-abonnement.
1. Voer de volgende stappen uit in het scherm **een Azure-VM maken met een nieuw AD-forest** :
    1. Selecteer het abonnement waarvoor u de resources wilt implementeren in de vervolg keuzelijst **abonnement** .
    1. Selecteer **nieuwe maken** naast **resource groep** en voer de naam **OnPremADVRG** in
    1. Voer de volgende gegevens in voor de rest van de sjabloon velden:

        * **Locatie**: US - centraal
        * **Gebruikers naam beheerder**: HDIFabrikamAdmin
        * **Beheerders wachtwoord**: < YOUR_PASSWORD >
        * **Domein**: HDIFabrikam.com
        * **DNS-voor voegsel**: hdifabrikam

        ![Sjabloon Azure VM en AD-forest maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. Klik op **kopen**
    1. Controleer de implementatie en wacht totdat deze is voltooid.
    1. Controleer of de resources zijn gemaakt onder de juiste resource `OnPremADVRG`groep.

## <a name="configure-users-and-groups-for-cluster-access"></a>Gebruikers en groepen configureren voor toegang tot het cluster

Overzicht: In deze sectie maakt u de gebruikers die toegang hebben tot het HDInsight-cluster aan het einde van deze hand leiding.

1. Maak verbinding met de domein controller met behulp van Extern bureaublad.
    1. Als u de sjabloon hebt gebruikt die aan het begin is vermeld, is de domein controller een virtuele machine `OnPremADVRG` met de naam adVM in de resource groep.
    1. Ga naar het Azure Portal > **resource groepen** > **OnPremADVRG** > **adVM** > **Connect**.
    1. Klik op het tabblad **RDP** en klik vervolgens op **RDP-bestand downloaden**.
    1. Sla het bestand op uw computer op en open het.
    1. Wanneer u om referenties wordt gevraagd `HDIFabrikam\HDIFabrikamAdmin` , gebruikt u als gebruikers naam en voert u het wacht woord in dat u hebt gekozen voor het beheerders account.

1. Zodra uw Extern bureaublad-sessie wordt geopend op de domein controller-VM, start u **Active Directory gebruikers en computers** vanaf het **Serverbeheer** dash board. Klik in de rechter bovenhoek op **extra** en vervolgens **Active Directory gebruikers en computers** in de vervolg keuzelijst.

    ![Serverbeheer Active Directory Management openen](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Maak twee nieuwe gebruikers: **HDIAdmin**, **HDIUser**. Deze twee gebruikers worden gebruikt om zich aan te melden bij HDInsight-clusters.

    1. Klik in het scherm **Active Directory gebruikers en computers** op **actie** > **nieuwe** > **gebruiker**.

        ![Nieuwe Active Directory gebruiker maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. Voer`HDIUser` in het scherm **Nieuw object-gebruiker** de **aanmeldings naam** van de gebruiker in en klik op **volgende**.

        ![Eerst gebruikers object beheerder maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. Voer in het pop-upvenster dat wordt weer gegeven het gewenste wacht woord voor het nieuwe account in. Schakel het selectie vakje **wacht woord verloopt nooit**in. HDIClick **OK**.
    1. Klik op **volt ooien** om het nieuwe account te maken.
    1. Maak een andere `HDIAdmin`gebruiker.

        ![Gebruikers object voor een tweede beheerder maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. Klik in het scherm **Active Directory gebruikers en computers** op **actie** > **nieuwe** > **groep**. Maken `HDIUserGroup` als een nieuwe groep.

    ![Nieuwe Active Directory groep maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![dialoog venster nieuwe object groep maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. Voeg de **HDIUser** die u in de vorige stap hebt gemaakt, toe aan de **HDIUserGroup** als een lid.

    1. Klik met de rechter muisknop op de **HDIUserGroup** en klik op **Eigenschappen**.
    1. Ga naar het tabblad **leden** en klik op **toevoegen**.
    1. Voer `HDIUser` in het vak met **het label de object namen in om te selecteren** en klik op **OK**.
    1. Herhaal de vorige stappen voor het andere account`HDIAdmin`

        ![lid HDIUser toevoegen aan groep HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

U hebt nu uw Active Directory-omgeving gemaakt, samen met twee gebruikers en een gebruikers groep voor toegang tot het HDInsight-cluster.

Deze gebruikers worden gesynchroniseerd met Azure AD.

### <a name="create-a-new-azure-active-directory"></a>Een nieuwe Azure Active Directory maken

1. Meld u aan bij Azure Portal.
1. Klik op **een resource** en type **Directory**maken. Selecteer **Azure Active Directory** > **maken**.
1. Voer **HDIFabrikam** in bij **organisatie naam**.
1. Voer **HDIFabrikamoutlook** in bij **initiële domein naam**.
1. Klik op **Create**.
1. Klik aan de linkerkant in het Azure Portal op **Azure Active Directory**.
1. Klik indien nodig op **Directory overschakelen om over te scha kelen** naar de nieuwe map die u hebt gemaakt **HDIFabrikamoutlook**.
1. Onder **beheren** klikt u op **aangepaste domein namen** > **toevoegen aangepast domein**.
1. Voer **HDIFabrikam.com** in bij **aangepaste domein naam** en klik op **domein toevoegen**.

![een nieuwe Azure Active Directory maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

![Azure AD, een nieuw aangepast domein maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>Uw Azure AD-Tenant configureren

Overzicht: Nu gaat u uw Azure AD-Tenant configureren zodat u gebruikers en groepen van on-premises AD kunt synchroniseren met de Cloud.

1. Maak een AD-Tenant beheerder.
    1. Meld u aan bij de Azure Portal en selecteer uw Azure AD-Tenant **HDIFabrikam**
    1. Selecteer **gebruikers** onder **beheren** en vervolgens **nieuwe gebruiker**.
    1. Voer de volgende gegevens in voor de nieuwe gebruiker:

        * Naam: fabrikamazureadmin
        * Gebruikers naam:fabrikamazureadmin@hdifabrikam.com
        * Wacht woord: een veilig wacht woord van uw keuze

    1. Klik op de sectie **groepen** , zoek naar **Aad DC-beheerders**en klik op **selecteren**.

        ![Dialoog venster Azure Active Directory groepen](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0038.png)

    1. Klik op de sectie **Directory-rol** en selecteer **globale beheerder** aan de rechter kant. Klik op **OK**.

        ![Dialoog venster Azure Active Directory rol](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0040.png)

    1. Voer een wacht woord in voor de gebruiker. Klik op **Create**.

1. Als u het wacht woord voor de zojuist gemaakte gebruiker <fabrikamazureadmin@hdifabrikam.com>wilt wijzigen. Meld u aan bij de Azure Portal met behulp van de identiteit en vervolgens wordt u gevraagd het wacht woord te wijzigen.

## <a name="sync-on-premises-users-to-azure-ad"></a>On-premises gebruikers synchroniseren met Azure AD

### <a name="download-and-install-microsoft-azure-active-directory-connect"></a>Down load en installeer Microsoft Azure Active Directory Connect

1. [Down load Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

1. Installeer Microsoft Azure Active Directory verbinding op de domein controller.

    1. Open het uitvoer bare bestand dat u in de vorige stap hebt gedownload en ga akkoord met de licentie voorwaarden. Klik op **Doorgaan**.

        ![Welkom bij Azure AD Connect dialoog venster](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0052.png)

    1. Klik op **snelle instellingen gebruiken** en voltooi de installatie.

        ![Azure AD Connect Express-instellingen](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0054.png)

### <a name="configure-sync-with-on-premises-domain-controller"></a>Synchronisatie met on-premises domein controller configureren

1. Voer in het scherm **verbinding maken met Azure AD** de gebruikers naam en het wacht woord in van de globale beheerder voor Azure AD. Klik op **volgende**. Dit is de gebruikers `fabrikamazureadmin@hdifabrikam.com` naam die u hebt gemaakt bij het configureren van uw AD-Tenant.

    ![Maak verbinding met Azure Active Directory ](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)

1. Voer in het scherm **verbinding maken met Active Directory Domain Services** de gebruikers naam en het wacht woord in voor een ondernemings Administrator-account. Klik op **volgende**. Dit is de gebruikers `HDIFabrikam\HDIFabrikamAdmin` naam en het bijbehorende wacht woord dat u eerder hebt gemaakt.

   ![Verbinding maken met Active Directory Domain Services](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. Klik op de pagina **aanmeldings configuratie van Azure AD** op **volgende**.
    ![Configuratie van aanmelding bij Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)

1. Klik in het scherm gereed voor configuratie op **installeren**.

    ![Azure AD Connect gereed om dialoog venster te configureren](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)

1. Wanneer het scherm **configuratie voltooid** wordt weer gegeven, klikt u op **Afsluiten**.
    ![Azure AD-configuratie voltooid](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. Nadat de synchronisatie is voltooid, controleert u of de gebruikers die u hebt gemaakt op de IAAS-Active Directory worden gesynchroniseerd naar Azure Active Directory.
    1. Meld u aan bij Azure Portal.
    1. Selecteer **Azure Active Directory** > HDIFabrikam- > **gebruikers**.

### <a name="create-an-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit maken

Maak een door de gebruiker toegewezen beheerde identiteit die wordt gebruikt om Azure Active Directory Domain Services (Azure AD-DS) te configureren. Zie een [rol maken, weer geven, verwijderen of toewijzen aan een door de gebruiker toegewezen beheerde identiteit met behulp van de Azure Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)voor meer informatie over het maken van een door de gebruiker toegewezen beheerde identiteit.

1. Meld u aan bij Azure Portal.
1. Klik op **een resource maken** en typ **beheerde identiteit**. Selecteer door de **gebruiker toegewezen beheerde identiteit** > **maken**.
1. Voer **HDIFabrikamManagedIdentity** in als de **resource naam**.
1. Selecteer uw abonnement.
1. Klik onder **resource groep** op **nieuwe maken** en voer **HDIFabrikam-centralus**in.
1. Selecteer **centrale VS** onder **locatie**.
1. Klik op **Create**.

![een nieuwe door de gebruiker toegewezen beheerde identiteit maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services inschakelen

Zie [Azure Active Directory Domain Services inschakelen met behulp van de Azure Portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)voor meer informatie.

1. Maak de Virtual Network voor het hosten van Azure AD-DS. Voer de volgende Power shell-code uit.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Meld u aan bij Azure Portal.
1. Klik op **resource maken**, Voer **domein Services** in en selecteer **Azure AD Domain Services**.
1. Voer de volgende stappen uit op het scherm **basis beginselen** :
    1. Selecteer **onder mapnaam de** Azure Active Directory gemaakt voor dit artikel, **HDIFabrikam**.
    1. Voer een **DNS-domein naam** in van **HDIFabrikam.com**.
    1. Selecteer uw abonnement.
    1. Geef de resource groep **HDIFabrikam-centralus** en de **locatie** van de **centrale VS**op.

        ![Azure AD Domain Services basis Details](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. Selecteer op het scherm **netwerk** voltooid het netwerk (**HDIFabrikam-VNET**) en het subnet (**AADDS-subnet**) dat u hebt gemaakt met het vorige Power shell-script. U kunt ook de optie **nieuwe maken** gebruiken om nu een virtueel netwerk te maken.

    ![Virtueel netwerk Azure AD Domain Services maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. In het scherm **beheer groep** ziet u een melding dat er al een groep met de naam **Aad DC-Administrators** is gemaakt voor het beheren van deze groep. U kunt eventueel lidmaatschap van deze groep wijzigen, maar dit is niet vereist voor de stappen van dit artikel. Klik op **OK**.

    ![Azure AD-Beheerders groep weer geven](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. Schakel volledige synchronisatie in op het scherm **synchronisatie** door **alle** te selecteren en klik vervolgens op **OK**.

    ![Azure AD Domain Services synchronisatie inschakelen](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. Controleer op het scherm **samen vatting** de details van Azure AD-DS en klik op **OK**.

    ![Samen vatting van Azure AD Domain Services inschakelen](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

1. Nadat u Azure AD-DS hebt ingeschakeld, wordt een lokale Domain Name Service (DNS)-server uitgevoerd op de AD-Virtual Machines (Vm's).

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Uw virtuele Azure AD-DS-netwerk configureren

Met de stappen in deze sectie kunt u uw virtuele Azure AD-DS-netwerk (**HDIFabrikam-AADDSVNET**) configureren voor het gebruik van uw aangepaste DNS-servers.

1. Zoek de IP-adressen van uw aangepaste DNS-servers. Klik op de AD DS-bron **HDIFabrikam.com** , klik op **Eigenschappen** onder **beheren**   en Bekijk de IP-adressen die worden weer gegeven onder **IP-adres op Virtual Network**.

    ![Aangepaste DNS-IP-adressen voor Azure AD-DS zoeken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. **HDIFabrikam-AADDSVNET** configureren voor aangepaste ip's `10.0.0.4` en `10.0.0.5`.

    1. Selecteer **DNS-servers** onder de categorie **instellingen** . Klik vervolgens op het keuze rondje naast **aangepast**, voer het eerste IP-adres (10.0.0.4) in het onderstaande tekstvak in en klik op **Opslaan**.
    1. Voeg aan de hand van dezelfde stappen extra IP-adressen (10.0.0.5) toe.

1. In ons scenario is Azure AD-DS geconfigureerd voor het gebruik van IP-adressen 10.0.0.4 en 10.0.0.5. Stel hetzelfde IP-adres in op AADDS VNet zoals weer gegeven in de onderstaande afbeelding.

    ![dialoog venster aangepaste DNS-servers weer geven](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>LDAP-verkeer beveiligen

LDAP (Lightweight Directory Access Protocol) wordt gebruikt voor het lezen van en schrijven naar Active Directory. U kunt LDAP-verkeer vertrouwelijk en beveiligd maken met behulp van Secure Sockets Layer-technologie (SSL)/Transport Layer Security (TLS). U kunt LDAP via SSL (LDAPS) inschakelen door een goed opgemaakt certificaat te installeren.

Zie [secure LDAP (LDAPS) configureren voor een Azure AD Domain Services beheerd domein](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap)voor meer informatie over beveiligde LDAP.

In deze sectie maakt u een zelfondertekend certificaat, downloadt u het certificaat en configureert u Secure LDAP (LDAPS) voor het **hdifabrikam** Azure AD-DS Managed Domain.

Met het volgende script maakt u een certificaat voor hdifabrikam. Het certificaat wordt opgeslagen in het pad ' LocalMachine '.

> [!Note] 
> Elk hulp programma of toepassing waarmee een geldige PKCS \#10-aanvraag wordt gemaakt, kan worden gebruikt om de SSL-certificaat aanvraag te vormen.

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

Controleer of het certificaat is geïnstalleerd in het persoonlijke\'archief van de computer. Voltooi de volgende stappen:

1. Start micro soft Management Console (MMC).
1. Voeg de module Certificaten toe waarmee certificaten op de lokale computer worden beheerd.
1. Vouw **certificaten (lokale computer)** uit, vouw **persoonlijk**uit en vouw vervolgens **certificaten**uit. Er moet een nieuw certificaat bestaan in het persoonlijke archief. Dit certificaat wordt uitgegeven aan de volledig gekwalificeerde hostnaam.

    ![Lokale certificaten maken verifiëren](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. Klik in het rechterdeel venster met de rechter muisknop op het certificaat dat u in de vorige stap hebt gemaakt, wijs **alle taken**aan en klik op **exporteren**.

1. Klik op de pagina **persoonlijke sleutel** exporteren op **Ja, de persoonlijke sleutel exporteren**. De persoonlijke sleutel is vereist voor het lezen van de versleutelde berichten van de computer waarop de sleutel wordt geïmporteerd.

    ![Wizard Certificaat, pagina persoonlijke sleutel exporteren](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. Behoud op de pagina **bestands indeling** voor export de standaard instellingen en klik op **volgende**. 
1. Typ een wacht woord voor de persoonlijke sleutel op de pagina **wacht woord** , selecteer **TripleDES-SHA1** voor **versleuteling** en klik op **volgende**.
1. Typ het pad en de naam voor het geëxporteerde certificaat bestand op de pagina **te exporteren** bestand en klik vervolgens op **volgende**.
1. De bestands naam moet de extensie. pfx hebben. dit bestand wordt geconfigureerd op Azure Portal om een beveiligde verbinding tot stand te brengen.
1. Schakel secure LDAP (LDAPS) in voor een Azure AD Domain Services beheerd domein.
    1. Selecteer de **HDIFabrikam.com** van het domein in de Azure Portal.
    1. Klik op **secure LDAP** onder **beheren**.
    1. Klik op het scherm **secure LDAP** op **inschakelen** onder **secure LDAP**.
    1. Blader naar het. pfx-certificaat bestand dat u hebt geëxporteerd op uw computer.
    1. Voer het certificaat wachtwoord in.

    ![Azure AD-DS, secure LDAP inschakelen](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. Nu u Secure LDAP hebt ingeschakeld, moet u ervoor zorgen dat deze bereikbaar is door poort 636 in te scha kelen.
    1. Klik op de netwerk beveiligings groep **AADDS-HDIFabrikam.com-NSG** in de resource groep **HDIFabrikam-centralus** .
    1. Klik onder **instellingen** op **beveiligings regels** > voor binnenkomende verbindingen**toevoegen**.
    1. Voer in het scherm **binnenkomende beveiligings regel toevoegen** de volgende eigenschappen in en klik op **toevoegen**:

        | Eigenschap | Value |
        |---|---|
        | Source | Any |
        | Source port ranges | * |
        | Destination | Any |
        | Destination port range | 636 |
        | Protocol | Any |
        | Action | Allow |
        | Priority | \<Desired Number\> |
        | Name | Port_LDAP_636 |

    ![Dialoog venster binnenkomende beveiligings regel toevoegen](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

1. `HDIFabrikamManagedIdentity`is de door de gebruiker toegewezen beheerde identiteit, de functie voor het beheren van HDInsight Domain Services-rol is ingeschakeld voor de beheerde identiteit waarmee deze identiteit kan worden gelezen, gemaakt, gewijzigd en verwijderd.

    ![Door de gebruiker toegewezen beheerde identiteit maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="creating-enterprise-security-package-enabled-hdinsight-cluster"></a>Enterprise Security Package ingeschakeld HDInsight-cluster maken

Voor deze stap zijn de volgende vereisten vereist:

1. Maak een nieuwe resource groep `HDIFabrikam-WestUS` op de locatie `West US`.
1. Maak een virtueel netwerk dat een door ESP ingeschakeld HDInsight-cluster moet hosten.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Maak een peer-relatie tussen de Virtual Network die als host fungeert`HDIFabrikam-AADDSVNET`voor AADDS () en de Virtual Network die als host moet fungeren voor`HDIFabrikam-HDIVNet`het HDInsight-cluster dat is ingeschakeld voor ESP (). Gebruik de volgende Power shell-code voor de peering van deze twee virtuele netwerken.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Maak een nieuw Azure Data Lake Storage Gen2-account, **Hdigen2store**, dat is geconfigureerd met de door de gebruiker beheerde identiteit **HDIFabrikamManagedIdentity**. Zie [Azure data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-data-lake-storage-gen2.md)voor meer informatie over het maken van data Lake Storage Gen2-accounts die zijn ingeschakeld met door de gebruiker beheerde identiteiten.

1. Aangepaste DNS instellen op het virtuele netwerk **HDIFabrikam-AADDSVNET** .
    1. Ga naar het Azure Portal > **resource groepen** > **OnPremADVRG** > **HDIFabrikam-AADDSVNET** > **DNS-servers**.
    1. Selecteer **aangepast** en voer `10.0.0.4` en `10.0.0.5`in.
    1. Klik op **Opslaan**.

        ![Aangepaste DNS-instellingen voor het virtuele netwerk opslaan](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. Maak een nieuw met ESP ingeschakelde HDInsight Spark-cluster.
    1. Klik op **aangepast (grootte, instellingen, apps)** .
    2. Voer de gewenste details in voor de **basis beginselen**van sectie 1. Zorg ervoor dat het **cluster type** **Spark 2,3 (HDI 3,6)** is en de **resource groep** **HDIFabrikam-centralus** is

    1. Voer de volgende stappen uit onder sectie 2 **beveiliging en netwerken**:
        1. Klik op **ingeschakeld** onder **Enterprise Security Package**.
        1. Klik op **gebruiker cluster beheerder** en selecteer het **HDIAdmin** -account dat u eerder hebt gemaakt als de on-premises beheer gebruiker. Klik op **Selecteren**.

        1. Klik op **cluster toegangs groep** en selecteer vervolgens **HDIUserGroup**. Alle gebruikers die u in de toekomst aan deze groep toevoegt, hebben toegang tot HDInsight-clusters.

            ![Selecteer de cluster toegangs groep HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. Voer de overige stappen van de cluster configuratie uit en controleer de details van de **cluster samenvatting**. Klik op **Create**.

1. Meld u aan bij de Ambari-gebruikers interface voor het nieuwe `https://CLUSTERNAME.azurehdinsight.net` cluster op door gebruik te maken `hdiadmin@hdifabrikam.com` van de gebruikers naam en het wacht woord voor de beheerder.

    ![Aanmeldings venster van Apache Ambari-gebruikers interface](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. Klik op **rollen** in het cluster dashboard.
1. Voer op de pagina **functies** de groep **hdiusergroup** in om deze toe te wijzen aan de rol **cluster beheerder** onder **rollen toewijzen aan deze**.

    ![Rol cluster beheerder toewijzen aan hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. Open uw SSH-client en meld u aan bij het cluster met behulp van de **hdiuser** die u eerder hebt gemaakt in het on-premises Active Directory.

    ![Aanmelden bij cluster met SSH-client](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

Als u zich kunt aanmelden met dit account, hebt u uw ESP-cluster op de juiste manier geconfigureerd om te synchroniseren met uw on-premises Active Directory.

## <a name="next-steps"></a>Volgende stappen

* [Een inleiding tot Apache Hadoop-beveiliging met Enterprise Security Package](hdinsight-security-overview.md)
