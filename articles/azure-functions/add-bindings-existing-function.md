---
title: Bindingen toevoegen aan een bestaande functie in Azure Functions
description: Meer informatie over het toevoegen van bindingen aan een bestaande functie in uw Azure Functions-project.
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: 23b7b05f9a9f9da5a48511ee555e3b6184a74179
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91654122"
---
# <a name="add-bindings-to-an-existing-function-in-azure-functions"></a>Bindingen toevoegen aan een bestaande functie in Azure Functions

Wanneer u een functie maakt, wordt de taalspecifieke trigger code toegevoegd aan het project vanuit een set trigger sjablonen. Als u uw functie wilt verbinden met andere services met behulp van invoer-of uitvoer bindingen, moet u in uw functie specifieke bindings definities toevoegen. Zie [Concepten van Azure Functions-triggers en -bindingen](functions-triggers-bindings.md) voor meer informatie over bindingen.

## <a name="local-development"></a>Lokale ontwikkeling       

Wanneer u lokaal functies ontwikkelt, moet u de functie code bijwerken om bindingen toe te voegen. Met Visual Studio code kunt u het eenvoudiger maken om bindingen aan een functie toe te voegen.  

### <a name="visual-studio-code"></a>Visual Studio Code

Wanneer u Visual Studio code gebruikt om uw functie te ontwikkelen en uw functie gebruikmaakt van een function.jsin het bestand, kan de uitbrei ding van de Azure Functions automatisch een binding toevoegen aan een bestaande function.jsin het bestand. Zie [invoer-en uitvoer bindingen toevoegen](functions-develop-vs-code.md#add-input-and-output-bindings)voor meer informatie.   

### <a name="manually-add-bindings-based-on-examples"></a>Hand matig bindingen toevoegen op basis van voor beelden

Wanneer u een binding aan een bestaande functie toevoegt, moet u zowel de functie code als de function.jsin het configuratie bestand bijwerken als deze wordt gebruikt door uw taal. De .NET-klassen bibliotheek en Java-functies gebruiken kenmerken in plaats van function.jsop, dus u moet dat in plaats daarvan bijwerken.

Gebruik de volgende tabel om voor beelden te vinden van specifieke bindings typen die u kunt gebruiken om u te helpen bij het bijwerken van een bestaande functie. Kies eerst het tabblad taal dat overeenkomt met uw project. 

[!INCLUDE [functions-bindings-code-example-chooser](../../includes/functions-bindings-code-example-chooser.md)]

## <a name="azure-portal"></a>Azure Portal

Wanneer u uw functies in de [Azure Portal](https://portal.azure.com)ontwikkelt, voegt u invoer-en uitvoer bindingen toe op het tabblad **integreren** voor een bepaalde functie. De nieuwe bindingen worden toegevoegd aan de function.jsop bestand of aan de kenmerken van de methode, afhankelijk van uw taal. In de volgende artikelen ziet u voor beelden van het toevoegen van bindingen aan een bestaande functie in de portal:

+ [Uitvoer binding wachtrij opslag](functions-integrate-storage-queue-output-binding.md)
+ [Uitvoer binding Azure Cosmos DB](functions-integrate-store-unstructured-data-cosmosdb.md)

## <a name="next-steps"></a>Volgende stappen

+ [Concepten van Azure Functions-triggers en -bindingen](functions-triggers-bindings.md)
