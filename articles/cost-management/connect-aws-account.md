---
title: Verbinding maken met een Amazon Web Services-account aan Cloudyn in Azure | Microsoft Docs
description: Verbinding maken met een Amazon Web Services-account om kosten en gebruiksgegevens in Cloudyn-rapporten weer te geven.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 676c01a26d67b395340e5b1ed2dacc6b3b824742
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74219746"
---
# <a name="connect-an-amazon-web-services-account"></a>Verbinding maken met een Amazon Web Services-account

U hebt twee opties verbinding maken met uw account Amazon Web Services (AWS) Cloudyn. U kunt verbinding maken met een IAM-rol of met een alleen-lezen IAM-gebruikersaccount. De IAM-rol wordt aanbevolen omdat Hiermee kunt u om toegang met gedefinieerde machtigingen voor het vertrouwde entiteiten te delegeren. De IAM-rol, hoeft u voor het delen van de toegangssleutel op lange termijn. Nadat u een AWS-account met Cloudyn verbonden, is kosten en gebruiksgegevens beschikbaar in Cloudyn-rapporten. Dit document begeleidt u bij beide opties.

Zie [identiteiten (gebruikers, groepen en rollen)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html)voor meer informatie over AWS iam-identiteiten.

Ook het inschakelen van AWS gedetailleerde rapporten facturering en het opslaan van de informatie in een bucket van AWS simple storage-service (S3). Gedetailleerde facturering rapporten bevatten kosten met tag en resourcegegevens op uurbasis. Opslaan van de rapporten kunt Cloudyn uw bucket ophalen en de informatie wordt weergegeven in de rapporten.


## <a name="aws-role-based-access"></a>AWS rollen gebaseerde toegang

De volgende secties helpen u bij het maken van een alleen-lezen IAM-rol om toegang te bieden aan Cloudyn.

### <a name="get-your-cloudyn-account-external-id"></a>Uw externe ID van Cloudyn-account ophalen

De eerste stap is de unieke verbindings-wachtwoordzin ophalen uit de Cloudyn-portal. Het wordt in AWS gebruikt als de **externe ID**.

