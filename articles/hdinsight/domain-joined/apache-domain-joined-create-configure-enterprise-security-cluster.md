---
title: Clusters van Enterprise Security Package maken, configureren - Azure
description: Meer informatie over het maken en configureren van Enterprise Security Package-clusters in Azure HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: 4edafc0c07e967acfabf7fdc5b58c481b2cfccc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436045"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Clusters van Enterprise Security Package maken en configureren in Azure HDInsight

Enterprise Security Package (ESP) voor Azure HDInsight geeft u toegang tot Active Directory-gebaseerde verificatie, multiuser-ondersteuning en op rollen gebaseerde toegangscontrole voor uw Apache Hadoop-clusters in Azure. HDInsight ESP-clusters stellen organisaties die zich houden aan strikt bedrijfsbeveiligingsbeleid in staat om gevoelige gegevens veilig te verwerken.

In deze handleiding ziet u hoe u een Azure HDInsight-cluster met ESP-ondersteuning maakt. Het laat ook zien hoe u een Windows IaaS VM maakt waarop Active Directory en Domain Name System (DNS) zijn ingeschakeld. Gebruik deze handleiding om de benodigde resources te configureren zodat on-premises gebruikers zich kunnen aanmelden bij een HDInsight-cluster met ESP.Use this guide to configure the necessary resources to allow on-premises users to sign in to an ESP-enabled HDInsight cluster.

De server die u maakt, fungeert als vervanging voor uw *werkelijke* on-premises omgeving. U gebruikt het voor de installatie- en configuratiestappen. Later herhaal je de stappen in je eigen omgeving.

Met deze handleiding u ook een hybride identiteitsomgeving maken door wachtwoordhashte te gebruiken met Azure Active Directory (Azure AD). De gids is een aanvulling op [het gebruik van ESP in HDInsight.](apache-domain-joined-architecture.md)

Voordat u dit proces in uw eigen omgeving gebruikt:

* Active Directory en DNS instellen.
* Azure AD inschakelen.
* On-premises gebruikersaccounts synchroniseren met Azure AD.

