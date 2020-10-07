---
ms.openlocfilehash: c3c5b8ef94b507cad433e587c9ebfc2ec16c0ff9
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91440411"
---
## <a name="add-secret-manager"></a>Secret Manager toevoegen

Het hulpprogramma Secret Manager slaat gevoelige gegevens voor ontwikkeltaken op buiten de projectstructuur. Deze aanpak voorkomt dat er per ongeluk appgeheimen worden gedeeld in de broncode. Voer de volgende stappen uit om het gebruik van Secret Manager in het ASP.NET Core-project mogelijk te maken:

#### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

Ga naar de hoofdmap van het project en voer de volgende opdracht uit om de opslag van geheimen in het project in te schakelen:

```dotnetcli
dotnet user-secrets init
```

Een `UserSecretsId`-element dat een GUID bevat wordt toegevoegd aan het bestand *.csproj*:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    
    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

</Project>
```

#### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

1. Open het *.csproj*-bestand.

1. Voeg een `UserSecretsId` -element toe aan het bestand *.csproj*, zoals hier wordt weergegeven. U kunt dezelfde GUID gebruiken, maar u kunt deze waarde ook vervangen door uw eigen waarden.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    
        <PropertyGroup>
            <TargetFramework>netcoreapp2.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>
    
        <ItemGroup>
            <PackageReference Include="Microsoft.AspNetCore.App" />
            <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
        </ItemGroup>
    
    </Project>
    ```
    
1. Sla het *.csproj*-bestand op.

---

> [!TIP]
> Raadpleeg [Veilige opslag van app-geheimen in ontwikkeling in ASP.NET Core](/aspnet/core/security/app-secrets) voor meer informatie over Secret Manager.
