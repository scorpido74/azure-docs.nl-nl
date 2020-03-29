---
title: Aan de slag met de Azure CDN-bibliotheek voor .NET | Microsoft Documenten
description: Meer informatie over het schrijven van .NET-toepassingen om Azure CDN te beheren met Visual Studio.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 63cf4101-92e7-49dd-a155-a90e54a792ca
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 7e3ad3a5928b36c221bb83b1c4012c3c9e14f35d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67594166"
---
# <a name="get-started-with-azure-cdn-development"></a>Aan de slag met Azure CDN-ontwikkeling
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

U de [Azure CDN-bibliotheek voor .NET](/dotnet/api/overview/azure/cdn) gebruiken om het maken en beheren van CDN-profielen en eindpunten te automatiseren.  Deze zelfstudie leidt door de creatie van een eenvoudige .NET-consoletoepassing die een aantal van de beschikbare bewerkingen demonstreert.  Deze zelfstudie is niet bedoeld om alle aspecten van de Azure CDN-bibliotheek voor .NET in detail te beschrijven.

Je hebt Visual Studio 2015 nodig om deze zelfstudie te voltooien.  [Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) is gratis te downloaden.

> [!TIP]
> Het [voltooide project van deze zelfstudie](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c) is beschikbaar om te downloaden op MSDN.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-nuget-packages"></a>Maak uw project en voeg Nuget-pakketten toe
Nu we een brongroep hebben gemaakt voor onze CDN-profielen en onze Azure AD-toepassingstoestemming hebben gegeven om CDN-profielen en eindpunten binnen die groep te beheren, kunnen we beginnen met het maken van onze toepassing.

Klik vanuit Visual Studio 2015 op **Bestand**, **Nieuw**, **Project...** om het nieuwe projectdialoogvenster te openen.  Vouw **Visual C#** uit en selecteer **Vervolgens Windows** in het deelvenster aan de linkerkant.  Klik op **Consoletoepassing** in het middelste deelvenster.  Geef uw project een naam en klik op **OK**.  

![Nieuw project](./media/cdn-app-dev-net/cdn-new-project.png)

Ons project gaat een aantal Azure-bibliotheken gebruiken die zijn opgenomen in Nuget-pakketten.  Laten we die toevoegen aan het project.

1. Klik op het menu **Extra,** **Nuget Package Manager**en vervolgens op Package Manager **Console**.
   
    ![Nuget-pakketten beheren](./media/cdn-app-dev-net/cdn-manage-nuget.png)
2. Voer in de Package Manager-console de volgende opdracht uit om de **Active Directory Authentication Library (ADAL)** te installeren:
   
    `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`
3. Voer het volgende uit om de **Azure CDN-beheerbibliotheek**te installeren:
   
    `Install-Package Microsoft.Azure.Management.Cdn`

## <a name="directives-constants-main-method-and-helper-methods"></a>Richtlijnen, constanten, hoofdmethoden en helpermethoden
Laten we de basisstructuur van ons programma laten schrijven.

1. Terug in het tabblad `using` Program.cs, vervang de richtlijnen aan de bovenkant met de volgende:
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using Microsoft.Azure.Management.Cdn;
    using Microsoft.Azure.Management.Cdn.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
