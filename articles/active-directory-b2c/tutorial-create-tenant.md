---
title: Zelfstudie - Een Azure Active Directory B2C-tenant maken
description: Meer informatie over het voorbereiden van uw toepassingen door een Azure Active Directory B2C-tenant te maken met behulp van de Azure-portal.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c31f3c4c6688af7d2142180e8d9b7100965bad96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186400"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Zelfstudie: Een Azure Active Directory B2C-tenant maken

Voordat uw toepassingen kunnen communiceren met Azure Active Directory B2C (Azure AD B2C), moeten ze zijn geregistreerd in een tenant die u beheert.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een Azure AD B2C-tenant maken
> * Uw huurder koppelen aan uw abonnement
> * Overschakelen naar de map met uw Azure AD B2C-tenant
> * De Azure AD B2C-bron toevoegen als **favoriet** in de Azure-portal

Je leert hoe je een toepassing registreert in de volgende zelfstudie.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="create-an-azure-ad-b2c-tenant"></a>Een Azure AD B2C-tenant maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/). Meld u aan met een Azure-account dat ten minste de [rol bijdragers](../role-based-access-control/built-in-roles.md) binnen het abonnement of een brongroep binnen het abonnement heeft toegewezen.

1. Selecteer de map met uw abonnement.

    Selecteer op de werkbalk Azure-portal het pictogram **Directory + Abonnement** en selecteer vervolgens de map met uw abonnement. Deze map is anders dan de map die uw Azure AD B2C-tenant bevat.

    ![Abonnementstenant, Directory + Filter van abonnement met abonnementstenant geselecteerd](media/tutorial-create-tenant/portal-01-pick-directory.png)

1. Selecteer in het menu van Azure Portal of op de **Startpagina** de optie **Een resource maken**.
1. Zoek naar **Azure Active Directory B2C**en selecteer **Vervolgens Maken**.
1. Selecteer **Een nieuwe Azure AD B2C-tenant maken**.

    ![Een nieuwe Azure AD B2C-tenant maken die is geselecteerd in Azure-portal](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. Voer een **organisatienaam** en **initiële domeinnaam in**. Selecteer het **land of de regio** (het kan later niet worden gewijzigd) en selecteer Vervolgens **Maken**.

    De domeinnaam wordt gebruikt als onderdeel van uw volledige tenant domeinnaam. In dit voorbeeld wordt de naam van de huurder *contosob2c.onmicrosoft.com:*

    ![Tenantformulier maken in met voorbeeldwaarden in Azure-portal](media/tutorial-create-tenant/portal-03-tenant-naming.png)

1. Zodra de tenant is gemaakt, selecteert u de **koppeling Nieuwe B2C-tenant maken of Koppeling naar bestaande tenant** boven aan de tenantcreatiepagina.

    ![Koppelingskoppeling broodkruimelkoppeling gemarkeerd in Azure-portal](media/tutorial-create-tenant/portal-04-select-link-sub-link.png)

1. Selecteer **Een bestaande Azure AD B2C-tenant koppelen aan mijn Azure-abonnement**.

   ![Een bestaande abonnementsselectie koppelen in Azure-portal](media/tutorial-create-tenant/portal-05-link-subscription.png)

1. Selecteer de **Azure AD B2C-tenant** die u hebt gemaakt en selecteer vervolgens uw **abonnement**.

    Selecteer voor **Resourcegroep** de optie **Nieuwe maken**. Voer een **naam** in voor de resourcegroep die de tenant bevat, selecteer de locatie van de **resourcegroep**en selecteer **Vervolgens Maken**.

    ![Formulier voor abonnementsinstellingen koppelen in Azure-portal](media/tutorial-create-tenant/portal-06-link-subscription-settings.png)

    U meerdere Azure AD B2C-tenants koppelen aan één Azure-abonnement voor factureringsdoeleinden.

## <a name="select-your-b2c-tenant-directory"></a>Selecteer uw B2C-tenantmap

Als u uw nieuwe Azure AD B2C-tenant wilt gebruiken, moet u overschakelen naar de map die de tenant bevat.

Selecteer het **filter Directory + abonnement** in het bovenste menu van de Azure-portal en selecteer vervolgens de map met uw Azure AD B2C-tenant.

Als u uw nieuwe Azure B2C-tenant in de lijst eerst niet ziet, vernieuwt u uw browservenster en selecteert u het **filter directory + abonnement** opnieuw in het bovenste menu.

![Lijst met B2C-tenant die is geselecteerd in Azure-portal](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Azure AD B2C toevoegen als favoriet (optioneel)

Met deze optionele stap u uw Azure AD B2C-tenant gemakkelijker selecteren in de volgende en alle volgende zelfstudies.

In plaats van te zoeken naar *Azure AD B2C* in **Alle services** elke keer dat u met uw tenant wilt werken, u in plaats daarvan de favoriet zijn voor de bron. Vervolgens u deze selecteren in de sectie **Favorieten** van het portalmenu om snel naar uw Azure AD B2C-tenant te bladeren.

U hoeft deze bewerking slechts één keer uit te voeren. Voordat u deze stappen uitvoert, controleert u of u bent overgestapt naar de map met uw Azure AD B2C-tenant zoals beschreven in de vorige sectie, [Selecteer uw B2C-tenantmap](#select-your-b2c-tenant-directory).

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer alle services in het menu **Azure-portal**.
1. Zoek in het zoekvak **Alle services** naar Azure **AD B2C,** plaats de plaats boven het zoekresultaat en selecteer vervolgens het sterpictogram in de knopinfo. **Azure AD B2C** wordt nu weergegeven in de Azure-portal onder **Favorieten**.
1. Als u de positie van uw nieuwe favoriet wilt wijzigen, gaat u naar het azure-portalmenu, selecteert u **Azure AD B2C**en sleept u deze omhoog of omlaag naar de gewenste positie.

    ![Azure AD B2C, menu Favorieten, Microsoft Azure-portal](media/tutorial-create-tenant/portal-08-b2c-favorite.png)

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een Azure AD B2C-tenant maken
> * Uw huurder koppelen aan uw abonnement
> * Overschakelen naar de map met uw Azure AD B2C-tenant
> * De Azure AD B2C-bron toevoegen als **favoriet** in de Azure-portal

Leer vervolgens hoe u een webtoepassing registreert in uw nieuwe tenant.

> [!div class="nextstepaction"]
> [Registreer uw sollicitaties >](tutorial-register-applications.md)
