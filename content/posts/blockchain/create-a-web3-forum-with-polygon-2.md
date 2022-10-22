---
title: 使用 Polygon 创建 Web3 论坛 (下篇)
description: null
author: 李留白
weight: 0
date: 2022-10-22T14:27:47.759Z
lastmod: 2022-10-22T15:41:46.288Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210212124018.png
---

上篇传送门：https://mp.weixin.qq.com/s/a3zZ3UhRyEruwuXyojzhtA

## 用户界面

如果你看一下`pages/index.tsx`，你会发现启动代码带有一些UI模板。

### 配置

以下是已经为我们完成的工作的分类。

> 1. 初始化了一个nextjs应用程序
> 2. 添加了带有一些基本主题的chakra-ui
> 3. 添加了带有一些基本配置的react-query
> 4. 添加了react-hot-toast以帮助向用户显示toasts

现在让我们开始运行应用程序服务器。

```bash
npm run dev
```

一旦运行，在浏览器中打开http://localhost:3000，你应该看到 "哎呀，还没有评论！"因为......我们就要做这部分了。

我们将使用一个叫做wagmi的库来帮助我们管理钱包的登录。我们将使用几个wagmi钩子，包括`useConnect`、`useAccount`和`useContract`，它们都将帮助我们与钱包互动。在本教程中，我们将通过MetaMask连接，但wagmi支持其他几种连接方式。

让我们更新`index.tsx`并将我们的应用程序包在一个`WagmiProvider`中。

pages/index.tsx

```tsx
import type { NextPage } from "next";
import * as React from "react";
import { QueryClient, QueryClientProvider, QueryCache } from "react-query";
import { ChakraProvider, Box, Heading } from "@chakra-ui/react";
import { Toaster, toast } from "react-hot-toast";
import theme from "../theme";
import { Provider as WagmiProvider } from "wagmi";
import { providers } from "ethers";

// Provider that will be used when no wallet is connected (aka no signer)
const provider = providers.getDefaultProvider("http://localhost:8545");

// Create a react-query client
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      refetchOnWindowFocus: false,
    },
  },
  queryCache: new QueryCache({
    onError: () => {
      toast.error(
        "Network Error: Ensure MetaMask is connected to the same network that your contract is deployed to."
      );
    },
  }),
});

const App: NextPage = () => {
  return (
    <WagmiProvider autoConnect provider={provider}>
      <ChakraProvider theme={theme}>
        <QueryClientProvider client={queryClient}>
          <Box p={8} maxW="600px" minW="320px" m="0 auto">
            <Heading>Oops, no comments yet!</Heading>
            <Toaster position="bottom-right" />
          </Box>
        </QueryClientProvider>
      </ChakraProvider>
    </WagmiProvider>
  );
};

export default App;
```

现在我们已经配置了wagmi，我们已经准备好开始使用它的hooks!

### 消费合约

让我们创建一个钩子，`useCommentsContract`，来与我们部署的合约进行交互。

在`hooks`文件夹中创建一个新的文件`useCommentsContract.ts`，代码如下（这个文件中做了很多事情，所以请看内联的注释来了解细节）。

hooks/useCommentsContract.ts

