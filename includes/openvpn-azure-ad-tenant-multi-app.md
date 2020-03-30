---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/18/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e950d194ab48cec1a70c7bd17617332cb858a55d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77485616"
---
## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. De Azure AD-tenant maken

Maak een Azure AD-tenant met de stappen in [het artikel Een nieuw tenant](../articles/active-directory/fundamentals/active-directory-access-create-new-tenant.md) maken:

* Organisatienaam
* Oorspronkelijke domeinnaam

  Voorbeeld:

   ![Nieuwe Azure AD-tenant](./media/openvpn-azure-ad-tenant-multi-app/new-tenant.png)

## <a name="2-create-tenant-users"></a><a name="users"></a>2. Tenant-gebruikers maken

In deze stap maakt u twee Azure AD-tenantgebruikers: één algemeen beheerdersaccount en één hoofdgebruikersaccount. Het hoofdgebruikersaccount wordt gebruikt als uw hoofdinsluitingsaccount (serviceaccount). Wanneer u een Azure AD-tenantgebruikersaccount maakt, past u de maprol aan voor het type gebruiker dat u wilt maken. Gebruik de stappen in [dit artikel](../articles/active-directory/fundamentals/add-users-azure-active-directory.md) om ten minste twee gebruikers voor uw Azure AD-tenant te maken. Wijzig de **maprol** om de accounttypen te maken:

* Globale beheerder
* Gebruiker

## <a name="3-register-the-vpn-client"></a><a name="register-client"></a>3. Registreer de VPN-client

Registreer de VPN-client in de Azure AD-tenant.

1. Zoek de directory-id van de map die u wilt gebruiken voor verificatie. Het wordt vermeld in de sectie Eigenschappen van de Active Directory-pagina.

    ![Directory-id](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

2. Kopieer de Map-id.

3. Meld u aan bij de Azure-portal als gebruiker waaraan de **globale beheerdersrol** is toegewezen.

4. Geef vervolgens toestemming van de beheerder. Kopieer en plak de URL die betrekking heeft op uw implementatielocatie in de adresbalk van uw browser:

    Public

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Duitsland

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Selecteer het globale **beheerdersaccount** als u daarom wordt gevraagd.

    ![Directory-id](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. Selecteer **Accepteren** wanneer daarom wordt gevraagd.

    ![Accepteren](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. Onder uw Azure AD wordt in **Enterprise-toepassingen** **Azure VPN** weergegeven.

     ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azure-vpn.png)

## <a name="4-register-additional-applications"></a><a name="register-apps"></a>4. Aanvullende aanvragen registreren

In deze stap registreert u extra toepassingen voor verschillende gebruikers en groepen.

1. Klik onder uw Azure Active Directory op **App-registraties** en vervolgens **+ Nieuwe registratie**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. Voer **op** de pagina Een aanvraag registreren de **naam**in . Selecteer de gewenste **ondersteunde accounttypen**en klik op **Registreren**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. Zodra de nieuwe app is geregistreerd, klikt u op Een API onder het app-blad **blootstellen.**

4. Klik **op + Een bereik toevoegen**.

5. Laat de **standaard-toepassings-id URI**. Klik **op Opslaan en doorgaan**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app3.png)

6. Vul de vereiste velden in en zorg ervoor dat **status** is **ingeschakeld**. Klik **op Bereik toevoegen**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app4.png)

7. Klik **op Een API blootstellen** en vervolgens + Een **clienttoepassing toevoegen**.  Voer **voor client-id**de volgende waarden in, afhankelijk van de cloud:

    - Voer **41b23e61-6c1e-4545-b367-cd054e0ed4b4** in voor Azure **Public**
    - Voer **51bb15d4-3a4f-4ebf-9dca-40096fe32426** in voor Azure **Government**
    - Voer **538ee9e6-310a-468d-afef-ea97365856a9** voor Azure **Duitsland in**
    - Voer **49f817b6-84ae-4cc0-928c-73f27289b3a a** voor Azure **China 21Vianet in**

8. Klik **op Toepassing toevoegen**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app5.png)

9. Kopieer de **toepassings-id (client)** van de pagina **Overzicht.** U hebt deze informatie nodig om uw VPN-gateway(s) te configureren.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. Herhaal de stappen in deze [sectie extra toepassingen](#register-apps) registreren om zoveel mogelijk toepassingen te maken die nodig zijn voor uw beveiligingsvereiste. Elke toepassing wordt gekoppeld aan een VPN-gateway en kan een andere set gebruikers hebben. Er kan slechts één toepassing aan een gateway worden gekoppeld.

## <a name="5-assign-users-to-applications"></a><a name="assign-users"></a>5. Gebruikers toewijzen aan toepassingen

Wijs de gebruikers toe aan uw toepassingen.

1. Selecteer onder **Azure AD-> Enterprise-toepassingen**de nieuw geregistreerde toepassing en klik op **Eigenschappen**. Ervoor zorgen dat **de gebruiker toewijzing vereist?** is ingesteld op **ja**. Klik op **Opslaan**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. Klik op de app-pagina op **Gebruikers en groepen**en klik vervolgens op Gebruiker **toevoegen**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user3.png)

3. Klik **onder Toewijzing toevoegen**op Gebruikers en **groepen**. Selecteer de gebruikers die u toegang wilt hebben tot deze VPN-toepassing. Klik **op Selecteren**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user4.png)