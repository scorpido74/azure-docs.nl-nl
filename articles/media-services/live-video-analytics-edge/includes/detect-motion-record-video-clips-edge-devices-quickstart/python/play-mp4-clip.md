---
ms.openlocfilehash: f4a1cc432a50a555fe6e050ca318b4cfaf1092d4
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682187"
---
De MP4-bestanden worden naar een map op het edge-apparaat geschreven die u in het *.env*-bestand hebt geconfigureerd met behulp van deze sleutel: OUTPUT_VIDEO_FOLDER_ON_DEVICE. Als u de standaardwaarde hebt gebruikt, bevinden de resultaten zich in de map */var/media/* .

Om de MP4-clip af te spelen:

1. Ga naar uw resourcegroep, zoek de VM en maak vervolgens verbinding met behulp van Azure Bastion.

    ![Resourcegroep](../../../media/quickstarts/resource-group.png)
    
    ![VM](../../../media/quickstarts/virtual-machine.png)
1. Meld u aan met de aanmeldingsgegevens die zijn gegenereerd bij het [instellen van uw Azure-resources](../../../detect-motion-emit-events-quickstart.md#set-up-azure-resources). 
1. Ga met de opdrachtprompt naar de betreffende map. De standaardlocatie is */var/media*. U vindt de MP4-bestanden in de map.

    ![Uitvoer](../../../media/quickstarts/samples-output.png) 

1. Gebruik [Secure Copy (SCP)](../../../../../virtual-machines/linux/copy-files-to-linux-vm-using-scp.md) om de bestanden naar uw lokale machine te kopiëren. 
1. Speel de bestanden af met [VLC media player](https://www.videolan.org/vlc/) of een andere MP4-speler.
