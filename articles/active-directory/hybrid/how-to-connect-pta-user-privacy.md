---
title: Verificatie van gebruikersprivacy en Azure Active Directory-pass-through | Microsoft Documenten
description: In dit artikel wordt ingaan op Azure Active Directory (Azure AD) Pass-through Authentication en GDPR compliance.
services: active-directory
keywords: Azure AD Connect Pass-through Authentication, GDPR, vereiste onderdelen voor Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0af1c42e7e2c163e7f9e7407d0236e35bfacf8e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76931013"
---
# <a name="user-privacy-and-azure-active-directory-pass-through-authentication"></a>Gebruikersprivacy en Pass-through-verificatie van Azure Active Directory


[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Overzicht

Azure AD Pass-through Authentication maakt het volgende logboektype, dat persoonlijke gegevens kan bevatten:

- Azure AD Connect tracelogbestanden.
- Verificatieagent traceerlogbestanden.
- Windows Event-logboekbestanden.

De privacy van gebruikers voor pass-through-verificatie op twee manieren verbeteren:

1.  Haal op verzoek gegevens voor een persoon en verwijder gegevens van die persoon uit de installaties.
2.  Zorg ervoor dat gegevens niet langer dan 48 uur worden bewaard.

We raden de tweede optie ten zeerste aan, omdat het gemakkelijker te implementeren en te onderhouden is. Hieronder volgen de instructies voor elk logboektype:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Azure AD Connect-traceringslogboekbestanden verwijderen

Controleer de inhoud van **de map %ProgramData%\AADConnect** en verwijder de inhoud van het tracelog **(trace-\*.log-bestanden)** van deze map binnen 48 uur na het installeren of upgraden van Azure AD Connect of het wijzigen van de pass-through-verificatieconfiguratie, omdat deze actie gegevens kan maken die onder de AVG vallen.

>[!IMPORTANT]
>Verwijder het bestand **PersistedState.xml** niet in deze map, omdat dit bestand wordt gebruikt om de status van de vorige installatie van Azure AD Connect te behouden en wordt gebruikt wanneer een upgrade-installatie is uitgevoerd. Dit bestand bevat nooit gegevens over een persoon en mag nooit worden verwijderd.

U deze traceerlogboekbestanden controleren en verwijderen met Windows Verkenner of u het volgende PowerShell-script gebruiken om de nodige acties uit te voeren:

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Sla het script op in een bestand met de ". PS1" extensie. Voer dit script uit als dat nodig is.

Zie [dit artikel](reference-connect-user-privacy.md)voor meer informatie over gerelateerde AZURE AD Connect GDPR-vereisten.

### <a name="delete-authentication-agent-event-logs"></a>Gebeurtenislogboeken van verificatieagent verwijderen

Dit product kan ook **Windows Event Logs**maken. Lees voor meer informatie [dit artikel.](https://msdn.microsoft.com/library/windows/desktop/aa385780(v=vs.85).aspx)

Als u logboeken wilt weergeven die betrekking hebben op de verificatieagent voor doorgeefing, opent u de **toepassing Logboeken** op de server en controleert u onder **Toepassings- en servicelogboeken\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

### <a name="delete-authentication-agent-trace-log-files"></a>Logboekbestanden van verificatieagent-tracering verwijderen

Controleer regelmatig de inhoud van **%ProgramData%\Microsoft\Azure AD Connect Authentication Agent\Trace** en verwijder de inhoud van deze map elke 48 uur. 

>[!IMPORTANT]
>Als de verificatieagentservice wordt uitgevoerd, u het huidige logboekbestand in de map niet verwijderen. Stop de service voordat u het opnieuw probeert. Om aanmeldingsfouten bij gebruikers te voorkomen, moet u pass-through-verificatie al hebben geconfigureerd voor [hoge beschikbaarheid.](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

U deze bestanden controleren en verwijderen met Windows Verkenner of u het volgende script gebruiken om de benodigde acties uit te voeren:

```
$Files = ((Get-childitem -Path "$env:programdata\microsoft\azure ad connect authentication agent\trace" -Recurse).VersionInfo).FileName 
 
Foreach ($file in $files) { 
    {Remove-Item -Path $File -Force} 
}
```

Volg de volgende stappen om dit script elke 48 uur uit te voeren:

1.  Sla het script op in een bestand met de ". PS1" extensie.
2.  Open **het Configuratiescherm** en klik op **Systeem en beveiliging**.
3.  Klik onder de kop **Systeembeheer** op Taken**plannen.**
4.  Klik in **Taakplanner**met de rechtermuisknop op "**Taakplanningsbibliotheek**" en klik op "**Basistaak maken...**".
5.  Voer de naam in voor de nieuwe taak en klik op **Volgende**.
6.  Selecteer '**Dagelijks'** voor de **taaktrigger** en klik op **Volgende**.
7.  Stel de herhaling in op twee dagen en klik op **Volgende**.
8.  Selecteer "**Start een programma"** als de actie en klik op **Volgende**.
9.  Typ "**PowerShell**" in het vak voor het programma/script en voer in het vak met het label "**Argumenten toevoegen (optioneel)** het volledige pad in naar het script dat u eerder hebt gemaakt en klik op **Volgende**.
10. In het volgende scherm ziet u een overzicht van de taak die u gaat maken. Controleer de waarden en klik op **Voltooien** om de taak te maken:
 
### <a name="note-about-domain-controller-logs"></a>Opmerking over logboeken van domeincontroller

Als controlelogboekregistratie is ingeschakeld, kan dit product beveiligingslogboeken genereren voor uw domeincontrollers. Lees dit [artikel](https://technet.microsoft.com/library/dd277403.aspx)voor meer informatie over het configureren van controlebeleid.

## <a name="next-steps"></a>Volgende stappen
* [Het Privacybeleid van Microsoft controleren op het vertrouwenscentrum](https://www.microsoft.com/trustcenter)
* [**Problemen oplossen**](tshoot-connect-pass-through-authentication.md) : meer informatie over het oplossen van veelvoorkomende problemen met de functie.
