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
ms.openlocfilehash: 9b9071eae4ec18cb1d5fd277f6f5403ce3997f48
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261745"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Enterprise Security Package clusters maken en configureren in azure HDInsight

Enterprise Security Package (ESP) voor Azure HDInsight biedt u toegang tot op Active Directory gebaseerde verificatie, ondersteuning voor meerdere gebruikers en toegangs beheer op basis van rollen voor uw Apache Hadoop clusters in Azure. Met HDInsight ESP-clusters kunnen organisaties die voldoen aan het strikte beveiligings beleid van het bedrijf, gevoelige gegevens veilig verwerken.

In deze hand leiding wordt beschreven hoe u een Azure HDInsight-cluster met ESP maakt. Ook wordt beschreven hoe u een Windows IaaS-VM maakt waarop Active Directory en Domain Name System (DNS) zijn ingeschakeld. Gebruik deze hand leiding om de benodigde bronnen te configureren zodat lokale gebruikers zich kunnen aanmelden bij een HDInsight-cluster met ESP-functionaliteit.

De server die u maakt, fungeert als vervanging voor uw *eigen eigen* on-premises omgeving. U gebruikt deze voor de installatie-en configuratie stappen. Later herhaalt u de stappen in uw eigen omgeving. 

Deze hand leiding helpt u ook bij het maken van een hybride identiteits omgeving met behulp van wacht woord-hash synchroniseren met Azure Active Directory (Azure AD). De gids is een aanvulling [op ESP in HDInsight](apache-domain-joined-architecture.md).

Voordat u dit proces in uw eigen omgeving gebruikt:
* Active Directory en DNS instellen.
* Schakel Azure AD in.
* On-premises gebruikers accounts synchroniseren met Azure AD.

