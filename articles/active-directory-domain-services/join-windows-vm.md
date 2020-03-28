---
title: Een Windows Server-vm deelnemen aan een beheerd domein | Microsoft Documenten
description: In deze zelfstudie leert u hoe u een virtuele machine van Windows Server aansluiten bij een beheerd beheerd Azure Directory Domain Services-domein.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 02/19/2020
ms.author: iainfou
ms.openlocfilehash: f853d6d59a4c23b7b52a2a0ba800ace58c997f6e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79481582"
---
# <a name="tutorial-join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Zelfstudie: Een virtuele windows server-machine aansluiten bij een beheerd domein

Azure Active Directory Domain Services (Azure AD DS) biedt beheerde domeinservices zoals domeinjoin, groepsbeleid, LDAP, Kerberos/NTLM-verificatie die volledig compatibel is met Windows Server Active Directory. Met een door Azure AD DS beheerd domein u functies voor domeinjoinen en beheer leveren aan virtuele machines (VM's) in Azure. In deze zelfstudie ziet u hoe u een Windows Server-vm maakt en deze vervolgens aansluit bij een door Azure AD DS beheerd domein.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Windows Server-vm maken
> * De Windows Server-vm verbinden met een virtueel Azure-netwerk
> * Deelnemen aan de VM naar het beheerde Azure AD DS-domein

Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om deze zelfstudie te voltooien, hebt u de volgende bronnen nodig:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)aan .
* Een Azure Active Directory-tenant die is gekoppeld aan uw abonnement, gesynchroniseerd met een on-premises directory of een map met alleen wolken.
    * Maak indien nodig [een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account.][associate-azure-ad-tenant]
* Een beheerd azure Directory Domain Services-domein is ingeschakeld en geconfigureerd in uw Azure AD-tenant.
    * Maak en configureer indien nodig [een Azure Active Directory Domain Services-exemplaar][create-azure-ad-ds-instance].
* Een gebruikersaccount dat deel uitmaakt van het door Azure AD DS beheerde domein.
    * Zorg ervoor dat Azure AD Connect-wachtwoordhashsynchronisatie of selfservicewachtwoordopnieuw instellen is uitgevoerd, zodat het account zich kan aanmelden bij het beheerde Azure AD DS-domein.
* Een Azure Bastion-host die is geïmplementeerd in uw virtuele Azure AD DS-netwerk.
    * Maak indien nodig [een Azure Bastion-host][azure-bastion].

