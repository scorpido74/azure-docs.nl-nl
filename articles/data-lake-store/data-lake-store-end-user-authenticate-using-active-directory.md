---
title: 'Verificatie door eindgebruikers: Azure Data Lake Storage Gen1 met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het bereiken van verificatie door eindgebruikers met Azure Data Lake Storage Gen1 met Azure Active Directory
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 4c2b774c304e46f9fc68f3beaf64218e614ecad1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66234056"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Verificatie van eindgebruikers met Azure Data Lake Storage Gen1 met Azure Active Directory
> [!div class="op_single_selector"]
> * [Verificatie van de eindgebruiker](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Verificatie van service-tot-service](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Storage Gen1 gebruikt Azure Active Directory voor verificatie. Voordat u een toepassing maakt die werkt met Data Lake Storage Gen1 of Azure Data Lake Analytics, moet u bepalen hoe u uw toepassing verifieert met Azure Active Directory (Azure AD). De twee belangrijkste beschikbare opties zijn:

* Verificatie door eindgebruikers (dit artikel)
* Service-to-service-verificatie (kies deze optie in de vervolgkeuzelijst hierboven)

Beide opties zorgen ervoor dat uw toepassing wordt voorzien van een OAuth 2.0-token, dat wordt gekoppeld aan elk verzoek aan Data Lake Storage Gen1 of Azure Data Lake Analytics.

In dit artikel wordt uitgelegd hoe u een **Azure AD-native toepassing maakt voor verificatie door eindgebruikers.** Zie [Service-to-service-verificatie met Data Lake Storage Gen1 met Azure Active Directory](data-lake-store-authenticate-using-active-directory.md)voor instructies over azure AD-toepassingsconfiguratie voor service-to-service-verificatie.

## <a name="prerequisites"></a>Vereisten
* Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

* Uw abonnements-ID. U het ophalen uit de Azure-portal. Het is bijvoorbeeld beschikbaar via het Data Lake Storage Gen1-accountblad.
  
    ![Abonnement-id](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* Uw Azure AD-domeinnaam. U deze ophalen door met de muis in de rechterbovenhoek van de Azure-portal te zweven. Van de screenshot hieronder, de domeinnaam is **contoso.onmicrosoft.com**, en de GUID binnen haakjes is de tenant-ID. 
  
    ![Aad-domein oppakken](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

* Uw Azure-tenant-id. Zie Het tenant-id ophalen voor instructies over het ophalen van de [tenant-id.](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)

## <a name="end-user-authentication"></a>Verificatie van de eindgebruiker
Dit verificatiemechanisme is de aanbevolen aanpak als u wilt dat een eindgebruiker zich aanmeldt bij uw toepassing via Azure AD. Uw toepassing heeft dan toegang tot Azure-bronnen met hetzelfde toegangsniveau als de eindgebruiker die is aangemeld. Uw eindgebruiker moet zijn of haar referenties periodiek verstrekken om ervoor te zorgen dat uw toepassing de toegang behoudt.

Het resultaat van het aanmelden van de eindgebruiker is dat uw toepassing een toegangstoken en een vernieuwingstoken krijgt. Het toegangstoken wordt gekoppeld aan elk verzoek aan Data Lake Storage Gen1 of Data Lake Analytics en is standaard één uur geldig. Het vernieuwingstoken kan worden gebruikt om een nieuw toegangstoken te verkrijgen en is standaard maximaal twee weken geldig. U twee verschillende benaderingen gebruiken voor aanmelden door eindgebruikers.

### <a name="using-the-oauth-20-pop-up"></a>De pop-up van OAuth 2.0 gebruiken
Uw toepassing kan leiden tot een OAuth 2.0 autorisatie pop-up, waarin de eindgebruiker kan hun referenties in te voeren. Deze pop-up werkt ook met het Azure AD Two-factor Authentication (2FA) proces, indien nodig. 

> [!NOTE]
> Deze methode wordt nog niet ondersteund in de Azure AD Authentication Library (ADAL) voor Python of Java.
> 
> 

### <a name="directly-passing-in-user-credentials"></a>Rechtstreeks doorgeven van gebruikersreferenties
Uw toepassing kan rechtstreeks gebruikersreferenties aan Azure AD verstrekken. Deze methode werkt alleen met gebruikersaccounts van een organisatie-id; het is niet compatibel met persoonlijke / "live ID" @outlook.com @live.comgebruikersaccounts, met inbegrip van de accounts eindigend in of . Bovendien is deze methode niet compatibel met gebruikersaccounts waarvoor Azure AD Two-factor Authentication (2FA) vereist is.

### <a name="what-do-i-need-for-this-approach"></a>Wat heb ik nodig voor deze aanpak?
* Azure AD-domeinnaam. Deze eis is al opgenomen in de voorwaarde van dit artikel.
* Azure AD-tenant-id. Deze eis is al opgenomen in de voorwaarde van dit artikel.
* Azure **AD-native toepassing**
* Toepassings-id voor de Azure AD-native toepassing
* URI omleiden voor de Azure AD-native toepassing
* Gedelegeerde machtigingen instellen


## <a name="step-1-create-an-active-directory-native-application"></a>Stap 1: Een active directory-native toepassing maken

Een Azure AD-native toepassing maken en configureren voor verificatie van eindgebruikers met Data Lake Storage Gen1 met Azure Active Directory. Zie Een [Azure AD-toepassing maken](../active-directory/develop/howto-create-service-principal-portal.md)voor instructies .

Controleer tijdens het volgen van de instructies in de koppeling of u **Native** selecteert voor toepassingstype, zoals in de volgende schermafbeelding wordt weergegeven:

![Web-app maken](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "Native-app maken")

## <a name="step-2-get-application-id-and-redirect-uri"></a>Stap 2: Get application ID and redirect URI Stap 2: Get application ID and redirect URI

Zie [De toepassings-id ophalen](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in) om de toepassings-id op te halen.

Als u de omleidingsURI wilt ophalen, neemt u de volgende stappen uit.

1. Selecteer in de **Azure-portal Azure Active Directory**, klik op **App-registraties**en zoek en klik vervolgens op de Azure AD-native toepassing die u hebt gemaakt.

2. Klik in het **pagina-blad Instellingen** voor de toepassing op **URI's omleiden**.

    ![Download Redirect URI](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. Kopieer de weergegeven waarde.


## <a name="step-3-set-permissions"></a>Stap 3: Machtigingen instellen

1. Selecteer in de **Azure-portal Azure Active Directory**, klik op **App-registraties**en zoek en klik vervolgens op de Azure AD-native toepassing die u hebt gemaakt.

2. Klik in het **pagina-blad Instellingen** voor de toepassing op **Vereiste machtigingen**en klik vervolgens op **Toevoegen**.

    ![client-id](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. Klik in het **beheer API-toegangs** toevoegen op **Een API selecteren,** klik op **Azure Data Lake**en klik vervolgens op **Selecteren**.

    ![client-id](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
 
4.  Klik in het **beheer API-toegangs** toevoegen op **Machtigingen selecteren,** schakel het selectievakje in om **Volledige toegang tot Data Lake Store te**geven en klik vervolgens op **Selecteren**.

    ![client-id](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    Klik op **Gereed**.

5. Herhaal ook de laatste twee stappen om machtigingen toe te kennen voor **Windows Azure Service Management API.**
   
## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u een Azure AD-native toepassing gemaakt en de informatie verzameld die u nodig hebt in uw clienttoepassingen die u maakt met behulp van .NET SDK, Java SDK, REST API, enz. U nu verder gaan naar de volgende artikelen waarin wordt gesproken over het gebruik van de Azure AD-webtoepassing om eerst te verifiëren met Data Lake Storage Gen1 en vervolgens andere bewerkingen in de winkel uit te voeren.

* [Verificatie van eindgebruikers met Data Lake Storage Gen1 met Java SDK](data-lake-store-end-user-authenticate-java-sdk.md)
* [Verificatie van eindgebruikers met Data Lake Storage Gen1 met .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
* [Verificatie van eindgebruikers met Data Lake Storage Gen1 met Python](data-lake-store-end-user-authenticate-python.md)
* [Verificatie van eindgebruikers met Data Lake Storage Gen1 met REST API](data-lake-store-end-user-authenticate-rest-api.md)