```ts
import * as wagmi from "wagmi";
import { useProvider, useSigner } from "wagmi";
import type { BigNumber } from "ethers";
// Import our contract ABI (a json representation of our contract's public interface).
// The hardhat compiler writes this file to artifacts during compilation.
import CommentsContract from "../artifacts/contracts/Comments.sol/Comments.json";

export interface Comment {
  id: string;
  topic: string;
  message: string;
  creator_address: string;
  created_at: BigNumber;
}

export enum EventType {
  CommentAdded = "CommentAdded",
}

const useCommentsContract = () => {
  // An ethers.Signer instance associated with the signed-in wallet.
  // https://docs.ethers.io/v5/api/signer/
  const [signer] = useSigner();
  // An ethers.Provider instance. This will be the same provider that is  
  // passed as a prop to the WagmiProvider.
  const provider = useProvider();

  // This returns a new ethers.Contract ready to interact with our comments API.
  // We need to pass in the address of our deployed contract as well as its abi.
  // We also pass in the signer if there is a signed in wallet, or if there's
  // no signed in wallet then we'll pass in the connected provider.
  const contract = wagmi.useContract({
    addressOrName: "0x5FbDB2315678afecb367f032d93F642f64180aa3",
    contractInterface: CommentsContract.abi,
    signerOrProvider: signer.data || provider,
  });

  // Wrapper to add types to our getComments function.
  const getComments = async (topic: string): Promise<Comment[]> => {
    return contract.getComments(topic).then((comments) => {
      // Each comment is represented as array by default so we convert to object
      return comments.map((c) => ({ ...c }));
    });
  };

  // Wrapper to add types to our addComment function.
  const addComment = async (topic: string, message: string): Promise<void> => {
    // Create a new transaction
    const tx = await contract.addComment(topic, message);
    // Wait for transaction to be mined
    await tx.wait();
  };

  return {
    contract,
    chainId: contract.provider.network?.chainId,
    getComments,
    addComment,
  };
};

export default useCommentsContract;
```

这个钩子将允许我们从用户界面与我们的合约功能进行交互，并且每当活跃的签名者/提供者通过wagmi/MetaMask发生变化时，将返回我们一个新的合约实例。

