---
title: Azure AD-toepassingsproxy implementeren voor Azure AD Domain Services | Microsoft Docs
description: Azure-AD-toepassingsproxy op Azure Active Directory Domain Services beheerde domeinen gebruiken
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: iainfou
ms.openlocfilehash: 80c3b2120a617e5c4c0f8de252b9436753fea011
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754406"
---
# <a name="deploy-azure-ad-application-proxy-on-an-azure-ad-domain-services-managed-domain"></a>Azure-AD-toepassingsproxy implementeren op een Azure AD Domain Services beheerd domein
Azure Active Directory (AD)-toepassings proxy helpt u externe werk nemers te ondersteunen door on-premises toepassingen te publiceren voor toegang via internet. Met Azure AD Domain Services kunt u nu oudere toepassingen die on-premises worden uitgevoerd, verplaatsen naar Azure-infrastructuur services. U kunt deze toepassingen vervolgens publiceren met behulp van de Azure-AD-toepassingsproxy om veilige externe toegang te bieden aan gebruikers in uw organisatie.

Als u geen ervaring hebt met Azure AD-toepassingsproxy, kunt u meer informatie over deze functie vinden in het volgende artikel: [veilige externe toegang bieden tot on-premises toepassingen](../active-directory/manage-apps/application-proxy.md).

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Voordat u begint
Voor het uitvoeren van de taken die in dit artikel worden vermeld, hebt u het volgende nodig:

1. Een geldig **Azure-abonnement**.
2. Een **Azure AD-Directory** : gesynchroniseerd met een on-premises Directory of een alleen-Cloud Directory.
3. Een **Azure AD Premium licentie** is vereist voor het gebruik van de Azure AD-toepassingsproxy.
4. **Azure AD Domain Services** moet zijn ingeschakeld voor de Azure AD-adres lijst. Als u dit nog niet hebt gedaan, volgt u alle taken die in de aan de slag- [hand leiding](tutorial-create-instance.md)worden beschreven.

<br>

## <a name="task-1---enable-azure-ad-application-proxy-for-your-azure-ad-directory"></a>Taak 1: Azure AD-toepassingsproxy inschakelen voor uw Azure AD-adres lijst
Voer de volgende stappen uit om Azure AD-toepassingsproxy in te scha kelen voor uw Azure AD-adres lijst.