2. We moeten een aantal constanten definiëren die onze methoden zullen gebruiken.  Voeg `Program` in de klasse, maar vóór de `Main` methode, het volgende toe.  Zorg ervoor dat u de tijdelijke ** &lt;aanduidingen,&gt;** inclusief de hoekbeugels, vervangt door uw eigen waarden indien nodig.
   
    ```csharp
    //Tenant app constants
    private const string clientID = "<YOUR CLIENT ID>";
    private const string clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    private const string authority = "https://login.microsoftonline.com/<YOUR TENANT ID>/<YOUR TENANT DOMAIN NAME>";
   
    //Application constants
    private const string subscriptionId = "<YOUR SUBSCRIPTION ID>";
    private const string profileName = "CdnConsoleApp";
    private const string endpointName = "<A UNIQUE NAME FOR YOUR CDN ENDPOINT>";
    private const string resourceGroupName = "CdnConsoleTutorial";
    private const string resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. Ook op klassenniveau definieert u deze twee variabelen.  We gebruiken deze later om te bepalen of ons profiel en eindpunt al bestaan.
   
    ```csharp
    static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
    ```
4. Vervang `Main` de methode als volgt:
   
   ```csharp
   static void Main(string[] args)
   {
       //Get a token
       AuthenticationResult authResult = GetAccessToken();
   
       // Create CDN client
       CdnManagementClient cdn = new CdnManagementClient(new TokenCredentials(authResult.AccessToken))
           { SubscriptionId = subscriptionId };
   
       ListProfilesAndEndpoints(cdn);
   
       // Create CDN Profile
       CreateCdnProfile(cdn);
   
       // Create CDN Endpoint
       CreateCdnEndpoint(cdn);
   
       Console.WriteLine();
   
       // Purge CDN Endpoint
       PromptPurgeCdnEndpoint(cdn);
   
       // Delete CDN Endpoint
       PromptDeleteCdnEndpoint(cdn);
   
       // Delete CDN Profile
       PromptDeleteCdnProfile(cdn);
   
       Console.WriteLine("Press Enter to end program.");
       Console.ReadLine();
   }
   ```
5. Sommige van onze andere methoden gaan de gebruiker vragen met "Ja / Nee" vragen.  Voeg de volgende methode toe om dat een beetje gemakkelijker te maken:
   
    ```csharp
    private static bool PromptUser(string Question)
    {
        Console.Write(Question + " (Y/N): ");
        var response = Console.ReadKey();
        Console.WriteLine();
        if (response.Key == ConsoleKey.Y)
        {
            return true;
        }
        else if (response.Key == ConsoleKey.N)
        {
            return false;
        }
        else
        {
            // They pressed something other than Y or N.  Let's ask them again.
            return PromptUser(Question);
        }
    }
    ```

Nu de basisstructuur van ons programma is geschreven, moeten `Main` we de methoden die door de methode worden genoemd, maken.

## <a name="authentication"></a>Authentication
Voordat we de Azure CDN-beheerbibliotheek kunnen gebruiken, moeten we onze serviceprincipal verifiëren en een verificatietoken verkrijgen.  Deze methode gebruikt ADAL om het token op te halen.

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority); 
    ClientCredential credential = new ClientCredential(clientID, clientSecret);
    AuthenticationResult authResult = 
        authContext.AcquireTokenAsync("https://management.core.windows.net/", credential).Result;

    return authResult;
}
```

Als u individuele gebruikersverificatie `GetAccessToken` gebruikt, ziet de methode er iets anders uit.

