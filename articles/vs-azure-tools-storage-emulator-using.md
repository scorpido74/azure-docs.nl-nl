---
title: De opslag emulator configureren en gebruiken met Visual Studio | Microsoft Docs
description: Het configureren en gebruiken van de opslag emulator, een hulp programma dat de blob-, wachtrij-en Table Storage-services simuleert die beschikbaar zijn in azure op uw lokale ontwikkel machine.
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
ms.openlocfilehash: aca4bf5017a4ee23d69016b937673443c1a0e200
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73935833"
---
# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>De opslag emulator configureren en gebruiken met Visual Studio
[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Overzicht
De Azure SDK-ontwikkel omgeving bevat de opslag emulator, een hulp programma waarmee de blob-, wachtrij-en Table Storage-services die beschikbaar zijn in Azure worden gesimuleerd op uw lokale ontwikkel machine. Als u een Cloud service bouwt die gebruikmaakt van de Azure Storage-services of een externe toepassing schrijft waarmee de opslag services worden aangeroepen, kunt u uw code lokaal testen op de opslag emulator. Met de Azure-Hulpprogram Ma's voor micro soft Visual Studio wordt het beheer van de opslag emulator geïntegreerd in Visual Studio. De Azure-Hulpprogram Ma's initialiseren de data base van de opslag-emulator bij het eerste gebruik, start de Storage emulator-service wanneer u uw code uitvoert of fouten oplost vanuit Visual Studio en biedt alleen-lezen toegang tot de gegevens van de opslag emulator via de Azure Storage Explorer.

Zie [de Azure Storage-emulator gebruiken voor ontwikkeling en testen](storage/common/storage-use-emulator.md)voor meer informatie over de opslag emulator, inclusief systeem vereisten en aangepaste configuratie-instructies.

> [!NOTE]
> Er zijn enkele verschillen in functionaliteit tussen de simulatie van de opslag emulator en de Azure Storage-services. Bekijk de [verschillen tussen de opslag emulator en de Azure Storage services](storage/common/storage-use-emulator.md) in de Azure SDK-documentatie voor informatie over de specifieke verschillen.
> 
> 

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>Een connection string configureren voor de opslag emulator
Als u toegang wilt krijgen tot de opslag emulator vanuit code binnen een rol, moet u een connection string configureren die naar de opslag emulator verwijst en die later kan worden gewijzigd in een Azure Storage-account. Een connection string is een configuratie-instelling die uw rol tijdens runtime kan lezen om verbinding te maken met een opslag account. Zie [Azure Storage-verbindings reeksen configureren](/azure/storage/common/storage-configure-connection-string)voor meer informatie over het maken van verbindings reeksen.

> [!NOTE]
> U kunt met behulp van de eigenschap **DevelopmentStorageAccount** een verwijzing naar het Storage emulator-account retour neren vanuit uw code. Deze aanpak werkt goed als u vanuit uw code toegang wilt krijgen tot de opslag emulator, maar als u van plan bent om uw toepassing te publiceren naar Azure, moet u een connection string maken voor toegang tot uw Azure-opslag account en uw code wijzigen om deze verbinding te gebruiken teken reeks voordat u deze publiceert. Als u de opslag emulator-account en een Azure-opslag account regel matig omschakelt, wordt dit proces door een connection string vereenvoudigd.
> 
> 

## <a name="initializing-and-running-the-storage-emulator"></a>De opslag emulator initialiseren en uitvoeren
U kunt opgeven dat wanneer u uw service in Visual Studio uitvoert of fouten oplost, Visual Studio de opslag emulator automatisch start. Open in Solution Explorer het snelmenu voor uw **Azure** -project en kies **Eigenschappen**. Kies op het tabblad **ontwikkeling** in de lijst **Start Azure Storage-emulator** de optie **waar** (als deze nog niet is ingesteld op die waarde).

De eerste keer dat u uw service vanuit Visual Studio uitvoert of fouten oplost, wordt door de opslag-emulator een initialisatie proces gestart. Met dit proces worden lokale poorten voor de opslag emulator gereserveerd en wordt de data base van de opslag-emulator gemaakt. Zodra de data base van de opslag-emulator is verwijderd, hoeft dit proces niet opnieuw te worden uitgevoerd.

> [!NOTE]
> Vanaf de release van juni 2012 van de Azure-Hulpprogram Ma's, wordt de opslag-emulator standaard uitgevoerd in SQL Express LocalDB. In eerdere versies van de Azure-Hulpprogram Ma's wordt de opslag-emulator uitgevoerd op basis van een standaard exemplaar van SQL Express 2005 of 2008, dat u moet installeren voordat u de Azure SDK kunt installeren. U kunt ook de opslag emulator uitvoeren op een benoemd exemplaar van SQL Express of een benoemd of standaard exemplaar van Microsoft SQL Server. Als u de opslag emulator moet configureren om te worden uitgevoerd op basis van een ander exemplaar dan het standaard exemplaar, raadpleegt u [de Azure Storage-emulator gebruiken voor ontwikkelen en testen](storage/common/storage-use-emulator.md).
> 
> 

De opslag emulator biedt een gebruikers interface voor het weer geven van de status van de lokale opslag Services en om ze te starten, te stoppen en opnieuw in te stellen. Zodra de Storage emulator-service is gestart, kunt u de gebruikers interface weer geven of de service starten of stoppen door met de rechter muisknop op het pictogram systeemvak te klikken voor de Microsoft Azure emulator in de Windows-taak balk.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>Opslag-emulator gegevens weer geven in Server Explorer
Met het knoop punt Azure Storage in Server Explorer kunt u gegevens weer geven en instellingen wijzigen voor Blob-en tabel gegevens in uw opslag accounts, inclusief de opslag emulator. Zie [Azure Blob Storage-resources beheren met Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs) voor meer informatie.

