---
title: Een Azure Event Hubs-schema register maken
description: In dit artikel wordt beschreven hoe u een schema register maakt in een Azure Event Hubs-naam ruimte.
ms.topic: how-to
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: b977ed8d6b4444daa1e125c61a8d38b2a6512e44
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91566360"
---
# <a name="create-an-azure-event-hubs-schema-registry-preview"></a>Een Azure Event Hubs-schema register maken (preview)
In dit artikel wordt beschreven hoe u een schema groep maakt met schema's in een schema register dat wordt gehost door Azure Event Hubs. Zie [Azure schema Registry in Event hubs](schema-registry-overview.md)voor een overzicht van de schema register functie van Azure Event hubs.

> [!NOTE]
> - De **schema register** functie is momenteel beschikbaar als **Preview-versie**en wordt niet aanbevolen voor productie werkbelastingen.
> - De functie is alleen beschikbaar in de lagen **Standard** en **dedicated** , niet in de laag **Basic** .
> - Deze preview is momenteel alleen beschikbaar in de regio **West-Centraal VS** . 

## <a name="prerequisites"></a>Vereisten
[Maak een event hubs naam ruimte](event-hubs-create.md#create-an-event-hubs-namespace). U kunt ook een bestaande naam ruimte gebruiken. 

## <a name="create-a-schema-group"></a>Een schema groep maken
1. Ga naar de pagina met de **Event hubs naam ruimte** . 
1. Selecteer **schema register** in het menu links. Als u een schema groep wilt maken, selecteert u in de werk balk de optie **+ schema groep** . 

    :::image type="content" source="./media/create-schema-registry/namespace-page.png" alt-text="Schema register pagina":::
1. Voer de volgende stappen uit op de pagina **schema groep maken** :
    1. Voer een **naam** in voor de schema groep.
    1. Kies voor **type serialisatie**een serialisatie-indeling die van toepassing is op alle schema's in de schema groep. Momenteel is **Avro** het enige type dat wordt ondersteund. Selecteer dus **Avro**. 
    1. Selecteer een **compatibiliteits modus** voor alle schema's in de groep. Voor **Avro**worden de modi voor voorwaartse en achterwaartse compatibiliteit ondersteund. 
    1. Selecteer vervolgens **maken** om de schema groep te maken. 
1. Selecteer de naam van de **schema groep** in de lijst met schema groepen.

    :::image type="content" source="./media/create-schema-registry/select-schema-group.png" alt-text="Schema register pagina":::    
1. U ziet de pagina **schema groep** voor de groep.

    :::image type="content" source="./media/create-schema-registry/schema-group-page.png" alt-text="Schema register pagina":::
    

## <a name="add-a-schema-to-the-schema-group"></a>Een schema toevoegen aan de schema groep
In deze sectie voegt u een schema aan de schema groep toe met behulp van de Azure Portal. 

1. Selecteer op de pagina **schema groep** **+ schema** op de werk balk. 
1. Voer de volgende stappen uit op de pagina **schema maken** :
    1. Voer een **naam** in voor het schema.
    1. Voer het volgende **schema** in het tekstvak in. U kunt ook bestand selecteren met het schema.
    
        ```json
        {
            "type": "record",
            "name": "AvroUser",
            "namespace": "com.azure.schemaregistry.samples",
            "fields": [
                {
                    "name": "name",
                    "type": "string"
                },
                {
                    "name": "favoriteNumber",
                    "type": "int"
                }
            ]
        }
        ```
    1. Selecteer **Maken**. 
1. Selecteer het **schema** in de lijst met schema's. 

    :::image type="content" source="./media/create-schema-registry/select-schema.png" alt-text="Schema register pagina":::
1. U ziet de **schema-overzichts** pagina voor het schema. 

    :::image type="content" source="./media/create-schema-registry/schema-overview-page.png" alt-text="Schema register pagina":::    
1. Als er meerdere versies van een schema zijn, ziet u deze in de vervolg keuzelijst **versies** . Selecteer een versie om over te scha kelen naar dat versie schema. 

## <a name="create-a-new-version-of-schema"></a>Een nieuwe versie van schema maken

1. Werk het schema bij in het tekstvak en selecteer **valideren**. In het volgende voor beeld is een nieuw veld `id` toegevoegd aan het schema. 

    :::image type="content" source="./media/create-schema-registry/update-schema.png" alt-text="Schema register pagina":::    
    
1. Controleer de validatie status en wijzigingen en selecteer **Opslaan**. 

    :::image type="content" source="./media/create-schema-registry/compare-save-schema.png" alt-text="Schema register pagina":::     
1. U ziet dat `2` is geselecteerd voor de **versie** op de **overzichts** pagina van het schema. 

    :::image type="content" source="./media/create-schema-registry/new-version.png" alt-text="Schema register pagina":::    
1. Selecteren `1` om de versie 1 van het schema weer te geven. 

    :::image type="content" source="./media/create-schema-registry/select-version.png" alt-text="Schema register pagina":::    


## <a name="next-steps"></a>Volgende stappen
Zie [Azure schema Registry in Event hubs](schema-registry-overview.md)voor meer informatie over schema register.

