---
title: DNS voor Azure AD-domeinservices beheren | Microsoft Documenten
description: Meer informatie over het installeren van de DNS-serverhulpprogramma's om DNS te beheren voor een beheerd Azure Active Directory Domain Services-domein.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 10/31/2019
ms.author: iainfou
ms.openlocfilehash: f0d8f73b47b1110e8e05365013bbf07fd94eb6ca
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655083"
---
# <a name="administer-dns-in-an-azure-ad-domain-services-managed-domain"></a>DNS beheren in een beheerd Azure AD Domain Services-domein

In Azure Active Directory Domain Services (Azure AD DS) is EEN belangrijk onderdeel DNS (Domain Name Resolution). Azure AD DS bevat een DNS-server die naamomzetting biedt voor het beheerde domein. Deze DNS-server bevat ingebouwde DNS-records en -updates voor de belangrijkste onderdelen waarmee de service kan worden uitgevoerd.

Wanneer u uw eigen toepassingen en services uitvoert, moet u mogelijk DNS-records maken voor machines die niet zijn verbonden met het domein, virtuele IP-adressen configureren voor load balancers of externe DNS-expediteurs instellen. Gebruikers die deel uitmaken van de groep *AAD DC-beheerders* krijgen DNS-beheerrechten op het beheerde Azure AD DS-domein en kunnen aangepaste DNS-records maken en bewerken.

In een hybride omgeving worden DNS-zones en records die zijn geconfigureerd in een on-premises AD DS-omgeving, niet gesynchroniseerd met Azure AD DS. Als u uw eigen DNS-vermeldingen wilt definiëren en gebruiken, maakt u records in de Azure AD DS DNS-server of gebruikt u voorwaardelijke expediteurs die naar bestaande DNS-servers in uw omgeving wijzen.

In dit artikel ziet u hoe u de DNS-serverhulpprogramma's installeert en vervolgens de DNS-console gebruikt om records in Azure AD DS te beheren.

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

    ![Kies ervoor om de DNS-serverhulpprogramma's te installeren in de lijst met beschikbare hulpprogramma's voor rolbeheer](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)

1. Selecteer **installeren** op **Install**de pagina Bevestiging . Het kan een minuut of twee duren om de tools voor groepsbeleidsbeheer te installeren.
1. Wanneer de installatie van de functie is voltooid, selecteert u **Sluiten** om de wizard **Rollen en onderdelen toevoegen** af te sluiten.

## <a name="open-the-dns-management-console-to-administer-dns"></a>De DNS-beheerconsole openen om DNS te beheren

Met de DNS-servertools u DNS-records beheren op het beheerde Azure AD DS-domein.

> [!NOTE]
> Als u DNS wilt beheren in een door Azure AD DS beheerd domein, moet u zijn aangemeld bij een gebruikersaccount dat lid is van de groep *AAD DC Administrators.*

1. Selecteer In het startscherm de optie **Systeembeheer**. Er wordt een lijst met beschikbare beheertools weergegeven, waaronder **DNS die** in de vorige sectie is geïnstalleerd. Selecteer **DNS** om de DNS-beheerconsole te starten.
1. Selecteer in het dialoogvenster **Verbinding maken met DNS-server** de optie **De volgende computer**en voer vervolgens de DNS-domeinnaam van het beheerde domein in, zoals *aaddscontoso.com:*

    ![Verbinding maken met het beheerde Azure AD DS-domein in de DNS-console](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)

1. De DNS-console maakt verbinding met het opgegeven door Azure AD DS beheerde domein. Vouw de **opzoekzones vooruit** of **opzoekzones voor reverse uit** om uw vereiste DNS-items te maken of bestaande records te bewerken als dat nodig is.

    ![DNS-console - domein beheren](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)

> [!WARNING]
> Wanneer u records beheert met de HULPPROGRAMMA's van DNS-server, moet u ervoor zorgen dat u de ingebouwde DNS-records die door Azure AD DS worden gebruikt, niet verwijdert of wijzigt. Ingebouwde DNS-records bevatten dns-records van domeinen, naamserverrecords en andere records die worden gebruikt voor DC-locatie. Als u deze records wijzigt, worden domeinservices verstoord op het virtuele netwerk.

## <a name="next-steps"></a>Volgende stappen

Zie het [artikel over DNS-hulpprogramma's op Technet](https://technet.microsoft.com/library/cc753579.aspx)voor meer informatie over het beheren van DNS.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
