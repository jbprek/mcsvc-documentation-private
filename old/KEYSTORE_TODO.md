Failed attempt to use keytool and asymetric encryption for config service
-------------------------------------------------------------------------

### Create Keystore

1. Need to set the following environment vars for the following command
    
    - KEY_STORE_DN
    - KEY_STORE_PAT
    - KEY_STORE_ALIAS
    - KEY_STORE_KEYPASS
    - KEY_STORE_STOREPASS

2. Run the command

    ```
    keytool -genkeypair -alias $KEY_STORE_ALIAS -keyalg RSA \
        -dname "$KEY_STORE_DN" -keystore $KEY_STORE_PATH -storetype jks \
        -keypass $KEY_STORE_KEYPASS -storepass $KEY_STORE_STOREPASS
    
    ```


#### Example environment vars
```
KEY_STORE_DN='CN=Web Server,OU=Unit,O=Organization,L=City,S=State,C=US'
KEY_STORE_ALIAS='frx-config-service'
KEY_STORE_PATH='/var/docker/volumes/frx-config-service/frx-config-service.jks'
KEY_STORE_KEYPASS='changeme'
KEY_STORE_STOREPASS='letmein'

echo $KEY_STORE_DN
echo $KEY_STORE_ALIAS
echo $KEY_STORE_PATH
echo $KEY_STORE_STOREPASS
echo $KEY_STORE_KEYPASS
```
### Standalone

```
mvn spring-boot:run \
    -Dencrypt.keystore.location="$KEY_STORE_PATH" \
    -Dencrypt.keystore.password="$KEY_STORE_KEYPASS" \
    -Dencrypt.keystore.alias="$KEY_STORE_ALIAS" \
    -Dencrypt.keystore.secret="$KEY_STORE_STOREPASS" 
    
```
