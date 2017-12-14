# dashd
Litecoind with SSL support

## Deploy

### Generate SSL certs

    openssl req -x509 -nodes -days 3650 -newkey rsa:2048 -keyout server.key -out server.crt
    
### Prepare config

    PASSWORD=`pwgen -nB 10 1`
    tee dash.conf << EOF
    onlynet=IPv4
    server=1
    rpcuser=rpcuser
    rpcpassword=$PASSWORD
    rpcport=9332
    rpcconnect=127.0.0.1
    disablewallet=0
    printtoconsole=1
    EOF

### Deploy using Docker
    
    mkdir dashd-ssl data
    mv server.key dashd-ssl/
    mv server.crt dashd-ssl/
    docker run -d --restart=always --name=dashd -v ./dash.conf:/etc/dashd/dash.conf -v ./dashd-ssl/:/etc/dashd-ssl/ -v ./data/:/root/ kuberstack/dashd

### Deploy using Kubernetes

    kubectl create secret generic dashd-conf --from-file=dash.conf
    kubectl create secret generic dashd-ssl --from-file=server.crt --from-file=server.key
    
    git clone https://github.com/kuberstack/dashd
    $EDITOR dashd/kubernetes/storage.yaml  # Change volume-ID
    kubectl create -f ./kubernetes/
