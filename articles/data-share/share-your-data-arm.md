---
title: クイック スタート:組織の外部と共有する - Azure Data Share
description: クイックスタート - Azure Data Share と Resource Manager テンプレートを使用して顧客やパートナーとデータを共有する
author: mumian
ms.author: jgao
ms.service: data-share
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/17/2020
ms.openlocfilehash: 62c800e8da3ab4f99b0933e286debcb05c5c3e22
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88575651"
---
# <a name="tutorial-share-data-using-azure-data-share-and-resource-manager-templates"></a>チュートリアル:Azure Data Share と Resource Manager テンプレートを使用してデータを共有する

Azure Resource Manager テンプレートを使用して Azure ストレージ アカウントから新しい Azure データ共有を設定し、Azure 組織の外部の顧客やパートナーとのデータの共有を開始する方法を学習します。 サポートされているデータ ストアの一覧については、「[Azure Data Share でサポートされているデータ ストア](./supported-data-stores.md)」を参照してください。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-share-share-storage-account%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-data-share-share-storage-account/)からのものです。

:::code language="json" source="~/quickstart-templates/101-data-share-share-storage-account/azuredeploy.json":::

このテンプレートでは、次のリソースが定義されています。

* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts):
* [Microsoft.Storage/storageAccounts/blobServices/containers](/azure/templates/microsoft.storage/storageaccounts/blobservices/containers)
* [Microsoft.Storage/storageAccounts/providers/roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [Microsoft.DataShare/accounts](/rest/api/datashare/accounts/create)
* [Microsoft.DataShare/accounts/shares](/rest/api/datashare/shares/create)
* [Microsoft.DataShare/accounts/shares/dataSets](/rest/api/datashare/datasets/create)
* [Microsoft.DataShare/accounts/shares/invitations](/rest/api/datashare/invitations/create)
* [Microsoft.DataShare/accounts/shares/synchronizationSettings](/rest/api/datashare/synchronizationsettings/create)

このテンプレートでは、次のタスクを実行します。

* ストレージ アカウントと、データ共有データ ソースとして使用されるコンテナーを作成する。
* データ共有アカウントとデータ共有を作成する。
* ストレージ BLOB データ閲覧者ロールをソース データ共有リソースに付与するロールの割り当てを作成する。 「[Azure Data Share のロールと要件](./concepts-roles-permissions.md)」を参照してください。
* データセットをデータ共有に追加する。
* 受信者をデータ共有に追加する。
* データ共有のスナップショット スケジュールを有効にする。

このテンプレートは学習用に作成されています。 実際には、通常、既存のストレージ アカウントにいくつかのデータがあります。 テンプレートまたはスクリプトを実行してデータセットを作成する前に、ロールの割り当てを作成する必要があります。 テンプレートをデプロイするときに、次のエラー メッセージが表示されることがあります。

```error message
"Missing permissions for DataShareAcccount on resource 'subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>' (Code: 5006)"
```

これは、RBAC の割り当てが完了する前に、デプロイによってデータセットの作成が試行されているためです。 エラー メッセージにもかかわらず、デプロイは成功する可能性があります。  引き続き、[デプロイされたリソースを確認する](#review-deployed-resources)ことができます。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. Azure にサインインし、テンプレートを開くには次の画像を選択します。

    [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-share-share-storage-account%2Fazuredeploy.json)
1. 次の値を選択または入力します。

    * **[サブスクリプション]** : データ共有とその他のリソースの作成に使用する Azure サブスクリプションを選択します。
    * **[リソース グループ]** : **[新規作成]** を選択して新しいリソース グループを作成するか、既存のリソース グループを選択します。
    * **場所**: リソース グループの場所を選択します。
    * **[プロジェクト名]** : プロジェクト名を入力します。  プロジェクト名は、リソース名を生成するために使用されます。  前のテンプレートの変数の定義を確認してください。
    * **[場所]** : リソースの場所を選択します。  リソース グループに対して同じ場所を使用できます。
    * **[Invitation Email]\(招待メール\)** : データ共有の受信者の Azure ログイン用メール アドレスを入力します。  メールの別名は機能しません。

    残りの設定については既定値を使用します。
1. **[上記の使用条件に同意する]** を選択し、 **[購入]** を選択します。

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 作成したデータ共有アカウントを開きます。
1. 左側のメニューで、 **[Send Shares]\(共有の送信\)** を選択します。  ストレージ アカウントが表示されます。
1. [ストレージ アカウント] を選択します。  **[詳細]** の下に、テンプレートで構成した同期設定が表示されます。

    ![Azure Data Share のストレージ アカウントの同期設定](./media/share-your-data-arm/azure-data-share-storage-account-synchronization-settings.png)
1. 上部から **[招待]** を選択します。 テンプレートをデプロイするときに指定したメール アドレスが表示されます。 **[状態]** は **[Pending]\(保留中\)** になっています。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、リソース グループを削除します。これにより、リソース グループ内のリソースが削除されます。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure データ共有を作成し、受信者を招待する方法について説明しました。 データ コンシューマーがデータ共有を受け入れて受信できるようにする方法については、続けて[データの受け入れと受信](subscribe-to-data-share.md)に関するチュートリアルを行ってください。
