---
title: Implementatiereferenties configureren
description: Meer informatie over welke typen implementatiereferenties zich bevinden in Azure App Service en hoe u deze configureert en gebruikt.
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: a9d875e2c3899fa91b9cc41c0ee3b5a93ec5b8c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266076"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Implementatiereferenties configureren voor Azure App Service
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) ondersteunt twee typen referenties voor [lokale Git-implementatie](deploy-local-git.md) en [FTP/S-implementatie.](deploy-ftp.md) Deze referenties zijn niet hetzelfde als uw Azure-abonnementsreferenties.

* **Referenties op gebruikersniveau:** één set referenties voor het hele Azure-account. Het kan worden gebruikt om te implementeren in App Service voor elke app, in elk abonnement, dat de Azure-account toestemming heeft om toegang te krijgen. Het is de standaardset die is opgedoken in de portal-GUI (zoals het **overzicht** en **de eigenschappen** van de [resourcepagina](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)van de app). Wanneer een gebruiker toegang krijgt tot de app via RBAC (Role-Based Access Control) of coadmin-machtigingen, kan die gebruiker zijn eigen gebruikersreferenties gebruiken totdat de toegang is ingetrokken. Deel deze referenties niet met andere Azure-gebruikers.

* **Referenties op app-niveau:** één set referenties voor elke app. Het kan alleen worden gebruikt om te implementeren voor die app. De referenties voor elke app worden automatisch gegenereerd bij het maken van apps. Ze kunnen niet handmatig worden geconfigureerd, maar kunnen op elk gewenst moment worden gereset. Als een gebruiker toegang kan krijgen tot referenties op app-niveau via (RBAC), moet die gebruiker inzender of hoger zijn in de app (inclusief ingebouwde rol websitebijdrager). Lezers mogen niet publiceren en hebben geen toegang tot deze referenties.

## <a name="configure-user-level-credentials"></a><a name="userscope"></a>Referenties op gebruikersniveau configureren

U uw gebruikersreferenties configureren op de [resourcepagina](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)van elke app. Ongeacht in welke app u deze referenties configureert, deze is van toepassing op alle apps en voor alle abonnementen in uw Azure-account. 

### <a name="in-the-cloud-shell"></a>In de cloud shell

Voer de opdracht voor de [gebruikersset van az webapp deployment uit](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) om de implementatiegebruiker in de [Cloud Shell](https://shell.azure.com)te configureren. Vervang \<gebruikersnaam \<> en wachtwoord> door een gebruikersnaam en wachtwoord van de implementatie. 

- De gebruikersnaam moet uniek zijn binnen Azure en voor lokale Git-pushes mag het symbool '@' niet bevatten. 
- Het wachtwoord moet ten minste acht tekens lang zijn, met twee van de volgende drie elementen: letters, cijfers en symbolen. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

De JSON-uitvoer geeft `null`het wachtwoord weer als . Als er een `'Conflict'. Details: 409`-fout optreedt, wijzigt u de gebruikersnaam. Als er een `'Bad Request'. Details: 400`-fout optreedt, kiest u een sterker wachtwoord. 

### <a name="in-the-portal"></a>In het portaal

In de Azure-portal moet u ten minste één app hebben voordat u toegang hebt tot de pagina met implementatiereferenties. Ga als u uw gebruikersreferenties wilt configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com)in het linkermenu **App Services** > **\<any_app>**  >  > **FTP** > **Dashboard** **FTP-dashboard van het implementatiecentrum**.

    ![](./media/app-service-deployment-credentials/access-no-git.png)

    Als u de Git-implementatie al hebt geconfigureerd, selecteert u **App Services** > **&lt;any_app>****FTP/Credentials**van  >  **het any_app-implementatiecentrum.** > 

    ![](./media/app-service-deployment-credentials/access-with-git.png)

2. Selecteer **Gebruikersreferenties,** configureer de gebruikersnaam en het wachtwoord en selecteer **Vervolgens Referenties opslaan**.

Zodra u uw implementatiereferenties hebt ingesteld, u de gebruikersnaam voor de implementatie van *Git* vinden op de **overzichtspagina van** uw app,

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Als de Git-implementatie is geconfigureerd, wordt op de pagina een **gebruikersnaam git/implementatie**weergegeven; anders een **FTP/deployment gebruikersnaam**.

> [!NOTE]
> Azure geeft geen wachtwoord voor implementatie op gebruikersniveau weer. Als u het wachtwoord bent vergeten, u uw referenties opnieuw instellen door de stappen in deze sectie te volgen.
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>Referenties op gebruikersniveau gebruiken met FTP/FTPS

Als u een FTP/FTPS-eindpunt gebruikt met referenties op gebruikersniveau, moet u een gebruikersnaam in de volgende indeling gebruiken:`<app-name>\<user-name>`

Aangezien referenties op gebruikersniveau zijn gekoppeld aan de gebruiker en niet aan een specifieke bron, moet de gebruikersnaam in deze indeling staan om de aanmeldingsactie naar het juiste eindpunt van de app te leiden.

## <a name="get-and-reset-app-level-credentials"></a><a name="appscope"></a>Referenties op app-niveau downloaden en opnieuw instellen
Ga als beste op basis van de referenties op app-niveau:

1. Selecteer in de [Azure-portal](https://portal.azure.com)in het linkermenu **App Services** > **&lt;any_app>**  > **FTP/Credentials** **van het implementatiecentrum** > .

2. Selecteer **App-referenties**en selecteer de koppeling **Kopiëren** om de gebruikersnaam of het wachtwoord te kopiëren.

Als u de referenties op app-niveau opnieuw wilt instellen, selecteert u **Referenties opnieuw instellen** in hetzelfde dialoogvenster.

## <a name="next-steps"></a>Volgende stappen

Ontdek hoe u deze referenties gebruiken om uw app te implementeren vanuit [de lokale Git](deploy-local-git.md) of met [FTP/S.](deploy-ftp.md)