![Azure AD-architectuurdiagram](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-an-on-premises-environment"></a>Een on-premises omgeving maken

In deze sectie gebruikt u een Azure Quickstart-implementatiesjabloon om nieuwe VM's te maken, DNS te configureren en een nieuw Active Directory-forest toe te voegen.

1. Ga naar de implementatiesjabloon Snelstart om een Azure VM te [maken met een nieuw Active Directory-forest](https://azure.microsoft.com/resources/templates/active-directory-new-domain/).

1. Selecteer **Implementeren naar Azure**.
1. Meld u aan bij uw Azure-abonnement.
1. Geef op **de pagina Een Azure VM maken met een nieuwe PAGINA AD-forest** de volgende gegevens op:

    |Eigenschap | Waarde |
    |---|---|
    |Abonnement|Selecteer het abonnement waar u de resources wilt implementeren.|
    |Resourcegroep|Selecteer **Nieuw maken**en voer de naam in`OnPremADVRG`|
    |Locatie|Selecteer een locatie.|
    |Gebruikersnaam beheerder|`HDIFabrikamAdmin`|
    |Beheerderswachtwoord|Voer een wachtwoord in.|
    |Domeinnaam|`HDIFabrikam.com`|
    |Dns-voorvoegsel|`hdifabrikam`|

    Laat de resterende standaardwaarden achter.

    ![Sjabloon voor een Azure VM maken met een nieuw Azure AD-forest](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

1. Bekijk de **algemene voorwaarden**en selecteer vervolgens ik ga akkoord met de hierboven vermelde **algemene voorwaarden**.
1. Selecteer **Kopen**en controleer de implementatie en wacht tot deze is voltooid. De implementatie duurt ongeveer 30 minuten.

## <a name="configure-users-and-groups-for-cluster-access"></a>Gebruikers en groepen configureren voor clustertoegang

In deze sectie maakt u de gebruikers die aan het einde van deze handleiding toegang hebben tot het HDInsight-cluster.

1. Maak verbinding met de domeincontroller via Extern bureaublad.
    1. Navigeer vanuit de Azure-portal naar **Resourcegroepen** > **OnPremADVRG** > **adVM** > **Connect**.
    1. Selecteer in de vervolgkeuzelijst **IP-adres** het openbare IP-adres.
    1. Selecteer **RDP-bestand downloaden**en open het bestand.
    1. Gebruik `HDIFabrikam\HDIFabrikamAdmin` als gebruikersnaam.
    1. Voer het wachtwoord in dat u voor het beheerdersaccount hebt gekozen.
    1. Selecteer **OK**.

1. Navigeer vanuit het dashboard **Serverbeheer voor** domeincontroller naar **Tools** > **Active Directory: Gebruikers en computers**.

    ![Open Active Directory Beheer op het dashboard Serverbeheer](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Maak twee nieuwe gebruikers: **HDIAdmin** en **HDIUser**. Deze twee gebruikers zullen zich aanmelden bij HDInsight-clusters.

    1. Klik `HDIFabrikam.com`op de pagina **Gebruikers en computers van Active Directory** met de rechtermuisknop en navigeer vervolgens naar **Nieuwe** > **gebruiker**.

        ![Een nieuwe Active Directory-gebruiker maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. Voer op de pagina **Nieuw object - Gebruiker** de **voornaam** en **aanmeldingsnaam van de gebruiker**in. `HDIUser` De andere velden worden automatisch gevuld. Selecteer **vervolgens Volgende**.

        ![Het eerste beheerdersgebruikersobject maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. Voer in het pop-upvenster dat wordt weergegeven een wachtwoord in voor het nieuwe account. Selecteer **Wachtwoord verloopt nooit**en vervolgens **OK** bij het pop-upbericht.
    1. Selecteer **Volgende**en vervolgens **voltooien** om het nieuwe account te maken.
    1. Herhaal de bovenstaande stappen `HDIAdmin`om de gebruiker te maken.

        ![Een tweede beheerdersgebruikersobject maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. Maak een wereldwijde beveiligingsgroep.

    1. Klik met de rechtermuisknop `HDIFabrikam.com` **op Active Directory- en computers en**navigeer vervolgens naar **Nieuwe** > **groep**.

    1. Voer `HDIUserGroup` het tekstvak **Groepsnaam** in.

    1. Selecteer **OK**.

    ![Een nieuwe Active Directory-groep maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![Een nieuw object maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. Leden toevoegen aan **HDIUserGroup**.

    1. Klik met de rechtermuisknop op **HDIUser** en selecteer **Toevoegen aan een groep...**.
    1. Voer `HDIUserGroup` **in het objectnamen invoeren om** tekstvak te selecteren in . Selecteer vervolgens **OK**en **OK** opnieuw in de pop-up.
    1. Herhaal de vorige stappen voor het **HDIAdmin-account.**

        ![Het lid HDIUser toevoegen aan de groep HDIUserGroup](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

U hebt nu uw Active Directory-omgeving gemaakt. U hebt twee gebruikers en een gebruikersgroep toegevoegd die toegang heeft tot het HDInsight-cluster.

De gebruikers worden gesynchroniseerd met Azure AD.

### <a name="create-an-azure-ad-directory"></a>Een Azure AD-map maken

1. Meld u aan bij Azure Portal.
1. Selecteer **Een resource** `directory`maken en typen . Selecteer **Azure Active Directory** > **Create**.
1. Voer **onder Organisatienaam**. `HDIFabrikam`
1. Voer **onder Initiële domeinnaam**. `HDIFabrikamoutlook`
1. Selecteer **Maken**.

    ![Een Azure AD-map maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

### <a name="create-a-custom-domain"></a>Een aangepast domein maken

1. Selecteer aangepaste domeinnamen in uw nieuwe **Azure Active Directory**onder **Beheren** **.**
1. Selecteer **+ Aangepast domein toevoegen**.
1. Voer **onder Aangepaste** `HDIFabrikam.com`domeinnaam domein in en selecteer **Vervolgens Domein toevoegen**.
1. Vul vervolgens [Uw DNS-gegevens toe aan de domeinregistrar.](../../active-directory/fundamentals/add-custom-domain.md#add-your-dns-information-to-the-domain-registrar)

![Een aangepast domein maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

### <a name="create-a-group"></a>Een groep maken

1. Selecteer **Groepen**in uw nieuwe **Azure Active Directory**onder **Beheren**.
1. Selecteer **+ Nieuwe groep**.
1. Voer in het tekstvak `AAD DC Administrators` **groepsnaam** de invoer in .
1. Selecteer **Maken**.

## <a name="configure-your-azure-ad-tenant"></a>Uw Azure AD-tenant configureren

Nu configureert u uw Azure AD-tenant zo dat u gebruikers en groepen synchroniseren van het on-premises Active Directory-exemplaar naar de cloud.

Maak een Active Directory-tenantbeheerder.

1. Meld u aan bij de Azure-portal en selecteer uw Azure AD-tenant **HDIFabrikam**.

1. Navigeer naar**Gebruikers** >  **beheren** > **Nieuwe gebruiker**.

1. Voer de volgende gegevens in voor de nieuwe gebruiker:

    **Identiteit**

    |Eigenschap |Beschrijving |
    |---|---|
    |Gebruikersnaam|Voer `fabrikamazureadmin` het tekstvak in. Selecteer in de vervolgkeuzelijst domeinnaam de optie`hdifabrikam.com`|
    |Name| Voer `fabrikamazureadmin` in.|

    **Wachtwoord**
    1. Selecteer **Laat mij het wachtwoord maken**.
    1. Voer een veilig wachtwoord van uw keuze in.

    **Groepen en rollen**
    1. Selecteer **0 geselecteerde groepen**.
    1. Selecteer **AAD DC-beheerders**en **selecteer .**

    ![Het dialoogvenster Azure AD-groepen](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-group-member.png)

    1. Selecteer **Gebruiker**.
    1. Selecteer **Globale beheerder**en selecteer vervolgens **.**

    ![Het dialoogvenster Azure AD-rol](./media/apache-domain-joined-create-configure-enterprise-security-cluster/azure-ad-add-role-member.png)

1. Selecteer **Maken**.

1. Laat de nieuwe gebruiker zich vervolgens aanmelden bij de Azure-portal, waar wordt gevraagd het wachtwoord te wijzigen. U moet dit doen voordat u Microsoft Azure Active Directory Connect configureert.

## <a name="sync-on-premises-users-to-azure-ad"></a>On-premises gebruikers synchroniseren met Azure AD

### <a name="configure-microsoft-azure-active-directory-connect"></a>Microsoft Azure Active Directory Connect configureren

1. Download Microsoft Azure [Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594)vanaf de domeincontroller.

1. Open het uitvoerbare bestand dat u hebt gedownload en ga akkoord met de licentievoorwaarden. Selecteer **Doorgaan**.

1. Selecteer **Expresinstellingen gebruiken**.

1. Voer op de pagina **Verbinding maken met Azure AD** de gebruikersnaam en het wachtwoord in van de globale beheerder voor Azure AD. Gebruik de `fabrikamazureadmin@hdifabrikam.com` gebruikersnaam die u hebt gemaakt toen u uw Active Directory-tenant configureerde. Selecteer **vervolgens Volgende**.

    ![De pagina Verbinding maken met Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)

1. Voer op de pagina **Verbinding maken met Active Directory Domain Services** de gebruikersnaam en het wachtwoord voor een bedrijfsbeheeraccount in. Gebruik de `HDIFabrikam\HDIFabrikamAdmin` gebruikersnaam en het wachtwoord dat u eerder hebt gemaakt. Selecteer **vervolgens Volgende**.

   ![De pagina Verbinding maken met Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. Selecteer Volgende op de configuratiepagina van Azure AD.Ad.To the **Azure AD sign-in configuration** page, select **Next**.
   ![De pagina 'Azure AD-aanmeldingsconfiguratie'](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)

1. **Selecteer**installeren op de pagina Klaar om **te configureren** .

   ![De pagina 'Klaar om te configureren'](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)

1. Selecteer **Afsluiten**op de pagina **Configuratie voltooid** .
   ![De pagina 'Configuratie voltooid'](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. Nadat de synchronisatie is voltooid, moet u controleren of de gebruikers die u hebt gemaakt in de IaaS-map, zijn gesynchroniseerd met Azure AD.
   1. Meld u aan bij Azure Portal.
   1. Selecteer **Azure Active Directory** > **HDIFabrikam-gebruikers** > **Users**.

### <a name="create-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit maken

Maak een door de gebruiker toegewezen beheerde identiteit die u gebruiken om Azure AD Domain Services (Azure AD DS) te configureren. Zie [Een rol maken, aanbieden, verwijderen of toewijzen aan een door de gebruiker toegewezen beheerde identiteit met behulp van de Azure-portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)voor meer informatie.

1. Meld u aan bij Azure Portal.
1. Selecteer **Een resource** `managed identity`maken en typen . Selecteer **User Assigned Managed Identity** > **Create**.
1. Voer **voor**de `HDIFabrikamManagedIdentity`resourcenaam .
1. Selecteer uw abonnement.
1. Selecteer **onder Resourcegroep**de `HDIFabrikam-CentralUS`optie Nieuw **maken** en voer .
1. Selecteer Central **US**onder **Locatie**.
1. Selecteer **Maken**.

![Een nieuwe door de gebruiker toegewezen beheerde identiteit maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-ad-ds"></a>Azure AD DS inschakelen

Volg deze stappen om Azure AD DS in te schakelen. Zie [Azure AD DS inschakelen met de Azure-portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)voor meer informatie.

1. Maak een virtueel netwerk om Azure AD DS te hosten. Voer de volgende PowerShell-code uit.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }

    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
    
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Meld u aan bij Azure Portal.
1. Selecteer **Resource maken,** enter `Domain services`en selecteer **Azure AD Domain Services** > **Create**.
1. Op de pagina **Basics:**
    1. Selecteer **onder Mapnaam**de Azure AD-map die u hebt gemaakt: **HDIFabrikam**.
    1. Voer voor **DNS-domeinnaam** *HDIFabrikam.com*in .
    1. Selecteer uw abonnement.
    1. Geef de resourcegroep **HDIFabrikam-CentralUS op**. Selecteer **Voor Locatie**De centrale **VS**.

        ![Basisgegevens van Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. Selecteer **op** de pagina Netwerk het netwerk (**HDIFabrikam-VNET**) en het subnet (**AADDS-subnet**) dat u hebt gemaakt met behulp van het PowerShell-script. Of kies **Nieuw maken** om nu een virtueel netwerk te maken.

    ![De stap 'Virtueel netwerk maken'](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. Op de **groepspagina van Administrator** ziet u een melding dat er al een groep met de naam **AAD DC Administrators** is gemaakt om deze groep te beheren. U het lidmaatschap van deze groep wijzigen als u dat wilt, maar in dit geval hoeft u het niet te wijzigen. Selecteer **OK**.

    ![De azure AD-beheerdersgroep weergeven](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. Schakel **op de** pagina Synchronisatie volledige synchronisatie in door **Alles** > **OK te**selecteren.

    ![Azure AD DS-synchronisatie inschakelen](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. Controleer op de pagina **Overzicht** de gegevens voor Azure AD DS en selecteer **OK**.

    ![De samenvatting van 'Azure AD Domain Services inschakelen'](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

Nadat u Azure AD DS hebt ingeschakeld, wordt een lokale DNS-server uitgevoerd op de Azure AD VM's.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Uw virtuele Azure AD DS-netwerk configureren

Gebruik de volgende stappen om uw Virtuele Azure AD DS-netwerk **(HDIFabrikam-AADDSVNET)** te configureren om uw aangepaste DNS-servers te gebruiken.

1. Zoek de IP-adressen van uw aangepaste DNS-servers.
    1. Selecteer `HDIFabrikam.com` de Azure AD DS-bron.
    1. Selecteer **Eigenschappen** **onder Beheren**.
    1. Zoek de IP-adressen onder **IP-adres op virtueel netwerk.**

    ![Aangepaste DNS-IP-adressen voor Azure AD DS zoeken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. Configureer **HDIFabrikam-AADDSVNET** om aangepaste IP-adressen 10.0.0.4 en 10.0.0.5 te gebruiken.

    1. Selecteer **onder Instellingen** **dns-servers**.
    1. selecteer **Aangepast**.
    1. Voer in het tekstvak het eerste IP-adres in (*10.0.0.4*).
    1. Selecteer **Opslaan**.
    1. Herhaal de stappen om het andere IP-adres toe te voegen (*10.0.0.5*).

In ons scenario hebben we Azure AD DS geconfigureerd om IP-adressen 10.0.0.4 en 10.0.0.5 te gebruiken, waarbij we hetzelfde IP-adres instellen op het virtuele Azure AD DS-netwerk:

![De pagina aangepaste DNS-servers](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>LDAP-verkeer beveiligen

LdAP (Lightweight Directory Access Protocol) wordt gebruikt om te lezen en te schrijven naar Azure Active Directory. U LDAP-verkeer vertrouwelijk en veilig maken met behulp van SSL-technologie (Secure Sockets Layer) of Transport Layer Security (TLS). U LDAP via SSL (LDAPS) inschakelen door een correct geformatteerd certificaat te installeren.

Zie [LDAPS configureren voor een door Azure AD DS beheerd domein voor](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap)meer informatie over beveiligde LDAP.

In deze sectie maakt u een zelfondertekend certificaat, downloadt u het certificaat en configureert u LDAPS voor het **beheerde HDIFabrikam** Azure AD DS-beheerde domein.

Met het volgende script wordt een certificaat gemaakt voor **HDIFabrikam**. Het certificaat wordt opgeslagen in het *localmachinepad.*

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE]  
> Elk hulpprogramma of toepassing die een geldige PKCS-aanvraag (Public Key Cryptography Standards) 10 maakt, \#kan worden gebruikt om de SSL-certificaataanvraag te vormen.

Controleer of het certificaat is geïnstalleerd in het persoonlijke archief **van** de computer:

1. Microsoft Management Console (MMC) starten.
1. Voeg de module **Certificaten** toe die certificaten beheert op de lokale computer.
1. **Uitgebreide persoonlijke certificaten (lokale computer)** > **Personal** > **uitvouwen.** Er moet een nieuw certificaat bestaan in de **persoonlijke** winkel. Dit certificaat wordt afgegeven aan de volledig gekwalificeerde hostnaam.

    ![Het maken van lokale certificaten verifiëren](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. Klik in het deelvenster rechts met de rechtermuisknop op het certificaat dat u hebt gemaakt. Wijs **Alle taken**aan en selecteer **Exporteren**.

1. Selecteer op de pagina **Privésleutel exporteren** de optie **Ja, exporteer de privésleutel**. De computer waar de sleutel wordt geïmporteerd, heeft de privésleutel nodig om de versleutelde berichten te lezen.

    ![De pagina Privésleutel exporteren van de wizard Certificaatexporteren](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. Laat op de pagina **Bestandsindeling exporteren** de standaardinstellingen achter en selecteer **Volgende**.
1. Typ **op** de pagina Wachtwoord een wachtwoord voor de privésleutel. Selecteer **TripleDES-SHA1**voor **versleuteling**. Selecteer **vervolgens Volgende**.
1. Typ **op** de pagina Bestand om te exporteren het pad en de naam voor het geëxporteerde certificaatbestand en selecteer **Volgende**. De bestandsnaam moet een .pfx extensie hebben. Dit bestand is geconfigureerd in de Azure-portal om een beveiligde verbinding tot stand te brengen.
1. LdAPS inschakelen voor een door Azure AD DS beheerd domein.
    1. Selecteer in de Azure-portal het domein `HDIFabrikam.com`.
    1. Selecteer **Onder Beheren**de optie Secure **LDAP**.
    1. Selecteer op de **pagina Secure LDAP** onder **Secure LDAP**de optie **Inschakelen**.
    1. Blader naar het .pfx-certificaatbestand dat u op uw computer hebt geëxporteerd.
    1. Voer het certificaatwachtwoord in.

    ![Beveiligde LDAP inschakelen](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. Nu u LDAPS hebt ingeschakeld, moet u ervoor zorgen dat deze bereikbaar is door poort 636 in te schakelen.
    1. Selecteer in de **BRONgroep HDIFabrikam-CentralUS** de netwerkbeveiligingsgroep **AADDS-HDIFabrikam.com-NSG**.
    1. Selecteer **Onder Instellingen**de **inkomende beveiligingsregels** > **Toevoegen**.
    1. Voer op de pagina **Binnenkomende beveiligingsregel toevoegen** de volgende eigenschappen in en selecteer **Toevoegen:**

        | Eigenschap | Waarde |
        |---|---|
        | Bron | Alle |
        | Poortbereiken van bron | * |
        | Doel | Alle |
        | Poortbereik van doel | 636 |
        | Protocol | Alle |
        | Actie | Toestaan |
        | Prioriteit | \<Gewenst aantal> |
        | Name | Port_LDAP_636 |

    ![Het dialoogvenster Binnenkomende beveiligingsregel toevoegen](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

**HDIFabrikamManagedIdentity** is de door de gebruiker toegewezen beheerde identiteit. De rol van HDInsight Domain Services Contributor is ingeschakeld voor de beheerde identiteit waarmee deze identiteit domeinservices kan lezen, maken, wijzigen en verwijderen.

![Een door de gebruiker toegewezen beheerde identiteit maken](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>Een HDInsight-cluster met ESP maken

Deze stap vereist de volgende voorwaarden:

1. Maak een nieuwe resource groep *HDIFabrikam-WestUS* in de locatie **West US**.
1. Maak een virtueel netwerk dat het HDInsight-cluster met ESP host.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Maak een peer-relatie tussen het virtuele netwerk`HDIFabrikam-AADDSVNET`dat Azure AD DS () host en`HDIFabrikam-HDIVNet`het virtuele netwerk dat het HDInsight-cluster met ESP host ( ). Gebruik de volgende PowerShell-code om de twee virtuele netwerken te peeren.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Maak een nieuw Azure Data Lake Storage Gen2-account genaamd **Hdigen2store.** Configureer het account met de door de gebruiker beheerde identiteit **HDIFabrikamManagedIdentity**. Zie [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-data-lake-storage-gen2.md)voor meer informatie.

1. Stel aangepaste DNS in op het **virtuele HDIFabrikam-AADDSVNET** virtuele netwerk.
    1. Ga naar de Azure-portal > **Resourcegroepen** > **OnPremADVRG** > **HDIFabrikam-AADDSVNET** > **DNS-servers**.
    1. Selecteer **Aangepast** en voer *10.0.0.4* en *10.0.0.5 in*.
    1. Selecteer **Opslaan**.

        ![Aangepaste DNS-instellingen opslaan voor een virtueel netwerk](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. Maak een nieuw HDInsight Spark-cluster met ESP-ingeschakeld.
    1. Selecteer **Aangepast (grootte, instellingen, apps)**.
    1. Voer details in voor **Basisbeginselen** (sectie 1). Controleer of het **clustertype** **Spark 2.3 (HDI 3.6)** is. Zorg ervoor dat de **resourcegroep** **HDIFabrikam-CentralUS**is.

    1. Vul voor **beveiliging + netwerken** (sectie 2) de volgende details in:
        * Selecteer **Ingeschakeld**onder **Enterprise Security Package**.
        * Selecteer **de gebruiker clusterbeheerder** en selecteer het **HDIAdmin-account** dat u hebt gemaakt als on-premises beheerder. Klik **op Selecteren**.
        * Selecteer **clustertoegangsgroep** > **HDIUserGroup**. Elke gebruiker die u in de toekomst aan deze groep toevoegt, heeft toegang tot HDInsight-clusters.

            ![De clustertoegangsgroep HDIUserGroup selecteren](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. Voer de andere stappen van de clusterconfiguratie uit en controleer de details in het **clusteroverzicht**. Selecteer **Maken**.

1. Meld u aan bij de Ambari-gebruikersinterface voor het nieuw gemaakte cluster op `https://CLUSTERNAME.azurehdinsight.net`. Gebruik uw `hdiadmin@hdifabrikam.com` gebruikersnaam en wachtwoord voor beheerders.

    ![Het inlogvenster apache Ambari UI](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. Selecteer **Rollen**in het clusterdashboard .
1. Voer op de pagina **Rollen** onder **Rollen toewijzen aan deze**, naast de rol **Clusterbeheerder** de *groep hdiusergroep*in . 

    ![De clusterbeheerrol toewijzen aan hdiusergroep](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. Open uw Secure Shell (SSH) client en meld u aan bij het cluster. Gebruik de **hdiuser** die u hebt gemaakt in het on-premises Active Directory-exemplaar.

    ![Aanmelden bij het cluster met de SSH-client](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

Als u zich met dit account aanmelden, hebt u uw ESP-cluster correct geconfigureerd om te synchroniseren met uw on-premises Active Directory-exemplaar.

## <a name="next-steps"></a>Volgende stappen

Lees [een inleiding tot Apache Hadoop beveiliging met ESP](hdinsight-security-overview.md).
