---
title: Zelf studie-een Azure Active Directory B2C-Tenant maken
description: Meer informatie over het voorbereiden van het registreren van uw toepassingen door een Azure Active Directory B2C-Tenant te maken met behulp van de Azure Portal.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 57aed26fe2f2f03dca347836f21d1cad14a07642
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063354"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Zelfstudie: Een Azure Active Directory B2C-Tenant maken

Voordat uw toepassingen kunnen communiceren met Azure Active Directory B2C (Azure AD B2C), moeten ze zijn geregistreerd in een Tenant die u beheert.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een Azure AD B2C-tenant maken
> * Uw Tenant aan uw abonnement koppelen

In de volgende zelf studie leert u hoe u een toepassing registreert.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-an-azure-ad-b2c-tenant"></a>Een Azure AD B2C-tenant maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map gebruikt die uw abonnement bevat. Selecteer het filter **Directory + abonnement** in het bovenste menu en selecteer vervolgens de map die uw abonnement bevat. Deze map wijkt af van de directory die uw Azure AD B2C-Tenant bevat.

    ![Het Directory-en abonnements filter waarvoor een abonnement-Tenant is geselecteerd](./media/tutorial-create-tenant/switch-directory-subscription.PNG)

3. Kies **een resource maken** in de linkerbovenhoek van de Azure Portal.
4. Zoek en selecteer **Active Directory B2C**en klik vervolgens op **maken**.
5. Kies **een nieuwe Azure AD B2C Tenant maken** en voer de naam van de organisatie en de initiële domein naam in. Selecteer het land/de regio (deze kan later niet worden gewijzigd) en klik vervolgens op **maken**.

    De initiële domein naam wordt gebruikt als onderdeel van de naam van uw Tenant. In dit voor beeld is de naam van de Tenant *contoso0926Tenant.onmicrosoft.com*:

    ![De B2C-pagina voor het maken van tenants in de Azure Portal](./media/tutorial-create-tenant/create-tenant.PNG)

6. Kies op de pagina **nieuwe B2C-Tenant maken of koppeling naar bestaande Tenant** **de optie een bestaande Azure AD B2C-Tenant koppelen aan mijn Azure-abonnement**.

    Selecteer de Tenant die u hebt gemaakt en selecteer uw abonnement.

    Selecteer voor resource groep de optie **nieuwe maken**. Voer een naam in voor de resource groep die de Tenant bevat, selecteer de locatie en klik vervolgens op **maken**.
1. Als u uw nieuwe Tenant wilt gaan gebruiken, moet u ervoor zorgen dat u de map gebruikt die uw Azure AD B2C Tenant bevat door het adres van de map en het **abonnement** te selecteren in het bovenste menu en de map te kiezen waarin het zich bevindt.

    Als u uw nieuwe Azure B2C-Tenant in de lijst niet ziet, vernieuwt u het browser venster en selecteert u vervolgens het filter voor het adres van de map en het **abonnement** in het bovenste menu.

    ![Map-en abonnements filter waarvoor B2C-Tenant is geselecteerd](./media/tutorial-create-tenant/switch-directories.PNG)

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een Azure AD B2C-tenant maken
> * Uw Tenant aan uw abonnement koppelen

Vervolgens leert u hoe u een webtoepassing in uw nieuwe Tenant kunt registreren.

> [!div class="nextstepaction"]
> [Uw toepassingen registreren >](tutorial-register-applications.md)
