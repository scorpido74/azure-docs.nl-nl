---
title: 'Verificatie van eindgebruikers: .NET SDK met Azure Data Lake Storage Gen1 met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het bereiken van verificatie door eindgebruikers met Azure Data Lake Storage Gen1 met Azure Active Directory met .NET SDK
services: data-lake-store
documentationcenter: ''
author: twooley
manager: cgronlun
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 215b839c21c2590c08ac2f4250086eaf97914ce1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66243714"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Verificatie van eindgebruikers met Azure Data Lake Storage Gen1 met .NET SDK
> [!div class="op_single_selector"]
> * [Java gebruiken](data-lake-store-end-user-authenticate-java-sdk.md)
> * [.NET SDK gebruiken](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Python gebruiken](data-lake-store-end-user-authenticate-python.md)
> * [REST-API gebruiken](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

In dit artikel vindt u meer informatie over het gebruik van de .NET SDK om verificatie van eindgebruikers uit te brengen met Azure Data Lake Storage Gen1. Zie [Service-to-service-verificatie met Data Lake Storage Gen1](data-lake-store-service-to-service-authenticate-net-sdk.md)met .NET SDK met .NET SDK met .NET SDK .

## <a name="prerequisites"></a>Vereisten
* **Visual Studio 2013 of hoger**. De onderstaande instructies maken gebruik van Visual Studio 2019.

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

* **Maak een Azure Active Directory 'Native'-toepassing.** U moet de stappen in [verificatie van eindgebruikers met Data Lake Storage Gen1](data-lake-store-end-user-authenticate-using-active-directory.md)hebben voltooid met Azure Active Directory .

## <a name="create-a-net-application"></a>Een .NET-toepassing maken
1. Selecteer in Visual Studio het menu **Bestand,** **Nieuw**en vervolgens **Project**.
2. Kies **Console-app (.NET Framework)** en selecteer **Volgende**.
3. Voer in **Projectnaam**enter `CreateADLApplication`en selecteer **Vervolgens Maken**.

4. Voeg de NuGet-pakketten toe aan het project.

   1. Klik in Solution Explorer met de rechtermuisknop op de projectnaam en klik op **Manage NuGet Packages**.
   2. Controleer op het tabblad **NuGet Package Manager** of **pakketbron** is ingesteld op **nuget.org** en dat het selectievakje **Prerelease opnemen** is ingeschakeld.
   3. Zoek en installeer de volgende NuGet-pakketten:

      * `Microsoft.Azure.Management.DataLake.Store`: in deze zelfstudie wordt gebruikgemaakt van v2.1.3-preview.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication`: in deze zelfstudie wordt gebruikgemaakt van v2.2.12.

        ![Een NuGet-bron toevoegen](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Een nieuw Azure Data Lake-account maken")
   4. Sluit de **NuGet Package Manager**.

5. Open **Program.cs**
6. Vervang de instructies voor gebruik door de volgende regels:

    ```csharp
    using System;
    using System.IO;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Collections.Generic;
            
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Store.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```     

## <a name="end-user-authentication"></a>Verificatie van de eindgebruiker
Voeg dit fragment toe aan uw .NET-clienttoepassing. Vervang de tijdelijke aanduidingswaarden door de waarden die zijn opgehaald uit een native Azure AD-toepassing (weergegeven als voorwaarde). Met dit fragment u uw toepassing **interactief** verifiëren met Data Lake Storage Gen1, wat betekent dat u wordt gevraagd uw Azure-referenties in te voeren.

Voor gebruiksgemak gebruikt het volgende fragment standaardwaarden voor client-id en wordt URI omgeleid die geldig zijn voor elk Azure-abonnement. In het volgende fragment hoeft u alleen de waarde voor uw tenant-id op te geven. U de tenant-id ophalen met behulp van de instructies die worden gegeven bij [Ophalen van de tenant-id.](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)
    
- Vervang de functie Main() door de volgende code:

    ```csharp
    private static void Main(string[] args)
    {
        //User login via interactive popup
        string TENANT = "<AAD-directory-domain>";
        string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
        System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
        System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
        string MY_DOCUMENTS = System.Environment.GetFolderPath(System.Environment.SpecialFolder.MyDocuments);
        string TOKEN_CACHE_PATH = System.IO.Path.Combine(MY_DOCUMENTS, "my.tokencache");
        var tokenCache = GetTokenCache(TOKEN_CACHE_PATH);
        var armCreds = GetCreds_User_Popup(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, tokenCache);
        var adlCreds = GetCreds_User_Popup(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, tokenCache);
    }
    ```

Een paar dingen die u moet weten over het vorige fragment:

* Het vorige fragment maakt gebruik `GetTokenCache` `GetCreds_User_Popup`van een helperfuncties en . De code voor deze helperfuncties is hier beschikbaar [op GitHub.](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#gettokencache)
* Om u te helpen de zelfstudie sneller te voltooien, wordt in het fragment een native toepassingsclient-id gebruikt die standaard beschikbaar is voor alle Azure-abonnementen. U kunt **dit fragment dus in zijn huidige vorm in uw toepassing gebruiken**.
* Als u echter uw eigen Azure AD-domein- en toepassingsclient-id wilt gebruiken, moet u een systeemeigen Azure AD-toepassing maken en vervolgens het Azure AD-tenant-ID, de client-ID en omleidings-URI gebruiken voor de toepassing die u hebt gemaakt. Zie [Een Active Directory-toepassing maken voor verificatie van eindgebruikers met Data Lake Storage Gen1](data-lake-store-end-user-authenticate-using-active-directory.md) voor instructies.

  
## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u verificatie van eindgebruikers gebruiken om te verifiëren met Azure Data Lake Storage Gen1 met behulp van .NET SDK. U nu de volgende artikelen bekijken waarin wordt gesproken over het gebruik van de .NET SDK om te werken met Azure Data Lake Storage Gen1.

* [Accountbeheerbewerkingen op Data Lake Storage Gen1 met .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Gegevensbewerkingen op Data Lake Storage Gen1 met .NET SDK](data-lake-store-data-operations-net-sdk.md)

