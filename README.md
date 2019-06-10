# Medichain Network Creation


## Enter the network directory
cd Medi_Chain/network


## Generate the artifacts and crypto materials
../tools/cryptogen generate --config=./crypto-config.yaml


## Create the orderer genesis block
export FABRIC_CFG_PATH=$PWD
### (Solo Ordering Service)
../tools/configtxgen -profile FiveOrgsOrdererGenesis -channelID byfn-sys-channel -outputBlock ./channel-artifacts/genesis.block
### Create the orderer genesis block (Raft Ordering Service)
../tools/configtxgen -profile SampleMultiNodeEtcdRaft -channelID byfn-sys-channel -outputBlock ./channel-artifacts/genesis.block
### Create the orderer genesis block (Kafka Ordering Service)
../tools/configtxgen -profile FiveOrgsOrdererGenesisKafka -channelID byfn-sys-channel -outputBlock ./channel-artifacts/genesis.block


## Create the channel transaction artifact
export CHANNEL_NAME=mychannel  && ../tools/configtxgen -profile FiveOrgsChannel -outputCreateChannelTx ./channel-artifacts/channel.tx -channelID $CHANNEL_NAME


## Define the anchor peers
../tools/configtxgen -profile FiveOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/DoctorMSPanchors.tx -channelID $CHANNEL_NAME -asOrg DoctorMSP
../tools/configtxgen -profile FiveOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/HospitalMSPanchors.tx -channelID $CHANNEL_NAME -asOrg HospitalMSP
../tools/configtxgen -profile FiveOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/LaboratoryMSPanchors.tx -channelID $CHANNEL_NAME -asOrg LaboratoryMSP
../tools/configtxgen -profile FiveOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/PharmacyMSPanchors.tx -channelID $CHANNEL_NAME -asOrg PharmacyMSP
../tools/configtxgen -profile FiveOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/HealthinsurerMSPanchors.tx -channelID $CHANNEL_NAME -asOrg HealthinsurerMSP


## Start the network
docker-compose -f docker-compose-cli.yaml up -d


## Enter the CLI container
docker exec -it cli bash


## Creating Channel
export CHANNEL_NAME=mychannel
peer channel create -o orderer.medichain.com:7050 -c $CHANNEL_NAME -f ./channel-artifacts/channel.tx --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/medichain.com/orderers/orderer.medichain.com/msp/tlscacerts/tlsca.medichain.com-cert.pem


## Each Organisation joining the channel
peer channel join -b mychannel.block
CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/hospital.medichain.com/users/Admin@hospital.medichain.com/msp CORE_PEER_ADDRESS=peer0.hospital.medichain.com:9051 CORE_PEER_LOCALMSPID="HospitalMSP" CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/hospital.medichain.com/peers/peer0.hospital.medichain.com/tls/ca.crt peer channel join -b mychannel.block
CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/laboratory.medichain.com/users/Admin@laboratory.medichain.com/msp CORE_PEER_ADDRESS=peer0.laboratory.medichain.com:11051 CORE_PEER_LOCALMSPID="LaboratoryMSP" CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/laboratory.medichain.com/peers/peer0.laboratory.medichain.com/tls/ca.crt peer channel join -b mychannel.block
CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/pharmacy.medichain.com/users/Admin@pharmacy.medichain.com/msp CORE_PEER_ADDRESS=peer0.pharmacy.medichain.com:13051 CORE_PEER_LOCALMSPID="PharmacyMSP" CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/pharmacy.medichain.com/peers/peer0.pharmacy.medichain.com/tls/ca.crt peer channel join -b mychannel.block
CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/healthinsurer.medichain.com/users/Admin@healthinsurer.medichain.com/msp CORE_PEER_ADDRESS=peer0.healthinsurer.medichain.com:15051 CORE_PEER_LOCALMSPID="HealthinsurerMSP" CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/healthinsurer.medichain.com/peers/peer0.healthinsurer.medichain.com/tls/ca.crt peer channel join -b mychannel.block


