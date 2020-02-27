---
title: De SKU voor een Azure AD Domain Services wijzigen | Microsoft Docs
description: Meer informatie over de SKU-laag voor een Azure AD Domain Services beheerd domein als uw bedrijfs vereisten veranderen
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: iainfou
ms.openlocfilehash: 18395f2b839aef88491f71aeed660eb2ce011e2c
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77614242"
---
# <a name="change-the-sku-for-an-existing-azure-ad-domain-services-managed-domain"></a>De SKU voor een bestaand Azure AD Domain Services beheerd domein wijzigen

In Azure Active Directory Domain Services (Azure AD DS) zijn de beschik bare prestaties en functies gebaseerd op het type SKU. Deze functie verschillen omvatten de back-upfrequentie of het maximum aantal eenrichtings vertrouwensrelaties voor uitgaande forests (momenteel als preview-versie). U selecteert een SKU wanneer u het beheerde domein maakt en u kunt switches als uw bedrijfs behoeften veranderen nadat het beheerde domein is geïmplementeerd. Wijzigingen in zakelijke vereisten kunnen de nood zaak van vaker back-ups bevatten of extra forestvertrouwensrelaties maken. Meer informatie over de limieten en prijzen van de verschillende Sku's vindt u in [azure AD DS SKU-concepten][concepts-sku] en [Azure AD DS prijzen][pricing] pagina's.

In dit artikel wordt beschreven hoe u de SKU voor een bestaand Azure AD DS beheerde domein wijzigt met behulp van de Azure Portal.

## <a name="before-you-begin"></a>Voordat u begint

U hebt de volgende resources en bevoegdheden nodig om dit artikel te volt ooien:

* Een actief Azure-abonnement.
    * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory Tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises Directory of een alleen-Cloud Directory.
    * Als dat nodig is, [maakt u een Azure Active Directory-Tenant][create-azure-ad-tenant] of [koppelt u een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* Een Azure Active Directory Domain Services beheerd domein ingeschakeld en geconfigureerd in uw Azure AD-Tenant.
    * Als dat nodig is, voltooit u de zelf studie voor het [maken en configureren van een Azure Active Directory Domain Services-exemplaar][create-azure-ad-ds-instance].

## <a name="sku-change-limitations"></a>Beperkingen voor het wijzigen van de SKU

Er gelden enkele beperkingen voor de bewerking voor het wijzigen van de SKU als u een resource-forest gebruikt (momenteel in de preview-versie) en u eenrichtings uitgaande forestvertrouwensrelaties van Azure hebt gemaakt AD DS naar een on-premises AD DS omgeving. De *Premium* -en *Enter prise* -sku's definiëren een limiet voor het aantal vertrouwens relaties dat u kunt maken. U kunt niet overschakelen naar een SKU met een lagere maximum limiet dan u momenteel hebt geconfigureerd.

Als u bijvoorbeeld twee forest-vertrouwens relaties hebt gemaakt voor de *Premium* -SKU, kunt u niet verlagen naar de *standaard* -SKU. De *standaard* -SKU ondersteunt geen forest-vertrouwens relaties. Of, als u zeven vertrouwens relaties hebt gemaakt voor de *Premium* -SKU, kunt u niet verlagen naar de *Enter prise* -SKU. De *Enter prise* -SKU ondersteunt Maxi maal vijf vertrouwens relaties.

Zie [Azure AD DS SKU-functies en-limieten][concepts-sku]voor meer informatie over deze limieten.

## <a name="select-a-new-sku"></a>Een nieuwe SKU selecteren

Voer de volgende stappen uit om de SKU voor een met Azure AD DS beheerd domein te wijzigen met behulp van de Azure Portal:

1. Zoek en selecteer **Azure AD Domain Services**aan de bovenkant van de Azure Portal. Kies uw beheerde domein in de lijst, zoals *aaddscontoso.com*.
1. Selecteer in het menu aan de linkerkant van de pagina Azure-AD DS **instellingen > SKU**.

    ![Selecteer de menu optie van de SKU voor uw Azure AD DS beheerde domein in het Azure Portal](media/change-sku/overview-change-sku.png)

1. Selecteer in de vervolg keuzelijst de SKU die u wilt voor uw Azure AD DS beheerde domein. Als u een resource-forest hebt, kunt u *standaard* -SKU niet selecteren als forest-vertrouwens relaties zijn alleen beschikbaar in de *Enter prise* -SKU of hoger.

    Kies de gewenste SKU in de vervolg keuzelijst en selecteer vervolgens **Opslaan**.

    ![Kies de vereiste SKU in het vervolg keuzemenu van de Azure Portal](media/change-sku/change-sku-selection.png)

Het kan een paar minuten duren voordat het SKU-type is gewijzigd.

## <a name="next-steps"></a>Volgende stappen

Zie [een uitgaande forestvertrouwensrelatie maken met een on-premises domein in Azure AD DS (preview)][create-trust]als u een bron-forest hebt en aanvullende vertrouwens relaties wilt maken nadat de SKU is gewijzigd.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus
[create-trust]: tutorial-create-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
