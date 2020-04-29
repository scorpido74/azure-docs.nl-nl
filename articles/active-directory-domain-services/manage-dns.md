---
title: DNS beheren voor Azure AD Domain Services | Microsoft Docs
description: Meer informatie over het installeren van de Hulpprogram Ma's voor DNS-server voor het beheren van DNS en voorwaardelijke doorstuur servers maken voor een Azure Active Directory Domain Services beheerd domein.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 04/16/2020
ms.author: iainfou
ms.openlocfilehash: f4bd3f75c3246cb11e88dbaae817eba8ac76b394
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81603464"
---
# <a name="administer-dns-and-create-conditional-forwarders-in-an-azure-ad-domain-services-managed-domain"></a>DNS beheren en voorwaardelijke doorstuur servers maken in een Azure AD Domain Services beheerd domein

In Azure Active Directory Domain Services (Azure AD DS) is een belang rijk onderdeel van DNS (Domain Name Resolution). Azure AD DS bevat een DNS-server die naam omzetting voor het beheerde domein biedt. Deze DNS-server bevat ingebouwde DNS-records en-updates voor de belangrijkste onderdelen waarmee de service kan worden uitgevoerd.

Wanneer u uw eigen toepassingen en services uitvoert, moet u mogelijk DNS-records maken voor machines die niet zijn toegevoegd aan het domein, virtuele IP-adressen configureren voor load balancers of externe DNS-doorstuur servers instellen. Gebruikers die deel uitmaken van de groep *Aad DC-Administrators* , krijgen machtigingen voor DNS-beheer op het door Azure AD DS beheerde domein verleend en kunnen aangepaste DNS-records maken en bewerken.

In een hybride omgeving worden DNS-zones en-records die zijn geconfigureerd in andere DNS-naam ruimten, zoals een on-premises AD DS omgeving, niet gesynchroniseerd met Azure AD DS. Voor het omzetten van benoemde resources in andere DNS-naam ruimten, maakt en gebruikt u voorwaardelijke doorstuur servers die verwijzen naar bestaande DNS-server in uw omgeving.

In dit artikel wordt beschreven hoe u de hulpprogram ma's voor DNS-server installeert en vervolgens de DNS-console gebruikt voor het beheren van records en het maken van voorwaardelijke doorstuur servers in azure AD DS.

## <a name="before-you-begin"></a>Voordat u begint

