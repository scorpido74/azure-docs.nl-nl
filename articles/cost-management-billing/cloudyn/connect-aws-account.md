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
ms.openlocfilehash: b64d54df43b27abb51210995f2426e23690fa2d3
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75994881"
---
# <a name="connect-an-amazon-web-services-account"></a>Verbinding maken met een Amazon Web Services-account

U hebt twee opties verbinding maken met uw account Amazon Web Services (AWS) Cloudyn. U kunt verbinding maken met een IAM-rol of met een alleen-lezen IAM-gebruikersaccount. De IAM-rol wordt aanbevolen omdat Hiermee kunt u om toegang met gedefinieerde machtigingen voor het vertrouwde entiteiten te delegeren. De IAM-rol, hoeft u voor het delen van de toegangssleutel op lange termijn. Nadat u een AWS-account met Cloudyn verbonden, is kosten en gebruiksgegevens beschikbaar in Cloudyn-rapporten. Dit document begeleidt u bij beide opties.

Zie voor meer informatie over AWS IAM-identiteiten [identiteiten (gebruikers, groepen en rollen)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html).

Ook het inschakelen van AWS gedetailleerde rapporten facturering en het opslaan van de informatie in een bucket van AWS simple storage-service (S3). Gedetailleerde facturering rapporten bevatten kosten met tag en resourcegegevens op uurbasis. Opslaan van de rapporten kunt Cloudyn uw bucket ophalen en de informatie wordt weergegeven in de rapporten.


## <a name="aws-role-based-access"></a>AWS rollen gebaseerde toegang

De volgende secties helpen u bij het maken van een alleen-lezen IAM-rol om toegang te bieden aan Cloudyn.

### <a name="get-your-cloudyn-account-external-id"></a>Uw externe ID van Cloudyn-account ophalen

De eerste stap is de unieke verbindings-wachtwoordzin ophalen uit de Cloudyn-portal. Het wordt gebruikt in AWS als de **externe ID**.

