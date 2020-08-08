---
title: Problemen met Windows Virtual Desktop vaststellen-Azure
description: De Windows-functie diagnostische gegevens over virtueel bureau blad gebruiken om problemen te diagnosticeren.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a985ce4f93b04e4065b5189b2a406b54729720c3
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/07/2020
ms.locfileid: "88005100"
---
# <a name="identify-and-diagnose-windows-virtual-desktop-issues"></a>Problemen met Windows virtueel bureau blad identificeren en vaststellen

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop met Azure Resource Manager Windows Virtual Desktop-objecten. Zie [dit artikel](./virtual-desktop-fall-2019/diagnostics-role-service-2019.md) als u Windows Virtual Desktop (klassiek) zonder Azure Resource Manager-objecten gebruikt.

Virtueel bureau blad van Windows biedt een functie voor diagnostische gegevens waarmee de beheerder problemen kan identificeren via één interface. Zie [log Analytics gebruiken voor de functie diagnostische gegevens voor](diagnostics-log-analytics.md)meer informatie over de diagnostische mogelijkheden van Windows virtueel bureau blad.

Verbindingen die zich niet in het virtuele bureau blad van Windows bevinden, worden niet weer gegeven in de resultaten van diagnostische gegevens omdat de functie Service diagnostiek zelf deel uitmaakt van Windows virtueel bureau blad. Er kunnen problemen met de Windows-verbinding met virtueel bureau blad optreden wanneer de eind gebruiker problemen met de netwerk verbinding ondervindt.

## <a name="common-error-scenarios"></a>Veelvoorkomende foutscenario's

Fout scenario's worden intern gecategoriseerd voor de service en extern naar virtueel bureau blad van Windows.

* Intern probleem: Hiermee geeft u scenario's op die niet door de klant kunnen worden verminderd en moeten worden omgezet als een ondersteunings probleem. Wanneer u feedback geeft via de [technische community van het Windows-bureau blad](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop), neemt u de correlatie-id en het geschatte tijds bestek op wanneer het probleem is opgetreden.
* Extern probleem: houdt rekening met scenario's die door de klant kunnen worden verminderd. Dit zijn externe virtuele Bureau bladen van Windows.

De volgende tabel bevat algemene fouten die door uw beheerders kunnen worden uitgevoerd.

>[!NOTE]
>Deze lijst bevat de meest voorkomende fouten en wordt bijgewerkt op een gewone uitgebracht. Om ervoor te zorgen dat u de meest recente informatie hebt, moet u dit artikel ten minste één keer per maand opnieuw controleren.

## <a name="management-errors"></a>Beheer fouten

|Foutbericht|Voorgestelde oplossing|
|---|---|
|Kan de registratie sleutel niet maken |Registratie token kan niet worden gemaakt. Probeer het opnieuw te maken met een kortere verloop tijd (tussen 1 uur en 1 maand). |
|Kan de registratie sleutel niet verwijderen|Het registratie token kan niet worden verwijderd. Probeer de app opnieuw te verwijderen. Als het nog steeds niet werkt, gebruikt u Power shell om te controleren of het token nog steeds aanwezig is. Als dat het geval is, kunt u deze verwijderen met Power shell.|
|Kan de verwerkings modus voor de sessie-host niet wijzigen |De afvoer modus kan niet worden gewijzigd op de VM. Controleer de status van de virtuele machine. Als de virtuele machine niet beschikbaar is, kan de afvoer modus niet worden gewijzigd.|
|Kan geen verbinding met gebruikers sessies verbreken |De verbinding van de gebruiker met de virtuele machine is mislukt. Controleer de status van de virtuele machine. Als de virtuele machine niet beschikbaar is, kan de gebruikers sessie niet worden verbroken. Als de virtuele machine beschikbaar is, controleert u de status van de gebruikers sessie om te zien of de verbinding is verbroken. |
|Kan niet alle gebruiker (s) in de sessiehost afmelden |Kan de gebruikers niet aanmelden bij de virtuele machine. Controleer de status van de virtuele machine. Als deze niet beschikbaar is, kunnen gebruikers niet worden afgemeld. Controleer de status van de gebruikers sessie om te zien of ze al zijn afgemeld. U kunt afmelden met Power shell afdwingen. |
|Kan de toewijzing van de gebruiker aan de toepassings groep niet opheffen|Kan de publicatie van een app-groep voor een gebruiker niet ongedaan maken. Controleer of er een gebruiker beschikbaar is in azure AD. Controleer of de gebruiker deel uitmaakt van een gebruikers groep waarvan de app-groep wordt gepubliceerd. |
|Er is een fout opgetreden bij het ophalen van de beschik bare locaties |Controleer de locatie van de virtuele machine die wordt gebruikt in de wizard hostgroep maken. Als de installatie kopie niet beschikbaar is op die locatie, voegt u een installatie kopie toe op die locatie of kiest u een andere VM-locatie. |

### <a name="external-connection-error-codes"></a>Fout codes voor externe verbindingen

|Numerieke code|Foutcode|Voorgestelde oplossing|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|De sessiehost is niet juist gekoppeld aan de Active Directory.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|De verbindingen zijn mislukt omdat de sessiehost niet beschikbaar is. Controleer de status van de sessie-host.|
|-2146233088|ConnectionFailedClientDisconnect|Als deze fout regel matig wordt weer geven, controleert u of de computer van de gebruiker is verbonden met het netwerk.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|De sessie waarmee de host-gebruiker probeerde verbinding te maken, is niet in orde. Fouten opsporen in de virtuele machine.|
|-2146233088|ConnectionFailedUserNotAuthorized|De gebruiker is niet gemachtigd om toegang te krijgen tot de gepubliceerde app of het bureau blad. De fout kan worden weer gegeven nadat de beheerder gepubliceerde resources heeft verwijderd. Vraag de gebruiker om de feed in de Extern bureaublad toepassing te vernieuwen.|
|2|FileNotFound|De toepassing waartoe de gebruiker toegang probeerde te krijgen, is onjuist geïnstalleerd of ingesteld op een onjuist pad.<br><br>Bij het publiceren van nieuwe apps terwijl de gebruiker een actieve sessie heeft, heeft de gebruiker geen toegang tot deze app. De sessie moet worden afgesloten en opnieuw worden gestart voordat de gebruiker toegang kan krijgen tot de app. |
|3|InvalidCredentials|De gebruikers naam of het wacht woord die de gebruiker heeft ingevoerd, komt niet overeen met de bestaande gebruikers namen of wacht woorden. Controleer de referenties voor type fouten en probeer het opnieuw.|
|8|ConnectionBroken|De verbinding tussen de client en de gateway of server is verbroken. Er is geen actie vereist, tenzij deze onverwacht plaatsvindt.|
|14|UnexpectedNetworkDisconnect|De verbinding met het netwerk is verbroken. Vraag de gebruiker om opnieuw verbinding te maken.|
|24|ReverseConnectFailed|De virtuele machine van de host heeft geen directe regel van het gezichts vermogen om te RD-gateway. Zorg ervoor dat het IP-adres van de gateway kan worden omgezet.|

## <a name="next-steps"></a>Volgende stappen

Zie [Windows Virtual Desktop Environment](environment-setup.md)(Engelstalig) voor meer informatie over de functies van virtuele Bureau bladen van Windows.

Zie de [Power shell-referentie](/powershell/windows-virtual-desktop/overview)voor een lijst met beschik bare Power shell-cmdlets voor virtuele Windows-Bureau bladen.
