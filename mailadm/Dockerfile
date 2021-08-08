FROM docker.io/alpine:3.14

ARG VMAIL_UID=1000
ARG VMAIL_GID=1000

# Create vmail user/group with desired UID/GID prior to installing Postfix.
# Otherwise it will create its own "vmail" user.
RUN addgroup -S -g $VMAIL_GID vmail
RUN adduser -S vmail -u $VMAIL_UID -G vmail

# Install Pillow (py3-pillow) from Alpine repository to avoid compiling it.
# Postfix is required to run "postmap" command.
RUN apk add --no-cache git bash python3 py3-pip py3-pillow postfix

RUN adduser -S mailadm -G vmail --home /var/lib/mailadm

WORKDIR /root
RUN git clone --no-checkout https://github.com/deltachat/mailadm.git
WORKDIR /root/mailadm
RUN git checkout --quiet dfac74575d4301a3313e1f5d96865793c8a114e2

RUN python3 -m pip install -q .

ENV MAILADM_DB=/var/lib/mailadm/mailadm.db
ARG MAIL_DOMAIN=example.org
ARG WEB_ENDPOINT=http://example.org/new_email

RUN chmod g+w /var/lib/mailadm

USER vmail
WORKDIR /var/lib/mailadm
RUN mailadm init --web-endpoint=$WEB_ENDPOINT --mail-domain=$MAIL_DOMAIN --vmail-user=vmail

CMD ["gunicorn", "-b", ":3691", "-w", "1", "mailadm.app:app"]
