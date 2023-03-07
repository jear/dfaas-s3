# dfaas-s3
```
# Grab the self-signed cert from Datafabric endpoint
bigid@bigid-scanner-5f8d44cd5d-hb4m6:/$ echo -n | openssl s_client -connect 16.103.6.95:9000 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > /tmp/examplecert.crt
depth=0 C = US, L = San Jose, O = MapR, OU = MapR Engineering, CN = *.ezmeral.ftc5.hpelabs.net
verify error:num=20:unable to get local issuer certificate
verify return:1
depth=0 C = US, L = San Jose, O = MapR, OU = MapR Engineering, CN = *.ezmeral.ftc5.hpelabs.net
verify error:num=21:unable to verify the first certificate
verify return:1
DONE

# Check
cat /tmp/examplecert.crt

# Import in java keystore
bigid@bigid-scanner-5f8d44cd5d-hb4m6:/$ sudo /opt/java/openjdk/bin/keytool -import -trustcacerts -keystore /opt/java/openjdk/lib/security/cacerts  -storepass changeit -noprompt -alias mycert -file /tmp/examplecert.crt
Warning: use -cacerts option to access cacerts keystore
Certificate was added to keystore

# cp the keystore in BigID  /etc/scanner
bigid@bigid-scanner-5f8d44cd5d-hb4m6:/$ sudo cp /opt/java/openjdk/lib/security/cacerts /etc/scanner/

env
[ . . . ]
SCANNER_JAVA_APM_OPTS=
SCANNER_JAVA_OPTS= -server -Xmx12185m
IS_SCANNER_BLOCKED_BY_CONFIG_SERVICE=false
SCANNER_MAX_THREADS=50


# Update env 
#1.  SCANNER_SSL_ARGS="-Djavax.net.ssl.trustStoreType=pkcs12 -Djavax.net.ssl.trustStore=/etc/scanner/cacerts -Djavax.net.ssl.trustStorePassword=changeit" 
#2.  export SCANNER_JAVA_OPTS="${SCANNER_JAVA_MEMORY_ARG} ${SCANNER_SSL_ARGS}"

bigid@bigid-scanner-5f8d44cd5d-4mlb6:/$ echo ${SCANNER_JAVA_MEMORY_ARG}

bigid@bigid-scanner-5f8d44cd5d-4mlb6:/$ echo ${SCANNER_SSL_ARGS}

sudo ps -ef | grep -i java
bigid          1       0  5 15:05 ?        00:02:52 java -cp /app.jar 
-Dloader.path=/usr/local/oracle/oracle8/ojdbc8-19.3.0.0.jar,/usr/local/ibm,/usr/local/shared-libs,/usr/local/sap/jconnect-16/jconn4.jar 
-Dlog4j2.formatMsgNoLookups=true 
-server -Xmx12185m 
-Djavax.net.ssl.trustStore=/docker-java-home/lib/security/cacerts 
-Djavax.net.ssl.trustStorePassword=changeit 
-Dloader.main=com.bigid.scanner.scannerboot.ScannerBootApplication 
-Djava.security.krb5.conf=/etc/scanner/krb5.conf org.springframework.boot.loader.PropertiesLauncher


bigid@bigid-scanner-5f8d44cd5d-4mlb6:/$ env | grep SCANNER
SCANNER_THREADS=5
SCANNER_JAVA_APM_OPTS=
SCANNER_JAVA_OPTS= -server -Xmx12185m
IS_SCANNER_BLOCKED_BY_CONFIG_SERVICE=false
SCANNER_MAX_THREADS=50


bigid@bigid-scanner-5f8d44cd5d-hb4m6:/$ export SCANNER_SSL_ARGS="
-Djavax.net.ssl.trustStoreType=pkcs12 
-Djavax.net.ssl.trustStore=/etc/scanner/cacerts 
-Djavax.net.ssl.trustStorePassword=changeit"

bigid@bigid-scanner-5f8d44cd5d-hb4m6:/$ export SCANNER_JAVA_OPTS=" -server -Xmx12185m  ${SCANNER_SSL_ARGS}"

bigid@bigid-scanner-5f8d44cd5d-hb4m6:/$ echo $SCANNER_JAVA_OPTS
-server -Xmx12185m -Djavax.net.ssl.trustStoreType=pkcs12 -Djavax.net.ssl.trustStore=/etc/scanner/cacerts -Djavax.net.ssl.trustStorePassword=changeit

# Restart app 

```
