---
title: Foutie mapfouten in Azure AD Domain Services oplossen | Microsoft Documenten
description: Meer informatie over wat een niet-overeenkomende mapfout betekent en hoe u deze oplossen in Azure AD Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: iainfou
ms.openlocfilehash: 76dc964b7fe7f5e8acfcfb03b2e89bebb2caa176
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613381"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Foutie mapfouten voor bestaande beheerde Azure AD Domain Services-domeinen oplossen

Als een azure active directory domain services (Azure AD DS)-beheerd domein een fout van de niet-overeenkomende tenant vertoont, u het beheerde domein pas beheren als het is opgelost. Deze fout treedt op als het onderliggende Azure-virtuele netwerk wordt verplaatst naar een andere Azure AD-map.

In dit artikel wordt uitgelegd waarom de fout optreedt en hoe u deze oplossen.

## <a name="what-causes-this-error"></a>Wat zijn de oorzaken van deze fout?

Er treedt een fout op die niet is gewijzigd wanneer een door Azure AD DS beheerd domein en virtueel netwerk tot twee verschillende Azure AD-tenants behoren. U bijvoorbeeld een door Azure AD DS beheerd domein hebben dat wordt genoemd *aaddscontoso.com* dat wordt uitgevoerd in de Azure AD-tenant van Contoso. Het Azure virtual network voor beheerd domein maakt echter deel uit van de Fabrikam Azure AD-tenant.

Azure gebruikt rbac (role-based access control) om de toegang tot bronnen te beperken. Wanneer u Azure AD DS inschakelt in een Azure AD-tenant, worden referentieshashes gesynchroniseerd met het beheerde domein. Voor deze bewerking moet u een tenantbeheerder zijn voor de Azure AD-map en moet de toegang tot de referenties worden beheerd. Als u resources wilt implementeren voor een virtueel azure-netwerk en beheerverkeer, moet u beheerdersbevoegdheden hebben op het virtuele netwerk waarin u Azure AD DS implementeert.

Als RBAC consistent en veilig toegang wil hebben tot alle bronnen die Azure AD DS gebruikt, moeten het beheerde domein en het virtuele netwerk tot dezelfde Azure AD-tenant behoren.

De volgende regels zijn van toepassing in de resourcemanager-omgeving:

- Een Azure AD-map kan meerdere Azure-abonnementen hebben.
- Een Azure-abonnement kan meerdere bronnen hebben, zoals virtuele netwerken.
- Eén beheerd Azure AD Domain Services-beheerd domein is ingeschakeld voor een Azure AD-map.
- Een beheerd Azure AD Domain Services-domein kan worden ingeschakeld op een virtueel netwerk dat behoort tot een van de Azure-abonnementen binnen dezelfde Azure AD-tenant.

### <a name="valid-configuration"></a>Geldige configuratie

In het volgende voorbeeldimplementatiescenario is het beheerde domein Contoso Azure AD DS ingeschakeld in de Ad-tenant van Contoso Azure. Het beheerde domein wordt geïmplementeerd in een virtueel netwerk dat behoort tot een Azure-abonnement dat eigendom is van de Contoso Azure AD-tenant. Zowel het beheerde domein als het virtuele netwerk behoren tot dezelfde Azure AD-tenant. Deze voorbeeldconfiguratie is geldig en wordt volledig ondersteund.

![Geldige Azure AD DS-tenantconfiguratie met het beheerde domein en het virtuele netwerkonderdeel van dezelfde Azure AD-tenant](./media/getting-started/valid-tenant-config.png)

### <a name="mismatched-tenant-configuration"></a>Niet op elkaar afgestemde tenantconfiguratie

In dit voorbeeld implementatiescenario is het beheerde Domein Contoso Azure AD DS ingeschakeld in de Contoso Azure AD-tenant. Het beheerde domein wordt echter geïmplementeerd in een virtueel netwerk dat behoort tot een Azure-abonnement dat eigendom is van de Fabrikam Azure AD-tenant. Het beheerde domein en het virtuele netwerk behoren tot twee verschillende Azure AD-tenants. Deze voorbeeldconfiguratie is een niet-overeenkomende tenant en wordt niet ondersteund. Het virtuele netwerk moet worden verplaatst naar dezelfde Azure AD-tenant als het beheerde domein.

![Niet op elkaar afgestemde tenantconfiguratie](./media/getting-started/mismatched-tenant-config.png)

## <a name="resolve-mismatched-tenant-error"></a>Fout van de niet-overeenkomende tenant oplossen

Met de volgende twee opties wordt de fout in de niet-overeenkomende map opgelost:

* [Verwijder het door Azure AD DS beheerde domein](delete-aadds.md) uit uw bestaande Azure AD-map. [Maak een vervangend Azure AD DS-beheerd domein](tutorial-create-instance.md) in dezelfde Azure AD-map als het virtuele netwerk dat u wilt gebruiken. Wanneer u klaar bent, sluit u alle machines aan die eerder zijn verbonden met het verwijderde domein bij het opnieuw gemaakte beheerde domein.
* [Verplaats het Azure-abonnement](../cost-management-billing/manage/billing-subscription-transfer.md) met het virtuele netwerk naar dezelfde Azure AD-map als het beheerde Azure AD DS-domein.

## <a name="next-steps"></a>Volgende stappen

Zie de handleiding voor het oplossen van [problemen](troubleshoot.md)met Azure AD DS voor meer informatie over probleemoplossingsproblemen.
