# Delta Chat testing environment

This reporitory contains Docker Compose configuration for running a test
server for [Delta Chat](https://delta.chat/).

Hostname is configured in `.env` file. Default hostname is
`testrun.localdomain`.

After configuring, you can start a server with
```
$ docker-compose up -d
```

SMTP is available at port 587 and IMAP is available at port 143.

## Setting up certificates

If you have a domain you own, you can generate valid certificates using
a tool such as [dehydrated](https://dehydrated.io/) and place them into
`certs` directory, which is used as a volume in `docker-compose.yml`.

If you don't have a valid domain, you can generate a snakeoil certificate:
```
openssl req -new -x509 -days 365 -nodes -out certs/fullchain.pem -keyout certs/privkey.pem
```

In this case make sure to change `WEB_ENDPOINT` to use `http://` scheme
in the `mailadm` container.

## Running tests in a virtual machine

If you don't want to install testing environment on your own host,
you can run it in a virtual machine. The only requirement is
[Vagrant](https://www.vagrantup.com/).

Run
```
vagrant up
```

Then connect to the development environment
```
vagrant ssh
```

Docker and other requirements are installed automatically.

## Running deltachat tests

Within SSH shell, start the server:
```
$ cd /vagrant
$ docker-compose up -d
```

Make sure you do not change the server hostname in the `.env`
file. Virtual machine is preconfigured with `testrun.localdomain`
hostname.

After starting the server, create a token for tests:
```
$ docker-compose exec mailadm mailadm add-token foobar --maxuse 100000 --token foobar
token:foobar
  prefix = tmp.
  expiry = 1d
  maxuse = 100000
  usecount = 0
  token  = foobar
  http://testrun.localdomain/new_email?t=foobar&n=foobar
  DCACCOUNT:http://testrun.localdomain/new_email?t=foobar&n=foobar
```

This runs a `mailadm` command inside `mailadm` container, creating a token
`foobar` which can be used 100000 times.

Now, run the tests:
```
$ export DCC_NEW_TMP_EMAIL='http://testrun.localdomain/new_email?t=foobar&n=foobar'
$ cd ~/deltachat-core-rust/python
$ python3 install_python_bindings.py
$ pytest
```
