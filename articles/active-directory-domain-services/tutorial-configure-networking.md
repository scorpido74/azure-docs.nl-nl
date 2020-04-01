---
title: Zelfstudie - Virtuele netwerken configureren voor Azure AD Domain Services | Microsoft Documenten
description: In deze zelfstudie leert u hoe u een subnet of netwerkpeering van een Azure Active Directory Domain Services maakt en configureert voor een Azure Active Directory Domain Services-instantie met behulp van de Azure-portal.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: af284e4c10487123c8c2a2105a25a2285ae0aa99
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474343"
---
# <a name="tutorial-configure-virtual-networking-for-an-azure-active-directory-domain-services-instance"></a>Zelfstudie: Virtuele netwerken configureren voor een Azure Active Directory Domain Services-exemplaar

Om connectiviteit te bieden aan gebruikers en toepassingen, wordt een azure Active Directory Domain Services (Azure AD DS)-beheerd domein geïmplementeerd in een subnet van het Azure-virtuele netwerk. Dit virtuele netwerksubnet mag alleen worden gebruikt voor de beheerde domeinbronnen die worden geleverd door het Azure-platform. Als u uw eigen VM's en toepassingen maakt, mogen deze niet worden geïmplementeerd in hetzelfde virtuele netwerksubnet. In plaats daarvan moet u uw toepassingen maken en implementeren in een afzonderlijk virtueel netwerksubnet of in een afzonderlijk virtueel netwerk dat is gekeken naar het virtuele Azure AD DS-netwerk.

In deze zelfstudie ziet u hoe u een speciaal virtueel netwerksubnet maakt en configureert of hoe u een ander netwerk peeren met het virtuele netwerk van het azure AD DS-beheerde domein.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Inzicht in de opties voor virtuele netwerkconnectiviteit voor met domeinen verbonden resources voor Azure AD DS
> * Een IP-adresbereik en extra subnet maken in het virtuele Azure AD DS-netwerk
> * Virtuele netwerkpeering configureren naar een netwerk dat gescheiden is van Azure AD DS

Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt voltooien, hebt u de volgende bronnen en bevoegdheden nodig:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)aan .
* Een Azure Active Directory-tenant die is gekoppeld aan uw abonnement, gesynchroniseerd met een on-premises directory of een map met alleen wolken.
    * Maak indien nodig [een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account.][associate-azure-ad-tenant]
* U hebt *algemene beheerdersrechten* nodig in uw Azure AD-tenant om Azure AD DS in te schakelen.
* U hebt *inzenderbevoegdheden* in uw Azure-abonnement nodig om de vereiste Azure AD DS-resources te maken.
* Een beheerd azure Directory Domain Services-domein is ingeschakeld en geconfigureerd in uw Azure AD-tenant.
    * Indien nodig maakt [en configureert][create-azure-ad-ds-instance]de eerste zelfstudie een Azure Active Directory Domain Services-exemplaar .

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

