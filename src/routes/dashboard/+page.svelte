<script>
  import { auth } from "$lib/firebase";
  import { onAuthStateChanged } from "firebase/auth";
  import { goto } from "$app/navigation";

  onAuthStateChanged(auth, (user) => {
    if (!user) goto("/login");
  });
</script>

<h1>Dashboard</h1>
