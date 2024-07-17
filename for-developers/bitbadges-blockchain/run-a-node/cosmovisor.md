# Cosmovisor

**Installing Cosmovisor**

The first step is to download Cosmovisor as an executable using [their documentation](https://docs.cosmos.network/v0.50/build/tooling/cosmovisor). Below is the Dockerized way we do it.

```dockerfile
FROM --platform=linux golang:1.21 AS builder

ENV COSMOS_VERSION=v0.47.5
RUN apt-get update && apt-get install -y git curl
RUN apt-get install -y make wget

WORKDIR /root
RUN git clone --depth 1 --branch ${COSMOS_VERSION} https://github.com/cosmos/cosmos-sdk.git

WORKDIR /root/cosmos-sdk/tools/cosmovisor

RUN make cosmovisor
```

**Environment Variables**

You will then need to set the following environment variables. The DAEMON\_HOME will be the home of your config files.&#x20;

<pre class="language-docker"><code class="lang-docker"><strong>DAEMON_HOME=/root/.bitbadgeschain
</strong>DAEMON_NAME=bitbadgeschaind
</code></pre>

**Initializing Executables**

Then, run the following to setup your Cosmovisor directory. The executable should be named bitbadgeschaind (if not, please rename).

```bash
cosmosvisor init ./bitbadgeschaind
```

This will create the necessary folders and copy the executable into the DAEMON\_HOME/cosmovisor/genesis/bin.

IMPORTANT: Depending on your sync method (explained later), you will need to download all relevant executables. If you are syncing from genesis, you will need all executables to be able to sync to the current state. If you are syncing from a later time, you will only need the binaries used after that time. See Adding Upgrades below. You must repeat this process for all such executables.

**Adding Upgrades**

For a given upgrade, it will have a new binary and a \<upgrade-name>. \<upgrade-name> is the name used in the x/upgrade module when proposing a new software upgrade.

Depending on your version of cosmovisor, you may be able to run the following. Again, make sure the binary name is bitbadgeschaind.

```
cosmovisor add-upgrade ...
```

Or, to manually upgrade, do the following.

1. Download the new binary and name it bitbadgeschaind. Do this in a separate folder to not interfere with anything currently running.
2. Create the DAEMON\_HOME/cosmovisor/upgrades/\<upgrade-name> and DAEMON\_HOME/cosmovisor/upgrades/\<upgrade-name>/bin directory.
3. Copy the new upgrade executable to the folder (keeping its name as bitbadgeschaind).

```dockerfile
# upgrade name = abc123
RUN mkdir ${DAEMON_HOME}/cosmovisor/upgrades/abc123/
RUN mkdir ${DAEMON_HOME}/cosmovisor/upgrades/abc123/bin
RUN cp /path_to_executable ${DAEMON_HOME}/cosmovisor/upgrades/abc123/bin/bitbadgeschaind
```

##