1. Open de Cloudyn-portal vanuit Azure portal of Ga naar [ https://azure.cloudyn.com ](https://azure.cloudyn.com) en meld u aan.
2. Klik op het tandwiel-symbool en selecteer vervolgens **Cloudaccounts**.
3. Selecteer in het beheer van Accounts de **AWS-Accounts** tabblad en klik vervolgens op **nieuwe toevoegen +** .
4. In de **AWS-Account toevoegen** dialoogvenster, Kopieer de **externe ID** en sla de waarde voor het maken van de stappen in de volgende sectie AWS-rol. De externe ID is uniek is voor uw account. In de volgende afbeelding, in het voorbeeld externe ID wordt _Contoso_ gevolgd door een getal. Uw ID verschilt.  
    ![Externe ID wordt weergegeven in het AWS-Account toevoegen](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>AWS alleen-lezen toegang op basis van rollen toevoegen

1. Aanmelden bij de AWS-console op https://console.aws.amazon.com/iam/home en selecteer **rollen**.
2. Klik op **rol maken** en selecteer vervolgens **een andere AWS-account**.
3. In de **Account-ID** vak, plak `432263259397`. Deze Account-ID is de Cloudyn data collector-account toegewezen door AWS aan de Cloudyn-service. Gebruik de exacte Account-ID die wordt weergegeven.
4. Naast **opties**, selecteer **externe ID vereisen**. Plak uw unieke waarde die u eerder hebt gekopieerd uit de **externe ID** veld in Cloudyn. Klik vervolgens op **volgende: machtigingen**.  
    ![Plak de externe ID van Cloudyn op de pagina van de rol maken](./media/connect-aws-account/create-role01.png)
5. Onder **koppelen van beleidsregels voor beveiligingsmachtigingen**, in de **beleidstype** filter vak zoeken, type `ReadOnlyAccess`, selecteer **ReadOnlyAccess**, klikt u vervolgens op **volgende: Beoordeling**.  
    ![Selecteer alleen-lezen toegang in de lijst met beleidsnamen van](./media/connect-aws-account/readonlyaccess.png)
6. Op de pagina controleren, zorg ervoor dat uw selecties kloppen en typt u een **rolnaam**. Bijvoorbeeld: *Azure-cost-Salesopm* Voer een **Beschrijving**van een rol in. Bijvoorbeeld, _roltoewijzing voor Cloudyn_, klikt u vervolgens op **rol maken**.
7. In de **rollen** lijst, klik op de rol die u hebt gemaakt en kopieer de **rol informatie** waarde op de pagina overzicht. De rol van informatie (resourcenaam Amazon)-waarde later gebruiken wanneer u uw configuratie in Cloudyn registreert.  
    ![de informatie van de rol van de pagina overzicht kopiëren](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cloudyn"></a>AWS IAM roltoegang in Cloudyn configureren

1. Open de Cloudyn-portal vanuit Azure portal of Ga naar https://azure.cloudyn.com/ en meld u aan.
2. Klik op het tandwiel-symbool en selecteer vervolgens **Cloudaccounts**.
3. Selecteer in het beheer van Accounts de **AWS-Accounts** tabblad en klik vervolgens op **nieuwe toevoegen +** .
4. In **accountnaam**, typ een naam voor het account.
5. Naast **toegangstype**, selecteer **IAM-rol**.
6. In de **rol informatie** veld, plak de waarde die u eerder hebt gekopieerd en klik vervolgens op **opslaan**.  
    ![Plak de informatie van de rol in het AWS-Account toevoegen](./media/connect-aws-account/add-aws-account-box.png)


Uw AWS-account wordt weergegeven in de lijst van accounts. De **eigenaar-ID** vermeld komt overeen met de waarde van de rol van informatie. Uw **accountstatus** moet hebben een groen vinkje symbool waarmee wordt aangegeven of toegang hebt tot Cloudyn uw AWS-account. Totdat u gedetailleerde AWS facturering inschakelt, wordt de consolidatiestatus van uw weergegeven als **zelfstandige**.

![Status van AWS-account wordt weergegeven op de pagina Accounts beheren](./media/connect-aws-account/aws-account-status01.png)

Cloudyn begint met het verzamelen van de gegevens en rapporten te vullen. Volgende [gedetailleerde AWS facturering inschakelen](#enable-detailed-aws-billing).


## <a name="aws-user-based-access"></a>AWS-toegang op basis van gebruiker

De volgende secties helpen u bij het maken van een gebruiker alleen-lezen toegang te hebben tot Cloudyn.

### <a name="add-aws-read-only-user-based-access"></a>AWS alleen-lezen toegang op basis van een gebruiker toevoegen

1. Aanmelden bij de AWS-console op https://console.aws.amazon.com/iam/home en selecteer **gebruikers**.
2. Klik op **Add User**.
3. In de **gebruikersnaam** veld, typt u de naam van een gebruiker.
4. Voor **toegangstype**, selecteer **toegang op programmeerniveau** en klikt u op **volgende: machtigingen**.  
    ![Voer een gebruikersnaam op de pagina van de gebruiker toevoegen](./media/connect-aws-account/add-user01.png)
5. Selecteer voor machtigingen, **bestaande beleidsregels rechtstreeks koppelen**.
6. Onder **koppelen van beleidsregels voor beveiligingsmachtigingen**, in de **beleidstype** filter vak zoeken, type `ReadOnlyAccess`, selecteer **ReadOnlyAccess**, en klik vervolgens op **volgende : Controleer**.  
    ![Selecteer ReadOnlyAccess machtigingen instellen voor de gebruiker](./media/connect-aws-account/set-permission-for-user.png)
7. Controleer of uw selecties kloppen en klik vervolgens op de pagina controleren **gebruiker maken**.
8. Op de pagina is voltooid, worden uw sleutel-ID en -geheim toegang toegangssleutel weergegeven. U kunt deze informatie gebruiken om te registreren in Cloudyn configureren.
9. Klik op **downloaden .csv** en sla het bestand credentials.csv op een veilige locatie.  
    ![Klik op downloaden .csv om op te slaan van de referenties](./media/connect-aws-account/download-csv.png)

### <a name="configure-aws-iam-user-based-access-in-cloudyn"></a>AWS IAM op basis van een gebruiker toegang in Cloudyn configureren

1. Open de Cloudyn-portal vanuit Azure Portal of ga naar https://azure.cloudyn.com/ en meld u aan.
2. Klik op het tandwiel-symbool en selecteer vervolgens **Cloudaccounts**.
3. Selecteer in het beheer van Accounts de **AWS-Accounts** tabblad en klik vervolgens op **nieuwe toevoegen +** .
4. Voor **accountnaam**, typ de naam van een account.
5. Naast **toegangstype**, selecteer **IAM gebruiker**.
6. In **toegangssleutel**, plak de **toegang ID sleutel** waarde uit het bestand credentials.csv.
7. In **geheime sleutel**, plak de **geheime toegangssleutel** waarde uit het bestand credentials.csv en klik vervolgens op **opslaan**.  

Uw AWS-account wordt weergegeven in de lijst van accounts. Uw **accountstatus** moet hebben een groen vinkje symbool.

Cloudyn begint met het verzamelen van de gegevens en rapporten te vullen. Volgende [gedetailleerde AWS facturering inschakelen](#enable-detailed-aws-billing).

## <a name="enable-detailed-aws-billing"></a>Gedetailleerde AWS facturering inschakelen

Gebruik de volgende stappen uit om uw informatie van de rol van AWS. De informatie van de rol kunt u lees-en schrijfmachtigingen voor een bucket van facturering verlenen.

1. Aanmelden bij de AWS-console op https://console.aws.amazon.com en selecteer **Services**.
2. In de Search Service vak *IAM*, en selecteert u die optie.
3. Selecteer **rollen** in het menu links.
4. Selecteer in de lijst met rollen, de rol die u hebt gemaakt voor toegang tot Cloudyn.
5. Klik op de pagina overzicht van rollen om te kopiëren de **rol informatie**. Houd de informatie van de rol bij de hand hebt voor de latere fasen.

### <a name="create-an-s3-bucket"></a>Een S3-bucket maken

U maakt een S3-bucket voor het opslaan van gedetailleerde informatie over facturering.

1. Aanmelden bij de AWS-console op https://console.aws.amazon.com en selecteer **Services**.
2. In de Search Service vak *S3*, en selecteer **S3**.
3. Klik op de pagina Amazon S3 **maken bucket**.
4. In de wizard bucket, kiest u een Bucket-naam en een regio en klik vervolgens op **volgende**.  
    ![Voorbeeld van de gegevens een van de pagina van de bucket maken](./media/connect-aws-account/create-bucket.png)
5. Op de **eigenschappen instellen** pagina, behouden de standaardwaarden en klik vervolgens op **volgende**.
6. Klik op de pagina controleren op **maken bucket**. De bucket-lijst wordt weergegeven.
7. Klik op de bucket die u hebt gemaakt en selecteer de **machtigingen** tabblad en selecteer vervolgens **Bucket beleid**. De Bucket editor wordt geopend.
8. De volgende JSON-voorbeeld Kopieer en plak deze in de beleidseditor Bucket.
   - Vervang `<BillingBucketName>` met de naam van de S3-bucket.
   - Vervang `<ReadOnlyUserOrRole>` met de rol of de gebruiker informatie die u eerder hebt gekopieerd.

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
    ![Klik op opslaan in de beleidseditor Bucket](./media/connect-aws-account/bucket-policy-editor.png)


### <a name="enable-aws-billing-reports"></a>AWS rapporten facturering inschakelen

Nadat u maken en configureren van de S3-bucket, gaat u naar [facturering voorkeuren](https://console.aws.amazon.com/billing/home?#/preference) in de AWS-console.

1. Selecteer op de pagina Voorkeuren **facturering rapporten ontvangen**.
2. Onder **facturering rapporten ontvangen**, voer de naam van de bucket die u hebt gemaakt en klik vervolgens op **controleren**.  
3. Selecteer alle vier meldingsopties granulariteit en klik vervolgens op **voorkeuren opslaan**.  
    ![Selecteer granulariteit rapporten inschakelen](./media/connect-aws-account/enable-reports.png)

Cloudyn haalt gedetailleerde informatie over de facturering van de S3-bucket gedetecteerd en gevuld rapporten nadat gedetailleerde facturering is ingeschakeld. Duurt maximaal 24 uur tot gedetailleerde factureringsgegevens wordt weergegeven in de Cloudyn-console. Als gedetailleerde factureringsgegevens beschikbaar is, wordt de status van uw account consolidatie weergegeven als **geconsolideerde**. Status van de account wordt weergegeven als **voltooid**.

![consolidatiestatus wordt weergegeven op het tabblad AWS-Accounts](./media/connect-aws-account/consolidated-status.png)

Sommige van de optimalisatierapporten is mogelijk een paar dagen aan gegevens om op te halen van de grootte van de steekproef van een voldoende gegevens voor nauwkeurige aanbevelingen.

## <a name="next-steps"></a>Volgende stappen

- Voor meer informatie over Cloudyn, blijven de [gebruik en kosten bekijken](tutorial-review-usage.md) voor Cloudyn.
