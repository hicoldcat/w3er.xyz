---
title: 揭秘 Merkle 树：理论分步实施指南
description: null
author: 李留白
weight: 0
date: 2023-09-17T15:19:50.190Z
lastmod: 2023-09-17T15:30:49.562Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202309172324944.png
---

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202309172324944.png)

## 介绍

大家好，在这篇文章中我将向大家简单介绍一下 Merkle 树以及我们如何实现 Merkle 树。首先，我将向您解释有关 Merkle 树的一些基本知识，然后我们将进入实现部分。我尽力解释每一行代码，以便大家能够简单地理解。那么让我们开始吧。

## 什么是Merkle树数据结构

首先，我们需要了解三个小主题，即“什么是 Merkle 树？” “Merkle Root是什么？” “Merkle证明是什么？”

### Merkle树

Merkle 树也称为哈希树，是计算机科学和区块链技术中使用的加密数据结构。它是一个树结构，其中每个叶节点（“Ta，Tb，Tc，Td，.....，Th”）代表一个数据块，每个非叶节点（“Ha，Hb，Hc，Hd，..”）代表一个数据块。 ..., Hh") 是其子节点哈希值的哈希值。顶部节点（“Habcdefgh”）称为根哈希，代表整个数据集。Merkle 树可以有效地验证数据完整性并促进快速、安全的数据检索。在区块链中，Merkle树通过以紧凑且防篡改的方式汇总数据，实现了交易的高效验证和整个链的不变性。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202309172327632.png)

在上面的示例图中，您可以看到实际的 Merkle 树数据结构是什么样的。正如您在此图中看到的“Ta，Tb，Tc，Td，.....，Th”，这是我们所做的交易。在交易上方，您可以看到另一层“Ha，Hb，Hc，Hd，.....，Hh”这是我们所做的以下交易的哈希。现在重点来了。正如您在上面看到的，还有三层，因此在下一层中我们可以看到“Hab，Hcd，Hef，Hgh”，这是子哈希的哈希。当我们连接 Ha 和 Hb 并找到连接形式的哈希值时，我们得到 Hab，当我们连接 Hc 和 Hd 并找到连接形式的哈希值时，我们得到 Hcd，这一步对于其他哈希值（Hef，Hgh）相同）。

> 请注意，交易的大小不是固定的。就我而言，只有 8 笔交易，这仅用于解释目的。

对于“Habcd”，我们连接“Hab & Hcd”并使用 sha256 哈希算法查找哈希，此步骤与“Hefgh”情况相同。最后，当我们连接“Habcd & Hefgh”并找到它的哈希值时，我们得到“Habcdefgh”哈希值。这称为根哈希。

### Merkle根

Merkle 根是 Merkle 树的顶部节点/哈希。在我们的例子中，哈希“ Habcdefgh ”是根节点。

### Merkle证明

Merkle 证明也称为 Merkle 路径。Merkle 证明是一种结构，它保存 Merkle 树所需的最少哈希值/节点，用于证明树中的特定哈希值。在我们的例子中，假设我们要检查 Ha 是否属于 Habcdefgh 根。然后我们的 Merkle 证明包含一个哈希数组，需要证明 Ha 属于 Habcdefgh，如 [Ha, Hb, Hcd, Hefgh]。我们只需要这四个哈希来检查 Ha 是否属于 Habcdegh。现在你想“这怎么可能？为什么我们只需要这四个哈希？其他哈希呢？”。这背后的原因是，如果我们有 Ha 和 Hb，抛出这些哈希值，我们就可以构造 Hab 哈希值，现在我们有了 Hab 哈希值，但要构造 Habcd 哈希值，我们需要 Hcd 哈希值，这就是我们采用 Hcd 而不是 Hab 的原因。现在我们有了 Habcd 哈希，因为我们从 Hab 和 Hcd 构造哈希，但我们需要 Hefgh 来构造根哈希 Habcdefgh，这就是我们在数组中采用 Hefgh 哈希的原因。现在我希望你们都能明白我想说的。

## Node JS 中的实现

经过太多的理论部分之后，我们终于来到了实现部分。我将尝试尽可能简单地解释实现部分，以便您能够理解。首先，我们**导入 crypto 模块**，因为该模块提供 sha256、随机字节生成器以及我们将在 Merkle 树实现中使用的其他功能。我们编写的用于导入加密模块的代码行如下所示：-

```javascript
import crypto from 'crypto';
```