> 请注意，wagmi是一个围绕另一个叫做[ethers](https://docs.ethers.io/v5/)的封装库。Ethers允许开发者与EVM兼容的区块链（即：Ethereum和Polygon）互动。

现在让我们创建另一个钩子`useComments`，它将负责在我们`getComments`函数的promise生命周期内管理react状态。

为了做到这一点，我们将使用 [react-query](https://react-query.tanstack.com/overview)，它是一个强大的异步状态管理库，代表我们获取数据，并为我们提供有用的状态助手，如 `isLoading`、`isSuccess` 和 `isError`。

hooks/useComments.ts

```ts
import { useQuery } from "react-query";
import useCommentsContract from "./useCommentsContract";

interface UseCommentsQuery {
  topic: string;
}

const useComments = ({ topic }: UseCommentsQuery) => {
  const contract = useCommentsContract();
  return useQuery(["comments", { topic, chainId: contract.chainId }], () =>
    contract.getComments(topic)
  );
};

export default useComments;
```

这个钩子将在渲染时自动获取给定主题的评论，并且在主题或chainId改变时重新获取评论。

传递给`useQuery`的第一个参数，`["comments", { topic, chainId: contract.chainId }]`，被称为[查询键](https://react-query.tanstack.com/guides/query-keys)。查询键被 react-query 序列化，用于维护获取的数据的全局缓存。

> 关于查询键的更多信息
>
> 假设我们有一个带有`/todos`和`/todos/:id`页面的todo列表应用。
>
> ```ts
> const TodoListPage = () => {
>   const query = useQuery(['todos'], () => api.getTodos())
>   
>   return <Box>...</Box>
> }
> 
> const TodoDetailPage = () => {
>   const router = useRouter()
>   const query = useQuery([
>       'todos', 
>       router.query.id
>     ], 
>     () => api.getTodo(router.query.id)
>   )
>   
>   return <Box>...</Box>
> }
> ```
>
> 如果用户访问`/todos/33`，然后导航到`/todos`，然后导航回`/todos/33`，第二次访问todo 33时，react-query就不必从api中获取todo，因为它已经将结果存储在查询键`['todos', 33]`下。
>
> react-query还提供了一种机制来使查询键失效，从而告诉查询缓存，一个缓存的值已经过期，需要重新获取。
>
> ```ts
> const AddTodoPage = () => {
>   const queryClient = useQueryClient()
>   const mutation = useMutation(api.addTodo, { 
>      onSuccess: () => {
>        queryClient.invalidateQueries(['todos'])
>      }
>   })
>   
>   return <Box>...</Box>
> }
> ```
>
> 像这样的查询无效是一种常见的模式，即一个新的todo被添加，然后todos列表的查询被无效，这反过来导致react-query自动重新获取todos列表。在重新获取后，任何使用`useQuery(['todos'], () => api.getTodos())`的组件都将以新的todo列表重新渲染。

我们终于准备好与我们部署的合约进行交互了

在`components`文件夹中创建一个文件`Comments.tsx`，代码如下：

```tsx
import * as React from "react";
import { Box } from "@chakra-ui/react";
import useComments from "../hooks/useComments";

interface CommentsProps {
  topic: string;
}

const Comments: React.FunctionComponent<CommentsProps> = ({ topic }) => {
  const query = useComments({ topic });

  return <Box as="pre">{JSON.stringify(query.data, null, 2)}</Box>;
};

export default Comments;
```

然后更新`index.tsx`来呈现我们的`Comments`组件，以取代 "哎呀，还没有评论！"的消息。

```tsx
import type { NextPage } from "next";
import * as React from "react";
import { QueryClient, QueryClientProvider, QueryCache } from "react-query";
import { ChakraProvider, Box, Heading } from "@chakra-ui/react";
import { Toaster, toast } from "react-hot-toast";
import theme from "../theme";
import { Provider as WagmiProvider } from "wagmi";
import { providers } from "ethers";
import Comments from "../components/Comments";

// Provider that will be used when no wallet is connected (aka no signer)
const provider = providers.getDefaultProvider("http://localhost:8545");

// Create a react-query client
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      refetchOnWindowFocus: false,
    },
  },
  queryCache: new QueryCache({
    onError: () => {
      toast.error(
        "Network Error: Ensure MetaMask is connected to the same network that your contract is deployed to."
      );
    },
  }),
});

const App: NextPage = () => {
  return (
    <WagmiProvider autoConnect provider={provider}>
      <ChakraProvider theme={theme}>
        <QueryClientProvider client={queryClient}>
          <Box p={8} maxW="600px" minW="320px" m="0 auto">
            <Comments topic="my-blog-post" />
            <Toaster position="bottom-right" />
          </Box>
        </QueryClientProvider>
      </ChakraProvider>
    </WagmiProvider>
  );
};

export default App;
```

现在到http://localhost:3000，你应该看到一个大的漂亮的json blob呈现在你的用户界面上。如果你想知道这些数据是从哪里来的，我们在运行我们的deploy-and-seed脚本时填充了它。

> 没有看到任何json？
>
> 如果你只是看到一个没有json的黑屏，或者你看到一个错误的toast发出，一定要去你的MetaMask扩展，选择`localhost 8545`作为当前网络。
>
> 如果你在网络列表中没有看到`localhost 8545`，那么在MetaMask中导航到设置->高级，确保 "显示测试网络 "被启用。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210222259429.png)

### 渲染评论

好了，感觉我们终于取得了进展。现在是时候正确地渲染这些取来的评论了。

我们使用[chakra-ui](https://chakra-ui.com/)作为一个组件库，你将会看到它给了我们一些相当优雅的JSX。

让我们更新`Comments.tsx`以反映以下内容。

```tsx
import * as React from "react";
import { Box, Spinner, Stack, Center } from "@chakra-ui/react";
import useComments from "../hooks/useComments";

interface CommentsProps {
  topic: string;
}

const Comments: React.FunctionComponent<CommentsProps> = ({ topic }) => {
  const query = useComments({ topic });

  return (
    <Box>
      {query.isLoading && (
        <Center p={8}>
          <Spinner />
        </Center>
      )}
      <Stack spacing={4}>
        {query.data?.map((comment) => (
          <Box key={comment.id} bg="whiteAlpha.100" rounded="2xl" p={3}>
            {comment.message}
          </Box>
        ))}
      </Stack>
    </Box>
  );
};

export default Comments;
```

这绝对是一个进步！现在我们显示了一个加载的loading，同时也显示了一个新的JSP。现在我们在加载评论时显示一个加载旋钮，当数据可用时，我们对评论列表进行映射，为每个评论呈现一个简单的评论卡。

我们导入了几个chakra-ui组件，下面是每个组件的小分类。

- `Box` - 一个简单的类似于Div的构建块

- `Stack` - 一个垂直的元素列表，每个元素之间有空间
- `Center` - 一个`Box`，默认情况下它的子元素是居中的。
- `Spinner` - 一个加载的旋转器，其大小和颜色可以通过props来配置。

你还会注意到我们给这些chakra组件传递了一些简略的props（例如：p代表padding，m代表margin，bg代表background）。大多数chakra-ui组件都支持一套基本的类似样式的道具，以帮助你轻松地添加样式。你可以在这里看到[样式属性的完整列表](https://chakra-ui.com/docs/features/style-props)。

好的，让我们创建一个专门的`Comment`组件，负责渲染每个单独的评论。在`component`文件夹下创建一个`Comment.tsx`文件，并像下面这样实现一个组件。

```tsx
import * as React from "react";
import { Text, Heading, HStack, Stack } from "@chakra-ui/react";
import TimeAgo from "react-timeago";
import Avatar from "@davatar/react";
import { Comment } from "../hooks/useCommentsContract";

interface CommentProps {
  comment: Comment;
}

const Comment: React.FunctionComponent<CommentProps> = ({ comment }) => {
  return (
    <HStack spacing={3} alignItems="start">
      <Avatar size={48} address={comment.creator_address} />
      <Stack spacing={1} flex={1} bg="whiteAlpha.100" rounded="2xl" p={3}>
        <Heading color="whiteAlpha.900" fontSize="lg">
          {comment.creator_address}
        </Heading>
        <Text color="whiteAlpha.800" fontSize="lg">
          {comment.message}
        </Text>
        <Text color="whiteAlpha.500" fontSize="md">
          <TimeAgo date={comment.created_at.toNumber() * 1000} />
        </Text>
      </Stack>
    </HStack>
  );
};

export default Comment;
```

在这个组件中，我们使用了`@davatar/react`来帮助我们为每个钱包地址渲染一个独特的头像。我们还使用了`react-timeago`来呈现评论创建后的持续时间。最后，我们显示了`creator_address`和`message`，没有进行额外的处理。

让我们重新配置`Comments.tsx`来渲染我们的`Comments`组件。

```tsx
import * as React from "react";
import { Box, Spinner, Stack, Center } from "@chakra-ui/react";
import useComments from "../hooks/useComments";
import Comment from "./Comment";

interface CommentsProps {
  topic: string;
}

const Comments: React.FunctionComponent<CommentsProps> = ({ topic }) => {
  const query = useComments({ topic });

  return (
    <Box>
      {query.isLoading && (
        <Center p={8}>
          <Spinner />
        </Center>
      )}
      <Stack spacing={4}>
        {query.data?.map((comment) => (
          <Comment key={comment.id} comment={comment} />
        ))}
      </Stack>
    </Box>
  );
};

export default Comments;
```

在这一点上，我们应该看到我们的应用程序看起来像一个真正的评论线程。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210222308935.png)

###  添加新的评论

现在我们已经有了很好的评论渲染，让我们谈谈通过用户界面添加新的评论。

我们将回到 react-query，这次我们将通过 useMutation 钩子创建一个mutation。

在`hooks`文件夹下，创建一个新的文件`useAddComment.ts`，实现如下。

```ts
import { useMutation } from "react-query";
import useCommentsContract from "./useCommentsContract";

interface UseAddCommentPayload {
  topic: string;
  message: string;
}

const useAddComment = () => {
  const contract = useCommentsContract();

  return useMutation(async ({ topic, message }: UseAddCommentPayload) => {
    await contract.addComment(topic, message);
  });
};

export default useAddComment;
```

正如你所看到的，这与我们之前写的`useComments`钩子非常相似，只是现在我们要返回一个mutation而不是一个查询。

mutation是为改变数据的异步行动准备的，比如插入、更新和删除，而查询是为只读的异步行动准备的。

现在在`components`文件夹下我们将创建`CommentEditor.tsx`来管理我们添加新评论的逻辑。

```tsx
import * as React from "react";
import { Button, HStack, Stack, Textarea } from "@chakra-ui/react";
import { constants } from "ethers";
import Avatar from "@davatar/react";
import { useAccount } from "wagmi";
import useAddComment from "../hooks/useAddComment";

interface CommentEditorProps {
  topic: string;
}

const CommentEditor: React.FunctionComponent<CommentEditorProps> = ({
  topic,
}) => {
  const [message, setMessage] = React.useState("");
  const mutation = useAddComment();
  const [accountQuery] = useAccount();

  return (
    <Stack spacing={3}>
      <HStack spacing={3} alignItems="start">
        <Avatar
          size={48}
          address={accountQuery.data?.address || constants.AddressZero}
        />
        <Textarea
          value={message}
          onChange={(e) => setMessage(e.target.value)}
          placeholder="Write a message.."
          p={3}
          flex={1}
          bg="whiteAlpha.100"
          rounded="2xl"
          fontSize="lg"
        />
      </HStack>
      <Button
        size="sm"
        colorScheme="pink"
        alignSelf="flex-end"
        onClick={() => {
          mutation
            .mutateAsync({
              message,
              topic,
            })
            .then(() => setMessage(""));
        }}
        isLoading={mutation.isLoading}
      >
        Submit
      </Button>
    </Stack>
  );
};

export default CommentEditor;
```

在这里，我们在左边显示一个与已登录用户相对应的头像（如果没有用户登录，则显示空地址）。然后在右边我们显示一个文本区域和一个提交按钮，允许用户写/提交评论。当提交按钮被点击时，我们在mutation上调用`mutateAsync`，这实际上启动了对区块链的调用。

让我们配置我们的`Components`组件来渲染`ComponentsEditor`，这样我们就可以测试它了。

```tsx
import * as React from "react";
import { Box, Spinner, Stack, Center } from "@chakra-ui/react";
import useComments from "../hooks/useComments";
import Comment from "./Comment";
import CommentEditor from "./CommentEditor";

interface CommentsProps {
  topic: string;
}

const Comments: React.FunctionComponent<CommentsProps> = ({ topic }) => {
  const query = useComments({ topic });

  return (
    <Box>
      {query.isLoading && (
        <Center p={8}>
          <Spinner />
        </Center>
      )}
      <Stack spacing={4}>
        {query.data?.map((comment) => (
          <Comment key={comment.id} comment={comment} />
        ))}
        {query.isFetched && <CommentEditor topic={topic} />}
      </Stack>
    </Box>
  );
};

export default Comments;
```

好了，现在从浏览器让我们试着提交一个评论，看看会发生什么。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210222310656.png)

哎呀，事实证明，我们不能只是匿名提交评论，因为，我们不能匿名发送区块链交易。我们需要在一个钱包中签名，以便发送区块链交易。

> 没有看到这个错误吗？
>
> 如果你没有遇到这个错误，你很可能已经有一个连接到localhost:3000的钱包，来自你以前工作的项目。在MetaMask中，尝试断开你的钱包与localhost的连接，然后你应该看到像上面那样的错误。

所以让我们在继续之前让用户登录。让我们在`components`文件夹下做一个自定义按钮，我们称之为`AuthButton.tsx`。

如果用户没有连接钱包，`AuthButton`将显示 "Sign In"，点击后将提示用户通过MetaMask登录。否则，如果用户已经连接了一个钱包，`AuthButton`将表现得像一个正常的按钮。

```tsx
import * as React from "react";
import { Button, ButtonProps } from "@chakra-ui/react";
import { useAccount, useConnect } from "wagmi";
import toast from "react-hot-toast";

interface AuthButtonProps extends ButtonProps {}

const AuthButton: React.FunctionComponent<AuthButtonProps> = (props) => {
  const [connectQuery, connect] = useConnect();
  const [accountQuery] = useAccount();

  React.useEffect(() => {
    if (connectQuery.error?.name === "ConnectorNotFoundError") {
      toast.error("MetaMask extension required to sign in");
    }
  }, [connectQuery.error]);

  // If not authenticated, require sign-in
  if (!accountQuery.data?.address) {
    return (
      <Button
        {...props}
        onClick={() => {
          connect(connectQuery.data.connectors[0]);
        }}
      >
        Sign In
      </Button>
    );
  }

  // If authenticated, show button as usual
  return <Button {...props}>{props.children}</Button>;
};

export default AuthButton;
```

这里我们调用wagmi提供的`useAccount`钩子，看看是否有一个已签名的钱包。我们也使用wagmi的`useConnect`钩子，并调用`connect`，传递MetaMask连接器（`InjectedConnector`），提示用户用MetaMask登录。

好了，现在回到 `components/CommentEditor.tsx `中，我们要做的就是把我们的 `Button `改成 `AuthButton`，我们就可以开始了。

```tsx
import * as React from "react";
import { HStack, Stack, Textarea } from "@chakra-ui/react";
import { constants } from "ethers";
import Avatar from "@davatar/react";
import AuthButton from "./AuthButton";
import { useAccount } from "wagmi";
import useAddComment from "../hooks/useAddComment";

interface CommentEditorProps {
  topic: string;
}

const CommentEditor: React.FunctionComponent<CommentEditorProps> = ({
  topic,
}) => {
  const [message, setMessage] = React.useState("");
  const mutation = useAddComment();
  const [accountQuery] = useAccount();

  return (
    <Stack spacing={3}>
      <HStack spacing={3} alignItems="start">
        <Avatar
          size={48}
          address={accountQuery.data?.address || constants.AddressZero}
        />
        <Textarea
          value={message}
          onChange={(e) => setMessage(e.target.value)}
          placeholder="Write a message.."
          p={3}
          flex={1}
          bg="whiteAlpha.100"
          rounded="2xl"
          fontSize="lg"
        />
      </HStack>
      <AuthButton
        size="sm"
        colorScheme="pink"
        alignSelf="flex-end"
        onClick={() => {
          mutation
            .mutateAsync({
              message,
              topic,
            })
            .then(() => setMessage(""));
        }}
        isLoading={mutation.isLoading}
      >
        Submit
      </AuthButton>
    </Stack>
  );
};

export default CommentEditor;
```

现在让我们用hardhat在运行`npx hardhat node`时为我们生成的一个账户登录。一旦我们登录了，我们就可以试着发表评论了!

下面是一个gif图，显示了在MetaMask上添加一个账户并发表评论的过程。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210222313747.gif)

注意，在提交评论后，mutation成功了，但是我们必须刷新页面，才能看到新的评论。这就是我们接下来要解决的问题。

### 监听事件

我们将通过监听由我们的合约发出的`CommentAdded`事件来实现实时评论更新。这不仅对我们提交的评论有效，而且当其他人从遥远的地方提交评论时也会刷新用户界面。

在`hooks`文件夹下，我们将添加一个新文件`useEvents.ts`。

```ts
import { useEffect } from "react";
import { useQueryClient } from "react-query";
import useCommentsContract, { EventType } from "./useCommentsContract";

interface UseEventsQuery {
  topic: string;
}

// Listen to events and refresh data
const useEvents = ({ topic }: UseEventsQuery) => {
  const queryClient = useQueryClient();
  const commentsContract = useCommentsContract();

  useEffect(() => {
    const handler = (comment) => {
      if (comment.topic !== topic) {
        return;
      }
      // Invalidates the query whose query key matches the passed array.
      // This will cause the useComments hook to re-render the Comments
      // component with fresh data.
      queryClient.invalidateQueries([
        "comments",
        { topic: comment.topic, chainId: commentsContract.chainId },
      ]);
    };

    commentsContract.contract.on(EventType.CommentAdded, handler);

    return () => {
      commentsContract.contract.off(EventType.CommentAdded, handler);
    };
  }, [queryClient, commentsContract.chainId, topic]);
};

export default useEvents;
```

这个钩子订阅了合约的 `CommentsAdded `事件，并告诉 react-query 把我们的` useComments` 查询标记为过期，导致它重新获取评论列表。

这里的另一种方法是把事件传递给我们的评论，插入到存储在 react-query 查询缓存中的评论数组。这将避免重新获取评论列表的需要，并将以更及时的方式更新列表。

好的，现在让我们从`Comments`组件中消费我们的`useEvents`。

```tsx
import * as React from "react";
import { Box, Spinner, Stack, Center } from "@chakra-ui/react";
import Comment from "./Comment";
import CommentEditor from "./CommentEditor";
import useComments from "../hooks/useComments";
import useEvents from "../hooks/useEvents";

interface CommentsProps {
  topic: string;
}

const Comments: React.FunctionComponent<CommentsProps> = ({ topic }) => {
  const query = useComments({ topic });

  useEvents({ topic });

  return (
    <Box>
      {query.isLoading && (
        <Center p={8}>
          <Spinner />
        </Center>
      )}
      <Stack spacing={4}>
        {query.data?.map((comment) => (
          <Comment key={comment.id} comment={comment} />
        ))}
        {query.isFetched && <CommentEditor topic={topic} />}
      </Stack>
    </Box>
  );
};

export default Comments;
```

现在我们提交一个评论后，我们看到它被自动添加到列表中。

### 最后的修饰

好了，我们的用户界面看起来很好，但让我们再调整一下。评论创建者这一行看起来相当长，而且不是超级有用。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210222314701.png)

