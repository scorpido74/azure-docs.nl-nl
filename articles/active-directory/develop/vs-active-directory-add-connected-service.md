---
title: De Active Directory connected service gebruiken (Visual Studio)
description: Een Azure Active Directory toevoegen met het dialoogvenster Verbonden services toevoegen van Visual Studio
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.custom: aaddev, vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.openlocfilehash: b3d068a8100cf78ccffac6d537fe71942239f383
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886157"
---
# <a name="add-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Een Azure Active Directory toevoegen met Verbonden services in Visual Studio

Door Azure Active Directory (Azure AD) te gebruiken, u Single Sign-On (SSO) ondersteunen voor ASP.NET MVC-webtoepassingen of Active Directory Authentication in Web API-services. Met Azure AD-verificatie kunnen uw gebruikers hun accounts uit Azure Active Directory gebruiken om verbinding te maken met uw webtoepassingen. De voordelen van Azure AD Authentication met Web API zijn verbeterde gegevensbeveiliging bij het blootstellen van een API vanuit een webtoepassing. Met Azure AD hoeft u geen apart verificatiesysteem te beheren met een eigen account en gebruikersbeheer.

In dit artikel en de bijbehorende artikelen vindt u informatie over het gebruik van de Visual Studio Connected Service-functie voor Active Directory. De mogelijkheid is beschikbaar in Visual Studio 2015 en hoger.

Op dit moment biedt de Active Directory connected-service geen ondersteuning voor ASP.NET Core-toepassingen.

## <a name="prerequisites"></a>Vereisten

- Azure-account: als u geen Azure-account hebt, u [zich aanmelden voor een gratis proefversie](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) of uw voordelen voor Visual [Studio-abonnees activeren.](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)
- **Visual Studio 2015** of hoger. [Download Visual Studio nu](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Verbinding maken met Azure Active Directory via het dialoogvenster Verbonden services

1. Maak of open in Visual Studio een ASP.NET MVC-project of een ASP.NET Web API-project. U de mvc-, web-API-, toepassingstoepassing met één pagina, Azure API-app, Azure Mobile App en Azure Mobile Service-sjablonen gebruiken.

1. Selecteer de menuopdracht **Project > Verbonden Service toevoegen...** of dubbelklik op het knooppunt Verbonden **Services** onder het project in Solution Explorer.

1. Selecteer **verificatie met Azure Active Directory**op de pagina Verbonden **Services** .

    ![Pagina Verbonden Services](./media/vs-azure-active-directory/connected-services-add-active-directory.png)

1. Selecteer **volgende**op de pagina **Inleiding** . Als u fouten op deze pagina ziet, raadpleegt u [Fouten diagnosticeren met de Azure Active Directory Connected Service](vs-active-directory-error.md).

    ![Introductiepagina](./media/vs-azure-active-directory/configure-azure-ad-wizard-1.png)

1. Selecteer op de pagina **Eén aanmelding** een domein in de vervolgkeuzelijst **Domein.** De lijst bevat alle domeinen die toegankelijk zijn voor de accounts die worden vermeld in het dialoogvenster Accountinstellingen van Visual Studio **(Bestands- > accountinstellingen...**). Als alternatief u een domeinnaam invoeren als u de domeinnaam die u `mydomain.onmicrosoft.com`zoekt niet vindt, zoals. U de optie kiezen om een Azure Active Directory-app te maken of de instellingen uit een bestaande Azure Active Directory-app te gebruiken. Selecteer **Volgende** wanneer u klaar bent.

    ![Op pagina met één teken](./media/vs-azure-active-directory/configure-azure-ad-wizard-2.png)

1. Selecteer op de pagina **Directory Access** de optie **Mapgegevens lezen** zoals gewenst. Ontwikkelaars bevatten meestal deze optie.

    ![Pagina Directorytoegang](./media/vs-azure-active-directory/configure-azure-ad-wizard-3.png)

1. Selecteer **Voltooien** om wijzigingen in uw project te starten om Azure AD-verificatie in te schakelen. Visual Studio toont de voortgang gedurende deze periode:

    ![Voortgang van verbonden Active Directory-service](./media/vs-azure-active-directory/active-directory-connected-service-output.png)

1. Wanneer het proces is voltooid, opent Visual Studio uw browser voor een van de volgende artikelen, naar gelang van uw projecttype:

    - [Aan de slag met .NET MVC-projecten](vs-active-directory-dotnet-getting-started.md)
    - [Aan de slag met WebAPI-projecten](vs-active-directory-webapi-getting-started.md)

1. U ook het Active Directory-domein zien op de [Azure-portal.](https://go.microsoft.com/fwlink/p/?LinkID=525040)

## <a name="how-your-project-is-modified"></a>Hoe uw project wordt gewijzigd

Wanneer u de verbonden service de wizard toevoegt, voegt Visual Studio Azure Active Directory en bijbehorende verwijzingen naar uw project toe. Configuratiebestanden en codebestanden in uw project worden ook gewijzigd om ondersteuning voor Azure AD toe te voegen. De specifieke wijzigingen die Visual Studio aanbrengt, zijn afhankelijk van het projecttype. Zie de volgende artikelen voor meer informatie:

- [Wat is er gebeurd met mijn .NET MVC-project?](vs-active-directory-dotnet-what-happened.md)
- [Wat is er gebeurd met mijn Web API-project?](vs-active-directory-webapi-what-happened.md)

## <a name="next-steps"></a>Volgende stappen

- [Verificatiescenario's voor Azure Active Directory](authentication-scenarios.md)
- [Aanmelding met Microsoft toevoegen aan een ASP.NET-web-app](quickstart-v2-aspnet-webapp.md)