现在我们需要定义两个用于**方向目的的变量（左和右）。**为什么我们需要这个方向变量？因为正如我已经告诉过你的 Merkle 证明，我们需要最少的哈希来证明特定哈希是该根哈希的一部分。正如您在上图中看到的 Ha 和 Hb 对，Ha 位于左侧，Hb 位于右侧。这对于 Hc 和 Hd 对来说是相同的，Hc 位于左侧，Hd 位于右侧，并且该列表对于层 1、2、3、...、n 是相同的。这有助于我们找到我们需要向左或向右的散列方向。如果我们有正确的散列，那么我们需要简单的左散列。

```javascript
const Left = 'left';
const Right = 'right';
```

您可以在图像中看到 Ha、Hb...，但您可能想知道实际的哈希值是什么样的。现在不用担心，我将向您展示实际的哈希值。在这里，我将定义一个变量，其中包含我们将用于创建 Merkle 树、证明 Merkle 证明以及查找这些哈希的 Markle 根的哈希列表。

```javascript
const listOfHashes = [
'0x0b46cbd82d0fe12c84fd676f2f6e4df01793fba1a88b49c6563f0487b9c14285',
'0x6ef163a08119da6c77d64c32946ab80a0e6c91c34612856be8a201a3673919c2',
'0x578ee1b8ac3c48f984b15621a476a02cbf46be9ee4f0b4691c16476ce389a0d5',
'0x268a39246efd811aa6b9377910e7a13131de485641907e87fd31ca6bc36a03b1',
'0x7dff748603fdeba8b761df81c372390c4fc4a6006e1c74933b81bd54dd904073',
'0x0624dc85cea85575ed94a290b5c3b3a0caa8a1c2e888c287f0689d4e4f4d4ba5',
'0x4ff2928281aa0d7f48d025576f0bad4794eb26cadf06e177a1ca073ff451eec7',
'0xba7f0db9e9ef33eeea0ef0645738c28328fe5e95acdcb8812b96437c9fde0ef1',
'0xb840fa85ebb73022fc52965f67a987cd8be7b0e22d1e5b8e4c898e3687f360b0',
'0x10e2dae66b1eed88f323ba69066a9f5d9cf7d1ae8f67e20a39fc898a1f4aad73',
'0x717dfefbb729adb0fc1b8982dd345161b19613ece76737ba75400de41802c11c',
'0x2562ea7215f843b9d6cacb3449e9af8caf9bea6a9735791b8072263b1a883ba0',
'0xff693258f9bed50e203866937b5f6a1941eaea4633092ee539f3796cde1cd823',
'0x6e657fc4835f14a0f3dee237c4fb3d91f1a7105b752f66f573cfd165e32247e5',
'0x555d04168bbfda587aeda61805b3dd4dd70aa62613a8671dc871109b66081e19'
];
```

定义一个函数来帮助我们找到**数据的 sha256 哈希值**。我们可以通过使用之前导入的加密模块来实现这一点。如果我们尝试自己完成，那么这是一个非常复杂且耗时的过程，因为我们需要自己实现 sha256 算法。

```javascript
const sha256 = data => {
    return crypto.createHash('sha256').update(data).digest().toString('hex');
};
```

定义一个函数来**查找散列的方向（左和右），**这就是为什么我们已经定义了两个变量 Left 和 Right。我们怎样才能得到这个方向呢？如果散列位于偶数索引上，则表示它位于左侧，或者如果散列位于奇数索引上，则表示它位于右侧，则为简单。如果我们得到哈希的索引，那么我们可以找到该索引与 2 的模，如果模等于 0，则为左，否则为右。

```javascript
const findDirectionOfHash = (hash, tree) => {
    const hashIndex = merkleTree[0].findIndex(h => h === hash);
    return hashIndex % 2 === 0 ? LEFT : RIGHT;
};
```

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202309172328521.png)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202309172328652.png)

在第一张图中，您可以看到 E 只有一个左散列，并且没有剩下的散列可供 E 连接并完成与 EE 情况相同的过程。所以要解决这个问题我们只需将哈希E和EE情况加倍即可完成树的过程。你可以在第二张图片中看到。实现此目的的代码如下：-

```javascript
function isEven(hashes) {
    if(hashes.length % 2 !== 0) {
        hashes.push(hashes[hashes.length - 1]);
    }
}
```

是时候计算**这个给定的 hash 列表的 Root 了**。你们准备好了吗？但愿如此。我们需要定义一个函数来计算哈希值的根。我们走吧。