Als u al een VM hebt waaraan u wilt deelnemen aan het domein, gaat u naar de sectie om lid te [worden van de VM naar het beheerde Azure AD DS-domein.](#join-the-vm-to-the-azure-ad-ds-managed-domain)

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

In deze zelfstudie maakt u een Windows Server-vm om met de Azure AD DS-beheerde domein lid te worden van uw door Azure AD DS beheerde domein. Meld u eerst aan bij de [Azure-portal](https://portal.azure.com)om aan de slag te gaan.

## <a name="create-a-windows-server-virtual-machine"></a>Een virtuele windows server-machine maken

Als u wilt zien hoe u een computer aansluiten bij een door Azure AD DS beheerd domein, maken we een Windows Server-vm. Deze VM is verbonden met een virtueel Azure-netwerk dat connectiviteit biedt met het beheerde Azure AD DS-domein. Het proces om lid te worden van een door Azure AD DS beheerd domein is hetzelfde als lid worden van een regelmatig on-premises Active Directory Domain Services-domein.

Als u al een VM hebt waaraan u wilt deelnemen aan het domein, gaat u naar de sectie om lid te [worden van de VM naar het beheerde Azure AD DS-domein.](#join-the-vm-to-the-azure-ad-ds-managed-domain)

1. Selecteer in het menu azure portal of op de **startpagina** de optie **Een bron maken**.

1. Kies Windows **Server 2016 Datacenter**vanaf aan de **slag.**

    ![Kies voor het maken van een Windows Server 2016-datacentervm in de Azure-portal](./media/join-windows-vm/select-vm-image.png)

1. Configureer in het venster **Basisbeginselen** de kerninstellingen voor de virtuele machine. Laat de standaardinstellingen voor *beschikbaarheidsopties*, *Afbeelding*en *grootte achter*.

    | Parameter            | Voorgestelde waarde   |
    |----------------------|-------------------|
    | Resourcegroep       | Een resourcegroep selecteren of maken, zoals *myResourceGroup* |
    | Naam van de virtuele machine | Voer een naam in voor de VM, zoals *myVM* |
    | Regio               | Kies de regio waarin u uw VM wilt maken, zoals *Oost-VS* |
    | Gebruikersnaam             | Voer een gebruikersnaam in voor het lokale beheerdersaccount dat u op de VM wilt maken, zoals *azureuser* |
    | Wachtwoord             | Voer een beveiligd wachtwoord in en bevestig deze vervolgens voor de lokale beheerder om op de vm te maken. Geef de referenties van een domeingebruikersaccount niet op. |

1. Standaard zijn VM's die in Azure zijn gemaakt, toegankelijk vanaf het internet via RDP. Wanneer RDP is ingeschakeld, kunnen er waarschijnlijk automatische aanmeldingsaanvallen optreden, waardoor accounts met algemene namen zoals *beheerder* of *beheerder* kunnen worden uitgeschakeld vanwege meerdere mislukte opeenvolgende aanmeldingspogingen.

    RDP mag alleen worden ingeschakeld wanneer dat nodig is, en beperkt tot een reeks geautoriseerde IP-bereiken. Deze configuratie helpt de beveiliging van de VM te verbeteren en vermindert het gebied voor mogelijke aanvallen. Of maak en gebruik een Azure Bastion-host die alleen toegang via de Azure-portal via TLS mogelijk maakt. In de volgende stap van deze zelfstudie gebruikt u een Azure Bastion-host om veilig verbinding te maken met de VM.

    Schakel vooralsnog directe RDP-verbindingen met de VM uit.

    Selecteer *Geen onder* **Openbare binnenkomende poorten**.

1. Als u klaar bent, selecteert u **Volgende: schijven**.
1. Kies in de vervolgkeuzelijst voor **het type OS-schijf** *standaard SSD*en selecteer **Volgende: Netwerken**.
1. Uw VM moet verbinding maken met een subnet van het Azure-virtuele netwerk dat kan communiceren met het subnet waaruw Door Azure AD DS-beheerde domein in is geïmplementeerd. We raden u aan dat een door Azure AD DS beheerd domein wordt geïmplementeerd in een eigen speciaal subnet. Implementeer uw VM niet in hetzelfde subnet als uw door Azure AD DS beheerde domein.

    Er zijn twee belangrijke manieren om uw VM te implementeren en verbinding te maken met een geschikt virtueel netwerksubnet:
    
    * Maak een bestaand subnet of selecteer een bestaand subnet in hetzelfde virtuele netwerk als uw door Azure AD DS beheerde domein wordt geïmplementeerd.
    * Selecteer een subnet in een virtueel Azure-netwerk dat ermee is verbonden met [azure virtual network peering.][vnet-peering]
    
    Als u een virtueel netwerksubnet selecteert dat niet is verbonden met het subnet voor uw Azure AD DS-exemplaar, u de VM niet aansluiten bij het beheerde domein. Laten we voor deze zelfstudie een nieuw subnet maken in het virtuele Azure-netwerk.

    Selecteer **in** het deelvenster Netwerken het virtuele netwerk waarin uw door Azure AD DS beheerde domein is geïmplementeerd, zoals *aaads-vnet*
1. In dit voorbeeld wordt het bestaande *aaads-subnet* weergegeven waaraan het beheerde Azure AD DS-domein is verbonden. Sluit uw VM niet aan op dit subnet. Als u een subnet voor de virtuele machine wilt maken, selecteert **u Subnetconfiguratie beheren**.

    ![Kies voor het beheren van de subnetconfiguratie in de Azure-portal](./media/join-windows-vm/manage-subnet.png)

1. Selecteer **Adresruimte**in het linkermenu van het virtuele netwerkvenster . Het virtuele netwerk wordt gemaakt met een enkele adresruimte van *10.0.1.0/24*, die wordt gebruikt door het standaardsubnet.

    Voeg een extra IP-adresbereik toe aan het virtuele netwerk. De grootte van dit adresbereik en het werkelijke IP-adresbereik dat moet worden gebruikt, is afhankelijk van andere netwerkbronnen die al zijn geïmplementeerd. Het IP-adresbereik mag niet overlappen met bestaande adresbereiken in uw Azure- of on-premises omgeving. Zorg ervoor dat u het IP-adresbereik groot genoeg maakt voor het aantal VM's dat u in het subnet verwacht te implementeren.

    In het volgende voorbeeld wordt een extra IP-adresbereik van *10.0.2.0/24* toegevoegd. Wanneer u klaar bent, selecteert **u Opslaan**.

    ![Een extra IP-adresbereik voor virtuele netwerken toevoegen aan de Azure-portal](./media/tutorial-configure-networking/add-vnet-address-range.png)

1. Selecteer vervolgens in het linkermenu van het virtuele netwerkvenster **Subnetten**en kies **+ Subnet** om een subnet toe te voegen.

1. Selecteer **+ Subnet**en voer vervolgens een naam in voor het subnet, zoals *beheer*. Geef een **ADRESbereik (CIDR-blok)** op, zoals *10.0.2.0/24*. Zorg ervoor dat dit IP-adresbereik niet overlapt met andere bestaande Azure- of on-premises adresbereiken. Laat de andere opties als standaardwaarden staan en selecteer **OK**.

    ![Een subnetconfiguratie maken in de Azure-portal](./media/join-windows-vm/create-subnet.png)

1. Het duurt een paar seconden om het subnet te maken. Zodra deze is gemaakt, selecteert u de *X* om het subnetvenster te sluiten.
1. Terug in het **deelvenster Netwerken** om een vm te maken, kiest u het subnet dat u hebt gemaakt in het vervolgkeuzemenu, zoals *beheer*. Controleer nogmaals of u het juiste subnet kiest en uw VM niet implementeert in hetzelfde subnet als uw door Azure AD DS beheerde domein.
1. Laat de andere opties als standaardwaarden achter en selecteer **Beheer**.
1. **Stel Opstartdiagnose** *in op Uit*. Laat de andere opties als standaardwaarden staan en selecteer **Controleren + maken**.
1. Controleer de VM-instellingen en selecteer **Vervolgens Maken**.

Het duurt een paar minuten om de VM te maken. De Azure-portal toont de status van de implementatie. Zodra de vm klaar is, selecteert **u Ga naar resource**.

![Ga naar de VM-bron in de Azure-portal zodra deze is gemaakt](./media/join-windows-vm/vm-created.png)

## <a name="connect-to-the-windows-server-vm"></a>Verbinding maken met de VM van Windows Server

Als u veilig verbinding wilt maken met uw VM's, gebruikt u een Azure Bastion-host. Met Azure Bastion wordt een beheerde host geïmplementeerd in uw virtuele netwerk en biedt het webgebaseerde RDP- of SSH-verbindingen met VM's. Er zijn geen openbare IP-adressen vereist voor de VM's en u hoeft geen netwerkbeveiligingsgroepregels voor extern extern extern verkeer op afstand te openen. U maakt verbinding met VM's via de Azure-portal vanuit uw webbrowser.

Voer de volgende stappen uit om een Bastion-host te gebruiken om verbinding te maken met uw VM:

1. Selecteer In het deelvenster **Overzicht** voor uw vm de optie **Verbinden**en vervolgens **Bastion**.

    ![Verbinding maken met de virtuele machine van Windows met Bastion in de Azure-portal](./media/join-windows-vm/connect-to-vm.png)

1. Voer de referenties in voor uw vm die u in de vorige sectie hebt opgegeven en selecteer **Verbinding maken**.

   ![Verbinding maken via de Bastion-host in de Azure-portal](./media/join-windows-vm/connect-to-bastion.png)

Laat uw webbrowser indien nodig pop-ups openen zodat de Bastion-verbinding wordt weergegeven. Het duurt een paar seconden om de verbinding met uw VM te maken.

## <a name="join-the-vm-to-the-azure-ad-ds-managed-domain"></a>Deelnemen aan de VM naar het beheerde Azure AD DS-domein

Nu de VM is gemaakt en een webgebaseerde RDP-verbinding is gemaakt met Azure Bastion, gaan we nu naar de virtuele windows server-machine in het beheerde Azure AD DS-domein. Dit proces is hetzelfde als een computer die verbinding maakt met een regelmatig on-premises Active Directory Domain Services-domein.

1. Als **Serverbeheer** niet standaard wordt geopend wanneer u zich aanmeldt bij de vm, selecteert u het menu **Start** en kiest u **Serverbeheer**.
1. Selecteer **Lokale server**in het linkerdeelvenster van het venster **Serverbeheer** . Kies **Werkgroep**onder **Eigenschappen** in het rechterdeelvenster .

    ![Serverbeheer openen op de VM en de eigenschap werkgroep bewerken](./media/join-windows-vm/server-manager.png)

1. Selecteer wijzigen **om** lid te worden van het beheerde Azure AD DS-domein in het venster **Systeemeigenschappen.**

    ![Kiezen om de werkgroep- of domeineigenschappen te wijzigen](./media/join-windows-vm/change-domain.png)

1. Geef in het vak **Domein** de naam op van uw door Azure AD DS beheerde domein, zoals *aaddscontoso.com,* en selecteer **OK**.

    ![Het beheerde Azure AD DS-domein opgeven om lid te worden](./media/join-windows-vm/join-domain.png)

1. Voer domeinreferenties in om lid te worden van het domein. Gebruik de referenties voor een gebruiker die deel uitmaakt van het door Azure AD DS beheerde domein. Het account moet deel uitmaken van het Azure AD DS-beheerde domein of Azure AD-tenant - accounts van externe mappen die zijn gekoppeld aan uw Azure AD-tenant kunnen niet correct worden geverifieerd tijdens het domein-joinproces. Accountgegevens kunnen op een van de volgende manieren worden opgegeven:

    * **UPN-indeling** (aanbevolen) - Voer het upn-achtervoegsel (user principal name) voor het gebruikersaccount in, zoals geconfigureerd in Azure AD. Het UPN-achtervoegsel van de *gebruikerscontosoadmin* is `contosoadmin@aaddscontoso.onmicrosoft.com`bijvoorbeeld . Er zijn een paar veelvoorkomende use-cases waarbij de UPN-indeling betrouwbaar kan worden gebruikt om zich aan te melden bij het domein in plaats van de *SAMAccountName-indeling:*
        * Als het UPN-voorvoegsel van een gebruiker lang is, zoals *denaam deehasareallylong,* kan de *SAMAccountName* automatisch worden gegenereerd.
        * Als meerdere gebruikers hetzelfde UPN-voorvoegsel in uw Azure AD-tenant hebben, zoals *dee,* wordt hun *SAMAccountName-indeling* mogelijk automatisch gegenereerd.
    * **SAMAccountName-indeling** - Voer de accountnaam in de *SAMAccountName-indeling* in. De *SAMAccountName* van *gebruikerscontosoadmin* is `AADDSCONTOSO\contosoadmin`bijvoorbeeld .

1. Het duurt enkele seconden om lid te worden van het beheerde Azure AD DS-domein. Als u klaar bent, verwelkomt het volgende bericht u in het domein:

    ![Welkom in het domein](./media/join-windows-vm/join-domain-successful.png)

    Selecteer **OK** om door te gaan.

1. Als u het proces wilt voltooien om lid te worden van het beheerde Azure AD DS-domein, start u de VM opnieuw.

> [!TIP]
> U een VM met PowerShell gebruiken met de cmdlet [Invoegcomputer.][add-computer] In het volgende voorbeeld wordt het *AADDSCONTOSO-domein* samengevoegd en wordt de VM opnieuw gestart. Voer de referenties in voor een gebruiker die deel uitmaakt van het beheerde Azure AD DS-domein:
>
> `Add-Computer -DomainName AADDSCONTOSO -Restart`
>
> Als u een VM wilt verbinden zonder verbinding te maken en de verbinding handmatig wilt configureren, u de cmdlet Azure PowerShell van [Set-AzVmAdDomainExtension][set-azvmaddomainextension] gebruiken.

Zodra de Windows Server VM opnieuw is gestart, worden alle beleidsregels die worden toegepast in het beheerde Azure AD DS-domein naar de VM gepusht. U zich nu ook aanmelden bij de Windows Server VM met behulp van de juiste domeinreferenties.

## <a name="clean-up-resources"></a>Resources opschonen

In de volgende zelfstudie gebruikt u deze Windows Server VM om de beheerhulpprogramma's te installeren waarmee u het beheervan het beheerde Azure AD DS-domein beheren. Als u niet verder wilt gaan in deze zelfstudiereeks, bekijkt u de volgende stappen voor het verwijderen [van de VM.](#delete-the-vm) Anders [gaat u verder met de volgende zelfstudie.](#next-steps)

### <a name="un-join-the-vm-from-azure-ad-ds-managed-domain"></a>De deelnemen aan de VM loskoppelen van het beheerde Azure AD DS-domein

Als u de VM uit het beheerde Azure AD DS-domein wilt verwijderen, voert u de stappen opnieuw door om [de VM aan een domein toe te voegen.](#join-the-vm-to-the-azure-ad-ds-managed-domain) In plaats van lid te worden van het beheerde Azure AD DS-domein, kiest u ervoor lid te worden van een werkgroep, zoals de *standaardwerkgroep*. Nadat de VM opnieuw is opgestart, wordt het computerobject verwijderd uit het beheerde Azure AD DS-domein.

Als u [de vm verwijdert](#delete-the-vm) zonder de deelname uit het domein te verwijderen, blijft er een verweesd computerobject achter in Azure AD DS.

### <a name="delete-the-vm"></a>De VM verwijderen

Als u deze Windows Server VM niet gaat gebruiken, verwijdert u de VM met de volgende stappen:

1. Selecteer **Resourcegroepen** in het linkermenu
1. Kies uw resourcegroep, zoals *myResourceGroup*.
1. Kies uw VM, zoals *myVM,* en selecteer **Verwijderen**. Selecteer **Ja** om de verwijdering van de bron te bevestigen. Het duurt een paar minuten om de VM te verwijderen.
1. Wanneer de VM wordt verwijderd, selecteert u de OS-schijf, de netwerkinterfacekaart en alle andere bronnen met het *myVM-voorvoegsel* en verwijdert u deze.

## <a name="troubleshoot-domain-join-issues"></a>Problemen met domeinjoinproblemen oplossen

De Windows Server VM moet met succes lid worden van het door Azure AD DS beheerde domein, net zoals een gewone on-premises computer lid wordt van een Active Directory Domain Services-domein. Als de Windows Server-vm niet kan deelnemen aan het door Azure AD DS beheerde domein, geeft dit aan dat er een probleem is met connectiviteit of referenties. Controleer de volgende secties voor probleemoplossing om met succes lid te worden van het beheerde domein.

### <a name="connectivity-issues"></a>Connectiviteitsproblemen

Als u geen prompt ontvangt waarin om referenties wordt gevraagd om lid te worden van het domein, is er een verbindingsprobleem. De VM kan het door Azure AD DS beheerde domein op het virtuele netwerk niet bereiken.

Nadat u elk van deze stappen voor het oplossen van problemen hebt geprobeerd, probeert u de Windows Server-vm opnieuw aan te sluiten bij het beheerde domein.

* Controleer of de VM is verbonden met hetzelfde virtuele netwerk waarin Azure AD DS is ingeschakeld of een peered-netwerkverbinding heeft.
* Probeer de DNS-domeinnaam van het beheerde `ping aaddscontoso.com`domein te pingen, zoals .
    * Als de ping-aanvraag mislukt, probeert u de IP-adressen `ping 10.0.0.4`voor het beheerde domein te pingen, zoals . Het IP-adres voor uw omgeving wordt weergegeven op de pagina *Eigenschappen* wanneer u het door Azure AD DS beheerde domein selecteert in uw lijst met Azure-bronnen.
    * Als u het IP-adres pingen, maar niet het domein, is DNS mogelijk onjuist geconfigureerd. Controleer of de IP-adressen van het beheerde domein zijn geconfigureerd als DNS-servers voor het virtuele netwerk.
* Probeer de DNS resolver-cache op de `ipconfig /flushdns` virtuele machine door te spoelen met behulp van de opdracht.

### <a name="credentials-related-issues"></a>Problemen met referenties

Als u een prompt ontvangt waarin om referenties wordt gevraagd om lid te worden van het domein, maar vervolgens een fout optreedt nadat u deze referenties hebt ingevoerd, kan de VM verbinding maken met het beheerde Azure AD DS-domein. De referenties die u hebt opgegeven, laten de VM vervolgens niet deelnemen aan het beheerde Azure AD DS-domein.

Nadat u elk van deze stappen voor het oplossen van problemen hebt geprobeerd, probeert u de Windows Server-vm opnieuw aan te sluiten bij het beheerde domein.

* Zorg ervoor dat het opgegeven gebruikersaccount deel uitmaakt van het door Azure AD DS beheerde domein.
* Controleer of het account deel uitmaakt van het door Azure AD DS beheerde domein of Azure AD-tenant. Accounts van externe mappen die zijn gekoppeld aan uw Azure AD-tenant, kunnen niet correct verifiëren tijdens het domeinjoinproces.
* Probeer de UPN-indeling te gebruiken `contosoadmin@aaddscontoso.onmicrosoft.com`om referenties op te geven, zoals . Als er veel gebruikers zijn met hetzelfde UPN-voorvoegsel in uw tenant of als uw UPN-voorvoegsel te lang is, kan de *SAMAccountName* voor uw account automatisch worden gegenereerd. In deze gevallen kan de *SAMAccountName-indeling* voor uw account afwijken van wat u verwacht of gebruikt in uw on-premises domein.
* Controleer of u [wachtwoordsynchronisatie][password-sync] hebt ingeschakeld voor uw beheerde domein. Zonder deze configuratiestap zijn de vereiste wachtwoordhashes niet aanwezig in het beheerde Azure AD DS-domein om uw aanmeldingspoging correct te verifiëren.
* Wacht tot de wachtwoordsynchronisatie is voltooid. Wanneer het wachtwoord van een gebruikersaccount wordt gewijzigd, wordt het wachtwoord van Azure AD automatisch bijgewerkt in Azure AD DS. Het duurt enige tijd voordat het wachtwoord beschikbaar is voor gebruik van domein-join.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een Windows Server-vm maken
> * Verbinding maken met de Windows Server-vm met een virtueel Azure-netwerk
> * Deelnemen aan de VM naar het beheerde Azure AD DS-domein

Als u uw door Azure AD DS beheerdomein wilt beheren, configureert u een beheer-VM met het Active Directory-beheercentrum (ADAC).

> [!div class="nextstepaction"]
> [Beheerhulpprogramma's installeren op een beheer-VM](tutorial-create-management-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[password-sync]: active-directory-ds-getting-started-password-sync.md
[add-computer]: /powershell/module/microsoft.powershell.management/add-computer
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[set-azvmaddomainextension]: /powershell/module/az.compute/set-azvmaddomainextension
