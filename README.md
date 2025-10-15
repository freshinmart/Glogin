<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Fresh.in - Belanja Kebutuhan Sehari-hari</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://cdn.jsdelivr.net/npm/tesseract.js@4/dist/tesseract.min.js"></script>
    <script src="https://html2canvas.hertzen.com/dist/html2canvas.min.js"></script>
    
    <!-- Firebase App (the core Firebase SDK) -->
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
    <!-- Firebase Authentication -->
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-auth.js"></script>
    <!-- Cloud Firestore -->
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-firestore.js"></script>
    
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;500;600;700&family=Montserrat:wght@700;800;900&display=swap" rel="stylesheet">
    <style>
        /* CSS yang sudah ada tetap dipertahankan */
        /* Saya akan menambahkan beberapa style untuk login dengan Google */
        
        .google-login-btn {
            background: white;
            color: #333;
            border: 1px solid #ddd;
            padding: 12px 20px;
            border-radius: var(--border-radius-sm);
            font-size: 14px;
            cursor: pointer;
            transition: var(--transition);
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
            width: 100%;
            margin-top: 15px;
        }

        .google-login-btn:hover {
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
        }

        .google-icon {
            width: 18px;
            height: 18px;
        }

        .login-divider {
            text-align: center;
            margin: 20px 0;
            position: relative;
            color: var(--gray);
        }

        .login-divider::before {
            content: '';
            position: absolute;
            top: 50%;
            left: 0;
            right: 0;
            height: 1px;
            background: #eee;
        }

        .login-divider span {
            background: white;
            padding: 0 15px;
            position: relative;
        }

        /* User Profile in Header */
        .user-profile-header {
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .user-avatar {
            width: 36px;
            height: 36px;
            border-radius: 50%;
            object-fit: cover;
            border: 2px solid var(--primary-light);
        }

        .user-name {
            font-size: 14px;
            font-weight: 500;
            color: var(--dark);
        }

        /* Loading untuk sinkronisasi data */
        .sync-loading {
            display: none;
            text-align: center;
            padding: 20px;
        }

        .sync-loading .loading-spinner {
            border: 3px solid #f3f3f3;
            border-top: 3px solid var(--primary);
            border-radius: 50%;
            width: 30px;
            height: 30px;
            animation: spin 1s linear infinite;
            margin: 0 auto 10px;
        }
    </style>
</head>
<body>
    <!-- Header (akan disembunyikan di halaman tertentu) -->
    <header id="main-header">
        <div class="header-container">
            <div class="header-left">
                <button class="mobile-menu-btn" id="mobile-menu-btn">
                    <i class="fas fa-bars"></i>
                </button>
                <div class="logo">
                    <h1><span class="fresh">Fresh</span><span class="dot-in">.in</span></h1>
                </div>
            </div>
            
            <div class="header-center">
                <div class="search-bar">
                    <input type="text" placeholder="Cari produk kebutuhan sehari-hari..." id="search-input">
                    <button id="search-btn"><i class="fas fa-search"></i> Cari</button>
                </div>
            </div>
            
            <div class="header-right">
                <!-- User Profile (ditampilkan jika sudah login) -->
                <div class="user-profile-header" id="user-profile-header" style="display: none;">
                    <img id="user-avatar" src="" alt="User Avatar" class="user-avatar">
                    <span class="user-name" id="user-name-header"></span>
                </div>
                
                <!-- Tombol Masuk - hanya ditampilkan jika belum login -->
                <button class="login-btn" id="login-header-btn">Masuk</button>
                
                <!-- Wishlist dan Cart - hanya ditampilkan jika sudah login -->
                <a href="#" class="icon-link" id="wishlist-link" style="display: none;">
                    <i class="far fa-heart"></i>
                </a>
                <a href="#" class="icon-link" id="cart-link" style="display: none;">
                    <i class="fas fa-shopping-cart"></i>
                </a>
            </div>
        </div>
    </header>

    <!-- Swipe Cart -->
    <div class="swipe-cart-container" id="swipe-cart">
        <div class="swipe-handle" id="swipe-handle">
            <i class="fas fa-shopping-cart"></i>
        </div>
        <div class="swipe-cart-header">
            <h3 class="swipe-cart-title">Keranjang Belanja</h3>
            <button class="swipe-cart-close" id="swipe-cart-close">&times;</button>
        </div>
        <div class="swipe-cart-content" id="swipe-cart-content">
            <!-- Cart items will be populated by JavaScript -->
        </div>
        <div class="swipe-cart-summary">
            <div class="summary-item">
                <span>Subtotal</span>
                <span id="swipe-cart-subtotal">Rp 0</span>
            </div>
            <div class="summary-item">
                <span>Pengiriman</span>
                <span id="swipe-cart-shipping">Rp 5,000</span>
            </div>
            <div class="summary-total">
                <span>Total</span>
                <span id="swipe-cart-total">Rp 5,000</span>
            </div>
            <button class="checkout-btn" id="swipe-checkout-btn">Lanjut ke Pembayaran</button>
        </div>
    </div>

    <!-- Bottom Navigation -->
    <nav class="bottom-nav">
        <a href="#" class="nav-item active" data-page="home">
            <i class="fas fa-home"></i>
            <span class="nav-label">Beranda</span>
        </a>
        <a href="#" class="nav-item" data-page="promo">
            <i class="fas fa-percentage"></i>
            <span class="nav-label">Promosi</span>
        </a>
        <a href="#" class="nav-item" data-page="cart">
            <i class="fas fa-shopping-cart"></i>
            <span class="nav-label">Keranjang</span>
        </a>
        <a href="#" class="nav-item" data-page="transactions">
            <i class="fas fa-shopping-bag"></i>
            <span class="nav-label">Transaksi</span>
        </a>
        <a href="#" class="nav-item" data-page="profile">
            <i class="fas fa-user"></i>
            <span class="nav-label">Profil</span>
        </a>
    </nav>

    <!-- Mobile Menu -->
    <div class="mobile-menu" id="mobile-menu">
        <div class="mobile-menu-header">
            <h3>Menu</h3>
            <button class="mobile-menu-close" id="mobile-menu-close">&times;</button>
        </div>
        <div class="mobile-menu-categories">
            <!-- Kategori dihapus dari mobile menu -->
            <div class="mobile-menu-category">
                <h4 class="mobile-menu-category-title">Akun Saya</h4>
                <ul class="mobile-menu-links">
                    <li><a href="#" data-page="profile"><i class="fas fa-user"></i> Profil</a></li>
                    <li><a href="#" data-page="transactions"><i class="fas fa-shopping-bag"></i> Transaksi</a></li>
                    <li><a href="#" data-page="wishlist"><i class="fas fa-heart"></i> Wishlist</a></li>
                    <li><a href="#" data-page="history"><i class="fas fa-history"></i> Riwayat</a></li>
                    <li><a href="#" id="logout-link"><i class="fas fa-sign-out-alt"></i> Logout</a></li>
                    <!-- Admin link akan ditambahkan oleh JavaScript jika user adalah admin -->
                </ul>
            </div>
            <div class="mobile-menu-category">
                <h4 class="mobile-menu-category-title">Lainnya</h4>
                <ul class="mobile-menu-links">
                    <li><a href="#" data-page="about"><i class="fas fa-info-circle"></i> Tentang Kami</a></li>
                    <li><a href="#" data-page="help"><i class="fas fa-headset"></i> Bantuan</a></li>
                    <li><a href="#" data-page="privacy"><i class="fas fa-shield-alt"></i> Kebijakan Privasi</a></li>
                    <li><a href="#" data-page="terms"><i class="fas fa-file-contract"></i> Syarat & Ketentuan</a></li>
                </ul>
            </div>
        </div>
    </div>

    <!-- Kategori hanya ditampilkan di halaman home -->
    <div class="nav-categories">
        <div class="category-scroll">
            <a href="#" class="category-item" data-category="buah">
                <div class="category-icon">
                    <i class="fas fa-apple-alt"></i>
                </div>
                <span class="category-name">Buah</span>
            </a>
            <a href="#" class="category-item" data-category="sayur">
                <div class="category-icon">
                    <i class="fas fa-carrot"></i>
                </div>
                <span class="category-name">Sayur</span>
            </a>
            <a href="#" class="category-item" data-category="protein">
                <div class="category-icon">
                    <i class="fas fa-egg"></i>
                </div>
                <span class="category-name">Protein</span>
            </a>
            <a href="#" class="category-item" data-category="minuman">
                <div class="category-icon">
                    <i class="fas fa-wine-bottle"></i>
                </div>
                <span class="category-name">Minuman</span>
            </a>
            <a href="#" class="category-item" data-category="roti">
                <div class="category-icon">
                    <i class="fas fa-bread-slice"></i>
                </div>
                <span class="category-name">Roti</span>
            </a>
            <a href="#" class="category-item" data-category="beku">
                <div class="category-icon">
                    <i class="fas fa-ice-cream"></i>
                </div>
                <span class="category-name">Beku</span>
            </a>
            <a href="#" class="category-item" data-category="snack">
                <div class="category-icon">
                    <i class="fas fa-pizza-slice"></i>
                </div>
                <span class="category-name">Snack</span>
            </a>
            <a href="#" class="category-item" data-category="kesehatan">
                <div class="category-icon">
                    <i class="fas fa-pump-medical"></i>
                </div>
                <span class="category-name">Kesehatan</span>
            </a>
        </div>
    </div>

    <!-- Home Page -->
    <div id="home-page" class="page active">
        <div class="container">
            <!-- Hero Carousel -->
            <div class="hero-carousel">
                <div class="carousel-container">
                    <div class="carousel-slides" id="carousel-slides">
                        <!-- Carousel slides will be populated by JavaScript -->
                    </div>
                    <div class="carousel-controls">
                        <button class="carousel-control prev" id="carousel-prev">&#10094;</button>
                        <button class="carousel-control next" id="carousel-next">&#10095;</button>
                    </div>
                    <div class="carousel-dots" id="carousel-dots">
                        <!-- Dots will be populated by JavaScript -->
                    </div>
                </div>
            </div>

            <!-- Search Results Section (Initially Hidden) -->
            <section class="search-results-section" id="search-results">
                <div class="section-header">
                    <h2 class="section-title">Hasil Pencarian</h2>
                </div>
                <div class="products-grid" id="search-results-grid">
                    <!-- Search results will be populated by JavaScript -->
                </div>
            </section>

            <section class="products-section" id="all-products-section">
                <div class="section-header">
                    <h2 class="section-title">Semua Produk</h2>
                    <a href="#" class="view-all" id="view-all-products">Lihat Semua <i class="fas fa-chevron-right"></i></a>
                </div>
                <div class="products-grid" id="all-products">
                    <!-- All products will be populated by JavaScript -->
                </div>
            </section>
        </div>
    </div>

    <!-- Loading untuk sinkronisasi data -->
    <div class="sync-loading" id="sync-loading">
        <div class="loading-spinner"></div>
        <p>Menyinkronkan data...</p>
    </div>

    <!-- Login/Signup Modal -->
    <div class="modal" id="login-modal">
        <div class="modal-content" style="max-width: 450px;">
            <div class="modal-header">
                <h3>Masuk atau Daftar</h3>
                <button class="modal-close" id="close-login">&times;</button>
            </div>
            <div class="modal-body">
                <div class="login-tabs">
                    <button class="login-tab active" data-tab="login">Masuk</button>
                    <button class="login-tab" data-tab="signup">Daftar</button>
                </div>
                
                <form id="login-form" class="login-form active">
                    <div class="form-group">
                        <label for="login-email">Email</label>
                        <input type="email" id="login-email" class="form-control" required>
                    </div>
                    <div class="form-group">
                        <label for="login-password">Password</label>
                        <input type="password" id="login-password" class="form-control" required>
                    </div>
                    <button type="submit" class="btn btn-primary" style="width: 100%; margin-top: 15px;">Masuk</button>
                </form>
                
                <form id="signup-form" class="login-form">
                    <div class="form-group">
                        <label for="signup-name">Nama Lengkap</label>
                        <input type="text" id="signup-name" class="form-control" required>
                    </div>
                    <div class="form-group">
                        <label for="signup-email">Email</label>
                        <input type="email" id="signup-email" class="form-control" required>
                    </div>
                    <div class="form-group">
                        <label for="signup-password">Password</label>
                        <input type="password" id="signup-password" class="form-control" required>
                    </div>
                    <div class="form-group">
                        <label for="signup-phone">Nomor Telepon</label>
                        <input type="tel" id="signup-phone" class="form-control" required>
                    </div>
                    <button type="submit" class="btn btn-primary" style="width: 100%; margin-top: 15px;">Daftar</button>
                </form>

                <!-- Login dengan Google -->
                <div class="login-divider">
                    <span>Atau</span>
                </div>
                <button class="google-login-btn" id="google-login-btn">
                    <img src="https://developers.google.com/identity/images/g-logo.png" alt="Google" class="google-icon">
                    Login dengan Google
                </button>
            </div>
        </div>
    </div>

    <!-- Sisa kode HTML tetap sama (checkout modal, QRIS modal, dll) -->

    <script>
        // Konfigurasi Firebase
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
        firebase.initializeApp(firebaseConfig);
        const auth = firebase.auth();
        const db = firebase.firestore();

        // Product data
        let products = [
            { id: 1, name: "ALBA PASTILES 100 GR", price: 10500, category: "snack", image: "https://via.placeholder.com/200x200/FF5733/FFFFFF?text=ALBA", originalPrice: 12500 },
            { id: 2, name: "ALPENLIEBE SAC KARAMEL", price: 6750, category: "snack", image: "https://via.placeholder.com/200x200/FF5733/FFFFFF?text=ALPENLIEBE" },
            { id: 3, name: "ANLENE VANILA SCH 20 GR", price: 76500, category: "kesehatan", image: "https://via.placeholder.com/200x200/4FC3F7/FFFFFF?text=ANLENE" },
            // ... data produk lainnya
        ];

        // Carousel data
        let carouselItems = [
            { 
                id: 1, 
                image: "https://images.unsplash.com/photo-1542838132-92c53300491e?ixlib=rb-1.2.1&auto=format&fit=crop&w=800&q=80",
                title: "Belanja Lebih Mudah & Cepat",
                description: "Gratis ongkir untuk pembelian di atas Rp100.000"
            },
            // ... data carousel lainnya
        ];

        // App state
        let cart = [];
        let wishlist = [];
        let currentUser = null;
        let currentCarouselIndex = 0;
        let carouselInterval;
        let selectedPaymentMethod = '';
        let currentTransactionAmount = 0;
        let proofUploaded = false;
        let ocrValidationPassed = false;
        let saveAddress = false;
        let isAdmin = false;
        let currentOrderId = '';
        let pageHistory = ['home'];

        // QRIS Static Data
        const QRIS_STATIC_DATA = "00020101021126610014COM.GO-JEK.WWW01189360091430687178460210G0687178460303UMI51440014ID.CO.QRIS.WWW0215ID10243600706340303UMI5204541153033605802ID5919Warung 68, SAWANGAN6005DEPOK61051651962070703A0163046826";

        // Status keywords for OCR validation
        const failedStatusKeywords = [
            'diproses', 'invalid', 'menunggu', 'pending', 'pin salah', 'menunggu pembayaran', 
            'saldo tidak cukup', 'koneksi terputus', 'jaringan', 'dibatalkan', 'expired', 
            'waktu habis', 'ditolak', 'declined', 'tidak berhasil', 'transaksi gagal', 'gagal',
            'processing', 'waiting', 'wrong pin', 'waiting for payment', 'insufficient balance', 
            'connection lost', 'network', 'cancelled', 'timed out', 'rejected', 'declined', 
            'unsuccessful', 'transaction failed', 'failed'
        ];
        
        const successStatusKeywords = [
            'sukses', 'berhasil', 'terkirim', 'selesai', 'lunas', 'tuntas', 'terbayar', 
            'approved', 'completed', 'pembayaran diterima', 'transaksi dikonfirmasi', 
            'dana telah diteruskan', 'success', 'sent', 'completed', 'paid', 'approved', 
            'completed', 'payment received', 'transaction confirmed', 'funds disbursed'
        ];

        // DOM Elements
        const cartCountElement = document.querySelector('.cart-count');
        const wishlistCountElement = document.querySelector('.wishlist-count');
        const allProductsGrid = document.getElementById('all-products');
        const searchResultsGrid = document.getElementById('search-results-grid');
        const searchResultsSection = document.getElementById('search-results');
        const wishlistGrid = document.getElementById('wishlist-products');
        const checkoutModal = document.getElementById('checkout-modal');
        const closeCheckoutBtn = document.getElementById('close-checkout');
        const cartLink = document.getElementById('cart-link');
        const wishlistLink = document.getElementById('wishlist-link');
        const mobileMenuBtn = document.getElementById('mobile-menu-btn');
        const mobileMenu = document.getElementById('mobile-menu');
        const mobileMenuClose = document.getElementById('mobile-menu-close');
        const searchInput = document.getElementById('search-input');
        const searchBtn = document.getElementById('search-btn');
        const header = document.getElementById('main-header');
        const pages = document.querySelectorAll('.page');
        const carouselSlides = document.getElementById('carousel-slides');
        const carouselDots = document.getElementById('carousel-dots');
        const carouselPrev = document.getElementById('carousel-prev');
        const carouselNext = document.getElementById('carousel-next');
        const loginModal = document.getElementById('login-modal');
        const closeLoginBtn = document.getElementById('close-login');
        const loginTabs = document.querySelectorAll('.login-tab');
        const loginForms = document.querySelectorAll('.login-form');
        const loginForm = document.getElementById('login-form');
        const signupForm = document.getElementById('signup-form');
        const toPaymentButton = document.getElementById('to-payment-button');
        const payButton = document.getElementById('pay-button');
        const qrisModal = document.getElementById('qris-modal');
        const closeQrisBtn = document.getElementById('close-qris');
        const qrisMerchant = document.getElementById('qris-merchant');
        const qrisAmount = document.getElementById('qris-amount');
        const qrisImage = document.getElementById('qris-image');
        const qrisLoading = document.getElementById('qris-loading');
        const confirmPaymentButton = document.getElementById('confirm-payment-button');
        const confirmationModal = document.getElementById('confirmation-modal');
        const closeConfirmationBtn = document.getElementById('close-confirmation');
        const proofDropArea = document.getElementById('proof-drop-area');
        const proofInput = document.getElementById('proof-input');
        const proofPreview = document.getElementById('proof-preview');
        const proofImage = document.getElementById('proof-image');
        const uploadLoading = document.getElementById('upload-loading');
        const submitProofButton = document.getElementById('submit-proof-button');
        const statusModal = document.getElementById('status-modal');
        const closeStatusBtn = document.getElementById('close-status');
        const viewTransactionsButton = document.getElementById('view-transactions-button');
        const closeStatusButton = document.getElementById('close-status-button');
        const orderId = document.getElementById('order-id');
        const statusOrderId = document.getElementById('status-order-id');
        const validationMessage = document.getElementById('validation-message');
        const tabButtons = document.querySelectorAll('.tab-button');
        const paymentMethods = document.querySelectorAll('.payment-method');
        const notification = document.getElementById('notification');
        const profileForm = document.getElementById('profile-form');
        const changePhotoBtn = document.getElementById('change-photo-btn');
        const profileImageInput = document.getElementById('profile-image-input');
        const profileImage = document.getElementById('profile-image');
        const profileName = document.getElementById('profile-name');
        const profileEmail = document.getElementById('profile-email');
        const saveAddressCheckbox = document.getElementById('save-address-checkbox');
        const adminTransactionsList = document.getElementById('admin-transactions-list');
        const logoutLink = document.getElementById('logout-link');
        const swipeCart = document.getElementById('swipe-cart');
        const swipeHandle = document.getElementById('swipe-handle');
        const swipeCartClose = document.getElementById('swipe-cart-close');
        const swipeCheckoutBtn = document.getElementById('swipe-checkout-btn');
        const swipeCartContent = document.getElementById('swipe-cart-content');
        const loginHeaderBtn = document.getElementById('login-header-btn');
        const checkoutProductsList = document.getElementById('checkout-products-list');
        const voucherCodeInput = document.getElementById('voucher-code');
        const applyVoucherBtn = document.getElementById('apply-voucher');
        const orderNotesTextarea = document.getElementById('order-notes');
        const backFromPromo = document.getElementById('back-from-promo');
        const backFromCart = document.getElementById('back-from-cart');
        const backFromTransactions = document.getElementById('back-from-transactions');
        const backFromProfile = document.getElementById('back-from-profile');
        const googleLoginBtn = document.getElementById('google-login-btn');
        const userProfileHeader = document.getElementById('user-profile-header');
        const userNameHeader = document.getElementById('user-name-header');
        const userAvatar = document.getElementById('user-avatar');
        const syncLoading = document.getElementById('sync-loading');

        // Initialize from localStorage
        function initializeApp() {
            if (localStorage.getItem('cart')) {
                cart = JSON.parse(localStorage.getItem('cart'));
                updateCartCount();
            }

            if (localStorage.getItem('wishlist')) {
                wishlist = JSON.parse(localStorage.getItem('wishlist'));
                updateWishlistCount();
            }

            if (localStorage.getItem('carouselItems')) {
                carouselItems = JSON.parse(localStorage.getItem('carouselItems'));
            }

            if (localStorage.getItem('products')) {
                products = JSON.parse(localStorage.getItem('products'));
            }

            renderProducts();
            renderCarousel();
            setupEventListeners();
            setupScrollBehavior();
            startCarousel();
            updateUserUI();
            
            // PASTIKAN HEADER DAN KATEGORI DITAMPILKAN SAAT INISIALISASI (HOME PAGE)
            document.body.classList.remove('hide-header');
        }

        // Update user UI based on login status
        function updateUserUI() {
            if (currentUser) {
                // Jika sudah login, sembunyikan tombol masuk dan tampilkan wishlist & cart
                if (loginHeaderBtn) loginHeaderBtn.style.display = 'none';
                if (wishlistLink) wishlistLink.style.display = 'flex';
                if (cartLink) cartLink.style.display = 'flex';
                
                // Tampilkan profil pengguna di header
                if (userProfileHeader) userProfileHeader.style.display = 'flex';
                if (userNameHeader) userNameHeader.textContent = currentUser.displayName || currentUser.email.split('@')[0];
                if (userAvatar) userAvatar.src = currentUser.photoURL || 'https://via.placeholder.com/36x36/cccccc/999999?text=User';
                
                // Tambahkan link logout di mobile menu jika user sudah login
                if (logoutLink) {
                    logoutLink.style.display = 'block';
                }
            } else {
                // Jika belum login, tampilkan tombol masuk dan sembunyikan wishlist & cart
                if (loginHeaderBtn) loginHeaderBtn.style.display = 'block';
                if (wishlistLink) wishlistLink.style.display = 'none';
                if (cartLink) cartLink.style.display = 'none';
                
                // Sembunyikan profil pengguna di header
                if (userProfileHeader) userProfileHeader.style.display = 'none';
                
                if (logoutLink) {
                    logoutLink.style.display = 'none';
                }
            }
        }

        // Update cart count display (HILANGKAN BACKGROUND DAN ANGKA)
        function updateCartCount() {
            const totalItems = cart.reduce((total, item) => total + item.quantity, 0);
            // JANGAN update cartCountElement karena kita ingin menyembunyikannya
            localStorage.setItem('cart', JSON.stringify(cart));
            
            // Simpan ke Firebase jika user sudah login
            if (currentUser) {
                saveUserDataToFirebase();
            }
        }

        // Update wishlist count display (HILANGKAN BACKGROUND DAN ANGKA)
        function updateWishlistCount() {
            // JANGAN update wishlistCountElement karena kita ingin menyembunyikannya
            localStorage.setItem('wishlist', JSON.stringify(wishlist));
            
            // Simpan ke Firebase jika user sudah login
            if (currentUser) {
                saveUserDataToFirebase();
            }
        }

        // Save user data to Firebase
        function saveUserDataToFirebase() {
            if (!currentUser) return;
            
            const userData = {
                uid: currentUser.uid,
                email: currentUser.email,
                displayName: currentUser.displayName,
                photoURL: currentUser.photoURL,
                cart: cart,
                wishlist: wishlist,
                lastUpdated: firebase.firestore.FieldValue.serverTimestamp()
            };
            
            // Simpan data profil jika ada
            if (currentUser.name || currentUser.phone || currentUser.address) {
                userData.profile = {
                    name: currentUser.name,
                    phone: currentUser.phone,
                    address: currentUser.address,
                    province: currentUser.province,
                    city: currentUser.city,
                    postal: currentUser.postal
                };
            }
            
            db.collection('users').doc(currentUser.uid).set(userData, { merge: true })
                .then(() => {
                    console.log('Data pengguna berhasil disimpan ke Firebase');
                })
                .catch((error) => {
                    console.error('Error menyimpan data pengguna: ', error);
                });
        }

        // Load user data from Firebase
        function loadUserDataFromFirebase() {
            if (!currentUser) return;
            
            db.collection('users').doc(currentUser.uid).get()
                .then((doc) => {
                    if (doc.exists) {
                        const userData = doc.data();
                        
                        // Load cart data
                        if (userData.cart) {
                            cart = userData.cart;
                            updateCartCount();
                        }
                        
                        // Load wishlist data
                        if (userData.wishlist) {
                            wishlist = userData.wishlist;
                            updateWishlistCount();
                        }
                        
                        // Load profile data
                        if (userData.profile) {
                            currentUser = { ...currentUser, ...userData.profile };
                            loadUserProfile();
                        }
                        
                        // Update wishlist icons
                        updateWishlistIcons();
                        
                        console.log('Data pengguna berhasil dimuat dari Firebase');
                    }
                })
                .catch((error) => {
                    console.error('Error memuat data pengguna: ', error);
                });
        }

        // Save transaction to Firebase
        function saveTransactionToFirebase(transaction) {
            if (!currentUser) return;
            
            // Tambahkan user ID ke transaksi
            transaction.userId = currentUser.uid;
            transaction.createdAt = firebase.firestore.FieldValue.serverTimestamp();
            
            db.collection('transactions').doc(transaction.id).set(transaction)
                .then(() => {
                    console.log('Transaksi berhasil disimpan ke Firebase');
                })
                .catch((error) => {
                    console.error('Error menyimpan transaksi: ', error);
                });
        }

        // Load transactions from Firebase
        function loadTransactionsFromFirebase() {
            if (!currentUser) return;
            
            db.collection('transactions')
                .where('userId', '==', currentUser.uid)
                .orderBy('createdAt', 'desc')
                .get()
                .then((querySnapshot) => {
                    const transactions = [];
                    querySnapshot.forEach((doc) => {
                        transactions.push(doc.data());
                    });
                    
                    // Simpan ke localStorage
                    localStorage.setItem('transactions', JSON.stringify(transactions));
                    
                    // Render ulang halaman transaksi jika sedang aktif
                    if (document.getElementById('transactions-page').classList.contains('active')) {
                        renderTransactions();
                    }
                })
                .catch((error) => {
                    console.error('Error memuat transaksi: ', error);
                });
        }

        // Login with email and password
        function loginWithEmail(email, password) {
            auth.signInWithEmailAndPassword(email, password)
                .then((userCredential) => {
                    // Login berhasil
                    currentUser = userCredential.user;
                    updateUserUI();
                    closeLoginModal();
                    showNotification('Login berhasil!', 'success');
                    
                    // Muat data dari Firebase
                    loadUserDataFromFirebase();
                    loadTransactionsFromFirebase();
                })
                .catch((error) => {
                    // Login gagal
                    showNotification('Email atau password salah', 'error');
                });
        }

        // Signup with email and password
        function signupWithEmail(name, email, password, phone) {
            auth.createUserWithEmailAndPassword(email, password)
                .then((userCredential) => {
                    // Pendaftaran berhasil
                    currentUser = userCredential.user;
                    
                    // Update profile user
                    return currentUser.updateProfile({
                        displayName: name
                    });
                })
                .then(() => {
                    // Simpan data tambahan ke Firebase
                    const userData = {
                        uid: currentUser.uid,
                        email: currentUser.email,
                        displayName: name,
                        phone: phone,
                        createdAt: firebase.firestore.FieldValue.serverTimestamp()
                    };
                    
                    return db.collection('users').doc(currentUser.uid).set(userData, { merge: true });
                })
                .then(() => {
                    updateUserUI();
                    closeLoginModal();
                    showNotification('Pendaftaran berhasil!', 'success');
                    
                    // Muat data dari Firebase
                    loadUserDataFromFirebase();
                })
                .catch((error) => {
                    // Pendaftaran gagal
                    showNotification('Pendaftaran gagal: ' + error.message, 'error');
                });
        }

        // Login with Google
        function loginWithGoogle() {
            const provider = new firebase.auth.GoogleAuthProvider();
            
            auth.signInWithPopup(provider)
                .then((result) => {
                    // Login berhasil
                    currentUser = result.user;
                    updateUserUI();
                    closeLoginModal();
                    showNotification('Login berhasil!', 'success');
                    
                    // Simpan data user ke Firebase
                    const userData = {
                        uid: currentUser.uid,
                        email: currentUser.email,
                        displayName: currentUser.displayName,
                        photoURL: currentUser.photoURL,
                        createdAt: firebase.firestore.FieldValue.serverTimestamp()
                    };
                    
                    return db.collection('users').doc(currentUser.uid).set(userData, { merge: true });
                })
                .then(() => {
                    // Muat data dari Firebase
                    loadUserDataFromFirebase();
                    loadTransactionsFromFirebase();
                })
                .catch((error) => {
                    // Login gagal
                    showNotification('Login dengan Google gagal: ' + error.message, 'error');
                });
        }

        // Logout functionality
        function logout() {
            auth.signOut()
                .then(() => {
                    currentUser = null;
                    updateUserUI();
                    navigateTo('home');
                    showNotification('Anda telah logout', 'success');
                })
                .catch((error) => {
                    console.error('Error logging out: ', error);
                });
        }

        // Process payment and complete order
        function processPayment() {
            // Save transaction to localStorage
            const transaction = {
                id: currentOrderId,
                date: new Date().toISOString(),
                items: [...cart],
                total: currentTransactionAmount,
                status: 'validated',
                paymentMethod: 'QRIS'
            };
            
            // Get existing transactions
            const transactions = JSON.parse(localStorage.getItem('transactions')) || [];
            transactions.push(transaction);
            localStorage.setItem('transactions', JSON.stringify(transactions));
            
            // Simpan ke Firebase
            saveTransactionToFirebase(transaction);
            
            // Clear cart
            cart = [];
            updateCartCount();
            
            // Close modals
            checkoutModal.classList.remove('active');
            confirmationModal.classList.remove('active');
            
            // Show status modal
            openStatusModal();
        }

        // Setup event listeners
        function setupEventListeners() {
            // ... event listeners yang sudah ada ...

            // Login dengan Google
            if (googleLoginBtn) {
                googleLoginBtn.addEventListener('click', loginWithGoogle);
            }

            // Login form submission
            if (loginForm) {
                loginForm.addEventListener('submit', function(e) {
                    e.preventDefault();
                    
                    const email = document.getElementById('login-email').value;
                    const password = document.getElementById('login-password').value;
                    
                    loginWithEmail(email, password);
                });
            }
            
            // Signup form submission
            if (signupForm) {
                signupForm.addEventListener('submit', function(e) {
                    e.preventDefault();
                    
                    const name = document.getElementById('signup-name').value;
                    const email = document.getElementById('signup-email').value;
                    const password = document.getElementById('signup-password').value;
                    const phone = document.getElementById('signup-phone').value;
                    
                    signupWithEmail(name, email, password, phone);
                });
            }

            // ... event listeners lainnya ...
        }

        // Initialize Firebase Auth state listener
        auth.onAuthStateChanged((user) => {
            if (user) {
                // Pengguna sudah login
                currentUser = user;
                updateUserUI();
                
                // Tampilkan loading sinkronisasi
                if (syncLoading) {
                    syncLoading.style.display = 'block';
                }
                
                // Muat data dari Firebase
                loadUserDataFromFirebase();
                loadTransactionsFromFirebase();
                
                // Sembunyikan loading setelah beberapa detik
                setTimeout(() => {
                    if (syncLoading) {
                        syncLoading.style.display = 'none';
                    }
                }, 2000);
                
                // Check if user is admin
                if (currentUser && (currentUser.email === 'admin@fresh.in' || currentUser.email === 'onlineperangkat@gmail.com')) {
                    isAdmin = true;
                    // Add admin link to mobile menu
                    addAdminToMenu();
                }
            } else {
                // Pengguna belum login
                currentUser = null;
                updateUserUI();
            }
        });

        // Initialize the app
        document.addEventListener('DOMContentLoaded', initializeApp);
    </script>
</body>
</html>