```javascript
function FindRootOfHashes(hashes){
      if(!hashes || hashes.length == 0) {
        return ''; // If this condition match then it's return the empty string.
    }
    isEven(hashes);
    const listOfTheHashes = [];
    for(let i = 0; i < hashes.length; i += 2) {
    // Concatenate the two hash with each other
        const hashPairConcatenated = hashes[i] + hashes[i + 1];
    //Then after concatenation we finding the hash of the concatenation pair.
        const hash = sha256(hashPairConcatenated);
    //Then we pushing this hash to the combinedHashes array.
        listOfTheHashes.push(hash);
    }
    // If the listOfTheHashes length is 1, it means that we have the merkle root already
    // and we can return
    if(listOfTheHashes.length === 1) {
        return listOfTheHashes.join('');
    }
    return FindRootOfHashes(listOfTheHashes);
}
```

这里需要理解代码的流程。让我向你解释一下。我们有一个包含我们已经定义的哈希列表的数组。该函数采用哈希列表并执行我们在函数内编写的任务。首先，它运行“if”条件，即数组的长度是否等于零。如果是，则返回空字符串，否则将移至下一行代码。在下一行代码中，它调用另一个函数“isEven()”来检查数组的长度是偶数还是奇数。如果数组的长度是奇数，那么它会将最后一个索引哈希加倍，将其推入数组中，然后返回到下一行代码。在下一行中，我们创建一个包含空动态数组的新变量。为什么我们需要创建这个变量？因为在这个变量中，我们将使用 sha256 找到每对的哈希值后推送得到的哈希值。在下一行中，我们运行 for 循环，开始从 0 迭代到 hashes.length-1 并将 I 的值增加 2，因为找到了两对的 sha256 哈希值，这是因为我们这样做了。连接并找到哈希抛出 sha256 算法后，我们将该哈希推送到我们之前定义的“listOfHashes”数组中。当循环完成后，我们从循环中退出并移至下一行代码。在下一行中，我们将比较“哈希列表”的长度，因为如果长度等于 1，则意味着我们得到哈希根，如果长度不等于 1，则我们将移至下一行。在那条线里面，我们再次调用 FindRootOfHashes 一次，此时我们发送的是 listOfHashes 数组，而不是 hashes 数组。我们需要执行相同的步骤来获取作为根的一个散列，并一次又一次地执行相同的步骤，直到我们在“散列列表”中获得一个散列。如果我们得到的哈希列表的长度等于 1，那么我们只需结束该函数并返回最终的哈希，即我们的根。这就是我们获取根哈希的方式，我希望您理解代码的流程。如果我们得到的哈希列表的长度等于 1，那么我们只需结束该函数并返回最终的哈希，即我们的根。这就是我们获取根哈希的方式，我希望您理解代码的流程。如果我们得到的哈希列表的长度等于 1，那么我们只需结束该函数并返回最终的哈希，即我们的根。这就是我们获取根哈希的方式，我希望您理解代码的流程。

现在是时候看看运行这个函数后我们的结果是什么了。

```javascript
const rootHash = FindRootOfHashes(listOfHashes);
console.log('The root of this list of hashes:',rootHash);
```

> 此哈希列表的根：d9e373ca7462868bc64ecefe14a2c13c954158981773f901702cd3351f2ba57a
>
> 这是我们的结果，请您也检查一下。

到这里寻找Merkle root部分就完成了，现在让我们进入**Merkle树实现部分。**

```javascript
function findTreeOfHashes(hashes) {
    if(!hashes || hashes.length === 0) {
        return [];
    }
    const tree = [hashes];
    const createTree = (hashes, tree) => {
        if(hashes.length === 1) {
            return hashes;
        }
        isEven(hashes);
        const listOfTheHashes = [];
        for(let i = 0; i < hashes.length; i += 2) {
            const hashesConcatenated = hashes[i] + hashes[i + 1];
            const hash = sha256(hashesConcatenated);
            listOfTheHashes.push(hash);
        }
        tree.push(listOfTheHashes);
        return createTree(listOfTheHashes, tree);
    }
    createTree(hashes, tree);
    return tree;
}
```

