# opengrok-service

## Steps
1. Download the opengrok.war file from the official site.
2. Create work directory for opengrok.
```shell
mkdir -p data/{src,data,dist,etc,log}
```
3. Unpack OpenGrok Tarball
```shell
tar -C data/dist --strip-components=1 -xzf downloads/opengrok-1.13.9.tar.gz
```
4. Copy the logging configuration file to the etc directory.
```shell
cp data/dist/doc/logging.properties data/etc
```
5. Download source codes into src directory
6. Download apache tomcat 
7. Copy the source.war file to the webapps directory of tomcat.
```shell
export WORK=/Users/sunjoo/workspace/opengrok-service
opengrok-deploy -c ${WORK}/data/etc/configuration.xml ${WORK}/data/dist/lib/source.war ${WORK}/program/apache-tomcat-10.1.26/webapps

```
8. Create the configuration file template.
```shell
opengrok-groups -a ./data/dist/lib/opengrok.jar -- -e > data/etc/read-only.xml
```
or
```shell
 opengrok-groups -j `which java`  -a ./data/dist/lib/opengrok.jar -- -e > data/etc/read-only.xml
 ```
9. Edit read-only.xml file - Add 'authenticationTokens' property to enable REST API call.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<java version="17.0.11" class="java.beans.XMLDecoder">
 <object class="org.opengrok.indexer.configuration.Configuration" id="Configuration0">
  <void property="sourceRoot"> <!-- name of the property in configuration -->
   <string>/home/sunjoopark/workspace/opengrok-service/data/src</string> <!-- java type and value -->
  </void>
  <void property="authenticationTokens">
   <void method="add">
    <string>bar</string>
   </void>
   <void method="add">
    <string>foo</string>
   </void>
  </void>
 </object>
</java>
```
10. Deploy the web app
```shell
#!/bin/bash
export WORK=/Users/sunjoo/workspace/opengrok-service
opengrok-deploy -c ${WORK}/data/etc/configuration.xml \
${WORK}/data/dist/lib/source.war \
${WORK}/program/apache-tomcat-10.1.26/webapps
```
10. Create Index
```shell
export WORK=/Users/sunjoo/workspace/opengrok-service
opengrok-indexer \
  -j `which java` \
  -J=-Djava.util.logging.config.file=${WORK}/data/etc/logging.properties \
  -a ${WORK}/data/dist/lib/opengrok.jar -- \
    -s ${WORK}/data/src \
    -d ${WORK}/data/data -H -P -S -G -W ${WORK}/data/etc/configuration.xml \
    -R ${WORK}/data/etc/read-only.xml \
    --token foo \
     -U http://localhost:8080/source
```
```shell
#!/bin/bash
export WORK=/home/sunjoopark/workspace/opengrok-service
java -Djava.util.logging.config.file=${WORK}/data/etc/logging.properties \
    -jar ${WORK}/data/dist/lib/opengrok.jar \
    -c /usr/bin/ctags \
    -s ${WORK}/data/src \
    -d ${WORK}/data/data -H -P -S -G -R ${WORK}/data/etc/read-only.xml \
    -W ${WORK}/data/etc/configuration.xml \
    --token foo \d
     -U http://localhost:8080/source
```