<script lang="ts">

  import { onMount, onDestroy } from 'svelte';
  import LedgerLivePlatformSDK from "@ledgerhq/live-app-sdk";
  import { WindowMessageTransport } from "@ledgerhq/live-app-sdk";
  import { GateFiEventTypes, GateFiDisplayModeEnum, GateFiSDK } from '@gatefi/js-sdk';

  export let message = "Select an Asset";
  export let assets: string[] = [];

  let selectedAsset = "";  // declare variable to hold selected asset
  $: {
    // if selected asset changes and SDK instance exists, destroy it
    if (selectedAsset !== lastSelectedAsset && gatefiSDKInstance) {
      gatefiSDKInstance.destroy();
      gatefiSDKInstance = null;
    }
  }
  // make sure user changes selected asset before destroying SDK instance
  let lastSelectedAsset = "";

  let llsdk: LedgerLivePlatformSDK;
  let gatefiSDKInstance: GateFiSDK | null;

  //map the drop down select to ledger currency id and unlimit ticker
  let mappings: { [key: string]: { ledger: string, unlimit: string } } = {};



  onMount(async () => {
    llsdk = new LedgerLivePlatformSDK(new WindowMessageTransport());
    llsdk.connect();
    console.log("Connected to Ledger Live SDK");

    // Fetch mappings from your JSON endpoint
    const response = await fetch('https://raw.githubusercontent.com/bryfeng/ledgermapping/master/mapping.json');
    if (response.ok) {
      mappings = await response.json();
      assets = Object.keys(mappings);
    } else {
      console.error('Error fetching mappings:', response.statusText);
    }

  });

  onDestroy(() => {
    if (gatefiSDKInstance) {
      gatefiSDKInstance.destroy();
      gatefiSDKInstance = null;
    }
  });




  async function handleButtonClick() {

    try {
      const ledgerId = mappings[selectedAsset].ledger;

      // if selected asset hasn't changed and SDK instance already exists, just show it
      if (selectedAsset === lastSelectedAsset && gatefiSDKInstance) {
        gatefiSDKInstance.show();
        return;
      }
      lastSelectedAsset = selectedAsset;
      const account = await llsdk.requestAccount({ currencies: [ledgerId] });
      console.log(`Account selected: ${account.address}`);

      const accountAddress = account.address;
      const unlimitTickerCode = mappings[selectedAsset].unlimit;
      // Call your other API with the unlimit code and the account address here

      if (!gatefiSDKInstance) {
        gatefiSDKInstance = new GateFiSDK({
          merchantId: "54c06a2e-57ff-47a8-975d-bfdbc5e51a22",
          displayMode: GateFiDisplayModeEnum.Overlay,
          nodeSelector: "#overlay-button",
          walletAddress: accountAddress,
          defaultFiat: {
              currency: "USD",
              amount: "50",
          },
          defaultCrypto: {
              currency: unlimitTickerCode
          },
        });

        gatefiSDKInstance.subscribe(GateFiEventTypes.onClose, () => {
        gatefiSDKInstance?.destroy();
        gatefiSDKInstance = null;
      });
      }

      
      
      gatefiSDKInstance.show();
    } catch (error) {
      console.error(`Error getting account: ${error}`);
    }
  }

  </script>

  <div class= "items">
  
  <p>{message}</p>
  
  <select bind:value={selectedAsset} name="asset">
    <option value=""></option>
    {#each assets as asset}
      <option value={asset}>{asset}</option>
    {/each}>
  </select>
  <br>
  <br>

  <button class = "buybutton" id="overlay-button" on:click={handleButtonClick}>Buy</button>
  </div>

  <style>
    .items{
      font-family: 'inter', sans-serif;
      font-weight: bold;
      text-align: center;
    }
    .buybutton{
      background-color: black;
      color: white;
      border: none;
      padding: 10px 20px;
      text-align: center;
      text-decoration: none;
      display: inline-block;
      font-family: 'inter', sans-serif;
      font-weight: bold;
      font-size: 16px;
      border-radius: 12px;
      transition: background-color 0.3s;
    }
    .buybutton:hover {
      background-color: grey; /* optional, for hover effect */
    }
    

  </style>