我知道你在告诉自己，这是什么？我可以理解你的感受，但是如果你理解这个函数的流程，那么这段代码对你来说就变得很简单了，让我们开始吧。在函数的开头有相同的 if 条件代码，用于检查给定哈希列表的长度，与之前的根函数相同，我认为这行代码没有问题。在下一行中，我们做了与之前相同的操作，就像我们创建一个包含其中的哈希列表的变量一样。我们创建这个变量是因为在这个数组中我们将保存整个树，您可以将其理解为二维数组意味着数组中的数组。在下一行中，我们创建另一个名为“createTree”的函数，在参数内我们传递哈希列表以及树。现在，直到 for 循环的下一个代码与上一个函数相同，您可以从那里阅读，因为如果我要在这里重复，那么本文的篇幅就会变得太长。在下一行中，我们将数组推入树变量中，并再次调用该函数，直到哈希列表的大小不等于 1。在这个函数之外，我们第一次调用该函数，我希望你有一些关于 Node js 的矿工知识。最后，我们只是返回整个树，在该树中，我们拥有整个 Merkle 树。让我们调用这个函数并检查它的结果。我们将数组推入树变量中，并再次调用该函数，直到哈希列表的大小不等于 1。在这个函数之外，我们第一次调用该函数，我希望你有一些关于 Node js 的矿工知识。最后，我们只是返回整个树，在该树中，我们拥有整个 Merkle 树。让我们调用这个函数并检查它的结果。我们将数组推入树变量中，并再次调用该函数，直到哈希列表的大小不等于 1。在这个函数之外，我们第一次调用该函数，我希望你有一些关于 Node js 的矿工知识。最后，我们只是返回整个树，在该树中，我们拥有整个 Merkle 树。让我们调用这个函数并检查它的结果。

```javascript
const treeHashes = findTreeOfHashes(listOfHashes);
console.log('this is our Merkle tree:', treeHashes);
```

这就是我们的结果，而且时间太长了。

```javascript
This is our Merkle tree: [ 

[ '0x0b46cbd82d0fe12c84fd676f2f6e4df01793fba1a88b49c6563f0487b9c14285',
 '0x6ef163a08119da6c77d64c32946ab80a0e6c91c34612856be8a201a3673919c2',
 '0x578ee1b8ac3c48f984b15621a476a02cbf46be9ee4f0b4691c16476ce389a0d5',
 '0x268a39246efd811aa6b9377910e7a13131de485641907e87fd31ca6bc36a03b1',
 '0x7dff748603fdeba8b761df81c372390c4fc4a6006e1c74933b81bd54dd904073',
 '0x0624dc85cea85575ed94a290b5c3b3a0caa8a1c2e888c287f0689d4e4f4d4ba5',
 '0x4ff2928281aa0d7f48d025576f0bad4794eb26cadf06e177a1ca073ff451eec7',
 '0xba7f0db9e9ef33eeea0ef0645738c28328fe5e95acdcb8812b96437c9fde0ef1',
 '0xb840fa85ebb73022fc52965f67a987cd8be7b0e22d1e5b8e4c898e3687f360b0',
 '0x10e2dae66b1eed88f323ba69066a9f5d9cf7d1ae8f67e20a39fc898a1f4aad73',
 '0x717dfefbb729adb0fc1b8982dd345161b19613ece76737ba75400de41802c11c',
 '0x2562ea7215f843b9d6cacb3449e9af8caf9bea6a9735791b8072263b1a883ba0',
 '0xff693258f9bed50e203866937b5f6a1941eaea4633092ee539f3796cde1cd823',
 '0x6e657fc4835f14a0f3dee237c4fb3d91f1a7105b752f66f573cfd165e32247e5',
 '0x555d04168bbfda587aeda61805b3dd4dd70aa62613a8671dc871109b66081e19',
 '0x555d04168bbfda587aeda61805b3dd4dd70aa62613a8671dc871109b66081e19' ],

[ '51fe46cbc3cae464b94d7f51fe5854520e5a50b81d582abe27d025fc610cf2a3',
 '8dcbdd3d6474719dc8e1faf4a922a84783560442bb6245fce705605747b9d5ff',
 'c4b210cec6adbf2a9ac9aaf3473e1231cf3bc830ffcd3236b8727ac6800e1f69',
 'c623904a923b8d31ff1fd80311553dc35978d64d7b55d9095f1bc57e788e36bf',
 '58f0b429e67192eba97a43650e0ffd9d73a3306e26ab3843d9f77962bc783a08',
 'be30349d91afdd8dd2414e8c19cf6d34fc4887fa3aeb658a06e77a709fd1ff85',
 '833e8957094c9010f084342ba7d96163fe2e1d09075ceb1bb562b6b39270fc15',
 'c57509171c1bc365eb921131a206430dcb5f14f0882067ded6f25ee3b761f3ca' ],

 [ 'fdaeb371ce597102fea90f6a2d3044132fd87566b48eccbf5dcb48c9a5994214',
 'e72bc2faf42326f2c599eeb8a644df144aee84b8f35a9503b0c03a4d5b00f8e0',
 '8cc28b64770f06d4ba5c191309958486bff886ac34f9c30cfb80de72afa8cb91',
 '11873e6aa3024d9c4f1237a8dab59c68f8fc890d65643c8b69e2bf47e353a126' ],

 [ 'ed7be99a093f6336ce28511e1dca2968559da8ee2993cd42e06e934272829c5d',
 '1fa1425ab0b2d77add1942ecfb7c0e8bd98870f6d799d001a959066bb410fd13' ], 

[ 'd9e373ca7462868bc64ecefe14a2c13c954158981773f901702cd3351f2ba57a' ] ] //root
```

