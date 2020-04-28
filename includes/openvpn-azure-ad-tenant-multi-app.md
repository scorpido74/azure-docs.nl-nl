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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77485616"
---
## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. de Azure AD-Tenant maken

Maak een Azure AD-Tenant met behulp van de stappen in het artikel [een nieuwe Tenant maken](../articles/active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Organisatie naam
* Oorspronkelijke domeinnaam

  Voorbeeld:

   ![Nieuwe Azure AD-Tenant](./media/openvpn-azure-ad-tenant-multi-app/new-tenant.png)

## <a name="2-create-tenant-users"></a><a name="users"></a>2. Tenant gebruikers maken

In deze stap maakt u twee Azure AD-Tenant gebruikers: één globaal beheerders account en één hoofd gebruikers account. Het hoofd gebruikers account wordt gebruikt als uw hoofd account voor insluiten (Service-account). Wanneer u een Azure AD-Tenant gebruikers account maakt, past u de Directory-rol aan voor het type gebruiker dat u wilt maken. Volg de stappen in [dit artikel](../articles/active-directory/fundamentals/add-users-azure-active-directory.md) om ten minste twee gebruikers te maken voor uw Azure AD-Tenant. Zorg ervoor dat u de **Directory-rol** wijzigt om de volgende account typen te maken:

* Globale beheerder
* Gebruiker

## <a name="3-register-the-vpn-client"></a><a name="register-client"></a>3. de VPN-client registreren

Registreer de VPN-client in de Azure AD-Tenant.

1. Zoek de Directory-ID van de map die u voor verificatie wilt gebruiken. Deze wordt weer gegeven in de sectie eigenschappen van de pagina Active Directory.

    ![Map-ID](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

2. Kopieer de Map-id.

3. Meld u aan bij de Azure Portal als gebruiker aan wie de rol van **globale beheerder** is toegewezen.

4. Geef vervolgens toestemming voor de beheerder. Kopieer en plak de URL die betrekking heeft op uw implementatie locatie in de adres balk van uw browser:

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

5. Selecteer het account van de **globale beheerder** als u hierom wordt gevraagd.

    ![Map-ID](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. Selecteer **accepteren** wanneer u hierom wordt gevraagd.

    ![Accepteren](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. Onder uw Azure AD, in **bedrijfs toepassingen**, wordt **Azure VPN** weer gegeven.

     ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azure-vpn.png)

## <a name="4-register-additional-applications"></a><a name="register-apps"></a>4. aanvullende toepassingen registreren

In deze stap registreert u aanvullende toepassingen voor verschillende gebruikers en groepen.

1. Klik onder uw Azure Active Directory op **app-registraties** en vervolgens op **+ nieuwe registratie**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. Op de pagina **een toepassing registreren** voert u de **naam**in. Selecteer de gewenste **ondersteunde account typen**en klik vervolgens op **registreren**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. Zodra de nieuwe app is geregistreerd, klikt u op **een API beschikbaar** maken onder de app-Blade.

4. Klik op **+ een bereik toevoegen**.

5. De standaard **-URI voor de toepassings-id**behouden. Klik op **opslaan en door gaan**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app3.png)

6. Vul de vereiste velden in en zorg ervoor dat de **status** is **ingeschakeld**. Klik op **bereik toevoegen**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app4.png)

7. Klik op **een API beschikbaar** maken en vervolgens **een client toepassing toevoegen**.  Voer voor **client-id**de volgende waarden in, afhankelijk van de Cloud:

    - Voer **41b23e61-6c1e-4545-b367-cd054e0ed4b4** voor Azure **Public** in
    - Voer **51bb15d4-3a4f-4ebf-9dca-40096fe32426** in voor Azure **Government**
    - **538ee9e6-310A-468d-afef-ea97365856a9** invoeren voor Azure **Duitsland**
    - **49f817b6-84ae-4cc0-928c-73f27289b3aa** invoeren voor Azure **China 21vianet**

8. Klik op **toepassing toevoegen**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app5.png)

9. Kopieer de **toepassings-id (client)** van de pagina **overzicht** . U hebt deze informatie nodig om uw VPN-gateway (s) te configureren.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. Herhaal de stappen in de sectie [aanvullende toepassingen registreren](#register-apps) om zoveel toepassingen te maken die nodig zijn voor uw beveiligings vereiste. Elke toepassing wordt gekoppeld aan een VPN-gateway en kan een andere set gebruikers hebben. Er kan slechts één toepassing aan een gateway worden gekoppeld.

## <a name="5-assign-users-to-applications"></a><a name="assign-users"></a>5. gebruikers toewijzen aan toepassingen

Wijs de gebruikers toe aan uw toepassingen.

1. Selecteer onder **Azure AD-> Enter prise-toepassingen**de zojuist geregistreerde toepassing en klik op **Eigenschappen**. Zorg ervoor dat de **gebruikers toewijzing vereist** is. is ingesteld op **Ja**. Klik op **Opslaan**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. Klik op de pagina app op **gebruikers en groepen**en klik vervolgens op **+ gebruiker toevoegen**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user3.png)

3. Klik onder **toewijzing toevoegen**op **gebruikers en groepen**. Selecteer de gebruikers die u toegang wilt geven tot deze VPN-toepassing. Klik op **selecteren**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user4.png)