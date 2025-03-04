# first install dependencies!
|      Tool      | Version |
|----------------|---------|
| Docker         | 24.x+   |
| Docker Compose | 2.x+    |
| Go             | 1.20+   |
| Node.js        | 18+     |
| npm            | latest  |
| Python         | 3.x     |

# download fabric installer
```shell
curl -sSLO https://raw.githubusercontent.com/hyperledger/fabric/main/scripts/install-fabric.sh && chmod +x install-fabric.sh
```

# install fabric with docker compose, samples and executables
# for now on use `sudo` or change user with `su`.
```shell
sudo ./install-fabric.sh docker samples binary
```

# enter test folder and up the network
```shell
cd fabric-samples/test-network
./network.sh up
```

# check if containers was up
```shell
docker ps -a
```

# create a channel
```shell
./network.sh createChannel -c customchannel
# or create and up channel
./network.sh up createChannel -c customchannel
```

# deploy container with a custom chaincode at your custom channel
```shell
./network.sh deployCC -ccn basic -c customchannel -ccp ../asset-transfer-basic/chaincode-go -ccl go
```

# export the binarys and fabric config path and config Org1
```shell
export PATH=${PWD}/../bin:$PATH
export FABRIC_CFG_PATH=$PWD/../config/
# also allow you to perate peer as Org1
export CORE_PEER_TLS_ENABLED=true
export CORE_PEER_LOCALMSPID="Org1MSP"
export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
export CORE_PEER_ADDRESS=localhost:7051
```

# initialize ledger with assets
```shell
peer chaincode invoke -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls --cafile "${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem" -C customchannel -n basic --peerAddresses localhost:7051 --tlsRootCertFiles "${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt" --peerAddresses localhost:9051 --tlsRootCertFiles "${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt" -c '{"function":"InitLedger","Args":[]}'

# check your assets use jq for formatting
peer chaincode query -C customchannel -n basic -c '{"Args":["GetAllAssets"]}' | jq
```

# change the owner of asset6 from Michel to Christopher
```shell
peer chaincode invoke -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls --cafile "${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem" -C customchannel -n basic --peerAddresses localhost:7051 --tlsRootCertFiles "${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt" --peerAddresses localhost:9051 --tlsRootCertFiles "${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt" -c '{"function":"TransferAsset","Args":["asset6","Christopher"]}'

# recheck assets
peer chaincode query -C customchannel -n basic -c '{"Args":["GetAllAssets"]}' | jq
```

# change to Org2
```shell
export CORE_PEER_TLS_ENABLED=true
export CORE_PEER_LOCALMSPID="Org2MSP"
export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt
export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp
export CORE_PEER_ADDRESS=localhost:9051

peer chaincode query -C customchannel -n basic -c '{"Args":["ReadAsset","asset6"]}' | jq
```

# destroy containers
```shell
./network.sh down
docker ps -a
```

# re deploy containers when needed
```shell
./network.sh up
```
