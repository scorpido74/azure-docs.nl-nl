---
title: Wat is groepsgebaseerde licenties - Azure Active Directory | Microsoft Documenten
description: Meer informatie over azure Active Directory-groepslicenties, inclusief hoe het werkt en aanbevolen procedures.
services: active-directory
keywords: Azure AD-licenties
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9eec896e6cccaf58c83820161c54c4f10cfadadd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561558"
---
# <a name="what-is-group-based-licensing-in-azure-active-directory"></a>Wat is groepsgebaseerde licenties in Azure Active Directory?

Voor betaalde Microsoft-cloudservices, zoals Office 365, Enterprise Mobility + Security, Dynamics 365 en andere vergelijkbare producten, zijn licenties vereist. Deze licenties worden toegewezen aan elke gebruiker die toegang tot deze services nodig heeft. Om licenties te beheren gebruiken beheerders een van de beheerportals (Office of Azure) en PowerShell-cmdlets. Azure AD (Azure Active Directory) is de onderliggende infrastructuur die ondersteuning biedt voor identiteitsbeheer in alle Microsoft-cloudservices. In Azure AD wordt informatie opgeslagen over de statussen van licentietoewijzingen voor gebruikers.

Tot nu toe konden licenties alleen worden toegewezen op het niveau van de individuele gebruiker, wat grootschalig beheer moeilijk maakt. Als u bijvoorbeeld gebruikerslicenties wilt toevoegen of verwijderen op basis van wijzigingen in de organisatie, zoals wanneer gebruikers toetreden tot de organisatie of tot een afdeling of deze verlaten, moet een beheerder vaak een complex PowerShell-script schrijven. Met dit script worden afzonderlijke aanroepen naar de cloudservice gemaakt.

Azure AD bevat nu licenties op basis van groepen om met dergelijke uitdagingen om te gaan. U kunt een of meer productlicenties toewijzen aan een groep. De licenties worden in Azure AD toegewezen aan alle leden van de groep. Wanneer er nieuwe gebruikers lid worden van de groep, worden aan hen de juiste licenties toegewezen. Wanneer zij de groep weer verlaten, worden deze licenties verwijderd. Dit licentiebeheer elimineert de noodzaak voor het automatiseren van licentiebeheer via PowerShell om veranderingen in de organisatie en afdelingsstructuur per gebruiker weer te geven.

## <a name="licensing-requirements"></a>Licentievereisten
U moet een van de volgende licenties hebben om groepslicenties te kunnen gebruiken:

- Betaald of proefabonnement voor Azure AD Premium P1 en hoger

- Betaalde of proefversie van Office 365 Enterprise E3 of Office 365 A3 of Office 365 GCC G3 of Office 365 E3 voor GCCH of Office 365 E3 voor DOD en hoger

### <a name="required-number-of-licenses"></a>Vereist aantal licenties
Voor groepen die een licentie hebben toegewezen, moet u ook een licentie hebben voor elk uniek lid. Hoewel u niet elk lid van de groep een licentie hoeft toe te wijzen, moet u ten minste voldoende licenties hebben om alle leden op te nemen. Als u bijvoorbeeld 1.000 unieke leden hebt die deel uitmaken van gelicentieerde groepen in uw tenant, moet u ten minste 1.000 licenties hebben om aan de licentieovereenkomst te voldoen.

## <a name="features"></a>Functies

Dit zijn de belangrijkste functies van licenties op basis van groepen:

- Licenties kunnen worden toegewezen aan elke beveiligingsgroep in Azure AD. Beveiligingsgroepen kunnen worden gesynchroniseerd vanuit on-premises, met behulp van Azure AD Connect. U kunt beveiligingsgroepen ook rechtstreeks in Azure AD maken (ook wel groepen met het kenmerk Alleen-cloud genoemd), of automatisch via de functie voor dynamische Azure AD-groepen.

- Wanneer een productlicentie is toegewezen aan een groep, kan de beheerder een of meer serviceplannen in het product uitschakelen. Deze toewijzing wordt meestal uitgevoerd wanneer de organisatie nog niet klaar is om een service in een product te gebruiken. Zo kan de beheerder, bijvoorbeeld, Office 365 toewijzen aan een afdeling, maar de Yammer-service tijdelijk uitschakelen.

- Alle Microsoft-cloudservices waarvoor licenties op gebruikersniveau zijn vereist, worden ondersteund. Deze ondersteuning omvat alle Office 365-producten, Enterprise Mobility + Security en Dynamics 365.

- Licenties op basis van groepen zijn momenteel alleen beschikbaar via de [Azure-portal.](https://portal.azure.com) Als u voornamelijk andere beheerportalen gebruikt voor gebruikers- en groepsbeheer, zoals het [Microsoft 365-beheercentrum,](https://admin.microsoft.com)u dit blijven doen. U moet Azure Portal wel gebruiken om licenties op groepsniveau te beheren.

- Wijzigingen in licenties die het resultaat zijn van wijzigingen in groepslidmaatschappen, worden automatisch beheerd in Azure AD. Wijzigingen in licenties worden meestal binnen enkele minuten na een lidmaatschapswijziging van kracht.

- Een gebruiker kan lid zijn van meerdere groepen waarvoor licentiebeleid is opgesteld. Een gebruiker kan ook een aantal licenties hebben die rechtstreeks zijn toegewezen, buiten een groep. De resulterende gebruikersstatus is een combinatie van alle toegewezen product- en servicelicenties. Als een gebruiker dezelfde licentie uit meerdere bronnen krijgt toegewezen, wordt de licentie slechts één keer verbruikt.

- In sommige gevallen kunnen er geen licenties worden toegewezen aan een gebruiker. Bijvoorbeeld wanneer er niet voldoende licenties beschikbaar zijn in de tenant, of wanneer tegelijkertijd conflicterende services zijn toegewezen. Beheerders hebben toegang tot informatie over gebruikers voor wie de groepslicenties niet volledig zijn verwerkt in Azure AD. Ze kunnen vervolgens een herstelbewerking uitvoeren op basis van deze informatie.

## <a name="your-feedback-is-welcome"></a>We stellen uw feedback op prijs!

Als u feedback of functieverzoeken hebt, deelt u deze met ons via [het Azure AD-beheerforum.](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=162510)

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over scenario’s voor licentiebeheer via licenties op basis van groepen, raadpleegt u:

* [Licenties toewijzen aan een groep in Azure Active Directory](../users-groups-roles/licensing-groups-assign.md)
* [Licentieproblemen voor een groep vaststellen en oplossen in Azure Active Directory](../users-groups-roles/licensing-groups-resolve-problems.md)
* [Gebruikers met een afzonderlijke licentie migreren naar licenties op basis van groepen in Azure Active Directory](../users-groups-roles/licensing-groups-migrate-users.md)
* [Gebruikers migreren tussen productlicenties met groepslicenties in Azure Active Directory](../users-groups-roles/licensing-groups-change-licenses.md)
* [Aanvullende scenario’s voor Azure Active Directory-licenties op basis van groepen](../users-groups-roles/licensing-group-advanced.md)
* [PowerShell-voorbeelden voor groepslicenties in Azure Active Directory](../users-groups-roles/licensing-ps-examples.md)
