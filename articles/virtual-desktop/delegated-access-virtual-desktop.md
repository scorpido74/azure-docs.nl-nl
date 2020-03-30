---
title: Gedelegeerde toegang in Windows Virtual Desktop - Azure
description: Beheerdersmogelijkheden delegeren op een Windows Virtual Desktop-implementatie, inclusief voorbeelden.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3e27550ecc5b42c2bf0d947690da09e13d88ea4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128031"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Gedelegeerde toegang in Windows Virtual Desktop

Windows Virtual Desktop heeft een gedelegeerd toegangsmodel waarmee u de hoeveelheid toegang definiëren die een bepaalde gebruiker mag hebben door deze een rol toe te kent. Een roltoewijzing bestaat uit drie componenten: beveiligingsprincipal, roldefinitie en bereik. Het gedelegeerde toegangsmodel voor Windows Virtual Desktop is gebaseerd op het Azure RBAC-model. Zie [het overzicht van azure-functiegebaseerde toegangscontrole](../role-based-access-control/built-in-roles.md)voor meer informatie over specifieke roltoewijzingen en hun componenten.

De gedelegeerde toegang voor Windows Virtual Desktop ondersteunt de volgende waarden voor elk element van de roltoewijzing:

* Beveiligings-principal
    * Gebruikers
    * Service-principals
* Roldefinitie ophalen
    * Ingebouwde rollen
* Bereik
    * Tenantgroepen
    * Tenants
    * Zwembaden hosten
    * App-groepen

## <a name="built-in-roles"></a>Ingebouwde rollen

Gedelegeerde toegang in Windows Virtual Desktop heeft verschillende ingebouwde roldefinities die u aan gebruikers en serviceprincipals toewijzen.

* Een RDS-eigenaar kan alles beheren, inclusief toegang tot bronnen.
* Een RDS-bijdrager kan alles beheren behalve toegang tot bronnen.
* Een RDS Reader kan alles bekijken, maar kan geen wijzigingen aanbrengen.
* Een RDS-operator kan diagnostische activiteiten bekijken.

## <a name="powershell-cmdlets-for-role-assignments"></a>PowerShell-cmdlets voor roltoewijzingen

U de volgende cmdlets uitvoeren om roltoewijzingen te maken, weer te geven en te verwijderen:

* **Get-RdsRoleAssignment** geeft een lijst met roltoewijzingen weer.
* **New-RdsRoleAssignment** maakt een nieuwe roltoewijzing.
* **Remove-RdsRoleAssignment** verwijdert roltoewijzingen.

### <a name="accepted-parameters"></a>Geaccepteerde parameters

U de drie basiscmdlets wijzigen met de volgende parameters:

* **AadTenantId:** geeft de Azure Active Directory-tenant-id op waarvan de serviceprincipal lid is.
* **AppGroupName**: naam van de app-groep Extern bureaublad.
* **Diagnostiek**: geeft de diagnostische scope aan. (Moet worden gekoppeld aan de **parameters Infrastructuur** of **Tenant.)**
* **HostPoolName:** naam van de extern bureaublad-hostgroep.
* **Infrastructuur**: geeft de infrastructuuromvang aan.
* **RoleDefinitionName:** naam van de functiegebaseerde toegangscontrolerol op basis van extern bureaublad-services die is toegewezen aan de gebruiker, groep of app. (Bijvoorbeeld de eigenaar van Extern bureaublad-services, extern bureaublad-serviceslezer, enzovoort.)
* **ServerPrincipleName:** naam van de Azure Active Directory-toepassing.
* **SignInName:** het e-mailadres of de gebruikersnaam van de gebruiker.
* **TenantName**: naam van de extern bureaublad-tenant.

## <a name="next-steps"></a>Volgende stappen

Zie de [PowerShell-referentie](/powershell/windows-virtual-desktop/overview)voor een completere lijst met PowerShell-cmdlets die elke rol kan gebruiken.

Zie [Windows Virtual Desktop-omgeving](environment-setup.md)voor richtlijnen voor het instellen van een Windows Virtual Desktop-omgeving.
