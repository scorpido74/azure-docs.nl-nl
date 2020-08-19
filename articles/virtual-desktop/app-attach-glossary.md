---
title: Woorden lijst voor het toevoegen van de MSIX-app van Windows virtueel bureau blad-Azure
description: Een woorden lijst met voor waarden en concepten voor het koppelen van MSIX-apps.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f3cc8495f673c8b428aa9e6ace2747a70c5b0847
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556286"
---
# <a name="msix-app-attach-glossary"></a>Woorden lijst voor het toevoegen van MSIX-apps

Dit artikel bevat een lijst met definities voor belang rijke termen en concepten die betrekking hebben op MSIX app attach.

## <a name="msix-container"></a>MSIX-container

Een MSIX-container is waar MSIX-apps worden uitgevoerd. Zie [MSIX-containers](/windows/msix/msix-container)voor meer informatie.

## <a name="msix-application"></a>MSIX-toepassing 

Een toepassing die is opgeslagen in een MSIX-bestand.

## <a name="msix-package"></a>MSIX-pakket 

Een MSIX-pakket is een MSIX-bestand of-toepassing.

## <a name="msix-share"></a>MSIX-share

Een MSIX-share is een netwerk share die uitgevouwen MSIX-pakketten bevat. MSIX-shares bieden ondersteuning voor SMB 3 of hoger. Toepassingen worden klaargezet van deze MSIX-share zonder dat ze toepassings bestanden naar het systeem station hoeven te verplaatsen.

## <a name="repackage"></a>Opnieuw verpakken

Bij het opnieuw inpakken wordt een niet-MSIX toepassing gebruikt en omgezet in MSIX met behulp van het MSIX-verpakkings programma (MPT). Zie het overzicht van het [MSIX-verpakkings programma](/windows/msix/packaging-tool/tool-overview)voor meer informatie.

## <a name="expand"></a>Uitvouwen

Een uitbreidbaar MSIX-pakket is een proces met meerdere stappen. Het MSIX-bestand wordt gebruikt en de inhoud wordt in een VHD (x) of een CIM-bestand geplaatst. 

Een MSIX-pakket uitbreiden:

1. Een MSIX-pakket (MSIX-bestand) ophalen.
2. Wijzig de naam van het MSIX-bestand in een zip-bestand.
3. Pak het resulterende zip-bestand uit in een map.
4. Maak een VHD die even groot is als de map.
5. Koppel de VHD.
6. Initialiseer een schijf.
7. Maak een partitie.
8. Format teer de partitie.
9. Kopieer de uitgepakte inhoud naar de VHD.
10. Gebruik het hulp programma MSIXMGR om Acl's toe te passen op de inhoud van de VHD.
11. Ontkoppel de VHD (x) of [CIM](#cim).

## <a name="upload-an-msix-package"></a>Een MSIX-pakket uploaden 

Als u een MSIX-pakket uploadt, moet u de VHD (x) of [CIM](#cim) met een uitgebreid MSIX-pakket uploaden naar de MSIX-share.

In Windows Virtual Desktop worden uploads eenmaal per MSIX-share uitgevoerd. Wanneer u een pakket uploadt, kunnen alle hostgroepen in hetzelfde abonnement hiernaar verwijzen.

## <a name="publish-an-msix-package"></a>Een MSIX-pakket publiceren

In virtuele Bureau bladen van Windows publiceert een MSIX-pakket deze naar een externe app of bureau blad.

## <a name="assign-an-msix-package"></a>Een MSIX-pakket toewijzen 

In Windows Virtual Desktop moet een gepubliceerd MSIX-pakket worden toegewezen aan een gebruikers-of gebruikers groep van een Active Directory-domein-service (AD DS) of Azure Active Directory (Azure AD).

## <a name="staging"></a>Staging

Fase ring omvat twee dingen:

- De VHD (x) of [CIM](#cim) koppelen aan de virtuele machine.
- Het besturings systeem op de hoogte stellen dat het MSIX-pakket beschikbaar is voor registratie.

## <a name="registration"></a>Registratie

Registratie maakt een klaargezet MSIX-pakket beschikbaar voor uw gebruikers. Registratie is per gebruiker. Als u een app niet expliciet hebt geregistreerd voor die specifieke gebruiker, kunnen ze de app niet meer uitvoeren.

Er zijn twee soorten registratie: normaal en vertraagd.

### <a name="regular-registration"></a>Normale registratie

Bij een normale registratie wordt elke toepassing die is toegewezen aan een gebruiker, volledig geregistreerd. Registratie vindt plaats wanneer de gebruiker zich aanmeldt bij de sessie. Dit kan van invloed zijn op de tijd die ze nodig hebben om te beginnen met het gebruik van Windows virtueel bureau blad.

### <a name="delayed-registration"></a>Vertraagde registratie

Bij een vertraagde registratie wordt elke toepassing die aan de gebruiker is toegewezen, slechts gedeeltelijk geregistreerd. Gedeeltelijke registratie betekent dat de tegel in het menu Start en bestands koppelingen voor dubbel klikken zijn geregistreerd. De registratie wordt uitgevoerd terwijl de gebruiker zich aanmeldt bij de sessie, zodat deze een minimale impact heeft op de tijd die nodig is om te beginnen met het gebruik van Windows virtueel bureau blad. De registratie is alleen voltooid wanneer de gebruiker de toepassing uitvoert in het MSIX-pakket.

De vertraagde registratie is momenteel de standaard configuratie voor het koppelen van de MSIX-app.

## <a name="deregistration"></a>Registratie opheffen

Bij het opheffen van de registratie wordt een geregistreerd, maar niet-uitgevoerde MSIX-pakket voor een gebruiker verwijderd. De registratie vindt plaats wanneer de gebruiker zich afmeldt bij de sessie. Tijdens het opheffen van de registratie pusht MSIX app attach toepassings gegevens die specifiek zijn voor de gebruiker naar het lokale gebruikers profiel.

## <a name="destage"></a>Destage

Met destaging wordt het besturings systeem gewaarschuwd dat een MSIX-pakket of-toepassing die momenteel niet wordt uitgevoerd en niet kan worden gekoppeld aan een wille keurige gebruiker. Hiermee wordt de verwijzing naar het bestand in het besturings systeem verwijderd.

## <a name="cim"></a>VRACHT

. CIM is een nieuwe bestands extensie die is gekoppeld aan een samengesteld installatie kopie bestand systeem (CimFS). Het koppelen en ontkoppelen van CIM-bestanden verloopt sneller dan die VHD-bestanden. CIM verbruikt ook minder CPU en geheugen dan VHD.

De volgende tabel is een prestatie vergelijking tussen VHD en CimFS. Deze getallen zijn het resultaat van een test uitvoering met 800 MB aan bestanden in elke indeling die wordt uitgevoerd op een DSv4-machine.

|  Specificaties                          | VHD                    | CimFS   |
|---------------------------------|--------------------------|-----------|
| Gemiddelde koppelings tijd     | 356 MS                     | 255 MS      |
| Gemiddelde ontkoppelings tijd   | 1615 MS                    | 36 MS       |
| Geheugenverbruik | 6% (van 8 GB)                      | 2% (van 8 GB)       |
| CPU (aantal pieken)          | Meerdere keren benut | Geen invloed |

## <a name="next-steps"></a>Volgende stappen

Als u meer wilt weten over het toevoegen van MSIX-apps, raadpleegt u ons [overzicht](what-is-app-attach.md) en [Veelgestelde vragen](app-attach-faq.md). U kunt ook aan de slag met het [instellen van een app-koppeling](app-attach.md).

