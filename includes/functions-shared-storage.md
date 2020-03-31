---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.openlocfilehash: 82d122ed236dc72ced7ebafe2301ef5f1143897f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76963649"
---
Gebruik een apart opslagaccount voor elke functie-app om de prestaties te maximaliseren. Dit is vooral belangrijk wanneer u duurzame functies of gebeurtenishub-geactiveerde functies hebt, die beide een groot aantal opslagtransacties genereren. Wanneer uw toepassingslogica samenwerkt met Azure Storage, rechtstreeks (met behulp van de Storage SDK) of via een van de opslagbindingen, moet u een speciaal opslagaccount gebruiken. Als u bijvoorbeeld een functie hebt die door de gebeurtenishub wordt&mdash;geactiveerd bij het schrijven van bepaalde gegevens naar blobopslag, gebruikt u twee opslagaccounts één voor de functie-app en een andere voor de blobs die door de functie worden opgeslagen.