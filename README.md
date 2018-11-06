

# Usage

```bash
./scripts/bootstrap.sh [version] [ca version] [thirdparty_version]

Ex: 
./scripts/bootstrap.sh 1.2.1 1.2.1 1.2.1

cd first-network
./byfn.sh generate
./byfn.sh up

./byfn.sh down
```

## Chaincode explanation
```
* First step: Install the chaincode to both of Anchor org1.peer0 and Anchor org2.peer0
* Then: Send command init("A", 100, "B", 200) to Anchor org2.peer0 => chaincode instantiated
A = 100
B = 200
* Then: Query from Ancho org1.peer0 with command query("A") and receive the result is 100
* Then: Send command invoke("A", "B", 10)
A = 100 - 10 = 90
B = 200 + 10 = 210
* Last: Install the chaincode to org2.peer1. Query A with command query("A") and receive the result is 90
```

## Logs
```log
STEP 1: Generating certs and genesis block for channel 'mychannel' with CLI timeout of '10' seconds and CLI delay of '3' seconds

##### Generate certificates using cryptogen tool #########
/Volumes/Data/Docs/Blockchain/HyperledgerFabric/fabric-samples/first-network/../bin/cryptogen
+ cryptogen generate --config=./crypto-config.yaml
org1.example.com
org2.example.com

#########  Generating Orderer Genesis block ##############
/Volumes/Data/Docs/Blockchain/HyperledgerFabric/fabric-samples/first-network/../bin/configtxgen
+ configtxgen -profile TwoOrgsOrdererGenesis -outputBlock ./channel-artifacts/genesis.block

### Generating channel configuration transaction 'channel.tx' ###
+ configtxgen -profile TwoOrgsChannel -outputCreateChannelTx ./channel-artifacts/channel.tx -channelID mychannel

#######    Generating anchor peer update for Org1MSP   ##########
+ configtxgen -profile TwoOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/Org1MSPanchors.tx -channelID mychannel -asOrg Org1MSP

#######    Generating anchor peer update for Org2MSP   ##########
+ configtxgen -profile TwoOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/Org2MSPanchors.tx -channelID mychannel -asOrg Org2MSP

=> To see the generate certificates using: ls -la ./crypto-config


STEP 2: Starting for channel 'mychannel' with CLI timeout of '10' seconds and CLI delay of '3' seconds
Creating network "net_byfn" with the default driver
Creating volume "net_orderer.example.com" with default driver
Creating volume "net_peer0.org1.example.com" with default driver
Creating volume "net_peer1.org1.example.com" with default driver
Creating volume "net_peer0.org2.example.com" with default driver
Creating volume "net_peer1.org2.example.com" with default driver
Creating peer1.org1.example.com ... done
Creating peer1.org2.example.com ... done
Creating peer0.org2.example.com ... done
Creating peer0.org1.example.com ... done
Creating orderer.example.com    ... done
Creating cli                    ... done

Build your first network (BYFN) end-to-end test
===================== Channel 'mychannel' created =====================
+ peer channel create -o orderer.example.com:7050 -c mychannel -f ./channel-artifacts/channel.tx --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem

===================== peer0.org1 joined channel 'mychannel' =====================
===================== peer1.org1 joined channel 'mychannel' =====================
===================== peer0.org2 joined channel 'mychannel' =====================
===================== peer1.org2 joined channel 'mychannel' =====================
+ peer channel join -b mychannel.block

===================== Anchor peers updated for org 'Org1MSP' on channel 'mychannel' =====================
+ peer channel update -o orderer.example.com:7050 -c mychannel -f ./channel-artifacts/Org1MSPanchors.tx --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem

===================== Anchor peers updated for org 'Org2MSP' on channel 'mychannel' =====================
+ peer channel update -o orderer.example.com:7050 -c mychannel -f ./channel-artifacts/Org2MSPanchors.tx --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem

===================== Chaincode is installed on peer0.org1 =====================
+ peer chaincode install -n mycc -v 1.0 -l golang -p github.com/chaincode/chaincode_example02/go/

===================== Chaincode is installed on peer0.org2 =====================
+ peer chaincode install -n mycc -v 1.0 -l golang -p github.com/chaincode/chaincode_example02/go/

===================== Chaincode is instantiated on peer0.org2 on channel 'mychannel' =====================
+ peer chaincode instantiate -o orderer.example.com:7050 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n mycc -l golang -v 1.0 -c '{"Args":["init","a","100","b","200"]}' -P 'AND ('\''Org1MSP.peer'\'','\''Org2MSP.peer'\'')'

===================== Query successful on peer0.org1 on channel 'mychannel' =====================
+ peer chaincode query -C mychannel -n mycc -c '{"Args":["query","a"]}'
=> 100

===================== Invoke transaction successful on peer0.org1peer0.org2 on channel 'mychannel' =====================
+ peer chaincode invoke -o orderer.example.com:7050 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n mycc --peerAddresses peer0.org1.example.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt --peerAddresses peer0.org2.example.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt -c '{"Args":["invoke","a","b","10"]}'
=> status:200

===================== Chaincode is installed on peer1.org2 =====================
+ peer chaincode install -n mycc -v 1.0 -l golang -p github.com/chaincode/chaincode_example02/go/
=> <status:200 payload:"OK" >

===================== Query successful on peer1.org2 on channel 'mychannel' =====================
+ peer chaincode query -C mychannel -n mycc -c '{"Args":["query","a"]}'
=> 90

```

## License <a name="license"></a>

Hyperledger Project source code files are made available under the Apache
License, Version 2.0 (Apache-2.0), located in the [LICENSE](LICENSE) file.
Hyperledger Project documentation files are made available under the Creative
Commons Attribution 4.0 International License (CC-BY-4.0), available at http://creativecommons.org/licenses/by/4.0/.
