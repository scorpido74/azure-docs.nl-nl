---
title: Een Windows Server-VM toevoegen aan een beheerd domein | Microsoft Docs
description: In deze zelf studie leert u hoe u een virtuele Windows Server-machine kunt toevoegen aan een Azure Active Directory Domain Services beheerd domein.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 02/19/2020
ms.author: iainfou
ms.openlocfilehash: 05705d14db336b15a6ddf2317f9e69464c8e575b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78378544"
---
# <a name="tutorial-join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Zelf studie: een virtuele Windows Server-machine toevoegen aan een beheerd domein

Azure Active Directory Domain Services (Azure AD DS) biedt beheerde domein Services, zoals domein deelname, groeps beleid, LDAP, Kerberos/NTLM-verificatie die volledig compatibel is met Windows Server Active Directory. Met een door Azure AD DS beheerd domein, kunt u domein lidmaatschaps functies en-beheer bieden voor virtuele machines (Vm's) in Azure. In deze zelf studie leert u hoe u een virtuele Windows Server-machine maakt en deze koppelt aan een door Azure AD DS beheerd domein.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Windows Server-VM maken
> * De virtuele Windows Server-machine verbinden met een virtueel Azure-netwerk
> * De virtuele machine toevoegen aan het beheerde domein van Azure AD DS

Als u nog geen abonnement op Azure hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van deze zelf studie hebt u de volgende resources nodig:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory Tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises Directory of een alleen-Cloud Directory.
    * Als dat nodig is, [maakt u een Azure Active Directory-Tenant][create-azure-ad-tenant] of [koppelt u een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* Een Azure Active Directory Domain Services beheerd domein ingeschakeld en geconfigureerd in uw Azure AD-Tenant.
    * Als dat nodig is, kunt [u een Azure Active Directory Domain Services-exemplaar maken en configureren][create-azure-ad-ds-instance].
* Een gebruikers account dat deel uitmaakt van het door Azure AD DS beheerde domein.
    * Zorg ervoor dat Azure AD Connect wachtwoord-hash synchronisatie of selfservice voor wachtwoord herstel is uitgevoerd zodat het account zich kan aanmelden bij Azure AD DS beheerd domein.
* Een Azure bastion-host die is geïmplementeerd in uw Azure AD DS virtuele netwerk.
    * Maak indien nodig [een Azure bastion-host][azure-bastion].

Als u al een virtuele machine hebt die lid moet worden van een domein, gaat u naar de sectie om [de VM toe te voegen aan het beheerde domein van Azure AD DS](#join-the-vm-to-the-azure-ad-ds-managed-domain).

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

In deze zelf studie maakt u een Windows Server-VM die u wilt toevoegen aan uw Azure AD DS beheerde domein met behulp van de Azure Portal. Meld u eerst aan bij de [Azure Portal](https://portal.azure.com)om aan de slag te gaan.

## <a name="create-a-windows-server-virtual-machine"></a>Een virtuele Windows Server-machine maken

Als u wilt zien hoe een computer moet worden toegevoegd aan een door Azure AD DS beheerd domein, kunt u een virtuele Windows Server-machine maken. Deze VM is verbonden met een virtueel Azure-netwerk dat verbinding maakt met het door Azure AD DS beheerde domein. Het proces om lid te worden van een Azure AD DS beheerd domein is hetzelfde als het toevoegen van een normaal on-premises Active Directory Domain Services domein.

Als u al een virtuele machine hebt die lid moet worden van een domein, gaat u naar de sectie om [de VM toe te voegen aan het beheerde domein van Azure AD DS](#join-the-vm-to-the-azure-ad-ds-managed-domain).

1. Selecteer in het menu Azure Portal of op de **Introductie** pagina de optie **een resource maken**.

1. Kies **Windows Server 2016 Data Center** **om aan**de slag te gaan.

    ![Kies voor het maken van een Windows Server 2016 Data Center VM in de Azure Portal](./media/join-windows-vm/select-vm-image.png)

1. Configureer in het venster **basis beginselen** de kern instellingen voor de virtuele machine. Behoud de standaard instellingen voor *beschikbaarheids opties*, *afbeelding*en *grootte*.

    | Parameter            | Voorgestelde waarde   |
    |----------------------|-------------------|
    | Resourcegroep       | Selecteer of maak een resource groep, zoals *myResourceGroup* |
    | Naam van de virtuele machine | Voer een naam in voor de virtuele machine, zoals *myVM* |
    | Regio               | Kies de regio waarin u uw virtuele machine wilt maken, zoals *VS-Oost* |
    | Gebruikersnaam             | Voer een gebruikers naam in voor het lokale beheerders account dat u op de virtuele machine wilt maken, zoals *azureuser* |
    | Wachtwoord             | Voer een veilig wacht woord voor de lokale beheerder in en bevestig dit om op de VM te maken. Geef geen referenties voor een domein gebruikers account op. |

1. Virtuele machines die in Azure worden gemaakt, zijn standaard toegankelijk via internet met RDP. Wanneer RDP is ingeschakeld, worden er waarschijnlijk een geautomatiseerde aanmeldings aanval uitgevoerd, waardoor accounts met veelvoorkomende namen, zoals *admin* of *Administrator* , mogelijk worden uitgeschakeld vanwege meerdere mislukte opeenvolgende aanmeldings pogingen.

    RDP moet alleen worden ingeschakeld als dit vereist is, en beperkt tot een aantal toegestane IP-bereiken. Deze configuratie helpt de beveiliging van de virtuele machine te verbeteren en vermindert het gebied voor mogelijke aanvallen. U kunt ook een Azure bastion-host maken en gebruiken die alleen toegankelijk is via de Azure Portal via SSL. In de volgende stap van deze zelf studie gebruikt u een Azure bastion-host om veilig verbinding te maken met de virtuele machine.

    Schakel voor Taan direct RDP-verbindingen met de virtuele machine uit.

    Selecteer onder **open bare binnenkomende poorten**de optie *geen*.

1. Wanneer u klaar bent, selecteert u **volgende: schijven**.
1. Kies in de vervolg keuzelijst voor het **schijf type van het besturings systeem** *Standard-SSD*en selecteer vervolgens **volgende: netwerken**.
1. Uw virtuele machine moet verbinding maken met een subnet van het virtuele netwerk van Azure dat kan communiceren met het subnet waarin uw Azure AD DS beheerde domein is geïmplementeerd. U kunt het beste een beheerd domein van Azure AD DS implementeren in een eigen toegewezen subnet. Implementeer uw virtuele machine niet in hetzelfde subnet als uw Azure AD DS beheerd domein.

    Er zijn twee hoofd manieren om uw virtuele machine te implementeren en verbinding te maken met een geschikt subnet van het virtuele netwerk:
    
    * Maak een of selecteer een bestaand subnet in hetzelfde virtuele netwerk als uw Azure AD DS beheerde domein wordt geïmplementeerd.
    * Selecteer een subnet in een virtueel Azure-netwerk dat is verbonden met [Azure Virtual Network-peering][vnet-peering].
    
    Als u een subnet van een virtueel netwerk selecteert dat niet is verbonden met het subnet voor uw Azure AD DS-exemplaar, kunt u de VM niet toevoegen aan het beheerde domein. Voor deze zelf studie maken we een nieuw subnet in het virtuele Azure-netwerk.

    Selecteer in het deel venster **netwerken** het virtuele netwerk waarin uw door Azure AD DS beheerd domein wordt geïmplementeerd, zoals *aaads-vnet*
1. In dit voor beeld wordt het bestaande *aaads-subnet* weer gegeven dat de Azure AD DS beheerde domein is verbonden met. Verbind uw virtuele machine niet met dit subnet. Als u een subnet voor de virtuele machine wilt maken, selecteert u **subnet configuratie beheren**.

    ![Kies ervoor om de configuratie van het subnet in de Azure Portal te beheren](./media/join-windows-vm/manage-subnet.png)

1. Selecteer in het menu aan de linkerkant van het venster virtueel netwerk **adres ruimte**. Het virtuele netwerk wordt gemaakt met één adres ruimte van *10.0.1.0/24*, dat wordt gebruikt door het standaard subnet.

    Voeg een extra IP-adres bereik toe aan het virtuele netwerk. De grootte van dit adres bereik en het daad werkelijke IP-adres bereik dat moet worden gebruikt, is afhankelijk van andere netwerk bronnen die al zijn geïmplementeerd. Het IP-adres bereik mag niet overlappen met de bestaande adresbereiken in uw Azure-of on-premises omgeving. Zorg ervoor dat u de grootte van het IP-adres bereik groot genoeg hebt voor het aantal Vm's dat u naar verwachting in het subnet wilt implementeren.

    In het volgende voor beeld wordt een extra IP-adres bereik van *10.0.2.0/24* toegevoegd. Wanneer u klaar bent, selecteert u **Opslaan**.

    ![Voeg een extra IP-adres bereik toe voor het virtuele netwerk in de Azure Portal](./media/tutorial-configure-networking/add-vnet-address-range.png)

1. Selecteer vervolgens in het menu aan de linkerkant van het venster virtueel netwerk **subnetten**en kies vervolgens **+ subnet** om een subnet toe te voegen.

1. Selecteer **+ subnet**en voer een naam in voor het subnet, zoals *beheer*. Geef een **adres bereik (CIDR-blok)** op, bijvoorbeeld *10.0.2.0/24*. Zorg ervoor dat dit IP-adres bereik niet overlapt met andere bestaande Azure-of on-premises adresbereiken. Wijzig de andere opties als de standaard waarden en selecteer vervolgens **OK**.

    ![Een subnet-configuratie maken in de Azure Portal](./media/join-windows-vm/create-subnet.png)

1. Het duurt een paar seconden om het subnet te maken. Zodra de app is gemaakt, selecteert u de *X* om het subnet venster te sluiten.
1. Ga terug naar het deel venster **netwerken** om een virtuele machine te maken. Kies het subnet dat u hebt gemaakt in de vervolg keuzelijst, zoals *beheer*. Zorg ervoor dat u het juiste subnet kiest en implementeer uw virtuele machine niet in hetzelfde subnet als uw door Azure AD DS beheerd domein.
1. Wijzig de andere opties als de standaard waarden en selecteer vervolgens **beheer**.
1. Stel **Diagnostische gegevens over opstarten** in op *uit*. Wijzig de andere opties als de standaard waarden en selecteer vervolgens **controleren + maken**.
1. Controleer de VM-instellingen en selecteer vervolgens **maken**.

Het duurt enkele minuten om de virtuele machine te maken. In het Azure Portal wordt de status van de implementatie weer gegeven. Zodra de VM gereed is, selecteert **u Ga naar resource**.

![Ga naar de VM-resource in de Azure Portal nadat deze is gemaakt](./media/join-windows-vm/vm-created.png)

## <a name="connect-to-the-windows-server-vm"></a>Verbinding maken met de Windows Server-VM

Gebruik een Azure bastion-host om veilig verbinding te maken met uw Vm's. Met Azure Bastion wordt een beheerde host geïmplementeerd in uw virtuele netwerk en worden RDP-of SSH-verbindingen op basis van het web op Vm's geboden. Er zijn geen open bare IP-adressen vereist voor de Vm's en u hoeft geen netwerk beveiligings groeps regels te openen voor extern extern verkeer. U maakt verbinding met virtuele machines met behulp van de Azure Portal van uw webbrowser.

Als u een bastion-host wilt gebruiken om verbinding te maken met uw virtuele machine, voert u de volgende stappen uit:

1. Selecteer in het deel venster **overzicht** voor uw virtuele machine **verbinding maken**en vervolgens **Bastion**.

    ![Verbinding maken met een virtuele Windows-machine met behulp van Bastion in de Azure Portal](./media/join-windows-vm/connect-to-vm.png)

1. Voer de referenties in voor uw virtuele machine die u in de vorige sectie hebt opgegeven en selecteer vervolgens **verbinding maken**.

   ![Verbinding maken via de bastion-host in de Azure Portal](./media/join-windows-vm/connect-to-bastion.png)

Als dat nodig is, laat u uw webbrowser pop-ups openen voor de Bastion-verbinding die moet worden weer gegeven. Het duurt een paar seconden om de verbinding met uw virtuele machine te maken.

## <a name="join-the-vm-to-the-azure-ad-ds-managed-domain"></a>De virtuele machine toevoegen aan het beheerde domein van Azure AD DS

Wanneer de virtuele machine is gemaakt en een op een webgebaseerde RDP-verbinding tot stand is gebracht met behulp van Azure Bastion, gaan we nu lid worden van de VM van Windows Server aan het beheerde domein van Azure AD DS. Dit proces is hetzelfde als een computer die verbinding maakt met een regel matig on-premises Active Directory Domain Services domein.

1. Als **Serverbeheer** standaard niet wordt geopend wanneer u zich aanmeldt bij de VM, selecteert u het menu **Start** en kiest u vervolgens **Serverbeheer**.
1. Selecteer **lokale server**in het linkerdeel venster van het venster **Serverbeheer** . Kies **werk groep**onder **Eigenschappen** in het rechterdeel venster.

    ![Open Serverbeheer op de VM en bewerk de eigenschap werk groep](./media/join-windows-vm/server-manager.png)

1. Selecteer in het venster **systeem eigenschappen** **wijzigen** om lid te worden van het beheerde domein van Azure AD DS.

    ![Kiezen of u de eigenschappen van de werk groep of het domein wilt wijzigen](./media/join-windows-vm/change-domain.png)

1. Geef in het vak **domein** de naam op van uw Azure AD DS beheerde domein, zoals *aaddscontoso.com*, en selecteer vervolgens **OK**.

    ![Opgeven welke Azure AD DS beheerde domein moet worden toegevoegd](./media/join-windows-vm/join-domain.png)

1. Voer de domein referenties in om lid te worden van het domein. Gebruik de referenties voor een gebruiker die deel uitmaakt van het door Azure AD DS beheerde domein. Het account moet deel uitmaken van de Azure AD DS beheerde domein-of Azure AD-Tenant accounts van externe mappen die zijn gekoppeld aan uw Azure AD-Tenant, kan niet correct worden geverifieerd tijdens het proces voor het samen voegen van het domein. Account referenties kunnen op een van de volgende manieren worden opgegeven:

    * **UPN-indeling** (aanbevolen): Voer het achtervoegsel van de User Principal Name (UPN) in voor het gebruikers account, zoals geconfigureerd in azure AD. Het UPN-achtervoegsel van de *contosoadmin* van de gebruiker wordt bijvoorbeeld `contosoadmin@aaddscontoso.onmicrosoft.com`. Er zijn enkele algemene gebruiks gevallen waarbij de UPN-indeling betrouwbaar kan worden gebruikt om u aan te melden bij het domein in plaats van de *SAMAccountName* -indeling:
        * Als het UPN-voor voegsel van een gebruiker lang is, zoals *deehasareallylongname*, kan de *SAMAccountName* automatisch worden gegenereerd.
        * Als meerdere gebruikers hetzelfde UPN-voor voegsel hebben in uw Azure AD-Tenant, zoals *Dee*, kan de *SAMAccountName* -indeling automatisch worden gegenereerd.
    * **SAMAccountName-indeling** : Voer de account naam in de *sAMAccountName* -indeling in. De *SAMAccountName* van de gebruiker *contosoadmin* zou bijvoorbeeld `AADDSCONTOSO\contosoadmin`zijn.

1. Het duurt een paar seconden om lid te worden van het door Azure AD DS beheerde domein. Wanneer dit is voltooid, ontvangt u het volgende bericht:

    ![Welkom bij het domein](./media/join-windows-vm/join-domain-successful.png)

    Selecteer **OK** om door te gaan.

1. Start de VM opnieuw op om het proces te volt ooien om lid te worden van het Azure AD DS beheerde domein.

> [!TIP]
> U kunt een virtuele machine met behulp van Power shell aan een domein toevoegen met de cmdlet [add-computer][add-computer] . In het volgende voor beeld wordt het domein *AADDSCONTOSO* toegevoegd en wordt de virtuele machine opnieuw opgestart. Wanneer u hierom wordt gevraagd, voert u de referenties in voor een gebruiker die deel uitmaakt van het beheerde domein van Azure AD DS:
>
> `Add-Computer -DomainName AADDSCONTOSO -Restart`
>
> Als u een domein wilt toevoegen aan een virtuele machine zonder verbinding te maken en de verbinding hand matig te configureren, kunt u de cmdlet [set-AzVmAdDomainExtension][set-azvmaddomainextension] Azure PowerShell gebruiken.

Als de virtuele machine van Windows Server opnieuw is opgestart, worden alle beleids regels die in het beheerde Azure AD DS Managed Domain worden toegepast, naar de virtuele machine gepusht. U kunt zich nu ook aanmelden bij de virtuele Windows Server-machine met behulp van de juiste domein referenties.

## <a name="clean-up-resources"></a>Resources opschonen

In de volgende zelf studie gebruikt u deze virtuele machine van Windows Server om de beheer hulpprogramma's te installeren waarmee u het door Azure AD DS beheerde domein kunt beheren. Als u niet wilt door gaan in deze reeks zelf studies, raadpleegt u de volgende stappen voor opschonen om [de virtuele machine te verwijderen](#delete-the-vm). Als dat niet het geval is, [gaat u verder met de volgende zelf studie](#next-steps).

### <a name="un-join-the-vm-from-azure-ad-ds-managed-domain"></a>De virtuele machine loskoppelen van Azure AD DS beheerde domein

Als u de virtuele machine uit de Azure AD DS beheerde domein wilt verwijderen, volgt u de stappen opnieuw om [de VM aan een domein toe te voegen](#join-the-vm-to-the-azure-ad-ds-managed-domain). In plaats van aan het beheerde domein van Azure AD DS te koppelen, kiest u om lid te worden van een werk groep, zoals de *standaardwerk groep*. Nadat de VM opnieuw is opgestart, wordt het computer object verwijderd uit het door Azure AD DS beheerde domein.

Als u [de virtuele machine verwijdert](#delete-the-vm) zonder lid te worden van het domein, blijft een zwevend computer object in azure AD DS.

### <a name="delete-the-vm"></a>De VM verwijderen

Als u deze virtuele machine van Windows Server niet gaat gebruiken, verwijdert u de virtuele machine met de volgende stappen:

1. Selecteer **resource groepen** in het menu links.
1. Kies uw resource groep, zoals *myResourceGroup*.
1. Kies uw virtuele machine, bijvoorbeeld *myVM*, en selecteer vervolgens **verwijderen**. Selecteer **Ja** om te bevestigen dat de resource wordt verwijderd. Het duurt enkele minuten om de virtuele machine te verwijderen.
1. Wanneer de virtuele machine wordt verwijderd, selecteert u de besturingssysteem schijf, netwerk interface kaart en eventuele andere resources met het *myVM-* voor voegsel en verwijdert u deze.

## <a name="troubleshoot-domain-join-issues"></a>Problemen met domein deelname oplossen

De virtuele machine van Windows Server moet worden toegevoegd aan het Azure AD DS beheerde domein, op dezelfde manier als een vaste on-premises computer zou worden toegevoegd aan een Active Directory Domain Services domein. Als de virtuele machine van Windows Server geen lid kan worden van het beheerde domein van Azure AD DS, wordt hiermee aangegeven dat er een probleem is met de connectiviteit of met betrekking tot referenties. Raadpleeg de volgende secties voor probleem oplossing om lid te worden van het beheerde domein.

### <a name="connectivity-issues"></a>Connectiviteitsproblemen

Als er geen prompt wordt weer gegeven waarin wordt gevraagd om referenties om lid te worden van het domein, is er een probleem met de verbinding. De virtuele machine kan het door Azure AD DS beheerde domein niet bereiken op het virtuele netwerk.

Na het uitvoeren van elk van deze probleemoplossings stappen probeert u de Windows Server-VM opnieuw aan het beheerde domein toe te voegen.

* Controleer of de virtuele machine is verbonden met hetzelfde virtuele netwerk als Azure AD DS is ingeschakeld of een peered netwerk verbinding heeft.
* Voer een ping uit voor de DNS-domein naam van het beheerde domein, zoals `ping aaddscontoso.com`.
    * Als de ping-aanvraag mislukt, probeert u de IP-adressen voor het beheerde domein te pingen, zoals `ping 10.0.0.4`. Het IP-adres voor uw omgeving wordt weer gegeven op de pagina *Eigenschappen* wanneer u de Azure AD DS beheerde domein selecteert in de lijst met Azure-resources.
    * Als u het IP-adres, maar niet het domein, kunt pingen, is het mogelijk dat DNS onjuist is geconfigureerd. Controleer of de IP-adressen van het beheerde domein zijn geconfigureerd als DNS-servers voor het virtuele netwerk.
* Probeer de cache van de DNS-resolver op de virtuele machine leeg te maken met behulp van de `ipconfig /flushdns` opdracht.

### <a name="credentials-related-issues"></a>Problemen met betrekking tot referenties

Als er een prompt wordt weer gegeven waarin wordt gevraagd om referenties om lid te worden van het domein, maar vervolgens een fout nadat u deze referenties hebt ingevoerd, kan de virtuele machine verbinding maken met het door Azure AD DS beheerde domein. De referenties die u hebt ingevoerd, kunnen de virtuele machine niet aan het beheerde domein van Azure AD DS toevoegen.

Na het uitvoeren van elk van deze probleemoplossings stappen probeert u de Windows Server-VM opnieuw aan het beheerde domein toe te voegen.

* Zorg ervoor dat het gebruikers account dat u opgeeft, behoort tot het door Azure AD DS beheerde domein.
* Controleer of het account deel uitmaakt van de Azure AD DS beheerde domein of Azure AD-Tenant. Accounts van externe mappen die zijn gekoppeld aan uw Azure AD-Tenant, kunnen niet correct worden geverifieerd tijdens het proces voor het samen voegen van het domein.
* Gebruik de UPN-indeling om referenties op te geven, zoals `contosoadmin@aaddscontoso.onmicrosoft.com`. Als er veel gebruikers zijn met hetzelfde UPN-voor voegsel in uw Tenant of als uw UPN-voor voegsel langer is dan lang, kan de *SAMAccountName* voor uw account automatisch worden gegenereerd. In dergelijke gevallen kan de *SAMAccountName* -indeling voor uw account afwijken van wat u verwacht of gebruikt in uw on-premises domein.
* Controleer of u [wachtwoord synchronisatie hebt ingeschakeld][password-sync] voor uw beheerde domein. Zonder deze configuratie stap zijn de vereiste wacht woord-hashes niet aanwezig in de Azure AD DS beheerde domein om uw aanmeldings poging correct te verifiëren.
* Wacht tot de wachtwoord synchronisatie is voltooid. Wanneer het wacht woord van een gebruikers account wordt gewijzigd, wordt met een automatische achtergrond synchronisatie vanuit Azure AD het wacht woord in azure AD DS bijgewerkt. Het duurt enige tijd voordat het wacht woord beschikbaar is voor gebruik door een domein.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een Windows Server-VM maken
> * Verbinding maken met de virtuele machine van Windows Server met een virtueel Azure-netwerk
> * De virtuele machine toevoegen aan het beheerde domein van Azure AD DS

Als u uw door Azure AD DS beheerd domein wilt beheren, configureert u een beheer-VM met behulp van de Active Directory-beheercentrum (ADAC).

> [!div class="nextstepaction"]
> [Beheer hulpprogramma's installeren op een beheer-VM](tutorial-create-management-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[password-sync]: active-directory-ds-getting-started-password-sync.md
[add-computer]: /powershell/module/microsoft.powershell.management/add-computer
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[set-azvmaddomainextension]: /powershell/module/az.compute/set-azvmaddomainextension