1. Meld u aan als beheerder in de [Azure-portal](https://portal.azure.com).

2. Klik op **Azure Active Directory** om het overzicht van mappen weer te geven. Klik op **bedrijfs toepassingen**.

3. Klik op **toepassings proxy**.

4. Als u de connector wilt downloaden, klikt u op de knop **connector** .

5. Ga naar de download pagina en accepteer de licentie voorwaarden en de privacyverklaring en klik op de knop **downloaden** .

    ![Downloaden bevestigen](./media/app-proxy/app-proxy-enabled-confirm-download.png)


## <a name="task-2---provision-domain-joined-windows-servers-to-deploy-the-azure-ad-application-proxy-connector"></a>Taak 2: Windows-servers die lid zijn van een domein inrichten voor het implementeren van de Azure AD-toepassingsproxy-connector
U hebt virtuele Windows Server-machines die lid zijn van een domein waarop u de Azure AD-toepassingsproxy-connector kunt installeren. Voor sommige toepassingen kunt u ervoor kiezen om meerdere servers in te richten waarop de connector is geïnstalleerd. Deze implementatie optie biedt meer Beschik baarheid en helpt zwaarere verificatie belasting te verwerken.

Richt de connector servers in op hetzelfde virtuele netwerk (of een verbonden/gekoppeld virtueel netwerk), waarin u uw door Azure AD Domain Services beheerd domein hebt ingeschakeld. Op dezelfde manier moeten de servers die als host fungeren voor de toepassingen die u publiceert via de toepassings proxy, worden geïnstalleerd op hetzelfde virtuele Azure-netwerk.

Voor het inrichten van connector servers, volgt u de taken die worden beschreven in het artikel [een virtuele Windows-machine toevoegen aan een beheerd domein](active-directory-ds-admin-guide-join-windows-vm.md).


## <a name="task-3---install-and-register-the-azure-ad-application-proxy-connector"></a>Taak 3: de Azure AD-toepassingsproxy-connector installeren en registreren
Eerder hebt u een virtuele Windows Server-machine ingericht en gekoppeld aan het beheerde domein. In deze taak installeert u de Azure AD-toepassingsproxy-connector op deze virtuele machine.

1. Kopieer het installatie pakket van de connector naar de virtuele machine waarop u de Azure AD Web Application proxy-connector installeert.

2. Voer **AADApplicationProxyConnectorInstaller. exe** uit op de virtuele machine. Ga akkoord met de licentie voorwaarden van de software.

    ![Voor waarden accepteren voor installatie](./media/app-proxy/app-proxy-install-connector-terms.png)
3. Tijdens de installatie wordt u gevraagd de connector te registreren bij de toepassings proxy van uw Azure AD-adres lijst.
   * Geef uw **Azure AD-toepassings beheerders referenties**op. De Tenant van de toepassings beheerder kan afwijken van uw Microsoft Azure referenties.
   * Het beheerders account dat wordt gebruikt om de connector te registreren, moet behoren tot dezelfde map waar u de Application proxy-service hebt ingeschakeld. Als het Tenant domein bijvoorbeeld contoso.com is, moet de beheerder worden admin@contoso.com of een andere geldige alias in dat domein.
   * Als verbeterde beveiliging van Internet Explorer is ingeschakeld voor de server waarop u de connector installeert, wordt het registratie scherm mogelijk geblokkeerd. Volg de instructies in het fout bericht om toegang toe te staan. Zorg ervoor dat de verbeterde beveiliging van Internet Explorer is uitgeschakeld.
   * Zie [Troubleshoot Application Proxy](../active-directory/manage-apps/application-proxy-troubleshoot.md) (Engelstalig) als de registratie van de connector niet lukt.

     ![Connector geïnstalleerd](./media/app-proxy/app-proxy-connector-installed.png)
4. Voer de probleem oplosser voor Azure AD-toepassingsproxy-connector uit om ervoor te zorgen dat de connector goed werkt. U ziet een geslaagd rapport nadat u de probleem Oplosser hebt uitgevoerd.

    ![Probleem Oplosser geslaagd](./media/app-proxy/app-proxy-connector-troubleshooter.png)
5. De zojuist geïnstalleerde connector wordt weer gegeven op de pagina Toepassings proxy in uw Azure AD-adres lijst.

    ![In de Azure Portal ziet de geïnstalleerde connector er als beschikbaar uit](./media/app-proxy/app-proxy-connector-page.png)

> [!NOTE]
> U kunt ervoor kiezen om connectors op meerdere servers te installeren om te zorgen voor hoge Beschik baarheid voor het verifiëren van toepassingen die zijn gepubliceerd via de Azure-AD-toepassingsproxy. Voer dezelfde stappen uit om de connector te installeren op andere servers die zijn gekoppeld aan uw beheerde domein.
>
>

## <a name="next-steps"></a>Volgende stappen
U hebt de Azure-AD-toepassingsproxy ingesteld en geïntegreerd met uw Azure AD Domain Services beheerd domein.

* **Uw toepassingen migreren naar Azure virtual machines:** U kunt uw toepassingen van on-premises servers omhoog en omlaag verplaatsen naar virtuele Azure-machines die zijn gekoppeld aan uw beheerde domein. Zo kunt u de kosten voor de infra structuur van on-premises servers weghalen.

* **Toepassingen publiceren met Azure AD-toepassingsproxy:** Publiceer toepassingen die worden uitgevoerd op uw virtuele Azure-machines met behulp van de Azure AD-toepassingsproxy. Zie [toepassingen publiceren met Azure AD-toepassingsproxy](../active-directory/manage-apps/application-proxy-publish-azure-portal.md) voor meer informatie


## <a name="deployment-note---publish-iwa-integrated-windows-authentication-applications-using-azure-ad-application-proxy"></a>Implementatie Opmerking: Publiceer IWA-toepassingen (Integrated Windows Authentication) met Azure AD-toepassingsproxy
Schakel eenmalige aanmelding in voor uw toepassingen met geïntegreerde Windows-verificatie (IWA) door toepassings proxy connectors toestemming te verlenen voor het imiteren van gebruikers en het verzenden en ontvangen van tokens namens hen. Configureer Kerberos-beperkte delegering (KCD) voor de connector om de vereiste machtigingen te verlenen voor toegang tot bronnen op het beheerde domein. Gebruik het op bronnen gebaseerde KCD-mechanisme voor beheerde domeinen voor een betere beveiliging.


### <a name="enable-resource-based-kerberos-constrained-delegation-for-the-azure-ad-application-proxy-connector"></a>Op resources gebaseerde Kerberos-beperkte delegering inschakelen voor de Azure AD-toepassingsproxy-connector
De Azure-toepassing proxy connector moet worden geconfigureerd voor Kerberos-beperkte delegering (KCD), zodat deze gebruikers op het beheerde domein kan imiteren. U hebt geen domein beheerders rechten op een Azure AD Domain Services beheerd domein. **Traditionele KCD op account niveau kunnen daarom niet worden geconfigureerd in een beheerd domein**.

Gebruik op resources gebaseerde KCD zoals beschreven in dit [artikel](deploy-kcd.md).

> [!NOTE]
> U moet lid zijn van de groep AAD DC Administrators om het beheerde domein te beheren met AD Power shell-cmdlets.
>
>

Gebruik de Power shell-cmdlet Get-ADComputer om de instellingen op te halen voor de computer waarop de Azure AD-toepassingsproxy-connector is geïnstalleerd.
```powershell
$ConnectorComputerAccount = Get-ADComputer -Identity contoso-proxy.contoso.com
```

Vervolgens gebruikt u de cmdlet Set-ADComputer om KCD op basis van resources in te stellen voor de bron server.
```powershell
Set-ADComputer contoso-resource.contoso.com -PrincipalsAllowedToDelegateToAccount $ConnectorComputerAccount
```

Als u meerdere connectors voor toepassings proxy hebt geïmplementeerd op uw beheerde domein, moet u op resources gebaseerde KCD configureren voor elk van deze connector exemplaren.


## <a name="related-content"></a>Gerelateerde inhoud
* [Azure AD Domain Services aan de slag-hand leiding](tutorial-create-instance.md)
* [Beperkte Kerberos-delegering configureren voor een beheerd domein](deploy-kcd.md)
* [Overzicht van beperkte Kerberos-overdracht](https://technet.microsoft.com/library/jj553400.aspx)
