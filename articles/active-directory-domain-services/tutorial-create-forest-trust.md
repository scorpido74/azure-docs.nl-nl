---
title: Zelf studie-een forest-vertrouwens relatie maken in Azure AD Domain Services | Microsoft Docs
description: Meer informatie over het maken van een eenrichtings-uitgaand forest naar een on-premises AD DS domein in de Azure Portal voor Azure AD Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: bd0ec46d224e68f92b5d042826633d1efc7c336e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425423"
---
# <a name="tutorial-create-an-outbound-forest-trust-to-an-on-premises-domain-in-azure-active-directory-domain-services-preview"></a>Zelf studie: een uitgaande forest-vertrouwens relatie maken met een on-premises domein in Azure Active Directory Domain Services (preview-versie)

In omgevingen waar u geen wacht woord-hashes kunt synchroniseren, of als u gebruikers hebt die zich alleen aanmelden met Smart Cards, zodat ze hun wacht woord niet kennen, kunt u een resource-forest gebruiken in Azure Active Directory Domain Services (AD DS). Een resource-forest gebruikt een eenrichtings uitgaande vertrouwens relatie van Azure AD DS naar een of meer on-premises AD DS omgevingen. Met deze vertrouwens relatie kunnen gebruikers, toepassingen en computers worden geverifieerd op basis van een on-premises domein van het door Azure AD DS beheerde domein. Azure AD DS resource-forests zijn momenteel beschikbaar als preview-versie.

