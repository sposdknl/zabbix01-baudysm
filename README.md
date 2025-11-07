V INSTALLU: 

#!/usr/bin/env bash

# Aktualizace systému
sudo apt-get update

# Instalace základních nástrojů
sudo apt-get install -y wget gnupg net-tools

# Přidání Zabbix repozitáře
wget https://repo.zabbix.com/zabbix/7.0/debian/pool/main/z/zabbix-release/zabbix-release_latest_7.0+debian11_all.deb
sudo dpkg -i zabbix-release_latest_7.0+debian11_all.deb
sudo apt-get update

# Instalace Zabbix Agenta 2 s pluginy
sudo apt-get install -y zabbix-agent2 zabbix-agent2-plugin-*

# Spuštění a aktivace služby
sudo systemctl enable zabbix-agent2
sudo systemctl restart zabbix-agent2


V CONFIGU:

#!/usr/bin/env bash

UNIQUE_HOSTNAME="debian-baudysm"
SHORT_HOSTNAME=$(echo "$UNIQUE_HOSTNAME" | cut -d'-' -f1,2)

# Záloha konfiguračního souboru
sudo cp -v /etc/zabbix/zabbix_agent2.conf /etc/zabbix/zabbix_agent2.conf-orig

# Nastavení hostname a serveru
sudo sed -i "/^Hostname=/c\Hostname=$SHORT_HOSTNAME" /etc/zabbix/zabbix_agent2.conf
grep -q "^Hostname=" /etc/zabbix/zabbix_agent2.conf || echo "Hostname=$SHORT_HOSTNAME" | sudo tee -a /etc/zabbix/zabbix_agent2.conf

sudo sed -i "/^Server=/c\Server=enceladus.pfsense.cz" /etc/zabbix/zabbix_agent2.conf
grep -q "^Server=" /etc/zabbix/zabbix_agent2.conf || echo "Server=enceladus.pfsense.cz" | sudo tee -a /etc/zabbix/zabbix_agent2.conf

sudo sed -i "/^ServerActive=/c\ServerActive=enceladus.pfsense.cz" /etc/zabbix/zabbix_agent2.conf
grep -q "^ServerActive=" /etc/zabbix/zabbix_agent2.conf || echo "ServerActive=enceladus.pfsense.cz" | sudo tee -a /etc/zabbix/zabbix_agent2.conf

# Timeout a metadata
sudo sed -i "/^Timeout=/c\Timeout=30" /etc/zabbix/zabbix_agent2.conf
grep -q "^Timeout=" /etc/zabbix/zabbix_agent2.conf || echo "Timeout=30" | sudo tee -a /etc/zabbix/zabbix_agent2.conf

sudo sed -i "/^HostMetadata=/c\HostMetadata=SPOS" /etc/zabbix/zabbix_agent2.conf
grep -q "^HostMetadata=" /etc/zabbix/zabbix_agent2.conf || echo "HostMetadata=SPOS" | sudo tee -a /etc/zabbix/zabbix_agent2.conf

# Zobrazení rozdílů a restart služby
sudo diff -u /etc/zabbix/zabbix_agent2.conf-orig /etc/zabbix/zabbix_agent2.conf
sudo systemctl restart zabbix-agent2



![Obrázek(Host.png)