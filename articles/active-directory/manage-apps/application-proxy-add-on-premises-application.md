---
title: Tutorial - Add an on-premises app - Application Proxy in Azure AD
description: Azure Active Directory (Azure AD) heeft een Application Proxy-service waarmee gebruikers toegang krijgen tot on-premises toepassingen door zich aan te melden met hun Azure AD-account. This tutorial shows you how to prepare your environment for use with Application Proxy. Then, it uses the Azure portal to add an on-premises application to your Azure AD tenant.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6f1f812bb7d31319476e6b940443e067fac895f
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420451"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Tutorial: Add an on-premises application for remote access through Application Proxy in Azure Active Directory

Azure Active Directory (Azure AD) heeft een Application Proxy-service waarmee gebruikers toegang krijgen tot on-premises toepassingen door zich aan te melden met hun Azure AD-account. In deze zelfstudie wordt uw omgeving voorbereid voor gebruik van Application Proxy. Zodra uw omgeving gereed is, gebruikt u de Azure-portal om een on-premises toepassing toe te voegen aan uw Azure AD-tenant.

Deze zelfstudie:

> [!div class="checklist"]
> * Opent poorten voor uitgaand verkeer en verschaft toegang tot specifieke URL 's
> * Installeert de connector op uw Windows-server en registreert deze bij Application Proxy
> * Controleert of de connector juist is geïnstalleerd en geregistreerd
> * Voegt een toepassing toe aan uw Azure AD-tenant
> * Verifies a test user can sign on to the application by using an Azure AD account

## <a name="before-you-begin"></a>Voordat u begint

To add an on-premises application to Azure AD, you need:

