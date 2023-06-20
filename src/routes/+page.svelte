<script>
    import AssetList from '../components/assets.svelte'
    import Descriptions from '../components/descriptions.svelte'
    import PaymentMethods from '../components/paymethods.svelte'
</script>
<img src="src/components/assets/logo.svg" alt="Logo" class="logo" />

<h2>Buy Crypto via Unlimit</h2>
<br>
<Descriptions />
<br>
<AssetList />
<br>
<PaymentMethods />
<div class="container">
<p>Visit <a href="https://www.unlimit.com/crypto/">unlimit.com/crypto</a> to learn more</p>
</div>

<style>
    .logo {
    width: 400px; /* adjust as needed */
    height: auto;
    display: block;
    margin: 0 auto; /* center the logo */
    }
    h2 {
    font-family: 'Poppins', sans-serif;
    font-weight: bold;
    text-align: center;
    margin-bottom: 30px;
    }
    :global(body) {
    background-color: #c8f73a;
    }
    .container {
    position: relative;
    min-height: 100vh;
    font-family: 'inter'
    }
    p {
    text-align: center;
    position: absolute;
    bottom: 10%;
    width: 100%;
    
    }
</style>