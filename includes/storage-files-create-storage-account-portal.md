---
title: storage-files-create-storage-account-portal
description: Een opslagaccount maken voor Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 03/28/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: d4054760c77a7a70b7ed84a9f95b88a3bcf2bda3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76021031"
---
Een opslagaccount is een gedeelde opslaggroep waarin u Azure-bestandsshares of andere opslagresources, zoals blobs of wachtrijen, kunt implementeren. Een opslagaccount kan een onbeperkt aantal shares bevatten. Een share kan een onbeperkt aantal bestanden opslaan, tot de capaciteitslimiet van het opslagaccount.

Een opslagaccount maken:

1. Selecteer in het **+** linkermenu om een resource te maken.
2. Typ **opslagaccount** in het zoekvak en selecteer **Opslagaccount - blob, bestand, tabel, wachtrij** en klik vervolgens op **Maken**.
    ![Een schermopname van hoe de invoer voor het opslagaccount eruit moet zien in het dialoogvenster voor het zoeken van resources](../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png)

3. Typ *mystorageacct* in het vak **Naam**, gevolgd door een paar willekeurige cijfers totdat u een groen vinkje ziet en u weet dat dit een unieke naam is. De naam van een opslagaccount mag alleen kleine letters bevatten en moet globaal uniek zijn. Noteer de naam van het opslagaccount. U gebruikt dit later. 
4. Laat **in het implementatiemodel**de standaardwaarde van **Resourcebeheer achter**. Meer informatie over de verschillen tussen Azure Resource Manager en de klassieke implementatie vindt u in [Implementatiemodellen en de status van uw resources begrijpen](../articles/azure-resource-manager/management/deployment-models.md).
5. Selecteer **StorageV2** bij **Soort account**. Zie [Azure Storage-accounts begrijpen](../articles/storage/common/storage-account-options.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) voor meer informatie over de verschillende soorten opslagaccounts.
6. Laat bij **Prestaties** de standaardwaarde **Standard** geselecteerd. Azure Files ondersteunt momenteel alleen standaardopslag. Zelfs als u Azure Premium Storage selecteert, wordt de bestandsshare opgeslagen in standaardopslag.
7. Selecteer **Lokaal redundante opslag (LRS)** bij **Replicatie**. 
8. Wij adviseren om bij **Veilige overdracht vereist** altijd **Ingeschakeld** te selecteren. Zie [Versleuteling in-transit begrijpen](../articles/storage/common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) voor meer informatie over deze optie.
9. Selecteer bij **Abonnement** het abonnement waarin u het opslagaccount wilt maken. Als u slechts één abonnement hebt, wordt dit abonnement standaard weergegeven.
10. Selecteer voor **Resourcegroep** de optie **Nieuwe maken**. Voer als naam *myResourceGroup* in.
11. Selecteer bij **Locatie****VS - oost**.
12. Laat bij **Virtuele netwerken** de standaardoptie **Uitgeschakeld** staan. 
13. Selecteer **Vastmaken aan dashboard** om het opslagaccount gemakkelijker te vinden.
14. Als u klaar bent, klikt u op **Maken** om de implementatie te starten.