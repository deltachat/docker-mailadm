#FROM docker.io/alpine:3.12
#RUN apk add --no-cache postfix

# Switched to debian because it generates TLS certificate by default
FROM docker.io/debian:buster

ARG VMAIL_UID=1000
ARG VMAIL_GID=1000

# Create vmail user/group with desired UID/GID prior to installing Postfix.
# Otherwise it will create its own "vmail" user.
RUN addgroup --system --gid $VMAIL_GID vmail
RUN adduser --system --uid $VMAIL_UID --gid $VMAIL_GID vmail

RUN apt-get update && apt-get install postfix -y

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

#CMD ["postfix", "start-fg"]
# Postfix runs in chroot, so resolv.conf should be copied inside.
# See https://serverfault.com/questions/1003885/postfix-in-docker-host-or-domain-name-not-found-dns-and-docker
CMD cp /etc/resolv.conf /var/spool/postfix/etc/resolv.conf && postfix start-fg
