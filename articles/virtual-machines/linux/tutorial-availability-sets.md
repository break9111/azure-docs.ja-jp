---
title: チュートリアル - Azure での Linux VM の高可用性
description: このチュートリアルでは、Azure CLI を使って、可用性セットに高可用性仮想マシンを展開する方法について説明します
documentationcenter: ''
services: virtual-machines-linux
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: tutorial
ms.date: 01/17/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: e04f6120f7d9a5646a3b30553b5083c5259acae2
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87499638"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-the-azure-cli"></a>チュートリアル:Azure CLI を使用して高可用性仮想マシンを作成して展開する

このチュートリアルでは、可用性セットと呼ばれる機能を使用して、Azure で仮想マシン ソリューションの可用性と信頼性を向上させる方法を学習します。 可用性セットは、Azure にデプロイする VM を、複数の分離されたハードウェア クラスターに分散します。 これにより、Azure 内でハードウェアまたはソフトウェアの障害が発生した場合に影響を受けるのは VM のサブセットに限定され、ソリューション全体は引き続き利用可能であり、運用可能であることが保証されます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 可用性セットの作成
> * 可用性セットに VM を作成する
> * 使用可能な VM のサイズを確認する

このチュートリアルでは、[Azure Cloud Shell](../../cloud-shell/overview.md) で CLI を使用します。このバージョンは常に更新され最新になっています。 Cloud Shell を開くには、コード ブロックの上部にある **[試してみる]** を選択します。

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.30 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="overview"></a>概要

可用性セットは、Azure で使用できる論理グループ作成機能であり、グループに配置された VM リソースは、Azure データ センター内にデプロイされるときに互いに分離されます。 Azure では、可用性セット内に配置された VM は、複数の物理サーバー、コンピューティング ラック、ストレージ ユニット、およびネットワーク スイッチ間で実行されます。 ハードウェアまたは Azure ソフトウェアの障害が発生した場合に影響を受けるのは VM のサブセットに限定され、アプリケーション全体が停止することはなく、顧客は引き続きアプリケーションを利用できます。 可用性セットは、信頼性の高いクラウド ソリューションを構築する際に不可欠な機能です。

フロントエンド Web サーバーが 4 台あり、データベースをホストする 2 台のバックエンド VM を使用する典型的な VM ベースのソリューションを考えてみましょう。 Azure で VM をデプロイする前に、2 つの可用性セット ("Web" 階層用に 1 つ、"データベース" 層用に 1 つ) を定義します。 新しい VM を作成するときに、az vm create コマンドのパラメーターとして可用性セットを指定でき、可用性セット内に作成した VM は、Azure によって複数の物理的なハードウェア リソースに自動的に分離されます。 いずれかの Web サーバーまたはデータベース サーバー VM で問題が発生した場合でも、Web サーバーとデータベース VM の他のインスタンスは別のハードウェアで実行されているため、稼働し続けます。


## <a name="create-an-availability-set"></a>可用性セットの作成

可用性セットは、[az vm availability-set create](/cli/azure/vm/availability-set) を使用して作成できます。 この例では、*myResourceGroupAvailability* リソース グループ内の *myAvailabilitySet* という名前の可用性セットに対して、更新ドメインと障害ドメインの数を *2* に設定します。

最初に [az group create](/cli/azure/group#az-group-create) を使用してリソース グループを作成した後、可用性セットを作成します。

```azurecli-interactive
az group create --name myResourceGroupAvailability --location eastus

az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

可用性セットでは、障害ドメインと更新ドメインでリソースを分離できます。 **障害ドメイン**は、サーバーとネットワークとストレージ リソースの分離されたコレクションを表します。 前の例では、VM が展開されるときに、少なくとも 2 つの障害ドメインに可用性セットが分散されます。 可用性セットは、2 つの**更新ドメイン**にも分散されます。 2 つの更新ドメインを使用すると、Azure がソフトウェアの更新を実行するときに、VM リソースが分離されて、VM 上で実行するすべてのソフトウェアが同時に更新されるのを防ぎます。


## <a name="create-vms-inside-an-availability-set"></a>可用性セット内の VM の作成

VM は、ハードウェア全体で適切に分散させるために、可用性セット内に作成する必要があります。 可用性セットを作成した後に、既存の VM を追加することはできません。

[az vm create](/cli/azure/vm) を使って VM を作成するときに、`--availability-set` パラメーターを使って可用性セットの名前を指定します。

```azurecli-interactive
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --vnet-name myVnet \
     --subnet mySubnet \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys
done
```

可用性セット内に 2 つの仮想マシンが存在するようになります。 それらは同じ可用性セットに属しているため、VM と (データ ディスクを含む) すべてのリソースは、複数の分離された物理ハードウェアに分散されます。 この分散によって、VM ソリューション全体の可用性が大きく向上します。

可用性セットの分散は、ポータルで [リソース グループ]、myResourceGroupAvailability、myAvailabilitySet の順に移動して確認できます。 次の例で示すように、VM は 2 つの障害ドメインと更新ドメインに分散されています。

![ポータルの可用性セット](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>使用可能な VM のサイズのチェック

ハードウェアに VM に使用できるサイズがある場合は、後で新しい VM を可用性セットに追加できます。 ハードウェア クラスター上で可用性セットに使用可能なすべてのサイズを一覧表示するには、[az vm availability-set list-sizes](/cli/azure/vm/availability-set#az-vm-availability-set-list-sizes) を使います。

```azurecli-interactive
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * 可用性セットの作成
> * 可用性セットに VM を作成する
> * 使用可能な VM のサイズを確認する

次のチュートリアルに進み、仮想マシンのスケール セットについて学習してください。

> [!div class="nextstepaction"]
> [仮想マシン スケール セットを作成する](tutorial-create-vmss.md)

* 可用性ゾーンの詳細については、[Availability Zones に関するドキュメント](../../availability-zones/az-overview.md)を参照してください。
* また、可用性セットと可用性ゾーンの両方について取り上げたドキュメントも、[こちら](./manage-availability.md)でご覧いただけます。
* 可用性ゾーンを試してみたい場合は「[Azure CLI を使用して可用性ゾーン内に Linux 仮想マシンを作成する](./create-cli-availability-zone.md)」をご覧ください。
