---
title: Problemen met grafische prestaties extern bureaublad diagnosticeren - Azure
description: In dit artikel wordt beschreven hoe u RemoteFX-grafische tellers gebruiken in externe bureaubladprotocolsessies om prestatieproblemen met afbeeldingen in Windows Virtual Desktop te diagnosticeren.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 05/23/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 84cee86dbddff77f6142925eec01889cf793a466
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127548"
---
# <a name="diagnose-graphics-performance-issues-in-remote-desktop"></a>Problemen met grafische prestaties in Extern bureaublad diagnosticeren

Om kwaliteitsproblemen met uw externe sessies te diagnosticeren, zijn er tellers beschikbaar onder het gedeelte RemoteFX Graphics van Performance Monitor. Met dit artikel u problemen met de grafische prestaties lokaliseren en oplossen tijdens RDP-sessies (Remote Desktop Protocol) met behulp van deze tellers.

## <a name="find-your-remote-session-name"></a>Uw externe sessienaam zoeken

U hebt uw externe sessienaam nodig om de grafische prestatiemeteritems te identificeren. Volg de instructies in deze sectie om uw exemplaar van elke teller te identificeren.

1. Open de opdrachtprompt van Windows vanuit uw externe sessie.
2. Voer de **opdracht qwinsta** uit en vind uw sessienaam.
    - Als uw sessie wordt gehost in een virtuele machine met meerdere sessies (VM): uw instantie van elke teller wordt achtergezet door hetzelfde nummer dat uw sessienaam achterwerkt, zoals 'rdp-tcp 37'.
    - Als uw sessie wordt gehost in een VM die virtuele grafische verwerkingseenheden (vGPU) ondersteunt: wordt uw exemplaar van elke teller opgeslagen op de server in plaats van in uw VM. Uw tellerexemplaren bevatten de VM-naam in plaats van het nummer in de sessienaam, zoals 'Win8 Enterprise VM'.

>[!NOTE]
> Hoewel tellers RemoteFX in hun naam hebben, bevatten ze ook remote desktop-afbeeldingen in vGPU-scenario's.

## <a name="access-performance-counters"></a>Prestatiemeteritems openen

Nadat u de naam van uw externe sessie hebt bepaald, volgt u deze instructies om de remoteFX Graphics-prestatiemeteritems voor uw externe sessie te verzamelen.

1. Selecteer **Start** > **Prestatiemonitor Beheergereedschappen** > **starten**.
2. Vouw in het dialoogvenster **Prestatiemeter** **controleprogramma's**uit, selecteer **Prestatiemeter**en selecteer **Vervolgens Toevoegen**.
3. Vouw in het dialoogvenster **Tellers toevoegen** in de lijst **Beschikbare tellers** de sectie uit voor RemoteFX Graphics.
4. Selecteer de tellers die moeten worden gecontroleerd.
5. Selecteer **in** de lijst Instanties van geselecteerde objecten de specifieke instanties die moeten worden gecontroleerd voor de geselecteerde tellers en selecteer **Vervolgens Toevoegen**. Als u alle beschikbare tellerinstanties wilt selecteren, selecteert u **Alle instanties**.
6. Nadat u de tellers hebt toegevoegd, selecteert u **OK**.

De geselecteerde prestatiemeteritems worden weergegeven op het scherm Prestatiemonitor.

>[!NOTE]
>Elke actieve sessie op een host heeft zijn eigen exemplaar van elke prestatiemeter.

## <a name="diagnose-issues"></a>Problemen diagnosticeren

Grafische gerelateerde prestatieproblemen vallen over het algemeen in vier categorieën:

- Lage framesnelheid
- Willekeurige kraampjes
- Hoge invoerlatentie
- Slechte framekwaliteit

### <a name="addressing-low-frame-rate-random-stalls-and-high-input-latency"></a>Aanpak van lage framesnelheid, willekeurige kraampjes en hoge invoerlatentie

Controleer eerst de uitvoerframes/secondeteller. Het meet het aantal frames ter beschikking gesteld aan de klant. Als deze waarde lager is dan de teller Invoerframes/Tweede, worden frames overgeslagen. Als u het knelpunt wilt identificeren, gebruikt u de tellers Frames Overgeslagen/Seconde.

Er zijn drie typen frames overgeslagen/tweede tellers:

