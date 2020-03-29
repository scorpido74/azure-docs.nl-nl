---
title: 'Service-to-service-verificatie: Azure Data Lake Storage Gen1 met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het implementeren van service-to-service met Azure Data Lake Storage Gen1 met Azure Active Directory
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
ms.openlocfilehash: 3fbf2f2540e8f1ca84aad2759b9a1fc790e4065d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66241361"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Service-to-service-verificatie met Azure Data Lake Storage Gen1 met Azure Active Directory
> [!div class="op_single_selector"]
> * [Verificatie van de eindgebruiker](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Verificatie van service-tot-service](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

Azure Data Lake Storage Gen1 gebruikt Azure Active Directory voor verificatie. Voordat u een toepassing maakt die werkt met Data Lake Storage Gen1, moet u bepalen hoe u uw toepassing verifieert met Azure Active Directory (Azure AD). De twee belangrijkste beschikbare opties zijn:

* Verificatie van de eindgebruiker 
* Service-to-service-verificatie (dit artikel) 

Beide opties zorgen ervoor dat uw toepassing wordt voorzien van een OAuth 2.0-token, dat wordt gekoppeld aan elk verzoek aan Data Lake Storage Gen1.

In dit artikel wordt uitgelegd hoe u een **Azure AD-webtoepassing maakt voor service-to-service-verificatie.** Zie [Verificatie van eindgebruikers met Gegevensmeeropslag Gen1 met Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md)voor instructies over azure AD-toepassingsconfiguratie voor verificatie door eindgebruikers.

## <a name="prerequisites"></a>Vereisten
* Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>Stap 1: Een Active Directory-webtoepassing maken

Een Azure AD-webtoepassing maken en configureren voor service-to-service-verificatie met Azure Data Lake Storage Gen1 met Azure Active Directory. Zie Een [Azure AD-toepassing maken](../active-directory/develop/howto-create-service-principal-portal.md)voor instructies .

Terwijl u de instructies in de vorige koppeling volgt, moet u **web-app/ API** voor toepassingstype selecteren, zoals in de volgende schermafbeelding wordt weergegeven:

![Web-app maken](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "Een web-app maken")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>Stap 2: Toepassings-id, verificatiesleutel en tenant-id
Wanneer u programmatisch inlogt, hebt u de ID voor uw toepassing nodig. Als de toepassing onder zijn eigen referenties wordt uitgevoerd, hebt u ook een verificatiesleutel nodig.

* Zie [Toepassings-id en verificatiesleutel](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)ophalen voor instructies voor het ophalen van de toepassings-id en verificatiesleutel (ook wel het clientgeheim genoemd) voor uw toepassing.

* Zie [Tenant-id ophalen](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)voor instructies over het ophalen van de tenant-id.

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder"></a>Stap 3: De Azure AD-toepassing toewijzen aan het Azure Data Lake Storage Gen1-accountbestand of -map


1. Meld u aan bij [Azure Portal](https://portal.azure.com). Open het Data Lake Storage Gen1-account dat u wilt koppelen aan de Azure Active Directory-toepassing die u eerder hebt gemaakt.
2. Klik in het accountblad Data Lake Storage Gen1 op **Data Explorer**.
   
    ![Mappen maken in Data Lake Storage Gen1-account](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Mappen maken in Data Lake-account")
3. Klik in het **blade van Data Explorer** op het bestand of de map waarvoor u toegang wilt bieden tot de Azure AD-toepassing en klik vervolgens op **Access**. Als u de toegang tot een bestand wilt configureren, klikt u op **Toegang** vanuit het blade **Bestandsvoorbeeld.**
   
    ![ACL's instellen op het datalake-bestandssysteem](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "ACL's instellen op het datalake-bestandssysteem")
4. Het **Access-blad** bevat de standaardtoegang en aangepaste toegang die al aan de hoofdmap is toegewezen. Klik **op** het pictogram Toevoegen om ACL's op aangepast niveau toe te voegen.
   
    ![Standaard- en aangepaste toegang weergeven](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "Standaard- en aangepaste toegang weergeven")
5. Klik **op** het pictogram Toevoegen om het **blad Aangepaste toegang toevoegen** te openen. Klik in dit blad op **Gebruiker of groep selecteren**en zoek vervolgens in Het hoofd van gebruiker of **groep** selecteren naar de Azure Active Directory-toepassing die u eerder hebt gemaakt. Als u veel groepen hebt om naar te zoeken, gebruikt u het tekstvak bovenaan om op de groepsnaam te filteren. Klik op de groep die u wilt toevoegen en klik vervolgens op **Selecteren**.
   
    ![Een groep toevoegen](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "Een groep toevoegen")
6. Klik **op Machtigingen selecteren,** selecteer de machtigingen en of u de machtigingen als standaardACL wilt toewijzen, toegang wilt krijgen tot ACL of beide. Klik op **OK**.
   
    ![Machtigingen toewijzen aan groep](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "Machtigingen toewijzen aan groep")
   
    Zie [Toegangsbeheer in Data Lake Storage Gen1](data-lake-store-access-control.md)voor meer informatie over machtigingen in Data Lake Storage Gen1 en Standaard/Access ACL's.
7. Klik **in** het blad Aangepaste toegang toevoegen op **OK**. De nieuw toegevoegde groep, met de bijbehorende machtigingen, wordt weergegeven in het **Access-blad.**
   
    ![Machtigingen toewijzen aan groep](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "Machtigingen toewijzen aan groep")

> [!NOTE]
> Als u van plan bent uw Azure Active Directory-toepassing te beperken tot een specifieke map, moet u ook diezelfde Azure Active Directory-toepassing **Uitvoeren** toestemming geven aan de root om toegang tot het maken van bestanden via de .NET SDK in te schakelen.

> [!NOTE]
> Als u de SDK's wilt gebruiken om een Data Lake Storage Gen1-account te maken, moet u de Azure AD-webtoepassing als rol toewijzen aan de Resourcegroep waarin u het Gen1-account gegevensopslag-opslag maakt.
> 
>

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>Stap 4: Download het OAuth 2.0-tokeneindpunt (alleen voor Java-toepassingen)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en klik in het linkerdeelvenster op Active Directory.

2. Klik in het linkerdeelvenster op **App-registraties**.

3. Klik boven aan het mes van app-registraties op **Eindpunten**.

    ![Eindpunt van OAuth-token](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "Eindpunt van OAuth-token")

4. Kopieer vanuit de lijst met eindpunten het eindpunt oAuth 2.0 token.

    ![Eindpunt van OAuth-token](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "Eindpunt van OAuth-token")   

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u een Azure AD-webtoepassing gemaakt en de informatie verzameld die u nodig hebt in uw clienttoepassingen die u maakt met behulp van .NET SDK, Java, Python, REST API, enz. U nu verder gaan naar de volgende artikelen waarin wordt gesproken over het gebruik van de Azure AD-native toepassing om eerst te verifiëren met Data Lake Storage Gen1 en vervolgens andere bewerkingen in de winkel uit te voeren.

* [Service-to-service-verificatie met Data Lake Storage Gen1 met Java](data-lake-store-service-to-service-authenticate-java.md)
* [Service-to-service-verificatie met Data Lake Storage Gen1 met .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
* [Service-to-service-verificatie met Data Lake Storage Gen1 met Python](data-lake-store-service-to-service-authenticate-python.md)
* [Service-to-service-verificatie met Data Lake Storage Gen1 met REST API](data-lake-store-service-to-service-authenticate-rest-api.md)


