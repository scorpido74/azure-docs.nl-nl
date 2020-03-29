---
title: Configureren en gebruiken van de Storage Emulator met Visual Studio | Microsoft Documenten
description: Configureren en gebruiken van de opslagemulator, een hulpprogramma dat de opslagservices voor blobs, wachtrijen en tabeldie beschikbaar is in Azure op uw lokale ontwikkelingsmachine simuleert.
services: visual-studio-online
author: ghogen
manager: jillfra
assetId: c8e7996f-6027-4762-806e-614b93131867
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 8/17/2017
ms.author: ghogen
ms.openlocfilehash: a6f853924416cce2440ca15767044029b20e651f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75450731"
---
# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>De Storage Emulator configureren en gebruiken met Visual Studio

[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Overzicht

De Azure SDK-ontwikkelomgeving bevat de opslagemulator, een hulpprogramma dat de opslagservices voor blobs, wachtrijen en tabeldie beschikbaar zijn in Azure op uw lokale ontwikkelingsmachine simuleert. Als u een cloudservice bouwt die gebruik maakt van de Azure-opslagservices of een externe toepassing schrijft die de opslagservices aanroept, u uw code lokaal testen op basis van de opslagemulator. De Azure Tools voor Microsoft Visual Studio integreren het beheer van de opslagemulator in Visual Studio. De Azure Tools initialiseren de opslagemulatordatabase bij het eerste gebruik, starten de opslagemulatorservice wanneer u uw code uitvoert of debugt vanuit Visual Studio en biedt alleen-lezen toegang tot de opslagemulatorgegevens via de Azure Storage Explorer.

Zie [De Azure Storage Emulator gebruiken voor ontwikkeling en testen voor](storage/common/storage-use-emulator.md)gedetailleerde informatie over de opslagemulator, inclusief systeemvereisten en aangepaste configuratie-instructies.

> [!NOTE]
> Er zijn enkele verschillen in functionaliteit tussen de simulatie van de opslagemulator en de Azure-opslagservices. Zie [verschillen tussen de Storage Emulator en Azure Storage Services](storage/common/storage-use-emulator.md) in de Azure SDK-documentatie voor informatie over de specifieke verschillen.

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>Een verbindingstekenreeks configureren voor de opslagemulator

Als u toegang wilt krijgen tot de opslagemulator vanuit code binnen een rol, wilt u een verbindingstekenreeks configureren die naar de opslagemulator verwijst en die later kan worden gewijzigd om naar een Azure-opslagaccount te wijzen. Een verbindingstekenreeks is een configuratie-instelling die uw rol tijdens runtime kan lezen om verbinding te maken met een opslagaccount. Zie [Azure Storage-verbindingstekenreeksen configureren](/azure/storage/common/storage-configure-connection-string)voor meer informatie over het maken van verbindingstekenreeksen.

> [!NOTE]
> U een verwijzing naar het opslagemulatoraccount van uw code retourneren met de eigenschap **DevelopmentStorageAccount.** Deze aanpak werkt correct als u toegang wilt krijgen tot de opslagemulator van uw code, maar als u van plan bent uw toepassing naar Azure te publiceren, moet u een verbindingstekenreeks maken om toegang te krijgen tot uw Azure-opslagaccount en uw code wijzigen om die verbinding te gebruiken tekenreeks voordat u het publiceert. Als u regelmatig schakelt tussen het opslagemulatoraccount en een Azure-opslagaccount, vereenvoudigt een verbindingstekenreeks dit proces.

## <a name="initializing-and-running-the-storage-emulator"></a>De opslagemulator initialiseren en uitvoeren

U opgeven dat Visual Studio de opslagemulator automatisch start wanneer u uw service uitvoert of debugt in Visual Studio. Open in Solution Explorer het snelmenu voor uw **Azure-project** en kies **Eigenschappen**. Kies op het tabblad **Ontwikkeling** in de lijst **Azure Storage Emulator starten** de optie **Waar** (als deze nog niet is ingesteld op die waarde).  Sommige projecttypen hebben het tabblad **Ontwikkeling** niet. Als dat het geval is, u het opstarten `StartDevelopmentStorage` van de opslagemulator in- of uitschakelen door het element in het projectbestand in te stellen. Stel het in op **True** om het in te schakelen, of **False** om het uit te schakelen.  Open bijvoorbeeld in een Azure Functions-project het projectbestand voor het bewerken en wijzigt u de XML-code als volgt:

```xml
  <PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
    <AzureFunctionsVersion>v2</AzureFunctionsVersion>
    <StartDevelopmentStorage>True</StartDevelopmentStorage>
  </PropertyGroup>
```

De eerste keer dat u uw service uitvoert of debugt vanuit Visual Studio, start de opslagemulator een initialisatieproces. Dit proces reserveert lokale poorten voor de opslagemulator en maakt de opslagemulatordatabase. Zodra dit proces is voltooid, hoeft dit niet opnieuw te worden uitgevoerd, tenzij de opslagemulatordatabase is verwijderd.

> [!NOTE]
> Vanaf de release van de Azure Tools in juni 2012 wordt de opslagemulator standaard uitgevoerd in SQL Express LocalDB. In eerdere versies van de Azure Tools wordt de opslagemulator uitgevoerd tegen een standaardexemplaar van SQL Express 2005 of 2008, dat u moet installeren voordat u de Azure SDK installeren. U de opslagemulator ook uitvoeren tegen een benoemde instantie van SQL Express of een benoemde of standaardinstantie van Microsoft SQL Server. Zie [De Azure Storage Emulator gebruiken voor ontwikkeling en testen](storage/common/storage-use-emulator.md)als u de opslagemulator moet configureren om te worden uitgevoerd tegen een andere instantie dan de standaardinstantie.

De opslagemulator biedt een gebruikersinterface om de status van de lokale opslagservices te bekijken en deze te starten, te stoppen en opnieuw in te stellen. Zodra de opslagemulatorservice is gestart, u de gebruikersinterface weergeven of de service starten of stoppen door met de rechtermuisknop op het pictogram van het meldingsgebied voor de Microsoft Azure Emulator in de Windows-taakbalk te klikken.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>Opslagemulatorgegevens weergeven in Server Explorer

Met het Azure Storage-knooppunt in Server Explorer u gegevens weergeven en instellingen wijzigen voor blob- en tabelgegevens in uw opslagaccounts, waaronder de opslagemulator. Zie [Azure Blob Storage Resources beheren met Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs) voor meer informatie.

