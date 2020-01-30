---
title: Aangepaste kenmerken definiëren in Azure Active Directory B2C | Microsoft Docs
description: Aangepaste kenmerken definiëren voor uw toepassing in Azure Active Directory B2C om informatie over uw klanten te verzamelen.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 433eff8f7ec22a3484e8e7f38dab2bb1c24e2fcc
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848068"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Aangepaste kenmerken definiëren in Azure Active Directory B2C

 Elke klant gerichte toepassing heeft unieke vereisten voor de gegevens die moeten worden verzameld. De Tenant van uw Azure Active Directory B2C (Azure AD B2C) wordt geleverd met een ingebouwde set informatie die is opgeslagen in kenmerken, zoals de naam, de voor waarde, de plaats en de post code. Met Azure AD B2C kunt u de set met kenmerken die zijn opgeslagen op elk klant account uitbreiden.

 U kunt aangepaste kenmerken in de [Azure Portal](https://portal.azure.com/) maken en gebruiken in uw registratie gebruikers stromen, registraties of aanmeldings stromen van gebruikers of profiel bewerkings gebruikers stromen. U kunt deze kenmerken ook lezen en schrijven met behulp van de [Azure AD-Graph API](manage-user-accounts-graph-api.md). Aangepaste kenmerken in Azure AD B2C [Azure AD Graph API Directory-schema-uitbrei dingen](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions)gebruiken.

> [!NOTE]
> Ondersteuning voor nieuwere [Microsoft Graph-API](https://docs.microsoft.com/graph/overview?view=graph-rest-1.0) voor het uitvoeren van query's in azure AD B2C Tenant is nog steeds in ontwikkeling.
>

## <a name="create-a-custom-attribute"></a>Een aangepast kenmerk maken

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map met uw Azure AD B2C-tenant gebruikt door hiernaar over te schakelen rechtsboven in de Azure Portal. Selecteer de abonnementsgegevens en selecteer vervolgens **Schakelen tussen mappen**.

    ![Overschakelen naar de Azure AD B2C-tenant](./media/user-flow-custom-attributes/switch-directories.png)

    Kies de map met uw tenant.

    ![B2C-Tenant gemarkeerd in Directory-en abonnements filter](./media/user-flow-custom-attributes/select-directory.PNG)

3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer **gebruikers kenmerken**en selecteer vervolgens **toevoegen**.
5. Geef een **naam** op voor het aangepaste kenmerk (bijvoorbeeld ' ShoeSize ')
6. Kies een **gegevens type**. Alleen **String**, **Boolean**en **int** zijn beschikbaar.
7. Voer desgewenst een **Beschrijving** in voor informatieve doel einden.
8. Klik op **Maken**.

Het aangepaste kenmerk is nu beschikbaar in de lijst met **gebruikers kenmerken** en voor gebruik in uw gebruikers stromen. Een aangepast kenmerk wordt alleen gemaakt wanneer het voor de eerste keer wordt gebruikt in een gebruikers stroom en niet wanneer u het toevoegt aan de lijst met **gebruikers kenmerken**.


## <a name="use-a-custom-attribute-in-your-user-flow"></a>Een aangepast kenmerk gebruiken in uw gebruikers stroom

1. Selecteer in uw Azure AD B2C-Tenant **gebruikers stromen**.
2. Selecteer uw beleid (bijvoorbeeld ' B2C_1_SignupSignin ') om het te openen.
4. Selecteer **gebruikers kenmerken** en selecteer vervolgens het aangepaste kenmerk (bijvoorbeeld ' ShoeSize '). Klik op **Opslaan**.
5. Selecteer **toepassings claims** en selecteer vervolgens het aangepaste kenmerk.
6. Klik op **Opslaan**.

Zodra u een nieuwe gebruiker hebt gemaakt met behulp van een gebruikers stroom die gebruikmaakt van het zojuist gemaakte aangepaste kenmerk, kan het object in [Azure AD Graph Explorer](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart)worden opgevraagd. U kunt ook de functie [**gebruikers stroom uitvoeren**](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows) op de gebruikers stroom gebruiken om de klant ervaring te controleren. Nu ziet u **ShoeSize** in de lijst met kenmerken die tijdens de registratie worden verzameld en wordt deze weer gegeven in het token dat wordt teruggestuurd naar uw toepassing.

