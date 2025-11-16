
✅ Requisitos: Criar SWAP (obrigatório para FreePBX)

O Debian 12 em VPSs mais baratas não vem com swap → FreePBX usa muita RAM ao instalar.

fallocate -l 4G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
echo "/swapfile none swap sw 0 0" >> /etc/fstab

# 2 Habilitando portas no Nftables

sudo nft -f /etc/nftables.conf
sudo systemctl restart nftables
