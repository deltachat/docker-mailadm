FROM docker.io/alpine:3.14

ARG VMAIL_UID=1000
ARG VMAIL_GID=1000

# Same as in mailadm container
RUN addgroup -S -g $VMAIL_GID vmail
RUN adduser -S vmail -u $VMAIL_UID -G vmail

RUN apk add --no-cache dovecot dovecot-sqlite dovecot-lmtpd

EXPOSE 143
EXPOSE 24

# Not sure if needed
EXPOSE 12345

COPY dovecot.conf /etc/dovecot/dovecot.conf
COPY dovecot-sql.conf.ext /etc/dovecot/dovecot-sql.conf.ext

CMD ["dovecot", "-F"]