所以我们要做一个`Username`组件，如果一个地址存在的话，它可以查询并显示其ENS名称，否则它将显示一个截断的钱包地址。

> ENS是什么？
>
> ENS是Ethereum Name Service的缩写，它允许用户注册一个人类可读的名字，以解析其钱包地址。
>
> 例如，在以太坊主网上pointergg.eth解析为0x3A5D69BafA68353d130afa251Ee68a41eEa7aed9。
>
> 如果你在社交媒体上看到以.eth结尾的用户名，那些很可能是ENS的名字，可以解析到所有者的钱包地址。

让我们添加一个组件` Username.tsx `并用以下内容填充它。

```tsx
import * as React from "react";
import { Text, TextProps } from "@chakra-ui/react";
import { useEnsLookup } from "wagmi";
import truncateMiddle from "truncate-middle";

interface UsernameProps extends TextProps {
  address: string;
}

const Username: React.FunctionComponent<UsernameProps> = ({
  address,
  ...otherProps
}) => {
  const [query] = useEnsLookup({ address });

  // Show ens name if exists, but show truncated address as fallback
  return (
    <Text
      display="inline"
      textTransform={query.data ? "none" : "uppercase"}
      {...otherProps}
    >
      {query.data || truncateMiddle(address || "", 5, 4, "...")}
    </Text>
  );
};

export default Username;

```

