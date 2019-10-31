---
title: 'Zelf studie: virtuele netwerken configureren voor Azure AD Domain Services | Microsoft Docs'
description: In deze zelf studie leert u hoe u een subnet van een virtueel netwerk van Azure of netwerk peering kunt maken en configureren voor een Azure Active Directory Domain Services-exemplaar met behulp van de Azure Portal.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: 26122278ad74fb1d383ca7a900810b6060ee78f5
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73172655"
---
# <a name="tutorial-configure-virtual-networking-for-an-azure-active-directory-domain-services-instance"></a>Zelf studie: virtuele netwerken voor een Azure Active Directory Domain Services-exemplaar configureren

Om verbinding te kunnen maken met gebruikers en toepassingen, wordt een beheerd domein van Azure Active Directory Domain Services (Azure AD DS) geïmplementeerd in een subnet van een virtueel netwerk in Azure. Dit subnet van het virtuele netwerk moet alleen worden gebruikt voor de beheerde domein resources van het Azure-platform. Wanneer u uw eigen Vm's en toepassingen maakt, mogen ze niet worden geïmplementeerd in hetzelfde subnet van het virtuele netwerk. In plaats daarvan moet u uw toepassingen maken en implementeren in een afzonderlijk subnet van het virtuele netwerk of in een afzonderlijk virtueel netwerk dat is gekoppeld aan het Azure AD DS virtuele netwerk.

In deze zelf studie leert u hoe u een toegewezen virtueel netwerk subnet maakt en configureert, of hoe u een ander netwerk peert naar het virtuele netwerk van het Azure AD DS beheerde domein.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Meer informatie over de connectiviteits opties voor virtuele netwerken voor bronnen die lid zijn van een domein aan Azure AD DS
> * Een IP-adres bereik en een extra subnet maken in het virtuele netwerk van Azure AD DS
> * Configureer peering van het virtuele netwerk in een netwerk dat losstaat van Azure AD DS

