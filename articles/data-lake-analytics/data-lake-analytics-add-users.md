---
title: Gebruikers toevoegen aan een Azure Data Lake Analytics-account
description: Meer informatie over het correct toevoegen van gebruikers aan uw Data Lake Analytics-account met behulp van de wizard gebruiker toevoegen en Azure PowerShell.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: db35f16e-1565-4873-a851-bd987accdc58
ms.topic: conceptual
ms.date: 05/24/2018
ms.openlocfilehash: 16c503fe2d584d5f8256c65bfc49825b300f6a36
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672731"
---
# <a name="adding-a-user-in-the-azure-portal"></a>Een gebruiker toevoegen in de Azure-portal

## <a name="start-the-add-user-wizard"></a>De wizard gebruiker toevoegen starten
1. Open uw Azure Data Lake Analytics via https://portal.azure.com.
2. Klik op **wizard gebruiker toevoegen**.
3. Zoek in de stap **gebruiker selecteren** de gebruiker die u wilt toevoegen. Klik op **Selecteren**.
4. de stap **functie selecteren** , kies **Data Lake Analytics ontwikkel aars**. Deze rol heeft de minimale set machtigingen die nodig zijn om U-SQL-taken te verzenden/controleren/beheren. Toewijzen aan deze rol als de groep niet is bedoeld voor het beheren van Azure-Services.
5. Selecteer in de stap **machtigingen voor catalogus selecteren** de aanvullende data bases waartoe de gebruiker toegang moet hebben. Lees-en schrijf toegang tot de hoofd database is vereist voor het verzenden van taken. Klik als u klaar bent op **OK**.
6. Bekijk in de laatste stap de naam **Assign selected permissions** de wijzigingen die door de wizard worden aangebracht. Klik op **OK**.


## <a name="configure-acls-for-data-folders"></a>Acl's voor gegevens mappen configureren
Ken ' R-X ' of ' LSU ', indien nodig, toe aan mappen met invoer gegevens en uitvoer gegevens.


## <a name="optionally-add-the-user-to-the-azure-data-lake-storage-gen1-role-reader-role"></a>U kunt de gebruiker eventueel toevoegen aan **de rol van Azure data Lake Storage gen1 rol.**
1.  Zoek uw Azure Data Lake Storage Gen1-account.
2.  Klik op **gebruikers**.
3. Klik op **Toevoegen**.
4.  Selecteer een Azure RBAC-rol om deze groep toe te wijzen.
5.  Toewijzen aan rol van lezer. Deze rol heeft de minimale set machtigingen die zijn vereist voor het bladeren door en het beheren van gegevens die zijn opgeslagen in ADLSGen1. Toewijzen aan deze rol als de groep niet is bedoeld voor het beheren van Azure-Services.
6.  Typ de naam van de groep.
7.  Klik op **OK**.

## <a name="adding-a-user-using-powershell"></a>Een gebruiker toevoegen met Power shell

1. Volg de instructies in deze hand leiding: [Azure PowerShell installeren en configureren](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Down load het Power shell-script [add-AdlaJobUser. ps1](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1) .
3. Voer het Power shell-script uit. 

De voorbeeld opdracht om gebruikers toegang te geven tot het verzenden van taken, het weer geven van de meta gegevens van nieuwe taken en het weer geven van oude meta gegevens is:

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Aan de slag met Data Lake Analytics met behulp van de Azure Portal](data-lake-analytics-get-started-portal.md)
* [Azure Data Lake Analytics beheren met Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

