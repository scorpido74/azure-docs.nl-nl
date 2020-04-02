---
title: Zelfstudie - Een forestvertrouwensrelatie maken in Azure AD Domain Services | Microsoft Documenten
description: Meer informatie over het maken van een eenrichtingsbos voor een on-premises AD DS-domein in de Azure-portal voor Azure AD Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: eb96cb32c05d2ba3fbd38e72c16540d947436117
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519075"
---
# <a name="tutorial-create-an-outbound-forest-trust-to-an-on-premises-domain-in-azure-active-directory-domain-services-preview"></a>Zelfstudie: Een uitgaande forestvertrouwensrelatie maken voor een on-premises domein in Azure Active Directory Domain Services (voorbeeld)

In omgevingen waar u wachtwoordhashes niet synchroniseren, of als u gebruikers hebt die zich uitsluitend aanmelden met smartcards, zodat ze hun wachtwoord niet kennen, u een bronforest gebruiken in Azure Active Directory Domain Services (AD DS). Een resourceforest gebruikt een eenrichtingsvertrouwensrelatie van Azure AD DS naar een of meer on-premises AD DS-omgevingen. Met deze vertrouwensrelatie kunnen gebruikers, toepassingen en computers zich verifiëren ten opzichte van een on-premises domein uit het beheerde Azure AD DS-domein. Azure AD DS-bronforests zijn momenteel in preview.

![Diagram van forestvertrouwensrelatie van Azure AD DS naar on-premises AD DS](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * DNS configureren in een on-premises AD DS-omgeving om Azure AD DS-connectiviteit te ondersteunen
> * Een inbound forest-vertrouwensrelatie in één richting maken in een on-premises AD DS-omgeving
> * Een eenrichtingsvertrouwensrelatie in Azure AD DS maken
> * De vertrouwensrelatie voor verificatie en brontoegang testen en valideren

Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt voltooien, hebt u de volgende bronnen en bevoegdheden nodig:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)aan .
* Een Azure Active Directory-tenant die is gekoppeld aan uw abonnement, gesynchroniseerd met een on-premises directory of een map met alleen wolken.
    * Maak indien nodig [een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account.][associate-azure-ad-tenant]
* Een beheerd Azure Directory Domain Services-domein dat is gemaakt met behulp van een bronforest en geconfigureerd in uw Azure AD-tenant.
    * Maak en configureer indien nodig [een Azure Active Directory Domain Services-exemplaar][create-azure-ad-ds-instance-advanced].
    
    > [!IMPORTANT]
    > Zorg ervoor dat u een door Azure AD DS beheerd domein maakt met behulp van een *resourceforest.* Met de standaardoptie wordt een *gebruikersforest* gemaakt. Alleen resourceforests kunnen vertrouwensrelaties creëren voor AD DS-omgevingen op het gebied van on-prem. U moet ook een minimum aan *Enterprise* SKU gebruiken voor uw beheerde domein. Wijzig indien nodig [de SKU voor een door Azure AD DS beheerd domein][howto-change-sku].

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

