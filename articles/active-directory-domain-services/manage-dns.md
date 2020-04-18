---
title: DNS voor Azure AD-domeinservices beheren | Microsoft Documenten
description: Meer informatie over het installeren van de DNS-serverhulpprogramma's om DNS te beheren en voorwaardelijke doorstuurers te maken voor een beheerd Azure Active Directory Domain Services-beheerd domein.
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
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81603464"
---
# <a name="administer-dns-and-create-conditional-forwarders-in-an-azure-ad-domain-services-managed-domain"></a>DNS beheren en voorwaardelijke doorstuurers maken in een beheerd Azure AD Domain Services-domein

In Azure Active Directory Domain Services (Azure AD DS) is EEN belangrijk onderdeel DNS (Domain Name Resolution). Azure AD DS bevat een DNS-server die naamomzetting biedt voor het beheerde domein. Deze DNS-server bevat ingebouwde DNS-records en -updates voor de belangrijkste onderdelen waarmee de service kan worden uitgevoerd.

Wanneer u uw eigen toepassingen en services uitvoert, moet u mogelijk DNS-records maken voor machines die niet zijn verbonden met het domein, virtuele IP-adressen configureren voor load balancers of externe DNS-expediteurs instellen. Gebruikers die deel uitmaken van de groep *AAD DC-beheerders* krijgen DNS-beheerrechten op het beheerde Azure AD DS-domein en kunnen aangepaste DNS-records maken en bewerken.

In een hybride omgeving worden DNS-zones en records die zijn geconfigureerd in andere DNS-naamruimten, zoals een on-premises AD DS-omgeving, niet gesynchroniseerd met Azure AD DS. Als u benoemde resources in andere DNS-naamruimten wilt oplossen, maakt en gebruikt u voorwaardelijke doorstuuraars die naar bestaande DNS-servers in uw omgeving wijzen.

In dit artikel ziet u hoe u de DNS-serverhulpprogramma's installeert en vervolgens de DNS-console gebruikt om records te beheren en voorwaardelijke doorstuurers te maken in Azure AD DS.

## <a name="before-you-begin"></a>Voordat u begint

Als u dit artikel wilt voltooien, hebt u de volgende bronnen en bevoegdheden nodig:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)aan .
* Een Azure Active Directory-tenant die is gekoppeld aan uw abonnement, gesynchroniseerd met een on-premises directory of een map met alleen wolken.
    * Maak indien nodig [een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account.][associate-azure-ad-tenant]
* Een beheerd azure Directory Domain Services-domein is ingeschakeld en geconfigureerd in uw Azure AD-tenant.
    * Vul indien nodig de zelfstudie in om [een Azure Active Directory Domain Services-exemplaar][create-azure-ad-ds-instance]te maken en te configureren.
* Connectiviteit van uw Azure AD DS-virtuele netwerk met de plaats waar uw andere DNS-naamruimten worden gehost.
    * Deze connectiviteit kan worden geleverd met een [Azure ExpressRoute-][expressroute] of [Azure VPN-gatewayverbinding.][vpn-gateway]
* Een Windows Server-beheer-VM die is verbonden met het beheerde Azure AD DS-domein.
    * Vul indien nodig de zelfstudie in om [een Windows Server-vm te maken en deze samen te voegen in een beheerd domein.][create-join-windows-vm]
* Een gebruikersaccount dat lid is van de Azure *AD DC-beheerdersgroep* in uw Azure AD-tenant.

## <a name="install-dns-server-tools"></a>DNS-serverhulpprogramma's installeren

Als u DNS-records wilt maken en wijzigen in Azure AD DS, moet u de DNS-serverhulpprogramma's installeren. Deze hulpprogramma's kunnen worden geïnstalleerd als een functie in Windows Server. Zie [Extern serverbeheertools (RSAT)][install-rsat]installeren voor meer informatie over het installeren van de beheerhulpprogramma's op een Windows-client.

1. Meld u aan bij uw management VM. Zie Verbinding maken met een Windows Server VM voor stappen over het maken van verbinding via de [Azure-portal.][connect-windows-server-vm]
1. Als **Serverbeheer** niet standaard wordt geopend wanneer u zich aanmeldt bij de vm, selecteert u het menu **Start** en kiest u **Serverbeheer**.
1. Selecteer **Rollen en onderdelen toevoegen**in het *dashboardvenster* van het venster **Serverbeheer** .
1. Selecteer **Volgende**op de pagina **Voor het begin** van de wizard Rollen en onderdelen *toevoegen*.
1. Laat voor *het installatietype*de **optie Op rollen of functie gebaseerde installatie** optie aanvinken en selecteer **Volgende**.
1. Kies op de pagina **Serverselectie** de huidige VM uit de servergroep, zoals *myvm.aaddscontoso.com*en selecteer **Volgende**.
1. Klik op de pagina **Serverrollen** op **Volgende**.
1. Vouw op de pagina **Functies** het knooppunt **Hulpmiddelen voor extern serverbeheer** uit en vouw vervolgens het knooppunt Hulpmiddelen voor **rolbeheer** uit. Selecteer de functie **DNS-serverhulpprogramma's** in de lijst met hulpprogramma's voor rolbeheer.

    ![Kies ervoor om de DNS-serverhulpprogramma's te installeren in de lijst met beschikbare hulpprogramma's voor rolbeheer](./media/manage-dns/install-dns-tools.png)

