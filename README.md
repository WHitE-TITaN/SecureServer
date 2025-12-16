# SecureServer - HTTPS Web Application with SSL

## Overview

SecureServer is a Java web application deployed on **Apache Tomcat** demonstrating **secure HTTPS communication** using **SSL certificates**. This mini-project shows how to configure a local server with HTTPS, generate SSL certificates, and redirect HTTP traffic to HTTPS for secure communication.

---

## Features

* Simple Java web application running on Tomcat
* HTTPS enabled using **self-signed SSL certificate**
* HTTP ➞ HTTPS automatic redirection
* Gradle build for easy compilation and deployment
* Demo-ready for local testing

---

## Prerequisites

* **Java JDK 17 or above**
* **Apache Tomcat 9+**
* **Gradle 8+** (for building the project)
* Basic understanding of web servers and SSL/TLS

---

## Setup Instructions

### 1. Clone the Repository

```bash
git clone https://github.com/WHitE-TITaN/SecureServer.git
cd SecureServer
```

---

### 2. Build the Project with Gradle

```bash
gradle build
```

This generates the `.war` file in `build/libs/secureServer.war`.

---

### 3. Install Apache Tomcat

* Download [Apache Tomcat 9/10](https://tomcat.apache.org/) for Windows
* Install and start the server
* Test by visiting:

```
http://localhost:8080
```

---

### 4. Generate a Self-Signed SSL Certificate

Tomcat uses a **Java Keystore (.jks)**:

```cmd
mkdir C:\tomcat
keytool -genkey -alias tomcat -keyalg RSA -keystore C:\tomcat\mykeystore.jks -keysize 2048 -validity 90
```

>Command format - -genkey -alias ServerType(tomcat, appache etc) -keyalg (algorithm to be used) -keystore (location to store the certificate) -keysize (size of the key) -validity (certificate validity date in days)

Fill in:

* Keystore password
* Name (CN)
* Organization (O)
* City (L)
* State (ST)
* Country (C)

This creates `mykeystore.jks`.
>Important: This command needs a c:/tomcat directory to store the keystore. !Ensure the directory exists before running the command.

---

### 5. Configure Tomcat for HTTPS

1. Open `TOMCAT_HOME/conf/server.xml`
2. Uncomment the HTTPS connector section and modify:

```xml
<Connector port="8443"
           protocol="org.apache.coyote.http11.Http11NioProtocol"
           maxThreads="150"
           SSLEnabled="true"
           scheme="https" secure="true">
    <SSLHostConfig>
        <!-->certificateKeystoreFile attribute contain the location the certificate
        the default location will be C:\tomcat\mykeystore.jks<-->
        <Certificate certificateKeystoreFile="C:\tomcat\mykeystore.jks"
                     certificateKeystorePassword="YOUR_PASSWORD"
                     type="RSA" />
    </SSLHostConfig>
</Connector>
```

3. Save and restart Tomcat.

---

### 6. Deploy the WAR File

* Copy `secureServer.war` from `build/libs` to:

```
TOMCAT_HOME/webapps/
```

* Tomcat will auto-deploy it.

>Or change the server.xml to point to the application or cloned location.

```xml
  <Host name="localhost"  appBase="webapps"
            unpackWARs="true" autoDeploy="true">

       <Context docBase="/location" path="/" reloadable="true"/>


        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="localhost_access_log" suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b" />

      </Host>
```
---

### 7. Test HTTPS

Open browser:

```
https://localhost:8443/
```

* Browser shows a padlock (self-signed certificate warning)
* Click **Advanced → Proceed**
* Your application is now running securely over HTTPS

---

### 8. Optional: Force HTTP → HTTPS

Add this to your app’s `web.xml`:

```xml
<security-constraint>
    <web-resource-collection>
        <web-resource-name>SecureApp</web-resource-name>
        <url-pattern>/*</url-pattern>
    </web-resource-collection>
    <user-data-constraint>
        <transport-guarantee>CONFIDENTIAL</transport-guarantee>
    </user-data-constraint>
</security-constraint>
```

Now, any HTTP request automatically redirects to HTTPS.

---

## Project Structure

```
SecureServer/
│
├── build/                # Gradle build output
├── src/
│   ├── main/java         # Java source code
│   └── main/webapp       # HTML/CSS/JSP files
├── build.gradle          # Gradle build script
├── settings.gradle
└── README.md
```

---

## Screenshots to Include

* Command prompt generating keystore
* Tomcat `server.xml` SSL configuration
* Browser showing HTTPS padlock
* Certificate properties
* HTTP → HTTPS redirect working

---

## Additional Notes

* Self-signed certificates are for demo purposes; production should use **Let’s Encrypt** or commercial certificates
* Gradle simplifies building and managing dependencies
* Project demonstrates **TLS handshake, secure communication, and server configuration**

---

## References

* [Apache Tomcat SSL Configuration](https://tomcat.apache.org/tomcat-9.0-doc/ssl-howto.html)
* [Keytool Documentation](https://docs.oracle.com/en/java/javase/17/docs/specs/man/keytool.html)
* [Gradle Official Docs](https://gradle.org/docs/)
