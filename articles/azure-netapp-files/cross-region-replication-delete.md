---
title: Replicaties voor Azure NetApp Files replicatie tussen regio's verwijderen | Microsoft Docs
description: Hierin wordt beschreven hoe u een replicatie verbinding verwijdert die niet meer nodig is tussen de bron-en de doel volumes.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: 0904ac36a9453e51dbb1efc50eee2b9bf3c669c7
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708661"
---
# <a name="delete-replications"></a>Replicaties verwijderen

U kunt de replicatie verbinding tussen de bron-en doel volumes beëindigen door de volume replicatie te verwijderen. U kunt de Verwijder bewerking uitvoeren vanaf de bron-of het doel volume. De Verwijder bewerking verwijdert alleen de verificatie van de replicatie. de bron of het doel volume wordt niet verwijderd. 

## <a name="steps"></a>Stappen

1. Selecteer **replicatie** van het bron-of doel volume om de volume replicatie te verwijderen.  

2. Klik op **Verwijderen**.    

3. Bevestig de verwijdering door **Ja** te typen en op **verwijderen**te klikken.   

    ![Replicatie verwijderen](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="next-steps"></a>Volgende stappen  

* [Replicatie tussen regio's](cross-region-replication-introduction.md)
* [Vereisten en overwegingen voor het gebruik van replicatie tussen regio's](cross-region-replication-requirements-considerations.md)
* [Status van replicatie relatie weer geven](cross-region-replication-display-health-status.md)
* [Problemen met kruis regio's oplossen-replicatie](troubleshoot-cross-region-replication.md)

