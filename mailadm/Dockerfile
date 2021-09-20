FROM docker-mailadm_dovecot as dvct

FROM alehaa/debian-systemd:buster

ARG VMAIL_UID=1000
ARG VMAIL_GID=1000

# Create vmail user/group with desired UID/GID prior to installing Postfix.
# Otherwise it will create its own "vmail" user.
RUN addgroup --system --gid $VMAIL_GID vmail
RUN adduser --system vmail --uid $VMAIL_UID --gid $VMAIL_GID

# Install Pillow (py3-pillow) from Alpine repository to avoid compiling it.
# Postfix is required to run "postmap" command.
#RUN apk add --no-cache git bash python3 py3-pip py3-pillow postfix python3-dev
COPY sources.list /etc/apt/sources.list
RUN apt update
RUN apt install -y git bash python3 python3-pip python3-venv python3-pillow python3-dev wget
RUN wget https://cloud.0x90.space/s/sasqNIH2bhINwgE/download -O /usr/sbin/postmap

RUN adduser --system mailadm --home /var/lib/mailadm
RUN addgroup mailadm vmail

WORKDIR /root
#RUN git clone --no-checkout https://github.com/deltachat/deltachat-core-rust.git
#WORKDIR /root/deltachat-core-rust/python
#RUN git checkout --quiet py-1.58.0
#RUN python install_python_bindings.py
#WORKDIR /root
RUN git clone --no-checkout   https://github.com/deltachat/mailadm
WORKDIR /root/mailadm
RUN git checkout --quiet bot

#RUN python3 -m pip -q install wheel
#RUN python3 -m pip -q install .

#ENV MAILADM_DB=/var/lib/mailadm/mailadm.db
#ARG MAIL_DOMAIN=example.org
#ARG WEB_ENDPOINT=http://example.org/new_email

#RUN chmod g+w /var/lib/mailadm

COPY .env /root/mailadm/
COPY --from=dvct /etc/dovecot /etc/dovecot
RUN ./install_mailadm.sh

USER vmail
WORKDIR /var/lib/mailadm
#RUN mailadm init --web-endpoint=$WEB_ENDPOINT --mail-domain=$MAIL_DOMAIN --vmail-user=vmail

#CMD ["gunicorn", "-b", ":3691", "-w", "1", "mailadm.app:app"]
USER mailadm
RUN . venv/bin/activate
ENV MAILADM_DB=/var/lib/mailadm/mailadm.db
CMD bash
