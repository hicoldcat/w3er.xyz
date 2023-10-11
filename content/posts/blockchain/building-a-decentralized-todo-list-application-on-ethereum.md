---
title: 在以太坊上构建去中心化的待办事项列表应用程序
description: null
author: 李留白
weight: 0
date: 2023-10-11T13:45:34.090Z
lastmod: 2023-10-11T13:55:47.568Z
tags: []
categories:
    - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231011214742.png
---

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231011214742.png)

欢迎来到以太坊区块链上令人兴奋的去中心化应用程序 (dApp) 世界！在本分步指南中，我们将逐步介绍使用 Hardhat 开发框架创建去中心化待办事项列表应用程序的过程。

我们将涵盖有趣的主题，例如设置开发环境、编写 Solidity 智能合约、测试它以及将其部署到 Sepolia 测试网。一起编写代码以便更好地理解！

## 先决条件

在我们深入研究之前，请确保您拥有以下工具和先决条件：

- **Node**
- **Hardhat：**与区块链交互的 JavaScript 框架。
- **Metamask**：安装 Metamaks 并获取您的私钥。配置 Metamask 以连接到 Sepolia 网络。
- **Alchemy**：获取 Sepolia 测试网的 Alchemy HTTP 端点。这是有关如何设置的[指南。](https://docs.alchemy.com/docs/how-to-add-alchemy-rpc-endpoint-for-local-development#step-2-add-http-url-to-local-project)
- **Test Sepolia ETH：从**[水龙头](https://sepoliafaucet.com/)请求一些Sepolia ETH 。

## 设置我们的环境

现在我们已经收集了我们的工具，是时候设置我们的开发环境了。

这是分步指南：

- 为您的应用程序 todolist 创建一个新的项目目录。

```powershell
mkdir todolist
cd todolist
npm init -y
npm install --save-dev hardhat
```

- 通过运行以下命令来初始化您的 Hardhat 项目：

```powershell
npx hardhat init
```

选择创建 JavaScript 项目的选项，并接受默认选项。Hardhat 将生成一个示例项目并为您安装必要的依赖项。

- 在您喜欢的代码编辑器中打开您的项目文件夹。如果您使用 Visual Studio Code，只需运行：

```powershell
code .
```

- 为了确保 Metamask 私钥和 Alchemy RPC URL 等敏感信息的安全，请在项目目录中创建一个 .env 文件，并按以下格式存储密钥：

  ![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231011215251.png)

- 安装该`dotenv`软件包，这将帮助我们使用环境变量：

```powershell
npm i dotenv
```

- 修改 Hardhat 配置文件（通常名为 Hardhat.config.js）以识别文件中的密钥`.env`：

```javascript
require("@nomicfoundation/hardhat-toolbox");
require("dotenv").config();

module.exports = {
  solidity: "0.8.19",

  networks: {
    sepolia: {
      url: process.env.ALCHEMY_API_KEY_URL,
      accounts: [process.env.PRIVATE_KEY],
    },
  },
};
```

您的环境现在已准备好在以太坊区块链上施展魔法！

### 建立我们的合同

让我们通过编写 Solidity 智能合约来深入研究待办事项列表应用程序的核心。在合同文件夹中，您将找到默认的“Lock.sol”文件。首先在“contracts”文件夹中找到“Lock.sol”文件，并将其重命名为“TodoList.sol”以与我们的合约名称保持一致。

下面是“TodoList”合约，以及解释每个代码块的作用的注释：

```solidity
// SPDX-License-Identifier: MIT

// Solidity Version
pragma solidity 0.8.19;

contract TodoList {
    // Struct to represent a task
    struct Task {
        uint id; // Unique task identifier
        uint date; // Timestamp of task creation
        string name; // Task name
        string description; // Task description
        bool isCompleted; // Flag indicating task completion status
        address owner; // Address of the task owner
    }

    // Array to store all tasks
    Task[] public tasks;

    // Mapping to associate user addresses with their task IDs
    mapping(address => uint[]) private userTasks;

    // Constructor function
    constructor() {}

    // Event emitted when a task is created
    event TaskCreated(
        uint id,
        uint date,
        string name,
        string description,
        bool isCompleted,
        address owner
    );

    // Event emitted when a task is marked as completed
    event TaskCompleted(uint id, address owner);

    // Event emitted when a task is deleted
    event TaskDeleted(uint id, address owner);

    // Function to create a new task
    function createTask(string memory name, string memory description) public {
        uint taskId = tasks.length; // Calculate the new task ID
        tasks.push(
            Task(taskId, block.timestamp, name, description, false, msg.sender)
        ); // Create and add the new task to the array
        userTasks[msg.sender].push(taskId); // Update the userTasks mapping
        emit TaskCreated(
            taskId,
            block.timestamp,
            name,
            description,
            false,
            msg.sender
        ); // Emit a TaskCreated event
    }

    // Function to retrieve task details by ID
    function getTask(
        uint id
    )
        public
        view
        returns (uint, uint, string memory, string memory, bool, address)
    {
        // Ensure the task ID is valid
        require(id < tasks.length, "Task ID does not exist"); 
        Task storage task = tasks[id]; // Retrieve the task from storage
        return (
            task.id,
            task.date,
            task.name,
            task.description,
            task.isCompleted,
            task.owner
        ); // Return task details
    }

    // Function to mark a task as completed
    function markTaskCompleted(uint id) public {
        // Ensure the task ID is valid
        require(id < tasks.length, "Task ID does not exist"); 
        Task storage task = tasks[id]; // Retrieve the task from storage
        require(
            task.owner == msg.sender,
            "Only the owner can complete the task"
        );
        // Ensure the task is not already completed
        require(!task.isCompleted, "Task is already completed"); 
        task.isCompleted = true; // Mark the task as completed
        emit TaskCompleted(id, msg.sender); // Emit a TaskCompleted event
    }

    // Function to delete a task
    function deleteTask(uint id) public {
        // Ensure the task ID is valid
        require(id < tasks.length, "Task ID does not exist"); 
        Task storage task = tasks[id]; // Retrieve the task from storage
        // Ensure only the owner can delete the task
        require(task.owner == msg.sender, "Only the owner can delete the task"); 
        emit TaskDeleted(id, msg.sender); // Emit a TaskDeleted event

        // Delete the task by replacing it with the last task in the array 
        // and reducing the array size
        uint lastIndex = tasks.length - 1;
        if (id != lastIndex) {
            Task storage lastTask = tasks[lastIndex];
            tasks[id] = lastTask;
            userTasks[msg.sender][id] = lastIndex;
        }
        tasks.pop();
        userTasks[msg.sender].pop();
    }

    // Function to retrieve all task IDs belonging to the caller
    function getUserTasks() public view returns (uint[] memory) {
        // Return the task IDs associated with the caller's address
        return userTasks[msg.sender]; 
    }
}
```

## 测试我们的合约

测试我们的合约对于保证其可靠性和功能性以及确保其按预期执行至关重要。在一个容易遭受黑客攻击和漏洞利用的行业中，编写测试非常有必要，以确保我们的合约不会容易受到漏洞利用。

### 用 Mocha 编写我们的测试

我们将使用 Mocha 进行测试，所以让我们设置我们的测试。在 test 文件夹中，将 Lock.js 文件重命名为 test.js 并将代码替换为以下内容：

```javascript
const { expect } = require("chai");
const { ethers } = require("hardhat");

describe("TodoList contract", function () {
  let TodoList;
  let todolist;
  let owner;

  before(async function () {
    [owner] = await ethers.getSigners();

    // Deploy the TodoList contract
    todolist = await ethers.deployContract("TodoList");
    // await TodoList.waitForDeployment();
  });

  it("should create a new task", async function () {
    const taskName = "Sample Task";
    const taskDescription = "This is a sample task description";

    // Create a new task
    await todolist.createTask(taskName, taskDescription);

    // Retrieve the task details
    const [id, date, name, description, isCompleted, taskOwner] =
      await todolist.getTask(0);

    expect(id).to.equal(0);
    expect(name).to.equal(taskName);
    expect(description).to.equal(taskDescription);
    expect(isCompleted).to.equal(false);
    expect(taskOwner).to.equal(owner.address);
  });

  it("should mark a task as completed", async function () {
    // Mark the task at index 0 as completed
    await todolist.markTaskCompleted(0);

    // Retrieve the task details
    const [, , , , isCompleted] = await todolist.getTask(0);

    expect(isCompleted).to.equal(true);
  });

  it("should delete a task", async function () {
    // Create a new task
    await todolist.createTask(
      "Task to be deleted",
      "This task will be deleted"
    );

    // Delete the task at index 1
    await todolist.deleteTask(1);

    // Attempt to retrieve the deleted task (should throw an error)
    let errorOccurred = false;
    try {
      await todolist.getTask(1);
    } catch (error) {
      errorOccurred = true;
    }

    expect(errorOccurred).to.equal(true);
  });

  it("should retrieve the user's tasks", async function () {
    // Create a new task
    await todolist.createTask("User's Task", "This is the user's task");

    // Retrieve the user's tasks
    const userTasks = await todolist.getUserTasks();

    // Expect that there is at least one task
    expect(userTasks.length).to.be.at.least(1);
  });
});
```

为了测试我们的合约，我们运行通用的：

```powershell
npx hardhat test
```

响应应如下所示：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231011215314.png)

## 部署我们的合约

现在，令人兴奋的部分 - 将我们的智能合约部署到 Sepolia 网络。我们将编写一个部署脚本来实现这一点。

### 编写我们的部署脚本

在脚本文件夹中，您将找到一个包含一些示例代码的deploy.js 文件。将 JavaScript 代码替换为以下内容：

```javascript
// Import the ethers library from the Hardhat framework
const { ethers } = require("hardhat");

// Define an asynchronous main function for contract deployment
async function main() {
  // Deploy the contract
  const TodoList = await ethers.deployContract("TodoList");

  // Log message to show deployment in progress
  console.log("Deploying contract.....");

  // Wait for the deployment of the contract to complete
  await TodoList.waitForDeployment();

  // Log the deployment target (contract address) to the console
  console.log(`TodoList deployed to ${TodoList.target}`);
}

// Execute the main function, and handle any errors that may occur
main().catch((error) => {
  console.error(error);
  process.exitCode = 1;
});
```

要将我们的合约部署到 Sepolia 网络，请使用以下命令：

```javascript
npx hardhat run scripts/deploy.js --network sepolia
```

> *注意：如果您打算将智能合约部署到不同的网络，您可以轻松替换* `sepolia` *为您选择的网络。*

当我们部署到测试网时，这应该需要几秒钟的时间。您将收到合同部署的确认信息以及合同地址。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231011215543.png)

现在您可以体验去中心化待办事项列表变为现实的兴奋！继续复制您的合约地址并验证其在[Sepolia Testnet Explorer](https://sepolia.etherscan.io/)上的存在，就像您在以太坊主网上所做的那样。超级有趣！

# 结论

您已成功构建第一个 dApp 并将其部署到以太坊区块链。作为下一步，我强烈推荐以下资源：

[Lumos Academy](https://academy.lumoslabs.co/)：Lumos Labs 的 Lumos Academy 是一个专门为正在学习 Web3 开发的（有抱负的）Web3 开发人员提供的平台

[以太坊开发教程](https://ethereum.org/en/developers/tutorials/)：这个精选的社区教程列表涵盖了广泛的以太坊开发主题

希望您喜欢这篇文章！如果您有任何问题或意见，请随时在下面留言或在[Twitter 上与我联系！](https://twitter.com/tosynthegeek)

> 原文：https://tosynthegeek.hashnode.dev/building-a-decentralized-todo-list-application-on-ethereum
