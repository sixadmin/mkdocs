---
author: Richard CRUZ
title: Configuration de fail2ban avec Authelia
---

# Configuration de fail2ban avec Authelia

Le but de cette procédure est de montrer comment configurer Authelia et Fail2ban pour bloquer les IP ayant une activité suspecte.

## Configuration de Authelia

Authelia fonctionne dans en container docker et on va paramétrer le fichier configuration.yml qui se trouve dans le dossier authelia (dossier au côté du docker-compose.yaml)

Au niveau de la rubrique log, on ajoute les lignes suivantes:

```
  ## File path where the logs will be written. If not set logs are written to stdout.
  file_path: /config/authelia.log
```
Les logs ne sont plus envoyer dans la sortie standard du container authelia mais dans le fichier /config/authelia.log de notre container.

Pour le rendre accessible par fail2ban, nous allons configurer un volume dans le docker-compose d'Authelia.

On rajoute donc les lignes suivants dans le docker-compose.yaml

```docker
    volumes:
      - ./authelia:/config
```

Si on se rend dans le dossier authelia, on va donc retrouver le fichier authelia.log, c'est exactement le même type de contenu qui était donc renvoyé vers la sortie standard avant que l'on fasse la modification.

Maintenant, on peut prendre configurer fail2ban pour qu'il parcours ce fichier.

## Configuration de fail2ban

Nous allons ensuite créer un fichier de configuration authelia.conf dans le dossier /etc/fail2ban/filter.d, voici son contenu:

```
# Fail2Ban filter for Authelia

# Make sure that the HTTP header "X-Forwarded-For" received by Authelia's backend
# only contains a single IP address (the one from the end-user), and not the proxy chain
# (it is misleading: usually, this is the purpose of this header).

# the failregex rule counts every failed 1FA attempt (first line, wrong username or password) and failed 2FA attempt
# second line) as a failure.
# the ignoreregex rule ignores info and warning messages as all authentication failures are flagged as errors
# the third line catches incorrect usernames entered at the password reset form
# the fourth line catches attempts to spam via the password reset form or 2fa device reset form. This requires debug logging to be enabled

[Definition]
failregex = ^.*Unsuccessful (1FA|TOTP|Duo|U2F) authentication attempt by user .*remote_ip"?(:|=)"?<HOST>"?.*$
            ^.*user not found.*path=/api/reset-password/identity/start remote_ip"?(:|=)"?<HOST>"?.*$
            ^.*Sending an email to user.*path=/api/.*/start remote_ip"?(:|=)"?<HOST>"?.*$

ignoreregex = ^.*level"?(:|=)"?info.*
              ^.*level"?(:|=)"?warning.*
```

Nous allons ensuite modifier le fichier /etc/fail2ban/jail.local

et rajouter la section suivante:

```
[authelia]
enabled = true
port = http,https,9091
filter = authelia
logpath = /chemin-vers-votre-fichier/authelia.log
maxretry = 3
bantime = 1d
findtime = 1d
chain = DOCKER-USER
action = iptables-allports[name=authelia]
```

Après avoir redémarré le service fail2ban, on peut faire un test en effectuant des tentatives par exemple depuis un téléphone portable.

Si on fait plusieurs tentatives de connexions avec des mauvais identifiants\ mots de passes, on retrouvera notre IP blacklisté 

```
richard@Homelab:/etc/fail2ban$ sudo fail2ban-client banned
[{'sshd': []}, {'authelia': ['37.171.XXX.189']}]
```

Pour autoriser de nouveau l'IP, on utilisera la commande suivante:

```
fail2ban-client set <jail-name> unbanip <ip-address>
```

Source pour installer fail2ban: https://hostnextra.com/learn/tutorials/how-to-install-fail2ban-on-ubuntu