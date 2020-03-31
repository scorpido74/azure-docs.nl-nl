---
title: Gegevens beveiligen die zijn opgeslagen in Azure Data Lake Storage Gen1 | Microsoft Documenten
description: Meer informatie over het beveiligen van gegevens in Azure Data Lake Storage Gen1 met behulp van groepen en toegangscontrolelijsten
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ca35e65f-3986-4f1b-bf93-9af6066bb716
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: cebdff5ed233516683df3330e8fd3332ded664e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260304"
---
# <a name="securing-data-stored-in-azure-data-lake-storage-gen1"></a>Gegevens beveiligen die zijn opgeslagen in Azure Data Lake Storage Gen1
Het beveiligen van gegevens in Azure Data Lake Storage Gen1 is een aanpak in drie stappen.  Zowel op rollen gebaseerde toegangscontrole (RBAC) als toegangscontrolelijsten (ACL's) moeten zijn ingesteld om gebruikers en beveiligingsgroepen volledig toegang tot gegevens mogelijk te maken.

1. Begin met het maken van beveiligingsgroepen in Azure Active Directory (AAD). Deze beveiligingsgroepen worden gebruikt om rbac (role-based access control) te implementeren in Azure-portal. Zie [Toegangsbeheer op basis van rollen in Microsoft Azure](../role-based-access-control/role-assignments-portal.md)voor meer informatie.
2. Wijs de AAD-beveiligingsgroepen toe aan het Data Lake Storage Gen1-account. Hiermee wordt de toegang tot het Data Lake Storage Gen1-account vanuit de portal- en beheerbewerkingen vanuit de portal of API's beheerd.
3. Wijs de AAD-beveiligingsgroepen toe als toegangscontrolelijsten (ACL's) op het Data Lake Storage Gen1-bestandssysteem.
4. Daarnaast u ook een IP-adresbereik instellen voor clients die toegang hebben tot de gegevens in Data Lake Storage Gen1.

In dit artikel vindt u instructies over het gebruik van de Azure-portal om de bovenstaande taken uit te voeren. Zie [Beveiliging in Azure Data Lake Storage Gen1](data-lake-store-security-overview.md)voor uitgebreide informatie over hoe Data Lake Storage Gen1 beveiliging op account- en gegevensniveau implementeert. Zie Overzicht van toegangscontrole in Data Lake Storage Gen1 voor deep-dive informatie over de manier waarop ACL's worden geïmplementeerd in Data Lake Storage [Gen1.](data-lake-store-access-control.md)

## <a name="prerequisites"></a>Vereisten
Voordat u met deze zelfstudie begint, moet u het volgende hebben of hebben gedaan:

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Een Data Lake Storage Gen1-account**. Zie [Aan de slag met Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md) voor instructies over het maken van een account.

## <a name="create-security-groups-in-azure-active-directory"></a>Beveiligingsgroepen maken in Azure Active Directory
Zie [Beveiligingsgroepen beheren in Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)voor instructies over het maken van AAD-beveiligingsgroepen en het toevoegen van gebruikers aan de groep.

> [!NOTE] 
> U zowel gebruikers als andere groepen toevoegen aan een groep in Azure AD met behulp van de Azure-portal. Als u echter een serviceprincipal aan een groep wilt toevoegen, gebruikt u de [PowerShell-module van Azure AD.](../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md)
> 
> ```powershell
> # Get the desired group and service principal and identify the correct object IDs
> Get-AzureADGroup -SearchString "<group name>"
> Get-AzureADServicePrincipal -SearchString "<SPI name>"
> 
> # Add the service principal to the group
> Add-AzureADGroupMember -ObjectId <Group object ID> -RefObjectId <SPI object ID>
> ```
 
## <a name="assign-users-or-security-groups-to-data-lake-storage-gen1-accounts"></a>Gebruikers of beveiligingsgroepen toewijzen aan Data Lake Storage Gen1-accounts
Wanneer u gebruikers of beveiligingsgroepen toewijst aan Data Lake Storage Gen1-accounts, beheert u de toegang tot de beheerbewerkingen voor het account met behulp van de Azure-portal en Azure Resource Manager API's. 

1. Open een Data Lake Storage Gen1-account. Klik in het linkerdeelvenster op **Alle bronnen**en klik vervolgens in het blad Alle bronnen op de accountnaam waaraan u een gebruiker of beveiligingsgroep wilt toewijzen.

2. Klik in het accountblad Data Lake Storage Gen1 op **Toegangsbeheer (IAM).** Het blad vermeldt standaard de eigenaren van het abonnement als eigenaar.
   
    ![Beveiligingsgroep toewijzen aan Azure Data Lake Storage Gen1-account](./media/data-lake-store-secure-data/adl.select.user.icon1.png "Beveiligingsgroep toewijzen aan Azure Data Lake Storage Gen1-account")

3. Klik in het **IAM-blad (Access Control)** op **Toevoegen** om het blad **Machtigingen toevoegen** te openen. Selecteer **in** het blad Machtigingen toevoegen een **rol** voor de gebruiker/groep. Zoek naar de beveiligingsgroep die u eerder in Azure Active Directory hebt gemaakt en selecteer deze. Als u veel gebruikers en groepen hebt om naar te zoeken, gebruikt u het tekstvak **selecteren** om op de groepsnaam te filteren. 
   
    ![Een rol voor de gebruiker toevoegen](./media/data-lake-store-secure-data/adl.add.user.1.png "Een rol voor de gebruiker toevoegen")
   
    De rol **Eigenaar** en **bijdrager** biedt toegang tot verschillende beheerfuncties op het datalake-account. Voor gebruikers die interactie hebben met gegevens in het gegevensmeer, maar nog steeds accountbeheergegevens moeten bekijken, u deze toevoegen aan **de** leesrol. Het bereik van deze rollen is beperkt tot de beheeractiviteiten met betrekking tot het Data Lake Storage Gen1-account.
   
    Voor gegevensbewerkingen definiëren afzonderlijke bestandssysteemmachtigingen wat de gebruikers kunnen doen. Daarom kan een gebruiker met een Reader-rol alleen de beheerdersinstellingen weergeven die aan het account zijn gekoppeld, maar kan deze mogelijk gegevens lezen en schrijven op basis van machtigingen voor bestandssystemen die aan het account zijn toegewezen. Gegevenslake Storage Gen1-bestandssysteemmachtigingen worden beschreven in [De beveiligingsgroep toewijzen als ACL's aan het Azure Data Lake Storage Gen1-bestandssysteem](#filepermissions).

    > [!IMPORTANT]
    > Alleen de rol **Eigenaar** maakt automatisch toegang tot het bestandssysteem mogelijk. Voor **de inzender,** **de lezer**en alle andere rollen zijn ACL's vereist om elk niveau van toegang tot mappen en bestanden in te schakelen.  De rol **Eigenaar** biedt machtigingen voor supergebruikersbestanden en mappen die niet via ACL's kunnen worden overschreven. Zie [RBAC voor accountbeheer voor](data-lake-store-security-overview.md#rbac-for-account-management)meer informatie over hoe RBAC-beleidsregels gegevenstoegang toewijzen.

4. Als u een groep/gebruiker wilt toevoegen die niet wordt vermeld in het blad **Machtigingen toevoegen,** u deze uitnodigen door hun e-mailadres in het tekstvak **selecteren** te typen en deze vervolgens in de lijst te selecteren.
   
    ![Een beveiligingsgroep toevoegen](./media/data-lake-store-secure-data/adl.add.user.2.png "Een beveiligingsgroep toevoegen")
   
5. Klik op **Opslaan**. U ziet de beveiligingsgroep die is toegevoegd zoals hieronder wordt weergegeven.
   
    ![Beveiligingsgroep toegevoegd](./media/data-lake-store-secure-data/adl.add.user.3.png "Beveiligingsgroep toegevoegd")

6. Uw gebruikers-/beveiligingsgroep heeft nu toegang tot het Data Lake Storage Gen1-account. Als u toegang wilt bieden aan specifieke gebruikers, u deze toevoegen aan de beveiligingsgroep. Als u de toegang voor een gebruiker wilt intrekken, u deze verwijderen uit de beveiligingsgroep. U ook meerdere beveiligingsgroepen aan een account toewijzen. 

## <a name="assign-users-or-security-groups-as-acls-to-the-data-lake-storage-gen1-file-system"></a><a name="filepermissions"></a>Gebruikers of beveiligingsgroepen toewijzen als ACL's aan het Data Lake Storage Gen1-bestandssysteem
Door gebruikers-/beveiligingsgroepen toe te wijs aan het Data Lake Storage Gen1-bestandssysteem, stelt u toegangscontrole in op de gegevens die zijn opgeslagen in Data Lake Storage Gen1.

1. Klik in het accountblad Data Lake Storage Gen1 op **Data Explorer**.
   
    ![Gegevens weergeven via Data Explorer](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Gegevens weergeven via Data Explorer")
2. Klik in het **blade van Data Explorer** op de map waarvoor u de ACL wilt configureren en klik vervolgens op **Access**. Als u ACL's aan een bestand wilt toewijzen, moet u eerst op het bestand klikken om het te bekijken en vervolgens op **Toegang** klikken vanuit het **blade voor bestandsvoorbeeld.**
   
    ![ACL's instellen op het Data Lake Storage Gen1-bestandssysteem](./media/data-lake-store-secure-data/adl.acl.1.png "ACL's instellen op het Data Lake Storage Gen1-bestandssysteem")
3. Het **Access-blad** bevat de eigenaren en toegewezen machtigingen die al aan de root zijn toegewezen. Klik **op** het pictogram Toevoegen om extra Toegangsaac's toe te voegen.
    > [!IMPORTANT]
    > Het instellen van toegangsmachtigingen voor één bestand geeft een gebruiker/groep niet noodzakelijkerwijs toegang tot dat bestand. Het pad naar het bestand moet toegankelijk zijn voor de toegewezen gebruiker/groep. Zie [Algemene scenario's met betrekking tot machtigingen](data-lake-store-access-control.md#common-scenarios-related-to-permissions)voor meer informatie en voorbeelden.
   
    ![Standaard- en aangepaste toegang weergeven](./media/data-lake-store-secure-data/adl.acl.2.png "Standaard- en aangepaste toegang weergeven")
   
   * De **eigenaren** en **alle anderen** bieden UNIX-stijl toegang, waar u lezen, schrijven, uitvoeren (rwx) opgeeft aan drie verschillende gebruikersklassen: eigenaar, groep en anderen.
   * **Toegewezen machtigingen** komen overeen met de POSIX ACL's waarmee u machtigingen instellen voor specifieke benoemde gebruikers of groepen buiten de eigenaar of groep van het bestand. 
     
     Zie [HDFS ACL's voor](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists)meer informatie . Zie Toegangscontrole in Data Lake Storage Gen1 voor meer informatie over de manier waarop ACL's worden geïmplementeerd in Data Lake Storage [Gen1.](data-lake-store-access-control.md)
4. Klik **op** het pictogram Toevoegen om het **blad Machtigingen toewijzen te** openen. Klik in dit blad op **Gebruiker of groep selecteren**en zoek vervolgens in **Gebruiker- of groepsblad selecteren** naar de beveiligingsgroep die u eerder in Azure Active Directory hebt gemaakt. Als u veel groepen hebt om naar te zoeken, gebruikt u het tekstvak bovenaan om op de groepsnaam te filteren. Klik op de groep die u wilt toevoegen en klik vervolgens op **Selecteren**.
   
    ![Een groep toevoegen](./media/data-lake-store-secure-data/adl.acl.3.png "Een groep toevoegen")
5. Klik **op Machtigingen selecteren**, selecteer de machtigingen, of de machtigingen moeten worden toegepast op recursieve ly, en of u de machtigingen wilt toewijzen als een toegang ACL, standaard ACL, of beide. Klik op **OK**.
   
    ![Machtigingen toewijzen aan groep](./media/data-lake-store-secure-data/adl.acl.4.png "Machtigingen toewijzen aan groep")
   
    Zie [Toegangsbeheer in Data Lake Storage Gen1](data-lake-store-access-control.md)voor meer informatie over machtigingen in Data Lake Storage Gen1 en Standaard/Access ACL's.
6. Nadat u op **Ok** hebt geklikt in het blad **Machtigingen selecteren,** worden de nieuw toegevoegde groep en de bijbehorende machtigingen nu weergegeven in het **access-blad.**
   
    ![Machtigingen toewijzen aan groep](./media/data-lake-store-secure-data/adl.acl.5.png "Machtigingen toewijzen aan groep")
   
   > [!IMPORTANT]
   > In de huidige versie u maximaal 28 vermeldingen hebben onder **Toegewezen machtigingen**. Als u meer dan 28 gebruikers wilt toevoegen, moet u beveiligingsgroepen maken, gebruikers toevoegen aan beveiligingsgroepen, toegang bieden tot die beveiligingsgroepen voor het Data Lake Storage Gen1-account.
   > 
   > 
7. Indien nodig u ook de toegangsmachtigingen wijzigen nadat u de groep hebt toegevoegd. Schakel het selectievakje voor elk machtigingstype (Lezen, Schrijven, Uitvoeren) uit of in of in op basis van de vraag of u die toestemming wilt verwijderen of toewijzen aan de beveiligingsgroep. Klik **op Opslaan** om de wijzigingen op te slaan of Verwijder **de** wijzigingen om de wijzigingen ongedaan te maken.

## <a name="set-ip-address-range-for-data-access"></a>IP-adresbereik instellen voor gegevenstoegang
Data Lake Storage Gen1 stelt u in staat om de toegang tot uw gegevensarchief op netwerkniveau verder te vergrendelen. U firewall inschakelen, een IP-adres opgeven of een IP-adresbereik definiëren voor uw vertrouwde clients. Zodra deze is ingeschakeld, kunnen alleen clients met de IP-adressen binnen het gedefinieerde bereik verbinding maken met de winkel.

![Firewall-instellingen en IP-toegang](./media/data-lake-store-secure-data/firewall-ip-access.png "Firewall-instellingen en IP-adres")

## <a name="remove-security-groups-for-a-data-lake-storage-gen1-account"></a>Beveiligingsgroepen voor een Data Lake Storage Gen1-account verwijderen
Wanneer u beveiligingsgroepen uit Gegevenslake Storage Gen1-accounts verwijdert, wijzigt u alleen de toegang tot de beheerbewerkingen voor het account met behulp van de API's Azure Portal en Azure Resource Manager.  

De toegang tot gegevens is ongewijzigd en wordt nog steeds beheerd door de toegangs-ACL's.  De uitzondering hierop zijn gebruikers/groepen in de rol Eigenaren.  Gebruikers/groepen die uit de rol Eigenaren zijn verwijderd, zijn niet langer supergebruikers en hun toegang valt terug tot toegang tot ACL-instellingen. 

1. Klik in het accountblad Data Lake Storage Gen1 op **Toegangsbeheer (IAM).** 
   
    ![Beveiligingsgroep toewijzen aan Data Lake Storage Gen1-account](./media/data-lake-store-secure-data/adl.select.user.icon.png "Beveiligingsgroep toewijzen aan Data Lake Storage Gen1-account")
2. Klik in het **IAM-blad (Access Control)** op de beveiligingsgroep(s) die u wilt verwijderen. Klik op **Verwijderen**.
   
    ![Beveiligingsgroep verwijderd](./media/data-lake-store-secure-data/adl.remove.group.png "Beveiligingsgroep verwijderd")

## <a name="remove-security-group-acls-from-a-data-lake-storage-gen1-file-system"></a>ACL's van beveiligingsgroepen verwijderen uit een Gegevensmeeropslag Gen1-bestandssysteem
Wanneer u acl.s. beveiligingsgroepacl. verwijdert uit een Data Lake Storage Gen1-bestandssysteem, wijzigt u de toegang tot de gegevens in het Data Lake Storage Gen1-account.

1. Klik in het accountblad Data Lake Storage Gen1 op **Data Explorer**.
   
    ![Mappen maken in Data Lake Storage Gen1-account](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Mappen maken in Data Lake Storage Gen1-account")
2. Klik in het **blade van Data Explorer** op de map waarvoor u de ACL wilt verwijderen en klik vervolgens op **Access**. Als u ACL's voor een bestand wilt verwijderen, moet u eerst op het bestand klikken om het te bekijken en vervolgens op **Toegang** klikken vanuit het **blade voor bestandsvoorbeeld.** 
   
    ![ACL's instellen op het Data Lake Storage Gen1-bestandssysteem](./media/data-lake-store-secure-data/adl.acl.1.png "ACL's instellen op het Data Lake Storage Gen1-bestandssysteem")
3. Klik in het **Access-blad** op de beveiligingsgroep die u wilt verwijderen. Klik in het blad **Toegangsdetails** op **Verwijderen**.
   
    ![Machtigingen toewijzen aan groep](./media/data-lake-store-secure-data/adl.remove.acl.png "Machtigingen toewijzen aan groep")

## <a name="see-also"></a>Zie ook
* [Overzicht van Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Gegevens uit Azure Storage Blobs kopiëren naar Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Lake Analytics gebruiken met Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight gebruiken met Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Aan de slag met Data Lake Storage Gen1 met behulp van PowerShell](data-lake-store-get-started-powershell.md)
* [Aan de slag met Data Lake Storage Gen1 met .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Toegang tot diagnostische logboeken voor Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md)