In deze zelfstudie maakt en configureert u de uitgaande forestvertrouwensrelatie van Azure AD DS met behulp van de Azure-portal. Meld u eerst aan bij de [Azure-portal](https://portal.azure.com)om aan de slag te gaan.

## <a name="networking-considerations"></a>Aandachtspunten voor netwerken

Het virtuele netwerk dat het Azure AD DS-bronforest host, heeft netwerkconnectiviteit met uw on-premises Active Directory nodig. Toepassingen en services hebben ook netwerkconnectiviteit nodig voor het virtuele netwerk dat het Azure AD DS-bronforest host. De netwerkverbinding met het Azure AD DS-bronforest moet altijd ingeschakeld en stabiel zijn, anders kunnen gebruikers zich niet verifiëren of toegang geven tot bronnen.

Voordat u een forestvertrouwensrelatie in Azure AD DS configureert, moet u ervoor zorgen dat uw netwerken tussen Azure en on-premises omgeving aan de volgende vereisten voldoen:

* Gebruik privé-IP-adressen. Vertrouw niet op DHCP met dynamische IP-adrestoewijzing.
* Vermijd overlappende IP-adresruimten om virtuele netwerkpeering en routering mogelijk te maken om succesvol te communiceren tussen Azure en on-premises.
* Een Virtueel Azure-netwerk heeft een gateway-subnet nodig om een [Azure site-to-site (S2S) VPN-][vpn-gateway] of [ExpressRoute-verbinding][expressroute] te configureren
* Maak subnetten met voldoende IP-adressen om uw scenario te ondersteunen.
* Zorg ervoor dat Azure AD DS een eigen subnet heeft, deel dit virtuele netwerksubnet niet met toepassings-VM's en -services.
* Peered virtuele netwerken zijn niet transitief.
    * Azure virtual network peerings must be created between all virtual networks you want to use the Azure AD DS resource forest trust to the on-premises AD DS environment.
* Zorg voor continue netwerkconnectiviteit met uw on-premises Active Directory-forest. Gebruik geen on-demand verbindingen.
* Controleer of er een continue naamomzetting (DNS) is tussen de naam van uw Azure AD DS-bronforest en de naam van uw on-premises Active Directory-forest.

## <a name="configure-dns-in-the-on-premises-domain"></a>DNS configureren in het on-premises domein

Als u het beheerde Azure AD DS-domein vanuit de on-premises omgeving correct wilt oplossen, moet u mogelijk doorstuurgers toevoegen aan de bestaande DNS-servers. Als u de on-premises omgeving niet hebt geconfigureerd om te communiceren met het beheerde Azure AD DS-domein, voert u de volgende stappen uit van een beheerwerkstation voor het on-premises AD DS-domein:

1. Selecteer **Start | Administratieve instrumenten | DNS (DNS)**
1. Dns-server met de rechtermuisknop selecteren, zoals *myAD01*, selecteer **Eigenschappen**
1. Kies **Doorstuurers**en **bewerk vervolgens** om extra forwarders toe te voegen.
1. Voeg de IP-adressen van het beheerde Azure AD DS-domein toe, zoals *10.0.2.4* en *10.0.2.5*.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>Inbound forest vertrouwen in het on-premises domein maken

Het on-premises AD DS-domein heeft een binnenkomende forestvertrouwensrelatie nodig voor het beheerde Azure AD DS-domein. Deze vertrouwensrelatie moet handmatig worden gemaakt in het on-premises AD DS-domein, maar kan niet worden gemaakt vanuit de Azure-portal.

Voer de volgende stappen uit van een beheerwerkstation voor het on-premises AD DS-domein om binnenkomende vertrouwensrelaties op het on-premises AD DS-domein te configureren:

1. Selecteer **Start | Administratieve instrumenten | Active Directory- domeinen en vertrouwensrelaties**
1. Domein met de rechtermuisknop selecteren, zoals *onprem.contoso.com*, **eigenschappen** selecteren
1. Tabblad **Vertrouwensrelaties** kiezen en vervolgens **Nieuwe vertrouwensrelatie**
1. Voer naam in op Azure AD DS-domeinnaam, zoals *aaddscontoso.com*en selecteer **Volgende**
1. Selecteer de optie om een **forestvertrouwensrelatie**te maken en vervolgens een **enkele manier te maken: binnenkomende** vertrouwensrelatie.
1. Kies ervoor om alleen de vertrouwensrelatie voor dit domein te **maken.** In de volgende stap maakt u de vertrouwensrelatie in de Azure-portal voor het beheerde Azure AD DS-domein.
1. Kies ervoor om **forestbrede verificatie**te gebruiken en voer vervolgens een vertrouwenswachtwoord in en bevestig deze. Hetzelfde wachtwoord wordt ook ingevoerd in de Azure-portal in de volgende sectie.
1. Stap door de volgende paar vensters met standaardopties en kies vervolgens de optie **nee, bevestig het uitgaande vertrouwen niet.**
1. Selecteer **Voltooien**

## <a name="create-outbound-forest-trust-in-azure-ad-ds"></a>Uitgaande forestvertrouwensrelatie in Azure AD DS

Nu het on-premises AD DS-domein is geconfigureerd om het beheerde Azure AD DS-domein op te lossen en een binnenkomende forestvertrouwensrelatie is gemaakt, wordt nu de uitgaande forestvertrouwensrelatie gemaakt. Deze uitgaande forestvertrouwensrelatie voltooit de vertrouwensrelatie tussen het on-premises AD DS-domein en het beheerde Azure AD DS-domein.

Voer de volgende stappen uit om de uitgaande vertrouwensrelatie voor het beheerde Azure AD DS-domein in de Azure-portal te maken:

1. Selecteer in de Azure-portal **Azure AD Domain Services**en selecteer deze vervolgens uw beheerde domein, zoals *aaddscontoso.com*
1. Selecteer In het menu aan de linkerkant van het door Azure AD DS beheerde domein de optie **Vertrouwensrelaties**en kies je voor + Een vertrouwensrelatie **toevoegen.**

   > [!NOTE]
   > Als u de **menuoptie Vertrouwensrelaties** niet ziet, schakelt u onder **Eigenschappen** voor het *foresttype in.* Alleen *resourceforests* kunnen vertrouwensrelaties creëren. Als het foresttype *Gebruiker*is, u geen vertrouwensrelaties maken. Er is momenteel geen manier om het foresttype van een door Azure AD DS beheerd domein te wijzigen. U moet het beheerde domein verwijderen en opnieuw maken als resourceforest.

1. Voer een weergavenaam in die uw vertrouwensrelatie identificeert en vervolgens de on-premises vertrouwde DNS-naam van het forest, zoals *onprem.contoso.com*
1. Geef hetzelfde vertrouwenswachtwoord op dat is gebruikt bij het configureren van de binnenkomende forestvertrouwensrelatie voor het on-premises AD DS-domein in de vorige sectie.
1. Zorg voor ten minste twee DNS-servers voor het on-premises AD DS-domein, zoals *10.1.1.4* en *10.1.1.5*
1. Wanneer u klaar **bent, slaat u** de uitgaande forestvertrouwensrelatie op

    ![Uitgaande forestvertrouwensrelatie in de Azure-portal maken](./media/tutorial-create-forest-trust/portal-create-outbound-trust.png)

## <a name="validate-resource-authentication"></a>Verificatie van resources valideren

Met de volgende algemene scenario's u valideren dat gebruikers correct worden geverifieerd en dat de toegang tot bronnen correct wordt geverifieerd:

* [On-premises gebruikersverificatie vanuit het Azure AD DS-bronforest](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [Toegang tot bronnen in het Azure AD DS-bronforest met behulp van on-premises gebruiker](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [Delen van bestanden en printers inschakelen](#enable-file-and-printer-sharing)
    * [Een beveiligingsgroep maken en leden toevoegen](#create-a-security-group-and-add-members)
    * [Een bestandsshare maken voor toegang tot meerdere bossen](#create-a-file-share-for-cross-forest-access)
    * [Verificatie voor het hele forest valideren naar een resource](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>On-premises gebruikersverificatie vanuit het Azure AD DS-bronforest

U moet de virtuele machine van Windows Server hebben aangesloten bij het Azure AD DS-brondomein. Gebruik deze virtuele machine om uw on-premises gebruiker te testen kan verifiëren op een virtuele machine.

1. Maak verbinding met de Windows Server-VM die is verbonden met het Azure AD DS-bronforest met [Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview) en uw Azure AD DS-beheerdersreferenties.
1. Open een opdrachtprompt `whoami` en gebruik de opdracht om de gedistingeerde naam van de momenteel geverifieerde gebruiker weer te geven:

    ```console
    whoami /fqdn
    ```

1. Gebruik `runas` de opdracht om te verifiëren als gebruiker uit het on-premises domein. Vervang in de `userUpn@trusteddomain.com` volgende opdracht het UPN van een gebruiker uit het vertrouwde on-premises domein. De opdracht vraagt u om het wachtwoord van de gebruiker:

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. Als de verificatie succesvol is, wordt er een nieuwe opdrachtprompt geopend. De titel van de `running as userUpn@trusteddomain.com`nieuwe opdrachtprompt bevat .
1. Gebruik `whoami /fqdn` in de nieuwe opdrachtprompt de onderscheiden naam van de geverifieerde gebruiker vanuit de on-premises Active Directory.

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>Toegang tot bronnen in het Azure AD DS-bronforest met behulp van on-premises gebruiker

Met de Windows Server VM die is gekoppeld aan het Azure AD DS-bronforest, u het scenario testen waarin gebruikers toegang hebben tot bronnen die worden gehost in het bronforest wanneer ze zich verifiëren vanaf computers in het on-premises domein met gebruikers uit het on-premises domein. In de volgende voorbeelden ziet u hoe u verschillende veelvoorkomende scenario's maken en testen.

#### <a name="enable-file-and-printer-sharing"></a>Delen van bestanden en printers inschakelen

1. Maak verbinding met de Windows Server-VM die is verbonden met het Azure AD DS-bronforest met [Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview) en uw Azure AD DS-beheerdersreferenties.

1. Open **Windows-instellingen**en selecteer **netwerk- en deelcentrum**.
1. Kies de optie voor **Geavanceerde instellingen voor delen wijzigen.**
1. Selecteer onder het **domeinprofiel**de optie **Het delen van bestanden en printers inschakelen** en vervolgens Wijzigingen **opslaan**.
1. **Netwerk- en deelcentrum sluiten**.

#### <a name="create-a-security-group-and-add-members"></a>Een beveiligingsgroep maken en leden toevoegen

1. Open **Active Directory: gebruikers en computers**.
1. Selecteer de domeinnaam met de rechtermuisknop, kies **Nieuw**en selecteer **vervolgens Organisatie-eenheid**.
1. Typ *Lokaal objecten*in het vak Naam en selecteer **OK**.
1. Selecteer en klik met de rechtermuisknop op **Lokale objecten** in het navigatiedeelvenster. Selecteer **Nieuw** en vervolgens **Groeperen**.
1. Typ *FileServerAccess* in het vak **Groepsnaam.** Selecteer Voor het **groepsbereik** **domeinlokaal**en kies **OK**.
1. Dubbelklik in het inhoudsvenster op **FileServerAccess**. Selecteer **Leden,** kies **voor Toevoegen**en selecteer Vervolgens **Locaties**.
1. Selecteer uw on-premises Active Directory in de **locatieweergave** en kies **OK**.
1. Typ *Domeingebruikers* in het vak **De objectnamen invoeren om te selecteren.** Selecteer **Namen controleren**, referenties voor de on-premises Active Directory geven en selecteer **OK**.

    > [!NOTE]
    > U moet referenties opgeven omdat de vertrouwensrelatie slechts één manier is. Dit betekent dat gebruikers van het Azure AD DS geen toegang hebben tot bronnen of geen toegang hebben tot gebruikers of groepen in het vertrouwde (on-premises) domein.

1. De groep **Domeingebruikers** uit uw on-premises Active Directory moet lid zijn van de **fileServerAccess-groep.** Selecteer **OK** om de groep op te slaan en het venster te sluiten.

#### <a name="create-a-file-share-for-cross-forest-access"></a>Een bestandsshare maken voor toegang tot meerdere bossen

1. Maak op de Windows Server VM die is verbonden met het Azure AD DS-bronforest een map en geef een naam op, zoals *CrossForestShare.*
1. Selecteer de map met de rechtermuisknop en kies **Eigenschappen**.
1. Selecteer het tabblad **Beveiliging** en kies **Bewerken**.
1. Selecteer In het dialoogvenster *Machtigingen voor CrossForestShare* de optie **Toevoegen**.
1. Typ *FileServerAccess* in **Enter the object names to select**, selecteer **OK**.
1. Selecteer *FileServerAccess* in de lijst **Groepen of gebruikersnamen.** Kies in de lijst **Machtigingen voor FileServerAccess** de optie *Toestaan* voor de machtigingen **wijzigen** en **schrijven** en selecteer **VERVOLGENS OK**.
1. Selecteer het tabblad **Delen** en kies **Geavanceerd delen...**
1. Kies **Deze map delen**en voer vervolgens een gedenkwaardige naam in voor het bestandsaandeel in Share **name** zoals *CrossForestShare*.
1. Selecteer **Machtigingen**. Kies Toestaan voor de machtiging **Wijzigen** **in** de lijst Machtigingen **voor iedereen.**
1. Selecteer **OK** twee keer en sluit **vervolgens .**

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>Verificatie voor het hele forest valideren naar een resource

1. Meld u aan bij een Windows-computer die is aangesloten bij uw on-premises Active Directory met behulp van een gebruikersaccount vanuit uw on-premises Active Directory.
1. Maak met **Windows Verkenner**verbinding met het deel dat u hebt `\\fs1.aaddscontoso.com\CrossforestShare`gemaakt met de volledig gekwalificeerde hostnaam en het aandeel zoals .
1. Als u de schrijfmachtiging wilt valideren, selecteert u met de rechtermuisknop in de map de optie **Nieuw**en selecteert u **Tekstdocument**. Gebruik de standaardnaam **Nieuw tekstdocument**.

    Als de schrijfmachtigingen correct zijn ingesteld, wordt een nieuw tekstdocument gemaakt. De volgende stappen openen, bewerken en verwijderen het bestand naar gelang van het geval.
1. Als u de leesmachtiging wilt valideren, opent u **Nieuw tekstdocument**.
1. Als u de machtiging wijzigen wilt valideren, voegt u tekst toe aan het bestand en sluit **u Kladblok**. Wanneer u wordt gevraagd wijzigingen op te slaan, kiest **u Opslaan**.
1. Als u de machtiging voor verwijderen wilt valideren, selecteert u **Nieuw tekstdocument** met de rechtermuisknop en kiest u **Verwijderen**. Kies **Ja** om het verwijderen van bestanden te bevestigen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * DNS configureren in een on-premises AD DS-omgeving om Azure AD DS-connectiviteit te ondersteunen
> * Een inbound forest-vertrouwensrelatie in één richting maken in een on-premises AD DS-omgeving
> * Een eenrichtingsvertrouwensrelatie in Azure AD DS maken
> * De vertrouwensrelatie voor verificatie en brontoegang testen en valideren

Zie [Wat zijn resourceforests voor][concepts-forest] meer conceptuele informatie over foresttypen in Azure AD [DS? En Hoe werken forestvertrouwensrelaties in Azure AD DS?][concepts-trust]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[howto-change-sku]: change-sku.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[expressroute]: ../expressroute/expressroute-introduction.md