* A [Microsoft Azure AD premium subscription](https://azure.microsoft.com/pricing/details/active-directory)
* An application administrator account
* User identities must be synchronized from an on-premises directory or created directly within your Azure AD tenants. Identity synchronization allows Azure AD to pre-authenticate users before granting them access to App Proxy published applications and to have the necessary user identifier information to perform single sign-on (SSO).

### <a name="windows-server"></a>Windows-server

Als u Application Proxy wilt gebruiken, hebt u een Windows-server nodig waarop Windows Server 2012 R2 of hoger wordt uitgevoerd. U installeert de Application Proxy-connector op de server. Deze connectorserver moet verbinding maken met de Application Proxy-service in Azure en met de on-premises toepassingen die u van plan bent te publiceren.

Om een hoge beschikbaarheid in uw productieomgeving te realiseren wordt aangeraden meer dan één Windows-server te gebruiken. Voor deze zelfstudie is één Windows-server toereikend.

> [!IMPORTANT]
> If you are installing the connector on Windows Server 2019 there is a HTTP2 limitation. A workaround to use the connector on this version is adding the following registry key and restarting the server. Note, this is a machine registry wide key. 
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp\EnableDefaultHttp2 (DWORD) Value: 0 
    ```

#### <a name="recommendations-for-the-connector-server"></a>Recommendations for the connector server

1. Plaats de connectorserver fysiek dicht bij de toepassingsservers om de prestaties tussen de connector en de toepassing te optimaliseren. Zie [Overwegingen bij de netwerktopologie](application-proxy-network-topology.md) voor meer informatie.
1. The connector server and the web applications servers should belong to the same Active Directory domain or span trusting domains. Having the servers in the same domain or trusting domains is a requirement for using single sign-on (SSO) with Integrated Windows Authentication (IWA) and Kerberos Constrained Delegation (KCD). Als de connectorserver en de webtoepassingsservers zich in verschillende Active Directory-domeinen bevinden, moet u delegatie op basis van resources gebruiken voor eenmalige aanmelding. Zie [KCD voor eenmalige aanmelding met Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md) voor meer informatie.

> [!WARNING]
> If you've deployed Azure AD Password Protection Proxy, do not install Azure AD Application Proxy and Azure AD Password Protection Proxy together on the same machine. Azure AD Application Proxy and Azure AD Password Protection Proxy install different versions of the Azure AD Connect Agent Updater service. These different versions are incompatible when installed together on the same machine.

#### <a name="tls-requirements"></a>TLS requirements

Voor de Windows-connectorserver moet TLS 1.2 zijn ingeschakeld voordat u de Application Proxy-connector installeert.

TLS 1.2 inschakelen:

1. Stel de volgende registersleutels in:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Restart the server.

> [!IMPORTANT]
> To provide the best-in-class encryption to our customers, the Application Proxy service limits access to only TLS 1.2 protocols. These changes were gradually rolled out and effective since August 31, 2019. Make sure that all your client-server and browser-server combinations are updated to use TLS 1.2 to maintain connection to Application Proxy service. These include clients your users are using to access applications published through Application Proxy. See Preparing for [TLS 1.2 in Office 365](https://support.microsoft.com/help/4057306/preparing-for-tls-1-2-in-office-365) for useful references and resources.

## <a name="prepare-your-on-premises-environment"></a>Bereid uw on-premises omgeving voor

Start by enabling communication to Azure data centers to prepare your environment for Azure AD Application Proxy. If there's a firewall in the path, make sure it's open. An open firewall allows the connector to make HTTPS (TCP) requests to the Application Proxy.

### <a name="open-ports"></a>Poorten openen

Open de volgende poorten voor **uitgaand** verkeer.

   | Poortnummer | Hoe dat wordt gebruikt |
   | --- | --- |
   | 80 | Het downloaden van certificaatintrekkingslijsten (CRL's) tijdens het valideren van het SSL-certificaat |
   | 443 | Alle uitgaande communicatie met de Application Proxy-service |

Als met uw firewall verkeer wordt afgedwongen op basis van de herkomst van gebruikers, open dan ook poorten 80 en 443 voor verkeer dat afkomstig is van Windows-services die als netwerkservice worden uitgevoerd.

### <a name="allow-access-to-urls"></a>Toegang tot URL's toestaan

Sta toegang tot de volgende URL's toe:

| URL | Hoe dat wordt gebruikt |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Communicatie tussen de connector en de Application Proxy-cloudservice |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Azure uses these URLs to verify certificates. |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>\*.microsoftonline.com<br>\*.microsoftonline-p.com<br>\*.msauth.net<br>\*.msauthimages.net<br>\*.msecnd.net<br>\*.msftauth.net<br>\*.msftauthimages.net<br>\*.phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net | De connector gebruikt deze URL's tijdens het registratieproces. |

You can allow connections to \*.msappproxy.net and \*.servicebus.windows.net if your firewall or proxy lets you configure DNS allow lists. If not, you need to allow access to the [Azure IP ranges and Service Tags - Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519). die overigens elke week worden bijgewerkt.

## <a name="install-and-register-a-connector"></a>Een connector installeren en registreren

To use Application Proxy, install a connector on each Windows server you're using with the Application Proxy service. De connector is een agent die de uitgaande verbinding vanaf de on-premises toepassingsservers naar Application Proxy in Azure AD beheert. U kunt een connector installeren op servers waarop ook andere verificatie-agents zijn geïnstalleerd, zoals Azure AD Connect.

De connector installeren:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) als een toepassingsbeheerder van de map die gebruikmaakt van Application Proxy. Als het domein van de tenant bijvoorbeeld contoso.com is, moet de beheerder admin@contoso.com of een andere beheerdersalias in dat domein zijn.
1. Select your username in the upper-right corner. Verify you're signed in to a directory that uses Application Proxy. If you need to change directories, select **Switch directory** and choose a directory that uses Application Proxy.
1. In left navigation panel, select **Azure Active Directory**.
1. Under **Manage**, select **Application proxy**.
1. Select **Download connector service**.

    ![Download connector service to see the Terms of Service](./media/application-proxy-add-on-premises-application/application-proxy-download-connector-service.png)

1. Lees de servicevoorwaarden. When you're ready, select **Accept terms & Download**.
1. At the bottom of the window, select **Run** to install the connector. Er wordt een installatiewizard geopend.
1. Follow the instructions in the wizard to install the service. Wanneer u wordt gevraagd de connector te registreren voor de Application Proxy voor uw Azure AD-tenant, geeft u uw gegevens als toepassingsbeheerder op.
    - Voor Internet Explorer (IE) geldt dat als **Verbeterde beveiliging van Internet Explorer** is ingesteld op **Aan**, het registratiescherm niet wordt weergegeven. Volg de instructies in het foutbericht om toegang te krijgen. Make sure that **Internet Explorer Enhanced Security Configuration** is set to **Off**.

### <a name="general-remarks"></a>Algemene opmerkingen

Als u al een connector hebt geïnstalleerd, voert u een nieuwe installatie uit met de meest recente versie. To see information about previously released versions and what changes they include, see [Application Proxy: Version Release History](application-proxy-release-version-history.md).

Als u meer dan één Windows-server voor uw on-premises toepassingen wilt, moet u de connector op elke server installeren en registreren. U kunt de connectors onderverdelen in connectorgroepen. Zie [Connectorgroepen](application-proxy-connector-groups.md) voor meer informatie.

Als uw organisatie proxyservers gebruikt om verbinding met internet te maken, moet u ze configureren voor Application Proxy.  Zie [Werken met bestaande on-premises proxyservers](application-proxy-configure-connectors-with-proxy-servers.md) voor meer informatie. 

Zie [Informatie over Azure AD Application Proxy-connectors](application-proxy-connectors.md) voor meer informatie over connectors, capaciteitsplanning en hoe connectors up-to-date blijven.

## <a name="verify-the-connector-installed-and-registered-correctly"></a>Controleer of de connector juist is geïnstalleerd en geregistreerd

U kunt de Azure-portal of uw Windows-server gebruiken om te bevestigen dat er een nieuwe connector correct is geïnstalleerd.

### <a name="verify-the-installation-through-azure-portal"></a>Verify the installation through Azure portal

Controleren of de connector juist is geïnstalleerd en geregistreerd:

1. Meld u aan bij uw tenantmap in de [Azure-portal](https://portal.azure.com).
1. In the left navigation panel, select **Azure Active Directory**, and then select **Application Proxy** under the **Manage** section. Al uw connectors en connectorgroepen worden op deze pagina weergegeven.
1. View a connector to verify its details. The connectors should be expanded by default. If the connector you want to view isn't expanded, expand the connector to view the details. Een actief groen label geeft aan dat de connector verbinding kan maken met de service. Maar ook al is het label groen, toch kan een netwerkprobleem er nog steeds voor zorgen dat de connector geen berichten ontvangt.

    ![Azure AD Application Proxy Connectors](./media/application-proxy-add-on-premises-application/app-proxy-connectors.png)

For more help with installing a connector, see [Problem installing the Application Proxy Connector](application-proxy-connector-installation-problem.md).

### <a name="verify-the-installation-through-your-windows-server"></a>Verify the installation through your Windows server

Controleren of de connector juist is geïnstalleerd en geregistreerd:

1. Open Windows Services Manager door te klikken op de **Windows**-toets en *services.msc* in te voeren.
1. Controleer of de status van de volgende twee services **Wordt uitgevoerd** is.
   - **Microsoft AAD Application Proxy Connector** enables connectivity.
   - **Microsoft AAD Application Proxy Connector Updater** is een automatische updateservice. De updater controleert op nieuwe versies van de connector en werkt de connector bij als dat nodig is.

     ![Connectorservices voor toepassingsproxy - schermafbeelding](./media/application-proxy-add-on-premises-application/app_proxy_services.png)

1. If the status for the services isn't **Running**, right-click to select each service and choose **Start**.

## <a name="add-an-on-premises-app-to-azure-ad"></a>Een on-premises app toevoegen aan Azure AD

Nu u uw omgeving hebt voorbereid en een connector hebt geïnstalleerd, kunt u on-premises toepassingen gaan toevoegen aan Azure AD.  

1. Meld u aan als beheerder in de [Azure-portal](https://portal.azure.com/).
2. In the left navigation panel, select **Azure Active Directory**.
3. Select **Enterprise applications**, and then select **New application**.
4. In the **On-premises applications** section, select **Add an on-premises application**.
5. In the **Add your own on-premises application** section, provide the following information about your application:

    | Veld | Beschrijving |
    | :---- | :---------- |
    | **Naam** | De naam van de toepassing die wordt weergegeven in het toegangsvenster en in de Azure-portal. |
    | **Interne URL** | Dit is de URL voor toegang tot de toepassing vanuit uw particuliere netwerk. U kunt voor het publiceren een specifiek pad opgeven op de back-endserver, terwijl de rest van de server ongepubliceerd blijft. Op deze manier kunt u verschillende sites op dezelfde server als verschillende apps publiceren en elk daarvan een eigen naam en toegangsregels geven.<br><br>Als u een pad publiceert, moet u ervoor zorgen dat dit alle benodigde installatiekopieën, scripts en opmaakmodellen voor uw toepassing bevat. For example, if your app is at https:\//yourapp/app and uses images located at https:\//yourapp/media, then you should publish https:\//yourapp/ as the path. Deze interne URL hoeft niet de bestemmingspagina te zijn die uw gebruikers te zien krijgen. Zie [Een aangepaste startpagina voor gepubliceerde apps instellen](application-proxy-configure-custom-home-page.md) voor meer informatie. |
    | **Externe URL** | Het adres voor gebruikers om toegang te krijgen tot de app van buiten uw netwerk. Als u het standaarddomein voor Application Proxy niet wilt gebruiken, lees dan de informatie over [aangepaste domeinen in Azure AD Application Proxy](application-proxy-configure-custom-domain.md).|
    | **Verificatie vooraf** | De manier waarop gebruikers door Application Proxy worden geverifieerd voordat ze toegang krijgen tot uw toepassing.<br><br>**Azure Active Directory**: de gebruikers worden omgeleid door Application Proxy zodat ze zich kunnen aanmelden met Azure AD. Hierbij worden hun machtigingen geverifieerd voor de map en de toepassing. We recommend keeping this option as the default so that you can take advantage of Azure AD security features like Conditional Access and Multi-Factor Authentication. **Azure Active Directory** is vereist voor het bewaken van de toepassing met Microsoft Cloud Application Security.<br><br>**Passthrough** - Users don't have to authenticate against Azure AD to access the application. U kunt nog steeds verificatievereisten op de back-end instellen. |
    | **Connectorgroep** | Connectors verwerken de externe toegang tot uw toepassing en met connectorgroepen kunt u connectors en toepassingen indelen per regio, netwerk of doel. Als u nog geen connectorgroepen hebt gemaakt, wordt uw toepassing toegewezen als **Standaard**.<br><br>Als uw toepassing gebruikmaakt van WebSockets om verbinding te maken, moeten alle connectors in de groep versie 1.5.612.0 of hoger hebben.|

6. Configureer zo nodig**aanvullende instellingen**. Voor de meeste toepassingen moet u voor deze instellingen de standaardwaarden behouden. 

    | Veld | Beschrijving |
    | :---- | :---------- |
    | **Toepassingstime-out voor de back-end** | Stel deze waarde alleen in op **Lang** als uw toepassing traag is met verifiëren en verbinding maken. At default, the backend application timeout has a length of 85 seconds. When set to long, the backend timeout is increased to 180 seconds. |
    | **Alleen-HTTP-cookies gebruiken** | Stel deze waarde in op **Ja** om ervoor te zorgen dat Application Proxy-cookies de HTTPOnly-vlag in de HTTP-antwoordheader bevatten. Als u Extern bureaublad-services gebruikt, stelt u deze waarde in op **Nee**.|
    | **Beveiligde cookies gebruiken**| Stel deze waarde in op **Ja** om cookies te verzenden via een beveiligd kanaal, zoals een versleutelde HTTPS-aanvraag.
    | **Permanente cookies gebruiken**| Houd deze waarde ingesteld op **Nee**. Only use this setting for applications that can't share cookies between processes. For more information about cookie settings, see [Cookie settings for accessing on-premises applications in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).
    | **URL's in headers vertalen** | Laat deze waarde op **Ja** staan, tenzij voor uw toepassing de oorspronkelijke host-header in de verificatieaanvraag moet zijn opgenomen. |
    | **URL's vertalen in de hoofdtekst van de toepassing** | Keep this value as **No** unless you have hardcoded HTML links to other on-premises applications and don't use custom domains. Zie [Vertaling koppelen aan Application Proxy](application-proxy-configure-hard-coded-link-translation.md) voor meer informatie.<br><br>Stel deze waarde in op **Ja** als u van plan bent om deze toepassing te bewaken met Microsoft Cloud App Security (MCAS). For more information, see [Configure real-time application access monitoring with Microsoft Cloud App Security and Azure Active Directory](application-proxy-integrate-with-microsoft-cloud-application-security.md). |

7. Selecteer **Toevoegen**.

## <a name="test-the-application"></a>De toepassing testen

U kunt nu testen of de toepassing correct is toegevoegd. Hiervoor gaat u in de volgende stappen een gebruikersaccount toevoegen aan de toepassing en u vervolgens proberen aan te melden.

### <a name="add-a-user-for-testing"></a>Een gebruiker toevoegen voor het testen

Controleer voordat u een gebruiker aan de toepassing toevoegt of het gebruikersaccount al machtigingen heeft voor toegang tot de toepassing vanuit het bedrijfsnetwerk.

Een testgebruiker toevoegen:

1. Select **Enterprise applications**, and then select the application you want to test.
2. Select **Getting started**, and then select **Assign a user for testing**.
3. Under **Users and groups**, select **Add user**.
4. Under **Add assignment**, select **Users and groups**. The **User and groups** section appears.
5. Choose the account you want to add.
6. Choose **Select**, and then select **Assign**.

### <a name="test-the-sign-on"></a>De aanmelding testen

To test the sign-on to the application:

1. Navigeer in uw browser naar de externe URL die u hebt geconfigureerd tijdens de stap voor het publiceren. Als het goed is, ziet u nu het startscherm.
1. Sign in as the user you created in the previous section.

Zie [Problemen en foutberichten met Application Proxy oplossen](application-proxy-troubleshoot.md) voor het oplossen van problemen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u uw on-premises omgeving voorbereid om te werken met Application Proxy en hebt u vervolgens de Application Proxy-connector geïnstalleerd en geregistreerd. Vervolgens hebt u een toepassing toegevoegd aan uw Azure AD-tenant. U hebt ook gecontroleerd of een gebruiker zich kan aanmelden bij de toepassing met behulp van een Azure AD-account

U hebt het volgende gedaan:
> [!div class="checklist"]
> * Poorten geopend voor uitgaand verkeer en toegang verschaft tot specifieke URL 's
> * De connector geïnstalleerd op uw Windows-server en deze geregistreerd bij Application Proxy
> * Gecontroleerd of de connector juist is geïnstalleerd en geregistreerd
> * Een toepassing toegevoegd aan uw Azure AD-tenant
> * Verified a test user can sign on to the application by using an Azure AD account

U kunt de toepassing nu gaan configureren voor eenmalige aanmelding. Use the following link to choose a single sign-on method and to find single sign-on tutorials.

> [!div class="nextstepaction"]
> [Eenmalige aanmelding configureren](what-is-single-sign-on.md#choosing-a-single-sign-on-method)
