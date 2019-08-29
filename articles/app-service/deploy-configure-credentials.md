---
title: Implementatie referenties configureren-Azure App Service | Microsoft Docs
description: Meer informatie over het gebruik van de Azure App Service implementatie referenties.
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnoc
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/14/2019
ms.author: cephalin
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: fc9445b64baae0e625b62356fee381329b01e8fd
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098484"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Implementatie referenties voor Azure App Service configureren
[Azure app service](https://go.microsoft.com/fwlink/?LinkId=529714) ondersteunt twee typen referenties voor [lokale Git-implementatie](deploy-local-git.md) en [FTP/S-implementatie](deploy-ftp.md). Deze referenties zijn niet hetzelfde als de referenties van uw Azure-abonnement.

* **Referenties op gebruikers niveau**: één set referenties voor het hele Azure-account. Het kan worden gebruikt om te implementeren in App Service voor elke app, in elk abonnement, dat het Azure-account toegangs rechten heeft. Dit is de standaard instelling die wordt weer gegeven in de gebruikers interface van de portal (zoals het **overzicht** en de **Eigenschappen** van de [resource pagina](../azure-resource-manager/manage-resources-portal.md#manage-resources)van de app). Wanneer een gebruiker toegang tot de app heeft verleend via op rollen gebaseerde Access Control (RBAC) of coadmin-machtigingen, kan die gebruiker hun eigen referenties op gebruikers niveau gebruiken tot de toegang is ingetrokken. Deel deze referenties niet met andere Azure-gebruikers.

* **Referenties op app-niveau**: één set referenties voor elke app. Het kan alleen worden gebruikt voor de implementatie van deze app. De referenties voor elke app worden automatisch gegenereerd bij het maken van een app. Ze kunnen niet hand matig worden geconfigureerd, maar kan altijd opnieuw worden ingesteld. Als u wilt dat een gebruiker toegang krijgt tot referenties op app-niveau via (RBAC), moet die gebruiker Inzender of hoger zijn op de app. Lezers mogen niet publiceren en hebben geen toegang tot deze referenties.

## <a name="userscope"></a>Referenties op gebruikers niveau configureren

U kunt uw referenties op gebruikers niveau configureren op de [resource pagina](../azure-resource-manager/manage-resources-portal.md#manage-resources)van elke app. Ongeacht in welke app u deze referenties configureert, is dit van toepassing op alle apps en voor alle abonnementen in uw Azure-account. 

### <a name="in-the-cloud-shell"></a>In de Cloud Shell

Als u de implementatie gebruiker wilt configureren in de [Cloud shell](https://shell.azure.com), voert u de opdracht [AZ webapp Deployment User set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) uit. Vervang \< gebruikers\<> naam en wacht woord > door een gebruikers naam en wacht woord voor de implementatie gebruiker. 

- De gebruikers naam moet uniek zijn binnen Azure en voor lokale Git-pushes mag het symbool ' @ ' niet bevatten. 
- Het wacht woord moet ten minste acht tekens lang zijn, met twee van de volgende drie elementen: letters, cijfers en symbolen. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

De JSON-uitvoer toont het wacht `null`woord als. Als er een `'Conflict'. Details: 409`-fout optreedt, wijzigt u de gebruikersnaam. Als er een `'Bad Request'. Details: 400`-fout optreedt, kiest u een sterker wachtwoord. 

### <a name="in-the-portal"></a>In de portal

In de Azure Portal moet u ten minste één app hebben voordat u toegang kunt krijgen tot de pagina implementatie referenties. Uw referenties op gebruikers niveau configureren:

1. Selecteer in het menu links in het [Azure Portal](https://portal.azure.com) **app Services** >  **\<any_app >**  > **Deployment Center** >  **-FTP-**  > **dash board**.

    ![](./media/app-service-deployment-credentials/access-no-git.png)

    Als u de Git-implementatie al hebt geconfigureerd, selecteert u **app Services** >  **&lt;any_app >**  > **Deployment Center** > **FTP/credentials**.

    ![](./media/app-service-deployment-credentials/access-with-git.png)

2. Selecteer **gebruikers referenties**, Configureer de gebruikers naam en het wacht woord en selecteer vervolgens **referenties opslaan**.

Wanneer u de referenties voor de implementatie hebt ingesteld, kunt u de gebruikers naam van de *Git* -implementatie vinden op de pagina **overzicht** van uw app.

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Als de Git-implementatie is geconfigureerd, wordt op de pagina een **Git/implementatie-gebruikers naam**weer gegeven. anders een **FTP/implementatie-gebruikers naam**.

> [!NOTE]
> Het wacht woord voor implementatie op gebruikers niveau wordt niet weer gegeven in Azure. Als u het wacht woord vergeet, kunt u uw referenties opnieuw instellen door de stappen in deze sectie te volgen.
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>Referenties op gebruikers niveau gebruiken met FTP-FTPS

Verificatie bij een FTP-FTPS-eind punt met referenties op gebruikers niveau vereist een gebruikers naam in de volgende indeling:`<app-name>\<user-name>`

Omdat referenties op gebruikers niveau zijn gekoppeld aan de gebruiker en niet aan een specifieke resource, moet de gebruikers naam de volgende indeling hebben om de aanmeldings actie door te sturen naar het juiste app-eind punt.

## <a name="appscope"></a>Referenties op app-niveau ophalen en opnieuw instellen
De referenties op app-niveau ophalen:

1. Selecteer in het menu links in het [Azure Portal](https://portal.azure.com) **app Services** >  **&lt;any_app >**  > **Deployment Center** > **FTP/credentials**.

2. Selecteer **app-referenties**en selecteer de koppeling **kopiëren** om de gebruikers naam of het wacht woord te kopiëren.

Als u de referenties op app-niveau opnieuw wilt instellen, selecteert u **referenties opnieuw instellen** in hetzelfde dialoog venster.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van deze referenties voor het implementeren van uw app vanuit [lokale Git](deploy-local-git.md) of het gebruik van [FTP/S](deploy-ftp.md).