这段代码再次利用了一个wagmi钩子，`useEnsLookup`，并显示解决的ENS名称，如果它存在。否则该组件只显示地址，只是现在我们截断了地址字符串，强迫它变成 `"0X123...4567 "`的格式。

现在我们可以从`Comment.tsx`中导入`Username`并消费它。

```tsx
import * as React from "react";
import { Text, Heading, HStack, Stack } from "@chakra-ui/react";
import TimeAgo from "react-timeago";
import Avatar from "@davatar/react";
import Username from "./Username";
import { Comment } from "../hooks/useCommentsContract";

interface CommentProps {
  comment: Comment;
}

const Comment: React.FunctionComponent<CommentProps> = ({ comment }) => {
  return (
    <HStack spacing={3} alignItems="start">
      <Avatar size={48} address={comment.creator_address} />
      <Stack spacing={1} flex={1} bg="whiteAlpha.100" rounded="2xl" p={3}>
        <Heading color="whiteAlpha.900" fontSize="lg">
          <Username address={comment.creator_address} />
        </Heading>
        <Text color="whiteAlpha.800" fontSize="lg">
          {comment.message}
        </Text>
        <Text color="whiteAlpha.500" fontSize="md">
          <TimeAgo date={comment.created_at.toNumber() * 1000} />
        </Text>
      </Stack>
    </HStack>
  );
};

export default Comment;
```

