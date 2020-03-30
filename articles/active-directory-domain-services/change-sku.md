---
title: De SKU wijzigen voor een Azure AD Domain Services | Microsoft Documenten
description: Meer informatie over het wijzigen van de SKU-laag voor een beheerd Azure AD Domain Services-domein als uw bedrijfsvereisten veranderen
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: iainfou
ms.openlocfilehash: b65310569e95173b88dd0aa0dfe1dbacd86cc8fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126698"
---
# <a name="change-the-sku-for-an-existing-azure-ad-domain-services-managed-domain"></a>De SKU wijzigen voor een bestaand beheerd Azure AD Domain Services-domein

In Azure Active Directory Domain Services (Azure AD DS) zijn de beschikbare prestaties en functies gebaseerd op het SKU-type. Deze functieverschillen omvatten de back-upfrequentie of het maximumaantal one-way outbound forest-vertrouwensrelaties (momenteel in preview). U selecteert een SKU wanneer u het beheerde domein maakt en u SKU's omhoog of omlaag schakelen als uw bedrijfsbehoeften veranderen nadat het beheerde domein is geïmplementeerd. Wijzigingen in de bedrijfsvereisten kunnen de noodzaak omvatten van frequentere back-ups of het maken van extra forestvertrouwensrelaties. Zie [Azure AD DS SKU-concepten][concepts-sku] en [Azure AD DS-prijspagina's][pricing] voor meer informatie over de limieten en prijzen van de verschillende SKU's.

In dit artikel ziet u hoe u de SKU voor een bestaand door Azure AD DS beheerd domein wijzigt met behulp van de Azure-portal.

## <a name="before-you-begin"></a>Voordat u begint

Als u dit artikel wilt voltooien, hebt u de volgende bronnen en bevoegdheden nodig:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)aan .
* Een Azure Active Directory-tenant die is gekoppeld aan uw abonnement, gesynchroniseerd met een on-premises directory of een map met alleen wolken.
    * Maak indien nodig [een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account.][associate-azure-ad-tenant]
* Een beheerd azure Directory Domain Services-domein is ingeschakeld en geconfigureerd in uw Azure AD-tenant.
    * Vul indien nodig de zelfstudie in om [een Azure Active Directory Domain Services-exemplaar][create-azure-ad-ds-instance]te maken en te configureren.

## <a name="sku-change-limitations"></a>Beperkingen voor SKU-wijzigingen

U SKU's omhoog of omlaag wijzigen nadat het door Azure AD DS beheerde domein is geïmplementeerd. Als u echter een resourceforest gebruikt (momenteel in preview) en een malige forestvertrouwensrelaties hebt gemaakt van Azure AD DS naar een on-premises AD DS-omgeving, zijn er enkele beperkingen voor de Wijzigingsbewerking Van SKU. De *Premium-* en *Enterprise* SKU's definiëren een limiet op het aantal vertrouwensrelaties dat u maken. U niet overstappen op een SKU met een lagere maximumlimiet dan u momenteel hebt geconfigureerd.

Bijvoorbeeld:

* Als u twee forestvertrouwensrelaties op de *Premium* SKU hebt gemaakt, u niet worden gewijzigd in de *Standaard* SKU. De *Standaard* SKU ondersteunt geen forestvertrouwensrelaties.
* Of als u zeven vertrouwensrelaties op de *Premium* SKU hebt gemaakt, u niet overstappen naar de *Enterprise* SKU. De *Enterprise* SKU ondersteunt maximaal vijf vertrouwensrelaties.

Zie Azure [AD DS SKU-functies en -limieten][concepts-sku]voor meer informatie over deze limieten.

## <a name="select-a-new-sku"></a>Een nieuwe SKU selecteren

Voer de volgende stappen uit om de SKU voor een door Azure AD DS beheerd domein te wijzigen met behulp van de Azure-portal:

1. Zoek en selecteer Azure AD Domain Services boven aan de **Azure-portal.** Kies uw beheerde domein in de lijst, zoals *aaddscontoso.com*.
1. Selecteer **Instellingen > SKU**in het menu aan de linkerkant van de pagina Azure AD DS .

    ![Selecteer de menuoptie SKU voor uw door Azure AD DS beheerde domein in de Azure-portal](media/change-sku/overview-change-sku.png)

1. Selecteer in de vervolgkeuzelijst de SKU die u wilt voor uw door Azure AD DS beheerde domein. Als u een bronforest hebt, u *standaard* SKU niet selecteren omdat forestvertrouwensrelaties alleen beschikbaar zijn op de *Enterprise* SKU of hoger.

    Kies de gewenste SKU in het vervolgkeuzemenu en selecteer **Opslaan**.

    ![Kies de vereiste SKU in het vervolgkeuzemenu in de Azure-portal](media/change-sku/change-sku-selection.png)

Het kan een minuut of twee duren om het SKU-type te wijzigen.

## <a name="next-steps"></a>Volgende stappen

Zie [Een uitgaande forestvertrouwensrelatie maken naar een on-premises domein in Azure AD DS (preview)][create-trust]als u een resourceforest hebt en extra vertrouwensrelaties wilt maken na de Wijziging van de SKU.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus
[create-trust]: tutorial-create-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
