# Grafana & Prometheus

<div>

<figure><img src="../../.gitbook/assets/grafana.jpg" alt="" width="100"><figcaption></figcaption></figure>

 

<figure><img src="../../.gitbook/assets/prometheus (2).png" alt="" width="65"><figcaption></figcaption></figure>

</div>

```sh
#!/bin/bash
PROMETHEUS_CHECKSUM="3f558531c6a575d8372b576b7e76578a98e2744da6b89982ea7021b6f000cddd"
PROMETHEUS_USER="prometheus"
PROMETHEUS_VERSION="2.36.2"
PROMETHEUS_ARCH="linux-amd64"
PROMETHEUS_URL="https://github.com/prometheus/prometheus/releases/download/"


OPTIONS=$1
MONITORING=$2



function help(){
echo "monitoring-stack [deploy|uninstall] [grafana|prometheus]"
}


function env:load(){
if ! [ -f /etc/profile.d/user-env.sh ]
then
cat > /etc/profile.d/user-env.sh <<EOF
#!/bin/env
export PATH="$PATH:/usr/local/bin"
EOF
source /etc/profile.d/user-env.sh
else
source /etc/profile.d/user-env.sh
fi
}

function prometheus:deploy(){
check_user_prometheus=`id ${PROMETHEUS_USER}`	
if [ $? -eq 1 ]
then
    useradd --no-create-home --shell /bin/false ${PROMETHEUS_USER}
fi
[ -d /etc/prometheus ] && echo "Directory Exist" || mkdir  -p /etc/prometheus && chown ${PROMETHEUS_USER}:${PROMETHEUS_USER} /etc/prometheus;
[ -d /var/lib/prometheus ] &&  echo "Directory Exist" || mkdir /var/lib/prometheus && chown ${PROMETHEUS_USER}:${PROMETHEUS_USER} /var/lib/prometheus;

if ! [ -f /usr/local/bin/prometheus ] && ! [ -f /usr/local/bin/promtool ] 
then
  if [ -f /opt/${PROMETHEUS_USER}-${PROMETHEUS_VERSION}.${PROMETHEUS_ARCH}.tar.gz ]
  then 
     echo "FilePrometheus Exist"
  else
     wget -c ${PROMETHEUS_URL}/v${PROMETHEUS_VERSION}/${PROMETHEUS_USER}-${PROMETHEUS_VERSION}.${PROMETHEUS_ARCH}.tar.gz  -O /opt/${PROMETHEUS_USER}-${PROMETHEUS_VERSION}.${PROMETHEUS_ARCH}.tar.gz	
  fi
  check_prometheus_checksum=`sha256sum /opt/${PROMETHEUS_USER}-${PROMETHEUS_VERSION}.${PROMETHEUS_ARCH}.tar.gz | awk '{print $1}'`
  if [ "${PROMETHEUS_CHECKSUM}" == "${check_prometheus_checksum}" ]
  then
    tar xvf  /opt/${PROMETHEUS_USER}-${PROMETHEUS_VERSION}.${PROMETHEUS_ARCH}.tar.gz -C /opt/
    cp /opt/${PROMETHEUS_USER}-${PROMETHEUS_VERSION}.${PROMETHEUS_ARCH}/prometheus /usr/local/bin/
    cp /opt/${PROMETHEUS_USER}-${PROMETHEUS_VERSION}.${PROMETHEUS_ARCH}/promtool /usr/local/bin/
    cp -r /opt/${PROMETHEUS_USER}-${PROMETHEUS_VERSION}.${PROMETHEUS_ARCH}/consoles /etc/prometheus/ && chown -R prometheus:prometheus /etc/prometheus/consoles	
    cp -r /opt/${PROMETHEUS_USER}-${PROMETHEUS_VERSION}.${PROMETHEUS_ARCH}/console_libraries /etc/prometheus/ && chown -R prometheus:prometheus /etc/prometheus/console_libraries
    cp -r /opt/${PROMETHEUS_USER}-${PROMETHEUS_VERSION}.${PROMETHEUS_ARCH}/prometheus.yml /etc/prometheus/ && chown -R prometheus:prometheus /etc/prometheus/prometheus.yml
  else 
    echo "Checksumming Failed, File Integrity ${PROMETHEUS_USER}-${PROMETHEUS_VERSION}.${PROMETHEUS_ARCH}.tar.gz not match"	   
  fi
fi	



if ! [ -f /etc/systemd/system/prometheus.service ]
then
cat >/etc/systemd/system/prometheus.service<<EOF
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target
[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
--config.file /etc/prometheus/prometheus.yml \
--storage.tsdb.path /var/lib/prometheus/ \
--web.console.templates=/etc/prometheus/consoles \
--web.console.libraries=/etc/prometheus/console_libraries
[Install]
WantedBy=multi-user.target
EOF
systemctl daemon-reload
fi


systemctl start prometheus
systemctl enable prometheus
systemctl status prometheus
}


function prometheus:uninstall(){
systemctl stop prometheus
systemctl disable prometheus

userdel ${PROMETHEUS_USER}
rm /usr/local/bin/prometheus
rm /usr/local/bin/promtool
rm /etc/systemd/system/prometheus.service
rm /etc/profile.d/user-env.sh
rm -rf /etc/prometheus/
rm -rf /var/lib/prometheus/
rm -rf /opt/${PROMETHEUS_USER}-${PROMETHEUS_VERSION}.${PROMETHEUS_ARCH}
rm -rf /opt/${PROMETHEUS_USER}-${PROMETHEUS_VERSION}.${PROMETHEUS_ARCH}.tar.gz
}

function grafana:deploy(){
curl https://packages.grafana.com/gpg.key | sudo apt-key add -
echo "deb https://packages.grafana.com/oss/deb stable main" > /etc/apt/sources.list.d/grafana.list
apt update
apt install grafana -y
systemctl start grafana-server
systemctl enable grafana-server
}

function grafana:uninstall(){
systemctl disable grafana-server
systemctl stop grafana-server
apt purge grafana
}

check_os=`cat /etc/os-release  | grep "NAME=\"Ubuntu\"" | wc -l`

if [ ${check_os} -eq 1 ]
then
    env:load;
    case "${OPTIONS}" in
	 deploy)
         if [ "${MONITORING}" == "prometheus" ]
         then
              prometheus:deploy;
	 elif [ "${MONITORING}" == "grafana" ]
	 then
	      grafana:deploy;
	 else
	      help;
	 fi
	 ;;
         uninstall)
         if [ "${MONITORING}" == "prometheus" ]
         then
              prometheus:uninstall;
         elif [ "${MONITORING}" == "grafana" ]
	 then
              grafana:uninstall;
         else
              help;
         fi
         ;;
         *)
	      help;
	 ;;
    esac
else
   echo "Make sure your OS is Ubuntu"
fi  
```

> Reference RoomIT : [https://docs.roomit.xyz](https://docs.roomit.xyz/)
