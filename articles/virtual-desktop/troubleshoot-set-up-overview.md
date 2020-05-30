---
title: Overzicht van het oplossen van problemen met Windows virtueel bureau blad-Azure
description: Een overzicht van het oplossen van problemen bij het instellen van een Windows Virtual Desktop-Tenant omgeving.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c5be26509eccdaebf1b504c1b0b8c7edb35e101c
ms.sourcegitcommit: 0fa52a34a6274dc872832560cd690be58ae3d0ca
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84203851"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Probleemoplossingsoverzicht, feedback en ondersteuning

>[!IMPORTANT]
>Deze inhoud is van toepassing op de lente 2020-update met Azure Resource Manager virtueel-bureaublad objecten van Windows. Raadpleeg [dit artikel](./virtual-desktop-fall-2019/troubleshoot-set-up-overview-2019.md)als u de versie van het Windows-bureau blad van Virtual Desktop 2019 zonder Azure Resource Manager objecten gebruikt.
>
> De Windows Virtual Desktop lente 2020-update is momenteel beschikbaar als open bare preview. Deze preview-versie is beschikbaar zonder service level agreement. het wordt niet aangeraden deze te gebruiken voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. 
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Dit artikel bevat een overzicht van de problemen die zich kunnen voordoen bij het instellen van een Windows Virtual Desktop-Tenant omgeving en biedt manieren om de problemen op te lossen.

## <a name="report-issues-during-public-preview"></a>Problemen melden tijdens de open bare preview

Als u problemen wilt melden of functies wilt voors Ellen tijdens de open bare preview van de lente 2020-release, gaat u naar de [technische community van Windows Virtual Desktop](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop). U kunt de technische community gebruiken voor het bespreken van aanbevolen procedures of suggesties en stemmen voor nieuwe functies. Wanneer u een probleem met betrekking tot de open bare preview-versie rapporteert, moet u ervoor zorgen dat u het probleem type labelt als **lente 2020-update (preview)**.

Wanneer u een bericht voor hulp vraagt of een nieuwe functie voor stelt, zorg er dan voor dat u het onderwerp in zoveel mogelijk details beschrijft. Gedetailleerde informatie kan andere gebruikers helpen bij het beantwoorden van uw vraag of het begrijpen van de functie waarvoor u een stem voor stelt.

## <a name="escalation-tracks"></a>Escalatie trajecten

