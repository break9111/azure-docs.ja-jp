---
title: Azure Cosmos DB の Azure Synapse Link (プレビュー) を構成して使用する
description: Azure Cosmos アカウントの Synapse Link を有効にする方法、分析ストアを有効にしたコンテナーを作成する方法、Synapse ワークスペースに Azure Cosmos データベースを接続する方法、クエリを実行する方法について説明します。
author: Rodrigossz
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/19/2020
ms.author: rosouz
ms.openlocfilehash: 9499fe2140f4a345d48bce6ef010989cfc22c58e
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037084"
---
# <a name="configure-and-use-azure-synapse-link-for-azure-cosmos-db-preview"></a>Azure Cosmos DB の Azure Synapse Link (プレビュー) を構成して使用する

Azure Cosmos DB の Synapse Link は、クラウド ネイティブのハイブリッド トランザクションと分析処理 (HTAP) の機能です。これを使用すると、Azure Cosmos DB のオペレーショナル データに対して準リアルタイムの分析を実行できます。 Synapse Link によって、Azure Cosmos DB と Azure Synapse Analytics の間の緊密でシームレスな統合が実現します。


> [!IMPORTANT]
> Azure Synapse Link を使用するには、前述のサポートされているリージョンのいずれかで Azure Cosmos アカウントと Azure Synapse Analytics ワークスペースをプロビジョニングする必要があります。サポートされているリージョンの一覧については、[Azure サービス更新情報](https://azure.microsoft.com/updates/)に関するページを参照してください。 

Azure Cosmos DB の Synapse Link を使用して分析クエリを実行するには、次の手順に従います。

* [Azure Cosmos アカウントの Synapse Link を有効にする](#enable-synapse-link)
* [分析ストアが有効な Azure Cosmos コンテナーを作成する](#create-analytical-ttl)
* [Azure Cosmos データベースを Synapse ワークスペースに接続する](#connect-to-cosmos-database)
* [Synapse Spark を使用して分析ストアにクエリを実行する](#query-analytical-store)

## <a name="enable-azure-synapse-link-for-azure-cosmos-accounts"></a><a id="enable-synapse-link"></a>Azure Cosmos アカウントの Azure Synapse Link を有効にする

### <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure アカウントを作成する](create-sql-api-dotnet.md#create-account)か、既存の Azure Cosmos アカウントを選択します。

1. Azure Cosmos アカウントに移動して、 **[機能]** ペインを開きます。

1. 機能一覧から **[Synapse Link]** を選択します。

   :::image type="content" source="./media/configure-synapse-link/find-synapse-link-feature.png" alt-text="Synapse Link プレビュー機能の検索":::

1. 次に、ご利用のアカウントで Synapse Link を有効にするように求めるメッセージが表示されます。 [有効化] を選択します。

   :::image type="content" source="./media/configure-synapse-link/enable-synapse-link-feature.png" alt-text="Synapse Link 機能の有効化":::

1. これで、ご利用のアカウントで Synapse Link を使用できるようになりました。 次に、分析ストアが有効なコンテナーを作成して、トランザクション ストアから分析ストアへのオペレーショナル データのレプリケートを自動的に開始する方法について確認します。

### <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート

[Azure Resource Manager テンプレート](manage-sql-with-resource-manager.md#azure-cosmos-account-with-analytical-store)では、SQL API の Synapse Link が有効な Azure Cosmos アカウントを作成します。 このテンプレートでは、分析 TTL を有効にして構成されたコンテナーと、手動または自動スケールのスループットを使用するオプションで、1 つのリージョンにコア (SQL) API アカウントを作成します。 このテンプレートをデプロイするには、readme ページで **[Azure に配置する]** をクリックします。

## <a name="create-an-azure-cosmos-container-with-analytical-store"></a><a id="create-analytical-ttl"></a> 分析ストアを使用して Azure Cosmos コンテナーを作成する

コンテナーの作成中に、Azure Cosmos コンテナーで分析ストアを有効にすることができます。 Azure portal を使用するか、Azure Cosmos DB SDK を使用してコンテナー作成時に `analyticalTTL` プロパティを構成することができます。

> [!NOTE]
> 現時点では、分析ストアは**新しい**コンテナーに対して有効にすることができます (新規、既存のどちらのアカウントでも)。

### <a name="azure-portal"></a>Azure portal

1. [Azure portal](https://portal.azure.com/) または [Azure Cosmos Explorer](https://cosmos.azure.com/) にサインインします。

1. Azure Cosmos アカウントに移動して、 **[データ エクスプローラー]** タブを開きます。

1. **[新しいコンテナー]** を選択し、データベースの名前、コンテナー、パーティション キー、スループットの詳細を入力します。 **[分析ストア]** オプションをオンにします。 分析ストアを有効にすると、`AnalyicalTTL` プロパティが既定値の -1 (無限のリテンション期間) に設定されたコンテナーが作成されます。 この分析ストアでは、レコードのすべての履歴バージョンが保持されます。

   :::image type="content" source="./media/configure-synapse-link/create-container-analytical-store.png" alt-text="Azure Cosmos コンテナーの分析ストアを有効にする":::

1. このアカウントで以前に Synapse Link を有効にしていない場合は、分析ストアが有効なコンテナーを作成するための前提条件であるため、これを行うように求めるメッセージが表示されます。 プロンプトが表示されたら、 **[Enable Synapse Link]\(Synapse Link を有効にする\)** を選択します。

1. **[OK]** を選択して、分析ストアが有効な Azure Cosmos コンテナーを作成します。

### <a name="net-sdk"></a>.NET SDK

次のコードでは、.NET SDK を使用して、分析ストアを持つコンテナーを作成します。 分析 TTL プロパティを必要な値に設定します。 許可される値の一覧については、[分析 TTL でサポートされる値](analytical-store-introduction.md#analytical-ttl)に関する記事を参照してください。

```csharp
// Create a container with a partition key, and analytical TTL configured to  -1 (infinite retention)
string containerId = “myContainerName”;
int analyticalTtlInSec = -1;
ContainerProperties cpInput = new ContainerProperties()
            {
Id = containerId,
PartitionKeyPath = "/id",
AnalyticalStorageTimeToLiveInSeconds = analyticalTtlInSec,
};
 await this. cosmosClient.GetDatabase("myDatabase").CreateContainerAsync(cpInput);
```

### <a name="java-v4-sdk"></a>Java V4 SDK

次のコードでは、Java V4 SDK を使用して、分析ストアを持つコンテナーを作成します。 `AnalyticalStoreTimeToLiveInSeconds` プロパティを必要な値に設定します。

```java
// Create a container with a partition key and  analytical TTL configured to  -1 (infinite retention) 
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

containerProperties.setAnalyticalStoreTimeToLiveInSeconds(-1);

container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

### <a name="python-v3-sdk"></a>Python V3 SDK

次のコードでは、Python SDK を使用して、分析ストアを持つコンテナーを作成します。

```python
import azure.cosmos.cosmos_client as cosmos_client
def create_collection_if_not_exists(cosmosEndpoint, cosmosKey, databaseName, collectionName):
    client = cosmos_client.CosmosClient(url_connection=cosmosEndpoint, auth={'masterKey': cosmosKey})

db = client.QueryDatabases("select * from c where c.id = '" + databaseName + "'").fetch_next_block()[0]
options = {
    'offerThroughput': 1000
}

container_definition = {
    'id': collectionName,
    "partitionKey": {  
        "paths": [  
        "/id"  
        ],  
        "kind": "Hash" 
    },
    "indexingPolicy": {  
    "indexingMode": "consistent",  
    "automatic": True,  
    "includedPaths": [],  
    "excludedPaths": [{
        "path": "/*"
    }]  
    },
    "defaultTtl": -1,
    "analyticalStorageTtl": -1
}

container = client.CreateContainer(db['_self'], container_definition, options)
```

### <a name="update-the-analytical-store-time-to-live"></a><a id="update-analytical-ttl"></a> 分析ストアの有効期間を更新する

特定の TTL 値で分析ストアを有効にしてから、後で別の有効な値に更新できます。 この値は、Azure portal または SDK を使用して更新できます。 さまざまな分析 TTL 構成オプションの詳細については、[分析 TTL でサポートされる値](analytical-store-introduction.md#analytical-ttl)に関する記事を参照してください。

#### <a name="azure-portal"></a>Azure portal

Azure portal を使用して分析ストアが有効なコンテナーを作成した場合は、それに -1 の既定の分析 TTL が含まれます。 この値を更新するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) または [Azure Cosmos Explorer](https://cosmos.azure.com/) にサインインします。

1. Azure Cosmos アカウントに移動して、 **[データ エクスプローラー]** タブを開きます。

1. 分析ストアが有効になっている既存のコンテナーを選択します。 それを展開し、次の値を変更します。

  * **[Scale & Settings]\(スケールと設定\)** ウィンドウを開きます。
  * **[設定]** で、** Analytical Storage Time to Live** を探します。
  * **[オン (既定値なし)]** または **[オン]** を選択し、TTL 値を設定します
  * **[保存]** をクリックして変更を保存します。

#### <a name="net-sdk"></a>.NET SDK

次のコードは、.NET SDK を使用して分析ストアの TTL を更新する方法を示しています。

```csharp
// Get the container, update AnalyticalStorageTimeToLiveInSeconds 
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Update analytical store TTL
containerResponse.Resource. AnalyticalStorageTimeToLiveInSeconds = 60 * 60 * 24 * 180  // Expire analytical store data in 6 months;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

#### <a name="java-v4-sdk"></a>Java V4 SDK

次のコードは、Java V4 SDK を使用して分析ストアの TTL を更新する方法を示しています。

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

// Update analytical store TTL to expire analytical store data in 6 months;
containerProperties.setAnalyticalStoreTimeToLiveInSeconds (60 * 60 * 24 * 180 );  
 
// Update container settings
container.replace(containerProperties).block();
```

## <a name="connect-to-a-synapse-workspace"></a><a id="connect-to-cosmos-database"></a> Synapse ワークスペースに接続する

Azure Synapse Link を使用して Azure Synapse Analytics Studio から Azure Cosmos DB データベースにアクセスする方法については、[Azure Synapse Link に接続する方法](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md)に関する記事の手順を参照してください。

## <a name="query-using-synapse-spark"></a><a id="query-analytical-store"></a> Synapse Spark を使用してクエリを実行する

Synapse Spark を使用してクエリを実行する方法については、[Azure Cosmos DB 分析ストアに対してクエリを実行する方法](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md)に関する記事の手順を参照してください。 この記事では、Synapse ジェスチャから分析ストアを操作する方法について、いくつかの例を紹介しています。 これらのジェスチャは、コンテナーを右クリックすると表示されます。 ジェスチャを使用すると、コードをすばやく生成し、ニーズに合わせて調整することができます。 また、1 回のクリックでデータを検出するのにも最適です。

## <a name="getting-started-with-azure-synpase-link---samples"></a><a id="cosmosdb-synapse-link-samples"></a>Azure Synpase Link の使用を開始する - サンプル

Azure Synapse Link の使用を開始する場合、[GitHub](https://aka.ms/cosmosdb-synapselink-samples) にサンプルが用意されています。 これらは、IoT および小売のシナリオでのエンド ツー エンドのソリューションを紹介しています。

## <a name="next-steps"></a>次のステップ

詳細については、次のドキュメントを参照してください。

* [Azure Cosmos DB の Azure Synapse Link。](synapse-link.md)

* [Azure Cosmos DB 分析ストアの概要。](analytical-store-introduction.md)

* [Azure Cosmos DB の Synapse Link に関してよく寄せられる質問。](synapse-link-frequently-asked-questions.md)

* [Azure Synapse Analytics での Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md)。

* [Azure Synapse Analytics での SQL サーバーレス/オンデマンド](../synapse-analytics/sql/on-demand-workspace-overview.md)。
