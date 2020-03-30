---
title: Overzicht van probleemoplossing windows Virtual Desktop - Azure
description: Een overzicht voor het oplossen van problemen tijdens het instellen van een Windows Virtual Desktop-tenantomgeving.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 02/07/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2b9dfb2ee7e74f94bfc3e6d2c679cb7da3d6bc66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127398"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Probleemoplossingsoverzicht, feedback en ondersteuning

In dit artikel vindt u een overzicht van de problemen die u ondervinden bij het instellen van een Windows Virtual Desktop-tenantomgeving en worden manieren gevonden om de problemen op te lossen.

## <a name="provide-feedback"></a>Feedback geven

Ga naar de [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) om de Windows Virtual Desktop-service te bespreken met het productteam en actieve communityleden.

## <a name="escalation-tracks"></a>Escalatiesporen

Gebruik de volgende tabel om problemen te identificeren en op te lossen die u tegenkomen bij het instellen van een tenantomgeving met behulp van extern bureaublad-client. Zodra uw tenant is ingesteld, u onze nieuwe [Diagnostics-service](diagnostics-role-service.md) gebruiken om problemen voor veelvoorkomende scenario's te identificeren.

>[!NOTE]
> We hebben een Tech Community-forum dat u bezoeken om uw problemen te bespreken met het productteam en actieve communityleden. Ga naar de [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) om een discussie te starten.

