---
title: Azure AD Connect en gebruikersprivacy | Microsoft Documenten
description: In dit document wordt beschreven hoe u GDPR-compatibiliteit verkrijgen met Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f5d3125b7b77e8ce7a943f640c44615049ab160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60455761"
---
# <a name="user-privacy-and-azure-ad-connect"></a>Gebruikersprivacy en Azure AD Connect 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>In dit artikel wordt ingehouden op Azure AD Connect en de privacy van gebruikers.  Zie het artikel [hier](reference-connect-health-user-privacy.md)voor informatie over Azure AD Connect Health en gebruikersprivacy.

De privacy van gebruikers voor Azure AD Connect-installaties op twee manieren verbeteren:

1.  Haal op verzoek gegevens voor een persoon en verwijder gegevens van die persoon uit de installaties
2.  Zorg ervoor dat gegevens niet langer dan 48 uur worden bewaard.

Het Azure AD Connect-team raadt de tweede optie aan, omdat het veel eenvoudiger is om te implementeren en te onderhouden.

Een Azure AD Connect-synchronisatieserver slaat de volgende privacygegevens van gebruikers op:
1.  Gegevens over een persoon in de **Azure AD Connect-database**
2.  Gegevens in de **logboekbestanden van Windows Event** die informatie over een persoon kunnen bevatten
3.  Gegevens in de **installatielogboekbestanden van Azure AD Connect** die mogelijk over een persoon kunnen bevatten

Azure AD Connect-klanten moeten de volgende richtlijnen gebruiken bij het verwijderen van gebruikersgegevens:
1.  Verwijder de inhoud van de map die regelmatig de installatielogboekbestanden van Azure AD Connect bevat , ten minste elke 48 uur
2.  Dit product kan ook eventlogs maken.  Zie de [documentatie hier](https://msdn.microsoft.com/library/windows/desktop/aa385780.aspx)voor meer informatie over logboeken voor logboeken.

Gegevens over een persoon worden automatisch verwijderd uit de Azure AD Connect-database wanneer de gegevens van die persoon worden verwijderd uit het bronsysteem waar deze vandaan komt. Er is geen specifieke actie van beheerders vereist om GDPR-compliant te zijn.  Het vereist echter wel dat de Azure AD Connect-gegevens ten minste om de twee dagen worden gesynchroniseerd met uw gegevensbron.

## <a name="delete-the-azure-ad-connect-installation-log-file-folder-contents"></a>De inhoud van de inhoud van de installatielogboekmap van Azure AD Connect verwijderen
Controleer en verwijder regelmatig de inhoud van de map **c:\programdata\aadconnect** – met uitzondering van het bestand **PersistedState.Xml.** Dit bestand behoudt de status van de vorige installatie van Azure A Connect en wordt gebruikt wanneer een upgrade-installatie wordt uitgevoerd. Dit bestand bevat geen gegevens over een persoon en mag niet worden verwijderd.

>[!IMPORTANT]
>Verwijder het bestand PersistedState.xml niet.  Dit bestand bevat geen gebruikersgegevens en behoudt de status van de vorige installatie.

U deze bestanden controleren en verwijderen met Windows Verkenner of u een script als volgt gebruiken om de nodige acties uit te voeren:


```
$Files = ((Get-childitem -Path "$env:programdata\aadconnect" -Recurse).VersionInfo).FileName
Foreach ($file in $files) {
If ($File.ToUpper() -ne "$env:programdata\aadconnect\PERSISTEDSTATE.XML".toupper()) # Do not delete this file
    {Remove-Item -Path $File -Force}
    } 
```

### <a name="schedule-this-script-to-run-every-48-hours"></a>Plan dit script om elke 48 uur uit te voeren
Gebruik de volgende stappen om het script elke 48 uur uit te voeren.

1.  Sla het script op in een bestand met de extensie **&#46;PS1,** open vervolgens het Configuratiescherm en klik op **Systemen en Beveiliging**.
    ![Systeem](./media/reference-connect-user-privacy/gdpr2.png)

2.  Klik onder de kop Systeembeheer op **Taken plannen**.
    ![Taak](./media/reference-connect-user-privacy/gdpr3.png)
3.  Klik in Taakplanner met de rechtermuisknop op **Bibliotheek Taakplanning** en klik op **Basistaak maken...**
4.  Voer de naam in voor de nieuwe taak en klik op **Volgende**.
5.  Selecteer **Dagelijks** voor de taaktrigger en klik op **Volgende**.
6.  Stel de herhaling in op **2 dagen** en klik op **Volgende**.
7.  Selecteer **Een programma starten** als de actie en klik op **Volgende**.
8.  Typ **PowerShell** in het vak voor het programma/script en voer in het vak met het label **Argumenten toevoegen (optioneel)** het volledige pad in naar het script dat u eerder hebt gemaakt en klik vervolgens op **Volgende**.
9.  In het volgende scherm ziet u een overzicht van de taak die u gaat maken. Controleer de waarden en klik op **Voltooien** om de taak te maken.



## <a name="next-steps"></a>Volgende stappen
* [Het Privacybeleid van Microsoft controleren op het vertrouwenscentrum](https://www.microsoft.com/trustcenter)
* [Status en gebruikersprivacy van Azure AD Connect](reference-connect-health-user-privacy.md)
