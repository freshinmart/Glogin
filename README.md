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
        :root {
            --primary: #00B14F;
            --primary-dark: #009440;
            --primary-light: #E0F6E9;
            --primary-gradient: linear-gradient(135deg, #00B14F 0%, #00D25B 100%);
            --accent: #FF6B00;
            --secondary: #2196F3;
            --light: #FFFFFF;
            --dark: #1E272E;
            --gray-light: #F5F7FA;
            --gray: #A6A6A6;
            --gray-dark: #687176;
            --success: #28C76F;
            --warning: #FF9F43;
            --error: #EA5455;
            --border-radius: 16px;
            --border-radius-sm: 8px;
            --box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
            --box-shadow-lg: 0 10px 30px rgba(0, 0, 0, 0.12);
            --transition: all 0.3s ease;
            --header-height: 70px;
            --bottom-nav-height: 65px;
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Poppins', sans-serif;
            background-color: var(--gray-light);
            color: var(--dark);
            overflow-x: hidden;
            line-height: 1.6;
            padding-top: var(--header-height);
            padding-bottom: var(--bottom-nav-height);
            transition: var(--transition);
        }

        /* Tambahkan style untuk menyembunyikan header dan kategori di halaman tertentu */
        .hide-header #main-header,
        .hide-header .nav-categories {
            display: none !important;
        }

        .hide-header body {
            padding-top: 0;
        }

        /* Perbaikan posisi konten halaman setelah header disembunyikan */
        .hide-header .page:not(#home-page) .container {
            padding-top: 0;
            margin-top: 0;
        }

        .hide-header .page:not(#home-page) .section-title {
            margin-top: 0;
            padding-top: 10px;
        }

        .hide-header .page:not(#home-page) .back-button {
            margin-top: 15px;
        }

        /* Promo page adjustment */
        .hide-header #promo-page .container {
            padding-top: 5px;
        }

        /* Cart page adjustment */
        .hide-header #cart-page .container {
            padding-top: 5px;
        }

        /* Transactions page adjustment */
        .hide-header #transactions-page .container {
            padding-top: 5px;
        }

        /* Profile page adjustment */
        .hide-header #profile-page .container {
            padding-top: 5px;
        }

        /* Sisa kode CSS tetap sama */
        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 0 20px;
            transition: var(--transition);
        }

        /* Header Styles */
        header {
            background-color: var(--light);
            box-shadow: var(--box-shadow);
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            z-index: 1000;
            height: var(--header-height);
            transition: var(--transition);
        }

        .header-container {
            display: flex;
            justify-content: space-between;
            align-items: center;
            height: 100%;
            padding: 0 20px;
        }

        .header-left {
            display: flex;
            align-items: center;
        }

        .mobile-menu-btn {
            display: none;
            background: none;
            border: none;
            font-size: 20px;
            color: var(--dark);
            cursor: pointer;
            margin-right: 15px;
        }

        .logo {
            display: flex;
            align-items: center;
        }

        .logo h1 {
            font-family: 'Montserrat', sans-serif;
            font-size: 24px;
            font-weight: 800;
            letter-spacing: -0.5px;
        }

        .fresh {
            color: var(--primary);
        }

        .dot-in {
            color: var(--accent);
        }

        .header-center {
            flex: 1;
            max-width: 500px;
            margin: 0 20px;
            position: relative;
        }

        .search-bar {
            position: relative;
        }

        .search-bar input {
            width: 100%;
            padding: 12px 50px 12px 20px;
            border: 1px solid #e0e0e0;
            border-radius: 30px;
            font-size: 14px;
            transition: var(--transition);
            background-color: var(--gray-light);
            box-shadow: inset 0 1px 3px rgba(0, 0, 0, 0.05);
        }

        .search-bar input:focus {
            outline: none;
            border-color: var(--primary);
            box-shadow: 0 0 0 2px rgba(0, 177, 79, 0.2);
            background-color: var(--light);
        }

        .search-bar button {
            position: absolute;
            right: 4px;
            top: 4px;
            background: var(--primary-gradient);
            color: white;
            border: none;
            border-radius: 20px;
            padding: 8px 16px;
            cursor: pointer;
            transition: var(--transition);
            font-weight: 500;
            font-size: 14px;
        }

        .search-bar button:hover {
            opacity: 0.9;
            transform: translateY(-1px);
        }

        .header-right {
            display: flex;
            gap: 15px;
        }

        .icon-link {
            position: relative;
            color: var(--dark);
            font-size: 18px;
            text-decoration: none;
            transition: var(--transition);
            display: flex;
            align-items: center;
            padding: 8px;
            border-radius: 50%;
        }

        .icon-link:hover {
            color: var(--primary);
            background-color: var(--primary-light);
        }

        .icon-link .label {
            font-size: 14px;
            margin-left: 5px;
            display: none;
        }

        /* Tombol Masuk */
        .login-btn {
            background: var(--primary);
            color: white;
            border: none;
            padding: 8px 16px;
            border-radius: 20px;
            font-weight: 500;
            cursor: pointer;
            transition: var(--transition);
            font-size: 14px;
        }

        .login-btn:hover {
            background: var(--primary-dark);
        }

        /* HILANGKAN BACKGROUND DAN ANGKA PADA CART DAN WISHLIST */
        .cart-count, .wishlist-count {
            display: none !important;
        }

        /* Bottom Navigation */
        .bottom-nav {
            position: fixed;
            bottom: 0;
            left: 0;
            width: 100%;
            height: var(--bottom-nav-height);
            background: var(--light);
            box-shadow: 0 -4px 20px rgba(0, 0, 0, 0.08);
            display: flex;
            justify-content: space-around;
            align-items: center;
            z-index: 1000;
            padding: 0 5px;
        }

        .nav-item {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            text-decoration: none;
            color: var(--gray);
            font-size: 12px;
            padding: 8px 12px;
            border-radius: var(--border-radius-sm);
            transition: var(--transition);
            flex: 1;
            max-width: 80px;
        }

        .nav-item.active {
            color: var(--primary);
        }

        .nav-item i {
            font-size: 20px;
            margin-bottom: 4px;
        }

        .nav-item.active i {
            transform: translateY(-2px);
        }

        .nav-item .nav-label {
            font-weight: 500;
        }

        /* Swipe Cart Styles */
        .swipe-cart-container {
            position: fixed;
            top: 0;
            right: -100%;
            width: 85%;
            height: 100%;
            background: white;
            z-index: 2000;
            transition: all 0.3s ease;
            box-shadow: -5px 0 15px rgba(0, 0, 0, 0.1);
            display: flex;
            flex-direction: column;
        }

        .swipe-cart-container.active {
            right: 0;
        }

        .swipe-cart-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 15px 20px;
            background: var(--primary-gradient);
            color: white;
        }

        .swipe-cart-title {
            font-size: 18px;
            font-weight: 600;
        }

        .swipe-cart-close {
            background: none;
            border: none;
            color: white;
            font-size: 24px;
            cursor: pointer;
        }

        .swipe-cart-content {
            flex: 1;
            overflow-y: auto;
            padding: 15px;
        }

        .swipe-cart-summary {
            padding: 15px 20px;
            border-top: 1px solid #eee;
            background: var(--gray-light);
        }

        .swipe-handle {
            position: absolute;
            top: 50%;
            left: -20px;
            transform: translateY(-50%);
            width: 20px;
            height: 60px;
            background: var(--primary);
            border-radius: 10px 0 0 10px;
            display: flex;
            align-items: center;
            justify-content: center;
            color: white;
            cursor: pointer;
        }

        /* Hide profile icon in header */
        #user-link {
            display: none !important;
        }

        /* Hide categories on non-home pages */
        .page:not(#home-page) .nav-categories {
            display: none !important;
        }

        /* Hide categories in mobile menu */
        .mobile-menu-category:first-child {
            display: none !important;
        }

        /* Payment method adjustments for QRIS only */
        .payment-method:not([data-method="qris"]) {
            display: none !important;
        }

        .payment-methods-grid {
            grid-template-columns: 1fr !important;
        }

        /* Transaction status adjustments */
        .status-completed {
            background-color: var(--primary-light);
            color: var(--success);
        }

        /* Admin Dashboard Styles */
        .admin-dashboard {
            display: grid;
            grid-template-columns: 1fr;
            gap: 20px;
            margin-top: 20px;
        }

        @media (min-width: 768px) {
            .admin-dashboard {
                grid-template-columns: repeat(2, 1fr);
            }
        }

        @media (min-width: 1024px) {
            .admin-dashboard {
                grid-template-columns: repeat(3, 1fr);
            }
        }

        .admin-stat-card {
            background: white;
            border-radius: var(--border-radius);
            padding: 20px;
            box-shadow: var(--box-shadow);
            text-align: center;
        }

        .admin-stat-icon {
            font-size: 2.5rem;
            color: var(--primary);
            margin-bottom: 10px;
        }

        .admin-stat-number {
            font-size: 2rem;
            font-weight: 700;
            color: var(--dark);
            margin-bottom: 5px;
        }

        .admin-stat-label {
            color: var(--gray);
            font-size: 0.9rem;
        }

        .admin-tabs {
            display: flex;
            border-bottom: 1px solid #eee;
            margin-bottom: 20px;
            overflow-x: auto;
        }

        .admin-tab {
            padding: 12px 20px;
            background: none;
            border: none;
            cursor: pointer;
            font-weight: 600;
            color: var(--gray);
            border-bottom: 2px solid transparent;
            white-space: nowrap;
        }

        .admin-tab.active {
            color: var(--primary);
            border-bottom: 2px solid var(--primary);
        }

        .admin-tab-content {
            display: none;
        }

        .admin-tab-content.active {
            display: block;
        }

        .admin-table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 15px;
            background: white;
            border-radius: var(--border-radius);
            overflow: hidden;
            box-shadow: var(--box-shadow);
        }

        .admin-table th,
        .admin-table td {
            padding: 12px;
            text-align: left;
            border-bottom: 1px solid #eee;
        }

        .admin-table th {
            background: var(--gray-light);
            font-weight: 600;
        }

        .admin-actions {
            display: flex;
            gap: 8px;
            flex-wrap: wrap;
        }

        .admin-btn {
            padding: 6px 12px;
            border-radius: var(--border-radius-sm);
            font-weight: 600;
            cursor: pointer;
            transition: var(--transition);
            border: none;
            font-size: 12px;
        }

        .admin-btn.small {
            padding: 4px 8px;
            font-size: 11px;
        }

        .admin-btn.approve {
            background: var(--success);
            color: white;
        }

        .admin-btn.reject {
            background: var(--error);
            color: white;
        }

        .admin-btn.edit {
            background: var(--warning);
            color: white;
        }

        .admin-btn.delete {
            background: var(--error);
            color: white;
        }

        .admin-btn.view {
            background: var(--secondary);
            color: white;
        }

        .admin-btn:hover {
            opacity: 0.9;
            transform: translateY(-2px);
        }

        .product-form {
            background: white;
            border-radius: var(--border-radius);
            padding: 20px;
            box-shadow: var(--box-shadow);
            margin-bottom: 20px;
        }

        .form-row {
            display: grid;
            grid-template-columns: 1fr;
            gap: 15px;
            margin-bottom: 15px;
        }

        @media (min-width: 768px) {
            .form-row {
                grid-template-columns: 1fr 1fr;
            }
        }

        .form-actions {
            display: flex;
            gap: 10px;
            justify-content: flex-end;
            margin-top: 20px;
        }

        /* Mobile Menu */
        .mobile-menu {
            position: fixed;
            top: var(--header-height);
            left: -100%;
            width: 80%;
            height: calc(100vh - var(--header-height) - var(--bottom-nav-height));
            background: var(--light);
            z-index: 999;
            transition: var(--transition);
            overflow-y: auto;
            box-shadow: 2px 0 10px rgba(0, 0, 0, 0.1);
        }

        .mobile-menu.active {
            left: 0;
        }

        .mobile-menu-header {
            padding: 15px;
            background: var(--primary-gradient);
            color: white;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .mobile-menu-close {
            background: none;
            border: none;
            color: white;
            font-size: 20px;
            cursor: pointer;
        }

        .mobile-menu-categories {
            padding: 15px;
        }

        .mobile-menu-category {
            margin-bottom: 15px;
        }

        .mobile-menu-category-title {
            font-weight: 600;
            margin-bottom: 10px;
            padding-bottom: 5px;
            border-bottom: 1px solid #eee;
            color: var(--primary);
        }

        .mobile-menu-links {
            list-style: none;
        }

        .mobile-menu-links li {
            margin-bottom: 8px;
        }

        .mobile-menu-links a {
            display: block;
            padding: 10px;
            color: var(--dark);
            text-decoration: none;
            border-radius: 6px;
            transition: var(--transition);
        }

        .mobile-menu-links a:hover {
            background: var(--primary-light);
            color: var(--primary);
        }

        .mobile-menu-links a i {
            margin-right: 10px;
            width: 20px;
            text-align: center;
        }

        /* Navigation Categories */
        .nav-categories {
            background: var(--light);
            padding: 15px 0;
            border-bottom: 1px solid #eee;
            overflow-x: auto;
            white-space: nowrap;
            scrollbar-width: none;
        }

        .nav-categories::-webkit-scrollbar {
            display: none;
        }

        .category-scroll {
            display: flex;
            gap: 15px;
            padding: 0 20px;
        }

        .category-item {
            display: flex;
            flex-direction: column;
            align-items: center;
            text-decoration: none;
            color: var(--dark);
            min-width: 70px;
            transition: var(--transition);
            padding: 10px 5px;
            border-radius: var(--border-radius-sm);
        }

        .category-item:hover {
            color: var(--primary);
            background-color: var(--primary-light);
        }

        .category-icon {
            width: 40px;
            height: 40px;
            border-radius: 12px;
            background: var(--primary-light);
            display: flex;
            align-items: center;
            justify-content: center;
            margin-bottom: 8px;
            font-size: 18px;
            color: var(--primary);
            transition: var(--transition);
        }

        .category-item:hover .category-icon {
            background: var(--primary-gradient);
            color: white;
            transform: translateY(-3px);
        }

        .category-name {
            font-size: 12px;
            text-align: center;
            font-weight: 500;
        }

        /* Hero Carousel */
        .hero-carousel {
            margin: 20px 0;
            border-radius: var(--border-radius);
            overflow: hidden;
            position: relative;
            height: 180px;
            box-shadow: var(--box-shadow);
        }

        .carousel-container {
            position: relative;
            width: 100%;
            height: 100%;
            overflow: hidden;
        }

        .carousel-slides {
            display: flex;
            transition: transform 0.5s ease;
            height: 100%;
        }

        .carousel-slide {
            min-width: 100%;
            height: 100%;
            position: relative;
        }

        .carousel-slide img {
            width: 100%;
            height: 100%;
            object-fit: cover;
        }

        .carousel-content {
            position: absolute;
            bottom: 0;
            left: 0;
            right: 0;
            color: white;
            z-index: 2;
            padding: 20px;
            background: linear-gradient(transparent, rgba(0, 0, 0, 0.7));
        }

        .carousel-content h2 {
            font-size: 18px;
            font-weight: 700;
            margin-bottom: 8px;
        }

        .carousel-content p {
            font-size: 13px;
            opacity: 0.9;
            margin-bottom: 12px;
        }

        .hero-btn {
            display: inline-block;
            background: white;
            color: var(--primary);
            text-decoration: none;
            padding: 8px 16px;
            border-radius: 20px;
            font-weight: 600;
            font-size: 13px;
            transition: var(--transition);
            width: fit-content;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
        }

        .hero-btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 6px 15px rgba(0, 0, 0, 0.15);
        }

        .carousel-controls {
            position: absolute;
            top: 50%;
            width: 100%;
            display: flex;
            justify-content: space-between;
            transform: translateY(-50%);
            z-index: 3;
            padding: 0 10px;
        }

        .carousel-control {
            background: rgba(0,0,0,0.5);
            color: white;
            border: none;
            padding: 10px;
            cursor: pointer;
            border-radius: 50%;
            width: 40px;
            height: 40px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 18px;
            transition: var(--transition);
        }

        .carousel-control:hover {
            background: rgba(0,0,0,0.7);
            transform: scale(1.1);
        }

        .carousel-dots {
            position: absolute;
            bottom: 10px;
            width: 100%;
            display: flex;
            justify-content: center;
            z-index: 3;
            gap: 8px;
        }

        .carousel-dot {
            width: 10px;
            height: 10px;
            border-radius: 50%;
            background: rgba(255,255,255,0.5);
            cursor: pointer;
            transition: var(--transition);
        }

        .carousel-dot.active {
            background: white;
            transform: scale(1.2);
        }

        /* Products Grid */
        .products-section {
            padding: 20px 0;
        }

        .section-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 16px;
        }

        .section-title {
            font-size: 18px;
            font-weight: 700;
            color: var(--dark);
            position: relative;
            padding-left: 12px;
        }

        .section-title::before {
            content: '';
            position: absolute;
            left: 0;
            top: 50%;
            transform: translateY(-50%);
            width: 4px;
            height: 18px;
            background: var(--primary-gradient);
            border-radius: 10px;
        }

        .view-all {
            color: var(--primary);
            text-decoration: none;
            font-size: 14px;
            font-weight: 500;
            display: flex;
            align-items: center;
        }

        .view-all i {
            margin-left: 5px;
            transition: var(--transition);
        }

        .view-all:hover i {
            transform: translateX(3px);
        }

        .products-grid {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 15px;
        }

        @media (min-width: 480px) {
            .products-grid {
                grid-template-columns: repeat(2, 1fr);
            }
        }

        @media (min-width: 768px) {
            .products-grid {
                grid-template-columns: repeat(4, 1fr);
            }
        }

        .product-card {
            background: var(--light);
            border-radius: var(--border-radius);
            overflow: hidden;
            box-shadow: var(--box-shadow);
            transition: var(--transition);
            position: relative;
        }

        .product-card:hover {
            transform: translateY(-5px);
            box-shadow: var(--box-shadow-lg);
        }

        .product-badge {
            position: absolute;
            top: 10px;
            left: 10px;
            background: var(--primary);
            color: white;
            padding: 4px 8px;
            border-radius: 4px;
            font-size: 10px;
            font-weight: 600;
            z-index: 2;
        }

        .product-badge.sale {
            background: var(--error);
        }

        .product-badge.new {
            background: var(--secondary);
        }

        .product-image {
            height: 160px;
            width: 100%;
            background-color: var(--gray-light);
            display: flex;
            align-items: center;
            justify-content: center;
            overflow: hidden;
            position: relative;
        }

        .product-image img {
            width: 100%;
            height: 100%;
            object-fit: cover;
            transition: transform 0.5s;
        }

        .product-card:hover .product-image img {
            transform: scale(1.05);
        }

        .like-button {
            position: absolute;
            top: 10px;
            right: 10px;
            background: rgba(255, 255, 255, 0.9);
            border: none;
            border-radius: 50%;
            width: 32px;
            height: 32px;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            z-index: 3;
            transition: var(--transition);
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
        }

        .like-button:hover {
            background: white;
            transform: scale(1.1);
        }

        .like-button i {
            color: #ccc;
            font-size: 16px;
            transition: var(--transition);
        }

        .like-button.liked i {
            color: var(--error);
        }

        .product-info {
            padding: 12px;
        }

        .product-title {
            font-size: 14px;
            font-weight: 500;
            margin-bottom: 8px;
            color: var(--dark);
            display: -webkit-box;
            -webkit-line-clamp: 2;
            -webkit-box-orient: vertical;
            overflow: hidden;
            height: 40px;
        }

        .product-price {
            display: flex;
            align-items: center;
            margin-bottom: 12px;
        }

        .current-price {
            font-size: 16px;
            font-weight: 700;
            color: var(--dark);
        }

        .original-price {
            font-size: 13px;
            color: var(--gray);
            text-decoration: line-through;
            margin-left: 8px;
        }

        .discount {
            background: var(--primary-light);
            color: var(--primary);
            font-size: 11px;
            font-weight: 600;
            padding: 3px 6px;
            border-radius: 4px;
            margin-left: 8px;
        }

        .add-to-cart {
            display: block;
            width: 100%;
            background: var(--primary-gradient);
            color: white;
            border: none;
            padding: 10px;
            border-radius: var(--border-radius-sm);
            font-weight: 600;
            cursor: pointer;
            transition: var(--transition);
            font-size: 14px;
            box-shadow: 0 4px 10px rgba(0, 177, 79, 0.25);
        }

        .add-to-cart:hover {
            opacity: 0.9;
            transform: translateY(-2px);
            box-shadow: 0 6px 15px rgba(0, 177, 79, 0.3);
        }

        /* Footer */
        footer {
            background: var(--light);
            padding: 30px 0 15px;
            margin-top: 30px;
        }

        .footer-grid {
            display: grid;
            grid-template-columns: 1fr;
            gap: 30px;
        }

        @media (min-width: 768px) {
            .footer-grid {
                grid-template-columns: repeat(4, 1fr);
            }
        }

        .footer-logo {
            font-family: 'Montserrat', sans-serif;
            font-size: 20px;
            font-weight: 800;
            margin-bottom: 12px;
            display: block;
            text-decoration: none;
        }

        .footer-fresh {
            color: var(--primary);
        }

        .footer-in {
            color: var(--accent);
        }

        .footer-about p {
            color: var(--gray);
            margin-bottom: 15px;
            line-height: 1.7;
            font-size: 14px;
        }

        .social-links {
            display: flex;
            gap: 10px;
        }

        .social-links a {
            display: inline-flex;
            align-items: center;
            justify-content: center;
            width: 36px;
            height: 36px;
            background: var(--gray-light);
            color: var(--dark);
            border-radius: 50%;
            text-decoration: none;
            transition: var(--transition);
            font-size: 14px;
        }

        .social-links a:hover {
            background: var(--primary);
            color: white;
            transform: translateY(-3px);
        }

        .footer-title {
            font-size: 16px;
            font-weight: 600;
            margin-bottom: 15px;
            color: var(--dark);
            position: relative;
            padding-bottom: 8px;
        }

        .footer-title::after {
            content: '';
            position: absolute;
            bottom: 0;
            left: 0;
            width: 30px;
            height: 3px;
            background: var(--primary);
            border-radius: 10px;
        }

        .footer-links {
            list-style: none;
        }

        .footer-links li {
            margin-bottom: 10px;
        }

        .footer-links a {
            color: var(--gray);
            text-decoration: none;
            transition: var(--transition);
            font-size: 14px;
            display: flex;
            align-items: center;
        }

        .footer-links a i {
            margin-right: 8px;
            font-size: 12px;
        }

        .footer-links a:hover {
            color: var(--primary);
            transform: translateX(5px);
        }

        .copyright {
            text-align: center;
            padding-top: 20px;
            border-top: 1px solid #eee;
            color: var(--gray);
            font-size: 13px;
            margin-top: 20px;
        }

        /* Modal Styles */
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.5);
            z-index: 2000;
            align-items: center;
            justify-content: center;
            padding: 15px;
            backdrop-filter: blur(5px);
        }

        .modal.active {
            display: flex;
        }

        .modal-content {
            background: white;
            border-radius: var(--border-radius);
            box-shadow: var(--box-shadow-lg);
            width: 100%;
            max-width: 500px;
            overflow: hidden;
            animation: modalSlideUp 0.3s ease;
            max-height: 90vh;
            display: flex;
            flex-direction: column;
        }

        @keyframes modalSlideUp {
            from { transform: translateY(50px); opacity: 0; }
            to { transform: translateY(0); opacity: 1; }
        }

        .modal-header {
            padding: 15px 20px;
            border-bottom: 1px solid #eee;
            display: flex;
            justify-content: space-between;
            align-items: center;
            background: var(--primary-gradient);
            color: white;
        }

        .modal-header h3 {
            font-size: 18px;
            font-weight: 600;
            flex: 1;
        }

        .modal-close {
            background: none;
            border: none;
            font-size: 24px;
            cursor: pointer;
            color: white;
            opacity: 0.8;
            transition: var(--transition);
        }

        .modal-close:hover {
            opacity: 1;
            transform: rotate(90deg);
        }

        .modal-body {
            padding: 20px;
            overflow-y: auto;
            flex: 1;
        }

        /* Cart Modal */
        .cart-item {
            display: flex;
            padding: 15px 0;
            border-bottom: 1px solid #eee;
        }

        .cart-item:last-child {
            border-bottom: none;
        }

        .cart-item-image {
            width: 80px;
            height: 80px;
            background: var(--gray-light);
            border-radius: var(--border-radius-sm);
            display: flex;
            align-items: center;
            justify-content: center;
            margin-right: 15px;
            overflow: hidden;
        }

        .cart-item-image img {
            width: 100%;
            height: 100%;
            object-fit: cover;
        }

        .cart-item-details {
            flex: 1;
        }

        .cart-item-title {
            font-weight: 500;
            margin-bottom: 5px;
            font-size: 14px;
        }

        .cart-item-price {
            color: var(--dark);
            font-weight: 600;
            margin-bottom: 8px;
            font-size: 15px;
        }

        .cart-item-actions {
            display: flex;
            gap: 8px;
            align-items: center;
        }

        .quantity-control {
            display: flex;
            align-items: center;
            border: 1px solid #ddd;
            border-radius: 20px;
            overflow: hidden;
        }

        .quantity-btn {
            background: none;
            border: none;
            width: 30px;
            height: 30px;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            font-size: 16px;
            transition: var(--transition);
        }

        .quantity-btn:hover {
            background: var(--gray-light);
        }

        .quantity-input {
            width: 30px;
            text-align: center;
            border: none;
            background: transparent;
            font-size: 14px;
            font-weight: 600;
        }

        .remove-btn {
            background: none;
            border: none;
            color: var(--error);
            cursor: pointer;
            font-size: 13px;
            display: flex;
            align-items: center;
            transition: var(--transition);
        }

        .remove-btn:hover {
            color: #c53030;
        }

        .remove-btn i {
            margin-right: 4px;
        }

        .cart-summary {
            padding: 15px 20px;
            border-top: 1px solid #eee;
            background: var(--gray-light);
        }

        .summary-item {
            display: flex;
            justify-content: space-between;
            margin-bottom: 8px;
            font-size: 14px;
        }

        .summary-total {
            display: flex;
            justify-content: space-between;
            font-weight: 700;
            font-size: 16px;
            margin-top: 12px;
            padding-top: 12px;
            border-top: 1px solid #ddd;
        }

        .checkout-btn {
            display: block;
            width: 100%;
            background: var(--primary-gradient);
            color: white;
            border: none;
            padding: 12px;
            border-radius: var(--border-radius-sm);
            font-weight: 600;
            cursor: pointer;
            transition: var(--transition);
            font-size: 15px;
            text-align: center;
            text-decoration: none;
            margin-top: 15px;
            box-shadow: 0 4px 10px rgba(0, 177, 79, 0.25);
        }

        .checkout-btn:hover {
            opacity: 0.9;
            transform: translateY(-2px);
            box-shadow: 0 6px 15px rgba(0, 177, 79, 0.3);
        }

        .empty-cart {
            text-align: center;
            padding: 40px 0;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            color: var(--gray);
        }

        .empty-cart i {
            font-size: 60px;
            color: #ddd;
            margin-bottom: 15px;
        }

        .empty-cart h4 {
            font-size: 18px;
            margin-bottom: 10px;
            color: var(--dark);
        }

        .empty-cart p {
            margin-bottom: 20px;
        }

        /* Checkout Modal */
        .checkout-steps {
            display: flex;
            justify-content: space-between;
            margin-bottom: 20px;
            position: relative;
        }

        .checkout-steps::before {
            content: '';
            position: absolute;
            top: 14px;
            left: 0;
            right: 0;
            height: 2px;
            background: #eee;
            z-index: 1;
        }

        .step {
            position: relative;
            z-index: 2;
            display: flex;
            flex-direction: column;
            align-items: center;
            width: 25%;
        }

        .step-icon {
            width: 30px;
            height: 30px;
            border-radius: 50%;
            background: white;
            border: 2px solid #eee;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 13px;
            margin-bottom: 5px;
            transition: var(--transition);
        }

        .step.active .step-icon {
            background: var(--primary);
            border-color: var(--primary);
            color: white;
            transform: scale(1.1);
        }

        .step.completed .step-icon {
            background: var(--success);
            border-color: var(--success);
            color: white;
        }

        .step-text {
            font-size: 11px;
            text-align: center;
            color: var(--gray);
            font-weight: 500;
        }

        .step.active .step-text {
            color: var(--primary);
            font-weight: 600;
        }

        .checkout-form {
            margin-top: 15px;
        }

        .form-group {
            margin-bottom: 15px;
        }

        .form-group label {
            display: block;
            margin-bottom: 8px;
            font-weight: 500;
            font-size: 14px;
        }

        .form-control {
            width: 100%;
            padding: 12px 15px;
            border: 1px solid #ddd;
            border-radius: var(--border-radius-sm);
            font-size: 14px;
            transition: var(--transition);
        }

        .form-control:focus {
            outline: none;
            border-color: var(--primary);
            box-shadow: 0 0 0 2px rgba(0, 177, 79, 0.2);
        }

        .payment-options {
            margin-top: 15px;
        }

        .payment-option {
            display: flex;
            align-items: center;
            padding: 15px;
            border: 1px solid #ddd;
            border-radius: var(--border-radius-sm);
            margin-bottom: 10px;
            cursor: pointer;
            transition: var(--transition);
        }

        .payment-option:hover {
            border-color: var(--primary);
        }

        .payment-option.selected {
            border-color: var(--primary);
            background-color: var(--primary-light);
        }

        .payment-option input {
            margin-right: 10px;
        }

        .payment-option-icon {
            margin-right: 10px;
            font-size: 20px;
            color: var(--primary);
        }

        .qris-preview {
            text-align: center;
            margin-top: 15px;
            padding: 15px;
            background: var(--gray-light);
            border-radius: var(--border-radius-sm);
            display: none;
        }

        .qris-preview img {
            max-width: 180px;
            margin-bottom: 10px;
            border-radius: var(--border-radius-sm);
        }

        .payment-actions {
            display: flex;
            justify-content: space-between;
            margin-top: 20px;
            gap: 10px;
        }

        .btn {
            padding: 12px 20px;
            border-radius: var(--border-radius-sm);
            font-weight: 600;
            cursor: pointer;
            transition: var(--transition);
            border: none;
            flex: 1;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .btn-secondary {
            background: #e0e0e0;
            color: var(--dark);
        }

        .btn-secondary:hover {
            background: #d0d0d0;
        }

        .btn-primary {
            background: var(--primary-gradient);
            color: white;
            box-shadow: 0 4px 10px rgba(0, 177, 79, 0.25);
        }

        .btn-primary:hover {
            opacity: 0.9;
            transform: translateY(-2px);
            box-shadow: 0 6px 15px rgba(0, 177, 79, 0.3);
        }

        .btn:disabled {
            opacity: 0.6;
            cursor: not-allowed;
            transform: none !important;
        }

        /* QRIS Payment Verification */
        .payment-verification {
            margin-top: 15px;
            padding: 15px;
            background: var(--gray-light);
            border-radius: var(--border-radius-sm);
            text-align: center;
        }

        .verification-status {
            display: flex;
            align-items: center;
            justify-content: center;
            margin-bottom: 10px;
            font-size: 14px;
            font-weight: 500;
        }

        .verification-status i {
            font-size: 24px;
            margin-right: 10px;
        }

        .verification-status.verifying i {
            color: var(--warning);
            animation: spin 1s linear infinite;
        }

        .verification-status.success i {
            color: var(--success);
        }

        .verification-status.error i {
            color: var(--error);
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        /* Wishlist Page */
        .wishlist-page {
            display: none;
            padding: 20px 0;
        }

        .wishlist-grid {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 15px;
        }

        @media (min-width: 768px) {
            .wishlist-grid {
                grid-template-columns: repeat(4, 1fr);
            }
        }

        /* Profile Page */
        .profile-page {
            display: none;
            padding: 20px 0;
        }

        .profile-card {
            background: white;
            border-radius: var(--border-radius);
            padding: 20px;
            box-shadow: var(--box-shadow);
        }

        .profile-header {
            text-align: center;
            margin-bottom: 20px;
            padding-bottom: 20px;
            border-bottom: 1px solid #eee;
        }

        .profile-image-container {
            position: relative;
            display: inline-block;
            margin-bottom: 15px;
        }

        .profile-image {
            width: 100px;
            height: 100px;
            border-radius: 50%;
            object-fit: cover;
            border: 3px solid var(--primary);
        }

        .change-photo-btn {
            position: absolute;
            bottom: 5px;
            right: 5px;
            background: var(--primary);
            color: white;
            border: none;
            border-radius: 50%;
            width: 30px;
            height: 30px;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: var(--transition);
        }

        .change-photo-btn:hover {
            background: var(--primary-dark);
            transform: scale(1.1);
        }

        .profile-form {
            display: grid;
            grid-template-columns: 1fr;
            gap: 15px;
        }

        @media (min-width: 768px) {
            .profile-form {
                grid-template-columns: 1fr 1fr;
            }
        }

        .profile-form .full-width {
            grid-column: 1 / -1;
        }

        /* Orders Page */
        .orders-page {
            display: none;
            padding: 20px 0;
        }

        .order-card {
            background: white;
            border-radius: var(--border-radius);
            padding: 15px;
            margin-bottom: 15px;
            box-shadow: var(--box-shadow);
        }

        .order-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 10px;
            padding-bottom: 10px;
            border-bottom: 1px solid #eee;
        }

        .order-id {
            font-weight: 600;
            color: var(--primary);
        }

        .order-date {
            color: var(--gray);
            font-size: 14px;
        }

        .order-status {
            padding: 4px 8px;
            border-radius: 20px;
            font-size: 12px;
            font-weight: 500;
        }

        .status-pending {
            background-color: #FFF6E9;
            color: var(--warning);
        }

        .status-completed {
            background-color: var(--primary-light);
            color: var(--success);
        }

        /* STATUS BARU UNTUK VALIDASI BERHASIL */
        .status-validated {
            background-color: #E0F6E9;
            color: var(--success);
        }

        .status-reviewed {
            background-color: #E3F2FD;
            color: var(--secondary);
        }

        .status-processing {
            background-color: #FFF6E9;
            color: var(--warning);
        }

        .status-shipped {
            background-color: #E3F2FD;
            color: var(--secondary);
        }

        .status-delivered {
            background-color: var(--primary-light);
            color: var(--success);
        }

        .order-details {
            margin-top: 10px;
        }

        .order-item {
            display: flex;
            justify-content: space-between;
            margin-bottom: 8px;
        }

        .order-total {
            display: flex;
            justify-content: space-between;
            font-weight: 700;
            margin-top: 10px;
            padding-top: 10px;
            border-top: 1px solid #eee;
        }

        /* History Page with Receipts */
        .history-page {
            display: none;
            padding: 20px 0;
        }

        .receipt-card {
            background: white;
            border-radius: var(--border-radius);
            padding: 20px;
            margin-bottom: 20px;
            box-shadow: var(--box-shadow);
            position: relative;
        }

        .receipt-header {
            text-align: center;
            margin-bottom: 20px;
            border-bottom: 2px dashed #eee;
            padding-bottom: 15px;
        }

        .receipt-header h3 {
            font-family: 'Montserrat', sans-serif;
            color: var(--primary);
            margin-bottom: 5px;
            font-size: 22px;
        }

        .receipt-details {
            margin-bottom: 15px;
        }

        .receipt-item {
            display: flex;
            justify-content: space-between;
            margin-bottom: 8px;
            padding-bottom: 8px;
            border-bottom: 1px dashed #eee;
        }

        .receipt-total {
            display: flex;
            justify-content: space-between;
            font-weight: 700;
            margin-top: 15px;
            padding-top: 15px;
            border-top: 2px dashed #eee;
        }

        .receipt-footer {
            text-align: center;
            margin-top: 20px;
            color: var(--gray);
            font-size: 12px;
        }

        .print-receipt {
            position: absolute;
            top: 15px;
            right: 15px;
            background: var(--primary);
            color: white;
            border: none;
            border-radius: 50%;
            width: 40px;
            height: 40px;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: var(--transition);
        }

        .print-receipt:hover {
            background: var(--primary-gradient);
            transform: scale(1.1);
        }

        /* Search Results Section */
        .search-results-section {
            display: none;
            padding: 20px 0;
        }
        
        .no-results {
            grid-column: 1 / -1;
            text-align: center;
            padding: 40px;
        }
        
        .no-results i {
            font-size: 48px;
            color: #ccc;
            margin-bottom: 16px;
        }
        
        /* Page container */
        .page {
            display: none;
        }
        
        .page.active {
            display: block;
        }
        
        /* Back button */
        .back-button {
            display: inline-flex;
            align-items: center;
            margin-bottom: 15px;
            color: var(--primary);
            text-decoration: none;
            font-weight: 500;
            padding: 8px 0;
            transition: var(--transition);
        }
        
        .back-button:hover {
            color: var(--accent);
            transform: translateX(-3px);
        }
        
        .back-button i {
            margin-right: 5px;
        }

        /* Floating action button */
        .fab {
            position: fixed;
            bottom: 80px;
            right: 20px;
            width: 60px;
            height: 60px;
            background: var(--primary-gradient);
            color: white;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 24px;
            box-shadow: 0 4px 15px rgba(0, 177, 79, 0.3);
            z-index: 999;
            transition: var(--transition);
            text-decoration: none;
        }
        
        .fab:hover {
            transform: translateY(-5px) scale(1.05);
            box-shadow: 0 8px 20px rgba(0, 177, 79, 0.4);
        }

        /* Help Page Styles */
        .faq-item {
            margin-bottom: 20px;
            padding-bottom: 15px;
            border-bottom: 1px solid #eee;
        }

        .faq-question {
            font-weight: 600;
            margin-bottom: 8px;
            color: var(--dark);
        }

        .faq-answer {
            color: var(--gray-dark);
            line-height: 1.6;
        }

        .contact-form {
            margin-top: 30px;
        }

        /* Login/Signup Modal */
        .login-tabs {
            display: flex;
            border-bottom: 1px solid #eee;
            margin-bottom: 20px;
        }

        .login-tab {
            flex: 1;
            padding: 12px;
            text-align: center;
            background: none;
            border: none;
            cursor: pointer;
            font-weight: 600;
            color: var(--gray);
            border-bottom: 2px solid transparent;
        }

        .login-tab.active {
            color: var(--primary);
            border-bottom: 2px solid var(--primary);
        }

        .login-form {
            display: none;
        }

        .login-form.active {
            display: block;
        }

        /* Proof upload area */
        .drop-area {
            border: 2px dashed var(--primary);
            border-radius: var(--border-radius-sm);
            padding: 20px;
            text-align: center;
            margin-bottom: 20px;
            transition: all 0.3s ease;
            background-color: var(--primary-light);
            cursor: pointer;
        }

        .drop-area:hover, .drop-area.dragover {
            background-color: var(--primary-light);
            border-color: var(--primary-dark);
        }

        .drop-area p {
            color: var(--gray-dark);
            margin-bottom: 15px;
        }

        .drop-area i {
            font-size: 2rem;
            color: var(--primary);
            margin-bottom: 15px;
            display: block;
        }

        .proof-preview {
            text-align: center;
            margin: 15px 0;
        }

        .proof-preview img {
            max-width: 100%;
            max-height: 200px;
            border: 1px solid #ddd;
            border-radius: var(--border-radius-sm);
        }

        .validation-message {
            margin: 15px 0;
            padding: 12px;
            border-radius: var(--border-radius-sm);
            text-align: center;
            font-weight: 600;
        }

        .validation-success {
            background-color: #f8fff9;
            border: 1px solid var(--success);
            color: var(--success);
        }

        .validation-error {
            background-color: #fff5f5;
            border: 1px solid var(--error);
            color: var(--error);
        }

        /* Status Indicator */
        .status-indicator {
            display: flex;
            justify-content: space-between;
            margin: 20px 0;
            position: relative;
        }

        .status-indicator::before {
            content: '';
            position: absolute;
            top: 15px;
            left: 0;
            right: 0;
            height: 2px;
            background: #e9ecef;
            z-index: 1;
        }

        .status-step {
            display: flex;
            flex-direction: column;
            align-items: center;
            z-index: 2;
        }

        .status-dot {
            width: 30px;
            height: 30px;
            border-radius: 50%;
            background: #e9ecef;
            display: flex;
            align-items: center;
            justify-content: center;
            margin-bottom: 5px;
            font-weight: bold;
            color: #6c757d;
        }

        .status-step.active .status-dot {
            background: var(--primary);
            color: white;
        }

        .status-step.completed .status-dot {
            background: var(--success);
            color: white;
        }

        .status-label {
            font-size: 0.8rem;
            text-align: center;
            color: #6c757d;
        }

        .status-step.active .status-label,
        .status-step.completed .status-label {
            color: var(--dark);
            font-weight: 600;
        }

        /* Transaction Details */
        .transaction-details {
            background: var(--gray-light);
            border-radius: var(--border-radius-sm);
            padding: 15px;
            margin: 15px 0;
        }

        .transaction-row {
            display: flex;
            justify-content: space-between;
            margin-bottom: 8px;
        }

        .transaction-row:last-child {
            margin-bottom: 0;
        }

        .transaction-label {
            color: var(--gray);
        }

        .transaction-value {
            font-weight: 600;
        }

        /* Success Message */
        .success-message {
            text-align: center;
            padding: 20px;
        }

        .success-icon {
            font-size: 3rem;
            color: var(--success);
            margin-bottom: 15px;
        }

        .success-title {
            font-size: 1.5rem;
            font-weight: bold;
            margin-bottom: 10px;
            color: var(--dark);
        }

        .success-text {
            color: var(--gray);
            margin-bottom: 20px;
        }

        .order-id {
            background: var(--gray-light);
            padding: 10px;
            border-radius: 5px;
            font-weight: bold;
            margin: 15px 0;
        }

        /* Tab Navigation */
        .tab-navigation {
            display: flex;
            border-bottom: 1px solid #eee;
            margin-bottom: 15px;
        }

        .tab-button {
            flex: 1;
            padding: 12px;
            text-align: center;
            background: none;
            border: none;
            cursor: pointer;
            font-weight: 600;
            color: var(--gray);
            border-bottom: 2px solid transparent;
        }

        .tab-button.active {
            color: var(--primary);
            border-bottom: 2px solid var(--primary);
        }

        .tab-content {
            display: none;
        }

        .tab-content.active {
            display: block;
        }

        /* Shipping Options */
        .shipping-option {
            border: 1px solid #ddd;
            border-radius: var(--border-radius-sm);
            padding: 10px;
            margin-bottom: 10px;
            cursor: pointer;
            transition: var(--transition);
        }

        .shipping-option:hover {
            border-color: var(--primary);
        }

        .shipping-option.selected {
            border-color: var(--primary);
            background: var(--primary-light);
        }

        .shipping-name {
            font-weight: bold;
            margin-bottom: 5px;
        }

        .shipping-price {
            color: var(--error);
            font-weight: bold;
        }

        /* Terms Checkbox */
        .terms-checkbox {
            display: flex;
            align-items: flex-start;
            gap: 10px;
            margin: 15px 0;
        }

        .terms-checkbox input {
            margin-top: 3px;
        }

        .terms-text {
            font-size: 0.9rem;
            color: var(--gray);
        }

        /* Loading Spinner */
        .loading {
            display: none;
            text-align: center;
            margin: 20px 0;
        }

        .loading-spinner {
            border: 4px solid #f3f3f3;
            border-top: 4px solid var(--primary);
            border-radius: 50%;
            width: 40px;
            height: 40px;
            animation: spin 1s linear infinite;
            margin: 0 auto;
        }

        /* Payment Methods Grid */
        .payment-methods-grid {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 10px;
            margin: 15px 0;
        }

        .payment-method {
            border: 1px solid #ddd;
            border-radius: var(--border-radius-sm);
            padding: 10px;
            text-align: center;
            cursor: pointer;
            transition: var(--transition);
        }

        .payment-method:hover {
            border-color: var(--primary);
            background: var(--primary-light);
        }

        .payment-method.selected {
            border-color: var(--primary);
            background: var(--primary-light);
        }

        .payment-icon {
            font-size: 1.5rem;
            margin-bottom: 5px;
            color: var(--primary);
        }

        .payment-name {
            font-size: 0.9rem;
            font-weight: 600;
        }

        /* QRIS Modal */
        .qris-modal {
            text-align: center;
        }

        .qris-header {
            padding: 15px;
            border-bottom: 1px solid #eee;
        }

        .qris-merchant {
            font-weight: bold;
            font-size: 1.1rem;
            margin-bottom: 5px;
        }

        .qris-amount {
            font-size: 1.5rem;
            font-weight: bold;
            color: var(--dark);
            margin: 10px 0;
        }

        .qris-code {
            margin: 20px 0;
            display: flex;
            justify-content: center;
        }

        .qris-code img {
            width: 220px;
            height: 220px;
            border: 1px solid #ddd;
            border-radius: var(--border-radius-sm);
            padding: 10px;
            background: white;
        }

        .qris-instruction {
            color: var(--gray);
            font-size: 0.9rem;
            margin: 15px 0;
        }

        /* Confirmation Section */
        .confirmation-section {
            margin-top: 20px;
            padding-top: 15px;
            border-top: 1px solid #eee;
        }

        /* Cart Page Styles */
        .cart-page {
            display: none;
            padding: 20px 0;
        }

        .cart-content {
            background: white;
            border-radius: var(--border-radius);
            padding: 20px;
            box-shadow: var(--box-shadow);
        }

        /* Promo Page Styles */
        .promo-page {
            display: none;
            padding: 20px 0;
        }

        /* Admin Page Styles */
        .admin-page {
            display: none;
            padding: 20px 0;
        }

        .admin-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
        }

        .admin-card {
            background: white;
            border-radius: var(--border-radius);
            padding: 20px;
            box-shadow: var(--box-shadow);
            margin-bottom: 20px;
        }

        /* Voucher Input Styles */
        .voucher-section {
            margin: 15px 0;
            padding: 15px;
            background: var(--gray-light);
            border-radius: var(--border-radius-sm);
        }

        .voucher-input {
            display: flex;
            gap: 10px;
        }

        .voucher-input input {
            flex: 1;
            padding: 10px 15px;
            border: 1px solid #ddd;
            border-radius: var(--border-radius-sm);
            font-size: 14px;
        }

        .voucher-input button {
            background: var(--primary);
            color: white;
            border: none;
            padding: 10px 15px;
            border-radius: var(--border-radius-sm);
            font-weight: 600;
            cursor: pointer;
            transition: var(--transition);
        }

        .voucher-input button:hover {
            background: var(--primary-dark);
        }

        /* Order Notes Styles */
        .order-notes {
            margin: 15px 0;
        }

        .order-notes textarea {
            width: 100%;
            padding: 12px 15px;
            border: 1px solid #ddd;
            border-radius: var(--border-radius-sm);
            font-size: 14px;
            resize: vertical;
            min-height: 80px;
            max-height: 120px;
        }

        .order-notes textarea:focus {
            outline: none;
            border-color: var(--primary);
        }

        /* Order Summary in Checkout */
        .order-summary-checkout {
            background: var(--gray-light);
            border-radius: var(--border-radius-sm);
            padding: 15px;
            margin: 15px 0;
        }

        .order-summary-checkout h4 {
            margin-bottom: 10px;
            color: var(--dark);
            font-weight: 600;
        }

        .checkout-product-item {
            display: flex;
            justify-content: space-between;
            margin-bottom: 8px;
            padding-bottom: 8px;
            border-bottom: 1px solid #eee;
        }

        .checkout-product-name {
            flex: 1;
        }

        .checkout-product-qty {
            color: var(--gray);
            margin: 0 10px;
        }

        .checkout-product-price {
            font-weight: 600;
        }

        /* Responsive - Perbaikan khusus untuk tampilan mobile */
        @media (max-width: 768px) {
            :root {
                --header-height: 60px;
                --bottom-nav-height: 60px;
            }
            
            .header-container {
                padding: 0 10px;
            }
            
            .mobile-menu-btn {
                display: flex;
            }
            
            .header-center {
                margin: 0 10px;
            }
            
            .search-bar input {
                padding: 10px 15px;
                font-size: 14px;
            }
            
            .search-bar button {
                padding: 6px 12px;
                font-size: 12px;
            }
            
            .header-right {
                gap: 8px;
            }
            
            .icon-link {
                font-size: 16px;
                padding: 6px;
            }
            
            .hero-carousel {
                height: 160px;
            }
            
            .carousel-content h2 {
                font-size: 16px;
            }
            
            .products-grid, .wishlist-grid {
                grid-template-columns: repeat(2, 1fr);
                gap: 10px;
            }
            
            .product-image {
                height: 140px;
            }
            
            /* Bottom navigation */
            .nav-item {
                font-size: 10px;
                padding: 6px 8px;
            }
            
            .nav-item i {
                font-size: 18px;
            }
            
            /* Header saat di-scroll */
            header.scrolled {
                box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
            }

            .admin-tabs {
                flex-wrap: wrap;
            }

            .admin-tab {
                flex: 1;
                min-width: 120px;
            }

            /* Perbaikan posisi konten pada mobile */
            .hide-header .page:not(#home-page) .container {
                padding-top: 5px;
            }
        }

        @media (max-width: 480px) {
            .logo h1 {
                font-size: 18px;
            }
            
            .search-bar input {
                padding: 8px 12px;
                font-size: 13px;
            }
            
            .search-bar button {
                padding: 5px 10px;
                font-size: 11px;
            }
            
            .icon-link {
                font-size: 15px;
            }
            
            .hero-carousel {
                height: 140px;
            }
            
            .carousel-content {
                padding: 10px;
            }
            
            .carousel-content h2 {
                font-size: 15px;
            }
            
            .carousel-content p {
                font-size: 12px;
            }
            
            .products-grid, .wishlist-grid {
                grid-template-columns: repeat(2, 1fr);
            }
            
            .footer-grid {
                grid-template-columns: 1fr;
            }
            
            .nav-item {
                max-width: 60px;
            }
        }

        /* Loading animation */
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }
        
        .product-card, .category-item {
            animation: fadeIn 0.5s ease forwards;
        }

        /* Notification */
        .notification {
            position: fixed;
            top: 20px;
            right: 20px;
            background: var(--primary);
            color: white;
            padding: 15px 20px;
            border-radius: var(--border-radius);
            box-shadow: var(--box-shadow-lg);
            z-index: 9999;
            opacity: 0;
            transform: translateX(100px);
            transition: all 0.3s ease;
            display: flex;
            align-items: center;
        }

        .notification i {
            margin-right: 10px;
            font-size: 20px;
        }

        .notification.show {
            opacity: 1;
            transform: translateX(0);
        }

        /* User Profile in Header */
        .user-profile-header {
            display: none;
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

        /* Login dengan Google */
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
                <div class="user-profile-header" id="user-profile-header">
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
    <!-- ... -->

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
            { id: 4, name: "ANLENE ACTIFIT 590 GR", price: 76500, category: "kesehatan", image: "https://via.placeholder.com/200x200/4FC3F7/FFFFFF?text=ANLENE+ACTIFIT", originalPrice: 85000 },
            { id: 5, name: "AQUA 600 ML", price: 3000, category: "minuman", image: "https://via.placeholder.com/200x200/4FC3F7/FFFFFF?text=AQUA" },
            { id: 6, name: "AQUA ISI ULANG GALON", price: 19000, category: "minuman", image: "https://via.placeholder.com/200x200/4FC3F7/FFFFFF?text=AQUA+GALON", originalPrice: 22000 },
            { id: 7, name: "ATTACK 750 GR SOFTENER", price: 26575, category: "rumahtangga", image: "https://via.placeholder.com/200x200/4FC3F7/FFFFFF?text=ATTACK" },
            { id: 8, name: "ATTACK JAZZ 800 GR", price: 18490, category: "rumahtangga", image: "https://via.placeholder.com/200x200/4FC3F7/FFFFFF?text=JAZZ", originalPrice: 21000 },
            { id: 9, name: "ATTACK SOFT 70 GR 1x6", price: 2000, category: "rumahtangga", image: "https://via.placeholder.com/200x200/4FC3F7/FFFFFF?text=ATTACK+SOFT" },
            { id: 10, name: "ATTACK SOFTENER 450 GR", price: 14000, category: "rumahtangga", image: "https://via.placeholder.com/200x200/4FC3F7/FFFFFF?text=SOFTENER" },
            { id: 11, name: "BAHAN SIRUP MELON", price: 6500, category: "minuman", image: "https://via.placeholder.com/200x200/4FC3F7/FFFFFF?text=MELON", originalPrice: 8000 },
            { id: 12, name: "BENDERA Krimer Putih 6 sachet", price: 9000, category: "makanan", image: "https://via.placeholder.com/200x200/4FC3F7/FFFFFF?text=BENDERA+PUTIH" },
            { id: 13, name: "BENDERA Coklat 6 sachet", price: 9000, category: "makanan", image: "https://via.placeholder.com/200x200/4FC3F7/FFFFFF?text=BENDERA+COKLAT" },
            { id: 14, name: "BENG BENG 22 GR COKLAT", price: 2000, category: "snack", image: "https://via.placeholder.com/200x200/FF5733/FFFFFF?text=BENG+BENG", originalPrice: 2500 },
        ];

        // Carousel data
        let carouselItems = [
            { 
                id: 1, 
                image: "https://images.unsplash.com/photo-1542838132-92c53300491e?ixlib=rb-1.2.1&auto=format&fit=crop&w=800&q=80",
                title: "Belanja Lebih Mudah & Cepat",
                description: "Gratis ongkir untuk pembelian di atas Rp100.000"
            },
            { 
                id: 2, 
                image: "https://images.unsplash.com/photo-1565299624946-b28f40a0ae38?ixlib=rb-1.2.1&auto=format&fit=crop&w=800&q=80",
                title: "Produk Segar Setiap Hari",
                description: "Dikirim langsung dari petani terpercaya"
            },
            { 
                id: 3, 
                image: "https://images.unsplash.com/photo-1565958011703-44f9829ba187?ixlib=rb-1.2.1&auto=format&fit=crop&w=800&q=80",
                title: "Diskon Spesial Akhir Tahun",
                description: "Dapatkan penawaran terbatas untuk produk pilihan"
            }
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
