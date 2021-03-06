# cryptonote-node-proxy

## Other cryptocurrency support

Supported algorithms:
* Cryptonight (electroneum and many other coins). Set coin to etn in the pool config, or copy lib/etn.js file to lib/yourcoin.js and add corresponding section in config.json.
* Cryptonight-heavy (SUMO)
* Cryptonightv7 (Monero v7)

## Update from previous version

Please run `npm update` if upgrading from previous release. This is to ensure new crypto libraries are downloaded and built.

## Setup Instructions

Based on a clean Ubuntu 16.04 LTS minimal install

## Deployment via Installer

1. Create a user 'nodeproxy' and assign a password (or add an SSH key. If you prefer that, you should already know how to do it)

```bash
useradd -d /home/nodeproxy -m -s /bin/bash nodeproxy
passwd nodeproxy
```

2. Add your user to `/etc/sudoers`, this must be done so the script can sudo up and do it's job.  We suggest passwordless sudo.  Suggested line: `<USER> ALL=(ALL) NOPASSWD:ALL`.  Our sample builds use: `nodeproxy ALL=(ALL) NOPASSWD:ALL`

```bash
echo "nodeproxy ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
```

3. Log in as the **NON-ROOT USER** you just created and run the [deploy script](https://github.com/passnet/cryptonote-node-proxy/raw/master/install.sh).  This is very important!  This script will install the proxy to whatever user it's running under!

```bash
curl -L https://github.com/passnet/cryptonote-node-proxy/raw/master/install.sh | bash
```

3. Once it's complete, copy `example_config.json` to `config.json` and edit as desired.
4. Run: `source ~/.bashrc`  This will activate NVM and get things working for the following pm2 steps.
8. Once you're happy with the settings, go ahead and start all the proxy daemon, commands follow.

```shell
cd ~/cryptonote-node-proxy/
pm2 start proxy.js --name=proxy --log-date-format="YYYY-MM-DD HH:mm Z"
pm2 save
```
You can check the status of your proxy by either issuing

```
pm2 logs proxy
```

or using the pm2 monitor

```
pm2 monit
```

## Known Issues

VMs with 512Mb or less RAM will need some swap space in order to compile the C extensions for node.  Bignum and the CN libraries can chew through some serious memory during compile.  In regards to this, one of our users has put together a guide for T2.Micro servers: https://docs.google.com/document/d/1m8E4_pDwKuFo0TnWJaO13LDHqOmbL6YrzyR6FvzqGgU (Credit goes to MayDay30 for his work with this!)

If not running on an Ubuntu 16.04 system, please make sure your kernel is at least 3.2 or higher, as older versions will not work for this.

Many smaller VMs come with ulimits set very low. We suggest looking into setting the ulimit higher. In particular, `nofile` (Number of files open) needs to be raised for high-usage instances.

If your system doesn't have AES-NI, then it will throw an error during the node-multi-hashing install, as this requires AES-NI.  If this is the case, go ahead and change the following line:
"multi-hashing": "git+https://github.com/Snipa22/node-multi-hashing-aesni.git",
to:
"multi-hashing": "git://github.com/clintar/node-multi-hashing.git#Nan-2.0",

In your `packages.json`, do a `npm install`, and it should pass.


## Performance

The proxy gains a massive boost over a basic pool by accepting that the majority of the hashes submitted _will_ not be valid (does not exceed the required difficulty of the pool).  Due to this, the proxy doesn't bother with attempting to validate the hash state nor value until the share difficulty exceeds the pool difficulty.

In testing, we've seen AWS t2.micro instances take upwards of 2k connections, while t2.small taking 6k.  The proxy is extremely light weight, and while there are more features on the way, it's our goal to keep the proxy as light weight as possible.

## Configuration Guidelines

Please check the [wiki](https://github.com/passnet/cryptonote-node-proxy/wiki/config_review) for information on configuration

## Developer Donations

The proxy is pre-configured for a 1% donation. This is easily toggled inside of it's configuration. If you'd like to make a one time donation, the addresses are as follows:

* XMR - 44M2Ro1pJ77CgToQ49SezUbQfcW7pbSMTAbj7f3godyvGdQpynRHZhS7vESJxvEvWi3JaqgFQXAjTA6UKgPtgBJh3SvxMYW
* BTC - 1GEuaQ8v4e9KFUSE5J4MMEHQR2WsMYpdz
* SUMO - Sumoo2P7oKS1RRqMPvssqTevJDYbchEgsJ8ZFA9Ya145j2hHmYMZ2Fz4QKmNgPwenigipPi75itAJ9YYFFoKWPZA4VEDwMe6oxs

You can also support original proxy developer Snipa22 by donating to those addresses:

* XMR - 44Ldv5GQQhP7K7t3ZBdZjkPA7Kg7dhHwk3ZM3RJqxxrecENSFx27Vq14NAMAd2HBvwEPUVVvydPRLcC69JCZDHLT2X5a4gr
* BTC - 114DGE2jmPb5CP2RGKZn6u6xtccHhZGFmM

## Known Working Pools

Monero:
* [XMRPool.net](https://xmrpool.net)
* [supportXMR.com](https://supportxmr.com)
* [pool.xmr.pt](https://pool.xmr.pt)
* [minemonero.pro](https://minemonero.pro)
* [XMRPool.xyz](https://xmrpool.xyz)
* [ViaXMR.com](https://viaxmr.com)
* [mine.MoneroPRO.com](https://mine.moneropro.com)
* [MinerCircle.com](https://www.minercircle.com)
* [xmr.p00ls.net](https://www.p00ls.net)
* [MoriaXMR.com](https://moriaxmr.com)
* [MoneroOcean.stream](https://moneroocean.stream)
* [SECUmine.net](https://secumine.net)
* [Chinaenter.cn](http://xmr.chinaenter.cn)

Multicoin pool:
* [Hashvault](https://www.hashvault.pro)

If you'd like to have your pool added, please make a pull request here.
