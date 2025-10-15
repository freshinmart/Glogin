<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>NexusMart - Modern E-Commerce Experience</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <script src="https://cdn.jsdelivr.net/npm/tesseract.js@4/dist/tesseract.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/html2canvas@1.4.1/dist/html2canvas.min.js"></script>
    
    <!-- Firebase App (the core Firebase SDK) -->
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
    <!-- Firebase Authentication -->
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-auth.js"></script>
    <!-- Cloud Firestore -->
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-firestore.js"></script>
    
    <style>
        :root {
            --primary: #4361ee;
            --primary-dark: #3a56d4;
            --secondary: #7209b7;
            --accent: #f72585;
            --light: #f8f9fa;
            --dark: #212529;
            --success: #4cc9f0;
            --warning: #f8961e;
            --danger: #e63946;
            --gray: #6c757d;
            --gray-light: #e9ecef;
            --border-radius: 12px;
            --box-shadow: 0 10px 30px rgba(0, 0, 0, 0.08);
            --transition: all 0.3s ease;
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Poppins', sans-serif;
            background: linear-gradient(135deg, #f5f7fa 0%, #e4e8f0 100%);
            color: var(--dark);
            line-height: 1.6;
            min-height: 100vh;
        }

        .app-container {
            max-width: 480px;
            margin: 0 auto;
            background-color: white;
            min-height: 100vh;
            position: relative;
            box-shadow: 0 0 50px rgba(0, 0, 0, 0.1);
            overflow: hidden;
        }

        /* Header Styles */
        .header {
            background: linear-gradient(135deg, var(--primary) 0%, var(--secondary) 100%);
            color: white;
            padding: 20px 15px;
            text-align: center;
            position: sticky;
            top: 0;
            z-index: 100;
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
        }

        .header h1 {
            font-size: 1.5rem;
            font-weight: 600;
            margin-bottom: 5px;
        }

        .header p {
            font-size: 0.85rem;
            opacity: 0.9;
        }

        .back-btn {
            position: absolute;
            left: 15px;
            top: 20px;
            color: white;
            text-decoration: none;
            font-size: 1.2rem;
            width: 36px;
            height: 36px;
            display: flex;
            align-items: center;
            justify-content: center;
            border-radius: 50%;
            transition: var(--transition);
        }

        .back-btn:hover {
            background-color: rgba(255, 255, 255, 0.2);
        }

        /* Page Styles */
        .page {
            display: none;
            animation: fadeIn 0.5s ease;
            padding-bottom: 80px;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .active {
            display: block;
        }

        /* Search Bar */
        .search-bar {
            background-color: white;
            padding: 15px;
            display: flex;
            align-items: center;
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.05);
            position: sticky;
            top: 90px;
            z-index: 90;
        }

        .search-bar input {
            flex: 1;
            padding: 12px 15px;
            border: none;
            background-color: var(--gray-light);
            border-radius: 50px;
            font-size: 0.9rem;
            margin-right: 10px;
            transition: var(--transition);
        }

        .search-bar input:focus {
            outline: none;
            box-shadow: 0 0 0 2px var(--primary);
        }

        .search-bar i {
            color: var(--gray);
            font-size: 1.2rem;
        }

        /* Section Styles */
        .section {
            padding: 20px;
            border-bottom: 1px solid var(--gray-light);
        }

        .section-title {
            font-weight: 600;
            margin-bottom: 15px;
            font-size: 1.1rem;
            display: flex;
            align-items: center;
            color: var(--dark);
        }

        .section-title i {
            margin-right: 10px;
            color: var(--primary);
        }

        /* Store & Product Items */
        .store-item, .product-item, .cart-item {
            padding: 15px;
            border-radius: var(--border-radius);
            margin-bottom: 15px;
            background-color: white;
            box-shadow: var(--box-shadow);
            transition: var(--transition);
            border-left: 4px solid var(--primary);
        }

        .store-item:hover, .product-item:hover {
            transform: translateY(-5px);
            box-shadow: 0 15px 30px rgba(0, 0, 0, 0.1);
        }

        .store-name, .product-name {
            font-weight: 600;
            margin-bottom: 5px;
            color: var(--dark);
        }

        .store-address, .product-desc {
            font-size: 0.85rem;
            color: var(--gray);
            margin-bottom: 10px;
        }

        .product-price {
            color: var(--primary);
            font-weight: 600;
            margin-bottom: 5px;
            font-size: 1.1rem;
        }

        .product-stock {
            font-size: 0.8rem;
            padding: 3px 8px;
            border-radius: 20px;
            display: inline-block;
        }

        .stock-available {
            background-color: rgba(76, 201, 240, 0.1);
            color: var(--success);
        }

        .stock-low {
            background-color: rgba(248, 150, 30, 0.1);
            color: var(--warning);
        }

        .stock-out {
            background-color: rgba(230, 57, 70, 0.1);
            color: var(--danger);
        }

        /* Quantity Control */
        .quantity-control {
            display: flex;
            align-items: center;
            margin-top: 15px;
        }

        .quantity-btn {
            width: 36px;
            height: 36px;
            background: linear-gradient(135deg, var(--primary) 0%, var(--secondary) 100%);
            color: white;
            border: none;
            border-radius: 50%;
            font-size: 1rem;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: var(--transition);
        }

        .quantity-btn:hover {
            transform: scale(1.1);
        }

        .quantity {
            margin: 0 15px;
            font-weight: 600;
            font-size: 1.1rem;
            min-width: 40px;
            text-align: center;
        }

        /* Checkout Section */
        .checkout-section {
            background-color: white;
            padding: 20px;
            position: fixed;
            bottom: 0;
            left: 0;
            right: 0;
            max-width: 480px;
            margin: 0 auto;
            box-shadow: 0 -5px 20px rgba(0, 0, 0, 0.1);
            border-top-left-radius: 20px;
            border-top-right-radius: 20px;
            z-index: 100;
        }

        .total-price {
            font-weight: 700;
            font-size: 1.3rem;
            margin-bottom: 15px;
            text-align: center;
            color: var(--dark);
        }

        .checkout-btn {
            background: linear-gradient(135deg, var(--primary) 0%, var(--secondary) 100%);
            color: white;
            border: none;
            padding: 16px;
            width: 100%;
            border-radius: 50px;
            font-size: 1rem;
            font-weight: 600;
            cursor: pointer;
            transition: var(--transition);
            box-shadow: 0 5px 15px rgba(67, 97, 238, 0.3);
        }

        .checkout-btn:hover {
            transform: translateY(-3px);
            box-shadow: 0 8px 20px rgba(67, 97, 238, 0.4);
        }

        /* Form Styles */
        .form-group {
            margin-bottom: 20px;
        }

        .form-group label {
            display: block;
            margin-bottom: 8px;
            font-weight: 500;
            color: var(--dark);
        }

        .form-group input, .form-group select, .form-group textarea {
            width: 100%;
            padding: 14px;
            border: 1px solid var(--gray-light);
            border-radius: var(--border-radius);
            font-size: 1rem;
            transition: var(--transition);
        }

        .form-group input:focus, .form-group select:focus, .form-group textarea:focus {
            outline: none;
            border-color: var(--primary);
            box-shadow: 0 0 0 3px rgba(67, 97, 238, 0.1);
        }

        /* Payment Options */
        .payment-options {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 15px;
            margin-top: 10px;
        }

        .payment-option {
            border: 2px solid var(--gray-light);
            border-radius: var(--border-radius);
            padding: 15px;
            text-align: center;
            cursor: pointer;
            transition: var(--transition);
        }

        .payment-option:hover {
            border-color: var(--primary);
            transform: translateY(-3px);
        }

        .payment-option.selected {
            border-color: var(--primary);
            background-color: rgba(67, 97, 238, 0.05);
        }

        .payment-icon {
            font-size: 2rem;
            margin-bottom: 10px;
            color: var(--primary);
        }

        .payment-name {
            font-weight: 500;
            font-size: 0.9rem;
        }

        /* Payment Details */
        .payment-details {
            padding: 20px;
            background-color: var(--light);
            border-radius: var(--border-radius);
            margin-top: 20px;
            display: none;
            animation: fadeIn 0.5s ease;
        }

        .payment-qr {
            text-align: center;
            margin: 20px 0;
        }

        .payment-qr img {
            max-width: 200px;
            height: auto;
            border-radius: 10px;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
        }

        .confirm-btn {
            background: linear-gradient(135deg, var(--success) 0%, var(--primary) 100%);
            color: white;
            border: none;
            padding: 16px;
            width: 100%;
            border-radius: 50px;
            font-size: 1rem;
            font-weight: 600;
            cursor: pointer;
            transition: var(--transition);
            margin-top: 20px;
            box-shadow: 0 5px 15px rgba(76, 201, 240, 0.3);
        }

        .confirm-btn:hover {
            transform: translateY(-3px);
            box-shadow: 0 8px 20px rgba(76, 201, 240, 0.4);
        }

        .confirm-btn:disabled {
            background: var(--gray);
            cursor: not-allowed;
            transform: none;
            box-shadow: none;
        }

        /* Success Page */
        .success-message {
            text-align: center;
            padding: 40px 20px;
        }

        .success-icon {
            font-size: 5rem;
            color: var(--success);
            margin-bottom: 20px;
            animation: bounce 1s ease infinite;
        }

        @keyframes bounce {
            0%, 100% { transform: translateY(0); }
            50% { transform: translateY(-10px); }
        }

        .success-message h2 {
            font-size: 1.8rem;
            margin-bottom: 10px;
            color: var(--dark);
        }

        .success-message p {
            color: var(--gray);
            margin-bottom: 30px;
        }

        /* Tab Navigation */
        .tab-navigation {
            display: flex;
            background-color: white;
            border-bottom: 1px solid var(--gray-light);
            overflow-x: auto;
            padding: 0 15px;
        }

        .tab {
            padding: 15px 20px;
            font-weight: 500;
            border-bottom: 3px solid transparent;
            cursor: pointer;
            white-space: nowrap;
            transition: var(--transition);
        }

        .tab.active {
            border-bottom: 3px solid var(--primary);
            color: var(--primary);
        }

        /* Floating Cart */
        .floating-cart {
            position: fixed;
            bottom: 100px;
            right: 20px;
            background: linear-gradient(135deg, var(--primary) 0%, var(--secondary) 100%);
            color: white;
            width: 60px;
            height: 60px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 1.5rem;
            box-shadow: 0 5px 15px rgba(67, 97, 238, 0.4);
            cursor: pointer;
            z-index: 90;
            transition: var(--transition);
        }

        .floating-cart:hover {
            transform: scale(1.1);
        }

        .cart-badge {
            position: absolute;
            top: -5px;
            right: -5px;
            background-color: var(--accent);
            color: white;
            width: 24px;
            height: 24px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 0.8rem;
            font-weight: 600;
        }

        .cart-badge.hidden {
            display: none;
        }

        .hidden {
            display: none !important;
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
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
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
            background: linear-gradient(135deg, var(--primary) 0%, var(--secondary) 100%);
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

        /* QRIS Page */
        .qris-page {
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
            border-radius: var(--border-radius);
            padding: 10px;
            background: white;
        }

        .qris-instruction {
            color: var(--gray);
            font-size: 0.9rem;
            margin: 15px 0;
        }

        /* Proof upload area */
        .drop-area {
            border: 2px dashed var(--primary);
            border-radius: var(--border-radius);
            padding: 20px;
            text-align: center;
            margin-bottom: 20px;
            transition: all 0.3s ease;
            background-color: rgba(67, 97, 238, 0.05);
            cursor: pointer;
        }

        .drop-area:hover, .drop-area.dragover {
            background-color: rgba(67, 97, 238, 0.1);
            border-color: var(--primary-dark);
        }

        .drop-area p {
            color: var(--gray);
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
            border-radius: var(--border-radius);
        }

        .validation-message {
            margin: 15px 0;
            padding: 12px;
            border-radius: var(--border-radius);
            text-align: center;
            font-weight: 600;
        }

        .validation-success {
            background-color: rgba(76, 201, 240, 0.1);
            border: 1px solid var(--success);
            color: var(--success);
        }

        .validation-error {
            background-color: rgba(230, 57, 70, 0.1);
            border: 1px solid var(--danger);
            color: var(--danger);
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

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        /* Animations */
        @keyframes pulse {
            0% { transform: scale(1); }
            50% { transform: scale(1.05); }
            100% { transform: scale(1); }
        }

        .pulse {
            animation: pulse 2s infinite;
        }

        /* Product List */
        .product-list {
            margin-top: 20px;
        }

        .product-list-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 10px;
            border-bottom: 1px solid var(--gray-light);
        }

        .product-list-item:last-child {
            border-bottom: none;
        }

        .product-list-info {
            flex: 1;
        }

        .product-list-name {
            font-weight: 500;
        }

        .product-list-price {
            color: var(--primary);
            font-weight: 600;
        }

        .product-list-actions {
            display: flex;
            gap: 10px;
        }

        .edit-btn, .delete-btn {
            padding: 5px 10px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-size: 0.8rem;
        }

        .edit-btn {
            background: var(--warning);
            color: white;
        }

        .delete-btn {
            background: var(--danger);
            color: white;
        }

        /* Transaction History */
        .transaction-item {
            padding: 15px;
            border-radius: var(--border-radius);
            margin-bottom: 15px;
            background-color: white;
            box-shadow: var(--box-shadow);
        }

        .transaction-header {
            display: flex;
            justify-content: space-between;
            margin-bottom: 10px;
        }

        .transaction-id {
            font-weight: 600;
            color: var(--primary);
        }

        .transaction-date {
            color: var(--gray);
            font-size: 0.9rem;
        }

        .transaction-total {
            font-weight: 600;
            font-size: 1.1rem;
            margin-top: 10px;
            text-align: right;
        }

        .transaction-status {
            margin: 10px 0;
            padding: 5px 10px;
            border-radius: 20px;
            display: inline-block;
            font-size: 0.8rem;
            font-weight: 600;
        }

        .status-unpaid {
            background-color: rgba(248, 150, 30, 0.1);
            color: var(--warning);
        }

        .status-paid {
            background-color: rgba(76, 201, 240, 0.1);
            color: var(--success);
        }

        /* Empty Cart */
        .empty-cart {
            text-align: center;
            padding: 40px 20px;
            color: var(--gray);
        }

        .empty-cart i {
            font-size: 4rem;
            margin-bottom: 15px;
            color: var(--gray-light);
        }

        .empty-cart h4 {
            font-size: 1.2rem;
            margin-bottom: 10px;
            color: var(--dark);
        }

        /* User Profile */
        .user-profile {
            display: flex;
            align-items: center;
            gap: 10px;
            margin-bottom: 15px;
        }

        .user-avatar {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            object-fit: cover;
        }

        .user-info {
            flex: 1;
        }

        .user-name {
            font-weight: 600;
            font-size: 0.9rem;
        }

        .user-email {
            font-size: 0.8rem;
            color: var(--gray);
        }

        .logout-btn {
            background: var(--danger);
            color: white;
            border: none;
            padding: 8px 15px;
            border-radius: 20px;
            font-size: 0.8rem;
            cursor: pointer;
            transition: var(--transition);
        }

        .logout-btn:hover {
            background: #c82333;
        }

        /* Login Page */
        .login-container {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            padding: 20px;
            text-align: center;
        }

        .login-card {
            background: white;
            border-radius: var(--border-radius);
            box-shadow: var(--box-shadow);
            padding: 30px;
            width: 100%;
            max-width: 400px;
        }

        .login-logo {
            font-size: 3rem;
            color: var(--primary);
            margin-bottom: 20px;
        }

        .login-title {
            font-size: 1.8rem;
            margin-bottom: 10px;
            color: var(--dark);
        }

        .login-subtitle {
            color: var(--gray);
            margin-bottom: 30px;
        }

        .google-login-btn {
            background: white;
            color: #333;
            border: 1px solid #ddd;
            padding: 12px 20px;
            border-radius: var(--border-radius);
            font-size: 1rem;
            cursor: pointer;
            transition: var(--transition);
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
            width: 100%;
        }

        .google-login-btn:hover {
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
        }

        .google-icon {
            width: 20px;
            height: 20px;
        }

        /* Responsive Adjustments */
        @media (max-width: 480px) {
            .payment-options {
                grid-template-columns: 1fr;
            }
            
            .floating-cart {
                bottom: 20px;
                right: 20px;
            }
        }

        @media (min-width: 768px) {
            .app-container {
                max-width: 768px;
            }
            
            .payment-options {
                grid-template-columns: repeat(3, 1fr);
            }
            
            .floating-cart {
                bottom: 30px;
                right: 30px;
                width: 70px;
                height: 70px;
                font-size: 1.8rem;
            }
            
            .cart-badge {
                width: 28px;
                height: 28px;
                font-size: 0.9rem;
            }
        }
    </style>
</head>
<body>
    <div class="app-container">
        <!-- Floating Cart (Hanya tampil di halaman produk) -->
        <div class="floating-cart hidden" id="floating-cart" onclick="showPage('cart-page')">
            <i class="fas fa-shopping-cart"></i>
            <div class="cart-badge hidden" id="cart-badge">0</div>
        </div>

        <!-- Halaman Login -->
        <div id="login-page" class="page active">
            <div class="login-container">
                <div class="login-card">
                    <div class="login-logo">
                        <i class="fas fa-shopping-bag"></i>
                    </div>
                    <h1 class="login-title">NexusMart</h1>
                    <p class="login-subtitle">Your modern shopping experience</p>
                    <button class="google-login-btn" id="google-login-btn">
                        <img src="https://developers.google.com/identity/images/g-logo.png" alt="Google" class="google-icon">
                        Login with Google
                    </button>
                </div>
            </div>
        </div>

        <!-- Halaman Dashboard -->
        <div id="dashboard-page" class="page">
            <div class="header">
                <div class="user-profile">
                    <img id="user-avatar" src="" alt="User Avatar" class="user-avatar">
                    <div class="user-info">
                        <div class="user-name" id="user-name"></div>
                        <div class="user-email" id="user-email"></div>
                    </div>
                    <button class="logout-btn" onclick="logout()">Logout</button>
                </div>
                <h1>NexusMart Dashboard</h1>
                <p>Your modern shopping experience</p>
            </div>
            
            <div class="section">
                <div class="section-title">
                    <i class="fas fa-store"></i>
                    Quick Actions
                </div>
                
                <div class="payment-options">
                    <div class="payment-option" onclick="showPage('choose-store-page')">
                        <div class="payment-icon">
                            <i class="fas fa-store"></i>
                        </div>
                        <div class="payment-name">Choose Store</div>
                    </div>
                    
                    <div class="payment-option" onclick="showPage('products-page')">
                        <div class="payment-icon">
                            <i class="fas fa-shopping-bag"></i>
                        </div>
                        <div class="payment-name">Browse Products</div>
                    </div>
                    
                    <div class="payment-option" onclick="showPage('cart-page')">
                        <div class="payment-icon">
                            <i class="fas fa-shopping-cart"></i>
                        </div>
                        <div class="payment-name">View Cart</div>
                    </div>
                    
                    <div class="payment-option" onclick="showPage('transaction-history-page')">
                        <div class="payment-icon">
                            <i class="fas fa-history"></i>
                        </div>
                        <div class="payment-name">Riwayat Transaksi</div>
                    </div>
                    
                    <div class="payment-option" onclick="showAddProductModal()">
                        <div class="payment-icon">
                            <i class="fas fa-plus-circle"></i>
                        </div>
                        <div class="payment-name">Tambah Produk</div>
                    </div>
                </div>
            </div>
        </div>

        <!-- Halaman Pilih Toko -->
        <div id="choose-store-page" class="page">
            <div class="header">
                <a href="#" class="back-btn" onclick="showPage('dashboard-page')"><i class="fas fa-arrow-left"></i></a>
                <h1>Choose Store</h1>
                <p>Select your preferred location</p>
            </div>
            
            <div class="search-bar">
                <input type="text" placeholder="Search stores...">
                <i class="fas fa-search"></i>
            </div>
            
            <div class="section">
                <div class="store-item" onclick="selectStore('HYPERMART CILEGON')">
                    <div class="store-name">HYPERMART CILEGON</div>
                    <div class="store-address">MAYOFIELD MALL JL SA TIRTASAYA SIMPANG TIGA CLEGON BANTEN</div>
                    <div class="product-stock stock-available">Open • 08:00-22:00</div>
                </div>
                
                <div class="store-item" onclick="selectStore('HYPERMART SUPERMIALL KARAWACI')">
                    <div class="store-name">HYPERMART SUPERMIALL KARAWACI</div>
                    <div class="store-address">SUPERMILL KARAWACI JL BOULEVARD DIPONEGORO 105 LIPPO KARAWACI 1200 TANGERANG</div>
                    <div class="product-stock stock-available">Open • 08:00-22:00</div>
                </div>
            </div>
        </div>

        <!-- Halaman Produk -->
        <div id="products-page" class="page">
            <div class="header">
                <a href="#" class="back-btn" onclick="showPage('choose-store-page')"><i class="fas fa-arrow-left"></i></a>
                <h1>Browse Products</h1>
                <p id="selected-store">HYPERMART CILEGON</p>
            </div>
            
            <div class="tab-navigation">
                <div class="tab active" onclick="filterProducts('all')">All</div>
                <div class="tab" onclick="filterProducts('import fruits')">Import Fruits</div>
                <div class="tab" onclick="filterProducts('vegetables')">Vegetables</div>
                <div class="tab" onclick="filterProducts('dairy')">Dairy</div>
                <div class="tab" onclick="filterProducts('beverages')">Beverages</div>
            </div>
            
            <div class="search-bar">
                <input type="text" placeholder="Search products...">
                <i class="fas fa-search"></i>
            </div>
            
            <div class="section">
                <div class="section-title">
                    <i class="fas fa-apple-alt"></i>
                    Import Fruits
                </div>
                
                <div class="product-item">
                    <div class="product-name">Apel Fuji Super</div>
                    <div class="product-desc">Fresh imported Fuji apples from Japan</div>
                    <div class="product-price">Rp. 71.900 (1 kg)</div>
                    <div class="product-stock stock-low">Stock < 50</div>
                    <div class="quantity-control">
                        <button class="quantity-btn" onclick="decreaseQuantity('apel')">-</button>
                        <span class="quantity" id="apel-quantity">0</span>
                        <button class="quantity-btn" onclick="increaseQuantity('apel')">+</button>
                    </div>
                </div>
                
                <div class="product-item">
                    <div class="product-name">Angour Red Globe Aust</div>
                    <div class="product-desc">Premium Australian red globe grapes</div>
                    <div class="product-price">Rp. 42.950 (500 gr)</div>
                    <div class="product-stock stock-out">Out of stock</div>
                </div>
                
                <div class="product-item">
                    <div class="product-name">Lemon Import</div>
                    <div class="product-desc">Fresh imported lemons</div>
                    <div class="product-price">Rp. 39.655 (770 gr)</div>
                    <div class="product-stock stock-available">In stock</div>
                    <div class="quantity-control">
                        <button class="quantity-btn" onclick="decreaseQuantity('lemon')">-</button>
                        <span class="quantity" id="lemon-quantity">0</span>
                        <button class="quantity-btn" onclick="increaseQuantity('lemon')">+</button>
                    </div>
                </div>
                
                <!-- Menampilkan produk kustom -->
                <div id="custom-products-section"></div>
            </div>
        </div>

        <!-- Halaman Keranjang -->
        <div id="cart-page" class="page">
            <div class="header">
                <a href="#" class="back-btn" onclick="showPage('products-page')"><i class="fas fa-arrow-left"></i></a>
                <h1>Shopping Cart</h1>
                <p>Review your items</p>
            </div>
            
            <div class="section" id="cart-items">
                <!-- Items will be added here dynamically -->
                <div class="empty-cart" id="empty-cart-message">
                    <i class="fas fa-shopping-cart"></i>
                    <h4>Keranjang Belanja Kosong</h4>
                    <p>Mulai belanja sekarang dan tambahkan produk ke keranjang Anda</p>
                </div>
            </div>
            
            <div class="checkout-section">
                <div class="total-price" id="cart-total">Total Belanja: Rp 0</div>
                <button class="checkout-btn" id="checkout-button" onclick="showPage('checkout-page')" disabled>PROCEED TO CHECKOUT <i class="fas fa-lock"></i></button>
            </div>
        </div>

        <!-- Halaman Checkout -->
        <div id="checkout-page" class="page">
            <div class="header">
                <a href="#" class="back-btn" onclick="showPage('cart-page')"><i class="fas fa-arrow-left"></i></a>
                <h1>Checkout</h1>
                <p>Complete your purchase</p>
            </div>
            
            <div class="section">
                <div class="section-title">
                    <i class="fas fa-user"></i>
                    Customer Information
                </div>
                
                <div class="form-group">
                    <label for="customer-name">Nama Customer</label>
                    <input type="text" id="customer-name" value="Muji Slamet" oninput="validateCheckoutForm()">
                </div>
                
                <div class="form-group">
                    <label for="customer-phone">Phone Number</label>
                    <input type="text" id="customer-phone" value="+62 888 1365 814" oninput="validateCheckoutForm()">
                </div>
                
                <div class="form-group" id="address-field" style="display: none;">
                    <label for="customer-address">Alamat Pengiriman</label>
                    <textarea id="customer-address" rows="3" placeholder="Masukkan alamat lengkap untuk pengiriman" oninput="validateCheckoutForm()"></textarea>
                </div>
            </div>
            
            <div class="section">
                <div class="section-title">
                    <i class="fas fa-truck"></i>
                    Delivery Method
                </div>
                
                <div class="payment-options">
                    <div class="payment-option selected" onclick="selectDeliveryOption(this, 'delivery')">
                        <div class="payment-icon">
                            <i class="fas fa-motorcycle"></i>
                        </div>
                        <div class="payment-name">Delivery (+Rp 5,000)</div>
                    </div>
                    
                    <div class="payment-option" onclick="selectDeliveryOption(this, 'park-pick')">
                        <div class="payment-icon">
                            <i class="fas fa-car"></i>
                        </div>
                        <div class="payment-name">Park & Pick</div>
                    </div>
                </div>
            </div>
            
            <div class="section">
                <div class="section-title">
                    <i class="fas fa-credit-card"></i>
                    Payment Method
                </div>
                
                <div class="payment-options">
                    <div class="payment-option selected" onclick="selectPaymentOption(this, 'qris')">
                        <div class="payment-icon">
                            <i class="fas fa-qrcode"></i>
                        </div>
                        <div class="payment-name">QRIS</div>
                    </div>
                </div>
            </div>
            
            <div class="checkout-section">
                <button class="checkout-btn" id="continue-payment-button" onclick="processPayment()" disabled>Continue to Payment <i class="fas fa-arrow-right"></i></button>
            </div>
        </div>

        <!-- Halaman QRIS -->
        <div id="qris-page" class="page qris-page">
            <div class="header">
                <h1>QRIS Payment</h1>
                <p>Scan the QR code to complete payment</p>
            </div>
            
            <div class="section">
                <div class="qris-amount" id="qris-amount">Rp 0</div>
                <div class="qris-instruction">
                    Minta pelanggan menindai kode QRIS untuk melakukan pembayaran
                </div>
                <div class="qris-code">
                    <img id="qris-image" src="" alt="QR Code">
                </div>
                <div class="loading" id="qris-loading">
                    <div class="loading-spinner"></div>
                    <p>Sedang memproses...</p>
                </div>
                <div class="qris-instruction">
                    Scan QR code di atas untuk melakukan pembayaran
                </div>
                <button class="confirm-btn" id="save-qris-btn" onclick="saveQRISProgress()">Simpan & Lanjutkan</button>
            </div>
        </div>

        <!-- Halaman Konfirmasi Pembayaran -->
        <div id="confirmation-page" class="page">
            <div class="header">
                <a href="#" class="back-btn" onclick="showPage('qris-page')"><i class="fas fa-arrow-left"></i></a>
                <h1>Konfirmasi Pembayaran</h1>
                <p>Upload bukti pembayaran Anda</p>
            </div>
            
            <div class="section">
                <div class="section-title">
                    <i class="fas fa-receipt"></i>
                    Upload Bukti Pembayaran
                </div>
                
                <p style="margin-bottom: 15px; text-align: center;">Silakan upload screenshot bukti pembayaran QRIS Anda</p>
                
                <div class="drop-area" id="proof-drop-area">
                    <i class="fas fa-cloud-upload-alt"></i>
                    <p>Drag & drop file bukti pembayaran di sini atau klik untuk memilih file</p>
                    <input type="file" id="proof-input" accept="image/*" style="display: none;">
                </div>
                
                <div class="loading" id="upload-loading">
                    <div class="loading-spinner"></div>
                    <p>Memproses gambar...</p>
                </div>
                
                <div class="proof-preview" id="proof-preview" style="display: none;">
                    <img id="proof-image" src="" alt="Preview Bukti Pembayaran">
                </div>
                
                <div class="validation-message" id="validation-message" style="display: none;"></div>
                
                <button class="confirm-btn" id="submit-proof-button" style="display: none;" onclick="submitProof()">Submit Bukti Pembayaran</button>
            </div>
        </div>

        <!-- Halaman Status Transaksi -->
        <div id="status-page" class="page">
            <div class="header">
                <h1>Status Transaksi</h1>
                <p>Pembayaran berhasil dikonfirmasi</p>
            </div>
            
            <div class="section">
                <div class="success-message">
                    <div class="success-icon">
                        <i class="fas fa-check-circle"></i>
                    </div>
                    <h2>Pembayaran Berhasil!</h2>
                    <p>Pembayaran Anda telah berhasil dikonfirmasi.</p>
                    <p>Pesanan Anda sedang dipersiapkan.</p>
                    
                    <div class="checkout-section" style="position: relative; box-shadow: none; margin-top: 20px;">
                        <button class="checkout-btn" onclick="sendToWhatsApp()">Kirim ke WhatsApp</button>
                        <button class="checkout-btn" style="background: var(--gray); margin-top: 10px;" onclick="showPage('products-page')">Kembali Berbelanja</button>
                    </div>
                </div>
            </div>
        </div>

        <!-- Halaman Riwayat Transaksi -->
        <div id="transaction-history-page" class="page">
            <div class="header">
                <a href="#" class="back-btn" onclick="showPage('dashboard-page')"><i class="fas fa-arrow-left"></i></a>
                <h1>Riwayat Transaksi</h1>
                <p>Daftar transaksi yang sudah dibayar</p>
            </div>
            
            <div class="section" id="transaction-history">
                <!-- Transactions will be added here dynamically -->
                <div class="empty-cart">
                    <i class="fas fa-receipt"></i>
                    <h4>Belum Ada Transaksi</h4>
                    <p>Transaksi Anda akan muncul di sini setelah melakukan pembayaran</p>
                </div>
            </div>
        </div>

        <!-- Tambah Produk Modal -->
        <div class="modal" id="add-product-modal">
            <div class="modal-content">
                <div class="modal-header">
                    <h3>Tambah Produk Baru</h3>
                    <button class="modal-close" id="close-add-product">&times;</button>
                </div>
                <div class="modal-body">
                    <div class="section-title">
                        <i class="fas fa-plus-circle"></i>
                        Tambah Produk Manual
                    </div>
                    
                    <div class="form-group">
                        <label for="product-store">Pilih Toko</label>
                        <select id="product-store">
                            <option value="HYPERMART CILEGON">HYPERMART CILEGON</option>
                            <option value="HYPERMART SUPERMIALL KARAWACI">HYPERMART SUPERMIALL KARAWACI</option>
                        </select>
                    </div>
                    
                    <div class="form-group">
                        <label for="product-name">Nama Produk</label>
                        <input type="text" id="product-name" placeholder="Masukkan nama produk">
                    </div>
                    
                    <div class="form-group">
                        <label for="product-price">Harga Produk</label>
                        <input type="number" id="product-price" placeholder="Masukkan harga produk">
                    </div>
                    
                    <div class="form-group">
                        <label for="product-category">Kategori Produk</label>
                        <select id="product-category">
                            <option value="import fruits">Import Fruits</option>
                            <option value="vegetables">Vegetables</option>
                            <option value="dairy">Dairy</option>
                            <option value="beverages">Beverages</option>
                            <option value="other">Other</option>
                        </select>
                    </div>
                    
                    <button class="confirm-btn" id="add-manual-product-btn" onclick="addManualProduct()">Tambah Produk</button>
                    
                    <div class="section-title" style="margin-top: 30px;">
                        <i class="fas fa-receipt"></i>
                        Tambah Produk dari Struk (OCR)
                    </div>
                    
                    <p style="margin-bottom: 15px; text-align: center;">Upload gambar struk untuk mengekstrak produk secara otomatis</p>
                    
                    <div class="form-group">
                        <label for="receipt-store">Pilih Toko</label>
                        <select id="receipt-store">
                            <option value="HYPERMART CILEGON">HYPERMART CILEGON</option>
                            <option value="HYPERMART SUPERMIALL KARAWACI">HYPERMART SUPERMIALL KARAWACI</option>
                        </select>
                    </div>
                    
                    <div class="drop-area" id="receipt-drop-area">
                        <i class="fas fa-file-invoice"></i>
                        <p>Drag & drop file struk di sini atau klik untuk memilih file</p>
                        <input type="file" id="receipt-input" accept="image/*" style="display: none;">
                    </div>
                    
                    <div class="loading" id="receipt-loading">
                        <div class="loading-spinner"></div>
                        <p>Memproses struk...</p>
                    </div>
                    
                    <div class="product-list" id="extracted-products" style="display: none;">
                        <div class="section-title">
                            <i class="fas fa-list"></i>
                            Produk yang Ditemukan
                        </div>
                        <!-- Products will be added here dynamically -->
                    </div>
                </div>
            </div>
        </div>
    </div>

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

        // Data produk
        const products = {
            apel: { price: 71900, quantity: 0, name: "APEL FUJI SUPER KG", store: "HYPERMART CILEGON", category: "import fruits" },
            lemon: { price: 39655, quantity: 0, name: "Lemon Import", store: "HYPERMART CILEGON", category: "import fruits" }
        };
        
        // Status keywords for OCR validation
        const failedStatusKeywords = [
            'diproses', 'invalid', 'menunggu', 'pending', 'pin salah', 'menunggu pembayaran', 
            'saldo tidak cukup', 'koneksi terputus', 'jaringan', 'dibatalkan', 'expired', 
            'waktu habis', 'ditolak', 'declined', 'tidak berhasil', 'transaksi gagal', 'gagal'
        ];
        
        const successStatusKeywords = [
            'sukses', 'berhasil', 'terkirim', 'selesai', 'lunas', 'tuntas', 'terbayar', 
            'approved', 'completed', 'pembayaran diterima', 'transaksi dikonfirmasi', 
            'dana telah diteruskan'
        ];
        
        // QRIS Static Data
        const QRIS_STATIC_DATA = "00020101021126610014COM.GO-JEK.WWW01189360091430687178460210G0687178460303UMI51440014ID.CO.QRIS.WWW0215ID10243600706340303UMI5204541153033605802ID5919Warung 68, SAWANGAN6005DEPOK61051651962070703A0163046826";
        
        let currentTransactionAmount = 0;
        let selectedDeliveryMethod = 'delivery';
        let selectedPaymentMethod = 'qris';
        let proofUploaded = false;
        let ocrValidationPassed = false;
        let orderId = 'ORD-' + Date.now();
        let selectedStore = 'HYPERMART CILEGON';
        let transactionHistory = [];
        let customProducts = [];
        let extractedProductsList = [];
        let currentQRISData = null;
        let currentUser = null;

        // Fungsi untuk menampilkan halaman tertentu
        function showPage(pageId) {
            document.querySelectorAll('.page').forEach(page => {
                page.classList.remove('active');
            });
            document.getElementById(pageId).classList.add('active');
            
            // Tampilkan/sembunyikan floating cart HANYA di halaman products-page
            const floatingCart = document.getElementById('floating-cart');
            if (pageId === 'products-page') {
                floatingCart.classList.remove('hidden');
            } else {
                floatingCart.classList.add('hidden');
            }
            
            // Update cart badge
            updateCartBadge();
            
            // Jika halaman riwayat transaksi, muat data
            if (pageId === 'transaction-history-page') {
                loadTransactionHistory();
            }
            
            // Jika halaman keranjang, render ulang
            if (pageId === 'cart-page') {
                renderCartPage();
            }
            
            // Jika halaman produk, render produk kustom
            if (pageId === 'products-page') {
                renderCustomProducts();
            }
        }
        
        // Fungsi untuk menambah/mengurangi kuantitas produk
        function increaseQuantity(product) {
            products[product].quantity += 1;
            document.getElementById(`${product}-quantity`).textContent = products[product].quantity;
            updateCartBadge();
        }
        
        function decreaseQuantity(product) {
            if (products[product].quantity > 0) {
                products[product].quantity -= 1;
                document.getElementById(`${product}-quantity`).textContent = products[product].quantity;
                updateCartBadge();
            }
        }
        
        // Fungsi untuk mengupdate badge keranjang
        function updateCartBadge() {
            let itemCount = 0;
            for (const product in products) {
                itemCount += products[product].quantity;
            }
            
            // Juga hitung produk kustom
            customProducts.forEach(product => {
                if (product.quantity) {
                    itemCount += product.quantity;
                }
            });
            
            const cartBadge = document.getElementById('cart-badge');
            cartBadge.textContent = itemCount;
            
            if (itemCount === 0) {
                cartBadge.classList.add('hidden');
            } else {
                cartBadge.classList.remove('hidden');
            }
        }
        
        // Fungsi untuk memilih toko
        function selectStore(storeName) {
            selectedStore = storeName;
            document.getElementById('selected-store').textContent = storeName;
            showPage('products-page');
        }
        
        // Fungsi untuk memilih opsi pengiriman
        function selectDeliveryOption(element, method) {
            document.querySelectorAll('.payment-option').forEach(option => {
                option.classList.remove('selected');
            });
            element.classList.add('selected');
            selectedDeliveryMethod = method;
            
            // Tampilkan/sembunyikan field alamat
            const addressField = document.getElementById('address-field');
            if (method === 'delivery') {
                addressField.style.display = 'block';
            } else {
                addressField.style.display = 'none';
            }
            
            validateCheckoutForm();
        }
        
        // Fungsi untuk memilih opsi pembayaran
        function selectPaymentOption(element, method) {
            document.querySelectorAll('.payment-option').forEach(option => {
                option.classList.remove('selected');
            });
            element.classList.add('selected');
            selectedPaymentMethod = method;
        }
        
        // Filter produk berdasarkan kategori
        function filterProducts(category) {
            // Update active tab
            document.querySelectorAll('.tab').forEach(tab => {
                tab.classList.remove('active');
            });
            event.target.classList.add('active');
            
            // Filter produk berdasarkan kategori
            const productItems = document.querySelectorAll('.product-item');
            productItems.forEach(item => {
                const productName = item.querySelector('.product-name').textContent.toLowerCase();
                let showItem = false;
                
                if (category === 'all') {
                    showItem = true;
                } else if (category === 'import fruits') {
                    showItem = productName.includes('apel') || productName.includes('lemon') || 
                               productName.includes('angour') || productName.includes('import');
                } else if (category === 'vegetables') {
                    showItem = productName.includes('sayur') || productName.includes('vegetable');
                } else if (category === 'dairy') {
                    showItem = productName.includes('susu') || productName.includes('dairy') || 
                               productName.includes('keju') || productName.includes('cheese');
                } else if (category === 'beverages') {
                    showItem = productName.includes('minuman') || productName.includes('beverage') || 
                               productName.includes('jus') || productName.includes('juice');
                }
                
                item.style.display = showItem ? 'block' : 'none';
            });
            
            // Filter produk kustom
            const customProductItems = document.querySelectorAll('.custom-product-item');
            customProductItems.forEach(item => {
                const productCategory = item.getAttribute('data-category');
                let showItem = false;
                
                if (category === 'all') {
                    showItem = true;
                } else {
                    showItem = productCategory === category;
                }
                
                item.style.display = showItem ? 'block' : 'none';
            });
        }
        
        // Validasi form checkout
        function validateCheckoutForm() {
            const customerName = document.getElementById('customer-name').value;
            const customerPhone = document.getElementById('customer-phone').value;
            const customerAddress = document.getElementById('customer-address').value;
            const continueButton = document.getElementById('continue-payment-button');
            
            let isValid = customerName && customerPhone;
            
            if (selectedDeliveryMethod === 'delivery') {
                isValid = isValid && customerAddress;
            }
            
            continueButton.disabled = !isValid;
        }
        
        // Proses pembayaran
        function processPayment() {
            // Hitung total
            let total = 0;
            for (const product in products) {
                if (products[product].quantity > 0) {
                    total += products[product].price * products[product].quantity;
                }
            }
            
            // Juga hitung produk kustom
            customProducts.forEach(product => {
                if (product.quantity > 0) {
                    total += product.price * product.quantity;
                }
            });
            
            currentTransactionAmount = total;
            
            // Generate QRIS
            generateQRIS(currentTransactionAmount + (selectedDeliveryMethod === 'delivery' ? 5000 : 0));
        }
        
        // Generate QRIS
        async function generateQRIS(amount) {
            const qrisLoading = document.getElementById('qris-loading');
            const qrisImage = document.getElementById('qris-image');
            const qrisAmount = document.getElementById('qris-amount');
            const saveQRISBtn = document.getElementById('save-qris-btn');
            
            qrisLoading.style.display = 'block';
            qrisImage.style.display = 'none';
            saveQRISBtn.disabled = true;
            qrisAmount.textContent = `Rp ${amount.toLocaleString('id-ID')}`;
            
            try {
                // Encode QRIS data for URL
                const encodedQris = encodeURIComponent(QRIS_STATIC_DATA);
                
                // Make API request
                const response = await fetch(`https://cekid-ariepulsa.my.id/api/?qris_data=${encodedQris}&nominal=${amount}`);
                const data = await response.json();
                
                if (data.status === 'success') {
                    qrisImage.src = data.link_qris;
                    qrisImage.style.display = 'block';
                    saveQRISBtn.disabled = false;
                    
                    // Simpan data QRIS untuk progress
                    currentQRISData = {
                        amount: amount,
                        qrCode: data.link_qris,
                        transactionId: orderId
                    };
                    
                    // Tampilkan halaman QRIS
                    showPage('qris-page');
                } else {
                    alert('Gagal generate QRIS: ' + (data.message || 'Unknown error'));
                    saveQRISBtn.disabled = false;
                }
            } catch (error) {
                alert('Terjadi kesalahan: ' + error.message);
                saveQRISBtn.disabled = false;
            } finally {
                qrisLoading.style.display = 'none';
            }
        }
        
        // Simpan progress QRIS ke database
        function saveQRISProgress() {
            if (!currentUser) {
                alert('Anda harus login terlebih dahulu');
                return;
            }
            
            const customerName = document.getElementById('customer-name').value;
            const customerPhone = document.getElementById('customer-phone').value;
            const customerAddress = document.getElementById('customer-address').value;
            
            const transaction = {
                id: orderId,
                date: new Date().toLocaleDateString('id-ID'),
                time: new Date().toLocaleTimeString('id-ID'),
                store: selectedStore,
                customerName: customerName,
                customerPhone: customerPhone,
                deliveryMethod: selectedDeliveryMethod,
                customerAddress: customerAddress,
                items: [],
                subtotal: currentTransactionAmount,
                deliveryFee: selectedDeliveryMethod === 'delivery' ? 5000 : 0,
                total: currentTransactionAmount + (selectedDeliveryMethod === 'delivery' ? 5000 : 0),
                paymentMethod: selectedPaymentMethod,
                status: 'unpaid', // Status belum dibayar
                qrCode: currentQRISData.qrCode,
                userId: currentUser.uid,
                createdAt: firebase.firestore.FieldValue.serverTimestamp()
            };
            
            // Tambahkan item ke transaksi (produk standar)
            for (const product in products) {
                if (products[product].quantity > 0) {
                    transaction.items.push({
                        name: products[product].name,
                        quantity: products[product].quantity,
                        price: products[product].price,
                        subtotal: products[product].price * products[product].quantity
                    });
                }
            }
            
            // Tambahkan item ke transaksi (produk kustom)
            customProducts.forEach(product => {
                if (product.quantity > 0) {
                    transaction.items.push({
                        name: product.name,
                        quantity: product.quantity,
                        price: product.price,
                        subtotal: product.price * product.quantity
                    });
                }
            });
            
            // Simpan ke Firestore
            db.collection('transactions').doc(orderId).set(transaction)
                .then(() => {
                    console.log('Transaksi berhasil disimpan ke database');
                    // Tampilkan halaman konfirmasi pembayaran
                    showPage('confirmation-page');
                })
                .catch((error) => {
                    console.error('Error menyimpan transaksi: ', error);
                    alert('Terjadi kesalahan saat menyimpan transaksi');
                });
        }
        
        // Setup proof upload functionality
        function setupProofUpload() {
            const proofDropArea = document.getElementById('proof-drop-area');
            const proofInput = document.getElementById('proof-input');
            const proofPreview = document.getElementById('proof-preview');
            const proofImage = document.getElementById('proof-image');
            const uploadLoading = document.getElementById('upload-loading');
            const submitProofButton = document.getElementById('submit-proof-button');
            const validationMessage = document.getElementById('validation-message');
            
            proofDropArea.addEventListener('click', () => {
                proofInput.click();
            });
            
            proofInput.addEventListener('change', async (e) => {
                if (e.target.files.length) {
                    const file = e.target.files[0];
                    const imageUrl = URL.createObjectURL(file);
                    
                    // Show preview
                    proofImage.src = imageUrl;
                    proofPreview.style.display = 'block';
                    
                    uploadLoading.style.display = 'block';
                    validationMessage.style.display = 'none';
                    submitProofButton.style.display = 'none';
                    
                    try {
                        // Process image with Tesseract OCR
                        const { data: { text } } = await Tesseract.recognize(
                            file,
                            'eng+ind', // English and Indonesian
                            { 
                                logger: m => console.log(m) 
                            }
                        );
                        
                        console.log('OCR Result:', text);
                        
                        // Check status keywords
                        const statusCheck = checkStatusKeywords(text);
                        
                        // Check if OCR validation passed
                        ocrValidationPassed = (statusCheck.status === 'success');
                        
                        if (ocrValidationPassed) {
                            validationMessage.textContent = 'Pembayaran berhasil dikonfirmasi! Status: ' + statusCheck.keyword;
                            validationMessage.className = 'validation-message validation-success';
                            validationMessage.style.display = 'block';
                            submitProofButton.style.display = 'block';
                            proofUploaded = true;
                        } else {
                            validationMessage.textContent = 'Maaf, bukti pembayaran tidak valid. Status: ' + statusCheck.keyword;
                            validationMessage.className = 'validation-message validation-error';
                            validationMessage.style.display = 'block';
                            submitProofButton.style.display = 'none';
                            proofUploaded = false;
                        }
                        
                    } catch (error) {
                        console.error('OCR Error:', error);
                        validationMessage.textContent = 'Terjadi kesalahan saat memproses bukti pembayaran. Silakan coba lagi.';
                        validationMessage.className = 'validation-message validation-error';
                        validationMessage.style.display = 'block';
                    } finally {
                        uploadLoading.style.display = 'none';
                        URL.revokeObjectURL(imageUrl);
                    }
                }
            });
        }
        
        // Submit proof
        function submitProof() {
            if (!proofUploaded || !ocrValidationPassed) {
                alert('Bukti pembayaran belum valid. Silakan periksa kembali.');
                return;
            }
            
            // Update status transaksi menjadi paid di database
            updateTransactionStatus(orderId, 'paid');
            
            // Tampilkan halaman status
            showPage('status-page');
            
            // Juga arahkan ke WhatsApp untuk konfirmasi pesanan
            sendToWhatsApp();
        }
        
        // Update status transaksi di database
        function updateTransactionStatus(transactionId, status) {
            if (!currentUser) return;
            
            db.collection('transactions').doc(transactionId).update({
                status: status,
                updatedAt: firebase.firestore.FieldValue.serverTimestamp()
            })
            .then(() => {
                console.log('Status transaksi berhasil diperbarui');
                
                // Reset keranjang jika status paid
                if (status === 'paid') {
                    // Reset keranjang
                    for (const product in products) {
                        products[product].quantity = 0;
                    }
                    
                    // Reset produk kustom
                    customProducts.forEach(product => {
                        product.quantity = 0;
                    });
                    
                    updateCartBadge();
                    
                    // Update UI produk
                    renderCustomProducts();
                }
            })
            .catch((error) => {
                console.error('Error memperbarui status transaksi: ', error);
            });
        }
        
        // Lanjutkan pembayaran untuk transaksi yang belum dibayar
        function continuePayment(transactionId) {
            if (!currentUser) return;
            
            db.collection('transactions').doc(transactionId).get()
                .then((doc) => {
                    if (doc.exists) {
                        const transaction = doc.data();
                        
                        // Set data transaksi yang akan dilanjutkan
                        orderId = transactionId;
                        currentTransactionAmount = transaction.subtotal;
                        selectedStore = transaction.store;
                        selectedDeliveryMethod = transaction.deliveryMethod;
                        selectedPaymentMethod = transaction.paymentMethod;
                        
                        // Isi form customer
                        document.getElementById('customer-name').value = transaction.customerName;
                        document.getElementById('customer-phone').value = transaction.customerPhone;
                        if (transaction.deliveryMethod === 'delivery') {
                            document.getElementById('customer-address').value = transaction.customerAddress;
                        }
                        
                        // Tampilkan QRIS yang sudah ada
                        showQRISPage(transaction);
                    }
                })
                .catch((error) => {
                    console.error('Error mengambil transaksi: ', error);
                });
        }
        
        // Tampilkan halaman QRIS untuk transaksi yang sudah ada
        function showQRISPage(transaction) {
            const qrisImage = document.getElementById('qris-image');
            const qrisAmount = document.getElementById('qris-amount');
            
            qrisAmount.textContent = `Rp ${transaction.total.toLocaleString('id-ID')}`;
            
            if (transaction.qrCode) {
                qrisImage.src = transaction.qrCode;
                qrisImage.style.display = 'block';
                currentQRISData = {
                    amount: transaction.total,
                    qrCode: transaction.qrCode,
                    transactionId: transaction.id
                };
            }
            
            showPage('qris-page');
        }
        
        // Check status keywords in OCR text
        function checkStatusKeywords(text) {
            const normalizedText = text.toLowerCase();
            
            // Check for failed status keywords
            for (const keyword of failedStatusKeywords) {
                if (normalizedText.includes(keyword.toLowerCase())) {
                    return {
                        status: 'failed',
                        keyword: keyword,
                        message: `Status transaksi tidak valid: ${keyword}`
                    };
                }
            }
            
            // Check for success status keywords
            for (const keyword of successStatusKeywords) {
                if (normalizedText.includes(keyword.toLowerCase())) {
                    return {
                        status: 'success',
                        keyword: keyword,
                        message: `Status transaksi valid: ${keyword}`
                    };
                }
            }
            
            // If no status keywords found
            return {
                status: 'unknown',
                message: 'Status transaksi tidak terdeteksi'
            };
        }
        
        // Render halaman keranjang
        function renderCartPage() {
            const cartItems = document.getElementById('cart-items');
            const emptyCartMessage = document.getElementById('empty-cart-message');
            let hasItems = false;
            let cartHTML = '';
            
            // Render produk standar
            for (const product in products) {
                if (products[product].quantity > 0) {
                    hasItems = true;
                    const subtotal = products[product].price * products[product].quantity;
                    
                    cartHTML += `
                        <div class="cart-item">
                            <div class="product-name">${products[product].name}</div>
                            <div class="product-desc">Qty ${products[product].quantity} * Rp. ${products[product].price.toLocaleString('id-ID')}</div>
                            <div class="product-price">Sub Total : Rp. ${subtotal.toLocaleString('id-ID')}</div>
                            <div class="quantity-control">
                                <button class="quantity-btn" onclick="decreaseCartQuantity('${product}')">-</button>
                                <span class="quantity">${products[product].quantity} Kg</span>
                                <button class="quantity-btn" onclick="increaseCartQuantity('${product}')">+</button>
                            </div>
                        </div>
                    `;
                }
            }
            
            // Render produk kustom
            customProducts.forEach((product, index) => {
                if (product.quantity > 0) {
                    hasItems = true;
                    const subtotal = product.price * product.quantity;
                    
                    cartHTML += `
                        <div class="cart-item">
                            <div class="product-name">${product.name}</div>
                            <div class="product-desc">Qty ${product.quantity} * Rp. ${product.price.toLocaleString('id-ID')}</div>
                            <div class="product-price">Sub Total : Rp. ${subtotal.toLocaleString('id-ID')}</div>
                            <div class="quantity-control">
                                <button class="quantity-btn" onclick="decreaseCustomCartQuantity(${index})">-</button>
                                <span class="quantity">${product.quantity} ${product.unit || 'pcs'}</span>
                                <button class="quantity-btn" onclick="increaseCustomCartQuantity(${index})">+</button>
                            </div>
                        </div>
                    `;
                }
            });
            
            if (hasItems) {
                emptyCartMessage.style.display = 'none';
                cartItems.innerHTML = cartHTML;
            } else {
                emptyCartMessage.style.display = 'block';
                cartItems.innerHTML = '';
            }
            
            updateCart();
        }
        
        // Fungsi untuk menambah/mengurangi kuantitas di keranjang
        function increaseCartQuantity(product) {
            products[product].quantity += 1;
            renderCartPage();
            updateCartBadge();
        }
        
        function decreaseCartQuantity(product) {
            if (products[product].quantity > 0) {
                products[product].quantity -= 1;
                renderCartPage();
                updateCartBadge();
            }
        }
        
        function increaseCustomCartQuantity(index) {
            customProducts[index].quantity += 1;
            renderCartPage();
            updateCartBadge();
        }
        
        function decreaseCustomCartQuantity(index) {
            if (customProducts[index].quantity > 0) {
                customProducts[index].quantity -= 1;
                renderCartPage();
                updateCartBadge();
            }
        }
        
        // Fungsi untuk mengupdate keranjang
        function updateCart() {
            let total = 0;
            let hasItems = false;
            
            for (const product in products) {
                if (products[product].quantity > 0) {
                    total += products[product].price * products[product].quantity;
                    hasItems = true;
                }
            }
            
            // Juga hitung produk kustom
            customProducts.forEach(product => {
                if (product.quantity > 0) {
                    total += product.price * product.quantity;
                    hasItems = true;
                }
            });
            
            document.getElementById('cart-total').textContent = `Total Belanja: Rp ${total.toLocaleString('id-ID')}`;
            currentTransactionAmount = total;
            
            // Enable/disable checkout button based on cart contents
            document.getElementById('checkout-button').disabled = !hasItems;
        }
        
        // Muat riwayat transaksi dari database
        function loadTransactionHistory() {
            if (!currentUser) return;
            
            const transactionHistoryElement = document.getElementById('transaction-history');
            
            db.collection('transactions')
                .where('userId', '==', currentUser.uid)
                .orderBy('createdAt', 'desc')
                .get()
                .then((querySnapshot) => {
                    if (querySnapshot.empty) {
                        transactionHistoryElement.innerHTML = `
                            <div class="empty-cart">
                                <i class="fas fa-receipt"></i>
                                <h4>Belum Ada Transaksi</h4>
                                <p>Transaksi Anda akan muncul di sini setelah melakukan pembayaran</p>
                            </div>
                        `;
                    } else {
                        let transactionsHTML = '';
                        
                        querySnapshot.forEach((doc) => {
                            const transaction = doc.data();
                            let itemsHTML = '';
                            
                            transaction.items.forEach(item => {
                                itemsHTML += `
                                    <div class="product-list-item">
                                        <div class="product-list-info">
                                            <div class="product-list-name">${item.name}</div>
                                            <div class="product-list-price">${item.quantity} x Rp ${item.price.toLocaleString('id-ID')}</div>
                                        </div>
                                        <div class="product-list-price">Rp ${item.subtotal.toLocaleString('id-ID')}</div>
                                    </div>
                                `;
                            });
                            
                            const statusText = transaction.status === 'paid' ? 'Sudah Dibayar' : 'Belum Dibayar';
                            const statusClass = transaction.status === 'paid' ? 'status-paid' : 'status-unpaid';
                            const buttonText = transaction.status === 'paid' ? 'Konfirmasi Pesanan' : 'Lanjutkan Pembayaran';
                            const buttonAction = transaction.status === 'paid' ? `confirmOrder('${transaction.id}')` : `continuePayment('${transaction.id}')`;
                            const buttonClass = transaction.status === 'paid' ? 'confirm-btn' : 'checkout-btn';
                            
                            transactionsHTML += `
                                <div class="transaction-item">
                                    <div class="transaction-header">
                                        <div class="transaction-id">${transaction.id}</div>
                                        <div class="transaction-date">${transaction.date} ${transaction.time}</div>
                                    </div>
                                    <div class="store-name">${transaction.store}</div>
                                    <div class="product-desc">${transaction.customerName} • ${transaction.customerPhone}</div>
                                    <div class="product-desc">${transaction.deliveryMethod === 'delivery' ? 'Delivery' : 'Park & Pick'} • ${transaction.paymentMethod}</div>
                                    <div class="transaction-status ${statusClass}">${statusText}</div>
                                    ${itemsHTML}
                                    <div class="transaction-total">Total: Rp ${transaction.total.toLocaleString('id-ID')}</div>
                                    <button class="${buttonClass}" onclick="${buttonAction}" style="margin-top: 15px; width: 100%;">${buttonText}</button>
                                </div>
                            `;
                        });
                        
                        transactionHistoryElement.innerHTML = transactionsHTML;
                    }
                })
                .catch((error) => {
                    console.error('Error mengambil riwayat transaksi: ', error);
                    transactionHistoryElement.innerHTML = `
                        <div class="empty-cart">
                            <i class="fas fa-receipt"></i>
                            <h4>Gagal Memuat Riwayat</h4>
                            <p>Terjadi kesalahan saat memuat riwayat transaksi</p>
                        </div>
                    `;
                });
        }
        
        // Muat produk kustom dari database
        function loadCustomProducts() {
            if (!currentUser) return;
            
            db.collection('products')
                .where('userId', '==', currentUser.uid)
                .get()
                .then((querySnapshot) => {
                    customProducts = [];
                    querySnapshot.forEach((doc) => {
                        customProducts.push(doc.data());
                    });
                    renderCustomProducts();
                })
                .catch((error) => {
                    console.error('Error mengambil produk kustom: ', error);
                });
        }
        
        // Tampilkan modal tambah produk
        function showAddProductModal() {
            document.getElementById('add-product-modal').classList.add('active');
            setupReceiptOCR();
        }
        
        // Setup OCR untuk struk
        function setupReceiptOCR() {
            const receiptDropArea = document.getElementById('receipt-drop-area');
            const receiptInput = document.getElementById('receipt-input');
            const receiptLoading = document.getElementById('receipt-loading');
            const extractedProducts = document.getElementById('extracted-products');
            
            receiptDropArea.addEventListener('click', () => {
                receiptInput.click();
            });
            
            receiptInput.addEventListener('change', async (e) => {
                if (e.target.files.length) {
                    const file = e.target.files[0];
                    
                    receiptLoading.style.display = 'block';
                    extractedProducts.style.display = 'none';
                    
                    try {
                        // Process image with Tesseract OCR
                        const { data: { text } } = await Tesseract.recognize(
                            file,
                            'eng+ind', // English and Indonesian
                            { 
                                logger: m => console.log(m) 
                            }
                        );
                        
                        console.log('OCR Result:', text);
                        
                        // Parse produk dari teks struk
                        const products = parseProductsFromReceipt(text);
                        
                        // Tampilkan produk yang ditemukan
                        displayExtractedProducts(products);
                        
                    } catch (error) {
                        console.error('OCR Error:', error);
                        alert('Terjadi kesalahan saat memproses struk. Silakan coba lagi.');
                    } finally {
                        receiptLoading.style.display = 'none';
                    }
                }
            });
        }
        
        // Parse produk dari teks struk
        function parseProductsFromReceipt(text) {
            // Ini adalah contoh sederhana parsing produk dari struk
            // Dalam implementasi nyata, Anda perlu menyesuaikan dengan format struk yang digunakan
            
            const lines = text.split('\n');
            const products = [];
            
            lines.forEach(line => {
                // Cari baris yang mengandung nama produk dan harga
                // Ini adalah contoh pattern matching sederhana
                const priceMatch = line.match(/(\d{1,3}(?:\.\d{3})*(?:,\d{2})?)/);
                
                if (priceMatch) {
                    const price = parseInt(priceMatch[0].replace(/\./g, ''));
                    
                    // Ambil nama produk (semua teks sebelum harga)
                    const productName = line.substring(0, priceMatch.index).trim();
                    
                    if (productName && price > 1000) { // Filter harga yang terlalu kecil
                        products.push({
                            name: productName,
                            price: price
                        });
                    }
                }
            });
            
            return products;
        }
        
        // Tampilkan produk yang diekstrak dari struk
        function displayExtractedProducts(products) {
            const extractedProducts = document.getElementById('extracted-products');
            let productsHTML = '';
            
            if (products.length === 0) {
                productsHTML = '<p>Tidak ada produk yang ditemukan dalam struk.</p>';
            } else {
                products.forEach((product, index) => {
                    productsHTML += `
                        <div class="product-list-item">
                            <div class="product-list-info">
                                <input type="text" class="product-list-name" value="${product.name}" onchange="updateExtractedProduct(${index}, 'name', this.value)">
                                <input type="number" class="product-list-price" value="${product.price}" onchange="updateExtractedProduct(${index}, 'price', this.value)">
                            </div>
                            <div class="product-list-actions">
                                <button class="edit-btn" onclick="saveExtractedProduct(${index})">Simpan</button>
                                <button class="delete-btn" onclick="removeExtractedProduct(${index})">Hapus</button>
                            </div>
                        </div>
                    `;
                });
            }
            
            extractedProducts.innerHTML = productsHTML;
            extractedProducts.style.display = 'block';
        }
        
        // Update produk yang diekstrak
        function updateExtractedProduct(index, field, value) {
            if (!extractedProductsList[index]) {
                extractedProductsList[index] = {};
            }
            
            extractedProductsList[index][field] = value;
        }
        
        // Hapus produk yang diekstrak
        function removeExtractedProduct(index) {
            extractedProductsList.splice(index, 1);
            displayExtractedProducts(extractedProductsList);
        }
        
        // Simpan produk yang diekstrak ke database
        function saveExtractedProduct(index) {
            if (!currentUser) {
                alert('Anda harus login terlebih dahulu');
                return;
            }
            
            const product = extractedProductsList[index];
            const store = document.getElementById('receipt-store').value;
            
            if (product && product.name && product.price) {
                const productData = {
                    id: 'custom-' + Date.now() + '-' + index,
                    name: product.name,
                    price: parseInt(product.price),
                    store: store,
                    category: 'other',
                    quantity: 0,
                    userId: currentUser.uid,
                    createdAt: firebase.firestore.FieldValue.serverTimestamp()
                };
                
                // Simpan produk ke Firestore
                db.collection('products').doc(productData.id).set(productData)
                    .then(() => {
                        alert('Produk berhasil disimpan: ' + product.name);
                        
                        // Reset daftar produk yang diekstrak
                        extractedProductsList = [];
                        document.getElementById('extracted-products').style.display = 'none';
                        
                        // Tutup modal
                        document.getElementById('add-product-modal').classList.remove('active');
                        
                        // Update UI produk
                        loadCustomProducts();
                    })
                    .catch((error) => {
                        console.error('Error menyimpan produk: ', error);
                        alert('Terjadi kesalahan saat menyimpan produk');
                    });
            } else {
                alert('Nama produk dan harga harus diisi');
            }
        }
        
        // Tambah produk manual ke database
        function addManualProduct() {
            if (!currentUser) {
                alert('Anda harus login terlebih dahulu');
                return;
            }
            
            const productName = document.getElementById('product-name').value;
            const productPrice = document.getElementById('product-price').value;
            const productStore = document.getElementById('product-store').value;
            const productCategory = document.getElementById('product-category').value;
            
            if (productName && productPrice) {
                const productData = {
                    id: 'custom-' + Date.now(),
                    name: productName,
                    price: parseInt(productPrice),
                    store: productStore,
                    category: productCategory,
                    quantity: 0,
                    userId: currentUser.uid,
                    createdAt: firebase.firestore.FieldValue.serverTimestamp()
                };
                
                // Simpan produk ke Firestore
                db.collection('products').doc(productData.id).set(productData)
                    .then(() => {
                        alert('Produk berhasil ditambahkan: ' + productName);
                        
                        // Reset form
                        document.getElementById('product-name').value = '';
                        document.getElementById('product-price').value = '';
                        
                        // Tutup modal
                        document.getElementById('add-product-modal').classList.remove('active');
                        
                        // Update UI produk
                        loadCustomProducts();
                    })
                    .catch((error) => {
                        console.error('Error menambahkan produk: ', error);
                        alert('Terjadi kesalahan saat menambahkan produk');
                    });
            } else {
                alert('Nama produk dan harga harus diisi');
            }
        }
        
        // Render produk kustom
        function renderCustomProducts() {
            const customProductsSection = document.getElementById('custom-products-section');
            let customHTML = '';
            
            // Filter produk kustom untuk toko yang dipilih
            const storeCustomProducts = customProducts.filter(product => product.store === selectedStore);
            
            if (storeCustomProducts.length > 0) {
                customHTML += `<div class="section-title"><i class="fas fa-star"></i> Produk Kustom</div>`;
                
                storeCustomProducts.forEach((product, index) => {
                    customHTML += `
                        <div class="product-item custom-product-item" data-category="${product.category}">
                            <div class="product-name">${product.name}</div>
                            <div class="product-desc">Produk kustom</div>
                            <div class="product-price">Rp. ${product.price.toLocaleString('id-ID')}</div>
                            <div class="product-stock stock-available">In stock</div>
                            <div class="quantity-control">
                                <button class="quantity-btn" onclick="decreaseCustomQuantity(${index})">-</button>
                                <span class="quantity" id="custom-${index}-quantity">${product.quantity || 0}</span>
                                <button class="quantity-btn" onclick="increaseCustomQuantity(${index})">+</button>
                            </div>
                        </div>
                    `;
                });
            }
            
            customProductsSection.innerHTML = customHTML;
        }
        
        // Fungsi untuk menambah/mengurangi kuantitas produk kustom
        function increaseCustomQuantity(index) {
            if (!customProducts[index].quantity) {
                customProducts[index].quantity = 0;
            }
            customProducts[index].quantity += 1;
            document.getElementById(`custom-${index}-quantity`).textContent = customProducts[index].quantity;
            updateCartBadge();
        }
        
        function decreaseCustomQuantity(index) {
            if (customProducts[index].quantity > 0) {
                customProducts[index].quantity -= 1;
                document.getElementById(`custom-${index}-quantity`).textContent = customProducts[index].quantity;
                updateCartBadge();
            }
        }
        
        // Konfirmasi pesanan (untuk transaksi yang sudah dibayar)
        function confirmOrder(transactionId) {
            if (!currentUser) return;
            
            db.collection('transactions').doc(transactionId).get()
                .then((doc) => {
                    if (doc.exists) {
                        const transaction = doc.data();
                        sendToWhatsApp(transaction);
                    }
                })
                .catch((error) => {
                    console.error('Error mengambil transaksi: ', error);
                });
        }
        
        // Kirim pesan ke WhatsApp
        function sendToWhatsApp(transaction = null) {
            const customerName = transaction ? transaction.customerName : document.getElementById('customer-name').value;
            const deliveryText = transaction ? (transaction.deliveryMethod === 'delivery' ? 'Delivery' : 'Park & Pick') : 
                                             (selectedDeliveryMethod === 'delivery' ? 'Delivery' : 'Park & Pick');
            const deliveryFee = transaction ? (transaction.deliveryMethod === 'delivery' ? 5000 : 0) : 
                                            (selectedDeliveryMethod === 'delivery' ? 5000 : 0);
            
            // Format pesan WhatsApp
            let message = `Lokasi toko :\nhttp://www.google.com/maps/place/-6.013695,106.045039\n\n`;
            message += `*Berikut ini adalah pesanan saya :*\n\n`;
            
            if (transaction) {
                // Loop melalui produk yang dibeli dari transaksi
                transaction.items.forEach((item, index) => {
                    message += `${index + 1}. *${item.name}* @ ${item.quantity} x ${item.price.toLocaleString('id-ID')},-0 = Rp. ${item.subtotal.toLocaleString('id-ID')},-0\n`;
                });
                
                message += `\n*EST. TOTAL = Rp. ${transaction.subtotal.toLocaleString('id-ID')},-0*\n`;
                
                if (transaction.deliveryMethod === 'delivery') {
                    message += `Biaya Pengiriman: Rp. ${deliveryFee.toLocaleString('id-ID')},-0\n`;
                    message += `*TOTAL = Rp. ${transaction.total.toLocaleString('id-ID')},-0*\n`;
                }
                
                message += '----------------------------------------\n';
                message += `Nama : ${customerName}\n`;
                message += `Delivery: ${deliveryText}\n`;
                message += `Pembayaran : QRIS (dibayar)\n`;
                
                if (transaction.deliveryMethod === 'delivery') {
                    message += `Alamat: ${transaction.customerAddress}\n`;
                }
                
                message += '----------------------------------------\n';
                message += 'NOTES:\n- Jika ada \n';
                message += '----------------------------------------\n';
                message += `No Order : ${transaction.id}\n`;
                message += '*Proses pesanan saya terimakasih*';
            } else {
                // Loop melalui produk yang dibeli (standar)
                for (const product in products) {
                    if (products[product].quantity > 0) {
                        const subtotal = products[product].price * products[product].quantity;
                        message += `1. *${products[product].name}* @ ${products[product].quantity} x ${products[product].price.toLocaleString('id-ID')},-0 = Rp. ${subtotal.toLocaleString('id-ID')},-0\n`;
                    }
                }
                
                // Loop melalui produk yang dibeli (kustom)
                customProducts.forEach(product => {
                    if (product.quantity > 0) {
                        const subtotal = product.price * product.quantity;
                        message += `1. *${product.name}* @ ${product.quantity} x ${product.price.toLocaleString('id-ID')},-0 = Rp. ${subtotal.toLocaleString('id-ID')},-0\n`;
                    }
                });
                
                message += `\n*EST. TOTAL = Rp. ${currentTransactionAmount.toLocaleString('id-ID')},-0*\n`;
                
                if (selectedDeliveryMethod === 'delivery') {
                    message += `Biaya Pengiriman: Rp. ${deliveryFee.toLocaleString('id-ID')},-0\n`;
                    message += `*TOTAL = Rp. ${(currentTransactionAmount + deliveryFee).toLocaleString('id-ID')},-0*\n`;
                }
                
                message += '----------------------------------------\n';
                message += `Nama : ${customerName}\n`;
                message += `Delivery: ${deliveryText}\n`;
                message += `Pembayaran : QRIS (dibayar)\n`;
                
                if (selectedDeliveryMethod === 'delivery') {
                    const customerAddress = document.getElementById('customer-address').value;
                    message += `Alamat: ${customerAddress}\n`;
                }
                
                message += '----------------------------------------\n';
                message += 'NOTES:\n- Jika ada \n';
                message += '----------------------------------------\n';
                message += `No Order : ${orderId}\n`;
                message += '*Proses pesanan saya terimakasih*';
            }
            
            // Encode pesan untuk URL
            const encodedMessage = encodeURIComponent(message);
            
            // Redirect ke WhatsApp dengan nomor baru
            window.open(`https://wa.me/628881365814?text=${encodedMessage}`, '_blank');
            
            // Kembali ke halaman produk
            showPage('products-page');
        }
        
        // Login dengan Google
        function loginWithGoogle() {
            const provider = new firebase.auth.GoogleAuthProvider();
            
            auth.signInWithPopup(provider)
                .then((result) => {
                    // Login berhasil
                    currentUser = result.user;
                    updateUserProfile();
                    showPage('dashboard-page');
                    
                    // Muat data dari database
                    loadCustomProducts();
                    loadTransactionHistory();
                })
                .catch((error) => {
                    console.error('Error login: ', error);
                    alert('Login gagal: ' + error.message);
                });
        }
        
        // Update profil pengguna di UI
        function updateUserProfile() {
            if (currentUser) {
                document.getElementById('user-name').textContent = currentUser.displayName;
                document.getElementById('user-email').textContent = currentUser.email;
                document.getElementById('user-avatar').src = currentUser.photoURL;
            }
        }
        
        // Logout
        function logout() {
            auth.signOut()
                .then(() => {
                    currentUser = null;
                    showPage('login-page');
                    
                    // Reset data
                    customProducts = [];
                    transactionHistory = [];
                    
                    // Reset keranjang
                    for (const product in products) {
                        products[product].quantity = 0;
                    }
                    updateCartBadge();
                })
                .catch((error) => {
                    console.error('Error logout: ', error);
                });
        }
        
        // Cek status login saat aplikasi dimuat
        auth.onAuthStateChanged((user) => {
            if (user) {
                // Pengguna sudah login
                currentUser = user;
                updateUserProfile();
                showPage('dashboard-page');
                
                // Muat data dari database
                loadCustomProducts();
                loadTransactionHistory();
            } else {
                // Pengguna belum login
                showPage('login-page');
            }
        });
        
        // Inisialisasi
        document.addEventListener('DOMContentLoaded', function() {
            // Setup modal close buttons
            document.getElementById('close-add-product').addEventListener('click', function() {
                document.getElementById('add-product-modal').classList.remove('active');
            });
            
            // Setup login button
            document.getElementById('google-login-btn').addEventListener('click', loginWithGoogle);
            
            // Close modals when clicking outside
            window.addEventListener('click', function(e) {
                if (e.target === document.getElementById('add-product-modal')) {
                    document.getElementById('add-product-modal').classList.remove('active');
                }
            });
            
            // Update cart badge on initial load
            updateCartBadge();
            
            // Setup proof upload
            setupProofUpload();
        });
    </script>
</body>
</html>
