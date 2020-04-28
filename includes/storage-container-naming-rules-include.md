---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: aeca3550b5fc58694779e7e54ce6ca547ba30e17
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "67176650"
---
Elke blob in Azure-opslag moet zich in een container bevinden. De container maakt deel uit van de blob-naam. Bijvoorbeeld, `mycontainer` is de naam van de container in deze voorbeelden van blob-URI's:

    https://storagesample.blob.core.windows.net/mycontainer/blob1.txt
    https://storagesample.blob.core.windows.net/mycontainer/photos/myphoto.jpg

Een containernaam moet een geldige DNS-naam zijn die voldoet aan de volgende naamgevingsregels:

1. Containernamen moeten beginnen met een letter of cijfer en mogen alleen letters, cijfers en het streepje (-) bevatten.
2. Elk streepje (-) moet direct worden voorafgegaan en worden gevolgd door een letter of cijfer; opeenvolgende streepjes zijn in containernamen niet toegestaan.
3. Alle letters in een containernaam moeten kleine letters zijn.
4. Containernamen moeten 3 tot 63 tekens lang zijn.

> [!IMPORTANT]
> De naam van een container bestaat alleen uit kleine letters. Als u een hoofdletter in een containernaam opneemt of de naamgevingsregels op een andere manier schendt, wordt mogelijk een 400-fout (ongeldige aanvraag) weergegeven. 
> 
> 

