# Changelog for eth2-docker project

## Updating the project

To update the components of the project, run from within the project
directoy (`cd ~/eth2-docker` by default):

* `git pull`
* `cp .env .env.bak && cp default.env .env`
* Adjust contents of new `.env`, use `.env.bak` for guidance (LOCAL_UID
  and COMPOSE_FILE are the most common variables that may need to be adjusted)
* `sudo docker-compose build --no-cache beacon` (go for a 30 minute walk)
* `sudo docker-compose down`
* !! If coming from Lighthouse v0.2.x, make changes as per notes for [v0.1.6](#v016-2020-10-09)
* `sudo docker-compose up -d eth2`

## v0.1.6 2020-10-09

* Support for Lighthouse v0.3.0, drop support for v0.2.x
  * Please note that Lighthouse v0.3.x makes a breaking change to the beacon
    db. You will need to sync again from scratch, after building the new v0.3.0
    beacon image. You can force this with 
    `sudo docker-compose down`, `sudo docker volume rm eth2-docker_lhbeacon-data`
    (adjust to your directory path if you are not in `eth2-docker`, see
    `sudo docker volume ls` for a list).
  * Likewise, the location of the validator keystore has changed. The fastest way
    to resolve this involves importing the keystore from scratch:
    `sudo docker volume rm eth2-docker_lhvalidator-data` (as before, adjust for
    your directory), and then import the keystore(s) again with
    `sudo docker-compose run validator-import`. Your keystore(s) need to be in
    `.eth2/validator_keys` inside the project directory for that.
  * When you have completed the above steps, bring up Lighthouse with
    `sudo docker-compose up -d eth2` and verify that the beacon started syncing
    and the validator found its public key(s) by observing logs:<br />
    `sudo docker-compose logs -f beacon` and `sudo docker-compose logs validator | head -30`,
    and if you wish to see ongoing validator logs, `sudo docker-compose logs -f validator`.
  * The beacon will sync from scratch, which will take about half a day. Your
    validator will be marked offline for that duration.