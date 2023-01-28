# irV2ray
>>>Setup outside Server<<<

1.install Ubuntu and then 

apt update && apt upgrade -y
bash <(curl -Ls https://raw.githubusercontent.com/NidukaAkalanka/x-ui-english/master/install.sh)

2.write in note username & Password & port You set in setup

3.install BBR & enable auto startup x-ui

4.login to web interface and bookmark your web address

5.ssh to server again

cd ~
mkdir ssl
cd ssl/
openssl req -x509 -nodes -newkey rsa:2048 -keyout server.key -out server.crt

6.cat server.key & server.crt and export it too notepad(*)

7.go back to web interface open inbounds , ADD Inbound like these:
remark: Name Of Your Connection (example : Core)
Protocol: vmess
listening ip: 0.0.0.0
port : anything (example 80)
add user:
network: ws
path: /Name Of Your Connection (example : Core)
tls:turn on
minVersion: 1.1 maxVersion: 1.3
tls cert: certificate file content
raw data of cert: import it from notepad(*)
raw data of key: import it from notepad(*)
sniffing : turn on
save inbounds

8.go to settings open Others tab
set Time zone : Asia/Tehran
save 

>>>Setup inside Server<<<

1.install Ubuntu and then 

apt update && apt upgrade -y
bash <(curl -Ls https://raw.githubusercontent.com/NidukaAkalanka/x-ui-english/master/install.sh)

2.write in note username & Password & port You set in setup

3.install BBR & enable auto startup x-ui

4.login to web interface and bookmark your web address

5.go to Panel setting open Xray Configuration import these config:

{
  "api": {
    "services": [
      "HandlerService",
      "LoggerService",
      "StatsService"
    ],
    "tag": "api"
  },
  "inbounds": [
    {
      "listen": "127.0.0.1",
      "port": 62789,
      "protocol": "dokodemo-door",
      "settings": {
        "address": "127.0.0.1"
      },
      "tag": "api"
    }
  ],
  "outbounds": [
  
  {
    "tag": "proxy",
    "protocol": "vmess",
    "settings": {
      "vnext": [
        {
          "address": "Public Ip of Outside Server",
          "port":import your port that you set before on Outside Server,
          "users": [
            {
              "id": "import your id that you set before on Outside Server",
              "alterId": 0,
              "security": "chacha20-poly1305"
            }
          ]
        }
      ]
    },
    "streamSettings": {
      "network": "ws",
	  "wsSettings": {
          "connectionReuse": true,
          "path": "/Name Of Your Connection (example : Core)"
        },
	  
"security":"tls",
"tlsSettings":{
"allowInsecure":true
}
    },
    "mux": {
      "enabled": true
    }
  },
    {
      "protocol": "freedom",
      "settings": {}
    },
    {
      "protocol": "blackhole",
      "settings": {},
      "tag": "blocked"
    }
  ],
  "policy": {
    "system": {
      "statsInboundDownlink": true,
      "statsInboundUplink": true
    }
  },
  "routing": {
    "rules": [
      {
        "inboundTag": [
          "api"
        ],
        "outboundTag": "api",
        "type": "field"
      },
      {
        "ip": [
          "geoip:private"
        ],
        "outboundTag": "blocked",
        "type": "field"
      },
      {
        "outboundTag": "blocked",
        "protocol": [
          "bittorrent"
        ],
        "type": "field"
      }
    ]
  },
  "stats": {}
}


FINISH
