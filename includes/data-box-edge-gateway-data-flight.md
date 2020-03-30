---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176672"
---
Voor gegevens tijdens de vlucht:

- Standaard TLS 1.2 wordt gebruikt voor gegevens die tussen het apparaat en Azure worden verplaatst. Er is geen terugval naar TLS 1.1 en eerder. Agentcommunicatie wordt geblokkeerd als TLS 1.2 niet wordt ondersteund. TLS 1.2 is ook vereist voor portal- en SDK-beheer.
- Wanneer clients toegang krijgen tot uw apparaat via de lokale webgebruikersinterface van een browser, wordt standaard TLS 1.2 gebruikt als het standaard beveiligde protocol.

    - Het beste is om uw browser te configureren om TLS 1.2 te gebruiken.
    - Als de browser TLS 1.2 niet ondersteunt, u TLS 1.1 of TLS 1.0 gebruiken.
- We raden u aan SMB 3.0 met versleuteling te gebruiken om gegevens te beschermen wanneer u deze kopieert vanaf uw gegevensservers.
