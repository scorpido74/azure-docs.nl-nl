---
title: Fouten diagnosticeren met Azure Active Directory Connected-service
description: Er is een incompatibel verificatie type gedetecteerd door de met Active Directory verbonden service
author: ghogen
manager: jillfra
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26f25daa01288959c38520f9713d35eb975d2df2
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73941389"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Fouten diagnosticeren met de Azure Active Directory Connected-service

Tijdens het detecteren van de vorige verificatie code heeft de Azure Active Director Connect-server een incompatibel verificatie type gedetecteerd.

Als u de vorige verificatie code in een project correct wilt detecteren, moet het project zijn gebouwd.  Als u deze fout ziet en u geen eerdere verificatie code in uw project hebt, bouwt u opnieuw op en probeert u het opnieuw.

## <a name="project-types"></a>Project typen

De verbonden service controleert het type project dat u ontwikkelt zodat het de juiste verificatie logica kan injecteren in het project. Als er een controller is die is afgeleid van `ApiController` in het project, wordt het project beschouwd als een WebAPI-project. Als er alleen controllers zijn die zijn afgeleid van `MVC.Controller` in het project, wordt het project beschouwd als een MVC-project. De verbonden service biedt geen ondersteuning voor een ander project type.

## <a name="compatible-authentication-code"></a>Compatibele verificatie code

De verbonden service controleert ook op verificatie-instellingen die eerder zijn geconfigureerd of compatibel zijn met de service. Als alle instellingen aanwezig zijn, wordt het beschouwd als een nieuwe aankomend geval en de verbonden service wordt weer gegeven met de instellingen.  Als slechts een deel van de instellingen aanwezig is, wordt dit beschouwd als een fout melding.

In een MVC-project controleert de verbonden service op een van de volgende instellingen, wat het resultaat is van het vorige gebruik van de service:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

De verbonden service controleert ook op een van de volgende instellingen in een web-API-project, wat het resultaat is van het vorige gebruik van de service:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>Incompatibele verificatie code

Ten slotte probeert de verbonden service versies van verificatie code te detecteren die zijn geconfigureerd met eerdere versies van Visual Studio. Als u deze fout hebt ontvangen, betekent dit dat het project een incompatibel verificatie type bevat. De verbonden service detecteert de volgende verificatie typen uit eerdere versies van Visual Studio:

* Windows-verificatie
* Afzonderlijke gebruikers accounts
* Organisatie-accounts

Als u Windows-verificatie in een MVC-project wilt detecteren, zoekt de verbinding naar het `authentication`-element in uw `web.config` bestand.

```xml
<configuration>
    <system.web>
        <authentication mode="Windows" />
    </system.web>
</configuration>
```

Als u Windows-verificatie in een web-API-project wilt detecteren, zoekt de verbonden service naar het `IISExpressWindowsAuthentication`-element in het `.csproj`-bestand van het project:

```xml
<Project>
    <PropertyGroup>
        <IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication>
    </PropertyGroup>
</Project>
```

Voor het detecteren van verificatie van afzonderlijke gebruikers accounts zoekt de verbonden service naar het pakket element in uw `packages.config`-bestand.

```xml
<packages>
    <package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" />
</packages>
```

De verbonden service zoekt naar het volgende element in`web.config`om een oude indeling voor organisatie-account verificatie te detecteren:

```xml
<configuration>
    <appSettings>
        <add key="ida:Realm" value="***" />
    </appSettings>
</configuration>
```

Als u het verificatie type wilt wijzigen, verwijdert u het incompatibele verificatie type en probeert u de verbonden service opnieuw toe te voegen.

Zie [verificatie scenario's voor Azure AD](authentication-scenarios.md)voor meer informatie.
