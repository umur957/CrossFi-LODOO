# CrossFi-LODOO
# Donanim icin 4/2 CPU ve beraberinde getirdiği RAM

# Kurulum Adimlari

# Sistemi güncelliyoruz
sudo apt update
sudo apt-get install git curl build-essential make jq gcc snapd chrony lz4 tmux unzip bc -y

# Binary yukluyoruz
wget https://github.com/crossfichain/crossfi-node/releases/download/v0.3.0-prebuild3/crossfi-node_0.3.0-prebuild3_linux_amd64.tar.gz && tar -xf crossfi-node_0.3.0-prebuild3_linux_amd64.tar.gz

# Config yukluyoruz
git clone https://github.com/crossfichain/testnet.git

# Node u baslatiyoruz
./bin/crossfid start --home ./testnet

# Portlari kontrol edelim
sudo lsof -i -P -n | grep LISTEN
sudo ufw status
# Statu inaktifse
sudo ufw enable
sudo ufw allow 22
sudo ufw allow 22/tcp
sudo ufw allow 26057
sudo ufw allow 26057/tcp
# servis kurulumu / tek seferde gir bu kodlari
#tek seferde gir bu kodları
sudo tee /etc/systemd/system/crossfid.service > /dev/null <<EOF  
[Unit]
Description=crossfid Daemon
After=network-online.target

[Service]
User=root
ExecStart=/root/bin/crossfid start --home /root/testnet
Restart=always
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF
# #Bunlari da tek tek gir / sync olmasini bekle
sonra sudo systemctl daemon-reload
sudo systemctl enable crossfid 
sudo systemctl restart crossfid 
sudo journalctl -u crossfid -f -o cat

# Cuzdanini import etmek icin bunu yaz, validatoradin yerine kendi verdigin validator adini yaz, bu kod sonrasi cuzdaninin seed keylerini gir
./bin/crossfid --home ./testnet keys add validatoradin –recover
# Validator olustur - alttaki LODOO yazan yerlerine kendine gore degistir
./bin/crossfid --home ./testnet tx staking create-validator \
  --amount=9850000000000000000000mpx \
  --pubkey=$(./bin/crossfid --home ./testnet tendermint show-validator) \
  --moniker="LODOO" \
  --website="https://x.com/LODOO" \
  --chain-id="crossfi-evm-testnet-1" \
  --commission-rate="0.10" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1000000" \
  --gas="auto" \
  --gas-prices="10000000000000mpx" \
  --gas-adjustment=1.5 \
  --from=LODOO

# Hata almadiysan alttaki linkten validatorunun durumuna bak
https://test.xfiscan.com/
