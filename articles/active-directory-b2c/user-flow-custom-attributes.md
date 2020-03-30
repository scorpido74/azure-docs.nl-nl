---
title: Aangepaste kenmerken definiëren in Azure Active Directory B2C | Microsoft Documenten
description: Definieer aangepaste kenmerken voor uw toepassing in Azure Active Directory B2C om informatie over uw klanten te verzamelen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7b1ecfba0435f827c7c7a4d05da619998140bc6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186046"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Aangepaste kenmerken definiëren in Azure Active Directory B2C

 Elke klantgerichte toepassing heeft unieke vereisten voor de informatie die moet worden verzameld. Uw Azure Active Directory B2C -tenant (Azure AD B2C) wordt geleverd met een ingebouwde set gegevens die zijn opgeslagen in kenmerken, zoals Voornaam, Achternaam, Plaats en Postcode. Met Azure AD B2C u de set kenmerken die op elk klantenaccount zijn opgeslagen, uitbreiden.

 U aangepaste kenmerken maken in de [Azure-portal](https://portal.azure.com/) en deze gebruiken in uw aanmeldingsgebruikersstromen, aanmeldings- of aanmeldingsgebruikersstromen of gebruikersstromen voor profielbewerking. U deze kenmerken ook lezen en schrijven met behulp van de [Microsoft Graph API.](manage-user-accounts-graph-api.md)

## <a name="create-a-custom-attribute"></a>Een aangepast kenmerk maken

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map met uw Azure AD B2C-tenant gebruikt door hiernaar over te schakelen rechtsboven in de Azure Portal. Selecteer de abonnementsgegevens en selecteer vervolgens **Schakelen tussen mappen**.

    ![Overschakelen naar de Azure AD B2C-tenant](./media/user-flow-custom-attributes/switch-directories.png)

    Kies de map met uw tenant.

    ![B2C-tenant gemarkeerd in directory- en abonnementsfilter](./media/user-flow-custom-attributes/select-directory.PNG)

3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer **Gebruikerskenmerken**en selecteer **Vervolgens Toevoegen**.
5. Een **naam opgeven** voor het aangepaste kenmerk (bijvoorbeeld 'Schoenformaat')
6. Kies een **gegevenstype**. Alleen **String,** **Boolean**en **Int** zijn beschikbaar.
7. Voer eventueel een **beschrijving** in voor informatieve doeleinden.
8. Klik **op Maken**.

Het aangepaste kenmerk is nu beschikbaar in de lijst **met gebruikerskenmerken** en voor gebruik in uw gebruikersstromen. Een aangepast kenmerk wordt alleen gemaakt wanneer het voor het eerst wordt gebruikt in een gebruikersstroom en niet wanneer u het toevoegt aan de lijst **met gebruikerskenmerken.**

## <a name="use-a-custom-attribute-in-your-user-flow"></a>Een aangepast kenmerk gebruiken in uw gebruikersstroom

1. Selecteer in uw Azure AD B2C-tenant **gebruikersstromen**.
1. Selecteer uw beleid (bijvoorbeeld B2C_1_SignupSignin) om het te openen.
1. Selecteer **Gebruikerskenmerken** en selecteer vervolgens het aangepaste kenmerk (bijvoorbeeld 'Schoenmaat'). Klik op **Opslaan**.
1. Selecteer **Toepassingsclaims** en selecteer vervolgens het aangepaste kenmerk.
1. Klik op **Opslaan**.

Zodra u een nieuwe gebruiker hebt gemaakt met behulp van een gebruikersstroom die het nieuw gemaakte aangepaste kenmerk gebruikt, kan het object worden opgevraagd in [Microsoft Graph Explorer.](https://developer.microsoft.com/graph/graph-explorer) U ook de functie [Gebruikersstroom uitvoeren](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows) op de gebruikersstroom gebruiken om de klantervaring te verifiëren. U ziet **Nu ShoeSize** in de lijst met kenmerken die tijdens de aanmeldingsreis zijn verzameld en zie het in het token dat naar uw toepassing wordt teruggestuurd.
