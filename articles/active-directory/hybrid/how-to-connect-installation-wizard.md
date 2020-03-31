---
title: De wizard Installatie van Azure AD Connect opnieuw uitvoeren | Microsoft Documenten
description: Hiermee wordt uitgelegd hoe de installatiewizard werkt wanneer u deze voor de tweede keer uitvoert.
keywords: Met de wizard Installatie van Azure AD Connect u onderhoudsinstellingen configureren wanneer u deze voor de tweede keer uitvoert
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: d800214e-e591-4297-b9b5-d0b1581cc36a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/17/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5440c54b01f62b3ad61b355f4c622a31910a65c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261331"
---
# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Synchronisatie van Azure AD Connect: de wizard Installatie een tweede keer uitvoeren
De eerste keer dat u de wizard Installatie van Azure AD Connect uitvoert, leidt u door hoe u uw installatie configureren. Als u de wizard installatie opnieuw uitvoert, biedt deze opties voor onderhoud.

>[!IMPORTANT]
>Houd er rekening mee dat u de installatiewizard niet uitvoeren terwijl er een synchronisatie bezig is.  Controleer of een synchronisatie niet wordt uitgevoerd voordat de wizard wordt gestart.

U de wizard installatie vinden in het startmenu met de naam **Azure AD Connect**.

![Het menu Start](./media/how-to-connect-installation-wizard/startmenu.png)

Wanneer u de wizard installatie start, ziet u een pagina met de volgende opties:

![Pagina met een lijst met extra taken](./media/how-to-connect-installation-wizard/additionaltasks.png)

Als u ADFS hebt geïnstalleerd met Azure AD Connect, hebt u nog meer opties. De extra opties die u voor ADFS hebt, zijn gedocumenteerd in [het ADFS-beheer.](how-to-connect-fed-management.md#manage-ad-fs)

Selecteer een van de taken en klik op **Volgende** om door te gaan.

> [!IMPORTANT]
> Terwijl u de installatiewizard hebt geopend, worden alle bewerkingen in de synchronisatieengine opgeschort. Zorg ervoor dat u de installatiewizard sluit zodra u de configuratiewijzigingen hebt voltooid.
>
>

## <a name="view-current-configuration"></a>Huidige configuratie weergeven
Met deze optie krijgt u een snelle weergave van uw momenteel geconfigureerde opties.

![Pagina met een lijst met alle opties en hun status](./media/how-to-connect-installation-wizard/viewconfig.png)

Klik **op Vorige** om terug te gaan. Als u **Afsluiten**selecteert, sluit u de wizard installatie.

## <a name="customize-synchronization-options"></a>Synchronisatieopties aanpassen
Deze optie wordt gebruikt om wijzigingen aan te brengen in de synchronisatieconfiguratie. U ziet een subset van opties uit het aangepaste configuratie-installatiepad. U ziet deze optie zelfs als u in eerste instantie express-installatie hebt gebruikt.

* [Voeg meer mappen toe.](how-to-connect-install-custom.md#connect-your-directories) Zie Een connector [verwijderen voor](how-to-connect-sync-service-manager-ui-connectors.md#delete)het verwijderen van een map.
* [Domein- en organisatie-eenheid-filtering wijzigen](how-to-connect-install-custom.md#domain-and-ou-filtering).
* Groepsfiltering verwijderen.
* [Wijzig optionele functies](how-to-connect-install-custom.md#optional-features).

De andere opties van de eerste installatie kunnen niet worden gewijzigd en zijn niet beschikbaar. Deze opties zijn:

* Wijzig het kenmerk dat u wilt gebruiken voor userPrincipalName en sourceAnchor.
* Wijzig de verbindingsmethode voor objecten uit verschillende foresten.
* Groepsfiltering inschakelen.

## <a name="refresh-directory-schema"></a>Mapschema vernieuwen
Deze optie wordt gebruikt als u het schema hebt gewijzigd in een van uw on-premises AD DS-forests. U hebt bijvoorbeeld Exchange geïnstalleerd of een upgrade uitgevoerd naar een Windows Server 2012-schema met apparaatobjecten. In dit geval moet u Azure AD Connect instrueren om het schema opnieuw te lezen vanuit AD DS en de cache bij te werken. Met deze actie worden ook de synchronisatieregels geregenereert. Als u bijvoorbeeld het Exchange-schema toevoegt, worden de synchronisatieregels voor Exchange aan de configuratie toegevoegd.

Wanneer u deze optie selecteert, worden alle mappen in uw configuratie weergegeven. U de standaardinstelling behouden en alle forests vernieuwen of een aantal van deze forests deselectie opheffen.

![Pagina met een lijst van alle mappen in de omgeving](./media/how-to-connect-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Faseringsmodus configureren
Met deze optie u de faseringsmodus op de server in- en uitschakelen. Meer informatie over de faseringsmodus en hoe deze wordt gebruikt, vindt u in [Operations.](how-to-connect-sync-staging-server.md)

De optie geeft aan of staging momenteel is ingeschakeld of uitgeschakeld:  
![Optie die ook de huidige status van de faseringsmodus weergeeft](./media/how-to-connect-installation-wizard/stagingmodecurrentstate.png)

Als u de status wilt wijzigen, schakelt u deze optie in en schakelt u het selectievakje in of uit.  
![Optie die ook de huidige status van de faseringsmodus weergeeft](./media/how-to-connect-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Aanmelding van gebruikers wijzigen
Met deze optie u de aanmeldingsmethode van de gebruiker wijzigen van en naar wachtwoordhashsynchronisatie, doorgeefverificatie of federatie. U niet wijzigen om niet te **configureren.**

Zie aanmelden voor gebruikers [voor](plan-connect-user-signin.md#changing-the-user-sign-in-method)meer informatie over deze optie.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het configuratiemodel dat wordt gebruikt door Azure AD Connect-synchronisatie in [Het declaratieve inrichting begrijpen.](concept-azure-ad-connect-sync-declarative-provisioning.md)

**Overzichtsonderwerpen**

* [Synchronisatie van Azure AD Connect: synchronisatie begrijpen en aanpassen](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)
