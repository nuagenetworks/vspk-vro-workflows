# VSPK-VRO-Workflows

Advanced workflows for the [Nuage vRO plug-in (vspk-vro)](https://github.com/nuagenetworks/vspk-vro)

#build

To build package: 

* mvn -Dmaven.wagon.http.ssl.insecure=true -Dvco.version={vro-version} -DrepoUrl=https://{vro-ip-address}:8281/vco-repo/ clean install

Example:

* mvn -Dmaven.wagon.http.ssl.insecure=true -Dvco.version=7.0.1 -DrepoUrl=https://192.168.1.15:8281/vco-repo/ clean install
