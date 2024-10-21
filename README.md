## Running environment
**ubuntu 22.04**

**golang 1.18**

**fabric 2.4.3**

**docker-compose**

**IPFS**

(The golang version cannot be too high. It is found that versions above 1.18 will cause fabric-sdk-go compilation to fail.)

## steps
### 1. Deploy the fabric-sample test network

1.1 clone the repository

``` bash
git clone https://github.com/hyperledger/fabric-samples.git
cd fabric-samples
git checkout v2.4.3
```

1.2 download binary files

```bash
wget https://github.com/hyperledger/fabric/releases/download/v2.4.3/hyperledger-fabric-linux-amd64-2.4.3.tar.gz
wget https://github.com/hyperledger/fabric-ca/releases/download/v1.5.3/hyperledger-fabric-ca-linux-amd64-1.5.3.tar.gz
tar xvzf hyperledger-fabric-linux-amd64-2.4.3.tar.gz
tar xvzf hyperledger-fabric-ca-linux-amd64-1.5.3.tar.gz
cd bin
export PATH=${PWD}:$PATH
cd ../
```
1.3 download docker images

```bash
docker pull hyperledger/fabric-peer:2.4.3
docker pull hyperledger/fabric-orderer:2.4.3
docker pull hyperledger/fabric-ccenv:2.4.3
docker pull hyperledger/fabric-tools:2.4.3
docker pull hyperledger/fabric-baseos:2.4.3
docker pull hyperledger/fabric-ca:1.5.3

docker tag hyperledger/fabric-peer:2.4.3 hyperledger/fabric-peer
docker tag hyperledger/fabric-peer:2.4.3 hyperledger/fabric-peer:2.4
docker tag hyperledger/fabric-orderer:2.4.3 hyperledger/fabric-orderer
docker tag hyperledger/fabric-orderer:2.4.3 hyperledger/fabric-orderer:2.4
docker tag hyperledger/fabric-ccenv:2.4.3 hyperledger/fabric-ccenv
docker tag hyperledger/fabric-ccenv:2.4.3 hyperledger/fabric-ccenv:2.4
docker tag hyperledger/fabric-tools:2.4.3 hyperledger/fabric-tools
docker tag hyperledger/fabric-tools:2.4.3 hyperledger/fabric-tools:2.4
docker tag hyperledger/fabric-baseos:2.4.3 hyperledger/fabric-baseos
docker tag hyperledger/fabric-baseos:2.4.3 hyperledger/fabric-baseos:2.4
docker tag hyperledger/fabric-ca:1.5.3 hyperledger/fabric-ca
docker tag hyperledger/fabric-ca:1.5.3 hyperledger/fabric-ca:1.5
```

1.3 start the fabric test network 

```bash
cd test-network
./network.sh down
./network.sh up

```

1.4 create the channel
```bash
./network.sh createChannel
```

you should see:

![image-20240704153459976](https://gitee.com/wujian2023/typora_images/raw/master/auto_upload/image-20240704153459976.png)

### 2. Deploy the DTModeling contract

2.1 copy repository contract folder to test-network

```bash
cp -r {repo_path}/CP-ABE-Blockchain/contract .
cd contract/
go mod vendor
```

2.2 deploy the contract

```bash
./network.sh deployCC -ccn DTModeling -ccp contract -ccl go
```

![image-20240704160206689](https://gitee.com/wujian2023/typora_images/raw/master/auto_upload/image-20240704160206689.png)

### 3. Test if can connect to fabric by SDK

3.1 copy the fabric certificates to config folder

```
cd CP-ABE-Blockchain/config
cp -r {fabric-sample-path}/test-network/organizations/* .
```

![image-20240704160848466](https://gitee.com/wujian2023/typora_images/raw/master/auto_upload/image-20240704160848466.png)

3.2 test if can connect to fabric 

```
cd ../
go test -v ./test/ -run=TestGetBlockData
```

![image-20240704161806012](https://gitee.com/wujian2023/typora_images/raw/master/auto_upload/image-20240704161806012.png)

### 4. Start IPFS

**(If you don't want to start IPFS, you need to change the `shareFileToIpfs` method in `device.go`)**

4.1 download IPFS

```
wget https://dist.ipfs.tech/kubo/v0.29.0/kubo_v0.29.0_linux-amd64.tar.gz
tar -xvzf kubo_v0.29.0_linux-amd64.tar.gz
cd kubo
./ipfs init
./ipfs daemon
```

![image-20240704165314529](https://gitee.com/wujian2023/typora_images/raw/master/auto_upload/image-20240704165314529.png)

### 5. Run code

4.1 start the AS server

```bash
go run main.go
```

![image-20240704162021399](https://gitee.com/wujian2023/typora_images/raw/master/auto_upload/image-20240704162021399.png)

4.2 run the CP_ABE_Blockchain_test

```
go test -v test/CP_ABE_Blockchain_test.go
```

![image-20240704164425872](https://gitee.com/wujian2023/typora_images/raw/master/auto_upload/image-20240704164425872.png)

