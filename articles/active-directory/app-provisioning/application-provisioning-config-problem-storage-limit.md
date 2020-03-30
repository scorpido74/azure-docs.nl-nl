---
title: Probleem met het opslaan van beheerdersreferenties bij het configureren van azure AD-galerie-app
description: Algemene problemen oplossen bij het configureren van gebruikersinrichting voor een toepassing die al in de Azure AD Application Gallery is vermeld
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ff80224037001e41daf49cd6fc21439b2cc5cff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522863"
---
# <a name="problem-saving-administrator-credentials-while-configuring-user-provisioning-to-an-azure-active-directory-gallery-application"></a>Probleem met het opslaan van beheerdersreferenties tijdens het configureren van gebruikersvoorziening in een Azure Active Directory Gallery-toepassing 

Wanneer u de Azure-portal gebruikt om [automatische gebruikersinrichting](user-provisioning.md) voor een bedrijfstoepassing te configureren, u een situatie tegenkomen waarin:

* De **beheerdersreferenties** die voor de toepassing zijn ingevoerd, zijn geldig en de knop **Verbinding testen** werkt. De referenties kunnen echter niet worden opgeslagen en de Azure-portal retourneert een algemeen foutbericht.

Als saml-gebaseerde enkele aanmelding ook is geconfigureerd voor dezelfde toepassing, is de meest waarschijnlijke oorzaak van de fout dat de interne opslaglimiet per toepassing voor certificaten en referenties van Azure AD voor certificaten en referenties is overschreden.

Azure AD heeft momenteel een maximale opslagcapaciteit van 1024 bytes voor alle certificaten, geheime tokens, referenties en gerelateerde configuratiegegevens die zijn gekoppeld aan één exemplaar van een toepassing (ook wel serviceprincipalrecord in Azure AD genoemd).

Wanneer SAML-gebaseerde enkele aanmelding is geconfigureerd, wordt het certificaat dat wordt gebruikt om de SAML-tokens te ondertekenen hier opgeslagen en verbruikt het vaak meer dan 50% procent van de ruimte.

Alle geheime tokens, URI's, e-mailadressen van meldingen, gebruikersnamen en wachtwoorden die worden ingevoerd tijdens het instellen van gebruikersinrichting, kunnen ertoe leiden dat de opslaglimiet wordt overschreden.

## <a name="how-to-work-around-this-issue"></a>Hoe dit probleem te omzeilen 

Er zijn twee mogelijke manieren om dit probleem vandaag nog te omzeilen:

1. **Gebruik twee galerijtoepassingsinstanties, één voor eenmalige aanmelding en één voor gebruikersinrichting** - Als u de galerietoepassing [LinkedIn Elevate](../saas-apps/linkedinelevate-tutorial.md) als voorbeeld neemt, u LinkedIn Elevate vanuit de galerie toevoegen en configureren voor eenmalige aanmelding. Voeg voor het inrichten een ander exemplaar van LinkedIn Elevate toe vanuit de Azure AD-app-galerie en noem deze de naam 'LinkedIn Elevate (Provisioning).' Configureer voor deze tweede instantie [de inrichting,](../saas-apps/linkedinelevate-provisioning-tutorial.md)maar niet eenmalige aanmelding. Bij het gebruik van deze tijdelijke oplossing moeten dezelfde gebruikers en groepen aan beide toepassingen worden [toegewezen.](../manage-apps/assign-user-or-group-access-portal.md) 

2. **Verklein de hoeveelheid opgeslagen configuratiegegevens** : Alle gegevens die zijn ingevoerd in het gedeelte [Beheerdersreferenties](user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) van het tabblad Inprovisioning, worden opgeslagen op dezelfde plaats als het SAML-certificaat. Hoewel het mogelijk niet mogelijk is om de lengte van al deze gegevens te verminderen, kunnen sommige optionele configuratievelden zoals de **meldingse-e-mail** worden verwijderd.

## <a name="next-steps"></a>Volgende stappen
[Gebruikersinrichting en de-provisioning configureren voor SaaS-toepassingen](user-provisioning.md)