![Diagram van forestvertrouwensrelatie van Azure AD DS op on-premises AD DS](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * DNS configureren in een on-premises AD DS omgeving ter ondersteuning van Azure AD DS-connectiviteit
> * Een eenrichtings vertrouwensrelatie voor een inkomend forest in een on-premises AD DS omgeving maken
> * Een eenrichtings vertrouwensrelatie voor uitgaande forests maken in azure AD DS
> * De vertrouwens relatie voor verificatie en toegang tot bronnen testen en valideren

Als u nog geen abonnement op Azure hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources en bevoegdheden nodig om deze zelf studie te volt ooien:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory Tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises Directory of een alleen-Cloud Directory.
    * Als dat nodig is, [maakt u een Azure Active Directory-Tenant][create-azure-ad-tenant] of [koppelt u een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* Een Azure Active Directory Domain Services beheerd domein dat is gemaakt met behulp van een resource-forest en geconfigureerd in uw Azure AD-Tenant.
    * Als dat nodig is, kunt [u een Azure Active Directory Domain Services-exemplaar maken en configureren][create-azure-ad-ds-instance-advanced].
    
    > [!IMPORTANT]
    > Zorg ervoor dat u een beheerd domein van Azure AD DS maakt met behulp van een *resource* -forest. Met de standaard optie wordt een *gebruikers* forest gemaakt. Alleen bron-forests kunnen vertrouwens relaties maken met on-premises AD DS omgevingen.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

In deze zelf studie maakt en configureert u de uitgaande forestvertrouwensrelatie vanuit Azure AD DS met behulp van de Azure Portal. Meld u eerst aan bij de [Azure Portal](https://portal.azure.com)om aan de slag te gaan.

## <a name="networking-considerations"></a>Aandachtspunten voor netwerken

Het virtuele netwerk dat als host fungeert voor het Azure AD DS resource-forest, moet een netwerk verbinding hebben met uw on-premises Active Directory. Toepassingen en services hebben ook een netwerk verbinding met het virtuele netwerk nodig die als host fungeert voor het forest van de Azure AD DS-resource. De netwerk verbinding met de Azure AD DS-bron-forest moet altijd zijn ingeschakeld en stabiel, anders kunnen gebruikers geen toegang krijgen tot bronnen.

Voordat u een forestvertrouwensrelatie configureert in azure AD DS, moet u ervoor zorgen dat uw netwerken tussen de Azure-en on-premises omgeving voldoen aan de volgende vereisten:

* Gebruik privé IP-adressen. Vertrouw niet op DHCP met dynamische IP-adres toewijzing.
* Vermijd overlappende IP-adres ruimten zodat peering van virtuele netwerken en route ring kunnen communiceren tussen Azure en on-premises.
* Een virtueel Azure-netwerk heeft een gateway-subnet nodig om een S2S-VPN-of ExpressRoute-verbinding (site-naar-site) te configureren
* Maak subnetten met voldoende IP-adressen ter ondersteuning van uw scenario.
* Zorg ervoor dat Azure AD DS een eigen subnet heeft, deel dit subnet van het virtuele netwerk niet met de Vm's en services van de toepassing.
* Gekoppelde virtuele netwerken zijn niet transitief.
    * Virtuele Azure-netwerk-peerings moeten worden gemaakt tussen alle virtuele netwerken die u wilt gebruiken voor de vertrouwens relatie van het Azure AD DS resource-forest naar de on-premises AD DS omgeving.
* Bied continue netwerk connectiviteit met uw on-premises Active Directory-forest. Gebruik geen verbindingen op aanvraag.
* Zorg ervoor dat er sprake is van een continue naam omzetting (DNS) tussen de naam van uw Azure AD DS resource-forest en de naam van uw on-premises Active Directory-forest.

## <a name="configure-dns-in-the-on-premises-domain"></a>DNS configureren in het on-premises domein

Als u de door Azure AD DS beheerde domein correct wilt omzetten vanuit de on-premises omgeving, moet u mogelijk doorstuur servers toevoegen aan de bestaande DNS-server. Als u de on-premises omgeving niet hebt geconfigureerd om te communiceren met het door Azure AD DS beheerde domein, voert u de volgende stappen uit vanaf een beheer werkstation voor het on-premises AD DS domein:

1. Selecteer **Start | Systeem beheer | DNS**
1. Klik met de rechter muisknop op DNS-server, zoals *myAD01*, selecteer **Eigenschappen**
1. Kies **doorstuur servers**en klik vervolgens op **bewerken** om aanvullende doorstuur servers toe te voegen.
1. Voeg de IP-adressen van het beheerde domein van Azure AD DS, zoals *10.0.1.4* en *10.0.1.5*, toe.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>Een binnenkomend forest-vertrouwens relatie maken in het on-premises domein

De on-premises AD DS domein vereist een binnenkomende forestvertrouwensrelatie voor het door Azure AD DS beheerde domein. Deze vertrouwens relatie moet hand matig worden gemaakt in het on-premises AD DS domein, maar kan niet worden gemaakt op basis van de Azure Portal.

Als u binnenkomende vertrouwens relatie wilt configureren voor het on-premises AD DS domein, voert u de volgende stappen uit vanaf een beheer werkstation voor het on-premises AD DS domein:

1. Selecteer **Start | Systeem beheer | Active Directory domeinen en vertrouwens relaties**
1. Klik met de rechter muisknop op domein, zoals *onprem.contoso.com*, selecteer **Eigenschappen**
1. Klik op het tabblad **vertrouwens relaties** en vervolgens op **nieuwe vertrouwens relatie**

   > [!NOTE]
   > Als u de menu optie **vertrouwens relaties** niet ziet, controleert u onder **Eigenschappen** voor het *type forest*. Alleen *bron* -forests kunnen vertrouwens relaties maken. Als het forest-type *gebruiker*is, kunt u geen vertrouwens relaties maken. Er is momenteel geen manier om het type forest van een door Azure AD DS beheerd domein te wijzigen. U moet het beheerde domein verwijderen en opnieuw maken als een resource-forest.

1. Voer een naam in voor Azure AD DS domein naam, bijvoorbeeld *aadds.contoso.com*, en selecteer vervolgens **volgende**
1. Selecteer de optie voor het maken van een **forestvertrouwensrelatie**en vervolgens het maken van een **eenrichtings vertrouwensrelatie: inkomend** vertrouwen.
1. Kies ervoor om de vertrouwens relatie **alleen voor dit domein**te maken. In de volgende stap maakt u de vertrouwens relatie in de Azure Portal voor het beheerde domein van Azure AD DS.
1. Kies voor het gebruik van **forest-dekkende verificatie**en voer vervolgens een vertrouwens wachtwoord in en bevestig dit. Dit wacht woord wordt ook ingevoerd in de Azure Portal in de volgende sectie.
1. Door loop de volgende vensters met de standaard opties en kies vervolgens de optie **Nee, Controleer de uitgaande vertrouwens relatie niet**.
1. Selecteer **Voltooien**.

## <a name="create-outbound-forest-trust-in-azure-ad-ds"></a>Een uitgaand forest vertrouwen maken in azure AD DS

Met het on-premises AD DS domein dat is geconfigureerd voor het oplossen van het beheerde Azure AD DS-domein en een binnenkomende forest-vertrouwens relatie, is nu de uitgaande forestvertrouwensrelatie gemaakt. Deze vertrouwens relatie met een uitgaand forest is de vertrouwensrelatie tussen het on-premises AD DS domein en het beheerde domein van Azure AD DS.

Voer de volgende stappen uit om de uitgaande vertrouwens relatie voor het door Azure AD DS beheerde domein te maken in de Azure Portal:

1. In de Azure Portal zoekt en selecteert u **Azure AD Domain Services**en selecteert u vervolgens uw beheerde domein, zoals *aadds.contoso.com*
1. Selecteer in het menu aan de linkerkant van het Azure AD DS Managed Domain de optie **vertrouwens relaties**en kies vervolgens om + een vertrouwens relatie **toe te voegen** .
1. Voer een weergave naam in die de vertrouwens relatie identificeert en vervolgens de naam van het on-premises vertrouwde forest-DNS, zoals *onprem.contoso.com*
1. Geef het wacht woord op voor de vertrouwens relatie die is gebruikt bij het configureren van de binnenkomende forestvertrouwensrelatie voor het on-premises AD DS domein in de vorige sectie.
1. Geef ten minste twee DNS-servers op voor het on-premises AD DS domein, zoals *10.0.2.4* en *10.0.2.5*
1. Als u klaar bent, **slaat** u de uitgaande forestvertrouwensrelatie op

    [Uitgaande forestvertrouwensrelatie maken in de Azure Portal](./media/create-forest-trust/portal-create-outbound-trust.png)

## <a name="validate-resource-authentication"></a>Verificatie van resources valideren

Met de volgende algemene scenario's kunt u controleren of de vertrouwens relatie van het forest op de juiste wijze gebruikers en toegang tot bronnen heeft.

* [On-premises gebruikers authenticatie vanuit het Azure AD DS resource-forest](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [Toegang krijgen tot resources in het Azure AD DS resource-forest met on-premises gebruiker](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [Bestands-en printer deling inschakelen](#enable-file-and-printer-sharing)
    * [Een beveiligings groep maken en leden toevoegen](#create-a-security-group-and-add-members)
    * [Een bestands share maken voor toegang tussen forests](#create-a-file-share-for-cross-forest-access)
    * [Verificatie tussen forests valideren voor een resource](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>On-premises gebruikers authenticatie vanuit het Azure AD DS resource-forest

U moet een virtuele Windows Server-machine koppelen aan het Azure AD DS-bron domein. Gebruik deze virtuele machine om uw on-premises gebruiker te laten verifiëren op een virtuele machine.

1. Maak verbinding met de Windows Server-VM die is toegevoegd aan het Azure AD DS resource-forest met Extern bureaublad en uw referenties voor de Azure AD DS-beheerder. Als er een verificatie op netwerkniveau-fout (NLA) wordt weer geven, controleert u of het gebruikers account dat u hebt gebruikt geen domein gebruikers account is.

    > [!NOTE]
    > Als u een beveiligde verbinding wilt maken met uw Vm's die zijn toegevoegd aan Azure AD Domain Services, kunt u de [Azure bastion host-service](https://docs.microsoft.com/azure/bastion/bastion-overview) gebruiken in ondersteunde Azure-regio's.

1. Open een opdracht prompt en gebruik de opdracht `whoami` om de DN-naam van de momenteel geverifieerde gebruiker weer te geven:

    ```console
    whoami /fqdn
    ```

1. Gebruik de `runas` opdracht om te verifiëren als een gebruiker van het on-premises domein. Vervang in de volgende opdracht `userUpn@trusteddomain.com` door de UPN van een gebruiker uit het vertrouwde on-premises domein. De opdracht vraagt u om het wacht woord van de gebruiker:

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. Als de verificatie is geslaagd, wordt een nieuwe opdracht prompt geopend. De titel van de nieuwe opdracht prompt bevat `running as userUpn@trusteddomain.com`.
1. Gebruik `whoami /fqdn` in de nieuwe opdracht prompt om de DN-naam van de geverifieerde gebruiker uit de on-premises Active Directory weer te geven.

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>Toegang krijgen tot resources in het Azure AD DS resource-forest met on-premises gebruiker

Met de virtuele machine van Windows Server die is toegevoegd aan het Azure AD DS resource-forest, kunt u het scenario testen waarbij gebruikers toegang hebben tot resources die worden gehost in de bron-forest wanneer ze worden geverifieerd vanaf computers in het on-premises domein met gebruikers van het on-premises domein. In de volgende voor beelden ziet u hoe u verschillende veelvoorkomende scenario's kunt maken en testen.

#### <a name="enable-file-and-printer-sharing"></a>Bestands-en printer deling inschakelen

1. Maak verbinding met de Windows Server-VM die is toegevoegd aan het Azure AD DS resource-forest met Extern bureaublad en uw referenties voor de Azure AD DS-beheerder. Als er een verificatie op netwerkniveau-fout (NLA) wordt weer geven, controleert u of het gebruikers account dat u hebt gebruikt geen domein gebruikers account is.

    > [!NOTE]
    > Als u een beveiligde verbinding wilt maken met uw Vm's die zijn toegevoegd aan Azure AD Domain Services, kunt u de [Azure bastion host-service](https://docs.microsoft.com/azure/bastion/bastion-overview) gebruiken in ondersteunde Azure-regio's.

1. Open **Windows-instellingen**, zoek naar en selecteer **netwerk centrum**.
1. Kies de optie voor het **wijzigen van geavanceerde** instellingen voor delen.
1. Selecteer onder het **domein profiel**de optie **Bestands-en printer deling inschakelen** en **Sla de wijzigingen**op.
1. Sluit **netwerk centrum**.

#### <a name="create-a-security-group-and-add-members"></a>Een beveiligings groep maken en leden toevoegen

1. Open **Active Directory gebruikers en computers**.
1. Klik met de rechter muisknop op de domein naam, kies **Nieuw**en selecteer **organisatie-eenheid**.
1. Typ *LocalObjects*in het vak Naam en selecteer **OK**.
1. Selecteer en klik met de rechter muisknop op **LocalObjects** in het navigatie deel venster. Selecteer **Nieuw** en vervolgens **groep**.
1. Typ *FileServerAccess* in het vak **groeps naam** . Selecteer **domeingebonden**in het **groeps bereik**en klik vervolgens op **OK**.
1. Dubbel klik in het inhouds venster op **FileServerAccess**. Selecteer **leden**, kies **toevoegen**en selecteer vervolgens **locaties**.
1. Selecteer uw on-premises Active Directory in de **locatie** weergave en kies vervolgens **OK**.
1. Typ *domein gebruikers* in het vak **Geef de object namen op** . Selecteer **Namen controleren**, geef referenties op voor de on-premises Active Directory en selecteer vervolgens **OK**.

    > [!NOTE]
    > U moet referenties opgeven, omdat de vertrouwens relatie maar één manier is. Dit betekent dat gebruikers van de Azure-AD DS geen toegang krijgen tot bronnen of kunnen zoeken naar gebruikers of groepen in het vertrouwde (on-premises) domein.

1. De groep **domein gebruikers** uit uw on-premises Active Directory moet lid zijn van de groep **FileServerAccess** . Selecteer **OK** om de groep op te slaan en het venster te sluiten.

#### <a name="create-a-file-share-for-cross-forest-access"></a>Een bestands share maken voor toegang tussen forests

1. Maak op de Windows Server-VM lid van de Azure AD DS-bron-forest een map en geef een naam op, bijvoorbeeld *CrossForestShare*.
1. Klik met de rechter muisknop op de map en kies **Eigenschappen**.
1. Selecteer het tabblad **beveiliging** en klik vervolgens op **bewerken**.
1. Selecteer in het dialoog venster *machtigingen voor CrossForestShare* **toevoegen**.
1. Typ *FileServerAccess* in **Geef de object namen op**en selecteer vervolgens **OK**.
1. Selecteer *FileServerAccess* in de lijst **groepen of gebruikers namen** . Kies in de lijst **machtigingen voor FileServerAccess** de optie *toestaan* voor de machtigingen **wijzigen** en **schrijven** en selecteer vervolgens **OK**.
1. Selecteer het tabblad **delen** en kies vervolgens **Geavanceerd delen...**
1. Kies **deze map delen**en voer een naam in voor het onthouden van de bestands share in **share naam** , zoals *CrossForestShare*.
1. Selecteer **Machtigingen**. Klik in de lijst **machtigingen voor iedereen** op **toestaan** voor de **wijzigings** machtiging.
1. Selecteer **OK** twee keer en vervolgens **sluiten**.

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>Verificatie tussen forests valideren voor een resource

1. Meld u aan op een Windows-computer die is toegevoegd aan uw on-premises Active Directory met behulp van een gebruikers account uit uw on-premises Active Directory.
1. Gebruik **Windows Verkenner**om verbinding te maken met de share die u hebt gemaakt met behulp van de volledig gekwalificeerde hostnaam en de share, zoals `\\fs1.aadds.contoso.com\CrossforestShare`.
1. U kunt de schrijf machtiging controleren door met de rechter muisknop in de map te selecteren, op **Nieuw**te kiezen en vervolgens **tekst document**te selecteren. Gebruik de standaard naam **nieuw tekst document**.

    Als de schrijf machtigingen juist zijn ingesteld, wordt er een nieuw tekst document gemaakt. De volgende stappen worden vervolgens naar wens geopend, bewerkt en verwijderd.
1. Als u de machtiging lezen wilt valideren, opent u **nieuw tekst document**.
1. Als u de machtiging wijzigen wilt valideren, voegt u tekst toe aan het bestand en sluit u **Klad blok**. Wanneer u wordt gevraagd om de wijzigingen op te slaan, kiest u **Opslaan**.
1. Als u de machtiging verwijderen wilt valideren, selecteert u **nieuw tekst document** en kiest u **verwijderen**. Kies **Ja** om het verwijderen van het bestand te bevestigen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * DNS configureren in een on-premises AD DS omgeving ter ondersteuning van Azure AD DS-connectiviteit
> * Een eenrichtings vertrouwensrelatie voor een inkomend forest in een on-premises AD DS omgeving maken
> * Een eenrichtings vertrouwensrelatie voor uitgaande forests maken in azure AD DS
> * De vertrouwens relatie voor verificatie en toegang tot bronnen testen en valideren

Zie [Wat zijn resource-forests?][concepts-forest] en [Hoe kunnen forest-vertrouwens relaties werken in azure AD DS?][concepts-trust] voor meer conceptuele informatie over forest-typen in azure AD DS.

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
