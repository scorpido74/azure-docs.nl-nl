---
title: Aan de slag met Azure AD in .NET MVC-projecten | Azure
description: Azure Active Directory gebruiken in .NET MVC-projecten nadat u verbinding hebt gemaakt met of een Azure AD hebt gemaakt met behulp van visual studio-verbonden services
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: fe408e965c528db1d82b73ee7b20bbe3b3933657
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886123"
---
# <a name="getting-started-with-azure-active-directory-aspnet-mvc-projects"></a>Aan de slag met Azure Active Directory (ASP.NET MVC-projecten)

> [!div class="op_single_selector"]
> - [Aan de slag](vs-active-directory-dotnet-getting-started.md)
> - [Wat is er gebeurd](vs-active-directory-dotnet-what-happened.md)

Dit artikel biedt extra richtlijnen nadat u Active Directory hebt toegevoegd aan een ASP.NET MVC-project via de opdracht **Project > Connected Services** van Visual Studio. Als u de service nog niet aan uw project hebt toegevoegd, u dit op elk gewenst moment doen.

Zie [Wat is er gebeurd met mijn MVC-project?](vs-active-directory-dotnet-what-happened.md) voor de wijzigingen die zijn aangebracht in uw project bij het toevoegen van de verbonden service.

## <a name="requiring-authentication-to-access-controllers"></a>Verificatie vereisen voor toegang tot controllers

Alle controllers in uw project `[Authorize]` waren versierd met het attribuut. Dit kenmerk vereist dat de gebruiker wordt geverifieerd voordat hij toegang krijgt tot deze controllers. Als u wilt dat de controller anoniem wordt geopend, verwijdert u dit kenmerk van de controller. Als u de machtigingen op een gedetailleerder niveau wilt instellen, past u het kenmerk toe op elke methode waarvoor autorisatie vereist is in plaats van deze toe te passen op de klasse controller.

## <a name="adding-signin--signout-controls"></a>Besturingselementen voor aanmelding/ afmelding toevoegen

Als u de besturingselementen SignIn/SignOut aan `_LoginPartial.cshtml` uw weergave wilt toevoegen, u de gedeeltelijke weergave gebruiken om de functionaliteit toe te voegen aan een van uw weergaven. Hier is een voorbeeld van de `_Layout.cshtml` functionaliteit die is toegevoegd aan de standaardweergave. (Let op het laatste element in de div met klasse navbar-samenvouwen):

```html
<!DOCTYPE html>
 <html>
 <head>
     <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>@ViewBag.Title - My ASP.NET Application</title>
    @Styles.Render("~/Content/css")
    @Scripts.Render("~/bundles/modernizr")
</head>
<body>
    <div class="navbar navbar-inverse navbar-fixed-top">
        <div class="container">
            <div class="navbar-header">
                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
            </div>
            <div class="navbar-collapse collapse">
                <ul class="nav navbar-nav">
                    <li>@Html.ActionLink("Home", "Index", "Home")</li>
                    <li>@Html.ActionLink("About", "About", "Home")</li>
                    <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
                </ul>
                @Html.Partial("_LoginPartial")
            </div>
        </div>
    </div>
    <div class="container body-content">
        @RenderBody() 
        <hr />
        <footer>
            <p>&copy; @DateTime.Now.Year - My ASP.NET Application</p>
        </footer>
    </div>
    @Scripts.Render("~/bundles/jquery")
    @Scripts.Render("~/bundles/bootstrap")
    @RenderSection("scripts", required: false)
</body>
</html>
```

## <a name="next-steps"></a>Volgende stappen

- [Verificatiescenario's voor Azure Active Directory](authentication-scenarios.md)
- [Aanmelding met Microsoft toevoegen aan een ASP.NET-web-app](quickstart-v2-aspnet-webapp.md)
