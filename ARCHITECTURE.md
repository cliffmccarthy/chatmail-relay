This diagram shows components of the chatmail server; this is a draft
overview as of mid-August 2025:

```mermaid
graph LR;
    cmdeploy --- sshd;
    letsencrypt --- acmetool-redirector;
    acmetool-redirector --- port80r@{ shape: text, label: "port 80" };
    port80l@{ shape: text, label: "port 80" } --- nginx;
    nginx --- |8443|nginx-internal[nginx (internal)];
    nginx-internal --- website[Website - /var/www/html];
    nginx-internal --- newemail.py;
    nginx-internal --- autoconfig.xml;
    cron --- acmetool;
    cron --- chatmail-metrics;
    cron --- expunge;
    chatmail-metrics --- website;
    acmetool --- certs[TLS certs - /var/lib/acme];
    nginx --- |465|postfix;
    nginx --- |993|dovecot;
    autoconfig.xml --- postfix;
    autoconfig.xml --- dovecot;
    postfix --- users[User data - home/vmail/mail];
    postfix --- |10080,10081|filtermail;
    postfix --- echobot;
    postfix --- |doveauth.socket|doveauth;
    dovecot --- |doveauth.socket|doveauth;
    dovecot --- users;
    dovecot --- |metadata.socket|chatmail-metadata;
    doveauth --- users;
    expunge --- users;
    chatmail-metadata --- iroh-relay;
    style certs fill:#ff6;
```

(Arrows in this diagram do not have a specific formal meaning; they
signify "depends on", or "uses", or "sends data to", or just "relates
to".)
