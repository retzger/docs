# v2.0 to v2.1

Upgrading from `v2.0` to `v2.1` is a bit simpler since we are not introducing any breaking API changes. Even though we try to ensure backward compatibility (BC) as much as possible, sometimes it is not possible or very complicated to avoid it and still create a good solution to a problem.

Upgrading, in general, is as simple as updating your installation through the commander or by
running `git pull` inside the installation directory. In a big application, however, there may
be more things to consider, which are explained in the following.

::: tip
This guide assumes you have Core installed inside the `~/core` directory with your configuration being located at `~/.config/ark-core/<network>`. If you are using different locations, you will need to adjust those inside the examples which can be found below.

Upgrading a complex software project always comes at the risk of breaking something, so make sure you have a backup.
:::

## Via Core Commander

The simple way to upgrade Core is using [Core Commander](https://github.com/ArkEcosystem/core-commander):

    cd ~/core-commander
    bash commander.sh

This command will execute the **Core Commander** and perform a check for updates. If one is available, you will be
asked to update, press `Y` to complete the upgrade and restart your relay and forger.

## Via Git (stable release)

Another way to upgrade is to run the latest stable release through the `master` branch:

    cd ~/core
    git reset --hard
    git fetch && git pull
    git checkout master
    yarn setup

## Via Git (specific version)

Another way to upgrade is to change to a specific version, for example to version 2.0.10 (replace this with the version you want):

    cd ~/core
    git reset --hard
    git fetch && git pull
    git checkout tags/2.1.0
    yarn setup

### Notes

- The `yarn setup` command will upgrade Core and its direct dependencies. Without `yarn setup` the upgrade will fail as Core is written in TypeScript and the files need to be run through the TypeScript Compiler **(tsc)**.

- It is recommended to start your relay and forger through the [Core Commander](https://github.com/ArkEcosystem/core-commander). If you wish to run them on your own, you should take a look at how commander executes them via `pm2`.

After upgrading you should check whether your application still works as expected and no plugins are broken.
See the following notes on which changes to consider when upgrading from one version to another.

::: warning
The following upgrading instructions are cumulative. That is if you want to upgrade from version A to version C and there is version B between A and C, you need to follow the instructions for both A and B.
:::

## Upgrade from Core 2.0.\* to 2.1.0

- Run `yarn run upgrade` from the root of the repository.

- If you have been using custom dynamic fees, open the `~/.config/ark-core/<network>/plugins.js` file and locate the `@arkecosystem/core-transaction-pool` plugin. Add below code to it and enter your desired values.

```js
dynamicFees: {
    enabled: true,
    minFeePool: 3000,
    minFeeBroadcast: 3000,
    addonBytes: {
        transfer: 100,
        secondSignature: 250,
        delegateRegistration: 400000,
        vote: 100,
        multiSignature: 500,
        ipfs: 250,
        timelockTransfer: 500,
        multiPayment: 500,
        delegateResignation: 400000,
    },
},
```

## Conclusion

Once all these changes have been made you will need to restart your relay and forger *(if you are a delegate)* for these changes to take effect. If you've been running your relay and forger manually you need to change `packages/core/bin/ark` to `packages/core/dist/index.js` to ensure that the JavaScript files, created by the TypeScript Compiler, are executed. Also make sure that you are no longer passing in the `--data` and `--config` flags so core can pick up the system paths. Those flags are only intended for development or custom setups.