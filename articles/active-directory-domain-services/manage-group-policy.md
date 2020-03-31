---
title: Groepsbeleid maken en beheren in Azure AD Domain Services | Microsoft Documenten
description: Meer informatie over het bewerken van de ingebouwde groepsbeleidsobjecten (GPO's) en het maken van uw eigen aangepaste beleidsregels in een beheerd Azure Active Directory Domain Services-domein.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: iainfou
ms.openlocfilehash: bce71355eef19ec3cc85525033274f57b1a3e0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78946410"
---
# <a name="administer-group-policy-in-an-azure-ad-domain-services-managed-domain"></a>Groepsbeleid beheren in een beheerd Azure AD Domain Services-domein

Instellingen voor gebruikers- en computerobjecten in Azure Active Directory Domain Services (Azure AD DS) worden vaak beheerd met Groepsbeleidsobjecten (GPO's). Azure AD DS bevat ingebouwde GPO's voor de containers *AADDC-gebruikers* en *AADDC-computers.* U deze ingebouwde GPO's aanpassen om groepsbeleid te configureren als dat nodig is voor uw omgeving. Leden van de *Azure AD DC-beheerdersgroep* hebben groepsbeleidsbeheerrechten in het Azure AD DS-domein en kunnen ook aangepaste GPO's en organisatie-eenheden (OE's) maken. Meer informatie over wat groepsbeleid is en hoe het werkt, zie [Groepsbeleidsoverzicht][group-policy-overview].

In een hybride omgeving worden groepsbeleidsregels die zijn geconfigureerd in een on-premises AD DS-omgeving, niet gesynchroniseerd met Azure AD DS. Als u configuratie-instellingen wilt definiëren voor gebruikers of computers in Azure AD DS, bewerkt u een van de standaard GPO's of maakt u een aangepaste GPO.

In dit artikel ziet u hoe u de hulpprogramma's voor groepsbeleidsbeheer installeert, vervolgens de ingebouwde GPO's bewerkt en aangepaste GPO's maakt.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Voordat u begint

Als u dit artikel wilt voltooien, hebt u de volgende bronnen en bevoegdheden nodig:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)aan .
* Een Azure Active Directory-tenant die is gekoppeld aan uw abonnement, gesynchroniseerd met een on-premises directory of een map met alleen wolken.
    * Maak indien nodig [een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account.][associate-azure-ad-tenant]
* Een beheerd azure Directory Domain Services-domein is ingeschakeld en geconfigureerd in uw Azure AD-tenant.
    * Vul indien nodig de zelfstudie in om [een Azure Active Directory Domain Services-exemplaar][create-azure-ad-ds-instance]te maken en te configureren.
* Een Windows Server-beheer-VM die is verbonden met het beheerde Azure AD DS-domein.
    * Vul indien nodig de zelfstudie in om [een Windows Server-vm te maken en deze samen te voegen in een beheerd domein.][create-join-windows-vm]
* Een gebruikersaccount dat lid is van de Azure *AD DC-beheerdersgroep* in uw Azure AD-tenant.

> [!NOTE]
> U beheersjablonen voor groepsbeleid gebruiken door de nieuwe sjablonen naar het beheerwerkstation te kopiëren. Kopieer de *.admx-bestanden* `%SYSTEMROOT%\PolicyDefinitions` naar en kopieer de landespecifieke `Language-CountryRegion` *.adml-bestanden* naar `%SYSTEMROOT%\PolicyDefinitions\[Language-CountryRegion]`, waar de taal en het gebied van de *.adml-bestanden* overeenkomen.
>
> Kopieer bijvoorbeeld de Engelse, Verenigde Staten-versie van de `\en-us` *.adml-bestanden* naar de map.
>
> U ook uw groepsbeleidsbeheersjabloon centraal opslaan op de domeincontrollers die deel uitmaken van het beheerde Azure AD DS-domein. Zie [De centrale opslag voor groepsbeleidsbeheersjablonen in Windows maken en beheren](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)voor meer informatie.

## <a name="install-group-policy-management-tools"></a>Hulpprogramma's voor groepsbeleidsbeheer installeren

Als u groepsbeleidsobject (GPO's) wilt maken en configureren, moet u de hulpprogramma's voor groepsbeleidsbeheer installeren. Deze hulpprogramma's kunnen worden geïnstalleerd als een functie in Windows Server. Zie [Extern serverbeheertools (RSAT)][install-rsat]installeren voor meer informatie over het installeren van de beheerhulpprogramma's op een Windows-client.

1. Meld u aan bij uw management VM. Zie Verbinding maken met een Windows Server VM voor stappen over het maken van verbinding via de [Azure-portal.][connect-windows-server-vm]
1. **Serverbeheer** moet standaard worden geopend wanneer u zich aanmeldt bij de vm. Als dit niet het probleem is, selecteert u **Serverbeheer**in het menu **Start** .
1. Selecteer **Rollen en onderdelen toevoegen**in het *dashboardvenster* van het venster **Serverbeheer** .
1. Selecteer **Volgende**op de pagina **Voor het begin** van de wizard Rollen en onderdelen *toevoegen*.
1. Laat voor *het installatietype*de **optie Op rollen of functie gebaseerde installatie** optie aanvinken en selecteer **Volgende**.
1. Kies op de pagina **Serverselectie** de huidige VM uit de servergroep, zoals *myvm.aaddscontoso.com*en selecteer **Volgende**.
1. Klik op de pagina **Serverrollen** op **Volgende**.
1. Selecteer **op** de pagina Onderdelen de functie **Groepsbeleidsbeheer.**

    ![Het 'groepsbeleidsbeheer' installeren op de pagina Functies](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)

1. Selecteer **installeren** op **Install**de pagina Bevestiging . Het kan een minuut of twee duren om de tools voor groepsbeleidsbeheer te installeren.
1. Wanneer de installatie van de functie is voltooid, selecteert u **Sluiten** om de wizard **Rollen en onderdelen toevoegen** af te sluiten.

## <a name="open-the-group-policy-management-console-and-edit-an-object"></a>De console Groepsbeleidsbeheer openen en een object bewerken

Standaardgroepsbeleidsobjecten (GPO's) bestaan voor gebruikers en computers in een door Azure AD DS beheerd domein. Als de functie Groepsbeleidsbeheer uit de vorige sectie is geïnstalleerd, bekijken en bewerken we een bestaande GPO. In de volgende sectie maakt u een aangepaste GPO.

> [!NOTE]
> Als u groepsbeleid wilt beheren in een door Azure AD DS beheerd domein, moet u zijn aangemeld bij een gebruikersaccount dat lid is van de groep *AAD DC Administrators.*

1. Selecteer In het startscherm de optie **Systeembeheer**. Er wordt een lijst met beschikbare beheertools weergegeven, waaronder **groepsbeleidsbeheer** dat in de vorige sectie is geïnstalleerd.
1. Als u de console Groepsbeleidsbeheer (GPMC) wilt openen, kiest u **Groepsbeleidsbeheer**.

    ![De console Groepsbeleidsbeheer wordt gereed gemaakt om groepsbeleidsobjecten te bewerken](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

Er zijn twee ingebouwde groepsbeleidsobjecten (GPO's) in een door Azure AD DS beheerd domein: een voor de container *AADDC-computers* en een voor de container *AADDC-gebruikers.* U deze GPO's aanpassen om groepsbeleid te configureren als dat nodig is binnen uw door Azure AD DS beheerde domein.

1. Vouw in de console **Groepsbeleidsbeheer** het **forest uit: aaddscontoso.com** knooppunt. Vouw vervolgens de **knooppunten Domeinen** uit.

    Er bestaan twee ingebouwde containers voor *AADDC-computers* en *AADDC-gebruikers.* Op elk van deze containers is een standaard GPO toegepast.

    ![Ingebouwde GPO's toegepast op de standaardcontainers 'AADDC-computers' en 'AADDC-gebruikers'](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)

1. Deze ingebouwde GPO's kunnen worden aangepast om specifiek groepsbeleid voor uw door Azure AD DS beheerde domein te configureren. Selecteer een van de GPO's, zoals *AADDC Computers GPO,* en kies **bewerken...**.

    ![Kies de optie om een van de ingebouwde GPO's te 'bewerken'](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)

1. De tool Groepsbeleidsbeheer wordt geopend om u de GPO te laten aanpassen, zoals *Accountbeleid:*

    ![GPO aanpassen om instellingen naar behoefte te configureren](./media/active-directory-domain-services-admin-guide/gp-editor.png)

    Kies Bestand **> opslaan** om het beleid op te slaan als u klaar bent. Computers vernieuwen groepsbeleid standaard om de 90 minuten en passen de aangebrachte wijzigingen toe.

## <a name="create-a-custom-group-policy-object"></a>Een aangepast groepsbeleidsobject maken

Als u vergelijkbare beleidsinstellingen wilt groeperen, maakt u vaak extra GPO's in plaats van alle vereiste instellingen toe te passen in de standaardgPO.To group similar policy settings, you often create additional GPOs instead of applying all of the required settings in the single, default GPO. Met Azure AD DS u uw eigen aangepaste groepsbeleidsobjecten maken of importeren en deze koppelen aan een aangepaste organisatie-eenheid. Zie [Een aangepaste organisatie-eenheid maken in een door Azure AD DS beheerd domein](create-ou.md)als u eerst een aangepaste organisatie-eenheid wilt maken.

1. Selecteer in de console **Groepsbeleidsbeheer** de aangepaste organisatie-eenheid (OU), zoals *MyCustomOU.* Selecteer de organisatie-eenheid en kies **Een GPO maken in dit domein en koppel deze hier...**:

    ![Een aangepaste GPO maken in de console Groepsbeleidsbeheer](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)

1. Geef een naam op voor de nieuwe GPO, zoals *Mijn aangepaste GPO,* selecteer **OK**. U deze aangepaste GPO optioneel baseren op een bestaande GPO en een reeks beleidsopties.

    ![Een naam opgeven voor de nieuwe aangepaste GPO](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)

1. De aangepaste GPO wordt gemaakt en gekoppeld aan uw aangepaste organisatie-eenheid. Als u nu de beleidsinstellingen wilt configureren, selecteert u de aangepaste GPO met de rechtermuisknop en kiest u **Bewerken...**:

    ![Kies de optie om uw aangepaste gPO te 'bewerken'](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)

1. De **groepsbeleidsbeheereditor** wordt geopend om u de GPO te laten aanpassen:

    ![GPO aanpassen om instellingen naar behoefte te configureren](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)

    Kies Bestand **> opslaan** om het beleid op te slaan als u klaar bent. Computers vernieuwen groepsbeleid standaard om de 90 minuten en passen de aangebrachte wijzigingen toe.

## <a name="next-steps"></a>Volgende stappen

Zie Functie Voor meer informatie over de beschikbare groepsbeleidsinstellingen die u configureren met de console Groepsbeleidsbeheer, raadpleegt u [Voorkeurenitems voor groepsbeleid][group-policy-console].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[group-policy-overview]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11)
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
[group-policy-console]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789194(v=ws.11)
