This diagram shows components of the chatmail server; this is a draft
overview as of mid-August 2025:

```mermaid
graph LR;
    cmdeploy --- sshd;
    letsencrypt --- acmetool-redirector;
    acmetool-redirector --- port80r@{ shape: text, label: "port 80" };
    port80l@{ shape: text, label: "port 80" } --- nginx;
    nginx --- |8443|nginx-internal[nginx - internal];
    nginx-internal --- certs-nginx[TLS certs - /var/lib/acme];
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
    postfix --- certs-postfix[TLS certs - /var/lib/acme];
    postfix --- users[User data - home/vmail/mail];
    postfix --- |10080,10081|filtermail;
    postfix --- echobot;
    postfix --- |doveauth.socket|doveauth;
    dovecot --- |doveauth.socket|doveauth;
    dovecot --- users;
    dovecot --- |metadata.socket|chatmail-metadata;
    dovecot --- certs-dovecot[TLS certs - /var/lib/acme];
    doveauth --- users;
    expunge --- users;
    chatmail-metadata --- iroh-relay;
    style certs fill:#ff6;
    style certs-nginx fill:#ff6;
    style certs-postfix fill:#ff6;
    style certs-dovecot fill:#ff6;
```

(Arrows in this diagram do not have a specific formal meaning; they
signify "depends on", or "uses", or "sends data to", or just "relates
to".)
