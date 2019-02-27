# STEPS TO BUILD AND DEPLOY TRON:

## 1. install java and oracle-java8-installer
    sudo add-apt-repository -y ppa:webupd8team/java
    sudo apt-get -y update
    sudo apt-get -y install default-jre
    sudo apt-get -y install default-jdk
    sudo apt-get -y install oracle-java8-installer
    
## 2. Deploy TRON
    git clone https://github.com/tronprotocol/java-tron.git
    cd java-tron && git checkout -t origin/master
    ./gradlew build
    
## 3. mkdir TRON FullNode and soliditynode
    #Create separate directories for fullnode and soliditynode
    mkdir /deploy/fullnode
    mkdir /deploy/soliditynode
    cp build/libs/FullNode.jar /deploy/fullnode
    cp build/libs/SolidityNode.jar /deploy/soliditynode
    
## 4. running 
    git clone https://github.com/tronprotocol/tron-deployment.git
    cd tron-deployment
    cp main_net_config.conf /deploy/fullnode
    cp main_net_config.conf /deploy/soliditynode
    cd /deploy/fullnode && java -jar FullNode.jar -c main_net_config
    cd /deploy/soliditynode && java -jar SolidityNode.jar -c main_net_config
    
## 5. TRON HTTP Gateway interface
    en:https://github.com/tronprotocol/Documentation/blob/master/TRX/Tron-http.md
    cn:https://github.com/tronprotocol/Documentation/blob/master/TRX_CN/Tron-http.md
    
## 6. TRON explorer
    https://tronscan.org/#/

https://github.com/tronprotocol/Documentation/blob/master/TRX_CN/Tron-doc.md
