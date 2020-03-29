---
title: Gebruikers toevoegen aan een Azure Data Lake Analytics-account
description: Meer informatie over het correct toevoegen van gebruikers aan uw Data Lake Analytics-account met de wizard Gebruiker toevoegen en Azure PowerShell.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: db35f16e-1565-4873-a851-bd987accdc58
ms.topic: conceptual
ms.date: 05/24/2018
ms.openlocfilehash: 16c503fe2d584d5f8256c65bfc49825b300f6a36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71672731"
---
# <a name="adding-a-user-in-the-azure-portal"></a>Een gebruiker toevoegen in de Azure-portal

## <a name="start-the-add-user-wizard"></a>De wizard Gebruiker toevoegen starten
1. Open uw Azure Data https://portal.azure.comLake Analytics via .
2. Klik **op Wizard Gebruiker toevoegen**.
3. Zoek in de **stap Gebruiker selecteren** de gebruiker die u wilt toevoegen. Klik **op Selecteren**.
4. de **rolstap Selecteren,** kies **Data Lake Analytics Developer**. Deze rol heeft de minimale set machtigingen die nodig zijn voor het indienen/controleren/beheren van U-SQL-taken. Wijs deze rol toe als de groep niet bedoeld is voor het beheer van Azure-services.
5. Selecteer in de stap **Catalogusmachtigingen selecteren** eventuele extra databases waartoe de gebruiker toegang nodig heeft. Lees- en schrijftoegang tot de hoofddatabase is vereist om taken in te dienen. Wanneer u klaar bent, klikt u op **OK**.
6. Controleer in de laatste stap **geselecteerde machtigingen toewijzen** de wijzigingen die de wizard zal aanbrengen. Klik op **OK**.


## <a name="configure-acls-for-data-folders"></a>ACL's configureren voor gegevensmappen
Geef indien nodig "R-X" of "RWX" toe op mappen met invoergegevens en uitvoergegevens.


## <a name="optionally-add-the-user-to-the-azure-data-lake-storage-gen1-role-reader-role"></a>Voeg de gebruiker optioneel toe aan de rol **Reader-rol Reader** van Azure Data Lake Storage Gen1.
1.  Zoek uw Azure Data Lake Storage Gen1-account.
2.  Klik op **Gebruikers**.
3. Klik op**toevoegen**.
4.  Selecteer een Azure RBAC-rol om deze groep toe te wijzen.
5.  Toewijzen aan de rol Reader. Deze rol heeft de minimale set machtigingen die nodig zijn om gegevens te bladeren/beheren die zijn opgeslagen in ADLSGen1. Wijs deze rol toe als de groep niet bedoeld is voor het beheer van Azure-services.
6.  Typ de naam van de groep in.
7.  Klik op **OK**.

## <a name="adding-a-user-using-powershell"></a>Een gebruiker toevoegen met PowerShell

1. Volg de instructies in deze handleiding: [Azure PowerShell installeren en configureren.](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)
2. Download het [PowerShell-script add-AdlaJobUser.ps1.](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1)
3. Voer het PowerShell-script uit. 

De voorbeeldopdracht om gebruikers toegang te geven tot het verzenden van taken, het bekijken van nieuwe metagegevens van taken en het bekijken van oude metagegevens is:

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Data Lake Analytics](data-lake-analytics-overview.md)
* [Aan de slag met Data Lake Analytics met behulp van de Azure-portal](data-lake-analytics-get-started-portal.md)
* [Azure Data Lake Analytics beheren met Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