## **部署到Polygon**


好了，后端完成了 ✅ 前端完成了 ✅。现在让我们把我们的合约部署到一个实际的区块链上。

如前所述，我们将使用Polygon，因为它比Ethereum有许多优势，包括更快的交易处理和更低的交易成本。

如果你在MetaMask中还没有把Polygon Mumbai作为一个网络选项，那么请点击下面的按钮来添加它。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210222318027.png)

让我们更新`hardhat.config.js`来初始化dotenv包，让我们把Polygon Mumbai添加到hardhat的支持网络中。

```js
require("@nomiclabs/hardhat-waffle");
require("dotenv").config();

// This is a sample Hardhat task. To learn how to create your own go to
// https://hardhat.org/guides/create-task.html
task("accounts", "Prints the list of accounts", async (taskArgs, hre) => {
  const accounts = await hre.ethers.getSigners();

  for (const account of accounts) {
    console.log(account.address);
  }
});

// You need to export an object to set up your config
// Go to https://hardhat.org/config/ to learn more

/**
 * @type import('hardhat/config').HardhatUserConfig
 */
module.exports = {
  solidity: "0.8.4",
  networks: {
    hardhat: {
      chainId: 1337,
    },
    matic: {
      url: "https://rpc-mumbai.maticvigil.com",
      accounts: [process.env.PRIVATE_KEY],
    },
  },
};
```

