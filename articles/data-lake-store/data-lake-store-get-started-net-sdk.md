---
title: '.NET SDK: Accountbeheerbewerkingen op Azure Data Lake Storage Gen1 | Microsoft Documenten'
description: Azure Data Lake Storage Gen1 .NET SDK gebruiken om accountbeheerbewerkingen uit te voeren in Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ea57d5a9-2929-4473-9d30-08227912aba7
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 8da40aa04381542c8c750c8d7e33c9a29879371d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65900875"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-net-sdk"></a>Accountbeheerbewerkingen op Azure Data Lake Storage Gen1 met .NET SDK
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST-API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

In dit artikel leert u hoe u accountbeheerbewerkingen uitvoert op Azure Data Lake Storage Gen1 met behulp van .NET SDK. Accountbeheerbewerkingen omvatten het maken van een Data Lake Storage Gen1-account, het vermelden van de accounts in een Azure-abonnement, het verwijderen van de accounts, enz.

Zie [Bestandssysteembewerkingen op Data Lake Storage Gen1 met .NET SDK](data-lake-store-data-operations-net-sdk.md)voor instructies over het uitvoeren van gegevensbeheerbewerkingen op Data Lake Storage Gen1 met behulp van .NET SDK .

## <a name="prerequisites"></a>Vereisten
* **Visual Studio 2013 of hoger**. De onderstaande instructies maken gebruik van Visual Studio 2019.

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

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
5. Open **Program.cs**, verwijder de bestaande code en neem de volgende instructies op om verwijzingen naar naamruimten toe te voegen.

        using System;
        using System.IO;
        using System.Linq;
        using System.Text;
        using System.Threading;
        using System.Collections.Generic;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

6. Declareer de variabelen en vervang de tijdelijke aanduidingen door waarden. Zorg er ook voor dat het lokale pad en de bestandsnaam die u opgeeft al bestaan op de computer.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                
                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;
                private static string _subId;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net"; 
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; 
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";                    
                }
            }
        }

In de rest van het artikel ziet u het gebruik van de beschikbare .NET-methoden voor het uitvoeren van bewerkingen, zoals verificatie, het uploaden van bestanden enzovoort.

## <a name="authentication"></a>Authentication

* Zie Verificatie van eindgebruikers met [Data Lake Storage Gen1 met .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)voor verificatie door eindgebruikers voor uw toepassing.
* Zie [Service-to-service-verificatie met Data Lake Storage Gen1 met .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)voor service-to-service-verificatie voor uw toepassing.

## <a name="create-client-object"></a>Clientobject maken
In het volgende fragment wordt het clientobject Data Lake Storage Gen1-account gemaakt, dat wordt gebruikt om accountbeheeraanvragen aan de service uit te geven, zoals account maken, account verwijderen, enz.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(armCreds) { SubscriptionId = _subId };
    
## <a name="create-a-data-lake-storage-gen1-account"></a>Een Data Lake Storage Gen1-account maken
In het volgende fragment wordt een Data Lake Storage Gen1-account gemaakt in het Azure-abonnement dat u hebt opgegeven tijdens het maken van het clientclientobject Data Lake Storage Gen1.

    // Create Data Lake Storage Gen1 account
    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

## <a name="list-all-data-lake-storage-gen1-accounts-within-a-subscription"></a>Alle Data Lake Storage Gen1-accounts in een abonnement weergeven
Voeg de volgende methode toe aan uw klassedefinitie. In het volgende fragment worden alle Data Lake Storage Gen1-accounts binnen een bepaald Azure-abonnement weergegeven.

    // List all Data Lake Storage Gen1 accounts within the subscription
    public static List<DataLakeStoreAccountBasic> ListAdlStoreAccounts()
    {
        var response = _adlsClient.Account.List(_adlsAccountName);
        var accounts = new List<DataLakeStoreAccountBasic>(response);

        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }

        return accounts;
    }

## <a name="delete-a-data-lake-storage-gen1-account"></a>Een Data Lake Storage Gen1-account verwijderen
In het volgende fragment wordt het Data Lake Storage Gen1-account verwijderd dat u eerder hebt gemaakt.

    // Delete Data Lake Storage Gen1 account
    _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);

## <a name="see-also"></a>Zie ook
* [Bestandssysteembewerkingen op Data Lake Storage Gen1 met .NET SDK](data-lake-store-data-operations-net-sdk.md)
* [Gegevenslakestorage Gen1 .NET SDK-verwijzing](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Volgende stappen
* [Gegevens beveiligen in Data Lake Storage Gen1](data-lake-store-secure-data.md)
