BEP-20 토큰을 BNB체인 (Binance Smart Chain, BSC) 테스트넷에 배포하려면, Hardhat 프레임워크와 ethers.js 또는 web3.js 라이브러리를 사용하면 됩니다. 

아래는 간략한 가이드입니다:

1. **프로젝트 설정**

    - 프로젝트 디렉토리를 생성하고 초기화하세요:
    ```bash
    mkdir mintb-poc-token
    cd mintb-poc-token
    npm init -y
    ```

    - Hardhat 및 필요한 패키지 설치:
    ```bash
    npm install --save-dev hardhat @nomiclabs/hardhat-waffle ethereum-waffle ethers
    npm install @openzeppelin/contracts
    ```

    - Hardhat 프로젝트 초기화:
    ```bash
    npx hardhat
    ```

2. **토큰 스마트 컨트랙트 작성**
   
   `contracts/MintBPOCToken.sol`에 BEP-20 토큰 컨트랙트를 작성:

    ```solidity
    // SPDX-License-Identifier: UNLICENSED
    pragma solidity ^0.8.20;

    import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

    contract MintBPOCToken is ERC20 {
        constructor(address minter) ERC20("MintB POC Token", "MTB") {
            _mint(minter, 100000000 * 10 ** decimals());
        }
    }
    ```

    **Note:** `@openzeppelin/contracts` 라이브러리를 사용하여 BEP-20 (혹은 ERC-20) 토큰을 쉽게 작성할 수 있습니다. 위의 코드는 생성 시 지정된 지갑에 1억 개의 토큰을 생성합니다.

3. **hardhat-config.js 수정**

   - BSC 테스트넷을 추가하고 개인 키 설정:

    ```javascript
    require("@nomiclabs/hardhat-waffle");

    module.exports = {
      networks: {
        bsctestnet: {
          url: "https://data-seed-prebsc-1-s1.binance.org:8545/",
          chainId: 97,
          gasPrice: 10000000000,
          accounts: ['YOUR_PRIVATE_KEY']
        }
      },
      solidity: "0.8.20",
    };
    ```

4. **배포 스크립트 작성**

    - `scripts/deploy.js`:

    ```javascript
    async function main() {
      const [deployer] = await ethers.getSigners();
      console.log("Deploying with account:", deployer.address);
      
      const Token = await ethers.getContractFactory("MintBPOCToken");
      const token = await Token.deploy(deployer.address);
      await token.deployed();

      console.log("Token deployed to:", token.address);
    }

    main()
      .then(() => process.exit(0))
      .catch(error => {
        console.error(error);
        process.exit(1);
      });
    ```

5. **배포**

    ```bash
    npx hardhat run scripts/deploy.js --network bsctestnet
    ```

이 가이드는 BEP-20 토큰을 BSC 테스트넷에 배포하는 기본적인 단계를 설명합니다. 원하는 기능을 추가하거나 수정하려면 스마트 컨트랙트와 배포 스크립트를 적절하게 수정하세요.
