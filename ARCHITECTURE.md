This diagram shows components of the chatmail server; this is a draft
overview as of mid-August 2025:

```mermaid
graph LR;
    cmdeploy --- sshd;
    letsencrypt --- acmetool-redirector;
    acmetool-redirector --- |80|port80r@{ shape: framed-circle, label: "80" };
    port80l@{ shape: framed-circle, label: "80" } --- |80|nginx;
    nginx --- |8443|nginx:internal;
    nginx:internal --- Website:/var/www/html;
    nginx:internal --- newemail.py;
    nginx:internal --- autoconfig.xml;
    cron --- acmetool;
    cron --- chatmail-metrics;
    cron --- expunge;
    chatmail-metrics --- Website:/var/www/html;
    acmetool --- certs;
    nginx --- |465|postfix;
    nginx --- |993|dovecot;
    autoconfig.xml --- postfix;
    autoconfig.xml --- dovecot;
    postfix --- Users:home/vmail/mail;
    postfix --- |10080,10081|filtermail;
    postfix --- echobot;
    postfix --- |doveauth.socket|doveauth;
    dovecot --- |doveauth.socket|doveauth;
    dovecot --- Users:/home/vmail/mail;
    dovecot --- |metadata.socket|chatmail-metadata;
    doveauth --- Users:/home/vmail/mail;
    expunge --- Users:/home/vmail/mail;
    chatmail-metadata --- iroh-relay;
```

(Arrows in this diagram do not have a specific formal meaning; they
signify "depends on", or "uses", or "sends data to", or just "relates
to".)
