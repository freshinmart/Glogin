<html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <title>Login Google + Firestore (Firebase v12)</title>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 100vh;
      background: #f8fafc;
    }
    button {
      background: #4285F4;
      color: white;
      border: none;
      padding: 10px 20px;
      border-radius: 6px;
      cursor: pointer;
      font-size: 16px;
      margin: 6px;
    }
    button:hover { background: #3367D6; }
    #userInfo { margin-top: 20px; text-align: center; }
    img { border-radius: 50%; width: 80px; margin-top: 10px; }
  </style>
</head>
<body>
  <h2>Login dengan Google (Firebase v12)</h2>
  <button id="loginBtn">Login Google</button>
  <button id="logoutBtn" style="display:none;">Logout</button>
  <div id="userInfo"></div>

  <script type="module">
    // Import modular Firebase SDKs
    import { initializeApp } from "https://www.gstatic.com/firebasejs/12.4.0/firebase-app.js";
    import { getFirestore, doc, setDoc } from "https://www.gstatic.com/firebasejs/12.4.0/firebase-firestore.js";
    import { getAuth, GoogleAuthProvider, signInWithPopup, onAuthStateChanged, signOut } from "https://www.gstatic.com/firebasejs/12.4.0/firebase-auth.js";

    // Konfigurasi Firebase kamu
    const firebaseConfig = {
      apiKey: "AIzaSyAhGrGR_-mopGtdJLQD7zX3_OOQIjALctI",
      authDomain: "freshin-proj.firebaseapp.com",
      projectId: "freshin-proj",
      storageBucket: "freshin-proj.firebasestorage.app",
      messagingSenderId: "329745365163",
      appId: "1:329745365163:web:ce106fce79b8773ae71fee",
      measurementId: "G-02JNHP6WVT"
    };

    // Inisialisasi Firebase
    const app = initializeApp(firebaseConfig);
    const db = getFirestore(app);
    const auth = getAuth(app);

    const loginBtn = document.getElementById("loginBtn");
    const logoutBtn = document.getElementById("logoutBtn");
    const userInfo = document.getElementById("userInfo");

    // Fungsi login dengan Google
    loginBtn.addEventListener("click", async () => {
      const provider = new GoogleAuthProvider();
      try {
        const result = await signInWithPopup(auth, provider);
        const user = result.user;

        // Simpan data user ke Firestore
        await setDoc(doc(db, "users", user.uid), {
          nama: user.displayName,
          email: user.email,
          foto: user.photoURL,
          terakhirLogin: new Date()
        });

        console.log("Login berhasil:", user.displayName);
      } catch (error) {
        alert("Login gagal: " + error.message);
      }
    });

    // Fungsi logout
    logoutBtn.addEventListener("click", async () => {
      await signOut(auth);
      alert("Berhasil logout");
    });

    // Pantau status login
    onAuthStateChanged(auth, (user) => {
      if (user) {
        loginBtn.style.display = "none";
        logoutBtn.style.display = "block";
        userInfo.innerHTML = `
          <h3>Halo, ${user.displayName}</h3>
          <p>${user.email}</p>
          <img src="${user.photoURL}" alt="Foto Profil">
        `;
      } else {
        loginBtn.style.display = "block";
        logoutBtn.style.display = "none";
        userInfo.innerHTML = "";
      }
    });
  </script>
</body>
</html>
