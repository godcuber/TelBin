<script>
  import { signInWithEmailAndPassword } from "firebase/auth";
  import { goto } from "$app/navigation";
  import { auth } from "$lib/firebase";

  let email = "";
  let password = "";
  let loading = false;

  async function login() {
    if (!auth) return alert("Firebase not ready");

    loading = true;
    try {
      await signInWithEmailAndPassword(auth, username, password);
      goto("/dashboard");
    } catch (e) {
      alert(e.message);
    }
    loading = false;
  }
</script>

<div class="container">
  <img src="/campus.png" alt="Campus" class="hero" />

  <h1 class="title">TeLBin</h1>
  <p class="subtitle">
    Please log in using your registered IGracias account to access this application.
  </p>

  <div class="form">
    <label for="email">Email</label>
    <input id="email" type="email" bind:value={email} />

    <label for="password">Password</label>
    <input id="password" type="password" bind:value={password} />

    <button on:click={login} disabled={loading}>
      {loading ? "Logging in..." : "Login"}
    </button>
  </div>
</div>

<style>
  .container {
    min-height: 100vh;
    background: white;
    text-align: center;
    display: flex;
    flex-direction: column;
    align-items: center;

    width: 100%;
    max-width: 412px;
    margin: 0 auto;
    }


  .hero {
    width: 100%;
    height: 280px;
    margin-down: -30px;
    object-fit: contain;
    border-bottom-left-radius: 60px;
  }

  .title {
    margin-top: 20px;
    font-size: 36px;
    font-weight: bold;
    color: #0b6b63;
  }

  .subtitle {
    font-size: 13px;
    margin: 10px 20px 30px;
    color: #333;
  }

  .form {
    width: 100%;
    max-width: 340px;
  }

  label {
    font-size: 13px;
    font-weight: 600;
    margin-top: 14px;
    display: block;
    text-align: left;
  }

  input {
    width: 100%;
    padding: 12px;
    border-radius: 10px;
    border: 1px solid #000;
    margin-top: 6px;
    font-size: 14px;
  }

  button {
    margin-top: 28px;
    width: 100%;
    padding: 12px;
    background: #0b6b63;
    color: white;
    border-radius: 10px;
    font-weight: bold;
    border: none;
    font-size: 15px;
  }
  html, body {
    margin: 0;
    padding: 0;
    width: 100%;
    overflow-x: hidden;
    }

</style>
