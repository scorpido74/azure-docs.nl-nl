---
title: '.NET SDK: Bestandssysteembewerkingen op Azure Data Lake Storage Gen1'
description: Gebruik de Azure Data Lake Storage Gen1 .NET SDK voor bestandssysteembewerkingen op Data Lake Storage Gen1, zoals mappen maken, enz.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: twooley
ms.openlocfilehash: 7e33ecbbb49fc2b0683d0757da36deec72796806
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638898"
---
# <a name="filesystem-operations-on-data-lake-storage-gen1-using-the-net-sdk"></a>Bestandssysteembewerkingen op Data Lake Storage Gen1 met behulp van de .NET SDK

> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST-API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

In dit artikel leert u hoe u bestandssysteembewerkingen uitvoert op Data Lake Storage Gen1 met behulp van de .NET SDK. Bestandssysteembewerkingen omvatten het maken van mappen in een Data Lake Storage Gen1-account, het uploaden van bestanden, het downloaden van bestanden, enz.

Zie [Accountbeheerbewerkingen voor Data Lake Storage Gen1 met behulp van .NET SDK met .NET SDK](data-lake-store-get-started-net-sdk.md).

## <a name="prerequisites"></a>Vereisten

* **Visual Studio 2013 of hoger**. De instructies in dit artikel maken gebruik van Visual Studio 2019.

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Data Lake Storage Gen1-account**. Zie Aan de slag met [Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)voor instructies over het maken van een account.

## <a name="create-a-net-application"></a>Een .NET-toepassing maken

Dit codevoorbeeld beschikbaar [in GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) doorloopt het proces waarin bestanden in het archief worden gemaakt, bestanden worden samengevoegd, een bestand wordt gedownload en een aantal bestanden uit het archief wordt verwijderd. In dit gedeelte van het artikel komen de belangrijkste onderdelen van de code aan bod.

1. Selecteer in Visual Studio het menu **Bestand,** **Nieuw**en vervolgens **Project**.
1. Kies **Console-app (.NET Framework)** en selecteer **Volgende**.
1. Voer in **Projectnaam**enter `CreateADLApplication`en selecteer **Vervolgens Maken**.
1. Voeg de NuGet-pakketten toe aan het project.

   1. Klik in Solution Explorer met de rechtermuisknop op de projectnaam en klik op **Manage NuGet Packages**.
   1. Controleer op het tabblad **NuGet Package Manager** of **pakketbron** is ingesteld op **nuget.org**. Controleer ook of het selectievakje **Prerelease opnemen** is ingeschakeld.
   1. Zoek en installeer de volgende NuGet-pakketten:

      * `Microsoft.Azure.DataLake.Store`- Dit artikel gebruikt v1.0.0.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication`- In dit artikel wordt v2.3.1 gebruikt.

      Sluit de **NuGet Package Manager**.

1. Open **Program.cs**, verwijder de bestaande code en neem de volgende instructies op om verwijzingen naar naamruimten toe te voegen.

    ```
    using System;
    using System.IO;using System.Threading;
    using System.Linq;
    using System.Text;
    using System.Collections.Generic;
    using System.Security.Cryptography.X509Certificates; // Required only if you're using an Azure AD application created with certificates
                
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.DataLake.Store;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. Declareer de variabelen zoals hieronder wordt weergegeven en vervang de tijdelijke aanduidingen door waarden. Zorg er ook voor dat het lokale pad en de bestandsnaam die u hier opgeeft al bestaan op de computer.

    ```
    namespace SdkSample
    {
        class Program
        {
            private static string _adlsg1AccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net";
        }
    }
    ```

In de overige gedeelten van het artikel u zien hoe u de beschikbare .NET-methoden gebruiken om bewerkingen uit te voeren, zoals verificatie, bestandsupload, enz.

## <a name="authentication"></a>Authentication

* Zie Verificatie van eindgebruikers met [Data Lake Storage Gen1 met .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)voor verificatie door eindgebruikers voor uw toepassing.
* Zie [Service-to-service-verificatie met Data Lake Storage Gen1 met .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)voor service-to-service-verificatie voor uw toepassing.

## <a name="create-client-object"></a>Clientobject maken

In het volgende fragment wordt het clientobject Data Lake Storage Gen1-bestandssysteem gemaakt, dat wordt gebruikt om aanvragen aan de service uit te geven.

```
// Create client objects
AdlsClient client = AdlsClient.CreateClient(_adlsg1AccountName, adlCreds);
```

## <a name="create-a-file-and-directory"></a>Een bestand en map maken

Voeg het volgende codefragment toe aan uw toepassing. In dit fragment wordt een bestand en een bovenliggende map toegevoegd die niet bestaat.

```
// Create a file - automatically creates any parent directories that don't exist
// The AdlsOutputStream preserves record boundaries - it does not break records while writing to the store

using (var stream = client.CreateFile(fileName, IfExists.Overwrite))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is test data to write.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);

    textByteArray = Encoding.UTF8.GetBytes("This is the second line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="append-to-a-file"></a>Toevoegen aan een bestand

In het volgende fragment worden gegevens toegevoegd aan een bestaand bestand in het Data Lake Storage Gen1-account.

```
// Append to existing file

using (var stream = client.GetAppendStream(fileName))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is the added line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="read-a-file"></a>Een bestand lezen

In het volgende fragment wordt de inhoud van een bestand in Data Lake Storage Gen1 gelezen.

```
//Read file contents

using (var readStream = new StreamReader(client.GetReadStream(fileName)))
{
    string line;
    while ((line = readStream.ReadLine()) != null)
    {
        Console.WriteLine(line);
    }
}
```

## <a name="get-file-properties"></a>Bestandseigenschappen opvragen

Het volgende codefragment retourneert de eigenschappen die zijn gekoppeld aan een bestand of map.

```
// Get file properties
var directoryEntry = client.GetDirectoryEntry(fileName);
PrintDirectoryEntry(directoryEntry);
```

De definitie `PrintDirectoryEntry` van de methode is beschikbaar als onderdeel van het voorbeeld [op GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="rename-a-file"></a>De naam van een bestand wijzigen

In het volgende fragment wordt de naam van een bestaand bestand in een Data Lake Storage Gen1-account gewijzigd.

```
// Rename a file
string destFilePath = "/Test/testRenameDest3.txt";
client.Rename(fileName, destFilePath, true);
```

## <a name="enumerate-a-directory"></a>Een map opsommen

In het volgende fragment worden mappen opgesomd in een Data Lake Storage Gen1-account.

```
// Enumerate directory
foreach (var entry in client.EnumerateDirectory("/Test"))
{
    PrintDirectoryEntry(entry);
}
```

De definitie `PrintDirectoryEntry` van de methode is beschikbaar als onderdeel van het voorbeeld [op GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="delete-directories-recursively"></a>Mappen recursief verwijderen

In het volgende fragment wordt een map en alle submappen opnieuw verwijderd.

```
// Delete a directory and all its subdirectories and files
client.DeleteRecursive("/Test");
```

## <a name="samples"></a>Voorbeelden

Hier volgen een paar voorbeelden die laten zien hoe u de Data Lake Storage Gen1 Filesystem SDK gebruiken.

* [Basisvoorbeeld op GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [Geavanceerd voorbeeld op GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>Zie ook

* [Accountbeheerbewerkingen op Data Lake Storage Gen1 met .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Gegevenslakestorage Gen1 .NET SDK-verwijzing](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Volgende stappen

* [Gegevens beveiligen in Data Lake Storage Gen1](data-lake-store-secure-data.md)
