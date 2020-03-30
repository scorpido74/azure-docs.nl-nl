---
title: Een OPC Twin-module implementeren in een bestaand Azure-project | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u OPC Twin implementeren in een bestaand project. U ook leren hoe u implementatiefouten oplossen.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b971ec13c71ccfd7d28ae6987593d09201b9b764
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73824125"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>OPC Twin implementeren in een bestaand project

De OPC Twin-module draait op IoT Edge en biedt verschillende edge-services aan de OPC Twin- en Registry-services.

De OPC Twin microservice vergemakkelijkt de communicatie tussen fabrieksoperators en OPC UA-serverapparaten op de fabrieksvloer via een OPC Twin IoT Edge-module. De microservice legt OPC UA-services (Bladeren, lezen, schrijven en uitvoeren) bloot via de REST API. 

De MICROservice voor het opc-apparaatregister biedt toegang tot geregistreerde OPC UA-toepassingen en hun eindpunten. Operators en beheerders kunnen nieuwe OPC UA-toepassingen registreren en uitschrijven en door de bestaande toepassingen bladeren, inclusief hun eindpunten. Naast applicatie- en eindpuntbeheer catalogiseert de registerservice ook geregistreerde OPC Twin IoT Edge-modules. De service-API biedt u controle over de functionaliteit van de edge-module, bijvoorbeeld het starten of stoppen van serverdetectie (scanservices) of het activeren van nieuwe endpoint twins die toegankelijk zijn met de OPC Twin-microservice.

De kern van de module is de supervisor identiteit. De supervisor beheert endpoint twin, wat overeenkomt met OPC UA-servereindpunten die worden geactiveerd met behulp van de bijbehorende OPC UA-register-API. Deze endpoint twins vertalen OPC UA JSON ontvangen van de OPC Twin microservice draait in de cloud in OPC UA binaire berichten, die worden verzonden via een stateful secure kanaal naar het beheerde eindpunt. De supervisor biedt ook detectieservices die gebeurtenissen voor het ontdekken van apparaten verzenden naar de onboarding-service voor op- en instappen van OPC UA-apparaten voor verwerking, waarbij deze gebeurtenissen resulteren in updates voor het OPC UA-register.  In dit artikel ziet u hoe u de OPC Twin-module implementeren in een bestaand project.

> [!NOTE]
> Zie de [GitHub-repository](https://github.com/Azure/azure-iiot-opc-twin-module)voor meer informatie over implementatiegegevens en -instructies.

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat PowerShell- en [AzureRM PowerShell-extensies](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) zijn geïnstalleerd. Als je dit nog niet hebt gedaan, kloon dan deze GitHub repository. Voer de volgende opdrachten uit in PowerShell:

```powershell
git clone --recursive https://github.com/Azure/azure-iiot-components.git
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>Industriële IoT-services implementeren in Azure

1. Voer in uw PowerShell-sessie het als:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy.cmd
    ```

2. Volg de aanwijzingen om een naam toe te wijzen aan de resourcegroep van de implementatie en een naam aan de website.   Het script implementeert de microservices en hun Azure-platformafhankelijkheden in de brongroep in uw Azure-abonnement.  Het script registreert ook een toepassing in uw Azure Active Directory (AAD)-tenant om verificatie op basis van OAUTH te ondersteunen.  Implementatie duurt enkele minuten.  Een voorbeeld van wat u ziet zodra de oplossing is geïmplementeerd:

   ![Industriële IoT OPC Twin ingezet voor bestaand project](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   De uitvoer bevat de URL van het openbare eindpunt. 

3. Zodra het script is voltooid, selecteert u `.env` of u het bestand wilt opslaan.  U hebt `.env` het omgevingsbestand nodig als u verbinding wilt maken met het eindpunt van de cloud met behulp van hulpprogramma's zoals de console of modules implementeren voor ontwikkeling en foutopsporing.

## <a name="troubleshooting-deployment-failures"></a>Implementatiefouten oplossen

### <a name="resource-group-name"></a>Naam van de resourcegroep

Zorg ervoor dat u een korte en eenvoudige naam van de resourcegroep gebruikt.  De naam wordt ook gebruikt om resources als zodanig een naam te geven en moet voldoen aan de vereisten voor resourcenaamgeving.  

### <a name="website-name-already-in-use"></a>Websitenaam al in gebruik

Het is mogelijk dat de naam van de website al in gebruik is.  Als u tegen deze fout aanloopt, moet u een andere toepassingsnaam gebruiken.

### <a name="azure-active-directory-aad-registration"></a>Azure Active Directory (AAD) registratie

Het implementatiescript probeert twee AAD-toepassingen te registreren in Azure Active Directory.  Afhankelijk van uw rechten op de geselecteerde AAD-tenant kan de implementatie mislukken. Er zijn twee opties:

1. Als u een AAD-tenant hebt gekozen uit een lijst met tenants, start u het script opnieuw en kiest u een ander script uit de lijst.
2. U ook een particuliere AAD-tenant implementeren in een ander abonnement, het script opnieuw starten en selecteren om het te gebruiken.

> [!WARNING]
> GA NOOIT verder zonder verificatie.  Als u ervoor kiest om dit te doen, kan iedereen toegang krijgen tot uw OPC Twin-eindpunten vanaf het internet.   U altijd kiezen voor de [lokale inzet optie](howto-opc-twin-deploy-dependencies.md) om de banden te schoppen.

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>Een alles-in-één demo voor industriële IoT-services implementeren

In plaats van alleen de services en afhankelijkheden u ook een alles-in-één demo implementeren.  De alles-in-één demo bevat drie OPC UA-servers, de OPC Twin-module, alle microservices en een voorbeeldwebtoepassing.  Het is bedoeld voor demonstratiedoeleinden.

1. Zorg ervoor dat je een kloon van de repository hebt (zie hierboven). Open een PowerShell-prompt in de hoofdmap van de opslagplaats en voer het uitvoeren uit:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy -type demo
    ```

2. Volg de aanwijzingen om een nieuwe naam toe te wijzen aan de resourcegroep en een naam aan de website.  Zodra het script succesvol is geïmplementeerd, wordt de URL van het eindpunt van de webtoepassing weergegeven.

## <a name="deployment-script-options"></a>Opties voor implementatiescript

Het script neemt de volgende parameters:

```powershell
-type
```

Het type implementatie (vm, lokaal, demo)

```powershell
-resourceGroupName
```

Kan de naam zijn van een bestaande of een nieuwe resourcegroep.

```powershell
-subscriptionId
```

Optioneel is de abonnements-ID waar resources worden geïmplementeerd.

```powershell
-subscriptionName
```

Of de naam van het abonnement.

```powershell
-resourceGroupLocation
```

Optioneel, een locatie voor resourcegroepen. Als dit is opgegeven, probeert u op deze locatie een nieuwe resourcegroep te maken.

```powershell
-aadApplicationName
```

Een naam voor de AAD-aanvraag om zich onder te registreren.

```powershell
-tenantId
```

AAD huurder te gebruiken.

```powershell
-credentials
```

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u OPC Twin implementeren in een bestaand project, gaat het hier om de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Veilige communicatie van OPC UA Client en OPC UA PLC](howto-opc-vault-secure.md)