1. Selecteer **installeren** op **Install**de pagina Bevestiging . Het kan een minuut of twee duren om de tools voor groepsbeleidsbeheer te installeren.
1. Wanneer de installatie van de functie is voltooid, selecteert u **Sluiten** om de wizard **Rollen en onderdelen toevoegen** af te sluiten.

## <a name="open-the-dns-management-console-to-administer-dns"></a>De DNS-beheerconsole openen om DNS te beheren

Met de DNS-servertools u DNS-records beheren op het beheerde Azure AD DS-domein.

> [!NOTE]
> Als u DNS wilt beheren in een door Azure AD DS beheerd domein, moet u zijn aangemeld bij een gebruikersaccount dat lid is van de groep *AAD DC Administrators.*

1. Selecteer In het startscherm de optie **Systeembeheer**. Er wordt een lijst met beschikbare beheertools weergegeven, waaronder **DNS die** in de vorige sectie is geïnstalleerd. Selecteer **DNS** om de DNS-beheerconsole te starten.
1. Selecteer in het dialoogvenster **Verbinding maken met DNS-server** de optie **De volgende computer**en voer vervolgens de DNS-domeinnaam van het beheerde domein in, zoals *aaddscontoso.com:*

    ![Verbinding maken met het beheerde Azure AD DS-domein in de DNS-console](./media/manage-dns/connect-dns-server.png)

1. De DNS-console maakt verbinding met het opgegeven door Azure AD DS beheerde domein. Vouw de **opzoekzones vooruit** of **opzoekzones voor reverse uit** om uw vereiste DNS-items te maken of bestaande records te bewerken als dat nodig is.

    ![DNS-console - domein beheren](./media/manage-dns/dns-manager.png)

> [!WARNING]
> Wanneer u records beheert met de HULPPROGRAMMA's van DNS-server, moet u ervoor zorgen dat u de ingebouwde DNS-records die door Azure AD DS worden gebruikt, niet verwijdert of wijzigt. Ingebouwde DNS-records bevatten dns-records van domeinen, naamserverrecords en andere records die worden gebruikt voor DC-locatie. Als u deze records wijzigt, worden domeinservices verstoord op het virtuele netwerk.

## <a name="create-conditional-forwarders"></a>Voorwaardelijke doorstuurers maken

Een Azure AD DS DNS-zone mag alleen de zone en records voor het beheerde domein zelf bevatten. Maak geen extra zones in Azure AD DS om benoemde resources in andere DNS-naamruimten op te lossen. Gebruik in plaats daarvan voorwaardelijke doorstuurers in het beheerde Azure AD DS-domein om de DNS-server te vertellen waar ze naartoe moeten gaan om adressen voor die bronnen op te lossen.

Een voorwaardelijke doorstuurer is een configuratieoptie in een DNS-server waarmee u een DNS-domein definiëren, zoals *contoso.com*, om query's naar door te sturen. In plaats van dat de lokale DNS-server query's voor records in dat domein probeert op te lossen, worden DNS-query's doorgestuurd naar de geconfigureerde DNS voor dat domein. Deze configuratie zorgt ervoor dat de juiste DNS-records worden geretourneerd, omdat u geen lokale DNS-zone maakt met dubbele records in het beheerde Azure AD DS-domein om deze bronnen weer te geven.

Voer de volgende stappen uit om een voorwaardelijke doorstuurserver in uw door Azure AD DS-beheerde domein te maken:

1. Selecteer uw AZURE AD DS DNS-zone, zoals *aaddscontoso.com*.vb
1. Selecteer **Voorwaardelijke doorstuurers**en selecteer Vervolgens met de rechtermuisknop en kies **Nieuwe voorwaardelijke doorstuurer...**
1. Voer uw andere **DNS-domein**in, zoals *contoso.com*, voer vervolgens de IP-adressen van de DNS-servers voor die naamruimte in, zoals in het volgende voorbeeld wordt weergegeven:

    ![Een voorwaardelijke forwarder voor de DNS-server toevoegen en configureren](./media/manage-dns/create-conditional-forwarder.png)

1. Schakel het selectievakje voor **Deze voorwaardelijke doorstuurserver opslaan in Active Directory in en repliceer deze als volgt**en selecteer vervolgens de optie voor Alle *DNS-servers in dit domein*, zoals in het volgende voorbeeld wordt weergegeven:

    ![DNS-console - domein beheren](./media/manage-dns/store-in-domain.png)

    > [!IMPORTANT]
    > Als de voorwaardelijke doorstuurserver is opgeslagen in het *forest* in plaats van het *domein,* mislukt de voorwaardelijke doorstuurserver.

1. Als u de voorwaardelijke doorstuurer wilt maken, selecteert u **OK**.

Naamoplossing van de resources in andere naamruimten van VM's die zijn verbonden met het beheerde Azure AD DS-domein, moet nu correct worden opgelost. Query's voor het DNS-domein dat is geconfigureerd in de voorwaardelijke expediteer worden doorgegeven aan de relevante DNS-servers.

## <a name="next-steps"></a>Volgende stappen

Zie het [artikel over DNS-hulpprogramma's op Technet](https://technet.microsoft.com/library/cc753579.aspx)voor meer informatie over het beheren van DNS.

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
