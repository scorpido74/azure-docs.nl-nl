---
title: 'Azure AD Connect: wat is de ADConnectivityTool PowerShell-module | Microsoft Documenten'
description: Dit document introduceert de nieuwe ADConnectivity PowerShell-module en hoe deze kan worden gebruikt om problemen op te lossen.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 4/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd5340cd8c802df4ffbe0207b5401d2fee4e207e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "64571126"
---
# <a name="troubleshoot-azure-ad-connectivity-with-the-adconnectivitytool-powershell-module"></a>Azure AD-connectiviteit oplossen met de ADConnectivityTool PowerShell-module

De ADConnectivity-tool is een PowerShell-module die in een van de volgende opties wordt gebruikt:

- Tijdens de installatie wanneer een probleem met de netwerkverbinding de succesvolle validatie van de Active Directory-referenties verhindert, wordt de gebruiker in de wizard opgegeven.
- Plaats de installatie door een gebruiker die de functies van een PowerShell-sessie aanroept.

Het hulpprogramma bevindt zich in: **C:\Program Files\Microsoft Azure Active Directory Connect\Tools\ ADConnectivityTool.psm1** 

## <a name="adconnectivitytool-during-installation"></a>ADConnectivityTool tijdens de installatie

Op de pagina **Uw mappen verbinden** in de wizard Azure AD Connect gebruikt adConnectivityTool automatisch een van de functies om te bepalen wat er aan de hand is.  Een van de volgende opties kan worden beschouwd als netwerkproblemen:

- De naam van het forest dat de gebruiker heeft verstrekt, is verkeerd getypt of er staat niet dat Forest niet bestaat 
- UDP-poort 389 is gesloten in de domeincontrollers die zijn gekoppeld aan het forest dat de gebruiker heeft verstrekt
- De referenties die zijn opgegeven in het venster 'AD-forestaccount' hebben geen bevoegdheden om de domeincontrollers op te halen die zijn gekoppeld aan het doelforest
- Een van de TCP-poorten 53, 88 of 389 wordt gesloten in de domeincontrollers die zijn gekoppeld aan het forest dat de gebruiker heeft verstrekt 
- Zowel UDP 389 als een TCP-poort (of poorten) zijn gesloten
- DNS kan niet worden opgelost voor het meegeleverde forest en de bijbehorende domeincontrollers

Wanneer een van deze problemen wordt gevonden, wordt een gerelateerd foutbericht weergegeven in de wizard AADConnect:


![Fout](media/how-to-connect-adconnectivitytools/error1.png)

Wanneer we bijvoorbeeld een map proberen toe te voegen aan het scherm **Connect your directories,** moet Azure AD Connect dit verifiëren en verwacht het te kunnen communiceren met een domeincontroller via poort 389.  Als dit niet het niet kan, zien we de fout die wordt weergegeven in de bovenstaande schermafbeelding.  

Wat er achter de schermen gebeurt, is dat `Start-NetworkConnectivityDiagnosisTools` Azure AD Connect de functie aanroept.  Deze functie wordt aangeroepen wanneer de validatie van referenties mislukt als gevolg van een probleem met de netwerkverbinding.

Ten slotte wordt een gedetailleerd logboekbestand gegenereerd wanneer het hulpprogramma vanuit de wizard wordt aangeroepen. Het logboek bevindt zich in **C:\ProgramData\AADConnect\ADConnectivityTool-datum\<>-time\<>.log**

## <a name="adconnectivitytools-post-installation"></a>ADConnectivityTools na installatie
Nadat Azure AD Connect is geïnstalleerd, kan een van de functies in de ADConnectivityTools PowerShell-module worden gebruikt.  

U vindt referentie-informatie over de functies in de [ADConnectivityTools-referentie](reference-connect-adconnectivitytools.md)

### <a name="start-connectivityvalidation"></a>Validatie van startconnectiviteit

We gaan deze functie oproepen omdat deze **alleen** handmatig kan worden aangeroepen als de ADConnectivityTool.psm1 is geïmporteerd in PowerShell. 

Met deze functie wordt dezelfde logica uitgevoerd als de wizard Azure AD Connect uitvoert om de opgegeven AD-referenties te valideren.  Maar het biedt een veel meer uitgebreide uitleg over het probleem en een voorgestelde oplossing. 

De connectiviteitsvalidatie bestaat uit de volgende stappen:
-   Domein FQDN-object (volledig gekwalificeerde domeinnaam)
-   Valideren dat deze referenties behoren tot de groep Enterprise Administrators als de gebruiker 'Nieuw AD-account maken' heeft geselecteerd.
-   Forest FQDN-object
-   Controleren of ten minste één domein dat is gekoppeld aan het eerder verkregen Forest FQDN-object bereikbaar is
-   Controleer of het functionele niveau van het forest Windows Server 2003 of hoger is.

De gebruiker kan een map toevoegen als al deze acties zijn uitgevoerd.

Als de gebruiker deze functie uitvoert nadat een probleem is opgelost (of als er helemaal geen probleem is), geeft de uitvoer aan dat de gebruiker terug gaat naar de wizard Azure AD Connect en probeert de referenties opnieuw in te voegen.



## <a name="next-steps"></a>Volgende stappen
- [Azure AD Connect: accounts en machtigingen](reference-connect-accounts-permissions.md)
- [Express-installatie](how-to-connect-install-express.md)
- [Aangepaste installatie](how-to-connect-install-custom.md)
- [ADConnectivityTools-verwijzing](reference-connect-adconnectivitytools.md)

