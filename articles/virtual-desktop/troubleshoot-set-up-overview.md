---
title: Overzicht van het oplossen van problemen met Windows virtueel bureau blad, feedback en ondersteuning-Azure
description: Een overzicht van het oplossen van problemen bij het instellen van een Windows Virtual Desktop-Tenant omgeving.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: a31750d888410b9bdaea28bc5153152c426f1915
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877549"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Probleemoplossingsoverzicht, feedback en ondersteuning

Dit artikel bevat een overzicht van de problemen die zich kunnen voordoen bij het instellen van een Windows Virtual Desktop-Tenant omgeving en biedt manieren om de problemen op te lossen.

## <a name="provide-feedback"></a>Feedback geven

Er worden momenteel geen ondersteunings kwesties in rekening gebracht terwijl het virtuele bureau blad van Windows in preview is. Ga naar de [technische community van Windows virtueel bureau blad](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) voor het bespreken van de Windows Virtual Desktop-service met het product team en de actieve leden van de community.

## <a name="escalation-tracks"></a>Escalatie trajecten

Gebruik de volgende tabel voor het identificeren en oplossen van problemen die zich kunnen voordoen bij het instellen van een Tenant omgeving met Extern bureaublad-client. Zodra de Tenant is ingesteld, kunt u onze nieuwe service voor [Diagnostische gegevens](https://docs.microsoft.com/azure/virtual-desktop/diagnostics-role-service) gebruiken om problemen voor veelvoorkomende scenario's te identificeren.

>[!NOTE]
>Er worden momenteel geen ondersteunings kwesties in rekening gebracht terwijl het virtuele bureau blad van Windows in preview is. Wanneer we verwijzen naar ondersteuning voor virtueel bureau blad van Windows, gaat u naar ons Tech Community-Forum voor Taan. Ga naar de [technische community van Windows virtueel bureau blad](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) om problemen met het product team en de actieve communityleden te bespreken. Als u een ondersteunings probleem moet oplossen, neemt u de activiteits-ID en het geschatte tijds bestek op wanneer het probleem is opgetreden.

| **Name**                                                            | **Voorgestelde oplossing**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Een Tenant maken                                                    | Als er een storing in azure optreedt, neemt u contact op met de [ondersteuning van Azure](https://azure.microsoft.com/support/options/). Neem anders contact op met **extern bureaublad-services/ondersteuning voor virtueel bureau blad van Windows**.|
| Toegang tot Marketplace-sjablonen in Azure Portal       | Als er een onderbreking van Azure is, neemt u contact op met de [ondersteuning van Azure](https://azure.microsoft.com/support/options/). <br> <br> Azure Marketplace-sjablonen voor virtueel bureau blad zijn gratis beschikbaar.|
| Toegang tot Azure Resource Manager sjablonen vanuit GitHub                                  | Zie de sectie ' virtuele bureau blad-sessiehost Vm's maken ' van [Tenant en hostgroep maken](troubleshoot-set-up-issues.md). Als het probleem nog steeds niet is opgelost, neemt u contact op met het ondersteunings [team van github](https://github.com/contact). <br> <br> Als de fout optreedt nadat u de sjabloon in GitHub hebt geopend, neemt u contact op met de [ondersteuning van Azure](https://azure.microsoft.com/support/options/).|
| Instellingen voor de Azure Virtual Network (VNET) en Express-route van de Session Host-pool               | Neem contact op met de **ondersteuning van Azure (netwerken)** . |
| De hostgroep van de virtuele machine (VM) maken wanneer Azure Resource Manager sjablonen die worden meegeleverd met Windows virtueel bureau blad niet worden gebruikt | Neem contact op met de **ondersteuning van Azure (Compute)** . <br> <br> Zie voor problemen met de Azure Resource Manager sjablonen die worden meegeleverd met Windows virtueel bureau blad maken de sectie Tenant voor Windows-virtueel bureau blad voor het [maken van Tenant-en hostgroepen](troubleshoot-set-up-issues.md). |
| Windows Virtual Desktop Session Host-omgeving beheren vanuit de Azure Portal    | Neem contact op met de **ondersteuning van Azure**. <br> <br> Zie [Windows Virtual Desktop Power shell](troubleshoot-powershell.md) (Engelstalig) of neem contact op met het ondersteunings team voor **virtuele bureau bladen van extern bureaublad-services/Windows**voor problemen met het beheer van extern bureaublad-services/Windows Virtual Desktop Power shell. |
| Virtuele Windows-bureaublad configuratie beheren die zijn gekoppeld aan host Pools en toepassings groepen (app-groepen)      | Zie [Windows Virtual Desktop Power shell](troubleshoot-powershell.md)of neem contact op met het ondersteunings **team van extern bureaublad-services/Windows Virtual Desktop**. <br> <br> Als er problemen zijn gekoppeld aan de voorbeeld Graphical User Interface (GUI), neem dan contact op met de Yammer-community.|
| Storing van extern bureau blad-clients bij starten                                                 | Zie [extern bureaublad client verbindingen](troubleshoot-client-connection.md) en als het probleem niet wordt opgelost, neemt u contact op met **extern bureaublad-services/het Windows-ondersteunings team voor virtueel bureau blad**.  <br> <br> Als het een netwerk probleem is, moeten uw gebruikers contact opnemen met de netwerk beheerder. |
| Verbonden, maar geen feed                                                                 | Problemen oplossen met behulp van de sectie ' Gebruiker connects, maar er wordt niets weer gegeven (geen feed) ' van [extern bureaublad-client verbindingen](troubleshoot-client-connection.md). <br> <br> Als uw gebruikers zijn toegewezen aan een app-groep, gaat u naar het **Windows-ondersteunings team voor extern bureaublad-services/virtueel bureau blad**. |
| Problemen met de detectie van feeds door het netwerk                                            | Uw gebruikers moeten contact opnemen met hun netwerk beheerder. |
| Clients verbinden                                                                    | Zie [extern bureaublad client verbindingen](troubleshoot-client-connection.md) . Als u het probleem niet kunt oplossen, raadpleegt u de [Virtual machine-configuratie van de sessie host](troubleshoot-vm-configuration.md). |
| Reactie tijd van externe toepassingen of bureau blad                                      | Neem contact op met het team dat verantwoordelijk is voor het desbetreffende product als er problemen zijn gekoppeld aan een specifieke toepassing of product. |
| Licentie berichten of-fouten                                                          | Neem contact op met het team dat verantwoordelijk is voor het desbetreffende product als er problemen zijn gekoppeld aan een specifieke toepassing of product. |

## <a name="next-steps"></a>Volgende stappen

- Zie [Tenant en hostgroep maken](troubleshoot-set-up-issues.md)voor informatie over het oplossen van problemen bij het maken van een Tenant en een hostgroep in een virtueel-bureaublad omgeving van Windows.
- Zie voor het oplossen van problemen bij het configureren van een virtuele machine (VM) in Windows virtueel bureau blad de [virtuele machine configuratie](troubleshoot-vm-configuration.md)van de host.
- Zie [extern bureaublad-client verbindingen](troubleshoot-client-connection.md)voor het oplossen van problemen met Windows-client verbindingen met virtueel bureau blad.
- Zie [Windows Virtual Desktop Power shell](troubleshoot-powershell.md)(Engelstalig) voor informatie over het oplossen van problemen met het gebruik van Power shell met Windows virtueel bureau blad.
- Zie voor meer informatie over de preview-service de [Windows-voorbeeld omgeving voor virtueel bureau blad](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Zie [zelf studie voor het oplossen van problemen met de zelf studie: Problemen oplossen met implementaties](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)van Resource Manager-sjablonen.
- Zie [bewerkingen controleren met Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)voor meer informatie over controle acties.
- Zie [implementatie bewerkingen weer geven](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)voor meer informatie over acties om de fouten te bepalen tijdens de implementatie.