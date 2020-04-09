---
title: Fouten met Azure AD Connected Service (Visual Studio) diagnosticeren
description: De active directory connected service heeft een onverenigbaar verificatietype gedetecteerd
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 4b39aa77ea3895a606ad34a3bc9b70dba924a23f
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886089"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Fouten diagnosticeren met de Azure Active Directory Connected Service

Tijdens het detecteren van eerdere verificatiecode heeft de Azure Active Directory connected service een onverenigbaar verificatietype gedetecteerd.

Als u eerdere verificatiecode in een project correct wilt detecteren, moet het project opnieuw worden opgebouwd. Als u deze fout ziet en u geen eerdere verificatiecode in uw project hebt, bouwt u deze weer op en probeert u het opnieuw.

## <a name="project-types"></a>Projecttypen

De verbonden service controleert het type project dat u ontwikkelt, zodat het de juiste verificatielogica in het project kan injecteren. Als er een controller is `ApiController` die afkomstig is van het project, wordt het project beschouwd als een WebAPI-project. Als er alleen controllers `MVC.Controller` zijn die afkomstig zijn uit het project, wordt het project beschouwd als een MVC-project. De verbonden service ondersteunt geen ander projecttype.

## <a name="compatible-authentication-code"></a>Compatibele verificatiecode

De verbonden service controleert ook op verificatie-instellingen die eerder zijn geconfigureerd of compatibel zijn met de service. Als alle instellingen aanwezig zijn, wordt het beschouwd als een aanvraag voor nieuwe deelnemers en wordt de verbonden service geopend, de instellingen weergeven.  Als slechts enkele instellingen aanwezig zijn, wordt dit beschouwd als een foutgeval.

In een MVC-project controleert de verbonden service op een van de volgende instellingen, die het gevolg zijn van eerder gebruik van de service:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

De verbonden service controleert ook op een van de volgende instellingen in een Web API-project, die het gevolg zijn van eerder gebruik van de service:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>Incompatibele verificatiecode

Ten slotte probeert de verbonden service versies van verificatiecode te detecteren die zijn geconfigureerd met eerdere versies van Visual Studio. Als u deze fout hebt ontvangen, betekent dit dat uw project een oncompatibel verificatietype bevat. De verbonden service detecteert de volgende typen verificatie van eerdere versies van Visual Studio:

* Windows-verificatie
* Individuele gebruikersaccounts
* Organisatie-accounts

Als u Windows-verificatie in een MVC-project wilt detecteren, zoekt de verbonden verbinding naar het `authentication` element in uw `web.config` bestand.

```xml
<configuration>
    <system.web>
        <authentication mode="Windows" />
    </system.web>
</configuration>
```

Als u Windows-verificatie in een Web API-project wilt detecteren, zoekt de verbonden service naar het element in het `IISExpressWindowsAuthentication` bestand van `.csproj` uw project:

```xml
<Project>
    <PropertyGroup>
        <IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication>
    </PropertyGroup>
</Project>
```

Als u verificatie van afzonderlijke gebruikersaccounts wilt detecteren, `packages.config` zoekt de verbonden service naar het pakketelement in uw bestand.

```xml
<packages>
    <package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" />
</packages>
```

Om een oude vorm van verificatie van het organigaccount`web.config`te detecteren, zoekt de verbonden service naar het volgende element in:

```xml
<configuration>
    <appSettings>
        <add key="ida:Realm" value="***" />
    </appSettings>
</configuration>
```

Als u het verificatietype wilt wijzigen, verwijdert u het incompatibele verificatietype en probeert u de verbonden service opnieuw toe te voegen.

Zie [Verificatiescenario's voor Azure AD voor](authentication-scenarios.md)meer informatie.
