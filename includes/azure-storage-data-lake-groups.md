---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 73a8f20122e21c4557289995c15b008401285011
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131646"
---
Gebruik altijd [Azure AD-beveiligings groepen](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups) als de toegewezen Principal in een ACL-vermelding. Houd de kans om afzonderlijke gebruikers of service-principals rechtstreeks toe te wijzen. Met deze structuur kunt u gebruikers of service-principals toevoegen en verwijderen zonder dat u de Acl's opnieuw hoeft toe te passen op een volledige mapstructuur. In plaats daarvan kunt u alleen gebruikers en service-principals toevoegen aan of verwijderen uit de juiste Azure AD-beveiligings groep. 

Er zijn veel verschillende manieren om groepen in te stellen. Stel dat u een map hebt met de naam **/LogData** , die logboek gegevens bevat die door de server worden gegenereerd. Met Azure Data Factory (ADF) worden gegevens opgenomen in die map. Specifieke gebruikers van het team van de service techniek uploaden logboeken en beheren andere gebruikers van deze map, en verschillende Databricks-clusters analyseren Logboeken uit die map. 

Als u deze activiteiten wilt inschakelen, kunt u een `LogsWriter` groep en een `LogsReader` groep maken. Daarna kunt u machtigingen als volgt toewijzen:

- Voeg de `LogsWriter` groep toe aan de ACL van de **/LogData** -map met `rwx` machtigingen.
- Voeg de `LogsReader` groep toe aan de ACL van de **/LogData** -map met `r-x` machtigingen.
- Voeg het Service-Principal-object of de Managed Service Identity (MSI) voor ADF toe aan de `LogsWriters` groep.
- Gebruikers in het team van de service techniek toevoegen aan de `LogsWriter` groep.
- Voeg het Service-Principal-object of MSI voor Databricks toe aan de `LogsReader` groep.

Als een gebruiker in het service engineering-team het bedrijf verlaat, kunt u ze gewoon uit de `LogsWriter` groep verwijderen. Als u die gebruiker niet aan een groep hebt toegevoegd, maar in plaats daarvan een exclusieve ACL-vermelding voor die gebruiker hebt, moet u die ACL-vermelding verwijderen uit de map **/LogData** . U moet ook de vermelding verwijderen uit alle submappen en bestanden in de volledige Directory-hiërarchie van de **/LogData** -map. 

Als u een groep wilt maken en leden wilt toevoegen, raadpleegt u [een basis groep maken en leden toevoegen met behulp van Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).