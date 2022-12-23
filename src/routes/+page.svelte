<script lang="ts">
    import { configure, getMethods } from "@radixdlt/connect-button";

    const sendTx = (address: string) =>
        getMethods().sendTransaction({
            version: 1,
            transactionManifest: `
          CREATE_RESOURCE Enum("Fungible", 18u8) Map<String, String>("description", "Dedo test token", "name", "Dedo", "symbol", "DEDO") Map<Enum, Tuple>() Some(Enum("Fungible", Decimal("15000")));
          CALL_METHOD ComponentAddress("${address}") "deposit_batch" Expression("ENTIRE_WORKTOP");`,
        });

    configure({
        dAppId: "dashboard",
        networkId: 11,
        logLevel: "DEBUG",
        onConnect: ({ setState, getWalletData }) => {
            getWalletData({
                oneTimeAccountsWithoutProofOfOwnership: {},
            })
                .map(({ oneTimeAccounts }) => {
                    setState({ connected: true });
                    return oneTimeAccounts[0].address;
                })
                .andThen(sendTx);
        },
        onDisconnect: ({ setState }) => {
            setState({ connected: false });
        },
        onCancel() {
            console.log("Cancel Clicked");
        },
        onDestroy() {
            console.log("Button Destroyed");
        },
    });
</script>

<radix-connect-button />

<style>
    radix-connect-button {
        position: absolute;
        right: 10px
    }
</style>