![Diagram van Azure AD-architectuur](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-an-on-premises-environment"></a>Een on-premises omgeving maken

In deze sectie gebruikt u een Azure Quick Start-implementatie sjabloon voor het maken van nieuwe Vm's, het configureren van DNS en het toevoegen van een nieuwe Active Directory-forest.

1. Ga naar de Snelstartgids voor het [maken van een Azure VM met een nieuwe Active Directory-forest](https://azure.microsoft.com/resources/templates/active-directory-new-domain/).

1. Selecteer **implementeren naar Azure**.
1. Meld u aan bij uw Azure-abonnement.
1. Op de pagina **een Azure VM maken met een nieuwe AD-forest** :
    1. Selecteer in de vervolg keuzelijst **abonnement** het abonnement waarin u de resources wilt implementeren.
    1. Selecteer in het veld **resource groep**de optie **nieuwe maken**en voer de naam *OnPremADVRG*in.
    1. Voer voor de rest van de sjabloon velden de volgende gegevens in:

        * **Locatie**: US - centraal
        * **Gebruikers naam beheerder**: HDIFabrikamAdmin
        * **Beheerders wachtwoord**: \<YOUR_PASSWORD >
        * **Domein naam**: HDIFabrikam.com
        * **DNS-voor voegsel**: hdifabrikam

        ![Sjabloon voor het maken van een Azure VM met een nieuw Azure AD-forest](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. Selecteer **Aankoop**.
    1. Controleer de implementatie en wacht totdat deze is voltooid.
    1. Zorg ervoor dat de resources zijn gemaakt onder de juiste resource groep **OnPremADVRG**.

## <a name="configure-users-and-groups-for-cluster-access"></a>Gebruikers en groepen configureren voor toegang tot het cluster

In deze sectie maakt u de gebruikers die toegang hebben tot het HDInsight-cluster aan het einde van deze hand leiding.

1. Maak verbinding met de domein controller met behulp van Extern bureaublad.
    1. Als u de sjabloon hebt gebruikt die aan het begin is vermeld, is de domein controller een virtuele machine met de naam **adVM** in de resource groep **OnPremADVRG** .
    1. Selecteer in de Azure Portal **resource groepen** > **OnPremADVRG** > **adVM** > **Connect**.
    1. Selecteer het tabblad **rdp** > het **RDP-bestand te downloaden**.
    1. Sla het bestand op uw computer op en open het.
    1. Wanneer u om referenties wordt gevraagd, gebruikt u *HDIFabrikam\HDIFabrikamAdmin* als de gebruikers naam. Voer vervolgens het wacht woord in dat u hebt gekozen voor het beheerders account.

1. Wanneer uw Extern bureaublad-sessie wordt geopend op de domein controller-VM, opent u **Active Directory gebruikers en computers**op het **Serverbeheer** dash board. Selecteer in de rechter bovenhoek **extra** > **Active Directory gebruikers en computers**.

    ![Open Active Directory Management op het Serverbeheer-dash board](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Twee nieuwe gebruikers maken: **HDIAdmin** en **HDIUser**. Deze twee gebruikers worden aangemeld bij HDInsight-clusters.

    1. Selecteer op de pagina **gebruikers en computers Active Directory** de optie **actie** > **nieuwe** > **gebruiker**.

        ![Een nieuwe Active Directory gebruiker maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. Voer op de pagina **Nieuw object-gebruiker** voor de **aanmeldings naam**van de gebruiker *HDIUser*in. Selecteer vervolgens **Volgende**.

        ![Het eerste gebruikers object voor de beheerder maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. Voer in het pop-upvenster dat wordt weer gegeven een wacht woord voor het nieuwe account in. Selecteer **wacht woord verloopt** > nooit**OK**.
    1. Selecteer **volt ooien** om het nieuwe account te maken.
    1. Maak een andere gebruiker met de naam *HDIAdmin*.

        ![Een tweede gebruikers object voor de beheerder maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. Selecteer op de pagina **gebruikers en computers Active Directory** **actie** > **nieuwe** > **groep**. Voeg een groep toe met de naam *HDIUserGroup*.

    ![Een nieuwe Active Directory groep maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![Een nieuw object maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. Voeg **HDIUser** toe aan **HDIUserGroup** als een groepslid.

    1. Klik met de rechter muisknop op **HDIUserGroup** en selecteer **Eigenschappen**.
    1. Selecteer **toevoegen**op het tabblad **leden** .
    1. Typ *HDIUser*in het vak **Geef de object namen op** . Selecteer vervolgens **OK**.
    1. Herhaal de vorige stappen voor het **HDIAdmin** -account.

        ![Voeg de leden HDIUser toe aan de groep HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

U hebt nu uw Active Directory-omgeving gemaakt. U hebt twee gebruikers en een gebruikers groep toegevoegd die toegang hebben tot het HDInsight-cluster.

De gebruikers worden gesynchroniseerd met Azure AD.

### <a name="create-an-azure-ad-directory"></a>Een Azure AD-Directory maken

1. Meld u aan bij Azure Portal.
1. Selecteer **een resource** en type *Directory*maken. Selecteer **Azure Active Directory** > **maken**.
1. Voer *HDIFabrikam*in bij **organisatie naam**.
1. Voer bij **initiële domein naam** *HDIFabrikamoutlook*in.
1. Selecteer **Maken**.

    ![Een Azure AD-Directory maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

1. Selecteer **Azure Active Directory**aan de linkerkant van de Azure Portal.
1. Selecteer, indien nodig, **Switch Directory** om naar de nieuwe **HDIFabrikamoutlook** -map te gaan.
1. Selecteer onder **beheren**de optie **aangepaste domein namen** > **aangepast domein toevoegen**.
1. Voer onder **aangepaste domein naam** *HDIFabrikam.com* in en selecteer **domein toevoegen**.

![Een aangepast domein maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>Uw Azure AD-Tenant configureren

Nu gaat u uw Azure AD-Tenant configureren zodat u gebruikers en groepen van het on-premises Active Directory-exemplaar kunt synchroniseren met de Cloud.

1. Maak een Active Directory-Tenant beheerder.
    1. Meld u aan bij de Azure Portal en selecteer uw Azure AD-Tenant, **HDIFabrikam**.
    1. Selecteer onder **beheren**de optie **gebruikers** > **nieuwe gebruiker**.
    1. Voer de volgende gegevens in voor de nieuwe gebruiker:
        * **Naam**: fabrikamazureadmin
        * **Gebruikers naam**:fabrikamazureadmin@hdifabrikam.com
        * **Wachtwoord**: Een veilig wacht woord van uw keuze

    1. Zoek in de sectie **groepen** naar **Aad DC-beheerders** en klik op **selecteren**.

        ![Het dialoog venster Azure AD-groepen](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0038.png)

    1. Open de sectie **Directory-rol** en selecteer aan de rechter kant de optie **globale beheerder** > **OK**.

        ![Het dialoog venster Azure AD-rol](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0040.png)

    1. Voer een wacht woord in voor de gebruiker. Selecteer vervolgens **Maken**.

1. Als u het wacht woord voor de zojuist gemaakte gebruikers \< fabrikamazureadmin@hdifabrikam.com> wilt wijzigen, gebruikt u de identiteit om u aan te melden bij de Azure Portal. Vervolgens wordt u gevraagd het wacht woord te wijzigen.

## <a name="sync-on-premises-users-to-azure-ad"></a>On-premises gebruikers synchroniseren met Azure AD

### <a name="download-and-install-azure-ad-connect"></a>Azure AD Connect downloaden en installeren

1. [Down load Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

1. Installeer Azure AD Connect op de domein controller.

    1. Open het uitvoer bare bestand dat u hebt gedownload en ga akkoord met de licentie voorwaarden. Selecteer **Doorgaan**.

        ![De pagina Welkom bij Azure AD Connect](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0052.png)

    1. Selecteer **snelle instellingen gebruiken** en voltooi de installatie.

        ![Azure AD Connect Express-instellingen](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0054.png)

### <a name="configure-a-sync-with-the-on-premises-domain-controller"></a>Een synchronisatie met de on-premises domein controller configureren

1. Voer op de pagina **verbinding maken met Azure AD** de gebruikers naam en het wacht woord in van de globale beheerder voor Azure AD. Gebruik de gebruikers `fabrikamazureadmin@hdifabrikam.com` naam die u hebt gemaakt tijdens het configureren van uw Active Directory-Tenant. Selecteer **volgende**. 

    ![De pagina verbinding maken met Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)

1. Voer op de pagina **verbinding maken met Active Directory Domain Services** de gebruikers naam en het wacht woord in voor een ondernemings Administrator-account. Gebruik de gebruikers `HDIFabrikam\HDIFabrikamAdmin` naam en het wacht woord dat u eerder hebt gemaakt. Selecteer **volgende**. 

   ![De pagina verbinding maken met Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. Selecteer **volgende**op de pagina **aanmeldings configuratie van Azure AD** .
   ![De pagina configuratie van Azure AD-aanmelding](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)

1. Selecteer **installeren**op de pagina **gereed voor configuratie** .

   ![De pagina gereed om te configureren](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)

1. Selecteer op de pagina **configuratie voltooid** de optie **Afsluiten**.
   ![De pagina configuratie voltooid](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. Nadat de synchronisatie is voltooid, controleert u of de gebruikers die u hebt gemaakt in de map IaaS, worden gesynchroniseerd met Azure AD.
   1. Meld u aan bij Azure Portal.
   1. Selecteer **Azure Active Directory** > HDIFabrikam- > **gebruikers**.

### <a name="create-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit maken

Maak een door de gebruiker toegewezen beheerde identiteit die u kunt gebruiken om Azure AD Domain Services (Azure AD DS) te configureren. Zie een [rol maken, weer geven, verwijderen of toewijzen aan een door de gebruiker toegewezen beheerde identiteit met behulp van de Azure Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)voor meer informatie.

1. Meld u aan bij Azure Portal.
1. Selecteer **een resource maken** en typ *beheerde identiteit*. Selecteer door de **gebruiker toegewezen beheerde identiteit** > **maken**.
1. Voer voor de **resource naam** *HDIFabrikamManagedIdentity*in.
1. Selecteer uw abonnement.
1. Selecteer onder **resource groep**de optie **nieuwe maken** en voer *HDIFabrikam-centralus*in.
1. Onder **locatie**selecteert u **Centraal VS**.
1. Selecteer **Maken**.

![een nieuwe door de gebruiker toegewezen beheerde identiteit maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-ad-ds"></a>Azure AD DS inschakelen

Volg deze stappen om Azure AD DS in te scha kelen. Zie [Azure AD DS inschakelen met behulp van de Azure Portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)voor meer informatie.

1. Maak een virtueel netwerk om Azure AD DS te hosten. Voer de volgende Power shell-code uit.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Meld u aan bij Azure Portal.
1. Selecteer **resource maken**, Voer *domein Services*in en selecteer **Azure AD Domain Services**.
1. Op de pagina **basis beginselen** :
    1. Selecteer bij **mapnaam**de Azure AD-map die u hebt gemaakt: **HDIFabrikam**.
    1. Voer voor **DNS-domein naam** *HDIFabrikam.com*in.
    1. Selecteer uw abonnement.
    1. Geef de resource groep **HDIFabrikam-centralus**op. Selecteer voor **locatie**de optie **Centraal VS**.

        ![Basis Details van Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. Selecteer op de pagina **netwerk** het netwerk (**HDIFabrikam-VNET**) en het subnet (**AADDS-subnet**) dat u hebt gemaakt met behulp van het Power shell-script. Of kies **nieuwe maken** om nu een virtueel netwerk te maken.

    ![De stap ' virtueel netwerk maken '](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. Op de pagina **beheer groep** ziet u een melding dat er al een groep met de naam **Aad DC-Administrators** is gemaakt voor het beheren van deze groep. U kunt het lidmaatschap van deze groep wijzigen als u wilt, maar in dit geval hoeft u deze niet te wijzigen. Selecteer **OK**.

    ![De Azure AD-Beheerders groep weer geven](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. Schakel volledige synchronisatie in op de pagina **synchronisatie** door **alle** > **OK**te selecteren.

    ![Azure AD DS-synchronisatie inschakelen](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. Controleer op de pagina **samen vatting** de details van Azure AD DS en selecteer **OK**.

    ![De samen vatting van ' inschakelen Azure AD Domain Services '](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

Nadat u Azure AD DS hebt ingeschakeld, wordt een lokale DNS-server uitgevoerd op de Azure AD-Vm's.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Uw Azure AD DS virtuele netwerk configureren

Gebruik de volgende stappen om uw Azure AD DS virtuele netwerk (**HDIFabrikam-AADDSVNET**) te configureren voor het gebruik van uw aangepaste DNS-servers.

1. Zoek de IP-adressen van uw aangepaste DNS-servers. 
    1. Selecteer de **HDIFabrikam.com** Azure AD DS-resource. 
    1. Selecteer onder **beheren**de optie **Eigenschappen**. 
    1. Zoek de IP-adressen onder **IP-adres in het virtuele netwerk**.

    ![Aangepaste DNS-IP-adressen voor Azure AD DS zoeken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. Configureer **HDIFabrikam-AADDSVNET** om aangepaste IP-adressen 10.0.0.4 en 10.0.0.5 te gebruiken.

    1. Selecteer bij **instellingen**de optie **DNS-servers**. 
    1. selecteer **Aangepast**.
    1. Voer in het tekstvak het eerste IP-adres (*10.0.0.4*) in.
    1. Selecteer **Opslaan**.
    1. Herhaal de stappen om het andere IP-adres (*10.0.0.5*) toe te voegen.

In ons scenario hebben we Azure AD DS geconfigureerd voor het gebruik van IP-adressen 10.0.0.4 en 10.0.0.5, waarbij hetzelfde IP-adres op het virtuele netwerk van Azure AD DS wordt ingesteld:

![De pagina aangepaste DNS-servers](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>LDAP-verkeer beveiligen

LDAP (Lightweight Directory Access Protocol) wordt gebruikt voor het lezen van en schrijven naar Azure Active Directory. U kunt LDAP-verkeer vertrouwelijk en beveiligd maken met behulp van Secure Sockets Layer (SSL) of Transport Layer Security (TLS)-technologie. U kunt LDAP via SSL (LDAPS) inschakelen door een goed opgemaakt certificaat te installeren.

Zie [LDAPS configureren voor een door Azure AD DS beheerd domein](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap)voor meer informatie over het beveiligen van LDAP.

In deze sectie maakt u een zelfondertekend certificaat, downloadt u het certificaat en configureert u LDAPS voor het **HDIFabrikam** Azure AD DS beheerde domein.

Met het volgende script maakt u een certificaat voor **HDIFabrikam**. Het certificaat wordt opgeslagen in het pad *LocalMachine* .

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE] 
> Elk hulp programma of toepassing waarmee een geldige PKCS 10-aanvraag (Public Key \#Cryptography Standards) wordt gemaakt, kan worden gebruikt om de SSL-certificaat aanvraag te vormen.

Controleer of het certificaat is geïnstalleerd in het **persoonlijke** archief van de computer:

1. Start micro soft Management Console (MMC).
1. Voeg de module **certificaten** toe waarmee certificaten op de lokale computer worden beheerd.
1. Vouw **certificaten (lokale computer)**  > **persoonlijke** > **certificaten**uit. Er moet een nieuw certificaat bestaan in het **persoonlijke** archief. Dit certificaat wordt uitgegeven aan de volledig gekwalificeerde hostnaam.

    ![Lokale certificaten maken verifiëren](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. Klik in het rechterdeel venster met de rechter muisknop op het certificaat dat u hebt gemaakt. Wijs **alle taken**aan en selecteer vervolgens **exporteren**.

1. Selecteer op de pagina **persoonlijke sleutel** exporteren **de optie Ja, de persoonlijke sleutel exporteren**. De computer waarop de sleutel wordt geïmporteerd, heeft de persoonlijke sleutel nodig om de versleutelde berichten te lezen.

    ![De pagina persoonlijke sleutel exporteren van de wizard Certificaat exporteren](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. Behoud op de pagina **bestands indeling** voor export de standaard instellingen en selecteer **volgende**. 
1. Typ een wacht woord voor de persoonlijke sleutel op de pagina **wacht woord** . Voor **versleuteling**selecteert u **TripleDES-SHA1**. Selecteer vervolgens **Volgende**.
1. Typ het pad en de naam voor het geëxporteerde certificaat bestand op de pagina **te exporteren** bestand en selecteer vervolgens **volgende**. De bestands naam moet de extensie. pfx hebben. Dit bestand wordt geconfigureerd in de Azure Portal om een beveiligde verbinding tot stand te brengen.
1. Schakel LDAPS in voor een beheerd domein van Azure AD DS.
    1. Selecteer in de Azure Portal het domein **HDIFabrikam.com**.
    1. Selecteer onder **beheren**de optie **secure LDAP**.
    1. Selecteer op de pagina **secure LDAP** onder **secure LDAP**de optie **inschakelen**.
    1. Blader naar het. pfx-certificaat bestand dat u hebt geëxporteerd op uw computer.
    1. Voer het certificaat wachtwoord in.

    ![Secure LDAP inschakelen](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. Nu u LDAPS hebt ingeschakeld, moet u ervoor zorgen dat deze bereikbaar is door poort 636 in te scha kelen.
    1. Selecteer in de resource groep **HDIFabrikam-centralus** de netwerk beveiligings groep **AADDS-HDIFabrikam.com-NSG**.
    1. Selecteer onder **instellingen**de optie **regels** > voor inkomende beveiliging**toevoegen**.
    1. Voer op de pagina **inkomende beveiligings regel toevoegen** de volgende eigenschappen in en selecteer **toevoegen**:

        | Eigenschap | Value |
        |---|---|
        | Source | Any |
        | Source port ranges | * |
        | Destination | Any |
        | Destination port range | 636 |
        | Protocol | Any |
        | Action | Allow |
        | Priority | \<Gewenst getal > |
        | Name | Port_LDAP_636 |

    ![Het dialoog venster "binnenkomende beveiligings regel toevoegen"](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

**HDIFabrikamManagedIdentity** is de door de gebruiker toegewezen beheerde identiteit. De functie voor het beheren van de HDInsight Domain Services-rol is ingeschakeld voor de beheerde identiteit waarmee de identiteit van domein Services kan worden gelezen, gemaakt, gewijzigd en verwijderd.

![Een door de gebruiker toegewezen beheerde identiteit maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>Een HDInsight-cluster met ESP-functionaliteit maken

Voor deze stap zijn de volgende vereisten vereist:

1. Maak een nieuwe resource groep *HDIFabrikam-westus* in de locatie **VS West**.
1. Maak een virtueel netwerk waarmee het HDInsight-cluster met ESP-functionaliteit wordt gehost.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Maak een peer-relatie tussen het virtuele netwerk dat als host fungeert`HDIFabrikam-AADDSVNET`voor Azure AD DS () en het virtuele netwerk dat het HDInsight-cluster met`HDIFabrikam-HDIVNet`ESP-functionaliteit () moet hosten. Gebruik de volgende Power shell-code voor de peering van de twee virtuele netwerken.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Maak een nieuw Azure Data Lake Storage Gen2 account met de naam **Hdigen2store**. Configureer het account met de door de gebruiker beheerde identiteit **HDIFabrikamManagedIdentity**. Zie [Azure data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-data-lake-storage-gen2.md)voor meer informatie.

1. Stel aangepaste DNS in op het virtuele netwerk **HDIFabrikam-AADDSVNET** .
    1. Ga naar het Azure Portal > **resource groepen** > **OnPremADVRG** > **HDIFabrikam-AADDSVNET** > **DNS-servers**.
    1. Selecteer **aangepast** en voer *10.0.0.4* en *10.0.0.5*in.
    1. Selecteer **Opslaan**.

        ![Aangepaste DNS-instellingen voor een virtueel netwerk opslaan](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. Maak een nieuw met ESP ingeschakelde HDInsight Spark-cluster.
    1. Selecteer **aangepast (grootte, instellingen, apps)** .
    2. Voer Details in voor **basis beginselen** (sectie 1). Zorg ervoor dat het **cluster type** **Spark 2,3 (HDI 3,6)** is. Zorg ervoor dat de **resource groep** **HDIFabrikam-centralus**is.

    1. Voor **beveiliging en netwerken** (sectie 2) vult u de volgende gegevens in:
        * Selecteer onder **Enterprise Security Package** **ingeschakeld**.
        * Selecteer **gebruiker cluster beheerder** en selecteer het **HDIAdmin** -account dat u hebt gemaakt als de on-premises beheer gebruiker. Klik op **Selecteren**.
        * Selecteer **cluster toegangs groep** > **HDIUserGroup**. Alle gebruikers die u in de toekomst aan deze groep toevoegt, hebben toegang tot HDInsight-clusters.

            ![Selecteer de cluster toegangs groep HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. Voer de overige stappen van de cluster configuratie uit en controleer de details van de **cluster samenvatting**. Selecteer **Maken**.

1. Meld u aan bij de Ambari-gebruikers interface voor het zojuist `https://CLUSTERNAME.azurehdinsight.net`gemaakte cluster op. Gebruik de gebruikers naam `hdiadmin@hdifabrikam.com` van uw beheerder en het bijbehorende wacht woord.

    ![Het aanmeldings venster van de Apache Ambari-gebruikers interface](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. Selecteer in het cluster dashboard **rollen**.
1. Voer op de pagina **functies** onder **rollen toewijzen aan deze**in naast de rol **cluster beheerder** de groep *hdiusergroup*. 

    ![Wijs de rol cluster beheerder toe aan hdiusergroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. Open uw SSH-client (Secure Shell) en meld u aan bij het cluster. Gebruik de **hdiuser** die u hebt gemaakt in het on-premises Active Directory-exemplaar.

    ![Aanmelden bij het cluster met behulp van de SSH-client](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

Als u zich kunt aanmelden met dit account, hebt u uw ESP-cluster op de juiste wijze geconfigureerd om te synchroniseren met uw on-premises Active Directory-exemplaar.

## <a name="next-steps"></a>Volgende stappen

Lees [een inleiding tot Apache Hadoop beveiliging met ESP](hdinsight-security-overview.md).