## Update the anchor peers
peer channel update -o orderer.medichain.com:7050 -c $CHANNEL_NAME -f ./channel-artifacts/DoctorMSPanchors.tx --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/medichain.com/orderers/orderer.medichain.com/msp/tlscacerts/tlsca.medichain.com-cert.pem
CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/hospital.medichain.com/users/Admin@hospital.medichain.com/msp CORE_PEER_ADDRESS=peer0.hospital.medichain.com:9051 CORE_PEER_LOCALMSPID="HospitalMSP" CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/hospital.medichain.com/peers/peer0.hospital.medichain.com/tls/ca.crt peer channel update -o orderer.medichain.com:7050 -c $CHANNEL_NAME -f ./channel-artifacts/HospitalMSPanchors.tx --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/medichain.com/orderers/orderer.medichain.com/msp/tlscacerts/tlsca.medichain.com-cert.pem
CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/laboratory.medichain.com/users/Admin@laboratory.medichain.com/msp CORE_PEER_ADDRESS=peer0.laboratory.medichain.com:11051 CORE_PEER_LOCALMSPID="LaboratoryMSP" CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/laboratory.medichain.com/peers/peer0.laboratory.medichain.com/tls/ca.crt peer channel update -o orderer.medichain.com:7050 -c $CHANNEL_NAME -f ./channel-artifacts/LaboratoryMSPanchors.tx --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/medichain.com/orderers/orderer.medichain.com/msp/tlscacerts/tlsca.medichain.com-cert.pem
CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/pharmacy.medichain.com/users/Admin@pharmacy.medichain.com/msp CORE_PEER_ADDRESS=peer0.pharmacy.medichain.com:13051 CORE_PEER_LOCALMSPID="PharmacyMSP" CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/pharmacy.medichain.com/peers/peer0.pharmacy.medichain.com/tls/ca.crt peer channel update -o orderer.medichain.com:7050 -c $CHANNEL_NAME -f ./channel-artifacts/PharmacyMSPanchors.tx --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/medichain.com/orderers/orderer.medichain.com/msp/tlscacerts/tlsca.medichain.com-cert.pem
CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/healthinsurer.medichain.com/users/Admin@healthinsurer.medichain.com/msp CORE_PEER_ADDRESS=peer0.healthinsurer.medichain.com:15051 CORE_PEER_LOCALMSPID="HealthinsurerMSP" CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/healthinsurer.medichain.com/peers/peer0.healthinsurer.medichain.com/tls/ca.crt peer channel update -o orderer.medichain.com:7050 -c $CHANNEL_NAME -f ./channel-artifacts/HealthinsurerMSPanchors.tx --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/medichain.com/orderers/orderer.medichain.com/msp/tlscacerts/tlsca.medichain.com-cert.pem


## Downing the network
docker-compose -f docker-compose-cli.yaml down


## Starting the network with CouchDB
docker-compose -f docker-compose-cli.yaml -f docker-compose-couch.yaml up -d


## Enter the CLI container
docker exec -it cli bash


## Installing the chaincode in the peer and instantiating it.
export CHANNEL_NAME=mychannel
peer chaincode install -n marbles -v 1.0 -p github.com/chaincode/marbles02/go
peer chaincode instantiate -o orderer.medichain.com:7050 --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/medichain.com/orderers/orderer.medichain.com/msp/tlscacerts/tlsca.medichain.com-cert.pem -C $CHANNEL_NAME -n marbles -v 1.0 -c '{"Args":["init"]}' -P "OR ('DoctorMSP.peer')"


## Performing some transactions
peer chaincode invoke -o orderer.medichain.com:7050 --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/medichain.com/orderers/orderer.medichain.com/msp/tlscacerts/tlsca.medichain.com-cert.pem -C $CHANNEL_NAME -n marbles -c '{"Args":["initMarble","marble1","blue","35","tom"]}'
peer chaincode invoke -o orderer.medichain.com:7050 --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/medichain.com/orderers/orderer.medichain.com/msp/tlscacerts/tlsca.medichain.com-cert.pem -C $CHANNEL_NAME -n marbles -c '{"Args":["initMarble","marble2","red","50","tom"]}'
peer chaincode invoke -o orderer.medichain.com:7050 --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/medichain.com/orderers/orderer.medichain.com/msp/tlscacerts/tlsca.medichain.com-cert.pem -C $CHANNEL_NAME -n marbles -c '{"Args":["initMarble","marble3","blue","70","tom"]}'
peer chaincode invoke -o orderer.medichain.com:7050 --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/medichain.com/orderers/orderer.medichain.com/msp/tlscacerts/tlsca.medichain.com-cert.pem -C $CHANNEL_NAME -n marbles -c '{"Args":["transferMarble","marble2","jerry"]}'
peer chaincode invoke -o orderer.medichain.com:7050 --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/medichain.com/orderers/orderer.medichain.com/msp/tlscacerts/tlsca.medichain.com-cert.pem -C $CHANNEL_NAME -n marbles -c '{"Args":["transferMarblesBasedOnColor","blue","jerry"]}'
peer chaincode invoke -o orderer.medichain.com:7050 --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/medichain.com/orderers/orderer.medichain.com/msp/tlscacerts/tlsca.medichain.com-cert.pem -C $CHANNEL_NAME -n marbles -c '{"Args":["delete","marble1"]}'


## Performing some regular query
peer chaincode query -C $CHANNEL_NAME -n marbles -c '{"Args":["readMarble","marble2"]}'


## Retrieving the history
peer chaincode query -C $CHANNEL_NAME -n marbles -c '{"Args":["readMarble","marble2"]}'