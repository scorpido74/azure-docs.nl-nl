---
title: Problemen met niet-overeenkomende mappen oplossen in Azure AD Domain Services | Microsoft Docs
description: Problemen met niet-overeenkomende mappen voor bestaande Azure AD Domain Services beheerde domeinen begrijpen en oplossen
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 4978f7b782271daff996807172a24103bd8d9860
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617282"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Fouten van niet-overeenkomende mappen oplossen voor bestaande Azure AD Domain Services beheerde domeinen
U hebt een bestaand Azure AD Domain Services beheerd domein. Wanneer u naar de Azure Portal gaat en het beheerde domein bekijkt, wordt het volgende fout bericht weer gegeven:

![Fout met niet-overeenkomende map](./media/getting-started/mismatched-tenant-error.png)

U kunt dit beheerde domein pas beheren als de fout is opgelost.


## <a name="whats-causing-this-error"></a>Wat is de oorzaak van deze fout?
Deze fout wordt veroorzaakt wanneer uw beheerde domein en het virtuele netwerk waarop het wordt ingeschakeld, deel uitmaken van twee verschillende Azure AD-tenants. U hebt bijvoorbeeld een beheerd domein met de naam ' contoso.com ' en het is ingeschakeld voor de Azure AD-Tenant van contoso. Het virtuele Azure-netwerk waarin het beheerde domein is ingeschakeld, behoort echter tot Fabrikam, een andere Azure AD-Tenant.

De nieuwe Azure Portal (en met name de Azure AD Domain Services-extensie) is gebaseerd op Azure Resource Manager. In de moderne Azure Resource Manager omgeving worden bepaalde beperkingen afgedwongen voor betere beveiliging en voor op rollen gebaseerd toegangs beheer (RBAC) naar bronnen. Het inschakelen van Azure AD Domain Services voor een Azure AD-Tenant is een gevoelige bewerking omdat referentie-hashes worden gesynchroniseerd met het beheerde domein. Voor deze bewerking moet u een Tenant beheerder zijn voor de Directory. Daarnaast moet u over beheerders rechten beschikken voor het virtuele netwerk waarin u het beheerde domein inschakelt. Het beheerde domein en het virtuele netwerk moeten deel uitmaken van dezelfde Azure AD-Tenant om de RBAC-controles consistent te laten werken.

Kortom, u kunt geen beheerd domein inschakelen voor een Azure AD-Tenant contoso.com in een virtueel netwerk dat deel uitmaakt van een Azure-abonnement dat eigendom is van een andere Azure AD-Tenant fabrikam.com. 

**Geldige configuratie**: In dit implementatie scenario wordt het beheerde domein contoso ingeschakeld voor de contoso Azure AD-Tenant. Het beheerde domein wordt weer gegeven in een virtueel netwerk dat deel uitmaakt van een Azure-abonnement dat eigendom is van de contoso Azure AD-Tenant. Daarom behoren zowel het beheerde domein als het virtuele netwerk tot dezelfde Azure AD-Tenant. Deze configuratie is geldig en volledig ondersteund.

![Geldige Tenant configuratie](./media/getting-started/valid-tenant-config.png)

Niet- **overeenkomende Tenant configuratie**: In dit implementatie scenario wordt het beheerde domein contoso ingeschakeld voor de contoso Azure AD-Tenant. Het beheerde domein wordt echter wel weer gegeven in een virtueel netwerk dat hoort bij een Azure-abonnement dat eigendom is van de fabrikam Azure AD-Tenant. Daarom maken het beheerde domein en het virtuele netwerk deel uit van twee verschillende Azure AD-tenants. Deze configuratie is een niet-overeenkomende Tenant configuratie en wordt niet ondersteund. Het virtuele netwerk moet worden verplaatst naar dezelfde Azure AD-Tenant (Contoso) als het beheerde domein. Zie de sectie [oplossing](#resolution) voor meer informatie.

![Niet-overeenkomende Tenant configuratie](./media/getting-started/mismatched-tenant-config.png)

Daarom ziet u deze fout wanneer het beheerde domein en het virtuele netwerk dat wordt ingeschakeld in deel uitmaken van twee verschillende Azure AD-tenants.

De volgende regels zijn van toepassing in de Resource Manager-omgeving:
- Een Azure AD-adres lijst kan meerdere Azure-abonnementen hebben.
- Een Azure-abonnement kan meerdere resources bevatten, zoals virtuele netwerken.
- Eén Azure AD Domain Services beheerd domein is ingeschakeld voor een Azure AD-adres lijst.
- Een Azure AD Domain Services beheerd domein kan worden ingeschakeld op een virtueel netwerk dat deel uitmaakt van een van de Azure-abonnementen binnen dezelfde Azure AD-Tenant.


## <a name="resolution"></a>Oplossing
Er zijn twee opties voor het oplossen van problemen met overeenkomende mappen. U kunt het volgende doen:

- Klik op de knop **verwijderen** om het bestaande beheerde domein te verwijderen. Maak opnieuw met behulp van de [Azure Portal](https://portal.azure.com), zodat het beheerde domein en het virtuele netwerk beschikbaar zijn in deel uitmaken van de Azure AD-adres lijst. Voeg alle computers die eerder zijn toegevoegd aan het verwijderde domein toe aan het zojuist gemaakte beheerde domein.

- Verplaats het Azure-abonnement met het virtuele netwerk naar de Azure AD-adres lijst waartoe uw beheerde domein behoort. Volg de stappen in het artikel [eigendom van een Azure-abonnement overdragen aan een ander account](../billing/billing-subscription-transfer.md) .


## <a name="related-content"></a>Gerelateerde inhoud
* [Azure AD Domain Services aan de slag-hand leiding](tutorial-create-instance.md)
* [Gids voor probleem oplossing-Azure AD Domain Services](troubleshoot.md)
