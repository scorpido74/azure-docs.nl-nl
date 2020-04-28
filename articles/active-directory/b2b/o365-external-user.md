---
title: Office 365 extern delen en B2B-samen werking-Azure AD
description: Hierin wordt beschreven hoe u resources deelt met externe partners met behulp van O365 en Azure Active Directory B2B-samen werking.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/24/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d70aed6fbe0f09ea6284f913c88186ecf94e297
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74272273"
---
# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Office 365 extern delen en Azure Active Directory B2B-samen werking

Extern delen in Office 365 (OneDrive, share point online, Unified groups, enzovoort) en Azure Active Directory (Azure AD) B2B-samen werking zijn technisch gezien hetzelfde. Alle externe delen (met uitzonde ring van OneDrive/share point online), met inbegrip van gasten in Office 365-groepen, maakt al gebruik van de Azure AD B2B-uitnodiging voor samen werking voor delen.

## <a name="how-does-azure-ad-b2b-differ-from-external-sharing-in-sharepoint-online"></a>Hoe verschilt Azure AD B2B van extern delen in share point online?

OneDrive/share point online heeft een afzonderlijke uitnodigings Manager. Ondersteuning voor extern delen in OneDrive/share point online is gestart voordat de ondersteuning van Azure AD is ontwikkeld. Extern delen van OneDrive/share point online heeft na verloop van tijd diverse functies en vele miljoenen gebruikers gegroeid die gebruikmaken van het in de bouw patroon ingebouwd deel van het product. Er zijn echter enkele subtiele verschillen tussen hoe OneDrive/share point online extern delen werkt en hoe Azure AD B2B-samen werking werkt. Meer informatie over OneDrive/share point online extern delen vindt u in [overzicht van externe delen](https://docs.microsoft.com/sharepoint/external-sharing-overview). Het proces wijkt doorgaans af van Azure AD B2B op de volgende manieren:

- OneDrive/share point online voegt gebruikers toe aan de Directory nadat gebruikers hun uitnodigingen hebben ingewisseld. Vóór de aflossing ziet u de gebruiker dus niet in de Azure AD-Portal. Als een andere site een gebruiker in de tussen tijd nodig heeft, wordt er een nieuwe uitnodiging gegenereerd. Wanneer u echter Azure AD B2B-samen werking gebruikt, worden gebruikers onmiddellijk aan de uitnodiging toegevoegd, zodat ze overal worden weer gegeven.

- De inwisselings ervaring in OneDrive/share point online ziet er anders uit dan de ervaring in azure AD B2B-samen werking. Nadat een gebruiker een uitnodiging heeft ingewisseld, zien de ervaringen er hetzelfde uit.

- Uitgenodigde Azure AD B2B-samenwerkings verbanden kunnen worden gekozen in de dialoog vensters van OneDrive/share point online delen. Gebruikers van OneDrive/share point online worden ook in azure AD weer gegeven na het inwisselen van hun uitnodigingen.

- De licentie vereisten verschillen. Voor elke betaalde Azure AD-licentie kunt u Maxi maal vijf gast gebruikers toegang bieden tot uw betaalde Azure AD-functies. Zie voor meer informatie over licentie verlening [Azure AD B2B-licenties](https://docs.microsoft.com/azure/active-directory/b2b/licensing-guidance) en [' wat is een externe gebruiker? ' in het overzicht van extern delen in share point online](https://docs.microsoft.com/sharepoint/external-sharing-overview#what-happens-when-users-share).

Als u extern delen in OneDrive of share point online wilt beheren met Azure AD B2B-samen werking, stelt u de instelling voor extern delen van OneDrive/share point online in op **alleen delen toestaan met de externe gebruikers die al bestaan in de adres lijst van uw organisatie**. Gebruikers kunnen naar extern gedeelde sites gaan en kiezen uit externe deel nemers die de beheerder heeft toegevoegd. De beheerder kan de externe deel nemers toevoegen via de uitnodiging-Api's voor B2B-samen werking.


![De instelling voor extern delen met OneDrive/share point online](media/o365-external-user/odsp-sharing-setting.png)

Na het inschakelen van extern delen, is de mogelijkheid om te zoeken naar bestaande gast gebruikers in de share point online (SPO) persoons kiezer standaard uitgeschakeld om verouderd gedrag te vinden.

U kunt deze functie inschakelen met behulp van de instelling ' ShowPeoplePickerSuggestionsForGuestUsers ' op het niveau van de Tenant en site verzameling. U kunt de functie instellen met behulp van de cmdlets set-SPOTenant en set-SPOSite, waarmee leden alle bestaande gast gebruikers in de Directory kunnen doorzoeken. Wijzigingen in het Tenant bereik hebben geen invloed op reeds ingerichte SPO-sites.

## <a name="next-steps"></a>Volgende stappen

* [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)
* [Een B2B-samenwerkings gebruiker toevoegen aan een rol](add-guest-to-role.md)
* [Uitnodigingen voor B2B-samen werking overdragen](delegate-invitations.md)
* [Dynamische groepen en B2B-samen werking](use-dynamic-groups.md)
* [Problemen oplossen Azure Active Directory B2B-samen werking](troubleshoot.md)