dotenv包将从一个名为`.env`的文件中获取键值对，并将它们作为常量添加到内存中的`process.env`下。这就是我们如何填充 `process.env.PRIVATE_KEY` 这个值。

进入MetaMask扩展，选择一个你想用来部署合约的账户。通过MetaMask用户界面找到该账户的[私钥](https://metamask.zendesk.com/hc/en-us/articles/360015289632-How-to-Export-an-Account-Private-Key)并复制它。

> 警告--无论何时在代码中直接使用钱包的私钥，我们强烈建议使用一个几乎不包含真实货币的账户。

让我们在Repo的根目录下创建一个`.env`文件，并将你从MetaMask复制的私钥填入其中。

```bash
PRIVATE_KEY=<your-private-key>
```

如果你使用的钱包在Polygon Mumbai网络上没有任何MATIC，那么你就需要从[水龙头](https://faucet.polygon.technology/)那里申请一些。

完成这些后，我们就可以把我们的合约部署到Polygon测试网了。

让我们再次运行我们的部署脚本，但这次将网络设置为matic。

```bash
npx hardhat run --network matic scripts/deploy-and-seed.js
```

如果成功的话，你应该看到进程打印出一个格式如下的信息。

````bash
Contract deployed to: <address-of-deployed-contract>
````

祝贺你，合约已经部署完毕 🎉🎉🎉

让我们把我们的用户界面连接到这个合约实例。

首先，我们需要用新的合约地址更新`useCommentsContract.ts`中的`addressOrName`字段。

```ts
  const contract = wagmi.useContract({
    addressOrName: "<address-of-deployed-contract>",
    contractInterface: CommentsContract.abi,
    signerOrProvider: signer.data || provider,
  });
```

然后在`index.tsx`中，我们将更新提供者的定义，以回落到Polygon Mumbai网络中。

```tsx
// Provider that will be used when no wallet is connected (aka no signer)
const provider = providers.getDefaultProvider(
  "https://rpc-mumbai.maticvigil.com"
);
```

在这一点上，让我们确保我们连接到MetaMask中的Polygon Mumbai网络，然后刷新用户界面。现在，我们的`Comments`组件应该全部连接到我们部署的Polygon合约上了!

## 总结

我们讲了很多! 我们希望这篇文章向你介绍了一些新的模式、库和思考web3技术的方法。

我们非常感谢[Polygon](https://polygon.technology/)为我们打开的大门。如果不是Polygon为我们提供的低交易费用和快速的交易速度，我们roadmap上的许多想法都不可能实现。

> 原文：https://www.pointer.gg/tutorials/create-a-web3-forum-with-polygon
> 翻译：李留白

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