Als u nog geen abonnement op Azure hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources en bevoegdheden nodig om deze zelf studie te volt ooien:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory Tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises Directory of een alleen-Cloud Directory.
    * Als dat nodig is, [maakt u een Azure Active Directory-Tenant][create-azure-ad-tenant] of [koppelt u een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* U hebt *globale beheerders* bevoegdheden nodig in uw Azure AD-Tenant om Azure AD DS in te scha kelen.
* U hebt *Inzender* bevoegdheden nodig in uw Azure-abonnement om de vereiste Azure AD DS-resources te maken.
* Een Azure Active Directory Domain Services beheerd domein ingeschakeld en geconfigureerd in uw Azure AD-Tenant.
    * Als dat nodig is, [maakt en configureert][create-azure-ad-ds-instance]de eerste zelf studie een Azure Active Directory Domain Services-exemplaar.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

In deze zelf studie maakt en configureert u het Azure AD DS-exemplaar met behulp van de Azure Portal. Meld u eerst aan bij de [Azure Portal](https://portal.azure.com)om aan de slag te gaan.

## <a name="application-workload-connectivity-options"></a>Connectiviteits opties voor toepassings werkbelasting

In de vorige zelf studie is een door Azure AD DS beheerd domein gemaakt dat enkele standaard configuratie opties voor het virtuele netwerk heeft gebruikt. Met deze standaard opties hebt u een virtueel Azure-netwerk en een virtueel netwerk subnet gemaakt. De Azure AD DS domein controllers die de beheerde domein services bieden, zijn verbonden met dit subnet van het virtuele netwerk.

Wanneer u virtuele machines maakt en uitvoert die het door Azure AD DS beheerde domein moeten gebruiken, moet er een netwerk verbinding worden gegeven. Deze netwerk verbinding kan op een van de volgende manieren worden gegeven:

* Maak een aanvullend subnet voor het virtuele netwerk in het virtuele netwerk van de standaard Azure AD DS beheerde domein. In dit extra subnet maakt en verbindt u uw virtuele machines.
    * Aangezien de Vm's deel uitmaken van hetzelfde virtuele netwerk, kunnen ze automatisch naam omzetting uitvoeren en communiceren met de Azure AD DS-domein controllers.
* Configureer Azure Virtual Network-peering van het virtuele netwerk van het Azure AD DS beheerde domein naar een of meer afzonderlijke virtuele netwerken. Met deze afzonderlijke virtuele netwerken maakt en verbindt u uw virtuele machines.
    * Wanneer u peering voor het virtuele netwerk configureert, moet u ook DNS-instellingen configureren om de naam omzetting terug te zetten op de domein controllers van Azure AD DS.

Meestal gebruikt u een van deze opties voor netwerk connectiviteit. De keuze is vaak uitvallend op de manier waarop u de afzonderlijke Azure-resources wilt beheren. Als u Azure AD DS en verbonden Vm's als één groep resources wilt beheren, kunt u een aanvullend subnet van het virtuele netwerk voor virtuele machines maken. Als u het beheer van Azure AD DS wilt scheiden en vervolgens alle verbonden Vm's, kunt u gebruikmaken van virtuele netwerk peering. U kunt er ook voor kiezen om virtuele netwerk-peering te gebruiken om verbinding te maken met bestaande Vm's in uw Azure-omgeving die zijn verbonden met een bestaand virtueel netwerk.

In deze zelf studie hoeft u slechts één van deze connectiviteits opties voor virtuele netwerken te configureren.

Zie [netwerk overwegingen voor Azure Active Directory Domain Services] [netwerk-overwegingen] voor meer informatie over het plannen en configureren van het virtuele netwerk.

## <a name="create-a-virtual-network-subnet"></a>Een subnet van een virtueel netwerk maken

Het virtuele Azure-netwerk dat is gemaakt met het beheerde domein van Azure AD DS bevat standaard één subnet van het virtuele netwerk. Dit subnet van het virtuele netwerk mag alleen worden gebruikt door het Azure-platform voor het leveren van beheerde domein Services. Als u uw eigen virtuele machines in dit virtuele Azure-netwerk wilt maken en gebruiken, maakt u een extra subnet.

Voer de volgende stappen uit om een subnet van een virtueel netwerk te maken voor Vm's en toepassings werkbelastingen:

1. Selecteer in de Azure Portal de resource groep van uw door Azure AD DS beheerd domein, zoals *myResourceGroup*. Kies in de lijst met resources het standaard virtuele netwerk, zoals *aadds-vnet*.
1. Selecteer in het menu aan de linkerkant van het venster virtueel netwerk **adres ruimte**. Het virtuele netwerk wordt gemaakt met één adres ruimte van *10.0.1.0/24*, dat wordt gebruikt door het standaard subnet.

    Voeg een extra IP-adres bereik toe aan het virtuele netwerk. De grootte van dit adres bereik en het daad werkelijke IP-adres bereik dat moet worden gebruikt, is afhankelijk van andere netwerk bronnen die al zijn geïmplementeerd. Het IP-adres bereik mag niet overlappen met de bestaande adresbereiken in uw Azure-of on-premises omgeving. Zorg ervoor dat u de grootte van het IP-adres bereik groot genoeg hebt voor het aantal Vm's dat u naar verwachting in het subnet wilt implementeren.

    In het volgende voor beeld wordt een extra IP-adres bereik van *10.0.2.0/24* toegevoegd. Wanneer u klaar bent, selecteert u **Opslaan**.

    ![Voeg een extra IP-adres bereik toe voor het virtuele netwerk in de Azure Portal](./media/tutorial-configure-networking/add-vnet-address-range.png)

1. Selecteer vervolgens in het menu aan de linkerkant van het venster virtueel netwerk **subnetten**en kies vervolgens **+ subnet** om een subnet toe te voegen.
1. Voer een naam in voor het subnet, zoals *werk belastingen*. Indien nodig werkt u het **adres bereik** bij als u een subset wilt gebruiken van het IP-adres bereik dat is geconfigureerd voor het virtuele netwerk in de vorige stappen. Behoud de standaard waarden voor opties, zoals de netwerk beveiligings groep, de route tabel en service-eind punten.

    In het volgende voor beeld wordt een subnet gemaakt met de naam *workloads* die het IP-adres bereik *10.0.2.0/24* gebruikt:

    ![Voeg een extra subnet met virtuele netwerken toe aan de Azure Portal](./media/tutorial-configure-networking/add-vnet-subnet.png)

1. Wanneer u klaar bent, selecteert u **OK**. Het kan even duren voordat het subnet van het virtuele netwerk is gemaakt.

Wanneer u een VM maakt die het beheerde domein van Azure AD DS moet gebruiken, moet u dit subnet van het virtuele netwerk selecteren. Maak geen Vm's in het standaard *aadds-subnet*. Als u een ander virtueel netwerk selecteert, is er geen verbinding met het netwerk en DNS-omzetting om het door Azure AD DS beheerde domein te bereiken tenzij u peering voor het virtuele netwerk configureert.

## <a name="configure-virtual-network-peering"></a>Peering voor het virtuele netwerk configureren

Mogelijk hebt u een bestaand virtueel Azure-netwerk voor Vm's, of wilt u uw Azure AD DS beheerde domein-virtueel netwerk gescheiden laten blijven. Als u het beheerde domein wilt gebruiken, hebben Vm's in andere virtuele netwerken een manier nodig om te communiceren met de Azure AD DS-domein controllers. Deze connectiviteit kan worden gegeven met behulp van peering van het virtuele netwerk van Azure.

Met de peering van het virtuele netwerk van Azure zijn twee virtuele netwerken met elkaar verbonden, zonder dat hiervoor een VPN-apparaat (virtueel particulier netwerk) nodig is. Met Network-peering kunt u snel virtuele netwerken verbinden en netwerk verkeer definiëren in uw Azure-omgeving. Zie [overzicht van Azure Virtual Network-peering][peering-overview]voor meer informatie over peering.

Voer de volgende stappen uit om een virtueel netwerk te koppelen aan het virtuele netwerk van Azure AD DS beheerde domein:

1. Kies het virtuele standaard netwerk dat u hebt gemaakt voor uw Azure AD DS-exemplaar met de naam *aadds-vnet*.
1. Selecteer in het menu aan de linkerkant van het venster virtueel netwerk de optie **peerings**.
1. Als u een peering wilt maken, selecteert u **+ toevoegen**. In het volgende voor beeld wordt de standaard *aadds-vnet* gekoppeld aan een virtueel netwerk met de naam *myVnet*. Configureer de volgende instellingen met uw eigen waarden:

    * **Naam van de peering van aadds-vnet naar extern virtueel netwerk**: een beschrijvende id van de twee netwerken, zoals *aadds-vnet-naar-myvnet*
    * **Implementatie type van virtueel netwerk**: *Resource Manager*
    * **Abonnement**: het abonnement van het virtuele netwerk waarmee u een peer wilt, zoals *Azure*
    * **Virtueel netwerk**: het virtuele netwerk waarmee u een peer wilt, zoals *myVnet*
    * **Naam van de peering van myVnet naar aadds-vnet**: een beschrijvende id van de twee netwerken, zoals *myVnet-to-aadds-vnet*

    ![Configureer peering voor het virtuele netwerk in de Azure Portal](./media/tutorial-configure-networking/create-peering.png)

    Laat alle andere standaard waarden voor toegang tot het virtuele netwerk of doorgestuurd verkeer, tenzij u specifieke vereisten voor uw omgeving hebt, en selecteer vervolgens **OK**.

1. Het duurt enkele minuten om de peering te maken op zowel het virtuele Azure AD DS-netwerk als het virtuele netwerk dat u hebt geselecteerd. Wanneer u klaar bent, zijn de **peering status** rapporten *verbonden*, zoals wordt weer gegeven in het volgende voor beeld:

    ![Verbonden netwerken aangesloten in de Azure Portal](./media/tutorial-configure-networking/connected-peering.png)

Voordat Vm's in het gekoppelde virtuele netwerk het beheerde domein van Azure AD DS kunnen gebruiken, moet u de DNS-servers configureren om de juiste naam omzetting mogelijk te maken.

### <a name="configure-dns-servers-in-the-peered-virtual-network"></a>DNS-servers configureren in het gekoppelde virtuele netwerk

Voor Vm's en toepassingen in het gekoppelde virtuele netwerk om te communiceren met het door Azure AD DS beheerde domein, moeten de DNS-instellingen worden bijgewerkt. De IP-adressen van de Azure AD DS domein controllers moeten worden geconfigureerd als de DNS-servers in het gekoppelde virtuele netwerk. Er zijn twee manieren om de domein controllers te configureren als DNS-servers voor het gekoppelde virtuele netwerk:

* Configureer de DNS-servers van het virtuele netwerk van Azure voor het gebruik van de Azure AD DS-domein controllers.
* Configureer de bestaande DNS-server die in gebruik is in het gekoppelde virtuele netwerk voor het door sturen van voorwaardelijke DNS om query's naar het door Azure AD DS beheerde domein te leiden. Deze stappen variëren afhankelijk van de bestaande DNS-server die in gebruik is.

In deze zelf studie gaan we de DNS-servers van het virtuele Azure-netwerk configureren om alle query's door te sturen naar de Azure AD DS-domein controllers.

1. Selecteer in de Azure Portal de resource groep van het gekoppelde virtuele netwerk, bijvoorbeeld *myResourceGroup*. Kies in de lijst met resources het gekoppelde virtuele netwerk, zoals *myVnet*.
1. Selecteer in het menu aan de linkerkant van het venster virtueel netwerk de optie **DNS-servers**.
1. Een virtueel netwerk maakt standaard gebruik van de ingebouwde door Azure verschafte DNS-servers. Kies voor het gebruik van **aangepaste** DNS-servers. Voer de IP-adressen in voor de Azure AD DS domein controllers, die meestal *10.0.1.4* en *10.0.1.5*zijn. Bevestig deze IP-adressen in het **overzichts** venster van uw Azure AD DS beheerde domein in de portal.

    ![De DNS-servers van het virtuele netwerk configureren voor het gebruik van de Azure AD DS-domein controllers](./media/tutorial-configure-networking/custom-dns.png)

1. Wanneer u klaar bent, selecteert u **Opslaan**. Het duurt enkele minuten om de DNS-servers voor het virtuele netwerk bij te werken.
1. Als u de bijgewerkte DNS-instellingen wilt Toep assen op de virtuele machines, start u Vm's die zijn verbonden met het gekoppelde virtuele netwerk opnieuw op.

Wanneer u een virtuele machine maakt die het beheerde domein van Azure AD DS moet gebruiken, moet u dit gekoppelde virtuele netwerk selecteren. Als u een ander virtueel netwerk selecteert, is er geen verbinding met het netwerk en DNS-omzetting om het beheerde domein van Azure AD DS te bereiken.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Meer informatie over de connectiviteits opties voor virtuele netwerken voor bronnen die lid zijn van een domein aan Azure AD DS
> * Een IP-adres bereik en een extra subnet maken in het virtuele netwerk van Azure AD DS
> * Configureer peering van het virtuele netwerk in een netwerk dat losstaat van Azure AD DS

Als u dit beheerde domein in actie wilt zien, maakt u een virtuele machine en voegt u deze toe aan het domein.

> [!div class="nextstepaction"]
> [Een virtuele machine met Windows Server toevoegen aan uw beheerde domein](join-windows-vm.md)

<!-- INTERNAL LINKS --> 
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[peering-overview]: ../virtual-network/virtual-network-peering-overview.md