> [!IMPORTANT]
> Gebruik dit codevoorbeeld alleen als u kiest voor individuele gebruikersverificatie in plaats van een serviceprincipal.
> 
> 

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority);
    AuthenticationResult authResult = authContext.AcquireTokenAsync("https://management.core.windows.net/",
        clientID, new Uri("http://<redirect URI>"), new PlatformParameters(PromptBehavior.RefreshSession)).Result;

    return authResult;
}
```

Zorg ervoor `<redirect URI>` dat u de omleidings-URI die u hebt ingevoerd toen u de toepassing in Azure AD hebt geregistreerd, vervangt.

## <a name="list-cdn-profiles-and-endpoints"></a>CDN-profielen en eindpunten weergeven
Nu zijn we klaar om CDN-operaties uit te voeren.  Het eerste wat onze methode doet is een lijst van alle profielen en eindpunten in onze resource groep, en als het vindt een match voor het profiel en eindpunt namen opgegeven in onze constanten, maakt een notitie van dat voor later, zodat we niet proberen om duplicaten te maken.

```csharp
private static void ListProfilesAndEndpoints(CdnManagementClient cdn)
{
    // List all the CDN profiles in this resource group
    var profileList = cdn.Profiles.ListByResourceGroup(resourceGroupName);
    foreach (Profile p in profileList)
    {
        Console.WriteLine("CDN profile {0}", p.Name);
        if (p.Name.Equals(profileName, StringComparison.OrdinalIgnoreCase))
        {
            // Hey, that's the name of the CDN profile we want to create!
            profileAlreadyExists = true;
        }

        //List all the CDN endpoints on this CDN profile
        Console.WriteLine("Endpoints:");
        var endpointList = cdn.Endpoints.ListByProfile(p.Name, resourceGroupName);
        foreach (Endpoint e in endpointList)
        {
            Console.WriteLine("-{0} ({1})", e.Name, e.HostName);
            if (e.Name.Equals(endpointName, StringComparison.OrdinalIgnoreCase))
            {
                // The unique endpoint name already exists.
                endpointAlreadyExists = true;
            }
        }
        Console.WriteLine();
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>CDN-profielen en eindpunten maken
Vervolgens maken we een profiel aan.

```csharp
private static void CreateCdnProfile(CdnManagementClient cdn)
{
    if (profileAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating profile {0}.", profileName);
        ProfileCreateParameters profileParms =
            new ProfileCreateParameters() { Location = resourceLocation, Sku = new Sku(SkuName.StandardVerizon) };
        cdn.Profiles.Create(profileName, profileParms, resourceGroupName);
    }
}
```

Zodra het profiel is gemaakt, maken we een eindpunt.

```csharp
private static void CreateCdnEndpoint(CdnManagementClient cdn)
{
    if (endpointAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating endpoint {0} on profile {1}.", endpointName, profileName);
        EndpointCreateParameters endpointParms =
            new EndpointCreateParameters()
            {
                Origins = new List<DeepCreatedOrigin>() { new DeepCreatedOrigin("Contoso", "www.contoso.com") },
                IsHttpAllowed = true,
                IsHttpsAllowed = true,
                Location = resourceLocation
            };
        cdn.Endpoints.Create(endpointName, endpointParms, profileName, resourceGroupName);
    }
}
```

> [!NOTE]
> In het bovenstaande voorbeeld wordt het eindpunt een oorsprong `www.contoso.com`met de naam *Contoso* met een hostnaam toegeschreven.  Je moet dit wijzigen om de hostnaam van je eigen oorsprong aan te wijzen.
> 
> 

## <a name="purge-an-endpoint"></a>Een eindpunt zuiveren
Ervan uitgaande dat het eindpunt is gemaakt, is een veelvoorkomende taak die we in ons programma zouden willen uitvoeren, het zuiveren van de inhoud in ons eindpunt.

```csharp
private static void PromptPurgeCdnEndpoint(CdnManagementClient cdn)
{
    if (PromptUser(String.Format("Purge CDN endpoint {0}?", endpointName)))
    {
        Console.WriteLine("Purging endpoint. Please wait...");
        cdn.Endpoints.PurgeContent(endpointName, profileName, resourceGroupName, new List<string>() { "/*" });
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

> [!NOTE]
> In het bovenstaande voorbeeld `/*` geeft de tekenreeks aan dat ik alles in de wortel van het eindpuntpad wil verwijderen.  Dit is gelijk aan het controleren **van Purge All** in het dialoogvenster 'zuivering' van de Azure-portal. In `CreateCdnProfile` de methode heb ik ons profiel gemaakt als een **Azure CDN van Verizon** profiel met behulp van de code, `Sku = new Sku(SkuName.StandardVerizon)`dus dit zal succesvol zijn.  Azure **CDN van Akamai-profielen** ondersteunt echter geen **Purge All,** dus als ik een Akamai-profiel voor deze zelfstudie gebruikte, zou ik specifieke paden moeten opnemen om te verwijderen.
> 
> 

## <a name="delete-cdn-profiles-and-endpoints"></a>CDN-profielen en eindpunten verwijderen
De laatste methoden verwijderen ons eindpunt en profiel.

```csharp
private static void PromptDeleteCdnEndpoint(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN endpoint {0} on profile {1}?", endpointName, profileName)))
    {
        Console.WriteLine("Deleting endpoint. Please wait...");
        cdn.Endpoints.DeleteIfExists(endpointName, profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}

private static void PromptDeleteCdnProfile(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN profile {0}?", profileName)))
    {
        Console.WriteLine("Deleting profile. Please wait...");
        cdn.Profiles.DeleteIfExists(profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

## <a name="running-the-program"></a>Het programma uitvoeren
We kunnen het programma nu compileren en uitvoeren door op de knop **Start** in Visual Studio te klikken.

![Programma uitgevoerd](./media/cdn-app-dev-net/cdn-program-running-1.png)

Wanneer het programma de bovenstaande prompt bereikt, moet u kunnen terugkeren naar uw resourcegroep in de Azure-portal en zien of het profiel is gemaakt.

![Gelukt!](./media/cdn-app-dev-net/cdn-success.png)

We kunnen dan de aanwijzingen bevestigen om de rest van het programma uit te voeren.

![Programma voltooien](./media/cdn-app-dev-net/cdn-program-running-2.png)

## <a name="next-steps"></a>Volgende stappen
Download het voorbeeld om het voltooide project van deze walkthrough te [bekijken.](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c)

Als u aanvullende documentatie wilt vinden in de Azure CDN-beheerbibliotheek voor .NET, bekijkt u de [verwijzing op MSDN](/dotnet/api/overview/azure/cdn).

Beheer uw CDN-resources met [PowerShell.](cdn-manage-powershell.md)

