---
title: Opc Twin-cloudafhankelijkheden implementeren in Azure | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u de opc twin azure-afhankelijkheden implementeert die nodig zijn voor lokale ontwikkeling en foutopsporing.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 231d1efa02ec80e8ad56a8895d4262d774480111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73824100"
---
# <a name="deploying-dependencies-for-local-development"></a>Afhankelijkheden implementeren voor lokale ontwikkeling

In dit artikel wordt uitgelegd hoe u alleen de Azure Platform Services implementeert die nodig zijn voor lokale ontwikkeling en foutopsporing.   Aan het einde heb je een resourcegroep geïmplementeerd die alles bevat wat je nodig hebt voor lokale ontwikkeling en foutopsporing.

## <a name="deploy-azure-platform-services"></a>Azure-platformservices implementeren

1. Zorg ervoor dat PowerShell- en [AzureRM PowerShell-extensies](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) zijn geïnstalleerd.  Open een opdrachtprompt of terminal en voer het uit:

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. Volg de aanwijzingen om een naam toe te wijzen aan de resourcegroep voor uw implementatie.  Het script implementeert alleen de afhankelijkheden van deze brongroep in uw Azure-abonnement, maar niet de microservices.  Het script registreert ook een toepassing in Azure Active Directory.  Dit is nodig om OAUTH-gebaseerde authenticatie te ondersteunen.  Implementatie kan enkele minuten duren.

3. Zodra het script is voltooid, u ervoor kiezen om het .env-bestand op te slaan.  Het .env-omgevingsbestand is het configuratiebestand van alle services en hulpprogramma's die u op uw ontwikkelmachine wilt uitvoeren.  

## <a name="troubleshooting-deployment-failures"></a>Implementatiefouten oplossen

### <a name="resource-group-name"></a>Naam van de resourcegroep

Zorg ervoor dat u een korte en eenvoudige naam van de resourcegroep gebruikt.  De naam wordt ook gebruikt om resources als zodanig een naam te geven en moet voldoen aan de vereisten voor resourcenaamgeving.  

### <a name="azure-active-directory-aad-registration"></a>Azure Active Directory (AAD) registratie

Het implementatiescript probeert AAD-toepassingen te registreren in Azure Active Directory.  Afhankelijk van uw rechten op de geselecteerde AAD-tenant kan dit mislukken.   Er zijn drie opties:

1. Als u een AAD-tenant hebt gekozen uit een lijst met tenants, start u het script opnieuw en kiest u een ander script uit de lijst.
2. U ook een private AAD-tenant implementeren, het script opnieuw starten en selecteren om het te gebruiken.
3. Doorgaan zonder verificatie.  Aangezien u uw microservices lokaal uitvoert, is dit acceptabel, maar bootst dit productieomgevingen niet na.  

## <a name="next-steps"></a>Volgende stappen

Nu u OPC Twin-services met succes hebt geïmplementeerd voor een bestaand project, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Meer informatie over het implementeren van OPC Twin-modules](howto-opc-twin-deploy-modules.md)