| **Probleem**                                                            | **Voorgestelde oplossing**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Een Windows Virtual Desktop-tenant maken                                                    | Als er een Azure-storing is, [opent u een Azure-ondersteuningsaanvraag;](https://azure.microsoft.com/support/create-ticket/) [anders een Azure-ondersteuningsaanvraag openen](https://azure.microsoft.com/support/create-ticket/), Windows Virtual **Desktop** voor de service selecteren, **Implementatie** voor het probleemtype selecteren en vervolgens Problemen selecteren bij het maken van een Windows **Virtual Desktop-tenant** voor het probleemsubtype.|
| Toegang tot Marketplace-sjablonen in Azure-portal       | Als er een Azure-storing is, [opent u een Azure-ondersteuningsaanvraag](https://azure.microsoft.com/support/create-ticket/). <br> <br> Azure Marketplace Windows Virtual Desktop-sjablonen zijn gratis beschikbaar.|
| Toegang tot Azure Resource Manager-sjablonen vanuit GitHub                                  | Zie het gedeelte [VM's voor windows-virtuele bureaublad-sessiehost maken](troubleshoot-set-up-issues.md#creating-windows-virtual-desktop-session-host-vms) van [Tenant- en hostpoolcreatie](troubleshoot-set-up-issues.md). Als het probleem nog steeds is opgelost, neemt u contact op met het [Ondersteuningsteam van GitHub](https://github.com/contact). <br> <br> Als de fout optreedt nadat u de sjabloon in GitHub hebt geopend, neemt u contact op met [Azure Support](https://azure.microsoft.com/support/create-ticket/).|
| Instellingen voor het azure virtual network (Azure Network) en Express-route               | [Open een Azure-ondersteuningsaanvraag](https://azure.microsoft.com/support/create-ticket/)en selecteer vervolgens de juiste service (onder de categorie Netwerken). |
| Vm-creatie (Session host pool Virtual Machine) wanneer Azure Resource Manager-sjablonen die zijn voorzien van Windows Virtual Desktop niet worden gebruikt | [Open een Azure-ondersteuningsaanvraag](https://azure.microsoft.com/support/create-ticket/)en selecteer Vervolgens Virtuele machine waarop Windows voor de service **wordt uitgevoerd.** <br> <br> Zie Windows Virtual Desktop-tenantsectie maken van Tenant en het maken van hostpoolvoor problemen met de Azure Resource [Manager-sjablonen](troubleshoot-set-up-issues.md)die zijn geleverd met Windows Virtual Desktop. |
| Windows Virtual Desktop-sessiehostomgeving beheren vanuit de Azure-portal    | [Open een Azure-ondersteuningsaanvraag](https://azure.microsoft.com/support/create-ticket/). <br> <br> Voor beheerproblemen bij het gebruik van Extern bureaublad-services/Windows Virtual Desktop PowerShell, zie [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md) of [open een Azure-ondersteuningsaanvraag,](https://azure.microsoft.com/support/create-ticket/)selecteert u **Windows Virtual Desktop** voor de service, selecteert u Configuratie **en beheer** voor het probleemtype en selecteert u Problemen configureren **met PowerShell** voor het probleemsubtype. |
| Windows Virtual Desktop-configuratie beheren die is gekoppeld aan hostgroepen en toepassingsgroepen (appgroepen)      | Zie [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)of open een [Azure-ondersteuningsaanvraag](https://azure.microsoft.com/support/create-ticket/), selecteer **Windows Virtueel bureaublad** voor de service en selecteer vervolgens het juiste probleemtype.|
| FSLogix-profielcontainers implementeren en beheren | Zie [Handleiding voor probleemoplossing voor FSLogix-producten](/fslogix/fslogix-trouble-shooting-ht/) en als dit het probleem niet oplost, [Opent u een Azure-ondersteuningsaanvraag,](https://azure.microsoft.com/support/create-ticket/)selecteert u **Windows Virtual Desktop** voor de service, selecteert u **FSLogix** voor het probleemtype en selecteert u vervolgens het juiste probleemsubtype. |
| Storing externe desktopclients bij het starten                                                 | Zie [Problemen met de Extern bureaublad-client oplossen](troubleshoot-client.md) en als dit het probleem niet oplost, opent u een [Azure-ondersteuningsaanvraag](https://azure.microsoft.com/support/create-ticket/), selecteert u Windows Virtueel **bureaublad** voor de service en selecteert u **Extern bureaublad-clients** voor het probleemtype.  <br> <br> Als het een netwerkprobleem is, moeten uw gebruikers contact opnemen met hun netwerkbeheerder. |
| Aangesloten, maar geen feed                                                                 | Problemen oplossen met het gebruik van de verbinding met de [gebruiker wordt verbroken, maar er wordt niets weergegeven (geen feed)](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) sectie van [Windows Virtual Desktop-serviceverbindingen](troubleshoot-service-connection.md). <br> <br> Als uw gebruikers zijn toegewezen aan een app-groep, [opent u een Azure-ondersteuningsaanvraag](https://azure.microsoft.com/support/create-ticket/), selecteert u **Windows Virtueel bureaublad** voor de service en selecteert u **Extern bureaublad-clients** voor het probleemtype. |
| Problemen met feeddetectie als gevolg van het netwerk                                            | Uw gebruikers moeten contact opnemen met hun netwerkbeheerder. |
| Klanten verbinden                                                                    | Zie [Windows Virtual Desktop-serviceverbindingen](troubleshoot-service-connection.md) en als dit uw probleem niet oplost, raadpleegt [u de configuratie van de virtuele machine sessiehost](troubleshoot-vm-configuration.md). |
| Reactievermogen van externe toepassingen of bureaublad                                      | Als problemen gekoppeld zijn aan een specifieke toepassing of product, neem dan contact op met het team dat verantwoordelijk is voor dat product. |
| Licentieberichten of -fouten                                                          | Als problemen gekoppeld zijn aan een specifieke toepassing of product, neem dan contact op met het team dat verantwoordelijk is voor dat product. |
| Problemen bij het gebruik van Windows Virtual Desktop-hulpprogramma's op GitHub (Azure Resource Manager-sjablonen, diagnosetool, beheertool) | Zie [Azure Resource Manager-sjablonen voor Extern bureaublad-services](https://github.com/Azure/RDS-Templates/blob/master/README.md) om problemen te melden. |

## <a name="next-steps"></a>Volgende stappen

- Zie [Tenant en hostpool maken](troubleshoot-set-up-issues.md)als u problemen wilt oplossen tijdens het maken van een tenant en hostpool in een Windows Virtual Desktop-omgeving.
- Zie Configuratie van de [virtuele machine sessiehost](troubleshoot-vm-configuration.md)voor het oplossen van problemen tijdens het configureren van een virtuele machine (VM) in Windows Virtual Desktop.
- Zie [Windows Virtual Desktop-serviceverbindingen](troubleshoot-service-connection.md)voor het oplossen van problemen met Windows Virtual Desktop-clientverbindingen.
- Zie Problemen met [de Extern bureaublad-client oplossen](troubleshoot-client.md) als u problemen met Extern bureaublad-clients wilt oplossen
- Zie [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)voor het oplossen van problemen bij het gebruik van PowerShell met Windows Virtual Desktop.
- Zie De Windows Virtual [Desktop-omgeving](environment-setup.md)voor meer informatie over de service.
- Zie [Zelfstudie: Problemen met resourcebeheersjabloonimplementaties](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)oplossen.
- Zie [Controlebewerkingen met Resource Manager](../azure-resource-manager/management/view-activity-logs.md)voor meer informatie over controleacties.
- Zie [Implementatiebewerkingen weergeven](../azure-resource-manager/templates/deployment-history.md)voor meer informatie over acties om fouten tijdens de implementatie te bepalen.
