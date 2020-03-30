---
title: Azure AD-rollen beheren in Privileged Identity Management (PIM) | Microsoft Documenten
description: Azure AD-rollen beheren voor toewijzing Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5d63577cbdbdf18cb17618bdb5c9e3b5de0e44a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245978"
---
# <a name="management-capabilities-for-azure-ad-roles-in-privileged-identity-management"></a>Beheermogelijkheden voor Azure AD-rollen in Privileged Identity Management

De beheerervaring voor Azure AD-rollen in Privileged Identity Management is bijgewerkt om te verenigen hoe Azure AD-rollen en Azure-resourcerollen worden beheerd. Voorheen had Privileged Identity Management voor Azure-resourcerollen een aantal belangrijke functies die niet beschikbaar waren voor Azure AD-rollen.

Nu de update momenteel wordt uitgerold, voegen we de twee samen in één beheerervaring en krijgt u dezelfde functionaliteit voor Azure AD-rollen als voor Azure-bronrollen. In dit artikel wordt u geïnformeerd over de bijgewerkte functies en eventuele vereisten.


## <a name="time-bound-assignments"></a>Tijdgebonden toewijzingen

Voorheen in Privileged Identity Management voor Azure AD-rollen, was u bekend met roltoewijzingen met twee mogelijke statussen - *in aanmerking komend* en *permanent*. Nu u een begin- en eindtijd instellen voor elk type toewijzing. Deze toevoeging geeft je vier mogelijke staten waarin je een opdracht plaatsen:

- Permanent in aanmerking komen
- Permanent actief
- In aanmerking komen, met opgegeven begin-/einddatums voor toewijzing
- Actief, met opgegeven begin-/einddatums voor toewijzing

In veel gevallen u zelfs als u niet wilt dat gebruikers elke keer in aanmerking komende toewijzing en activeren van rollen hebben, uw Azure AD-organisatie nog steeds beschermen door een vervaldatum in te stellen voor toewijzingen. Als u bijvoorbeeld tijdelijke gebruikers hebt die in aanmerking komen, u overwegen een vervaldatum in te stellen om deze automatisch uit de roltoewijzing te verwijderen wanneer hun werk is voltooid.

## <a name="new-role-settings"></a>Nieuwe rolinstellingen

We voegen ook nieuwe instellingen toe voor Azure AD-rollen. Voorheen kon u alleen activeringsinstellingen per rol configureren. Dat wil zeggen dat activeringsinstellingen zoals vereisten voor meervoudige verificatie en vereisten voor incident/request-tickets zijn toegepast op alle gebruikers die in aanmerking komen voor een bepaalde rol. U nu configureren of een individuele gebruiker multi-factor authenticatie moet uitvoeren voordat hij een rol kan activeren. U ook geavanceerde controle hebben over uw e-mails met betrekking tot het beheer van privileged identity management met betrekking tot specifieke rollen.

## <a name="extend-and-renew-assignments"></a>Opdrachten verlengen en vernieuwen

Zodra u erachter te komen time-gebonden opdracht, de eerste vraag die je zou kunnen vragen is wat er gebeurt als een rol is verstreken? In deze nieuwe versie bieden we twee opties voor dit scenario:

- Uitbreiden : wanneer een roltoewijzing bijna afloopt, kan de gebruiker Privileged Identity Management gebruiken om een extensie voor die roltoewijzing aan te vragen
- Vernieuwen : wanneer een roltoewijzing is verlopen, kan de gebruiker Privileged Identity Management gebruiken om een verlenging aan te vragen voor die roltoewijzing

Beide door de gebruiker geïnitieerde acties vereisen een goedkeuring van een globale beheerder of bevoorrechte rolbeheerder. Beheerders hoeven zich niet langer bezig te houden met het beheren van deze expirato's. Ze hoeven alleen maar te wachten op de verlenging of verlenging aanvragen en ze goed te keuren als het verzoek geldig is.

## <a name="api-changes"></a>API-wijzigingen

Wanneer klanten de bijgewerkte versie hebben uitgerold naar hun Azure AD-organisatie, werkt de bestaande grafiek-API niet meer. U moet overschakelen naar de [Graph API voor Azure-bronrollen](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-resources?view=graph-rest-beta). Als u Azure AD-rollen `/azureResources` wilt `/aadroles` beheren met die API, `resourceId`wisselt u in de handtekening en gebruikt u de Directory-id voor de .

We hebben ons best gedaan om alle klanten die de vorige API gebruiken te bereiken om hen van tevoren op de hoogte te stellen van deze wijziging. Als uw Azure AD-organisatie is verplaatst naar de nieuwe versie en u nog pim_preview@microsoft.comsteeds afhankelijk bent van de oude API, neem dan contact op met het team op .

## <a name="powershell-change"></a>PowerShell-wijziging

Voor klanten die de PowerShell-module Privileged Identity Management voor Azure AD-rollen gebruiken, stopt de PowerShell met de update. In plaats van de vorige cmdlets moet u de cmdlets Privileged Identity Management gebruiken in de Azure AD Preview PowerShell-module. Installeer de Azure AD PowerShell-module in de [PowerShell-galerie](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). U nu [de documentatie en voorbeelden voor PIM-bewerkingen lezen in deze PowerShell-module.](powershell-for-azure-ad-roles.md)

## <a name="next-steps"></a>Volgende stappen

- [Een aangepaste Azure AD-rol toewijzen](azure-ad-custom-roles-assign.md)
- [Een aangepaste azure-roltoewijzing verwijderen of bijwerken](azure-ad-custom-roles-update-remove.md)
- [Een aangepaste azure-functietoewijzing configureren](azure-ad-custom-roles-configure.md)
- [Roldefinities in Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
