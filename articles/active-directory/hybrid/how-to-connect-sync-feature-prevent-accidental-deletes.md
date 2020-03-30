---
title: 'Azure AD Connect-synchronisatie: voorkom per ongeluk verwijderen | Microsoft Documenten'
description: In dit onderwerp wordt de functie voor het per ongeluk verwijderen (voorkomen van onbedoelde verwijderingen) in Azure AD Connect beschreven.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b852cb4-2850-40a1-8280-8724081601f7
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48f3109b4c87e25444629ca25411894eab8a9d56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71827126"
---
# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Azure AD Connect-synchronisatie: onopzettelijke verwijderingen voorkomen
In dit onderwerp wordt de functie voor het per ongeluk verwijderen (voorkomen van onbedoelde verwijderingen) in Azure AD Connect beschreven.

Bij het installeren van Azure AD Connect wordt Onopzettelijke verwijderingen voorkomen standaard ingeschakeld en geconfigureerd zodat exporteren van meer dan 500 verwijderingen niet is toegestaan. Deze functie is ontworpen om u te beschermen tegen onbedoelde configuratiewijzigingen en wijzigingen aan uw on-premises directory die invloed zouden hebben op veel gebruikers en andere objecten.

## <a name="what-is-prevent-accidental-deletes"></a>Wat is voorkomen dat per ongeluk wordt verwijderd
Veelvoorkomende scenario's wanneer u veel verwijderingen ziet, zijn:

* Wijzigingen in [filteren](how-to-connect-sync-configure-filtering.md) waarbij een volledige [organisatie-eenheid](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) of [domein](how-to-connect-sync-configure-filtering.md#domain-based-filtering) niet is geselecteerd.
* alle objecten in een organisatie-eenheid worden verwijderd;
* de naam van een organisatie-eenheid is gewijzigd, zodat alle objecten in die eenheid als buiten het synchronisatiebereik worden beschouwd.

De standaardwaarde van 500 objecten kan `Enable-ADSyncExportDeletionThreshold`worden gewijzigd met PowerShell met powershell, dat deel uitmaakt van de AD Sync-module die is geïnstalleerd met Azure Active Directory Connect. U moet deze waarde zo configureren dat deze past bij de grootte van uw organisatie. Aangezien de synchronisatieplanner elke 30 minuten wordt uitgevoerd, is de waarde het aantal deletes dat binnen 30 minuten wordt gezien.

Als er te veel delete's zijn gestart om naar Azure AD te worden geëxporteerd, stopt de export en ontvangt u een e-mail als volgt:

![Voorkomen dat per ongeluk e-mail wordt verwijderd](./media/how-to-connect-sync-feature-prevent-accidental-deletes/email.png)

> *Hallo (technisch contact). Op (het tijdstip) heeft de synchronisatieservice Identiteit gedetecteerd dat het aantal verwijderingen de geconfigureerde verwijderingsdrempel voor (organisatienaam) heeft overschreden. In deze pagina Identiteitsynchronisatie is in totaal (aantal)objecten verzonden voor verwijdering. Hiermee is voldaan aan of overschreden de geconfigureerde verwijderingsdrempelwaarde van (getal) objecten. We hebben u nodig om te bevestigen dat deze verwijderingen moeten worden verwerkt voordat we verder gaan. Raadpleeg de verwijderingen voor het voorkomen van onbedoelde verwijderingen voor meer informatie over de fout in dit e-mailbericht.*
>
> 

U ook `stopped-deletion-threshold-exceeded` de status zien wanneer u in de **gebruikersinterface van synchronisatieservicebeheermanager** voor het exportprofiel kijkt.
![Voorkomen dat per ongeluk de gebruikersinterface van Sync Service Manager wordt verwijderd](./media/how-to-connect-sync-feature-prevent-accidental-deletes/syncservicemanager.png)

Als dit onverwacht was, dan onderzoeken en corrigerende maatregelen te nemen. Ga als volgt te werk om te zien welke objecten op het punt staan te worden verwijderd:

1. Synchronisatieservice **starten** vanuit het menu Start.
2. Ga naar **Connectors**.
3. Selecteer de connector met type **Azure Active Directory**.
4. Selecteer onder **Acties** aan de rechterkant **zoekconnectorruimte**.
5. Selecteer in de pop-up onder **Scope**de optie **Verbroken sinds** en kies een tijd in het verleden. Klik op **Zoeken**. Op deze pagina vindt u een overzicht van alle objecten die op het punt staan te worden verwijderd. Door op elk item te klikken, u aanvullende informatie over het object krijgen. U ook op **Kolominstelling** klikken om extra kenmerken toe te voegen die zichtbaar zijn in het raster.

![Zoekconnectorruimte](./media/how-to-connect-sync-feature-prevent-accidental-deletes/searchcs.png)

[!NOTE] Als u niet zeker weet of alle verwijderingen gewenst zijn en een veiligere route wilt afleggen. U de PowerShell-cmdlet gebruiken: `Enable-ADSyncExportDeletionThreshold` om een nieuwe drempel in te stellen in plaats van de drempelwaarde uit te schakelen die ongewenste verwijderingen mogelijk maakt. 

## <a name="if-all-deletes-are-desired"></a>Als alle verwijderingen gewenst zijn
Als alle verwijderingen gewenst zijn, ga dan als volgt te werk:

1. Voer de PowerShell-cmdlet `Get-ADSyncExportDeletionThreshold`uit om de huidige verwijderingsdrempel op te halen. Geef een Azure AD Global Administrator-account en -wachtwoord op. De standaardwaarde is 500.
2. Voer de PowerShell-cmdlet uit om deze beveiliging tijdelijk uit `Disable-ADSyncExportDeletionThreshold`te schakelen en deze deletes door te laten gaan: . Geef een Azure AD Global Administrator-account en -wachtwoord op.
   ![Referenties](./media/how-to-connect-sync-feature-prevent-accidental-deletes/credentials.png)
3. Als de Azure Active Directory Connector nog steeds is geselecteerd, selecteert u de actie **Uitvoeren** en selecteert u **Exporteren**.
4. Voer de PowerShell-cmdlet uit om `Enable-ADSyncExportDeletionThreshold -DeletionThreshold 500`de beveiliging opnieuw in te schakelen: . Vervang 500 door de waarde die u hebt opgemerkt bij het ophalen van de huidige verwijderingsdrempel. Geef een Azure AD Global Administrator-account en -wachtwoord op.

## <a name="next-steps"></a>Volgende stappen
**Overzichtsonderwerpen**

* [Synchronisatie van Azure AD Connect: synchronisatie begrijpen en aanpassen](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)
