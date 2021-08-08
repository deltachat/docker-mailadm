FROM docker.io/alpine:3.14

ARG VMAIL_UID=1000
ARG VMAIL_GID=1000

# Create vmail user/group with desired UID/GID prior to installing Postfix.
# Otherwise it will create its own "vmail" user.
RUN addgroup -S -g $VMAIL_GID vmail
RUN adduser -S vmail -u $VMAIL_UID -G vmail

RUN apk add --no-cache postfix

ARG MAIL_DOMAIN=example.org

# Enable submission port
RUN postconf -M submission/inet="submission inet n - n - - smtpd" \
  && postconf -P submission/inet/syslog_name=postfix/submission \
  && postconf -P submission/inet/smtpd_tls_security_level=encrypt \
  && postconf -P submission/inet/smtpd_sasl_type=dovecot \
  && postconf -P submission/inet/smtpd_sasl_path=inet:dovecot:12345 \
  && postconf -P submission/inet/smtpd_sasl_auth_enable=yes \
  && postconf "myhostname=${MAIL_DOMAIN}" \
  && postconf "mydestination=" \
  && postconf "virtual_mailbox_domains=${MAIL_DOMAIN}" \
  && postconf "virtual_transport=lmtp:dovecot:24" \
  && postconf "virtual_mailbox_base=/home/vmail" \
  && postconf "virtual_mailbox_maps=hash:/var/lib/mailadm/virtual_mailboxes" \
  && postconf "maillog_file=/dev/stdout" \
  && postconf "smtpd_tls_cert_file=/certs/fullchain.pem" \
  && postconf "smtpd_tls_key_file=/certs/privkey.pem"

EXPOSE 587

CMD ["postfix", "start-fg"]