1. Open de Cloudyn-Portal vanuit het Azure Portal of navigeer naar [https://azure.cloudyn.com](https://azure.cloudyn.com) en meld u aan.
2. Klik op het symbool tandwiel en selecteer vervolgens **Cloud accounts**.
3. Selecteer in account beheer het tabblad **AWS accounts** en klik vervolgens op **nieuwe toevoegen +** .
4. Kopieer in het dialoog venster **AWS-account toevoegen** de **externe ID** en sla de waarde voor het maken van AWS-rollen op in de volgende sectie. De externe ID is uniek is voor uw account. In de volgende afbeelding is het voor beeld externe ID _Contoso_ gevolgd door een getal. Uw ID verschilt.  
    ![externe ID die wordt weer gegeven in het vak AWS-account toevoegen](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>AWS alleen-lezen toegang op basis van rollen toevoegen

1. Meld u aan bij de AWS-console op https://console.aws.amazon.com/iam/home en selecteer **rollen**.
2. Klik op **rol maken** en selecteer vervolgens **een ander AWS-account**.
3. Plak `432263259397`in het vak **account-id** . Deze Account-ID is de Cloudyn data collector-account toegewezen door AWS aan de Cloudyn-service. Gebruik de exacte Account-ID die wordt weergegeven.
4. Selecteer **externe ID vereisen**naast **Opties**. Plak uw unieke waarde die eerder is gekopieerd in het veld **externe ID** in Cloudyn. Klik vervolgens op **volgende: machtigingen**.  
    ![externe ID plakken vanuit Cloudyn op de pagina rol maken](./media/connect-aws-account/create-role01.png)
5. Onder **machtigings beleid koppelen**typt u `ReadOnlyAccess`in het vak filter **type** filteren, selecteert u **ReadOnlyAccess**en klikt u op **volgende: controleren**.  
    ![Selecteer alleen-lezen toegang in de lijst met beleids namen](./media/connect-aws-account/readonlyaccess.png)
6. Controleer op de pagina controleren of uw selecties juist zijn en typ de **naam**van een rol. Bijvoorbeeld: *Azure-cost-Salesopm* Voer een **Beschrijving**van een rol in. Bijvoorbeeld _roltoewijzing voor Cloudyn_en klik vervolgens op **rol maken**.
7. Klik in de lijst **rollen** op de rol die u hebt gemaakt en kopieer de waarde van de **rol Arn** van de pagina samen vatting. De rol van informatie (resourcenaam Amazon)-waarde later gebruiken wanneer u uw configuratie in Cloudyn registreert.  
    ![de functie ARN van de pagina samen vatting kopiëren](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cloudyn"></a>AWS IAM roltoegang in Cloudyn configureren

1. Open de Cloudyn-Portal vanuit het Azure Portal of navigeer naar https://azure.cloudyn.com/ en meld u aan.
2. Klik op het symbool tandwiel en selecteer vervolgens **Cloud accounts**.
3. Selecteer in account beheer het tabblad **AWS accounts** en klik vervolgens op **nieuwe toevoegen +** .
4. Typ in **account naam**een naam voor het account.
5. Selecteer in het veld **toegangs type**de optie **iam-rol**.
6. Plak in het veld **Role Arn** de waarde die u eerder hebt gekopieerd en klik vervolgens op **Opslaan**.  
    ![plak de rol ARN in het vak AWS account toevoegen](./media/connect-aws-account/add-aws-account-box.png)


Uw AWS-account wordt weergegeven in de lijst van accounts. De **eigenaar-id** komt overeen met de ARN waarde van uw rol. De **account status** moet een groen vinkje hebben dat aangeeft dat Cloudyn toegang heeft tot uw AWS-account. Totdat u gedetailleerde AWS-facturering hebt ingeschakeld, wordt uw consolidatie status weer gegeven als **zelfstandig**.

![Status van AWS-account wordt weergegeven op de pagina Accounts beheren](./media/connect-aws-account/aws-account-status01.png)

Cloudyn begint met het verzamelen van de gegevens en rapporten te vullen. Schakel vervolgens [gedetailleerde AWS-facturering in](#enable-detailed-aws-billing).


## <a name="aws-user-based-access"></a>AWS-toegang op basis van gebruiker

De volgende secties helpen u bij het maken van een gebruiker alleen-lezen toegang te hebben tot Cloudyn.

### <a name="add-aws-read-only-user-based-access"></a>AWS alleen-lezen toegang op basis van een gebruiker toevoegen

1. Meld u aan bij de AWS-console op https://console.aws.amazon.com/iam/home en selecteer **gebruikers**.
2. Klik op **Add User**.
3. Typ een gebruikers naam in het veld **gebruikers naam** .
4. Selecteer voor **toegangs type** **programmatische toegang** en klik op **volgende: machtigingen**.  
    ![Geef een gebruikers naam op op de pagina gebruiker toevoegen](./media/connect-aws-account/add-user01.png)
5. Selecteer **bestaande beleids regels direct koppelen**voor machtigingen.
6. Onder **machtigings beleid koppelen**typt u `ReadOnlyAccess`in het vak filter **type** filteren, selecteert u **ReadOnlyAccess**en klikt u op **volgende: controleren**.  
    ![Selecteer ReadOnlyAccess om machtigingen voor de gebruiker in te stellen](./media/connect-aws-account/set-permission-for-user.png)
7. Controleer op de pagina controleren of uw selecties juist zijn en klik vervolgens op **gebruiker maken**.
8. Op de pagina is voltooid, worden uw sleutel-ID en -geheim toegang toegangssleutel weergegeven. U kunt deze informatie gebruiken om te registreren in Cloudyn configureren.
9. Klik op **down load. CSV** en sla het bestand referenties. CSV op een veilige locatie op.  
    ![op down load. CSV om de referenties op te slaan](./media/connect-aws-account/download-csv.png)

### <a name="configure-aws-iam-user-based-access-in-cloudyn"></a>AWS IAM op basis van een gebruiker toegang in Cloudyn configureren

1. Open de Cloudyn-portal vanuit Azure Portal of ga naar https://azure.cloudyn.com/ en meld u aan.
2. Klik op het symbool tandwiel en selecteer vervolgens **Cloud accounts**.
3. Selecteer in account beheer het tabblad **AWS accounts** en klik vervolgens op **nieuwe toevoegen +** .
4. Voor **account naam**typt u de naam van een account.
5. Selecteer vervolgens in het veld **toegangs type**de optie **iam-gebruiker**.
6. Plak in **toegangs sleutel**de waarde van de **toegangs sleutel-id** uit het CSV-bestand referenties.
7. Plak in **geheime sleutel**de waarde van de **geheime toegangs sleutel** uit het CSV-bestand referenties en klik vervolgens op **Opslaan**.  

Uw AWS-account wordt weergegeven in de lijst van accounts. De **account status** moet een symbool voor een groen vinkje hebben.

Cloudyn begint met het verzamelen van de gegevens en rapporten te vullen. Schakel vervolgens [gedetailleerde AWS-facturering in](#enable-detailed-aws-billing).

## <a name="enable-detailed-aws-billing"></a>Gedetailleerde AWS facturering inschakelen

Gebruik de volgende stappen uit om uw informatie van de rol van AWS. De informatie van de rol kunt u lees-en schrijfmachtigingen voor een bucket van facturering verlenen.

1. Meld u aan bij de AWS-console op https://console.aws.amazon.com en selecteer **Services**.
2. Typ *iam*in het zoekvak van de service en selecteer deze optie.
3. Selecteer **rollen** in het menu aan de linkerkant.
4. Selecteer in de lijst met rollen, de rol die u hebt gemaakt voor toegang tot Cloudyn.
5. Klik op de pagina overzicht van functies om de **rol Arn**te kopiëren. Houd de informatie van de rol bij de hand hebt voor de latere fasen.

### <a name="create-an-s3-bucket"></a>Een S3-bucket maken

U maakt een S3-bucket voor het opslaan van gedetailleerde informatie over facturering.

1. Meld u aan bij de AWS-console op https://console.aws.amazon.com en selecteer **Services**.
2. Typ *S3*in het zoekvak van de service en selecteer **S3**.
3. Klik op de pagina Amazon S3 op **Bucket maken**.
4. Kies in de wizard Bucket maken een Bucket naam en regio en klik vervolgens op **volgende**.  
    ![voorbeeld informatie één de pagina Bucket maken](./media/connect-aws-account/create-bucket.png)
5. Behoud op de pagina **Eigenschappen instellen** de standaard waarden en klik vervolgens op **volgende**.
6. Klik op de pagina controleren op **Bucket maken**. De bucket-lijst wordt weergegeven.
7. Klik op de Bucket die u hebt gemaakt, selecteer het tabblad **machtigingen** en selecteer vervolgens **Bucket beleid**. De Bucket editor wordt geopend.
8. De volgende JSON-voorbeeld Kopieer en plak deze in de beleidseditor Bucket.
   - Vervang `<BillingBucketName>` door de naam van uw S3-Bucket.
   - Vervang `<ReadOnlyUserOrRole>` door de ARN van de rol of gebruiker die u eerder hebt gekopieerd.

   ```json
   {
    "Version": "2012-10-17",
    "Id": "Policy1426774604000",
    "Statement": [
        {
            "Sid": "Stmt1426774604000",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::386209384616:root"
            },
            "Action": [
                "s3:GetBucketAcl",
                "s3:GetBucketPolicy"
            ],
            "Resource": "arn:aws:s3:::<BillingBucketName>"
        },
        {
            "Sid": "Stmt1426774604001",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::386209384616:root"
            },
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::<BillingBucketName>/*"
        },
        {
            "Sid": "Stmt1426774604002",
            "Effect": "Allow",
            "Principal": {
                "AWS": "<ReadOnlyUserOrRole>"
            },
            "Action": [
                "s3:List*",
                "s3:Get*"
            ],
            "Resource": "arn:aws:s3:::<BillingBucketName>/*"
        }
    ]
   }
   ```

9. Klik op **Opslaan**.  
    ![klikt u op opslaan in de editor voor het Bucket beleid](./media/connect-aws-account/bucket-policy-editor.png)


### <a name="enable-aws-billing-reports"></a>AWS rapporten facturering inschakelen

Nadat u de S3-Bucket hebt gemaakt en geconfigureerd, gaat u naar [facturerings voorkeuren](https://console.aws.amazon.com/billing/home?#/preference) in de AWS-console.

1. Selecteer op de pagina voor keuren **facturerings rapporten ontvangen**.
2. Onder **facturerings rapporten ontvangen**voert u de naam in van de Bucket die u hebt gemaakt en klikt u vervolgens op **verifiëren**.  
3. Selecteer alle vier opties voor het granulatie rapport en klik vervolgens op **voor keuren opslaan**.  
    ![granulatie selecteren om rapporten in te scha kelen](./media/connect-aws-account/enable-reports.png)

Cloudyn haalt gedetailleerde informatie over de facturering van de S3-bucket gedetecteerd en gevuld rapporten nadat gedetailleerde facturering is ingeschakeld. Duurt maximaal 24 uur tot gedetailleerde factureringsgegevens wordt weergegeven in de Cloudyn-console. Wanneer gedetailleerde facturerings gegevens beschikbaar zijn, wordt uw account consolidatie status weer gegeven als **geconsolideerd**. De account status wordt weer gegeven als **voltooid**.

![consolidatiestatus wordt weergegeven op het tabblad AWS-Accounts](./media/connect-aws-account/consolidated-status.png)

Sommige van de optimalisatierapporten is mogelijk een paar dagen aan gegevens om op te halen van de grootte van de steekproef van een voldoende gegevens voor nauwkeurige aanbevelingen.

## <a name="next-steps"></a>Volgende stappen

- Ga verder met de zelf studie [gebruik en kosten](tutorial-review-usage.md) voor Cloudyn voor meer informatie over Cloudyn.