- Frames overgeslagen/seconde (onvoldoende serverbronnen)
- Frames overgeslagen/seconde (onvoldoende netwerkbronnen)
- Frames overgeslagen/seconde (onvoldoende clientresources)

Een hoge waarde voor een van de frames overgeslagen / tweede tellers impliceert dat het probleem is gerelateerd aan de bron de teller tracks. Als de client bijvoorbeeld niet decodeert en frames presenteert met dezelfde snelheid die de server de frames biedt, is de teller Frames Overgeslagen/Tweede (Onvoldoende clientbronnen) hoog.

Als de teller Uitvoerframes/Seconde overeenkomt met de teller Invoerframes/Tweede, maar u nog steeds ongebruikelijke vertraging of vertraging opmerkt, kan gemiddelde coderingstijd de boosdoener zijn. Codering is een synchroon proces dat plaatsvindt op de server in het vGPU-scenario (single-session) en op de VM in het scenario met meerdere sessies. Gemiddelde coderingstijd moet onder de 33 ms zijn. Als de gemiddelde coderingstijd minder dan 33 ms is, maar u nog steeds prestatieproblemen hebt, kan er een probleem zijn met de app of het besturingssysteem dat u gebruikt.

Zie [Prestatiemeteritems voor gebruikersinvoervertraging](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)voor meer informatie over het diagnosticeren van app-gerelateerde problemen.

Omdat RDP een gemiddelde coderingstijd van 33 ms ondersteunt, ondersteunt het een invoerframesnelheid tot 30 frames per seconde. Houd er rekening mee dat 33 ms de maximale ondersteunde framesnelheid is. In veel gevallen zal de framesnelheid die de gebruiker ervaart lager zijn, afhankelijk van hoe vaak een frame door de bron aan RDP wordt geleverd. Taken zoals het bekijken van een video vereisen bijvoorbeeld een volledige invoerframesnelheid van 30 frames per seconde, maar minder rekenintensieve taken zoals het zelden bewerken van een document resulteren in een veel lagere waarde voor invoerframes/seconde zonder degradatie in de ervaringskwaliteit van de gebruiker.

### <a name="addressing-poor-frame-quality"></a>Slechte framekwaliteit aanpakken

Gebruik de framekwaliteit-teller om problemen met de framekwaliteit te diagnosticeren. Deze teller drukt de kwaliteit van het uitvoerframe uit als percentage van de kwaliteit van het bronframe. Het kwaliteitsverlies kan te wijten zijn aan RemoteFX, of het kan inherent zijn aan de grafische bron. Als RemoteFX het kwaliteitsverlies heeft veroorzaakt, kan het probleem een gebrek zijn aan netwerk- of serverbronnen om inhoud met een hogere getrouwheid te verzenden.

## <a name="mitigation"></a>Oplossing

Als serverbronnen het knelpunt veroorzaken, probeert u een van de volgende benaderingen om de prestaties te verbeteren:

- Verminder het aantal sessies per host.
- Verhoog de geheugen- en rekenbronnen op de server.
- Laat de resolutie van de verbinding vallen.

Als netwerkbronnen het knelpunt veroorzaken, probeert u een van de volgende benaderingen om de beschikbaarheid van het netwerk per sessie te verbeteren:

- Verminder het aantal sessies per host.
- Gebruik een netwerk met een hogere bandbreedte.
- Laat de resolutie van de verbinding vallen.

Als clientresources het knelpunt veroorzaken, probeert u een van de volgende benaderingen om de prestaties te verbeteren:

- Installeer de meest recente Extern bureaublad-client.
- Verhoog het geheugen en bereken resources op de clientmachine.

> [!NOTE]
> We ondersteunen momenteel de teller Source Frames/Second niet. Voorlopig wordt de bronframes/seconde-teller altijd 0 weergegeven.

## <a name="next-steps"></a>Volgende stappen

- Zie [GPU-versnelling configureren voor gpu-versnelling configureren voor windows Virtual Desktop- instellingen voor](configure-vm-gpu.md)het maken van een gpu-geoptimaliseerde Azure-virtuele machine.
- Zie Overzicht, feedback en ondersteuning voor [probleemoplossingen en](troubleshoot-set-up-overview.md)escalatiesporen voor een overzicht van probleemoplossing.
- Zie [Windows Desktop-omgeving](environment-setup.md)voor meer informatie over de service.
