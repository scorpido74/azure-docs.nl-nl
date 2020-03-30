---
title: Windows Virtual Desktop-omgeving - Azure
description: De basiselementen van een Windows Virtual Desktop-omgeving.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 33d058f028b7032f296ffcf82f0e5fe2c993e6fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127913"
---
# <a name="windows-virtual-desktop-environment"></a>Windows Virtual Desktop-omgeving

Windows Virtual Desktop is een service die gebruikers eenvoudig en veilig toegang geeft tot hun gevirtualiseerde desktops en RemoteApps. In dit onderwerp vindt u meer informatie over de algemene structuur van de Windows Virtual Desktop-omgeving.

## <a name="tenants"></a>Tenants

De Windows Virtual Desktop-tenant is de primaire interface voor het beheren van uw Windows Virtual Desktop-omgeving. Elke Windows Virtual Desktop-tenant moet zijn gekoppeld aan de Azure Active Directory met de gebruikers die zich bij de omgeving aanmelden. Vanuit de Windows Virtual Desktop-tenant u beginnen met het maken van hostpools om de workloads van uw gebruikers uit te voeren.

## <a name="host-pools"></a>Zwembaden hosten

Een hostgroep is een verzameling van virtuele Azure-machines die zich registreren bij Windows Virtual Desktop als sessiehosts wanneer u de Windows Virtual Desktop-agent uitvoert. Alle virtuele machines voor sessiehost in een hostpool moeten afkomstig zijn van dezelfde afbeelding voor een consistente gebruikerservaring.

Een hostpool kan een van de twee typen zijn:

- Persoonlijk, waarbij elke sessiehost is toegewezen aan individuele gebruikers.
- Samengevoegd, waar sessiehosts verbindingen kunnen accepteren van elke gebruiker die gemachtigd is voor een app-groep in de hostgroep.

U extra eigenschappen instellen op de hostgroep om het taakverdelingsgedrag te wijzigen, hoeveel sessies elke sessiehost kan nemen en wat de gebruiker kan doen met sessiehosts in de hostgroep terwijl hij is aangemeld bij hun Windows Virtual Desktop-sessies. U beheert de bronnen die via appgroepen aan gebruikers worden gepubliceerd.

## <a name="app-groups"></a>App-groepen

Een app-groep is een logische groepering van toepassingen die zijn geïnstalleerd op sessiehosts in de hostgroep. Een app-groep kan een van de twee typen zijn:

- RemoteApp, waarbij gebruikers toegang krijgen tot de RemoteApps die u afzonderlijk selecteert en publiceert in de appgroep
- Bureaublad, waar gebruikers toegang hebben tot het volledige bureaublad

Standaard wordt een bureaublad-appgroep (met de naam 'Bureaubladtoepassingsgroep' genoemd) automatisch gemaakt wanneer u een hostgroep maakt. U deze appgroep op elk gewenst moment verwijderen. U echter geen andere bureaublad-appgroep maken in de hostgroep terwijl er een bureaublad-appgroep bestaat. Als u RemoteApps wilt publiceren, moet u een RemoteApp-appgroep maken. U meerdere RemoteApp-appgroepen maken om rekening te houden met verschillende werkscenario's. Verschillende RemoteApp-appgroepen kunnen ook overlappende RemoteApps bevatten.

Als u resources wilt publiceren aan gebruikers, moet u deze toewijzen aan app-groepen. Houd bij het toewijzen van gebruikers aan app-groepen rekening met de volgende dingen:

- Een gebruiker kan niet worden toegewezen aan zowel een bureaublad-appgroep als een RemoteApp-appgroep in dezelfde hostgroep.
- Een gebruiker kan worden toegewezen aan meerdere appgroepen binnen dezelfde hostgroep en zijn feed is een opeenstapeling van beide appgroepen.

## <a name="tenant-groups"></a>Tenantgroepen

In Windows Virtual Desktop vindt de Windows Virtual Desktop-tenant plaats waar het grootste deel van de installatie en configuratie plaatsvindt. De Windows Virtual Desktop-tenant bevat de hostgroepen, app-groepen en gebruikerstoewijzingen voor app-groepen. Er kunnen echter bepaalde situaties zijn waarin u meerdere Windows Virtual Desktop-tenants tegelijk moet beheren, vooral als u een Cloud Service Provider (CSP) of een hostingpartner bent. In deze situaties u een aangepaste Windows Virtual Desktop-tenantgroep gebruiken om elk van de Windows Virtual Desktop-tenants van de klant te plaatsen en de toegang centraal te beheren. Als u echter slechts één Windows Virtual Desktop-tenant beheert, is het concept van de tenantgroep niet van toepassing en u uw tenant blijven beheren die in de standaardtenantgroep bestaat.

## <a name="end-users"></a>Eindgebruikers

Nadat u gebruikers aan hun appgroepen hebt toegewezen, kunnen ze verbinding maken met een Windows Virtual Desktop-implementatie met een van de Windows Virtual Desktop-clients.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over gedelegeerde toegang en het toewijzen van rollen aan gebruikers op [Delegated Access in Windows Virtual Desktop](delegated-access-virtual-desktop.md).

Zie [Een tenant maken in Windows Virtual Desktop](tenant-setup-azure-active-directory.md)voor meer informatie over het instellen van uw Windows Virtual Desktop-tenant.

Zie een van de volgende artikelen voor meer informatie over het maken van verbinding met Windows Virtual Desktop:

- [Verbinding maken vanaf Windows 10 of Windows 7](connect-windows-7-and-10.md)
- [Verbinding maken via een webbrowser](connect-web.md)
