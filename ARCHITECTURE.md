This diagram shows components of the chatmail server; this is a draft
overview as of mid-August 2025:

```mermaid
graph LR;
    cmdeploy --- sshd;
    letsencrypt --- |80|acmetool-redirector;
    acmetool-redirector --- |443|nginx-right[nginx];
    nginx --- |8443|nginx-internal["nginx (internal)"];
    nginx-internal --- website["`Website
    /var/www/html`"];
    nginx-internal --- newemail.py;
    nginx-internal --- autoconfig.xml;
    nginx-internal ~~~ certs-nginx["`TLS certs
    /var/lib/acme`"] --> nginx-internal;
    cron --- acmetool;
    cron --- chatmail-metrics;
    cron --- expunge;
    chatmail-metrics --- website;
    acmetool --> certs["`TLS certs
    /var/lib/acme`"];
    nginx --- |465|postfix;
    nginx --- |993|dovecot;
    autoconfig.xml --- postfix;
    autoconfig.xml --- dovecot;
    postfix --- echobot;
    postfix --- |10080,10081|filtermail;
    postfix --- users["`User data
    home/vmail/mail`"];
    postfix --- |doveauth.socket|doveauth;
    dovecot --- |doveauth.socket|doveauth;
    dovecot --- users;
    dovecot --- |metadata.socket|chatmail-metadata;
    doveauth --- users;
    expunge --- users;
    chatmail-metadata --- iroh-relay;
    certs-nginx --> postfix;
    certs-nginx --> dovecot;
    style certs fill:#ff6;
    style certs-nginx fill:#ff6;
    style nginx fill:#f86;
    style nginx-right fill:#f86;
```

(Arrows in this diagram do not have a specific formal meaning; they
signify "depends on", or "uses", or "sends data to", or just "relates
to".)
