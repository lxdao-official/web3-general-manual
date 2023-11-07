# NFT 创作
在了解过 NFT 的一些基本知识后，那么站在 NFT 项目方视角，NFT 是如何被创造出来的呢，它们又是怎么运作的，本小节做一些简洁流程的讲解。复杂的大项目，会有更多的参与者和设计细节。

## 参与者
这里介绍 NFT 项目的 2 个重要参与角色。
- 创作者，是指创作需要代币化的资产或权益的人（可能是一个团队），比如数字艺术作品、摄影作品、头像等。
- 开发者：这里泛指为目项提供互联网技术的团队，包括技术、产品、设计等。
- 运营：这里指 NFT 项目方的运营人员。

## 创作流程
这里跳过了想法和立项阶段，以 PFPs 类型的 NFT 为例。
### 创作和上传存储
代币化的对象是第一步要考虑的。
1. 制作素材 <br>
创作者通过专业的原画或设计技能，将需要创作的作品数字化，形式是批量图片。
2. 上传存储 <br>
通过工具，创作者或开发者将批量图片上传到到网络存储空间，生成批量链接。
> 网络存储有中心化和去中心化 2 种方式。市面上有不少 NFT 项目是采用中心化存储（后端服务器、CDN 等）的方式，比较容易篡改，可信度不高。推荐使用去中心化存储（IPFS、Arweave），更加 Web3 Native 一些。

### 产品化和技术实现
吸引消费者来购买 NFT，产提供良好的用户体验，是产品化和技术实现的主旋律。 这个环节实际上也是 DApp 的设计和技术实现，在第4章有比较完整的的介绍，这里不做过多的讲解。

#### 1. 设计产品功能
- 一个最基础的 NFT 项目起码需要定量、定价、铸造<sup>[1]</sup>、提现等基本功能。
- 稍微复杂一些，可以设计轮次、白名单<sup>[2]</sup>、空投<sup>[3]</sup>等功能。
- 更复杂一些，可以设计盲盒<sup>[4]</sup>功能。

#### 2. 编写智能合约、部署、校验 
- 下面是一个简单的 NFT 合约，发行数量 100，定价 0.1 ETH，提供铸造和提现功能。
```solidity
contract Web3Newbie is ERC721Enumerable, Ownable {
    uint256 public constant price = 0.001 ether;
    uint256 public constant maxSupply = 100;
    string public baseURI;

    constructor(string memory _baseURI) ERC721("Web3Newbie", "WNT") {
        baseURI = _baseURI;
    }

    function mint() external payable {
        require(totalSupply() < maxSupply, "Mint exceed max supply");
        require(price <= msg.value, "Ether value sent is not correct");

        uint256 mintIndex = totalSupply();
        _safeMint(_msgSender(), mintIndex + 1);
    }

    function tokenURI(uint256 tokenId) public view override returns (string memory) {
        if (!_exists(tokenId)) {
            return "";
        }
        return string(abi.encodePacked(baseURI, Strings.toString(tokenId)));
    }

    function withdraw() public onlyOwner {
        uint256 balance = address(this).balance;
        payable(_msgSender()).transfer(balance);
    }
}
```
- `function mint`提供铸造功能，用户消耗 0.001 个 eth 可以 mint 出来 1个 NFT，总数量限制在 100 个以内；
- `function withdraw`提供提现功能，合约的拥有者（owner）可以提取用户铸造 NFT 时支付的代币。
- `function tokenURI`提供 NFT 的 URI。它是 NFT 本质和外在形式的关键连接点。如何理解？（这里需要一个流程图？）每个在智能合约中产生的 NFT，都有一个 id（tokenId）。外部（OpenSea）通过 id 参数和 tokenURI 接口，可以获取到 NFT 的 metadata（实际上就是一段 JSON 标准格式的数据），解析出它的内容，比如名称、描述、图片地址、属性等、然后通过 Web 前端页面展示出来，一个 NFT 的外在形式就体现出来了。

#### 3. 前端和后端
从技术角度看，前后端是连接区块链节点和用户的桥梁，用户是没法直接与区块链节点产生互动的，所以需要可视化的交互。前后端技术开发的独立网站或移动端App，承载 NFT 项目的用户交互需求，比如项目信息展示、铸造功能等。<br>
这里的前端、后端、智能合约等技术实现的应用，就是 DApp，在第四章有详细讲解。

#### 其它
- 市面上有不少一键发布 NFT 项目的网站，不需要自己技术实现，可以大大降低 NFT 的创作门槛，是非常方便的。
- 更加精美、功能更丰富的项目，还是需要定制化开发的，取决于项目方需求。

### 运营
在 NFT 创作完成前后都有不少运营工作是需要做的，大概有以下这些。

#### 营销和推广
- 制定营销策略，包括社交媒体宣传、广告、合作伙伴关系、公关等，以推广NFT项目。
- 与名人、艺术家或其他影响力人士合作，以提高知名度。
- 创建网站和社交媒体资料，以展示 NFT 作品并与社区互动。

#### 作品发行
- 策划发行前预热，这个阶段一般会采用网站显示倒计时方式，告知用户准确的发行时间
- 在发行的过程中，根据策略做好轮次控制，这里是指通过预置的智能合约功能，来控制 NFT 的发行。比如有一些 NFT 为了回馈 OG、合作方、社区积极参与者等，在发行早期，只允许白名单用户铸造 NFT；通过空投方式，免费回馈用户；控制发行次数和 NFT 数量，做运营评估或饥饿营销等。

#### 市场跟进
- 确保二级市场流动性。
- 跟进 NFT 价格和趋势，收集和分析数据，以做出相应运营策略。

#### 社区管理
- 制定社区规则和标准，以维护积极的社区氛围。
- 建立和维护社交媒体、聊天室和论坛，以便与收藏家和持有者互动。
- 积极响应社区问题、提供支持和解决争议。

## 创作优秀 NFT 的要素归纳
- 一致的设计：NFT 应该是创作者一种独特的表达方式，如何让这些表达独特且具有一致的艺术主题，是创作者需要重要考虑的。比如 CryptoPunks 的反判、BAYC 的厌世。
- 链上设计：通过当前链的生态来设计操盘手法，另外定价、数量、税收策略、激励措施等都会影响 NFT 的售卖和流通。
- 稀有度设计：合理且独特的稀有度设计，可以让 NFT 有更高的溢价，可玩性更高。
- 网站设计：优秀的网站设计属于锦上添花，它应该与 NFT 具有一致的艺术表达，是项目背后实力的一种表现。
- 运营：运营者是 NFT 与持有者之间的沟通桥梁，高频率和高质量的互动，是优秀 NFT 所必须的。

## 引用及注释
<sup>[1]</sup> 铸造（mint），是创建或铸造新的代币或资产的过程。<br>
<sup>[2]</sup> 白名单（allowlist），是一种限制性列表，通常由NFT项目的创建者或管理员创建和维护。白名单上列出了一些特定的地址，这些地址被允许参与NFT项目的特定活动或获取特定NFT。<br>
<sup>[3]</sup> 空投（airdrop），是免费发放 NFT 的过程。激励、促进互动、数量有限。<br>
<sup>[4]</sup> 盲盒（mystery boxes），一种基于 NFT 的销售和交易模型。
