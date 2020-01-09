---
title: Overzicht van het oplossen van problemen met Windows virtueel bureau blad-Azure
description: Een overzicht van het oplossen van problemen bij het instellen van een Windows Virtual Desktop-Tenant omgeving.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/06/2019
ms.author: helohr
ms.openlocfilehash: 817cd09797ad861521a462da9c5d0850c65bf562
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75459571"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Probleemoplossingsoverzicht, feedback en ondersteuning

Dit artikel bevat een overzicht van de problemen die zich kunnen voordoen bij het instellen van een Windows Virtual Desktop-Tenant omgeving en biedt manieren om de problemen op te lossen.

## <a name="provide-feedback"></a>Feedback geven

Ga naar de [technische community van Windows virtueel bureau blad](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) voor het bespreken van de Windows Virtual Desktop-service met het product team en de actieve leden van de community.

## <a name="escalation-tracks"></a>Escalatie trajecten

Gebruik de volgende tabel voor het identificeren en oplossen van problemen die zich kunnen voordoen bij het instellen van een Tenant omgeving met Extern bureaublad-client. Zodra de Tenant is ingesteld, kunt u onze nieuwe service voor [Diagnostische gegevens](https://docs.microsoft.com/azure/virtual-desktop/diagnostics-role-service) gebruiken om problemen voor veelvoorkomende scenario's te identificeren.

>[!NOTE]
> We hebben een technisch Community-Forum dat u kunt bezoeken om uw problemen met het product team en de actieve communityleden te bespreken. Bezoek de [technische community van Windows Virtual Desktop](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) voor 

| **Probleem**                                                            | **Voorgestelde oplossing**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Een Tenant maken                                                    | Als er een storing in azure optreedt, neemt u contact op met de [ondersteuning van Azure](https://azure.microsoft.com/support/options/). **Open anders een ondersteunings aanvraag voor Windows virtueel bureau blad (Compute)** .|
| Toegang tot Marketplace-sjablonen in Azure Portal       | Als er een onderbreking van Azure is, neemt u contact op met de [ondersteuning van Azure](https://azure.microsoft.com/support/options/). <br> <br> Azure Marketplace-sjablonen voor virtueel bureau blad zijn gratis beschikbaar.|
| Toegang tot Azure Resource Manager sjablonen vanuit GitHub                                  | Zie de sectie ' virtuele bureau blad-sessiehost Vm's maken ' van [Tenant en hostgroep maken](troubleshoot-set-up-issues.md). Als het probleem nog steeds niet is opgelost, neemt u contact op met het [ondersteunings team van github](https://github.com/contact). <br> <br> Als de fout optreedt nadat u de sjabloon in GitHub hebt geopend, neemt u contact op met de [ondersteuning van Azure](https://azure.microsoft.com/support/options/).|
| Instellingen voor de Azure Virtual Network (VNET) en Express-route van de Session Host-pool               | Neem contact op met de **ondersteuning van Azure (netwerken)** . |
| De hostgroep van de virtuele machine (VM) maken wanneer Azure Resource Manager sjablonen die worden meegeleverd met Windows virtueel bureau blad niet worden gebruikt | Neem contact op met de **ondersteuning van Azure (Compute)** . <br> <br> Zie voor problemen met de Azure Resource Manager sjablonen die worden meegeleverd met Windows virtueel bureau blad maken de sectie Tenant voor Windows-virtueel bureau blad voor het [maken van Tenant-en hostgroepen](troubleshoot-set-up-issues.md). |
| Windows Virtual Desktop Session Host-omgeving beheren vanuit de Azure Portal    | Neem contact op met de **ondersteuning van Azure**. <br> <br> Zie [Windows Virtual Desktop Power shell](troubleshoot-powershell.md) of **een ondersteunings aanvraag voor Windows Virtual Desktop (Compute)** voor meer informatie over beheer problemen bij het gebruik van extern bureaublad-services/virtueel bureau blad Power shell van Windows. |
| Virtuele Windows-bureaublad configuratie beheren die zijn gekoppeld aan host Pools en toepassings groepen (app-groepen)      | Zie [Windows Virtual Desktop Power shell](troubleshoot-powershell.md)of **Open een ondersteunings aanvraag voor Windows Virtual Desktop (Compute)** . <br> <br> Als er problemen zijn gekoppeld aan de voorbeeld Graphical User Interface (GUI), neem dan contact op met de Yammer-community.|
| Storing van extern bureau blad-clients bij starten                                                 | Zie [problemen met de Extern bureaublad-client oplossen](troubleshoot-client.md) en als het probleem niet wordt opgelost, **opent u een ondersteunings aanvraag voor Windows virtueel bureau blad (Compute)** .  <br> <br> Als het een netwerk probleem is, moeten uw gebruikers contact opnemen met de netwerk beheerder. |
| Verbonden, maar geen feed                                                                 | Problemen oplossen met behulp van de sectie ' verbinding maken, maar er wordt niets weer gegeven (geen feed) ' van [het oplossen van problemen met de Extern bureaublad-client](troubleshoot-client.md). <br> <br> Als uw gebruikers zijn toegewezen aan een app-groep, **opent u een ondersteunings aanvraag voor Windows virtueel bureau blad (Compute)** . |
| Problemen met de detectie van feeds door het netwerk                                            | Uw gebruikers moeten contact opnemen met hun netwerk beheerder. |
| Clients verbinden                                                                    | Zie [Windows Virtual Desktop Service Connections](troubleshoot-service-connection.md) (Engelstalig) als u het probleem niet kunt oplossen, raadpleegt u de [virtuele machine configuratie](troubleshoot-vm-configuration.md)van de host. |
| Reactie tijd van externe toepassingen of bureau blad                                      | Neem contact op met het team dat verantwoordelijk is voor het desbetreffende product als er problemen zijn gekoppeld aan een specifieke toepassing of product. |
| Licentie berichten of-fouten                                                          | Neem contact op met het team dat verantwoordelijk is voor het desbetreffende product als er problemen zijn gekoppeld aan een specifieke toepassing of product. |
| Problemen met het gebruik van virtuele bureau blad-hulpprogram ma's van Windows op GitHub (Azure Resource Manager sjablonen, diagnostische hulp programma, beheer hulpprogramma) | Zie [Azure Resource Manager sjablonen voor extern bureaublad-services voor](https://github.com/Azure/RDS-Templates/blob/master/README.md) het melden van problemen. |

## <a name="next-steps"></a>Volgende stappen

- Zie [Tenant en hostgroep maken](troubleshoot-set-up-issues.md)voor informatie over het oplossen van problemen bij het maken van een Tenant en een hostgroep in een virtueel-bureaublad omgeving van Windows.
- Zie voor het oplossen van problemen bij het configureren van een virtuele machine (VM) in Windows virtueel bureau blad de [virtuele machine configuratie](troubleshoot-vm-configuration.md)van de host.
- Zie [Windows Virtual Desktop Service Connections](troubleshoot-service-connection.md)(Engelstalig) voor het oplossen van problemen met Windows Virtual Desktop-Client verbindingen.
- Zie [problemen met de Extern bureaublad-client oplossen](troubleshoot-client.md) om problemen met extern bureaublad-clients op te lossen
- Zie [Windows Virtual Desktop Power shell](troubleshoot-powershell.md)(Engelstalig) voor informatie over het oplossen van problemen met het gebruik van Power shell met Windows virtueel bureau blad.
- Zie [Windows Virtual Desktop Environment](environment-setup.md)(Engelstalig) voor meer informatie over de service.
- Zie [zelf studie: problemen met implementaties van Resource Manager-sjablonen oplossen](../azure-resource-manager/resource-manager-tutorial-troubleshoot.md)om de zelf studie voor problemen oplossen op te lossen.
- Zie [bewerkingen controleren met Resource Manager](../azure-resource-manager/resource-group-audit.md)voor meer informatie over controle acties.
- Zie [implementatie bewerkingen weer geven](../azure-resource-manager/resource-manager-deployment-operations.md)voor meer informatie over acties om fouten te bepalen tijdens de implementatie.
