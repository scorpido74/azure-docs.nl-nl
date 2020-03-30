---
title: Office 365 extern delen en B2B-samenwerking - Azure AD
description: Bespreekt het delen van resources met externe partners met behulp van O365 en Azure Active Directory B2B-samenwerking.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272273"
---
# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Office 365 extern delen en Azure Active Directory B2B-samenwerking

Extern delen in Office 365 (OneDrive, SharePoint Online, Unified Groups, enz.) en Azure Active Directory (Azure AD) B2B-samenwerking zijn technisch gezien hetzelfde. Alle externe delen (behalve OneDrive/SharePoint Online), inclusief gasten in Office 365-groepen, maakt al gebruik van de Azure AD B2B-uitnodigingsuitnodigings-API's voor delen.

## <a name="how-does-azure-ad-b2b-differ-from-external-sharing-in-sharepoint-online"></a>Waarin verschilt Azure AD B2B van extern delen in SharePoint Online?

OneDrive/SharePoint Online heeft een aparte uitnodigingsmanager. Ondersteuning voor extern delen in OneDrive/SharePoint Online is gestart voordat Azure AD de ondersteuning ontwikkelde. In de loop der tijd heeft Extern delen van OneDrive/SharePoint Online verschillende functies en vele miljoenen gebruikers verzameld die het ingebouwde deelpatroon van het product gebruiken. Er zijn echter enkele subtiele verschillen tussen de manier waarop extern delen van OneDrive/SharePoint Online werkt en hoe Azure AD B2B-samenwerking werkt. Meer informatie over extern delen van OneDrive/SharePoint Online vindt u in [het overzicht extern delen](https://docs.microsoft.com/sharepoint/external-sharing-overview). Het proces verschilt over het algemeen op deze manieren van Azure AD B2B:

- OneDrive/SharePoint Online voegt gebruikers toe aan de map nadat gebruikers hun uitnodigingen hebben ingewisseld. Voor het inwisselen ziet u de gebruiker dus niet in de Azure AD-portal. Als een andere site in de tussentijd een gebruiker uitnodigt, wordt een nieuwe uitnodiging gegenereerd. Wanneer u echter Azure AD B2B-samenwerking gebruikt, worden gebruikers onmiddellijk op uitnodiging toegevoegd, zodat ze overal worden weergegeven.

- De inwisselervaring in OneDrive/SharePoint Online ziet er anders uit dan de ervaring in Azure AD B2B-samenwerking. Nadat een gebruiker een uitnodiging heeft ingewisseld, lijken de ervaringen op elkaar.

- Azure AD B2B-samenwerking uitgenodigde gebruikers kunnen worden gekozen uit dialoogvensters voor delen van OneDrive/SharePoint Online. Gebruikers van OneDrive/SharePoint Online-uitnodigingen worden ook weergegeven in Azure AD nadat ze hun uitnodigingen hebben ingewisseld.

- De licentievereisten verschillen. Voor elke betaalde Azure AD-licentie u maximaal 5 gastgebruikers toegang geven tot uw betaalde Azure AD-functies. Zie [Azure AD B2B-licenties](https://docs.microsoft.com/azure/active-directory/b2b/licensing-guidance) en ['Wat is een externe gebruiker? voor](https://docs.microsoft.com/sharepoint/external-sharing-overview#what-happens-when-users-share)meer informatie over licenties in het overzicht voor extern delen van SharePoint Online .

Als u extern delen wilt beheren in OneDrive/SharePoint Online met Azure AD B2B-samenwerking, stelt u de instelling voor extern delen van OneDrive/SharePoint Online in **op Delen toestaan alleen met de externe gebruikers die al in de map van uw organisatie aanwezig zijn.** Gebruikers kunnen naar extern gedeelde sites gaan en kiezen uit externe bijdragers die de beheerder heeft toegevoegd. De beheerder kan de externe bijdragers toevoegen via de A-api's voor uitnodigingen voor B2B-samenwerkingsuitnodigingen.


![De instelling voor extern delen van OneDrive/SharePoint Online](media/o365-external-user/odsp-sharing-setting.png)

Nadat externe delen is ingeschakeld, is de mogelijkheid om te zoeken naar bestaande gastgebruikers in de SharePoint Online (SPO) mensenkiezer standaard uitgeschakeld om het gedrag van oudere gegevens te matchen.

U deze functie inschakelen met de instelling 'ShowPeoplePickerSuggestionsForGuestUsers' op tenant- en siteverzamelingsniveau. U de functie instellen met de cmdlets Set-SPOTenant en Set-SPOSite, waarmee leden alle bestaande gastgebruikers in de map kunnen doorzoeken. Wijzigingen in het tenantbereik hebben geen invloed op reeds ingerichte SPO-sites.

## <a name="next-steps"></a>Volgende stappen

* [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)
* [Een B2B-samenwerkingsgebruiker toevoegen aan een rol](add-guest-to-role.md)
* [B2B-uitnodigingen voor samenwerking delegeren](delegate-invitations.md)
* [Dynamische groepen en B2B-samenwerking](use-dynamic-groups.md)
* [Azure Active Directory B2B-samenwerking oplossen](troubleshoot.md)