U hebt de volgende resources en bevoegdheden nodig om dit artikel te volt ooien:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory Tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises Directory of een alleen-Cloud Directory.
    * Als dat nodig is, [maakt u een Azure Active Directory-Tenant][create-azure-ad-tenant] of [koppelt u een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* Een Azure Active Directory Domain Services beheerd domein ingeschakeld en geconfigureerd in uw Azure AD-Tenant.
    * Als dat nodig is, voltooit u de zelf studie voor het [maken en configureren van een Azure Active Directory Domain Services-exemplaar][create-azure-ad-ds-instance].
* Connectiviteit vanuit uw Azure AD DS virtuele netwerk naar de locatie waar uw andere DNS-naam ruimten worden gehost.
    * Deze connectiviteit kan worden gegeven met een [Azure ExpressRoute][expressroute] -of [Azure VPN gateway][vpn-gateway] -verbinding.
* Een Windows Server Management-VM die deel uitmaakt van het door Azure AD DS beheerde domein.
    * Als dat nodig is, voltooit u de zelf studie voor het [maken van een Windows Server-VM en voegt u deze toe aan een beheerd domein][create-join-windows-vm].
* Een gebruikers account dat lid is van de groep *Azure AD DC-Administrators* in uw Azure AD-Tenant.

## <a name="install-dns-server-tools"></a>Hulpprogram ma's voor DNS-server installeren

Als u DNS-records wilt maken en wijzigen in azure AD DS, moet u de DNS-server hulpprogramma's installeren. Deze hulpprogram ma's kunnen worden geïnstalleerd als een functie in Windows Server. Zie install [Remote Server Administration Tools (RSAT) (Engelstalig)][install-rsat]voor meer informatie over het installeren van de beheer Programma's op een Windows-client.

1. Meld u aan bij uw beheer-VM. Zie [verbinding maken met een virtuele machine van Windows Server][connect-windows-server-vm]voor stappen voor het maken van verbinding met behulp van de Azure Portal.
1. Als **Serverbeheer** standaard niet wordt geopend wanneer u zich aanmeldt bij de VM, selecteert u het menu **Start** en kiest u vervolgens **Serverbeheer**.
1. Selecteer in het deel venster *dash board* van het **Serverbeheer** -venster **functies en onderdelen toevoegen**.
1. Selecteer op de pagina **voordat u begint** van de *wizard functies en onderdelen toevoegen*de optie **volgende**.
1. Voor het *installatie type*, houdt u de **installatie optie op basis van functie of op basis** van het onderdeel ingeschakeld en selecteert u **volgende**.
1. Kies op de pagina **server selectie** de huidige virtuele machine uit de Server groep, bijvoorbeeld *myvm.aaddscontoso.com*, en selecteer vervolgens **volgende**.
1. Klik op de pagina **Server functies** op **volgende**.
1. Vouw op de pagina **functies** het knoop punt **Remote Server Administration Tools** uit en vouw vervolgens het knoop punt **hulpprogram ma's voor functie beheer** uit. Selecteer de functie **Hulpprogram ma's voor DNS-server** in de lijst met hulpprogram ma's voor functie beheer.

    ![De Hulpprogram ma's voor de DNS-server installeren in de lijst met beschik bare hulpprogram ma's voor functie beheer](./media/manage-dns/install-dns-tools.png)

1. Selecteer **installeren**op de pagina **bevestiging** . Het kan een paar minuten duren voordat de groepsbeleid-beheer hulpprogramma's zijn geïnstalleerd.
1. Wanneer de installatie van de functie is voltooid, selecteert u **sluiten** om de wizard **functies en onderdelen toevoegen** af te sluiten.

## <a name="open-the-dns-management-console-to-administer-dns"></a>De DNS-beheer console openen voor het beheren van DNS

Als u de hulpprogram ma's voor DNS-server hebt geïnstalleerd, kunt u DNS-records beheren op het door Azure AD DS beheerde domein.

> [!NOTE]
> Als u DNS wilt beheren in een door Azure AD DS beheerd domein, moet u zijn aangemeld bij een gebruikers account dat lid is van de groep *Aad DC-Administrators* .

1. Selecteer in het Start scherm de optie **systeem beheer**. Er wordt een lijst met beschik bare beheer hulpprogramma's weer gegeven, inclusief **DNS** die in de vorige sectie is geïnstalleerd. Selecteer **DNS** om de DNS-beheer console te starten.
1. Selecteer in het dialoog venster **verbinding maken met DNS-server** **de volgende computer**en voer vervolgens de DNS-domein naam in van het beheerde domein, zoals *aaddscontoso.com*:

    ![Verbinding maken met het beheerde domein van Azure AD DS in de DNS-console](./media/manage-dns/connect-dns-server.png)

1. De DNS-console maakt verbinding met het opgegeven Azure AD DS beheerde domein. Vouw de zone voor **Forward lookup** of **zones voor reverse lookup** uit om de vereiste DNS-vermeldingen te maken of bewerk bestaande records als dat nodig is.

    ![DNS-console-domein beheren](./media/manage-dns/dns-manager.png)

> [!WARNING]
> Wanneer u records beheert met behulp van de DNS-server hulpprogramma's, moet u ervoor zorgen dat u de ingebouwde DNS-records die door Azure AD DS worden gebruikt, niet verwijdert of wijzigt. Ingebouwde DNS-records zijn DNS-records voor domein, naam server records en andere records die worden gebruikt voor de locatie van de domein controller. Als u deze records wijzigt, worden de domein Services onderbroken op het virtuele netwerk.

## <a name="create-conditional-forwarders"></a>Voorwaardelijke doorstuur servers maken

Een Azure AD DS DNS-zone mag alleen de zone en records voor het beheerde domein zelf bevatten. Maak geen extra zones in azure AD DS om benoemde resources in andere DNS-naam ruimten op te lossen. Gebruik in plaats daarvan voorwaardelijke doorstuur servers in het door Azure AD DS beheerde domein om de DNS-server te laten weten waar ze moeten worden geplaatst om adressen voor die resources op te lossen.

Voorwaardelijke doorstuur servers is een configuratie optie in een DNS-server waarmee u een DNS-domein, zoals *contoso.com*, kunt definiëren om query's door te sturen naar. In plaats van de lokale DNS-server die query's voor records in dat domein probeert op te lossen, worden DNS-query's doorgestuurd naar de geconfigureerde DNS voor dat domein. Deze configuratie zorgt ervoor dat de juiste DNS-records worden geretourneerd, omdat u geen lokale DNS-zone maakt met dubbele records in het Azure AD DS beheerde domein om deze resources weer te geven.

Voer de volgende stappen uit om een voorwaardelijke doorstuur server te maken in uw Azure AD DS beheerde domein:

1. Selecteer de DNS-zone van Azure AD DS, zoals *aaddscontoso.com*. VB
1. Selecteer **voorwaardelijke doorstuur servers**, klik met de rechter muisknop en kies **nieuwe voorwaardelijke doorstuur server...**
1. Voer uw andere **DNS-domein**in, bijvoorbeeld *contoso.com*, en voer vervolgens de IP-adressen van de DNS-servers voor die naam ruimte in, zoals wordt weer gegeven in het volgende voor beeld:

    ![Voorwaardelijke doorstuur servers toevoegen en configureren voor de DNS-server](./media/manage-dns/create-conditional-forwarder.png)

1. Schakel het selectie vakje **in om deze voorwaardelijke doorstuur server op te slaan in Active Directory en repliceer deze als volgt en**Selecteer vervolgens de optie voor *alle DNS-servers in dit domein*, zoals wordt weer gegeven in het volgende voor beeld:

    ![DNS-console-domein beheren](./media/manage-dns/store-in-domain.png)

    > [!IMPORTANT]
    > Als de voorwaardelijke doorstuur server wordt opgeslagen in het *forest* in plaats van het *domein*, mislukt de voorwaardelijke doorstuur server.

1. Selecteer **OK**om de voorwaardelijke doorstuur server te maken.

Naam omzetting van de resources in andere naam ruimten van Vm's die zijn verbonden met het Azure AD DS beheerde domein moeten nu correct worden omgezet. Query's voor het DNS-domein dat is geconfigureerd in de voorwaardelijke doorstuur server worden door gegeven aan de relevante DNS-servers.

## <a name="next-steps"></a>Volgende stappen

Zie het [artikel over DNS-hulpprogram ma's op TechNet](https://technet.microsoft.com/library/cc753579.aspx)voor meer informatie over het beheren van DNS.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
