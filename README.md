# PulseChain Testnet Validator Node Setup Helper Scripts for Ubuntu Linux

Read ALL the instructions as they will explain and tell you how to run these scripts

# Description

The setup script installs pre-reqs, golang, rust, go-pulse (geth fork) and lighthouse on a fresh, clean Ubuntu OS for getting a PulseChain Testnet (V3) Validator Node setup and running.

There are other helper scripts that do various things, check the notes for each one specifically for more info.

Note: the pulsechain validator setup script currently DOES NOT install monitoring/metrics packages such as Grafana or Prometheous, that may be done in a separate monitoring setup script

# Usage

```
$ ./pulsechain-testnet-validator-setup.sh [0x...YOUR ETHEREUM FEE ADDRESS] [12.89...YOUR SERVER IP ADDRESS]
```

**Command line options**

- ETHEREUM FEE ADDRESS is the FEE_RECIPIENT value for --suggested-fee-recipient to a wallet address you want to recieve priority fees from users whenever your validator proposes a new block (else it goes to the burn address)

- SERVER_IP_ADDRESS to your validator server's IP address

Note: you may get prompted throughout the process to hit [Enter] for OK and continue the process

For example when running Ubuntu on AWS EC2 cloud service, you can expect to hit OK on kernel upgrade notice, [Enter] or "1" to continue Rust install process and so on

# Environment
Tested on Ubuntu 22.04 (on Amazon AWS EC2 /w M2.2xlarge VM) running as a non-root user (ubuntu) with sudo privileges


**IMPORTANT things to do AFTER RUNNING THIS SCRIPT to complete the node setup**

1) Generate validator keys with deposit tool, import them into lighthouse and make your 32m tPLS deposit on the launchpad

Note: generate your keys on a different, secure machine (NOT on the validator server) and transfer them over for import

```
$ sudo apt install -y python3-pip
$ git clone https://gitlab.com/pulsechaincom/staking-deposit-cli.git
$ cd staking-deposit-cli && pip3 install -r requirements.txt && sudo python3 setup.py install
$ ./deposit.sh new-mnemonic
```

Then follow the instructions from there, copy them over to the validator and import into lighthouse AS THE NODE USER (not the 'ubuntu' user on ec2)

Something like this should work
```
$ sudo -u node bash
$ lighthouse account validator import --directory ~/validator_keys --network=pulsechain_testnet_v3
```

2) Start the beacon and validator clients

```
$ sudo systemctl daemon-reload
$ sudo systemctl enable lighthouse-beacon lighthouse-validator
$ sudo systemctl start lighthouse-beacon lighthouse-validator
```

If you want to look at lighthouse debug logs (similar to geth)

```
$ journalctl -u lighthouse-beacon.service (with -f to get the latest logs OR without the get the beginning)
$ journalctl -u lighthouse-validator.service
```

# Reset Validator Script
This helper script deletes all your validator data so you can try the setup again if you want a fresh install or feel like you made an error.

Be careful! It deletes and resets things, so read the code and make sure you understand what it does before using it.

# AWS EC2 Helper Script
Just some nice-to-haves if you're using the AWS Cloud for your validator server.

Now let's get validating! @rhmaximalist
