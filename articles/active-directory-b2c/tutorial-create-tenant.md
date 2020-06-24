---
title: Zelf studie-een Azure Active Directory B2C-Tenant maken
description: Meer informatie over het voorbereiden van het registreren van uw toepassingen door een Azure Active Directory B2C-Tenant te maken met behulp van de Azure Portal.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4e2957ea179d9737bb6aad6fb8f121780575115b
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84976376"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Zelfstudie: Een Azure Active Directory B2C-tenant maken

Voordat uw toepassingen kunnen communiceren met Azure Active Directory B2C (Azure AD B2C), moeten ze zijn geregistreerd in een Tenant die u beheert.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een Azure AD B2C-tenant maken
> * Uw Tenant aan uw abonnement koppelen
> * Ga naar de map met uw Azure AD B2C-Tenant
> * Voeg de Azure AD B2C resource als **favoriet** toe aan de Azure Portal

In de volgende zelf studie leert u hoe u een toepassing registreert.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-an-azure-ad-b2c-tenant"></a>Een Azure AD B2C-tenant maken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/). Meld u aan met een Azure-account waaraan ten minste de rol [Inzender](../role-based-access-control/built-in-roles.md) is toegewezen binnen het abonnement of een resource groep binnen het abonnement.

1. Selecteer de map die uw abonnement bevat.

    Selecteer op de werk balk Azure Portal het pictogram voor het adres van de map en het **abonnement** en selecteer vervolgens de map die uw abonnement bevat. Deze map wijkt af van de directory die uw Azure AD B2C-Tenant bevat.

    ![Abonnement-Tenant, adres lijst-en abonnements filter waarvoor de Tenant van het abonnement is geselecteerd](media/tutorial-create-tenant/portal-01-pick-directory.png)

1. Selecteer in het menu van de Azure-portal of op de **startpagina** de optie **Een resource maken**.
1. Zoek naar **Azure Active Directory B2C**en selecteer vervolgens **maken**.
1. Selecteer **Een nieuwe Azure AD B2C-tenant maken**.

    ![Maak een nieuwe Azure AD B2C-Tenant geselecteerd in Azure Portal](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. Voer op de pagina **een directory maken** het volgende in:

   - **Organisatie naam** : Voer de naam van uw organisatie in.
   - **Initiële domein naam** : Voer een domein naam in. Deze naam wordt standaard toegevoegd met *. onmicrosoft.com*. U kunt dit later wijzigen door een domein naam toe te voegen die uw organisatie al gebruikt, zoals ' contoso.com '.
   - **Land of regio** : Selecteer uw land of regio in de lijst. U kunt deze selectie later niet meer wijzigen.
   - **Abonnement** : Selecteer uw abonnement in de lijst.
   - **Resource groep** : Selecteer een resource groep die de Tenant zal bevatten. Of selecteer **Nieuw maken**, voer een **naam** in voor de resource groep, selecteer de locatie van de **resource groep**en selecteer **OK**.

    ![Een Tenant formulier maken in met voorbeeld waarden in Azure Portal](media/tutorial-create-tenant/review-and-create-tenant.png)

1. Selecteer **Controleren + maken**.
1. Controleer de instellingen van uw Directory. Selecteer vervolgens **Maken**.

U kunt meerdere Azure AD B2C-tenants koppelen aan één Azure-abonnement voor facturerings doeleinden.

## <a name="select-your-b2c-tenant-directory"></a>Selecteer uw B2C-Tenant Directory

Als u uw nieuwe Azure AD B2C Tenant wilt gaan gebruiken, moet u overschakelen naar de map die de Tenant bevat.

Selecteer het filter **Directory + abonnement** in het bovenste menu van de Azure Portal en selecteer vervolgens de map die uw Azure AD B2C Tenant bevat.

Als u uw nieuwe Azure B2C-Tenant in de lijst niet ziet, vernieuwt u het browser venster en selecteert u vervolgens het filter voor het adres van de map en het **abonnement** in het bovenste menu.

![B2C-Tenant die de map bevat die is geselecteerd in Azure Portal](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Azure AD B2C als favoriet toevoegen (optioneel)

Met deze optionele stap kunt u uw Azure AD B2C-Tenant gemakkelijker selecteren in de volgende en alle volgende zelf studies.

In plaats van elke keer dat u met uw Tenant wilt werken, kunt u in plaats daarvan *Azure AD B2C* in **alle services** zoeken. Vervolgens kunt u deze selecteren in de sectie **Favorieten** van het menu van de portal om snel naar uw Azure AD B2C-Tenant te bladeren.

U hoeft deze bewerking slechts één keer uit te voeren. Voordat u deze stappen uitvoert, moet u ervoor zorgen dat u bent overgeschakeld naar de map met uw Azure AD B2C Tenant, zoals wordt beschreven in de vorige sectie, [Selecteer uw B2C-Tenant Directory](#select-your-b2c-tenant-directory).

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer in het menu Azure Portal **alle services**.
1. In het zoekvak **alle services** zoekt u naar **Azure AD B2C**, houdt u de muis aanwijzer boven het Zoek resultaat en selecteert u vervolgens het ster pictogram in de knop info. **Azure AD B2C** nu wordt weer gegeven in de Azure portal onder **Favorieten**.
1. Als u de positie van uw nieuwe favoriet wilt wijzigen, gaat u naar het menu Azure Portal, selecteert u **Azure AD B2C**en sleept u het omhoog of omlaag naar de gewenste positie.

    ![Azure AD B2C, menu Favorieten Microsoft Azure-portal](media/tutorial-create-tenant/portal-08-b2c-favorite.png)

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een Azure AD B2C-tenant maken
> * Uw Tenant aan uw abonnement koppelen
> * Ga naar de map met uw Azure AD B2C-Tenant
> * Voeg de Azure AD B2C resource als **favoriet** toe aan de Azure Portal

Vervolgens leert u hoe u een webtoepassing in uw nieuwe Tenant kunt registreren.

> [!div class="nextstepaction"]
> [Uw toepassingen registreren >](tutorial-register-applications.md)
