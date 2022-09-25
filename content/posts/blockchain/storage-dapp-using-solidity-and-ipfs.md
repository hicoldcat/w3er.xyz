---
title: 使用Solidity和IPFS存储DApp
description: null
author: 李留白
weight: 0
date: 2022-09-25T07:11:52.652Z
lastmod: 2022-09-25T07:32:52.401Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220925151208.png
---

*想象一下有一个去中心化的存储应用，我们可以彼此分享我们的磁盘，中间没有任何服务器。想象一下，在这个去中心化的应用中，每个文件都可以通过一个简单的哈希值轻松地被分享和找到。听起来很有趣，对吗？嗯，这是一个正在进行的项目，我将解释它是如何工作的。*

## 简介

>你可以从[Githhub仓库查看源码](https://github.com/MCarlomagno/persssist)，最好能点亮小星星。

所有的一切都始于一个问题，当我试图弄清楚如何在dApp中存储大文件而不失去dApp的定义时，这个问题就出来了。如果应用程序将文件存储在一个集中的存储服务中，那么根据定义，该应用程序就不再是分散的了。

因此，经过一番研究，我发现了一个令人震惊的解决方案：[IPFS](https://ipfs.io/) 🤯。

### IPFS

IPFS是一个点对点的超媒体协议，这意味着一个在计算机之间共享媒体的协议。通过使用这个协议，你可以很容易地建立p2p网络，在各节点之间共享信息（这里的节点可以是你的电脑）。

幸运的是，存在一个高水平的Javascript包，用于访问和与该协议进行交互，这意味着你可以建立一个前端应用程序，将你的计算机作为一个节点，在网络上共享和访问文件。我想你已经知道这个应用程序将如何工作了😉。

但接下来的问题是关于如何组织文件、用户，以去中心化的方式引用文件元数据，并创建普遍可访问的信息。这里就是区块链和智能合约来帮助我们的地方。

### 智能合约

智能合约是一个简单的程序，用一个地址来识别，在区块链网络中运行交易。我不打算在这个概念上挖得太深，但简而言之，由于智能合约的不可更改性，我们可以把它作为一个小小的数据库。

## 构建应用程序

### 用Solidity创建智能合约

第一部分是在Solidity语言上创建一个智能合约，用于存储上传至应用程序的文件信息。我们将保存关于它们的一般信息，如文件名、类型、大小等。

正如你所看到的，代码是相当简洁和直接的，我们只有三个变量，一个函数，一个事件和一个代表系统中的文件的结构。代码是不言自明的，但为了更好地理解，我添加了一些注释。

[contract.sol ](https://gist.github.com/MCarlomagno/5466b9f2d7a6b3982f81b3dba8548dd1#file-contract-sol)

```solidity
// SPDX-License-Identifier: MIT
pragma solidity >=0.4.22 <0.9.0;

contract Persssist {
    string public name = "Persssist";
    uint256 public fileCount = 0;
    
    // this mapping behaves as a "catalog"
    // of files uploaded to the storage, we declare
    // it as public in order to access it directly from the Frontend
    mapping(uint256 => File) public files;

    struct File {
        uint256 fileId;
        string filePath;
        uint256 fileSize;
        string fileType;
        string fileName;
        address payable uploader;
    }

    event FileUploaded(
        uint256 fileId,
        string filePath,
        uint256 fileSize,
        string fileType,
        string fileName,
        address payable uploader
    );
    
    // we upload the file metadata
    // to the smart contract files
    // mapping in order to persist
    // the information.
    function uploadFile(
        string memory _filePath,
        uint256 _fileSize,
        string memory _fileType,
        string memory _fileName
    ) public {
        require(bytes(_filePath).length > 0);
        require(bytes(_fileType).length > 0);
        require(bytes(_fileName).length > 0);
        require(msg.sender != address(0));
        require(_fileSize > 0);
        
        // since solidity mappings
        // do not have a lenght attribute
        // the simplest way to control the amount 
        // of files is using a counter
        fileCount++;

        files[fileCount] = File(
            fileCount,
            _filePath,
            _fileSize,
            _fileType,
            _fileName,
            payable(msg.sender)
        );
        
        // From the frontend application
        // we can listen the events emitted from
        // the smart contract in order to update the UI.
        emit FileUploaded(
            fileCount,
            _filePath,
            _fileSize,
            _fileType,
            _fileName,
            payable(msg.sender)
        );
    }
}
```

### 使用Truffle和Ganache部署智能合约

最方便程序员调试和测试智能合约的方法，是通过使用本地开发环境，如[Truffle](https://trufflesuite.com/)，为我们的项目轻松创建良好的管道。

调试智能合约的另一个非常重要的工具是 [Ganache](https://trufflesuite.com/ganache/) （Truffle套件的一部分），这是一个本地区块链，你可以运行它来部署和调试你的合约，他们提供了一套账户和配置，有足够的以太坊来做你项目中的几乎所有事情。

因此，下一步是在我们的项目中设置truffle，为了做到这一点，我们可以用npm简单地安装它。

```
npm install -g truffle
```

然后，你可以用任何你想要的框架/库创建你的前端项目，在我的例子中是Next js。一旦你创建了这个项目，你就可以在它上面初始化一个truffle环境，在项目的根目录下定位控制台并输入。

```
truffle init
```

这将为你的项目创建一个基本配置，在其他文件中，将创建一个truffle-config.js，这个文件在项目中特别重要。

> 你可以在[这里](https://github.com/MCarlomagno/persssist/blob/main/truffle-config.js)检查项目配置。

为了在项目中实际设置我们的智能合约，我们应该运行一个迁移，你可以按照[以下步骤](https://trufflesuite.com/docs/truffle/getting-started/running-migrations/)进行。

### 测试合约

这一部分是智能合约开发中最重要的做法之一，为了自信地部署智能合约，应该尽可能地测试它。由于智能合约的性质是不可改变的，部署一个有错误的合约的成本非常高。

为了测试合同，Truffle配备了mocha和chai，这使得测试变得更加容易。

让我们看看智能合约测试的一些基本例子，你可以在这个测试文件中看到完整的测试套装。

**测试合同正确部署**

[basic_test_example.js](https://gist.github.com/MCarlomagno/4ba83fb2867c2793983f03ea6c4d718a#file-basic_test_example-js)

```js
  it('deployed successfully', async () => {
    const address = await this.contract.address
    assert.notEqual(address, 0x0)
    assert.notEqual(address, '')
    assert.notEqual(address, null)
    assert.notEqual(address, undefined)
  })
```

**测试上传一个有效的文件。**

[upload_test_example.js](https://gist.github.com/MCarlomagno/434564499b2a7ea3a4640d3005544155#file-upload_test_example-js)

```js
 it("uploads a valid file", async () => {
    // checks filecount variable
    // before uploading the file
    const countBeforeUpload = await this.contract.fileCount();
    
    await this.contract.uploadFile('path', 1, 'type', 'name');
    
    // checks filecount increased
    // by one after upload
    const countAfterUpload = await this.contract.fileCount();
    assert.equal(Number(countAfterUpload), Number(countBeforeUpload) + 1, "count increased to 1 after upload")
    
    // checks last file contains the same
    // info than the uploaded file
    const lastFile = await this.contract.files(countAfterUpload);
    assert.equal(lastFile.fileName, 'name', "last file has the filename of the previous uploaded file")
    assert.equal(lastFile.filePath, 'path', "last file has the path of the previous uploaded file")
    assert.equal(lastFile.fileSize, 1, "last file has the size of the previous uploaded file")
    assert.equal(lastFile.fileType, 'type', "last file has the type of the previous uploaded file")

  })
```

### 使用Metamask进行认证

一旦我们完成了应用程序的基本配置，我们就可以开始用Metamask进行用户认证，为此，我们有一个js API来连接应用程序和扩展，不需要任何库。

为了创造一个流畅的用户体验，我们可以创建一个方法，当有现有账户时自动连接。

[fetch_accounts.js](https://gist.github.com/MCarlomagno/312261d69a1b9cc0f878211fa13f16a3#file-fetch_accounts-js)

```js
async fetchAccounts() {
  if(typeof window === "undefined") return;
  return window
    .ethereum?.request({ method: "eth_accounts" })
    .catch((err: any) => console.log(err));
}
```

而另一种方法是强制请求，打开一个Metamask弹出窗口进行认证

[request_accounts.js ](https://gist.github.com/MCarlomagno/ab6ac15a6a9e94e56b952df53373a054#file-request_accounts-js)

```js
async requestAccounts() {
  if(typeof window === "undefined") return;
  return window
    .ethereum?.request({ method: "eth_requestAccounts" })
    .catch((err: any) => console.log(err));
}
```

### 用智能合约连接前端

在这个阶段，我们已经有了一个与Metamask扩展连接的前端应用程序和一个用Ganache运行的智能合约，现在我们需要找到一种方法来与Solidity合约作为API进行交互。

为此，我们将需要一些库，如 [Web3.js](https://web3js.readthedocs.io/en/v1.7.3/) 或 [Ethers.js](https://docs.ethers.io/v5/)。在我的例子中，我选择了Web3.js。

导入web3并创建合同对象

[raw](https://gist.github.com/MCarlomagno/d86014fb550ef41a17ef553f750c94a4/raw/68795a8b459232ad5659c284306b3af507573515/create-contract.js)[create-contract.js](https://gist.github.com/MCarlomagno/d86014fb550ef41a17ef553f750c94a4#file-create-contract-js)

```react
import Web3 from "web3";

// we import the abi file created after 
// the migration using truffle.
import Persssist from '../../public/abis/Persssist.json';


async initializeContractLocal() {
  if (window.ethereum) this.web3 = new Web3(window.ethereum)
  else if (window.web3) this.web3 = new Web3(window.web3.currentProvider);
  if(!this.web3) throw 'Web3 not initialized';
  
  const networkId = await this.web3.eth.net.getId();
  const networkData = (PersssistLocal as any).networks[networkId];
  if (networkData) {
    this.contract = new this.web3.eth.Contract(
      (Persssist as any).abi,
      networkData.address
    )
  }
}
```

*注意：有一个特殊的文件叫'Persssist'，这就是abi。以JSON格式表示的智能合约，作为Javascript和Solidity之间的接口。*

### 将智能合约与IPFS连接起来

一旦我们有了合同对象，我们就可以开始使用IPFS来上传文件。这里的诀窍是，我们可以使用唯一的路径在IPFS中引用一个文件，该路径作为IPFS文件系统中创建的每个文件的ID。

智能合约状态下的每个文件都会引用IPFS文件系统中的一个路径，这样就在合约和存储系统之间建立了一个可靠的连接。

中间的唯一元素是为每个用户在本地浏览器上运行的前端应用程序，以这种方式创建一个分散的存储应用程序。

### 上传文件

这个任务由2个步骤组成。

1. 将文件上传到IPFS，获得其独特的路径。
2. 在智能合约中用结果创建文件记录。

首先我们创建IPFS连接，这不是每次用户上传文件都需要的，但我在这个函数中这样做只是为了让它更清晰。

然后我们上传文件，只需使用缓冲区和文件类型，结果是我们获得IPFS文件系统上的文件路径。

[upload.js ](https://gist.github.com/MCarlomagno/26f992fbbce6cd0bb7519a901ea8c6cb#file-upload-js)

```js
import { create } from "ipfs-http-client";

async upload(file: File) {
    this.ipfs = create({
      host: 'ipfs.infura.io',
      port: 5001,
      protocol: 'https'
    });
  
    const blob = new Blob([file.buffer], { type: file.type });
    const result = await this.ipfs.add(blob);
      
    // the result contains the path
    // to the file on IPFS
    return result;
 }
```

第二步是在智能合约中实际存储文件信息，以便在未来轻松下载文件

[upload.js ](https://gist.github.com/MCarlomagno/93f5112dfcac580e1110e0a588a22aac#file-upload-js)

```js
 async uploadFileMetadata(
    path: string, 
    size: number, 
    type: string, 
    name: string, 
    account: string,
  ) {
    return this.contract.methods
        .uploadFile(path, size, type, name)
        .send({ from: account })
        .on('transactionHash', onSuccess)
        .on('error', onError);
  }
```

### 取出和下载文件

对于这一步，我们要做的是逆向过程。我们需要获取存储在智能合约中的文件，然后，使用唯一的路径，从IPFS文件系统中下载文件。

为了创造一个用户友好的互动，我们可以获取所有的文件，只下载用户选择的文件。

[fetch.js ](https://gist.github.com/MCarlomagno/5d1c5a794da405b5f5b1d8e713df0d17#file-fetch-js)

```js
 async getFilesMetadata(): Promise<PersssistFile[]> {
        const methods = this.contract.methods;
        const filesCount = await methods.fileCount().call();
        const filesMetadata: PersssistFile[] = [];
        for (var i = filesCount; i >= 1; i--) {
            const file = await methods.files(i).call()
            filesMetadata.push({
                fileId: file.id, 
                fileName: file.fileName, 
                filePath: file.filePath, 
                fileSize: file.fileSize, 
                fileType: file.fileType, 
                uploader: file.uploader
            });
        }
        return filesMetadata;
    }
```

在用户选择了要下载的文件后，我们可以开始下载的过程。这是最困难的部分之一，因为IPFS只下载压缩格式的文件，所以我们需要做一些变通的方法来获得正确的文件格式，在这种情况下，我们要添加untar库。

[donwload.js ](https://gist.github.com/MCarlomagno/f55238da8a8ed2675e96084770806928#file-donwload-js)

```js
const untar = await require("js-untar");

async download(file: PersssistFile) {
  const iterable = this.ipfs.get(file.filePath);
  var chunks: Uint8Array[] = [];
  
  // we need to use a for await for downloading 
  // the buffer in chunks.
  for await (const b of iterable) {
      chunks.push(b);
  }
  
  // the result is a tar file, so we need to find a way to
  // untar the file from the fronted, in my case I did it with untar lib.
  const tarball = new Blob(chunks, { type: 'application/x-tar' })
  const tarAsArrayBuffer = await tarball.arrayBuffer();
  const result = await this.untar(tarAsArrayBuffer);
  
  // finally we create the blob and download it.
  const resultFile = new Blob([result[0].buffer], { type: file.fileType })
  var url = window.URL.createObjectURL(resultFile);
  this.downloadURL(url, file.fileName);
}
```

## 结语

去中心化的应用是一个很好的机会，可以从web2.0赋予互联网用户和开发者。不需要在两个人的中间有什么东西来创造一个可信任的和流畅的互动，这只是（我希望）我们在不久的将来会有的去中心化互联网的无限例子之一。

如果你在这里，我相信你和我一样喜欢编程，所以我强烈建议你查看代码，如果你敢于贡献，并从中创造出更大更好的东西。非常感谢你的阅读!

> 注意：你可以从[Github资源库](https://github.com/MCarlomagno/persssist)中获取代码（并点亮星星😉）。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
