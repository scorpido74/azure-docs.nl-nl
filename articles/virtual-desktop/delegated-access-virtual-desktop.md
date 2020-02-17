---
title: Gedelegeerde toegang in het virtuele bureau blad van Windows-Azure
description: Beheer mogelijkheden delegeren voor een implementatie van Windows virtueel bureau blad, met inbegrip van voor beelden.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 849a3b1eb1a4648c2eaae7dd564f14b9c511fdcf
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367358"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Gedelegeerde toegang in Windows Virtual Desktop

Het virtuele bureau blad van Windows beschikt over een gedelegeerd toegangs model waarmee u de hoeveelheid toegang kunt definiëren die een bepaalde gebruiker mag hebben door hen een rol toe te wijzen. Een roltoewijzing heeft drie onderdelen: beveiligingsprincipal, roldefinitie en bereik. Het Windows-model voor gedelegeerde toegang voor virtueel bureau blad is gebaseerd op het Azure RBAC-model. Voor meer informatie over specifieke roltoewijzingen en de bijbehorende onderdelen raadpleegt u [het overzicht van toegangs beheer op basis van rollen in azure](../role-based-access-control/built-in-roles.md).

Het Windows-bureau blad gedelegeerde toegang ondersteunt de volgende waarden voor elk element van de roltoewijzing:

* Beveiligings-principal
    * Gebruikers
    * Service-principals
* Roldefinitie
    * Ingebouwde rollen
* Bereik
    * Tenant groepen
    * Tenants
    * Hostgroepen
    * App-groepen

## <a name="built-in-roles"></a>Ingebouwde rollen

Gedelegeerde toegang in het virtuele bureau blad van Windows heeft verschillende ingebouwde roldefinities die u kunt toewijzen aan gebruikers en service-principals.

* Een RDS-eigenaar kan alles beheren, inclusief toegang tot resources.
* Een RDS-Inzender kan alles beheren, maar ook toegang tot resources.
* Een RDS-lezer kan alles weer geven, maar kan geen wijzigingen aanbrengen.
* Een RDS-operator kan diagnostische activiteiten weer geven.

## <a name="powershell-cmdlets-for-role-assignments"></a>Power shell-cmdlets voor roltoewijzingen

U kunt de volgende cmdlets uitvoeren om roltoewijzingen te maken, weer te geven en te verwijderen:

* **Get-RdsRoleAssignment** toont een lijst met roltoewijzingen.
* **New-RdsRoleAssignment** maakt een nieuwe roltoewijzing.
* **Met Remove-RdsRoleAssignment** worden roltoewijzingen verwijderd.

### <a name="accepted-parameters"></a>Geaccepteerde para meters

U kunt de Basic drie cmdlets met de volgende para meters wijzigen:

* **AadTenantId**: Hiermee geeft u de Azure Active Directory Tenant-id op waarvan de service-principal lid is.
* **AppGroupName**: naam van de app-groep voor extern bureaublad.
* **Diagnostische gegevens**: Hiermee wordt het bereik voor diagnostische gegevens aangegeven. (Moet worden gekoppeld aan ofwel de **infra structuur** -of **Tenant** parameters zijn.)
* **HostPoolName**: de naam van de hostgroep extern bureaublad.
* **Infra structuur**: geeft het bereik van de infra structuur aan.
* **RoleDefinitionName**: extern bureaublad-services de naam van de op rollen gebaseerde toegangs beheer functie die is toegewezen aan de gebruiker, groep of app. (Bijvoorbeeld Extern bureaublad-services eigenaar, Extern bureaublad-services lezer, enzovoort.)
* **ServerPrincipleName**: naam van de toepassing Azure Active Directory.
* **SignInName**: het e-mail adres of de User Principal name van de gebruiker.
* **Tenantnaam**: naam van de Extern bureaublad Tenant.

## <a name="next-steps"></a>Volgende stappen

Zie de [Power shell-referentie](/powershell/windows-virtual-desktop/overview)voor een volledige lijst met Power shell-cmdlets die elke rol kan gebruiken.

Zie voor richt lijnen voor het instellen van een virtuele Windows-desktop omgeving [Windows Virtual Desktop Environment](environment-setup.md)(Engelstalig).
