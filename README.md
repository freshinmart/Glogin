<html>
<html lang="id">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Login Google + Firestore (Firebase v12) - Redirect</title>
  <style>
    body {
      font-family: 'Segoe UI', Arial, sans-serif;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      min-height: 100vh;
      margin: 0;
      background: #f8fafc;
      color: #111827;
      padding: 20px;
    }
    .card {
      background: white;
      padding: 24px;
      border-radius: 12px;
      box-shadow: 0 8px 24px rgba(0,0,0,0.08);
      min-width: 280px;
      max-width: 420px;
      width: 100%;
      text-align: center;
    }
    button {
      background: #4285F4;
      color: white;
      border: none;
      padding: 10px 18px;
      border-radius: 8px;
      cursor: pointer;
      font-size: 16px;
      margin: 8px;
    }
    button.secondary {
      background: #e5e7eb;
      color: #111827;
    }
    img { border-radius: 50%; width: 80px; margin-top: 10px; }
    .hint { font-size: 13px; color: #6b7280; margin-top: 8px; }
    .error { color: #b91c1c; margin-top: 12px; font-weight: 600; }
    a.small { font-size: 13px; color: #2563eb; text-decoration: none; display:block; margin-top:6px;}
  </style>
</head>
<body>
  <div class="card">
    <h2>Login dengan Google</h2>
    <div id="controls">
      <button id="loginBtn">Login Google</button>
      <button id="logoutBtn" class="secondary" style="display:none;">Logout</button>
    </div>
    <div id="userInfo"></div>
    <div id="status" class="hint"></div>
    <div id="err" class="error" style="display:none;"></div>
    <a class="small" href="#" id="runLocalHelp">Cara jalankan lokal / hosting</a>
  </div>

  <script type="module">
    // ---------- Konfigurasi Firebase (Ganti jika perlu) ----------
    import { initializeApp } from "https://www.gstatic.com/firebasejs/12.4.0/firebase-app.js";
    import {
      getFirestore, doc, setDoc
    } from "https://www.gstatic.com/firebasejs/12.4.0/firebase-firestore.js";
    import {
      getAuth, GoogleAuthProvider,
      signInWithRedirect, getRedirectResult,
      onAuthStateChanged, signOut
    } from "https://www.gstatic.com/firebasejs/12.4.0/firebase-auth.js";

    const firebaseConfig = {
      apiKey: "AIzaSyAhGrGR_-mopGtdJLQD7zX3_OOQIjALctI",
      authDomain: "freshin-proj.firebaseapp.com",
      projectId: "freshin-proj",
      storageBucket: "freshin-proj.firebasestorage.app",
      messagingSenderId: "329745365163",
      appId: "1:329745365163:web:ce106fce79b8773ae71fee",
      measurementId: "G-02JNHP6WVT"
    };

    // ---------- Inisialisasi ----------
    const app = initializeApp(firebaseConfig);
    const db = getFirestore(app);
    const auth = getAuth(app);
    const provider = new GoogleAuthProvider();

    // ---------- Elemen UI ----------
    const loginBtn = document.getElementById('loginBtn');
    const logoutBtn = document.getElementById('logoutBtn');
    const userInfo = document.getElementById('userInfo');
    const statusEl = document.getElementById('status');
    const errEl = document.getElementById('err');
    const runLocalHelp = document.getElementById('runLocalHelp');

    // ---------- Utility: Tampilkan pesan error ----------
    function showError(msg) {
      console.error(msg);
      errEl.style.display = 'block';
      errEl.textContent = msg;
    }
    function clearError() {
      errEl.style.display = 'none';
      errEl.textContent = '';
    }

    // ---------- Deteksi environment (file:// atau content://) ----------
    const isFileProtocol = location.protocol === 'file:';
    const isContentProtocol = location.protocol === 'content:';
    if (isFileProtocol || isContentProtocol) {
      statusEl.textContent = 'Diperingatkan: halaman dibuka dari file lokal / content URI. Google Sign-In via popup/redirect biasanya tidak didukung. Jalankan lewat localhost atau hosting.';
    }

    // ---------- Tautan bantuan menjalankan lokal/hosting ----------
    runLocalHelp.addEventListener('click', (e) => {
      e.preventDefault();
      alert('Jalankan lokal: gunakan VSCode Live Server atau jalankan simple http server.\n\nContoh (Node):\n1) npm i -g http-server\n2) http-server ./ -p 8080\n\nKemudian buka: http://localhost:8080');
    });

    // ---------- Login (redirect) ----------
    loginBtn.addEventListener('click', async () => {
      clearError();
      // Jika dibuka dari file:// maka batalkan dan beri tahu
      if (isFileProtocol || isContentProtocol) {
        showError('Tidak bisa melakukan Google Sign-In dari file lokal / content URI. Jalankan lewat http://localhost atau deploy ke hosting.');
        return;
      }
      try {
        // Mulai redirect ke Google
        await signInWithRedirect(auth, provider);
        // Browser akan berpindah; setelah kembali getRedirectResult akan dipanggil otomatis di bagian bawah
      } catch (error) {
        showError('Gagal memulai redirect: ' + (error.message || error.code));
      }
    });

    // ---------- Logout ----------
    logoutBtn.addEventListener('click', async () => {
      try {
        await signOut(auth);
        alert('Logout berhasil');
      } catch (err) {
        showError('Gagal logout: ' + (err.message || err.code));
      }
    });

    // ---------- Saat aplikasi menerima hasil redirect (setelah login lewat Google) ----------
    // getRedirectResult harus dipanggil setelah inisialisasi auth
    try {
      const redirectResult = await getRedirectResult(auth);
      // Jika pengguna baru berhasil login via redirect, redirectResult.user akan tersedia
      if (redirectResult && redirectResult.user) {
        const user = redirectResult.user;
        // Simpan/ update data user di Firestore
        try {
          await setDoc(doc(db, 'users', user.uid), {
            nama: user.displayName,
            email: user.email,
            foto: user.photoURL,
            terakhirLogin: new Date()
          }, { merge: true });
          console.log('User saved via redirect:', user.uid);
        } catch (dbErr) {
          console.error('Gagal simpan user:', dbErr);
        }
      }
    } catch (grErr) {
      // Menangkap error dari getRedirectResult (mis. dibatalkan, popup blocked, dll.)
      // Beberapa error 'auth/cancelled-popup-request' bisa muncul jika ada popup lain.
      console.warn('getRedirectResult warning:', grErr);
      // Tidak menampilkan secara agresif, cukup log. Jika ingin tampilkan:
      // showError('Login gagal: ' + (grErr.message || grErr.code));
    }

    // ---------- Pantau status autentikasi real-time ----------
    onAuthStateChanged(auth, async (user) => {
      clearError();
      if (user) {
        // tampilkan info user
        loginBtn.style.display = 'none';
        logoutBtn.style.display = 'inline-block';
        userInfo.innerHTML = `
          <h3>Halo, ${user.displayName}</h3>
          <p>${user.email}</p>
          <img src="${user.photoURL}" alt="Foto Profil">
        `;
        statusEl.textContent = 'Anda sudah login. Data tersimpan di Firestore (collection: users).';
        // Pastikan data user tersimpan (onAuthStateChanged berguna saat reload)
        try {
          await setDoc(doc(db, 'users', user.uid), {
            nama: user.displayName,
            email: user.email,
            foto: user.photoURL,
            terakhirLogin: new Date()
          }, { merge: true });
        } catch (dbErr) {
          console.error('Gagal menyimpan user onAuthStateChanged:', dbErr);
        }
      } else {
        // belum login
        loginBtn.style.display = 'inline-block';
        logoutBtn.style.display = 'none';
        userInfo.innerHTML = '';
        statusEl.textContent = 'Belum login';
      }
    });
  </script>
</body>
</html>