我们终于完成了两个主要功能。**让我们转到下一个函数，它是哈希是否属于这棵树的**证明。如果您还在阅读这篇文章，谢谢。我在评论中解释了每一行，所以不要忘记阅读它。

```javascript
function findTheMerkleProof(hash, hashes) {
// This candition is checking the size of the hashes array as well as checking
// that hash and hashes is given or not. If the candition matches then it will
//return null.
    if(!hash || !hashes || hashes.length === 0) {
        return null;
    }
// Here we are creating a variable and call our findTreeOfHashes function with
//the given hashes array. The tree variable holds the entire Markle tree.
    const tree = findTreeOfHashes(hashes);
// This variable hold the hash as well as the direction of the hash. It is a type
// of array that contains object inside it.
    const merkleProof = [{
        hash,
// Here we are call the findDirectionOFHash function that we define earlier.
        direction: findDirectionOfHash(hash, tree)
    }];
// Here we finding the index of the hash because it is importand to find the 
// patner of this hash.
    let hashIndex = tree[0].findIndex(h => h === hash);
// for loop for finding the sibling direction as well as the index and after 
// finding this things we are creating siblingNode name variable that is object
// type and hold the hash and the direction of sibling.
    for(let level = 0; level < tree.length - 1; level++) {
        const isLeftChild = hashIndex % 2 === 0;
        const siblingDirection = isLeftChild ? RIGHT : LEFT;
        const siblingIndex = isLeftChild ? hashIndex + 1 : hashIndex - 1;
        const siblingNode = {
            hash: tree[level][siblingIndex],
            direction: siblingDirection
        };
// Now we are pushing the siblingNode values inside the merkleProof array that
// we defind above the hashIndex.
        merkleProof.push(siblingNode);
// Here we are trying to find the index of this hash parent like if the recent 
// hash index is 4 if we divide it with 2 then we get 2. So, the parent hash index
// is 2 simple. We are using Math.floor because if we have odd number index like 5
// then we try to divide it with two then the outcome is 2.5 but ther is no index like
// this in our array so Math.floor is round up the value 2.5 to 2.
        hashIndex = Math.floor(hashIndex / 2);
    }
// In the end we are simply returning the whole merkleProof array.
    return merkleProof;
}
```

让我们在控制台中调用这个函数。

```javascript
// listOfHashes[4] means find proof for index number 4 hash.
const findMerkleProof = findTheMerkleProof(listOfHashes[4], listOfHashes);
console.log('findMerkleProof: ', findMerkleProof);
```

这就是我们的结果

```javascript
findMerkleProof:  [
  {
    hash: '0x7dff748603fdeba8b761df81c372390c4fc4a6006e1c74933b81bd54dd904073',
    direction: 'left'
  },
  {
    hash: '0x0624dc85cea85575ed94a290b5c3b3a0caa8a1c2e888c287f0689d4e4f4d4ba5',
    direction: 'right'
  },
  {
    hash: 'c623904a923b8d31ff1fd80311553dc35978d64d7b55d9095f1bc57e788e36bf',
    direction: 'right'
  },
  {
    hash: 'fdaeb371ce597102fea90f6a2d3044132fd87566b48eccbf5dcb48c9a5994214',
    direction: 'left'
  },
  {
    hash: '1fa1425ab0b2d77add1942ecfb7c0e8bd98870f6d799d001a959066bb410fd13',
    direction: 'right'
  }
]
```

这就是今天的全部内容，我知道这太长了，但我试图轻松地解释一切，这就是为什么它太长了。感谢您阅读到最后，很快再见。不断阅读，不断学习，不断成长。

> 原文：https://blockchaindeveloperblog.hashnode.dev/demystifying-merkle-trees-a-step-by-step-implementation-guide-with-theory