In deze zelfstudie maakt en configureert u het Azure AD DS-exemplaar met behulp van de Azure-portal. Meld u eerst aan bij de [Azure-portal](https://portal.azure.com)om aan de slag te gaan.

## <a name="application-workload-connectivity-options"></a>Opties voor toepassingsworkloadconnectiviteit

In de vorige zelfstudie is een door Azure AD DS beheerd domein gemaakt dat gebruik maakte van een aantal standaardconfiguratieopties voor het virtuele netwerk. Met deze standaardopties is een extern azure-netwerk en een virtueel netwerksubnet gemaakt. De Azure AD DS-domeincontrollers die de beheerde domeinservices leveren, zijn verbonden met dit virtuele netwerksubnet.

Wanneer u VM's maakt en uitvoert die het door Azure AD DS beheerde domein moeten gebruiken, moet er netwerkconnectiviteit worden verstrekt. Deze netwerkconnectiviteit kan op een van de volgende manieren worden geboden:

* Maak een extra virtueel netwerksubnet in het virtuele netwerk van het standaard Azure AD DS-beheerde domein. In dit aanvullende subnet maakt en verbindt u uw VM's.
    * Aangezien de VM's deel uitmaken van hetzelfde virtuele netwerk, kunnen ze automatisch naamomzetting uitvoeren en communiceren met de Azure AD DS-domeincontrollers.
* Configureer het virtuele Azure-netwerkpeering van het virtuele netwerk van het Azure AD DS-beheerde domein naar een of meer afzonderlijke virtuele netwerken. Deze afzonderlijke virtuele netwerken zijn waar u uw VM's maakt en verbindt.
    * Wanneer u virtuele netwerkpeering configureert, moet u ook DNS-instellingen configureren om naamomzetting terug te gebruiken naar de Azure AD DS-domeincontrollers.

Meestal gebruikt u slechts één van deze netwerkconnectiviteitsopties. De keuze is vaak aan de manier waarop u uw Azure-resources wilt beheren. Als u Azure AD DS en verbonden VM's als één groep resources wilt beheren, u een extra virtueel netwerksubnet voor VM's maken. Als u het beheer van Azure AD DS en vervolgens alle verbonden VM's wilt scheiden, u virtuele netwerkpeering gebruiken. U er ook voor kiezen om virtuele netwerkpeering te gebruiken om connectiviteit te bieden met bestaande VM's in uw Azure-omgeving die zijn verbonden met een bestaand virtueel netwerk.

In deze zelfstudie hoeft u slechts één van deze virtuele netwerkconnectiviteitsopties te configureren.

Zie [netwerkoverwegingen voor Azure Active Directory Domain Services][netwerkoverwegingen] voor meer informatie over het plannen en configureren van het virtuele netwerk.

## <a name="create-a-virtual-network-subnet"></a>Een virtueel netwerksubnet maken

Het Azure virtuele netwerk dat is gemaakt met het door Azure AD DS beheerde domein, bevat standaard één virtueel netwerksubnet. Dit virtuele netwerksubnet mag alleen door het Azure-platform worden gebruikt om beheerde domeinservices te leveren. Als u uw eigen VM's wilt maken en gebruiken in dit virtuele Azure-netwerk, maakt u een extra subnet.

Voer de volgende stappen uit om een virtueel netwerksubnet voor VM's en toepassingsworkloads te maken:

1. Selecteer in de Azure-portal de brongroep van uw door Azure AD DS beheerde domein, zoals *myResourceGroup.* Kies in de lijst met bronnen het standaard virtuele netwerk, zoals *aadds-vnet.*
1. Selecteer **Adresruimte**in het linkermenu van het virtuele netwerkvenster . Het virtuele netwerk wordt gemaakt met een enkele adresruimte van *10.0.2.0/24*, die wordt gebruikt door het standaardsubnet.

    Voeg een extra IP-adresbereik toe aan het virtuele netwerk. De grootte van dit adresbereik en het werkelijke IP-adresbereik dat moet worden gebruikt, is afhankelijk van andere netwerkbronnen die al zijn geïmplementeerd. Het IP-adresbereik mag niet overlappen met bestaande adresbereiken in uw Azure- of on-premises omgeving. Zorg ervoor dat u het IP-adresbereik groot genoeg maakt voor het aantal VM's dat u in het subnet verwacht te implementeren.

    In het volgende voorbeeld wordt een extra IP-adresbereik van *10.0.3.0/24* toegevoegd. Wanneer u klaar bent, selecteert **u Opslaan**.

    ![Een extra IP-adresbereik voor virtuele netwerken toevoegen aan de Azure-portal](./media/tutorial-configure-networking/add-vnet-address-range.png)

1. Selecteer vervolgens in het linkermenu van het virtuele netwerkvenster **Subnetten**en kies **+ Subnet** om een subnet toe te voegen.
1. Voer een naam in voor het subnet, zoals *werkbelastingen*. Werk indien nodig het **adresbereik** bij als u in de vorige stappen een subset wilt gebruiken van het IP-adresbereik dat is geconfigureerd voor het virtuele netwerk. Voor nu, laat de standaardinstellingen voor opties zoals netwerk beveiliging groep, route tabel, service eindpunten.

    In het volgende voorbeeld wordt een subnet met de naam *workloads* gemaakt dat het *IP-adresbereik van 10.0.3.0/24* gebruikt:

    ![Een extra virtueel netwerksubnet toevoegen aan de Azure-portal](./media/tutorial-configure-networking/add-vnet-subnet.png)

1. Wanneer u klaar bent, selecteert u **OK**. Het duurt een paar momenten om het virtuele netwerk subnet te maken.

Wanneer u een VM maakt die het beheerde Azure AD DS-domein moet gebruiken, moet u dit virtuele netwerksubnet selecteren. Maak geen VM's in het standaard *subnet aadds-subnet*. Als u een ander virtueel netwerk selecteert, is er geen netwerkverbinding en DNS-resolutie om het beheerde Azure AD DS-domein te bereiken, tenzij u virtuele netwerkpeering configureert.

## <a name="configure-virtual-network-peering"></a>Virtueel netwerkpeering configureren

U hebt mogelijk een bestaand Virtueel Azure-netwerk voor VM's of wilt uw azure AD DS-beheerde domein gescheiden houden. Als u het beheerde domein wilt gebruiken, hebben VM's in andere virtuele netwerken een manier nodig om te communiceren met de Azure AD DS-domeincontrollers. Deze connectiviteit kan worden geleverd met behulp van Azure virtual network peering.

Met Azure virtual network peering zijn twee virtuele netwerken met elkaar verbonden, zonder dat er een VPN-apparaat (virtual private network) nodig is. Met netwerkpeering u snel virtuele netwerken verbinden en verkeersstromen in uw Azure-omgeving definiëren. Zie overzicht van azure [virtual network peering][peering-overview]voor meer informatie over peering.

Voer de volgende stappen uit om een virtueel netwerk te peeren naar het virtuele azure AD DS-netwerk van het Azure AD DS-beheerde domein:

1. Kies het standaard virtuele netwerk dat is gemaakt voor uw Azure AD DS-exemplaar met de naam *aadds-vnet*.
1. Selecteer **Peerings**in het linkermenu van het virtuele netwerkvenster .
1. Als u een peering wilt maken, selecteert u **+ Toevoegen**. In het volgende voorbeeld wordt de *standaardaadds-vnet* gekeken naar een virtueel netwerk met de naam *myVnet*. Configureer de volgende instellingen met uw eigen waarden:

    * **Naam van het peering van aadds-vnet naar extern virtueel netwerk:** een beschrijvende id van de twee netwerken, zoals *aadds-vnet-to-myvnet*
    * **Type virtuele netwerkimplementatie**: *Resourcemanager*
    * **Abonnement:** het abonnement van het virtuele netwerk waarnaar u wilt peeren, zoals *Azure*
    * **Virtueel netwerk**: het virtuele netwerk waarnaar u wilt peeren, zoals *myVnet*
    * **Naam van het peering van myVnet naar aadds-vnet**: Een beschrijvende id van de twee netwerken, zoals *myvnet-to-aadds-vnet*

    ![Virtueel netwerkpeeren configureren in de Azure-portal](./media/tutorial-configure-networking/create-peering.png)

    Laat alle andere standaardinstellingen voor virtuele netwerktoegang of doorgestuurd verkeer achter, tenzij u specifieke vereisten voor uw omgeving hebt en selecteer **OK**.

1. Het duurt even om het peering te maken op zowel het virtuele Azure AD DS-netwerk als het virtuele netwerk dat u hebt geselecteerd. Wanneer u klaar bent, worden de **peeringstatusrapporten** *Verbonden,* zoals weergegeven in het volgende voorbeeld:

    ![Met succes verbonden peered-netwerken in de Azure-portal](./media/tutorial-configure-networking/connected-peering.png)

Voordat VM's in het gepeerde virtuele netwerk het door Azure AD DS beheerde domein kunnen gebruiken, configureert u de DNS-servers om de juiste naamomzetting mogelijk te maken.

### <a name="configure-dns-servers-in-the-peered-virtual-network"></a>DNS-servers configureren in het virtuele netwerk met peered

Voor VM's en toepassingen in het virtuele netwerk met peered om met het beheerde Azure AD DS-domein te praten, moeten de DNS-instellingen worden bijgewerkt. De IP-adressen van de Azure AD DS-domeincontrollers moeten zijn geconfigureerd als de DNS-servers in het virtuele netwerk met peered. Er zijn twee manieren om de domeincontrollers te configureren als DNS-servers voor het peered virtuele netwerk:

* Configureer de DNS-servers van het Azure Virtual Network om de Azure AD DS-domeincontrollers te gebruiken.
* Configureer de bestaande DNS-server die wordt gebruikt op het virtuele netwerk met peered om voorwaardelijke DNS-forwarding te gebruiken om query's naar het beheerde Azure AD DS-beheerde domein te leiden. Deze stappen zijn afhankelijk van de bestaande DNS-server die in gebruik is.

Laten we in deze zelfstudie de DNS-servers van het Azure-virtuele netwerk configureren om alle query's naar de Azure AD DS-domeincontrollers te leiden.

1. Selecteer in de Azure-portal de brongroep van het virtuele netwerk met peered, zoals *myResourceGroup.* Kies in de lijst met bronnen het peered virtuele netwerk, zoals *myVnet.*
1. Selecteer **DNS-servers**in het linkermenu van het virtuele netwerkvenster.
1. Standaard maakt een virtueel netwerk gebruik van de ingebouwde door Azure geleverde DNS-servers. Kies voor **aangepaste** DNS-servers. Voer de IP-adressen in voor de Azure AD DS-domeincontrollers, die meestal *10.0.2.4* en *10.0.2.5*zijn. Bevestig deze IP-adressen in het **overzichtsvenster** van uw door Azure AD DS beheerde domein in de portal.

    ![De DNS-servers van het virtuele netwerk configureren om de Azure AD DS-domeincontrollers te gebruiken](./media/tutorial-configure-networking/custom-dns.png)

1. Wanneer u klaar bent, selecteert **u Opslaan**. Het duurt even om de DNS-servers voor het virtuele netwerk bij te werken.
1. Als u de bijgewerkte DNS-instellingen wilt toepassen op de VM's, start u VM's opnieuw die zijn aangesloten op het virtuele netwerk met peered.

Wanneer u een VM maakt die het beheerde Azure AD DS-beheerde domein moet gebruiken, moet u dit virtuele netwerk met peered selecteren. Als u een ander virtueel netwerk selecteert, is er geen netwerkverbinding en DNS-resolutie om het door Azure AD DS beheerde domein te bereiken.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Inzicht in de opties voor virtuele netwerkconnectiviteit voor met domeinen verbonden resources voor Azure AD DS
> * Een IP-adresbereik en extra subnet maken in het virtuele Azure AD DS-netwerk
> * Virtuele netwerkpeering configureren naar een netwerk dat gescheiden is van Azure AD DS

Als u dit beheerde domein in actie wilt zien, maakt en sluit u een virtuele machine aan het domein aan.

> [!div class="nextstepaction"]
> [Een virtuele windows server-machine aansluiten bij uw beheerde domein](join-windows-vm.md)

<!-- INTERNAL LINKS --> 
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[peering-overview]: ../virtual-network/virtual-network-peering-overview.md