Voordat u iets anders doet, controleert u de [Azure-status pagina](https://status.azure.com/status) en [Azure service Health](https://azure.microsoft.com/features/service-health/) om te controleren of uw Azure-service correct wordt uitgevoerd.

Gebruik de volgende tabel voor het identificeren en oplossen van problemen die zich kunnen voordoen bij het instellen van een Tenant omgeving met Extern bureaublad-client. Zodra de Tenant is ingesteld, kunt u onze nieuwe service voor [Diagnostische gegevens](diagnostics-role-service.md) gebruiken om problemen voor veelvoorkomende scenario's te identificeren.

| **Probleem**                                                            | **Voorgestelde oplossing**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Toegang tot Marketplace-sjablonen in Azure Portal       | Azure Marketplace-sjablonen voor virtueel bureau blad zijn gratis beschikbaar.|
| Instellingen voor de Azure Virtual Network (VNET) en Express-route van de Session Host-pool               | [Open een ondersteunings aanvraag voor Azure](https://azure.microsoft.com/support/create-ticket/)en selecteer vervolgens de betreffende service (onder de categorie netwerken). |
| De hostgroep van de virtuele machine (VM) maken wanneer Azure Resource Manager sjablonen die worden meegeleverd met Windows virtueel bureau blad niet worden gebruikt | [Open een ondersteunings aanvraag voor Azure](https://azure.microsoft.com/support/create-ticket/)en selecteer vervolgens de **virtuele machine met Windows** voor de service. <br> <br> Zie voor problemen met de Azure Resource Manager sjablonen die worden meegeleverd met Windows virtueel bureau blad maken de sectie Tenant met Windows-virtueel bureau blad voor het maken van een [omgeving en hostgroep](troubleshoot-set-up-issues.md). |
| Windows Virtual Desktop Session Host-omgeving beheren vanuit de Azure Portal    | [Open een ondersteunings aanvraag voor Azure](https://azure.microsoft.com/support/create-ticket/). <br> <br> Zie [Windows Virtual Desktop Power shell](troubleshoot-powershell.md) of [een Azure-ondersteunings aanvraag openen](https://azure.microsoft.com/support/create-ticket/), **Windows virtueel bureau blad** voor de service, **configuratie en beheer** selecteren voor het probleem type en selecteer vervolgens **problemen met het configureren van tenants met Power shell** voor het subtype probleem. Extern bureaublad-services |
| Virtuele Windows-bureaublad configuratie beheren die zijn gekoppeld aan host Pools en toepassings groepen (app-groepen)      | Zie [Windows Virtual Desktop Power shell](troubleshoot-powershell.md), of [Open een ondersteunings aanvraag voor Azure](https://azure.microsoft.com/support/create-ticket/), selecteer **Windows virtueel bureau blad** voor de service en selecteer vervolgens het juiste probleem type.|
| FSLogix-profiel containers implementeren en beheren | Raadpleeg de [gids voor probleem oplossing voor FSLogix-producten](/fslogix/fslogix-trouble-shooting-ht/) en als het probleem niet wordt opgelost, [opent u een ondersteunings aanvraag voor Azure](https://azure.microsoft.com/support/create-ticket/), selecteert u **virtueel bureau blad voor Windows** voor de service, selecteert u **FSLogix** voor het probleem type en selecteert u vervolgens het juiste probleem subtype. |
| Storing van extern bureau blad-clients bij starten                                                 | Zie [problemen met de Extern bureaublad-client oplossen](troubleshoot-client.md) en als het probleem niet wordt opgelost, [opent u een ondersteunings aanvraag voor Azure](https://azure.microsoft.com/support/create-ticket/), selecteert u **Windows Virtual Desktop** voor de service en selecteert u vervolgens **extern bureaublad clients** voor het probleem type.  <br> <br> Als het een netwerk probleem is, moeten uw gebruikers contact opnemen met de netwerk beheerder. |
| Verbonden, maar geen feed                                                                 | Problemen oplossen met behulp van de [gebruiker verbinding maken, maar er wordt niets weer gegeven (geen feed)](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) van de [virtuele bureau blad-service verbindingen van Windows](troubleshoot-service-connection.md). <br> <br> Als uw gebruikers zijn toegewezen aan een app-groep, [opent u een ondersteunings aanvraag voor Azure](https://azure.microsoft.com/support/create-ticket/), selecteert u **Windows virtueel bureau blad** voor de service en selecteert u vervolgens **extern bureaublad clients** voor het probleem type. |
| Problemen met de detectie van feeds door het netwerk                                            | Uw gebruikers moeten contact opnemen met hun netwerk beheerder. |
| Clients verbinden                                                                    | Zie [Windows Virtual Desktop Service Connections](troubleshoot-service-connection.md) (Engelstalig) als u het probleem niet kunt oplossen, raadpleegt u de [virtuele machine configuratie](troubleshoot-vm-configuration.md)van de host. |
| Reactie tijd van externe toepassingen of bureau blad                                      | Neem contact op met het team dat verantwoordelijk is voor het desbetreffende product als er problemen zijn gekoppeld aan een specifieke toepassing of product. |
| Licentie berichten of-fouten                                                          | Neem contact op met het team dat verantwoordelijk is voor het desbetreffende product als er problemen zijn gekoppeld aan een specifieke toepassing of product. |
| Problemen met verificatie methoden van derden | Controleer of uw externe provider Windows Virtual Desktop-scenario's ondersteunt en hoe u deze aanpakt met betrekking tot bekende problemen. |
| Problemen met het gebruik van Log Analytics voor virtueel bureau blad van Windows | Voor problemen met het diagnostische schema [opent u een ondersteunings aanvraag voor Azure](https://azure.microsoft.com/support/create-ticket/).<br><br>Voor query's, visualisaties of andere problemen in Log Analytics selecteert u het juiste probleem type onder Log Analytics. |
| Problemen met het gebruik van M365-apps | Neem contact op met het M365-beheer centrum met een van de Help-opties voor het [M365-beheer centrum](/microsoft-365/admin/contact-support-for-business-products/). |

## <a name="next-steps"></a>Volgende stappen

- Zie [omgeving en hostgroep maken](troubleshoot-set-up-issues.md)om problemen op te lossen tijdens het maken van een hostgroep in een virtuele Windows-desktop omgeving.
- Zie voor het oplossen van problemen bij het configureren van een virtuele machine (VM) in Windows virtueel bureau blad de [virtuele machine configuratie](troubleshoot-vm-configuration.md)van de host.
- Zie [Windows Virtual Desktop Service Connections](troubleshoot-service-connection.md)(Engelstalig) voor het oplossen van problemen met Windows Virtual Desktop-Client verbindingen.
- Zie [problemen met de Extern bureaublad-client oplossen](troubleshoot-client.md) om problemen met extern bureaublad-clients op te lossen
- Zie [Windows Virtual Desktop Power shell](troubleshoot-powershell.md)(Engelstalig) voor informatie over het oplossen van problemen met het gebruik van Power shell met Windows virtueel bureau blad.
- Zie [Windows Virtual Desktop Environment](environment-setup.md)(Engelstalig) voor meer informatie over de service.
- Zie [zelf studie: problemen met implementaties van Resource Manager-sjablonen oplossen](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)om de zelf studie voor problemen oplossen op te lossen.
- Zie [bewerkingen controleren met Resource Manager](../azure-resource-manager/management/view-activity-logs.md)voor meer informatie over controle acties.
- Zie [implementatie bewerkingen weer geven](../azure-resource-manager/templates/deployment-history.md)voor meer informatie over acties om fouten te bepalen tijdens de implementatie.
