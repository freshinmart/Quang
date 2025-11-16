<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>KasPOS - Aplikasi Point of Sale</title>
    <!-- Library untuk scanner barcode dan QR -->
    <script src="https://cdn.jsdelivr.net/npm/quagga@0.12.1/dist/quagga.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        
        body {
            background-color: #f5f5f5;
            color: #333;
            line-height: 1.6;
        }
        
        .container {
            display: flex;
            min-height: 100vh;
        }
        
        /* Sidebar Styles */
        .sidebar {
            width: 280px;
            background: linear-gradient(135deg, #2c3e50, #34495e);
            color: white;
            padding: 0;
            height: 100vh;
            position: fixed;
            overflow-y: auto;
            box-shadow: 2px 0 10px rgba(0,0,0,0.1);
            z-index: 1000;
        }
        
        .sidebar-header {
            padding: 25px 20px;
            border-bottom: 1px solid rgba(255,255,255,0.1);
            background-color: rgba(0,0,0,0.1);
        }
        
        .logo {
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        .logo-icon {
            font-size: 24px;
            background-color: #3498db;
            width: 40px;
            height: 40px;
            border-radius: 8px;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        
        .logo-text {
            font-size: 20px;
            font-weight: 700;
        }
        
        .trial-info {
            margin-top: 10px;
            font-size: 13px;
            color: #ecf0f1;
            background-color: rgba(52, 152, 219, 0.2);
            padding: 8px 12px;
            border-radius: 6px;
            border-left: 3px solid #3498db;
        }
        
        .menu {
            padding: 15px 0;
        }
        
        .menu-section {
            margin-bottom: 10px;
        }
        
        .menu-title {
            font-size: 12px;
            text-transform: uppercase;
            color: #bdc3c7;
            padding: 10px 20px;
            letter-spacing: 1px;
        }
        
        .menu-item {
            padding: 12px 20px;
            display: flex;
            align-items: center;
            gap: 12px;
            cursor: pointer;
            transition: all 0.3s;
            border-left: 3px solid transparent;
        }
        
        .menu-item:hover {
            background-color: rgba(255,255,255,0.05);
            border-left-color: #3498db;
        }
        
        .menu-item.active {
            background-color: rgba(52, 152, 219, 0.2);
            border-left-color: #3498db;
        }
        
        .menu-item i {
            font-size: 18px;
            width: 24px;
            text-align: center;
        }
        
        .menu-item-text {
            flex: 1;
        }
        
        .badge {
            background-color: #e74c3c;
            color: white;
            font-size: 11px;
            padding: 2px 8px;
            border-radius: 10px;
        }
        
        .menu-divider {
            height: 1px;
            background-color: rgba(255,255,255,0.1);
            margin: 15px 20px;
        }
        
        .upgrade-section {
            margin: 20px;
            padding: 15px;
            background: linear-gradient(135deg, #3498db, #2980b9);
            border-radius: 8px;
            text-align: center;
        }
        
        .upgrade-title {
            font-size: 14px;
            font-weight: 600;
            margin-bottom: 8px;
        }
        
        .upgrade-desc {
            font-size: 12px;
            margin-bottom: 12px;
            opacity: 0.9;
        }
        
        .btn-upgrade {
            background-color: white;
            color: #3498db;
            border: none;
            padding: 8px 15px;
            border-radius: 20px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s;
            font-size: 12px;
            width: 100%;
        }
        
        .btn-upgrade:hover {
            background-color: #f8f9fa;
            transform: translateY(-2px);
            box-shadow: 0 4px 8px rgba(0,0,0,0.1);
        }
        
        /* Main Content Styles */
        .main-content {
            flex: 1;
            margin-left: 280px;
            padding: 20px;
            transition: margin-left 0.3s;
        }
        
        .header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
            padding-bottom: 15px;
            border-bottom: 1px solid #ddd;
        }
        
        .trial-notice {
            background-color: #fff8e1;
            border-left: 4px solid #ffc107;
            padding: 12px 15px;
            margin-bottom: 20px;
            border-radius: 4px;
            font-size: 14px;
        }
        
        .content-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
        }
        
        .page-title {
            font-size: 22px;
            font-weight: 600;
            color: #2c3e50;
        }
        
        .btn {
            padding: 8px 16px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 14px;
            transition: background-color 0.3s;
        }
        
        .btn-primary {
            background-color: #3498db;
            color: white;
        }
        
        .btn-secondary {
            background-color: #95a5a6;
            color: white;
        }
        
        .btn-success {
            background-color: #2ecc71;
            color: white;
        }
        
        .btn-danger {
            background-color: #e74c3c;
            color: white;
        }
        
        .btn-warning {
            background-color: #f39c12;
            color: white;
        }
        
        /* Halaman Daftar Produk */
        .filter-section {
            display: flex;
            gap: 15px;
            margin-bottom: 20px;
        }
        
        .filter-select {
            padding: 8px 15px;
            border: 1px solid #ddd;
            border-radius: 4px;
            background-color: white;
            cursor: pointer;
            font-size: 14px;
        }
        
        .product-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
            gap: 20px;
            margin-top: 20px;
        }
        
        .product-card {
            background-color: white;
            border-radius: 8px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
            padding: 15px;
            transition: transform 0.3s, box-shadow 0.3s;
            cursor: pointer;
        }
        
        .product-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 5px 15px rgba(0,0,0,0.1);
        }
        
        .product-image {
            width: 100%;
            height: 150px;
            background-color: #f8f9fa;
            border-radius: 4px;
            margin-bottom: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
            color: #6c757d;
        }
        
        .product-name {
            font-weight: 600;
            margin-bottom: 5px;
        }
        
        .product-price {
            color: #e74c3c;
            font-weight: 600;
            margin-bottom: 10px;
        }
        
        .product-actions {
            display: flex;
            justify-content: space-between;
        }
        
        .empty-state {
            text-align: center;
            padding: 40px 20px;
            color: #7f8c8d;
        }
        
        .empty-state i {
            font-size: 48px;
            margin-bottom: 15px;
            color: #bdc3c7;
        }
        
        /* Form Styles */
        .form-container {
            background-color: white;
            border-radius: 8px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
            padding: 25px;
            margin-top: 20px;
        }
        
        .section-title {
            font-size: 18px;
            font-weight: 600;
            margin-bottom: 20px;
            color: #2c3e50;
        }
        
        .form-group {
            margin-bottom: 20px;
        }
        
        .form-label {
            display: block;
            margin-bottom: 8px;
            font-weight: 500;
            color: #2c3e50;
        }
        
        .form-control {
            width: 100%;
            padding: 10px 12px;
            border: 1px solid #ddd;
            border-radius: 4px;
            font-size: 14px;
            transition: border-color 0.3s;
        }
        
        .form-control:focus {
            border-color: #3498db;
            outline: none;
        }
        
        .form-control.error {
            border-color: #e74c3c;
        }
        
        .form-hint {
            font-size: 12px;
            color: #7f8c8d;
            margin-top: 5px;
        }
        
        .form-error {
            font-size: 12px;
            color: #e74c3c;
            margin-top: 5px;
            display: none;
        }
        
        .divider {
            height: 1px;
            background-color: #eee;
            margin: 25px 0;
        }
        
        .form-row {
            display: flex;
            gap: 20px;
        }
        
        .form-col {
            flex: 1;
        }
        
        .category-select {
            position: relative;
        }
        
        .category-select select {
            appearance: none;
            background-image: url("data:image/svg+xml;charset=UTF-8,%3csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24' fill='none' stroke='%23333' stroke-width='2' stroke-linecap='round' stroke-linejoin='round'%3e%3cpolyline points='6 9 12 15 18 9'%3e%3c/polyline%3e%3c/svg%3e");
            background-repeat: no-repeat;
            background-position: right 10px center;
            background-size: 16px;
            padding-right: 35px;
        }
        
        .optional-badge {
            font-size: 12px;
            color: #7f8c8d;
            font-weight: normal;
        }
        
        .form-actions {
            display: flex;
            justify-content: flex-end;
            gap: 10px;
            margin-top: 30px;
            padding-top: 20px;
            border-top: 1px solid #eee;
        }
        
        /* Checkbox Styles */
        .checkbox-group {
            display: flex;
            flex-direction: column;
            gap: 12px;
            margin-top: 10px;
        }
        
        .checkbox-item {
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        .checkbox-item input[type="checkbox"] {
            width: 18px;
            height: 18px;
        }
        
        /* Image Upload Styles */
        .image-upload-container {
            border: 2px dashed #ddd;
            border-radius: 8px;
            padding: 25px;
            text-align: center;
            margin-top: 15px;
            transition: border-color 0.3s;
            cursor: pointer;
        }
        
        .image-upload-container:hover {
            border-color: #3498db;
        }
        
        .image-upload-icon {
            font-size: 48px;
            color: #bdc3c7;
            margin-bottom: 10px;
        }
        
        .image-upload-text {
            color: #7f8c8d;
            margin-bottom: 15px;
        }
        
        .btn-upload {
            background-color: #3498db;
            color: white;
            padding: 8px 16px;
            border-radius: 4px;
            border: none;
            cursor: pointer;
            font-size: 14px;
        }
        
        .image-preview {
            margin-top: 15px;
            display: none;
        }
        
        .preview-image {
            max-width: 200px;
            max-height: 200px;
            border-radius: 4px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }
        
        .image-actions {
            margin-top: 10px;
            display: flex;
            justify-content: center;
            gap: 10px;
        }
        
        .btn-remove {
            background-color: #e74c3c;
            color: white;
            padding: 5px 10px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 12px;
        }
        
        /* Halaman Kategori */
        .categories-list {
            background-color: white;
            border-radius: 8px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
            padding: 20px;
            margin-top: 20px;
        }
        
        .category-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 12px 15px;
            border-bottom: 1px solid #eee;
        }
        
        .category-item:last-child {
            border-bottom: none;
        }
        
        .category-actions {
            display: flex;
            gap: 10px;
        }
        
        /* Halaman Scanner */
        .scanner-container {
            background-color: white;
            border-radius: 8px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
            padding: 25px;
            margin-top: 20px;
            text-align: center;
        }
        
        .scanner-preview {
            width: 100%;
            max-width: 500px;
            height: 300px;
            background-color: #2c3e50;
            border-radius: 8px;
            margin: 20px auto;
            display: flex;
            align-items: center;
            justify-content: center;
            color: white;
            font-size: 18px;
            position: relative;
            overflow: hidden;
        }
        
        #cameraPreview {
            width: 100%;
            height: 100%;
            object-fit: cover;
        }
        
        .scanner-overlay {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            border: 2px solid #3498db;
            box-shadow: 0 0 0 1000px rgba(0, 0, 0, 0.5);
            display: flex;
            align-items: center;
            justify-content: center;
        }
        
        .scanner-frame {
            width: 70%;
            height: 40%;
            border: 2px solid #3498db;
            position: relative;
        }
        
        .scanner-frame::before, .scanner-frame::after {
            content: '';
            position: absolute;
            width: 20px;
            height: 20px;
            border: 2px solid #3498db;
        }
        
        .scanner-frame::before {
            top: -2px;
            left: -2px;
            border-right: none;
            border-bottom: none;
        }
        
        .scanner-frame::after {
            bottom: -2px;
            right: -2px;
            border-left: none;
            border-top: none;
        }
        
        .scanner-controls {
            margin-top: 20px;
            display: flex;
            justify-content: center;
            gap: 15px;
        }
        
        .btn-scan {
            background-color: #3498db;
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 4px;
            cursor: pointer;
            font-size: 16px;
        }
        
        .scanner-status {
            margin-top: 15px;
            padding: 10px;
            border-radius: 4px;
            font-size: 14px;
        }
        
        .scanner-status.success {
            background-color: #d4edda;
            color: #155724;
            border: 1px solid #c3e6cb;
        }
        
        .scanner-status.error {
            background-color: #f8d7da;
            color: #721c24;
            border: 1px solid #f5c6cb;
        }
        
        .scanner-status.info {
            background-color: #d1ecf1;
            color: #0c5460;
            border: 1px solid #bee5eb;
        }
        
        .recent-products {
            margin-top: 30px;
        }
        
        .cart-button {
            position: fixed;
            bottom: 20px;
            right: 20px;
            background-color: #2ecc71;
            color: white;
            padding: 12px 20px;
            border-radius: 50px;
            box-shadow: 0 4px 10px rgba(0,0,0,0.2);
            cursor: pointer;
            display: flex;
            align-items: center;
            gap: 8px;
            z-index: 100;
        }
        
        /* Halaman Keranjang Belanja */
        .cart-container {
            background-color: white;
            border-radius: 8px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
            padding: 25px;
            margin-top: 20px;
        }
        
        .cart-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
            padding-bottom: 15px;
            border-bottom: 1px solid #eee;
        }
        
        .cart-items {
            margin-bottom: 20px;
        }
        
        .cart-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 15px 0;
            border-bottom: 1px solid #eee;
        }
        
        .cart-item:last-child {
            border-bottom: none;
        }
        
        .cart-item-info {
            flex: 1;
        }
        
        .cart-item-name {
            font-weight: 600;
            margin-bottom: 5px;
        }
        
        .cart-item-desc {
            color: #7f8c8d;
            font-size: 14px;
        }
        
        .cart-item-price {
            font-weight: 600;
            color: #e74c3c;
        }
        
        .cart-item-quantity {
            display: flex;
            align-items: center;
            gap: 10px;
            margin: 0 15px;
        }
        
        .quantity-btn {
            width: 30px;
            height: 30px;
            border: 1px solid #ddd;
            background-color: #f8f9fa;
            border-radius: 4px;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
        }
        
        .quantity-value {
            min-width: 30px;
            text-align: center;
        }
        
        .cart-summary {
            margin-top: 20px;
            padding-top: 20px;
            border-top: 1px solid #eee;
        }
        
        .summary-row {
            display: flex;
            justify-content: space-between;
            margin-bottom: 10px;
        }
        
        .summary-total {
            font-weight: 600;
            font-size: 18px;
            color: #2c3e50;
        }
        
        /* Halaman Checkout */
        .checkout-container {
            display: flex;
            gap: 20px;
        }
        
        .checkout-left {
            flex: 2;
        }
        
        .checkout-right {
            flex: 1;
        }
        
        .payment-methods {
            display: grid;
            grid-template-columns: 1fr 1fr 1fr;
            gap: 10px;
            margin-bottom: 20px;
        }
        
        .payment-method {
            padding: 15px;
            border: 1px solid #ddd;
            border-radius: 8px;
            text-align: center;
            cursor: pointer;
            transition: all 0.3s;
        }
        
        .payment-method:hover {
            border-color: #3498db;
        }
        
        .payment-method.active {
            border-color: #3498db;
            background-color: #e3f2fd;
        }
        
        .payment-icon {
            font-size: 24px;
            margin-bottom: 5px;
        }
        
        .denomination-grid {
            display: grid;
            grid-template-columns: repeat(5, 1fr);
            gap: 10px;
            margin-bottom: 20px;
        }
        
        .denomination-btn {
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 4px;
            background-color: #f8f9fa;
            cursor: pointer;
            text-align: center;
            transition: all 0.2s;
        }
        
        .denomination-btn:hover {
            background-color: #e9ecef;
        }
        
        .payment-input {
            margin-bottom: 20px;
        }
        
        .payment-actions {
            display: flex;
            gap: 10px;
        }
        
        .btn-pay {
            flex: 1;
            padding: 12px;
            font-size: 16px;
            font-weight: 600;
        }
        
        /* QRIS Styles */
        .qris-container {
            text-align: center;
            padding: 20px;
            background-color: #f8f9fa;
            border-radius: 8px;
            margin-bottom: 20px;
        }
        
        .qris-code {
            width: 200px;
            height: 200px;
            background-color: white;
            margin: 0 auto 15px;
            border: 1px solid #ddd;
            border-radius: 8px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 14px;
            color: #7f8c8d;
        }
        
        /* Halaman Konfirmasi Transaksi */
        .transaction-details {
            background-color: white;
            border-radius: 8px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
            padding: 25px;
            margin-top: 20px;
        }
        
        .transaction-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
            padding-bottom: 15px;
            border-bottom: 1px solid #eee;
        }
        
        .transaction-date {
            color: #7f8c8d;
            font-size: 14px;
        }
        
        .transaction-id {
            font-weight: 600;
            color: #3498db;
        }
        
        .transaction-items {
            margin-bottom: 20px;
        }
        
        .transaction-item {
            display: flex;
            justify-content: space-between;
            padding: 10px 0;
            border-bottom: 1px solid #eee;
        }
        
        .transaction-item:last-child {
            border-bottom: none;
        }
        
        .transaction-summary {
            margin-top: 20px;
            padding-top: 20px;
            border-top: 1px solid #eee;
        }
        
        .summary-item {
            display: flex;
            justify-content: space-between;
            margin-bottom: 10px;
        }
        
        .summary-label {
            color: #7f8c8d;
        }
        
        .summary-value {
            font-weight: 600;
        }
        
        .print-option {
            display: flex;
            align-items: center;
            gap: 10px;
            margin-top: 15px;
        }
        
        /* Modal Styles */
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0,0,0,0.5);
            z-index: 2000;
            align-items: center;
            justify-content: center;
        }
        
        .modal-content {
            background-color: white;
            padding: 25px;
            border-radius: 8px;
            width: 90%;
            max-width: 500px;
            box-shadow: 0 5px 15px rgba(0,0,0,0.3);
        }
        
        .modal-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
            padding-bottom: 15px;
            border-bottom: 1px solid #eee;
        }
        
        .modal-title {
            font-size: 18px;
            font-weight: 600;
        }
        
        .close-modal {
            background: none;
            border: none;
            font-size: 24px;
            cursor: pointer;
            color: #7f8c8d;
        }
        
        /* Keyboard Styles */
        .keyboard {
            margin-top: 20px;
        }
        
        .keyboard-row {
            display: flex;
            justify-content: center;
            margin-bottom: 8px;
        }
        
        .key {
            width: 40px;
            height: 40px;
            margin: 0 4px;
            display: flex;
            align-items: center;
            justify-content: center;
            background-color: #f8f9fa;
            border: 1px solid #ddd;
            border-radius: 4px;
            cursor: pointer;
            font-weight: 500;
            transition: all 0.2s;
        }
        
        .key:hover {
            background-color: #e9ecef;
        }
        
        .key-wide {
            width: 80px;
        }
        
        .key-space {
            width: 200px;
        }
        
        /* Responsive Styles */
        @media (max-width: 768px) {
            .container {
                flex-direction: column;
            }
            
            .sidebar {
                width: 100%;
                height: auto;
                position: relative;
            }
            
            .main-content {
                margin-left: 0;
            }
            
            .product-grid {
                grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
            }
            
            .form-row {
                flex-direction: column;
                gap: 0;
            }
            
            .checkout-container {
                flex-direction: column;
            }
            
            .payment-methods {
                grid-template-columns: 1fr;
            }
            
            .denomination-grid {
                grid-template-columns: repeat(3, 1fr);
            }
        }
        
        /* Page Styles */
        .page {
            display: none;
        }
        
        .page.active {
            display: block;
        }
        
        /* Scanner Canvas */
        #scannerCanvas {
            display: none;
        }
        
        /* Scanner Guide Text */
        .scanner-guide {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            color: white;
            text-align: center;
            z-index: 2;
            font-weight: bold;
            text-shadow: 1px 1px 2px rgba(0,0,0,0.7);
        }
        
        /* Scanner Result */
        .scanner-result {
            margin-top: 15px;
            padding: 10px;
            background-color: #f8f9fa;
            border-radius: 4px;
            text-align: center;
        }
        
        .barcode-value {
            font-family: monospace;
            font-size: 1.2rem;
            font-weight: bold;
            color: var(--success);
        }
    </style>
</head>
<body>
    <div class="container">
        <!-- Sidebar Navigasi -->
        <div class="sidebar">
            <div class="sidebar-header">
                <div class="logo">
                    <div class="logo-icon">K</div>
                    <div class="logo-text">KasPOS</div>
                </div>
                <div class="trial-info">
                    Saat ini anda trial, anda dapat hingga maksimal 10 produk
                </div>
            </div>
            
            <div class="menu">
                <div class="menu-section">
                    <div class="menu-title">Menu Utama</div>
                    <div class="menu-item active" data-page="products">
                        <i>🛍️</i>
                        <div class="menu-item-text">Tampilan Produk</div>
                    </div>
                    <div class="menu-item" data-page="add-product">
                        <i>➕</i>
                        <div class="menu-item-text">Tambah Produk</div>
                    </div>
                    <div class="menu-item" data-page="scanner">
                        <i>📷</i>
                        <div class="menu-item-text">Scanner Produk</div>
                    </div>
                    <div class="menu-item" data-page="cart">
                        <i>🛒</i>
                        <div class="menu-item-text">Keranjang Belanja</div>
                        <div class="badge" id="cartCount">0</div>
                    </div>
                </div>
                
                <div class="menu-divider"></div>
                
                <div class="menu-section">
                    <div class="menu-title">Transaksi</div>
                    <div class="menu-item" data-page="transactions">
                        <i>📋</i>
                        <div class="menu-item-text">Riwayat Transaksi</div>
                    </div>
                    <div class="menu-item" data-page="reports">
                        <i>📊</i>
                        <div class="menu-item-text">Laporan Transaksi</div>
                    </div>
                </div>
                
                <div class="menu-divider"></div>
                
                <div class="menu-section">
                    <div class="menu-title">Pengaturan</div>
                    <div class="menu-item" data-page="categories">
                        <i>📂</i>
                        <div class="menu-item-text">Kelola Kategori</div>
                    </div>
                    <div class="menu-item" data-page="settings">
                        <i>⚙️</i>
                        <div class="menu-item-text">Pengaturan</div>
                    </div>
                    <div class="menu-item" data-page="backup">
                        <i>💾</i>
                        <div class="menu-item-text">Backup Restore</div>
                    </div>
                </div>
                
                <div class="menu-divider"></div>
                
                <div class="menu-section">
                    <div class="menu-title">Lainnya</div>
                    <div class="menu-item" data-page="upgrade">
                        <i>🚀</i>
                        <div class="menu-item-text">Upgrade</div>
                        <div class="badge">PRO</div>
                    </div>
                    <div class="menu-item" data-page="feedback">
                        <i>📩</i>
                        <div class="menu-item-text">Kirim Masukan</div>
                    </div>
                    <div class="menu-item" data-page="rating">
                        <i>⭐</i>
                        <div class="menu-item-text">Beri Nilai Aplikasi</div>
                    </div>
                    <div class="menu-item" data-page="changelog">
                        <i>📝</i>
                        <div class="menu-item-text">Catatan Perubahan</div>
                    </div>
                </div>
                
                <div class="upgrade-section">
                    <div class="upgrade-title">Upgrade ke Versi Pro</div>
                    <div class="upgrade-desc">Dapatkan fitur lengkap tanpa batasan</div>
                    <button class="btn-upgrade" data-page="upgrade">UPGRADE SEKARANG</button>
                </div>
            </div>
        </div>
        
        <!-- Main Content -->
        <div class="main-content">
            <!-- Halaman Daftar Produk -->
            <div class="page active" id="products-page">
                <div class="header">
                    <h2>Dashboard</h2>
                    <div class="user-info">
                        <span>Admin</span>
                    </div>
                </div>
                
                <div class="trial-notice">
                    Saat ini anda sedang memakai versi trial, anda dapat menambahkan hingga maksimal 10 produk.
                </div>
                
                <div class="content-header">
                    <div class="page-title">Daftar Produk</div>
                    <button class="btn btn-primary" id="addProductBtn">Tambah Produk</button>
                </div>
                
                <div class="filter-section">
                    <select class="filter-select" id="categoryFilter">
                        <option>Semua Kategori</option>
                    </select>
                    
                    <select class="filter-select" id="sortFilter">
                        <option>Nama Produk (A-Z)</option>
                        <option>Nama Produk (Z-A)</option>
                        <option>Harga Terendah</option>
                        <option>Harga Tertinggi</option>
                    </select>
                </div>
                
                <div id="productList">
                    <!-- Daftar produk akan ditampilkan di sini -->
                    <div class="empty-state">
                        <i>📦</i>
                        <h3>Tidak ada item</h3>
                        <p>Silakan tambahkan produk pertama Anda</p>
                    </div>
                </div>
                
                <div class="cart-button" id="cartButton">
                    <i>🛒</i> Keranjang (<span id="cartButtonCount">0</span>)
                </div>
            </div>
            
            <!-- Halaman Tambah Produk -->
            <div class="page" id="add-product-page">
                <div class="header">
                    <h2>Dashboard</h2>
                    <div class="user-info">
                        <span>Admin</span>
                    </div>
                </div>
                
                <div class="trial-notice">
                    Saat ini anda sedang memakai versi trial, anda dapat menambahkan hingga maksimal 10 produk.
                </div>
                
                <div class="content-header">
                    <div class="page-title">Tambah Produk</div>
                    <button class="btn btn-secondary" id="backButton">Kembali</button>
                </div>
                
                <div class="form-container">
                    <h2 class="section-title">Rincian Produk</h2>
                    
                    <div class="form-group">
                        <label class="form-label" for="productTitle">Judul</label>
                        <input type="text" class="form-control" id="productTitle" placeholder="Masukkan judul produk" maxlength="30">
                        <div class="form-error" id="titleError">Judul tidak boleh kosong</div>
                        <div class="form-hint">
                            <span id="titleCounter">0</span>/30 karakter
                        </div>
                    </div>
                    
                    <div class="divider"></div>
                    
                    <div class="form-group">
                        <label class="form-label" for="productDescription">Deskripsi (Berat, ukuran, warna, dll.)</label>
                        <textarea class="form-control" id="productDescription" rows="3" placeholder="Masukkan deskripsi produk" maxlength="300"></textarea>
                        <div class="form-hint">
                            <span id="descriptionCounter">0</span>/300 karakter
                        </div>
                    </div>
                    
                    <div class="form-group">
                        <label class="form-label" for="productCategory">Kategori</label>
                        <div class="category-select">
                            <select class="form-control" id="productCategory">
                                <option value="">Tidak ada kategori</option>
                            </select>
                        </div>
                        <div class="form-hint">
                            <a href="#" id="manageCategoriesLink">Kelola Kategori</a>
                        </div>
                    </div>
                    
                    <div class="divider"></div>
                    
                    <div class="form-group">
                        <label class="form-label" for="productBarcode">Barcode / UPC (Universal Product Code)</label>
                        <input type="text" class="form-control" id="productBarcode" placeholder="Masukkan kode barcode produk">
                    </div>
                    
                    <h2 class="section-title">Harga</h2>
                    
                    <div class="form-row">
                        <div class="form-col">
                            <div class="form-group">
                                <label class="form-label" for="productPrice">Harga Jual</label>
                                <input type="number" class="form-control" id="productPrice" placeholder="0" min="0">
                            </div>
                        </div>
                        <div class="form-col">
                            <div class="form-group">
                                <label class="form-label" for="productCost">
                                    Harga Pokok <span class="optional-badge">(Opsional)</span>
                                </label>
                                <input type="number" class="form-control" id="productCost" placeholder="0" min="0">
                                <div class="form-hint">
                                    Jika dikosongkan, keuntungan akan dihitung dengan asumsi harga pembelian = 0
                                </div>
                            </div>
                        </div>
                    </div>
                    
                    <div class="divider"></div>
                    
                    <h2 class="section-title">Pengenal Produk</h2>
                    
                    <div class="checkbox-group">
                        <div class="checkbox-item">
                            <input type="checkbox" id="useImage">
                            <label for="useImage">Gambar</label>
                        </div>
                        <div class="checkbox-item">
                            <input type="checkbox" id="useShapeColor">
                            <label for="useShapeColor">Bentuk Dan Warna</label>
                        </div>
                    </div>
                    
                    <div class="divider"></div>
                    
                    <div class="form-group">
                        <label class="form-label">Pilih Gambar</label>
                        
                        <div class="image-upload-container" id="imageUploadArea">
                            <div class="image-upload-icon">📷</div>
                            <div class="image-upload-text">Klik untuk memilih gambar atau seret ke sini</div>
                            <button type="button" class="btn-upload" id="selectImageBtn">Pilih Gambar</button>
                            <input type="file" id="productImage" accept="image/*" style="display: none;">
                        </div>
                        
                        <div class="image-preview" id="imagePreview">
                            <img class="preview-image" id="previewImage" src="" alt="Preview Gambar">
                            <div class="image-actions">
                                <button type="button" class="btn-remove" id="removeImageBtn">Hapus</button>
                            </div>
                        </div>
                        
                        <div class="checkbox-item" style="margin-top: 15px;">
                            <input type="checkbox" id="deleteImage">
                            <label for="deleteImage">Hapus gambar yang sudah dipilih</label>
                        </div>
                    </div>
                    
                    <div class="form-actions">
                        <button class="btn btn-secondary" id="cancelButton">Batal</button>
                        <button class="btn btn-success" id="saveButton">Simpan Produk</button>
                    </div>
                </div>
            </div>
            
            <!-- Halaman Kelola Kategori -->
            <div class="page" id="categories-page">
                <div class="header">
                    <h2>Dashboard</h2>
                    <div class="user-info">
                        <span>Admin</span>
                    </div>
                </div>
                
                <div class="content-header">
                    <div class="page-title">Kelola Kategori</div>
                    <button class="btn btn-primary" id="addCategoryBtn">+ Tambah Kategori</button>
                </div>
                
                <div class="categories-list">
                    <div id="categoriesList">
                        <div class="empty-state">
                            <i>📂</i>
                            <h3>Tidak ada item</h3>
                            <p>Silakan tambahkan kategori pertama Anda</p>
                        </div>
                    </div>
                </div>
            </div>
            
            <!-- Halaman Scanner -->
            <div class="page" id="scanner-page">
                <div class="header">
                    <h2>Dashboard</h2>
                    <div class="user-info">
                        <span>Admin</span>
                    </div>
                </div>
                
                <div class="content-header">
                    <div class="page-title">Scan Produk</div>
                </div>
                
                <div class="scanner-container">
                    <div class="scanner-preview">
                        <video id="cameraPreview" autoplay playsinline></video>
                        <div class="scanner-overlay">
                            <div class="scanner-frame"></div>
                            <div class="scanner-guide">Arahkan kode batang ke dalam area ini</div>
                        </div>
                    </div>
                    
                    <div class="scanner-controls">
                        <button class="btn-scan" id="startCameraBtn">Mulai Kamera</button>
                        <button class="btn-scan btn-danger" id="stopCameraBtn" style="display: none;">Hentikan Kamera</button>
                        <button class="btn-scan btn-warning" id="switchCameraBtn" style="display: none;">Ganti Kamera</button>
                    </div>
                    
                    <div id="scannerStatus" class="scanner-status info">
                        Klik "Mulai Kamera" untuk memulai pemindaian barcode
                    </div>
                    
                    <div class="scanner-result" id="scannerResult" style="display: none;">
                        <p>Kode Batang: <span id="scannerBarcodeValue" class="barcode-value"></span></p>
                    </div>
                    
                    <div class="recent-products">
                        <h3>Terakhir Ditambahkan</h3>
                        <div id="recentProductsList">
                            <div class="empty-state">
                                <i>📦</i>
                                <h4>Tidak ada item</h4>
                            </div>
                        </div>
                    </div>
                </div>
                
                <div class="cart-button" id="scannerCartButton">
                    <i>🛒</i> Buka Keranjang (<span id="scannerCartCount">0</span>)
                </div>
                
                <!-- Canvas untuk pemrosesan gambar scanner -->
                <canvas id="scannerCanvas" style="display: none;"></canvas>
            </div>
            
            <!-- Halaman Keranjang Belanja -->
            <div class="page" id="cart-page">
                <div class="header">
                    <h2>Dashboard</h2>
                    <div class="user-info">
                        <span>Admin</span>
                    </div>
                </div>
                
                <div class="content-header">
                    <div class="page-title">Keranjang Belanja</div>
                    <button class="btn btn-danger" id="clearCartBtn">Bersihkan Keranjang</button>
                </div>
                
                <div class="cart-container">
                    <div class="cart-header">
                        <h3 id="cartItemsCount">1 Item</h3>
                    </div>
                    
                    <div class="cart-items" id="cartItems">
                        <!-- Item keranjang akan ditampilkan di sini -->
                        <div class="empty-state">
                            <i>🛒</i>
                            <h3>Keranjang Kosong</h3>
                            <p>Silakan tambahkan produk ke keranjang</p>
                        </div>
                    </div>
                    
                    <div class="cart-summary">
                        <div class="summary-row">
                            <span>Subtotal</span>
                            <span id="cartSubtotal">Rp 0</span>
                        </div>
                        <div class="summary-row">
                            <span>Pajak (10%)</span>
                            <span id="cartTax">Rp 0</span>
                        </div>
                        <div class="summary-row summary-total">
                            <span>Total</span>
                            <span id="cartTotal">Rp 0</span>
                        </div>
                    </div>
                    
                    <div class="form-actions">
                        <button class="btn btn-secondary" id="continueShoppingBtn">Lanjut Belanja</button>
                        <button class="btn btn-success" id="checkoutBtn" disabled>Lanjut ke Pembayaran</button>
                    </div>
                </div>
            </div>
            
            <!-- Halaman Checkout -->
            <div class="page" id="checkout-page">
                <div class="header">
                    <h2>Dashboard</h2>
                    <div class="user-info">
                        <span>Admin</span>
                    </div>
                </div>
                
                <div class="content-header">
                    <div class="page-title">Checkout</div>
                    <button class="btn btn-secondary" id="backToCartBtn">Kembali ke Keranjang</button>
                </div>
                
                <div class="checkout-container">
                    <div class="checkout-left">
                        <div class="form-container">
                            <h2 class="section-title">Metode Pembayaran</h2>
                            
                            <div class="payment-methods">
                                <div class="payment-method" data-method="cash">
                                    <div class="payment-icon">💵</div>
                                    <div>Cash</div>
                                </div>
                                <div class="payment-method" data-method="debit">
                                    <div class="payment-icon">💳</div>
                                    <div>Debit</div>
                                </div>
                                <div class="payment-method active" data-method="qris">
                                    <div class="payment-icon">📱</div>
                                    <div>QRIS</div>
                                </div>
                            </div>
                            
                            <div id="qrisContainer" class="qris-container">
                                <h3>Scan QR Code untuk Pembayaran</h3>
                                <div class="qris-code">
                                    QR Code akan muncul di sini
                                </div>
                                <p>Arahkan kamera ke QR code untuk melakukan pembayaran</p>
                            </div>
                            
                            <div id="cashContainer" style="display: none;">
                                <h3>Pembayaran Tunai</h3>
                                
                                <div class="form-group">
                                    <label class="form-label">Nominal Pembayaran</label>
                                    <input type="text" class="form-control" id="paymentAmount" placeholder="Rp 0" readonly>
                                </div>
                                
                                <div class="form-group">
                                    <label class="form-label">Uang Pas</label>
                                    <div class="denomination-grid">
                                        <div class="denomination-btn" data-amount="100000">100K</div>
                                        <div class="denomination-btn" data-amount="75000">75K</div>
                                        <div class="denomination-btn" data-amount="50000">50K</div>
                                        <div class="denomination-btn" data-amount="20000">20K</div>
                                        <div class="denomination-btn" data-amount="10000">10K</div>
                                        <div class="denomination-btn" data-amount="5000">5K</div>
                                        <div class="denomination-btn" data-amount="2000">2K</div>
                                        <div class="denomination-btn" data-amount="1000">1K</div>
                                        <div class="denomination-btn" data-amount="500">500</div>
                                        <div class="denomination-btn" data-amount="200">200</div>
                                        <div class="denomination-btn" data-amount="100">100</div>
                                    </div>
                                </div>
                                
                                <div class="payment-input">
                                    <input type="number" class="form-control" id="customAmount" placeholder="Masukkan jumlah custom">
                                </div>
                                
                                <div class="summary-row">
                                    <span>Total Belanja</span>
                                    <span id="checkoutTotal">Rp 0</span>
                                </div>
                                <div class="summary-row">
                                    <span>Jumlah Bayar</span>
                                    <span id="checkoutPaid">Rp 0</span>
                                </div>
                                <div class="summary-row summary-total">
                                    <span>Kembalian</span>
                                    <span id="checkoutChange">Rp 0</span>
                                </div>
                            </div>
                            
                            <div class="payment-actions">
                                <button class="btn btn-secondary" id="cancelPaymentBtn">Batal</button>
                                <button class="btn btn-success btn-pay" id="processPaymentBtn">Bayar</button>
                            </div>
                        </div>
                    </div>
                    
                    <div class="checkout-right">
                        <div class="form-container">
                            <h2 class="section-title">Rincian Belanja</h2>
                            
                            <div id="checkoutItems">
                                <!-- Item checkout akan ditampilkan di sini -->
                            </div>
                            
                            <div class="cart-summary">
                                <div class="summary-row">
                                    <span>Subtotal</span>
                                    <span id="checkoutSubtotal">Rp 0</span>
                                </div>
                                <div class="summary-row">
                                    <span>Pajak (10%)</span>
                                    <span id="checkoutTax">Rp 0</span>
                                </div>
                                <div class="summary-row summary-total">
                                    <span>Total</span>
                                    <span id="checkoutTotalRight">Rp 0</span>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            
            <!-- Halaman Konfirmasi Transaksi -->
            <div class="page" id="confirmation-page">
                <div class="header">
                    <h2>Dashboard</h2>
                    <div class="user-info">
                        <span>Admin</span>
                    </div>
                </div>
                
                <div class="content-header">
                    <div class="page-title">Konfirmasi Transaksi</div>
                </div>
                
                <div class="transaction-details">
                    <div class="transaction-header">
                        <div>
                            <div class="transaction-date" id="transactionDate">16 Nov 2025 • 04:39</div>
                            <div class="transaction-id" id="transactionId">#1</div>
                        </div>
                        <button class="btn btn-secondary" id="changeDateBtn">Ubah Tanggal</button>
                    </div>
                    
                    <div class="transaction-items" id="confirmationItems">
                        <!-- Item transaksi akan ditampilkan di sini -->
                    </div>
                    
                    <div class="transaction-summary">
                        <div class="summary-item">
                            <span class="summary-label">Total</span>
                            <span class="summary-value" id="confirmationTotal">Rp 5.000</span>
                        </div>
                        <div class="summary-item">
                            <span class="summary-label">Bayar (QRIS)</span>
                            <span class="summary-value" id="confirmationPaid">Rp 5.000</span>
                        </div>
                        <div class="summary-item">
                            <span class="summary-label">Kembalian</span>
                            <span class="summary-value" id="confirmationChange">Rp 0</span>
                        </div>
                    </div>
                    
                    <div class="print-option">
                        <input type="checkbox" id="autoPrint">
                        <label for="autoPrint">Print Otomatis</label>
                        <span style="margin-left: 5px;">?</span>
                    </div>
                    
                    <div class="form-actions">
                        <button class="btn btn-success" id="saveTransactionBtn">Simpan Transaksi</button>
                    </div>
                </div>
            </div>
            
            <!-- Halaman Rincian Transaksi -->
            <div class="page" id="transaction-detail-page">
                <div class="header">
                    <h2>Dashboard</h2>
                    <div class="user-info">
                        <span>Admin</span>
                    </div>
                </div>
                
                <div class="content-header">
                    <div class="page-title">Rincian Transaksi</div>
                    <button class="btn btn-secondary" id="backToTransactionsBtn">Kembali</button>
                </div>
                
                <div class="transaction-details">
                    <div class="transaction-header">
                        <div>
                            <div class="transaction-date" id="detailDate">16 Nov 2025 04:39</div>
                            <div class="transaction-id" id="detailId">#1</div>
                        </div>
                    </div>
                    
                    <div class="transaction-items" id="detailItems">
                        <!-- Item transaksi akan ditampilkan di sini -->
                    </div>
                    
                    <div class="transaction-summary">
                        <div class="summary-item">
                            <span class="summary-label">Total</span>
                            <span class="summary-value" id="detailTotal">Rp 5.000</span>
                        </div>
                        <div class="summary-item">
                            <span class="summary-label">Bayar (QRIS)</span>
                            <span class="summary-value" id="detailPaid">Rp 5.000</span>
                        </div>
                        <div class="summary-item">
                            <span class="summary-label">Kembalian</span>
                            <span class="summary-value" id="detailChange">Rp 0</span>
                        </div>
                    </div>
                </div>
            </div>
            
            <!-- Halaman lainnya -->
            <div class="page" id="transactions-page">
                <div class="content-header">
                    <div class="page-title">Riwayat Transaksi</div>
                </div>
                <div class="empty-state">
                    <i>📋</i>
                    <h3>Halaman Riwayat Transaksi</h3>
                    <p>Fitur ini akan segera tersedia</p>
                </div>
            </div>
            
            <div class="page" id="reports-page">
                <div class="content-header">
                    <div class="page-title">Laporan Transaksi</div>
                </div>
                <div class="empty-state">
                    <i>📊</i>
                    <h3>Halaman Laporan Transaksi</h3>
                    <p>Fitur ini akan segera tersedia</p>
                </div>
            </div>
            
            <div class="page" id="settings-page">
                <div class="content-header">
                    <div class="page-title">Pengaturan</div>
                </div>
                <div class="empty-state">
                    <i>⚙️</i>
                    <h3>Halaman Pengaturan</h3>
                    <p>Fitur ini akan segera tersedia</p>
                </div>
            </div>
            
            <div class="page" id="backup-page">
                <div class="content-header">
                    <div class="page-title">Backup & Restore</div>
                </div>
                <div class="empty-state">
                    <i>💾</i>
                    <h3>Halaman Backup & Restore</h3>
                    <p>Fitur ini akan segera tersedia</p>
                </div>
            </div>
            
            <div class="page" id="upgrade-page">
                <div class="content-header">
                    <div class="page-title">Upgrade ke Versi Pro</div>
                </div>
                <div class="empty-state">
                    <i>🚀</i>
                    <h3>Halaman Upgrade</h3>
                    <p>Fitur ini akan segera tersedia</p>
                </div>
            </div>
            
            <div class="page" id="feedback-page">
                <div class="content-header">
                    <div class="page-title">Kirim Masukan</div>
                </div>
                <div class="empty-state">
                    <i>📩</i>
                    <h3>Halaman Kirim Masukan</h3>
                    <p>Fitur ini akan segera tersedia</p>
                </div>
            </div>
            
            <div class="page" id="rating-page">
                <div class="content-header">
                    <div class="page-title">Beri Nilai Aplikasi</div>
                </div>
                <div class="empty-state">
                    <i>⭐</i>
                    <h3>Halaman Beri Nilai Aplikasi</h3>
                    <p>Fitur ini akan segera tersedia</p>
                </div>
            </div>
            
            <div class="page" id="changelog-page">
                <div class="content-header">
                    <div class="page-title">Catatan Perubahan</div>
                </div>
                <div class="empty-state">
                    <i>📝</i>
                    <h3>Halaman Catatan Perubahan</h3>
                    <p>Fitur ini akan segera tersedia</p>
                </div>
            </div>
        </div>
    </div>
    
    <!-- Modal Tambah Kategori -->
    <div class="modal" id="categoryModal">
        <div class="modal-content">
            <div class="modal-header">
                <div class="modal-title">Tambah Kategori</div>
                <button class="close-modal" id="closeCategoryModal">&times;</button>
            </div>
            <div class="form-group">
                <label class="form-label" for="categoryName">Masukkan Kategori</label>
                <input type="text" class="form-control" id="categoryName" placeholder="Nama kategori">
            </div>
            
            <div class="keyboard">
                <div class="keyboard-row">
                    <div class="key">1</div>
                    <div class="key">2</div>
                    <div class="key">3</div>
                    <div class="key">4</div>
                    <div class="key">5</div>
                    <div class="key">6</div>
                    <div class="key">7</div>
                    <div class="key">8</div>
                    <div class="key">9</div>
                    <div class="key">0</div>
                </div>
                <div class="keyboard-row">
                    <div class="key">Q</div>
                    <div class="key">W</div>
                    <div class="key">E</div>
                    <div class="key">R</div>
                    <div class="key">T</div>
                    <div class="key">Y</div>
                    <div class="key">U</div>
                    <div class="key">I</div>
                    <div class="key">O</div>
                    <div class="key">P</div>
                </div>
                <div class="keyboard-row">
                    <div class="key">A</div>
                    <div class="key">S</div>
                    <div class="key">D</div>
                    <div class="key">F</div>
                    <div class="key">G</div>
                    <div class="key">H</div>
                    <div class="key">J</div>
                    <div class="key">K</div>
                    <div class="key">L</div>
                </div>
                <div class="keyboard-row">
                    <div class="key">Z</div>
                    <div class="key">X</div>
                    <div class="key">C</div>
                    <div class="key">V</div>
                    <div class="key">B</div>
                    <div class="key">N</div>
                    <div class="key">M</div>
                    <div class="key key-wide">⌫</div>
                </div>
                <div class="keyboard-row">
                    <div class="key key-space">Spasi</div>
                </div>
            </div>
            
            <div class="form-actions">
                <button class="btn btn-secondary" id="cancelCategoryBtn">Batal</button>
                <button class="btn btn-success" id="saveCategoryBtn">Simpan</button>
            </div>
        </div>
    </div>

    <script>
        // Data aplikasi
        let products = JSON.parse(localStorage.getItem('kaspos_products')) || [];
        let categories = JSON.parse(localStorage.getItem('kaspos_categories')) || [];
        let cart = JSON.parse(localStorage.getItem('kaspos_cart')) || [];
        let transactions = JSON.parse(localStorage.getItem('kaspos_transactions')) || [];
        let currentPage = 'products';
        let editingProductId = null;
        let currentPaymentMethod = 'qris';
        let stream = null;
        let isScanning = false;
        let currentFacingMode = 'environment'; // 'environment' untuk kamera belakang, 'user' untuk depan
        let quaggaScannerInstance = null;
        
        // DOM Elements
        const pages = document.querySelectorAll('.page');
        const menuItems = document.querySelectorAll('.menu-item');
        const addProductBtn = document.getElementById('addProductBtn');
        const backButton = document.getElementById('backButton');
        const manageCategoriesLink = document.getElementById('manageCategoriesLink');
        const addCategoryBtn = document.getElementById('addCategoryBtn');
        const categoryModal = document.getElementById('categoryModal');
        const closeCategoryModal = document.getElementById('closeCategoryModal');
        const cancelCategoryBtn = document.getElementById('cancelCategoryBtn');
        const saveCategoryBtn = document.getElementById('saveCategoryBtn');
        const categoryName = document.getElementById('categoryName');
        const keyboardKeys = document.querySelectorAll('.key');
        const cartButton = document.getElementById('cartButton');
        const scannerCartButton = document.getElementById('scannerCartButton');
        const clearCartBtn = document.getElementById('clearCartBtn');
        const continueShoppingBtn = document.getElementById('continueShoppingBtn');
        const checkoutBtn = document.getElementById('checkoutBtn');
        const backToCartBtn = document.getElementById('backToCartBtn');
        const paymentMethods = document.querySelectorAll('.payment-method');
        const qrisContainer = document.getElementById('qrisContainer');
        const cashContainer = document.getElementById('cashContainer');
        const denominationBtns = document.querySelectorAll('.denomination-btn');
        const customAmount = document.getElementById('customAmount');
        const paymentAmount = document.getElementById('paymentAmount');
        const cancelPaymentBtn = document.getElementById('cancelPaymentBtn');
        const processPaymentBtn = document.getElementById('processPaymentBtn');
        const saveTransactionBtn = document.getElementById('saveTransactionBtn');
        const backToTransactionsBtn = document.getElementById('backToTransactionsBtn');
        const startCameraBtn = document.getElementById('startCameraBtn');
        const stopCameraBtn = document.getElementById('stopCameraBtn');
        const switchCameraBtn = document.getElementById('switchCameraBtn');
        const cameraPreview = document.getElementById('cameraPreview');
        const scannerStatus = document.getElementById('scannerStatus');
        const scannerResult = document.getElementById('scannerResult');
        const scannerBarcodeValue = document.getElementById('scannerBarcodeValue');
        const scannerCanvas = document.getElementById('scannerCanvas');
        
        // Inisialisasi aplikasi
        function initApp() {
            // Load data dari localStorage
            loadCategories();
            renderProducts();
            updateCart();
            setupEventListeners();
            updateTrialNotice();
            
            // Tambahkan beberapa produk contoh jika belum ada
            if (products.length === 0) {
                addSampleProducts();
            }
        }
        
        // Tambahkan produk contoh
        function addSampleProducts() {
            const sampleProducts = [
                {
                    id: '1',
                    title: 'Aqua',
                    description: '600ml',
                    category: 'minuman',
                    barcode: '8886008101059', // Barcode Aqua
                    price: 5000,
                    cost: 3000,
                    useImage: false,
                    useShapeColor: false,
                    image: '',
                    deleteImage: false,
                    stock: 10,
                    createdAt: new Date().toISOString()
                },
                {
                    id: '2',
                    title: 'Indomie Goreng',
                    description: '75g',
                    category: 'makanan',
                    barcode: '8992702020019', // Barcode Indomie
                    price: 3000,
                    cost: 2000,
                    useImage: false,
                    useShapeColor: false,
                    image: '',
                    deleteImage: false,
                    stock: 20,
                    createdAt: new Date().toISOString()
                },
                {
                    id: '3',
                    title: 'Pocari Sweat',
                    description: '500ml',
                    category: 'minuman',
                    barcode: '8999999035150', // Barcode Pocari Sweat
                    price: 8000,
                    cost: 5000,
                    useImage: false,
                    useShapeColor: false,
                    image: '',
                    deleteImage: false,
                    stock: 15,
                    createdAt: new Date().toISOString()
                }
            ];
            
            products = [...products, ...sampleProducts];
            localStorage.setItem('kaspos_products', JSON.stringify(products));
            renderProducts();
        }
        
        // Setup event listeners
        function setupEventListeners() {
            // Navigasi menu
            menuItems.forEach(item => {
                item.addEventListener('click', function() {
                    const pageId = this.getAttribute('data-page');
                    navigateToPage(pageId);
                });
            });
            
            // Tombol navigasi
            addProductBtn.addEventListener('click', () => navigateToPage('add-product'));
            backButton.addEventListener('click', () => navigateToPage('products'));
            manageCategoriesLink.addEventListener('click', (e) => {
                e.preventDefault();
                navigateToPage('categories');
            });
            cartButton.addEventListener('click', () => navigateToPage('cart'));
            scannerCartButton.addEventListener('click', () => navigateToPage('cart'));
            continueShoppingBtn.addEventListener('click', () => navigateToPage('products'));
            checkoutBtn.addEventListener('click', () => navigateToPage('checkout'));
            backToCartBtn.addEventListener('click', () => navigateToPage('cart'));
            cancelPaymentBtn.addEventListener('click', () => navigateToPage('cart'));
            backToTransactionsBtn.addEventListener('click', () => navigateToPage('transactions'));
            
            // Keranjang
            clearCartBtn.addEventListener('click', clearCart);
            
            // Modal kategori
            addCategoryBtn.addEventListener('click', () => categoryModal.style.display = 'flex');
            closeCategoryModal.addEventListener('click', () => categoryModal.style.display = 'none');
            cancelCategoryBtn.addEventListener('click', () => categoryModal.style.display = 'none');
            saveCategoryBtn.addEventListener('click', saveCategory);
            
            // Keyboard virtual
            keyboardKeys.forEach(key => {
                key.addEventListener('click', function() {
                    const keyText = this.textContent;
                    if (keyText === '⌫') {
                        categoryName.value = categoryName.value.slice(0, -1);
                    } else if (keyText === 'Spasi') {
                        categoryName.value += ' ';
                    } else {
                        categoryName.value += keyText;
                    }
                });
            });
            
            // Pembayaran
            paymentMethods.forEach(method => {
                method.addEventListener('click', function() {
                    paymentMethods.forEach(m => m.classList.remove('active'));
                    this.classList.add('active');
                    currentPaymentMethod = this.getAttribute('data-method');
                    
                    if (currentPaymentMethod === 'qris') {
                        qrisContainer.style.display = 'block';
                        cashContainer.style.display = 'none';
                    } else {
                        qrisContainer.style.display = 'none';
                        cashContainer.style.display = 'block';
                    }
                });
            });
            
            // Denominasi uang
            denominationBtns.forEach(btn => {
                btn.addEventListener('click', function() {
                    const amount = parseInt(this.getAttribute('data-amount'));
                    addToPaymentAmount(amount);
                });
            });
            
            customAmount.addEventListener('input', function() {
                const value = parseInt(this.value) || 0;
                if (value > 0) {
                    paymentAmount.value = formatCurrency(value);
                    calculateChange();
                }
            });
            
            processPaymentBtn.addEventListener('click', processPayment);
            saveTransactionBtn.addEventListener('click', saveTransaction);
            
            // Scanner
            startCameraBtn.addEventListener('click', startCamera);
            stopCameraBtn.addEventListener('click', stopCamera);
            switchCameraBtn.addEventListener('click', switchCamera);
            
            // Setup form produk
            setupProductForm();
        }
        
        // Navigasi antar halaman
        function navigateToPage(pageId) {
            // Sembunyikan semua halaman
            pages.forEach(page => page.classList.remove('active'));
            
            // Tampilkan halaman yang dipilih
            document.getElementById(`${pageId}-page`).classList.add('active');
            
            // Update menu aktif
            menuItems.forEach(item => {
                if (item.getAttribute('data-page') === pageId) {
                    item.classList.add('active');
                } else {
                    item.classList.remove('active');
                }
            });
            
            // Update state halaman saat ini
            currentPage = pageId;
            
            // Load data halaman jika diperlukan
            if (pageId === 'products') {
                renderProducts();
            } else if (pageId === 'categories') {
                renderCategories();
            } else if (pageId === 'scanner') {
                renderRecentProducts();
                // Jika scanner aktif, hentikan saat pindah halaman
                if (isScanning) {
                    stopCamera();
                }
            } else if (pageId === 'cart') {
                renderCart();
            } else if (pageId === 'checkout') {
                renderCheckout();
            } else if (pageId === 'confirmation') {
                renderConfirmation();
            } else if (pageId === 'transaction-detail') {
                renderTransactionDetail();
            }
        }
        
        // Setup form produk
        function setupProductForm() {
            const productTitle = document.getElementById('productTitle');
            const productDescription = document.getElementById('productDescription');
            const productCategory = document.getElementById('productCategory');
            const productBarcode = document.getElementById('productBarcode');
            const productPrice = document.getElementById('productPrice');
            const productCost = document.getElementById('productCost');
            const useImage = document.getElementById('useImage');
            const useShapeColor = document.getElementById('useShapeColor');
            const productImage = document.getElementById('productImage');
            const selectImageBtn = document.getElementById('selectImageBtn');
            const imageUploadArea = document.getElementById('imageUploadArea');
            const imagePreview = document.getElementById('imagePreview');
            const previewImage = document.getElementById('previewImage');
            const removeImageBtn = document.getElementById('removeImageBtn');
            const deleteImage = document.getElementById('deleteImage');
            const titleError = document.getElementById('titleError');
            const titleCounter = document.getElementById('titleCounter');
            const descriptionCounter = document.getElementById('descriptionCounter');
            const saveButton = document.getElementById('saveButton');
            const cancelButton = document.getElementById('cancelButton');
            
            // Event listeners form produk
            productTitle.addEventListener('input', updateTitleCounter);
            productTitle.addEventListener('blur', validateTitle);
            productDescription.addEventListener('input', updateDescriptionCounter);
            selectImageBtn.addEventListener('click', () => productImage.click());
            productImage.addEventListener('change', handleImageSelect);
            imageUploadArea.addEventListener('dragover', handleDragOver);
            imageUploadArea.addEventListener('drop', handleDrop);
            removeImageBtn.addEventListener('click', removeImage);
            saveButton.addEventListener('click', saveProduct);
            cancelButton.addEventListener('click', cancelForm);
            
            // Update character counters
            function updateTitleCounter() {
                titleCounter.textContent = productTitle.value.length;
            }
            
            function updateDescriptionCounter() {
                descriptionCounter.textContent = productDescription.value.length;
            }
            
            // Validate title field
            function validateTitle() {
                if (productTitle.value.trim() === '') {
                    productTitle.classList.add('error');
                    titleError.style.display = 'block';
                    return false;
                } else {
                    productTitle.classList.remove('error');
                    titleError.style.display = 'none';
                    return true;
                }
            }
            
            // Handle image selection
            function handleImageSelect(e) {
                const file = e.target.files[0];
                if (file) {
                    if (file.type.startsWith('image/')) {
                        const reader = new FileReader();
                        reader.onload = function(e) {
                            previewImage.src = e.target.result;
                            imagePreview.style.display = 'block';
                            imageUploadArea.style.display = 'none';
                        };
                        reader.readAsDataURL(file);
                    } else {
                        alert('Silakan pilih file gambar yang valid.');
                    }
                }
            }
            
            // Handle drag over
            function handleDragOver(e) {
                e.preventDefault();
                imageUploadArea.style.borderColor = '#3498db';
                imageUploadArea.style.backgroundColor = '#f8f9fa';
            }
            
            // Handle drop
            function handleDrop(e) {
                e.preventDefault();
                imageUploadArea.style.borderColor = '#ddd';
                imageUploadArea.style.backgroundColor = 'transparent';
                
                const files = e.dataTransfer.files;
                if (files.length > 0) {
                    const file = files[0];
                    if (file.type.startsWith('image/')) {
                        const reader = new FileReader();
                        reader.onload = function(e) {
                            previewImage.src = e.target.result;
                            imagePreview.style.display = 'block';
                            imageUploadArea.style.display = 'none';
                        };
                        reader.readAsDataURL(file);
                    } else {
                        alert('Silakan pilih file gambar yang valid.');
                    }
                }
            }
            
            // Remove image
            function removeImage() {
                previewImage.src = '';
                imagePreview.style.display = 'none';
                imageUploadArea.style.display = 'block';
                productImage.value = '';
                deleteImage.checked = false;
            }
            
            // Cancel form
            function cancelForm() {
                if (confirm('Apakah Anda yakin ingin membatalkan? Perubahan yang belum disimpan akan hilang.')) {
                    navigateToPage('products');
                }
            }
        }
        
        // Simpan produk
        function saveProduct() {
            const productTitle = document.getElementById('productTitle');
            const productDescription = document.getElementById('productDescription');
            const productCategory = document.getElementById('productCategory');
            const productBarcode = document.getElementById('productBarcode');
            const productPrice = document.getElementById('productPrice');
            const productCost = document.getElementById('productCost');
            const useImage = document.getElementById('useImage');
            const useShapeColor = document.getElementById('useShapeColor');
            const previewImage = document.getElementById('previewImage');
            const deleteImage = document.getElementById('deleteImage');
            
            // Validate required fields
            if (productTitle.value.trim() === '') {
                alert('Judul produk harus diisi!');
                productTitle.focus();
                return;
            }
            
            if (productPrice.value === '' || parseFloat(productPrice.value) <= 0) {
                alert('Harga jual harus diisi dengan nilai yang valid!');
                productPrice.focus();
                return;
            }
            
            // Check trial limit
            if (products.length >= 10) {
                alert('Anda telah mencapai batas maksimal 10 produk pada versi trial.');
                return;
            }
            
            // Create new product object
            const newProduct = {
                id: Date.now().toString(),
                title: productTitle.value.trim(),
                description: productDescription.value.trim(),
                category: productCategory.value,
                barcode: productBarcode.value.trim(),
                price: parseFloat(productPrice.value),
                cost: productCost.value ? parseFloat(productCost.value) : 0,
                useImage: useImage.checked,
                useShapeColor: useShapeColor.checked,
                image: previewImage.src || '',
                deleteImage: deleteImage.checked,
                stock: 0, // Default stock
                createdAt: new Date().toISOString()
            };
            
            // Add to products array
            products.push(newProduct);
            
            // Save to localStorage
            localStorage.setItem('kaspos_products', JSON.stringify(products));
            
            // Show success message
            alert('Produk berhasil disimpan!');
            
            // Reset form
            document.getElementById('productTitle').value = '';
            document.getElementById('productDescription').value = '';
            document.getElementById('productCategory').value = '';
            document.getElementById('productBarcode').value = '';
            document.getElementById('productPrice').value = '';
            document.getElementById('productCost').value = '';
            document.getElementById('useImage').checked = false;
            document.getElementById('useShapeColor').checked = false;
            document.getElementById('imagePreview').style.display = 'none';
            document.getElementById('imageUploadArea').style.display = 'block';
            document.getElementById('deleteImage').checked = false;
            
            // Navigate back to products page
            navigateToPage('products');
        }
        
        // Simpan kategori
        function saveCategory() {
            if (categoryName.value.trim() === '') {
                alert('Nama kategori tidak boleh kosong!');
                return;
            }
            
            // Check if category already exists
            if (categories.find(c => c.name.toLowerCase() === categoryName.value.trim().toLowerCase())) {
                alert('Kategori dengan nama tersebut sudah ada!');
                return;
            }
            
            // Add new category
            const newCategory = {
                id: Date.now().toString(),
                name: categoryName.value.trim(),
                createdAt: new Date().toISOString()
            };
            
            categories.push(newCategory);
            localStorage.setItem('kaspos_categories', JSON.stringify(categories));
            
            // Close modal and reset form
            categoryModal.style.display = 'none';
            categoryName.value = '';
            
            // Reload categories
            loadCategories();
            renderCategories();
        }
        
        // Load categories to select options
        function loadCategories() {
            const categorySelect = document.getElementById('productCategory');
            const categoryFilter = document.getElementById('categoryFilter');
            
            // Clear existing options (except first one)
            while (categorySelect.children.length > 1) {
                categorySelect.removeChild(categorySelect.lastChild);
            }
            
            while (categoryFilter.children.length > 1) {
                categoryFilter.removeChild(categoryFilter.lastChild);
            }
            
            // Add categories to selects
            categories.forEach(category => {
                const option1 = document.createElement('option');
                option1.value = category.id;
                option1.textContent = category.name;
                categorySelect.appendChild(option1);
                
                const option2 = document.createElement('option');
                option2.value = category.id;
                option2.textContent = category.name;
                categoryFilter.appendChild(option2);
            });
        }
        
        // Render products
        function renderProducts() {
            const productList = document.getElementById('productList');
            const categoryFilter = document.getElementById('categoryFilter');
            const sortFilter = document.getElementById('sortFilter');
            
            let filteredProducts = [...products];
            
            // Filter by category
            const selectedCategory = categoryFilter.value;
            if (selectedCategory !== 'Semua Kategori') {
                filteredProducts = filteredProducts.filter(p => p.category === selectedCategory);
            }
            
            // Sort products
            const sortOption = sortFilter.value;
            switch (sortOption) {
                case 'Nama Produk (A-Z)':
                    filteredProducts.sort((a, b) => a.title.localeCompare(b.title));
                    break;
                case 'Nama Produk (Z-A)':
                    filteredProducts.sort((a, b) => b.title.localeCompare(a.title));
                    break;
                case 'Harga Terendah':
                    filteredProducts.sort((a, b) => a.price - b.price);
                    break;
                case 'Harga Tertinggi':
                    filteredProducts.sort((a, b) => b.price - a.price);
                    break;
            }
            
            // Update trial notice
            updateTrialNotice();
            
            // Render products
            if (filteredProducts.length === 0) {
                productList.innerHTML = `
                    <div class="empty-state">
                        <i>📦</i>
                        <h3>Tidak ada item</h3>
                        <p>Silakan tambahkan produk pertama Anda</p>
                    </div>
                `;
            } else {
                productList.innerHTML = `
                    <div class="product-grid">
                        ${filteredProducts.map(product => `
                            <div class="product-card" onclick="addToCart('${product.id}')">
                                <div class="product-image">
                                    ${product.image ? `<img src="${product.image}" alt="${product.title}" style="width:100%;height:100%;object-fit:cover;">` : product.title.charAt(0).toUpperCase()}
                                </div>
                                <div class="product-name">${product.title}</div>
                                <div class="product-description">${product.description}</div>
                                <div class="product-price">Rp ${product.price.toLocaleString('id-ID')}</div>
                                <div class="product-barcode" style="font-size: 12px; color: #7f8c8d;">${product.barcode || 'Tidak ada barcode'}</div>
                            </div>
                        `).join('')}
                    </div>
                `;
            }
        }
        
        // Render categories
        function renderCategories() {
            const categoriesList = document.getElementById('categoriesList');
            
            if (categories.length === 0) {
                categoriesList.innerHTML = `
                    <div class="empty-state">
                        <i>📂</i>
                        <h3>Tidak ada item</h3>
                        <p>Silakan tambahkan kategori pertama Anda</p>
                    </div>
                `;
            } else {
                categoriesList.innerHTML = `
                    ${categories.map(category => `
                        <div class="category-item">
                            <div class="category-name">${category.name}</div>
                            <div class="category-actions">
                                <button class="btn btn-primary" onclick="editCategory('${category.id}')">Edit</button>
                                <button class="btn btn-danger" onclick="deleteCategory('${category.id}')">Hapus</button>
                            </div>
                        </div>
                    `).join('')}
                `;
            }
        }
        
        // Render recent products for scanner page
        function renderRecentProducts() {
            const recentProductsList = document.getElementById('recentProductsList');
            const recentProducts = products.slice(-3).reverse(); // Get last 3 products
            
            if (recentProducts.length === 0) {
                recentProductsList.innerHTML = `
                    <div class="empty-state">
                        <i>📦</i>
                        <h4>Tidak ada item</h4>
                    </div>
                `;
            } else {
                recentProductsList.innerHTML = `
                    ${recentProducts.map(product => `
                        <div class="product-card" style="margin-bottom: 10px;">
                            <div class="product-name">${product.title}</div>
                            <div class="product-description">${product.description}</div>
                            <div class="product-price">Rp ${product.price.toLocaleString('id-ID')}</div>
                            <div class="product-barcode" style="font-size: 12px; color: #7f8c8d;">${product.barcode || 'Tidak ada barcode'}</div>
                        </div>
                    `).join('')}
                `;
            }
        }
        
        // Scanner functions - DIUBAH SEPERTI PADA KODE YANG DIBERIKAN
        function startCamera() {
            try {
                // Hentikan scanner sebelumnya jika ada
                if (quaggaScannerInstance) {
                    quaggaScannerInstance.stop();
                }

                // Hentikan kamera sebelumnya
                stopCameraStream();

                // Update status
                scannerStatus.textContent = 'Menginisialisasi kamera...';
                scannerStatus.className = 'scanner-status info';

                // Inisialisasi Quagga
                Quagga.init({
                    inputStream: {
                        name: "Live",
                        type: "LiveStream",
                        target: cameraPreview,
                        constraints: {
                            width: 640,
                            height: 480,
                            facingMode: currentFacingMode
                        }
                    },
                    decoder: {
                        readers: [
                            "code_128_reader",
                            "ean_reader",
                            "ean_8_reader",
                            "code_39_reader",
                            "upc_reader",
                            "upc_e_reader"
                        ]
                    },
                    locator: {
                        patchSize: "medium",
                        halfSample: true
                    },
                    locate: true,
                    numOfWorkers: 2
                }, function(err) {
                    if (err) {
                        console.error('Error initializing Quagga:', err);
                        scannerStatus.textContent = 'Tidak dapat mengakses kamera. Pastikan Anda memberikan izin akses kamera.';
                        scannerStatus.className = 'scanner-status error';
                        return;
                    }
                    console.log('Quagga initialized successfully');
                    Quagga.start();
                    quaggaScannerInstance = Quagga;
                    
                    startCameraBtn.style.display = 'none';
                    stopCameraBtn.style.display = 'inline-block';
                    switchCameraBtn.style.display = 'inline-block';
                    isScanning = true;

                    // Update status
                    scannerStatus.textContent = 'Kamera aktif - Arahkan kode batang ke area pemindaian';
                    scannerStatus.className = 'scanner-status info';
                });

                Quagga.onDetected(function(result) {
                    const code = result.codeResult.code;
                    handleScannedCode(code);
                });
            } catch (err) {
                console.error('Error accessing camera:', err);
                scannerStatus.textContent = 'Tidak dapat mengakses kamera. Pastikan Anda memberikan izin akses kamera.';
                scannerStatus.className = 'scanner-status error';
            }
        }
        
        function stopCamera() {
            if (quaggaScannerInstance) {
                quaggaScannerInstance.stop();
                quaggaScannerInstance = null;
            }
            stopCameraStream();
            startCameraBtn.style.display = 'inline-block';
            stopCameraBtn.style.display = 'none';
            switchCameraBtn.style.display = 'none';
            isScanning = false;
            scannerResult.style.display = 'none';

            // Update status
            scannerStatus.textContent = 'Kamera dihentikan - Klik "Mulai Kamera" untuk memulai pemindaian';
            scannerStatus.className = 'scanner-status info';
        }
        
        function switchCamera() {
            // Switch between front and back camera
            currentFacingMode = currentFacingMode === 'environment' ? 'user' : 'environment';
            stopCamera();
            setTimeout(startCamera, 500); // Restart camera with new facing mode
        }
        
        function stopCameraStream() {
            if (stream) {
                stream.getTracks().forEach(track => track.stop());
                stream = null;
            }
        }
        
        function handleScannedCode(code) {
            // Tampilkan hasil scan
            scannerBarcodeValue.textContent = code;
            scannerResult.style.display = 'block';
            
            // Find product by barcode
            const product = products.find(p => p.barcode === code);
            
            if (product) {
                // Product found, add to cart
                addToCart(product.id);
                
                // Update status
                scannerStatus.textContent = `Berhasil memindai: ${product.title} - Ditambahkan ke keranjang`;
                scannerStatus.className = 'scanner-status success';
                
                // Show notification
                showNotification(`${product.title} berhasil di-scan dan ditambahkan ke keranjang`);
                
                // Continue scanning after a short delay
                setTimeout(() => {
                    scannerStatus.textContent = 'Kamera aktif - Arahkan kode batang ke area pemindaian';
                    scannerStatus.className = 'scanner-status info';
                }, 3000);
            } else {
                // Product not found
                scannerStatus.textContent = `Kode "${code}" tidak ditemukan dalam database produk`;
                scannerStatus.className = 'scanner-status error';
                
                // Continue scanning after a short delay
                setTimeout(() => {
                    scannerStatus.textContent = 'Kamera aktif - Arahkan kode batang ke area pemindaian';
                    scannerStatus.className = 'scanner-status info';
                }, 3000);
            }
        }
        
        // Simulate barcode scan for testing
        function simulateBarcodeScan() {
            if (products.length > 0) {
                const randomProduct = products[Math.floor(Math.random() * products.length)];
                if (randomProduct.barcode) {
                    handleScannedCode(randomProduct.barcode);
                } else {
                    scannerStatus.textContent = 'Produk contoh tidak memiliki barcode. Tambahkan barcode ke produk terlebih dahulu.';
                    scannerStatus.className = 'scanner-status error';
                }
            } else {
                scannerStatus.textContent = 'Tidak ada produk yang tersedia untuk di-scan.';
                scannerStatus.className = 'scanner-status error';
            }
        }
        
        // Keranjang belanja
        function addToCart(productId) {
            const product = products.find(p => p.id === productId);
            if (!product) return;
            
            const existingItem = cart.find(item => item.productId === productId);
            
            if (existingItem) {
                existingItem.quantity += 1;
            } else {
                cart.push({
                    productId: productId,
                    quantity: 1,
                    price: product.price,
                    name: product.title,
                    description: product.description
                });
            }
            
            updateCart();
        }
        
        function removeFromCart(productId) {
            cart = cart.filter(item => item.productId !== productId);
            updateCart();
        }
        
        function updateCart() {
            localStorage.setItem('kaspos_cart', JSON.stringify(cart));
            
            // Update cart count
            const totalItems = cart.reduce((sum, item) => sum + item.quantity, 0);
            document.getElementById('cartCount').textContent = totalItems;
            document.getElementById('cartButtonCount').textContent = totalItems;
            document.getElementById('scannerCartCount').textContent = totalItems;
            
            // Enable/disable checkout button
            document.getElementById('checkoutBtn').disabled = totalItems === 0;
        }
        
        function clearCart() {
            if (cart.length === 0) return;
            
            if (confirm('Apakah Anda yakin ingin mengosongkan keranjang?')) {
                cart = [];
                updateCart();
                renderCart();
            }
        }
        
        function renderCart() {
            const cartItems = document.getElementById('cartItems');
            const cartItemsCount = document.getElementById('cartItemsCount');
            
            if (cart.length === 0) {
                cartItems.innerHTML = `
                    <div class="empty-state">
                        <i>🛒</i>
                        <h3>Keranjang Kosong</h3>
                        <p>Silakan tambahkan produk ke keranjang</p>
                    </div>
                `;
                cartItemsCount.textContent = '0 Item';
                document.getElementById('cartSubtotal').textContent = 'Rp 0';
                document.getElementById('cartTax').textContent = 'Rp 0';
                document.getElementById('cartTotal').textContent = 'Rp 0';
                return;
            }
            
            const totalItems = cart.reduce((sum, item) => sum + item.quantity, 0);
            const subtotal = cart.reduce((sum, item) => sum + (item.price * item.quantity), 0);
            const tax = subtotal * 0.1; // 10% tax
            const total = subtotal + tax;
            
            cartItemsCount.textContent = `${totalItems} Item`;
            document.getElementById('cartSubtotal').textContent = formatCurrency(subtotal);
            document.getElementById('cartTax').textContent = formatCurrency(tax);
            document.getElementById('cartTotal').textContent = formatCurrency(total);
            
            cartItems.innerHTML = `
                ${cart.map(item => `
                    <div class="cart-item">
                        <div class="cart-item-info">
                            <div class="cart-item-name">${item.name}</div>
                            <div class="cart-item-desc">${item.description}</div>
                        </div>
                        <div class="cart-item-price">${formatCurrency(item.price)}</div>
                        <div class="cart-item-quantity">
                            <div class="quantity-btn" onclick="updateQuantity('${item.productId}', ${item.quantity - 1})">-</div>
                            <div class="quantity-value">${item.quantity}</div>
                            <div class="quantity-btn" onclick="updateQuantity('${item.productId}', ${item.quantity + 1})">+</div>
                        </div>
                        <div class="cart-item-price">${formatCurrency(item.price * item.quantity)}</div>
                        <button class="btn btn-danger" onclick="removeFromCart('${item.productId}')">Hapus</button>
                    </div>
                `).join('')}
            `;
        }
        
        function updateQuantity(productId, newQuantity) {
            if (newQuantity < 1) {
                removeFromCart(productId);
                return;
            }
            
            const item = cart.find(item => item.productId === productId);
            if (item) {
                item.quantity = newQuantity;
                updateCart();
                renderCart();
            }
        }
        
        function renderCheckout() {
            const checkoutItems = document.getElementById('checkoutItems');
            const checkoutSubtotal = document.getElementById('checkoutSubtotal');
            const checkoutTax = document.getElementById('checkoutTax');
            const checkoutTotal = document.getElementById('checkoutTotal');
            const checkoutTotalRight = document.getElementById('checkoutTotalRight');
            
            if (cart.length === 0) {
                navigateToPage('cart');
                return;
            }
            
            const subtotal = cart.reduce((sum, item) => sum + (item.price * item.quantity), 0);
            const tax = subtotal * 0.1; // 10% tax
            const total = subtotal + tax;
            
            checkoutSubtotal.textContent = formatCurrency(subtotal);
            checkoutTax.textContent = formatCurrency(tax);
            checkoutTotal.textContent = formatCurrency(total);
            checkoutTotalRight.textContent = formatCurrency(total);
            
            checkoutItems.innerHTML = `
                ${cart.map(item => `
                    <div class="transaction-item">
                        <div>${item.name} (${item.quantity}x)</div>
                        <div>${formatCurrency(item.price * item.quantity)}</div>
                    </div>
                `).join('')}
            `;
            
            // Reset payment
            paymentAmount.value = '';
            customAmount.value = '';
            document.getElementById('checkoutPaid').textContent = 'Rp 0';
            document.getElementById('checkoutChange').textContent = 'Rp 0';
        }
        
        function addToPaymentAmount(amount) {
            const currentAmount = parseInt(paymentAmount.value.replace(/\D/g, '')) || 0;
            const newAmount = currentAmount + amount;
            paymentAmount.value = formatCurrency(newAmount);
            calculateChange();
        }
        
        function calculateChange() {
            const paid = parseInt(paymentAmount.value.replace(/\D/g, '')) || 0;
            const total = getCartTotal();
            const change = paid - total;
            
            document.getElementById('checkoutPaid').textContent = formatCurrency(paid);
            document.getElementById('checkoutChange').textContent = formatCurrency(change < 0 ? 0 : change);
            
            // Enable/disable payment button
            processPaymentBtn.disabled = paid < total;
        }
        
        function getCartTotal() {
            const subtotal = cart.reduce((sum, item) => sum + (item.price * item.quantity), 0);
            const tax = subtotal * 0.1; // 10% tax
            return subtotal + tax;
        }
        
        function processPayment() {
            const total = getCartTotal();
            let paid = 0;
            
            if (currentPaymentMethod === 'cash') {
                paid = parseInt(paymentAmount.value.replace(/\D/g, '')) || 0;
                if (paid < total) {
                    alert('Jumlah pembayaran kurang dari total belanja!');
                    return;
                }
            } else {
                paid = total; // For QRIS and Debit, paid amount equals total
            }
            
            // Simpan data transaksi sementara
            sessionStorage.setItem('currentTransaction', JSON.stringify({
                items: cart,
                total: total,
                paid: paid,
                change: currentPaymentMethod === 'cash' ? paid - total : 0,
                paymentMethod: currentPaymentMethod,
                date: new Date().toISOString()
            }));
            
            navigateToPage('confirmation');
        }
        
        function renderConfirmation() {
            const transactionData = JSON.parse(sessionStorage.getItem('currentTransaction'));
            if (!transactionData) {
                navigateToPage('cart');
                return;
            }
            
            const confirmationItems = document.getElementById('confirmationItems');
            const confirmationTotal = document.getElementById('confirmationTotal');
            const confirmationPaid = document.getElementById('confirmationPaid');
            const confirmationChange = document.getElementById('confirmationChange');
            const transactionDate = document.getElementById('transactionDate');
            const transactionId = document.getElementById('transactionId');
            
            // Format date
            const date = new Date(transactionData.date);
            const formattedDate = `${date.getDate()} ${getMonthName(date.getMonth())} ${date.getFullYear()} • ${formatTime(date)}`;
            transactionDate.textContent = formattedDate;
            
            // Generate transaction ID
            const nextId = transactions.length + 1;
            transactionId.textContent = `#${nextId}`;
            
            confirmationTotal.textContent = formatCurrency(transactionData.total);
            confirmationPaid.textContent = formatCurrency(transactionData.paid) + ` (${transactionData.paymentMethod.toUpperCase()})`;
            confirmationChange.textContent = formatCurrency(transactionData.change);
            
            confirmationItems.innerHTML = `
                ${transactionData.items.map(item => `
                    <div class="transaction-item">
                        <div>
                            <div class="cart-item-name">${item.name}</div>
                            <div class="cart-item-desc">${item.description}</div>
                        </div>
                        <div>${formatCurrency(item.price * item.quantity)}</div>
                    </div>
                `).join('')}
            `;
        }
        
        function saveTransaction() {
            const transactionData = JSON.parse(sessionStorage.getItem('currentTransaction'));
            if (!transactionData) return;
            
            // Generate transaction ID
            const nextId = transactions.length + 1;
            
            // Create transaction object
            const transaction = {
                id: nextId.toString(),
                date: transactionData.date,
                items: transactionData.items,
                total: transactionData.total,
                paid: transactionData.paid,
                change: transactionData.change,
                paymentMethod: transactionData.paymentMethod
            };
            
            // Add to transactions
            transactions.push(transaction);
            localStorage.setItem('kaspos_transactions', JSON.stringify(transactions));
            
            // Clear cart and current transaction
            cart = [];
            updateCart();
            sessionStorage.removeItem('currentTransaction');
            
            // Show success message
            alert('Transaksi berhasil disimpan!');
            
            // Navigate to transaction detail
            sessionStorage.setItem('lastTransactionId', transaction.id);
            navigateToPage('transaction-detail');
        }
        
        function renderTransactionDetail() {
            const transactionId = sessionStorage.getItem('lastTransactionId');
            if (!transactionId) {
                navigateToPage('transactions');
                return;
            }
            
            const transaction = transactions.find(t => t.id === transactionId);
            if (!transaction) {
                navigateToPage('transactions');
                return;
            }
            
            const detailItems = document.getElementById('detailItems');
            const detailTotal = document.getElementById('detailTotal');
            const detailPaid = document.getElementById('detailPaid');
            const detailChange = document.getElementById('detailChange');
            const detailDate = document.getElementById('detailDate');
            const detailId = document.getElementById('detailId');
            
            // Format date
            const date = new Date(transaction.date);
            const formattedDate = `${date.getDate()} ${getMonthName(date.getMonth())} ${date.getFullYear()} ${formatTime(date)}`;
            detailDate.textContent = formattedDate;
            
            detailId.textContent = `#${transaction.id}`;
            detailTotal.textContent = formatCurrency(transaction.total);
            detailPaid.textContent = formatCurrency(transaction.paid) + ` (${transaction.paymentMethod.toUpperCase()})`;
            detailChange.textContent = formatCurrency(transaction.change);
            
            detailItems.innerHTML = `
                ${transaction.items.map(item => `
                    <div class="transaction-item">
                        <div>
                            <div class="cart-item-name">${item.name}</div>
                            <div class="cart-item-desc">${item.description}</div>
                        </div>
                        <div>${formatCurrency(item.price * item.quantity)}</div>
                    </div>
                `).join('')}
            `;
        }
        
        // Utility functions
        function formatCurrency(amount) {
            return 'Rp ' + amount.toLocaleString('id-ID');
        }
        
        function formatTime(date) {
            return date.toLocaleTimeString('id-ID', { hour: '2-digit', minute: '2-digit' });
        }
        
        function getMonthName(monthIndex) {
            const months = ['Jan', 'Feb', 'Mar', 'Apr', 'Mei', 'Jun', 'Jul', 'Agu', 'Sep', 'Okt', 'Nov', 'Des'];
            return months[monthIndex];
        }
        
        function showNotification(message) {
            // Create notification element
            const notification = document.createElement('div');
            notification.style.cssText = `
                position: fixed;
                top: 20px;
                right: 20px;
                background-color: #2ecc71;
                color: white;
                padding: 12px 20px;
                border-radius: 4px;
                box-shadow: 0 2px 10px rgba(0,0,0,0.2);
                z-index: 3000;
                transition: opacity 0.3s;
            `;
            notification.textContent = message;
            
            document.body.appendChild(notification);
            
            // Remove notification after 3 seconds
            setTimeout(() => {
                notification.style.opacity = '0';
                setTimeout(() => {
                    document.body.removeChild(notification);
                }, 300);
            }, 3000);
        }
        
        function updateTrialNotice() {
            const trialNotice = document.querySelector('.trial-notice');
            if (products.length >= 10) {
                trialNotice.innerHTML = `
                    <strong>Batas tercapai!</strong> Anda telah mencapai batas maksimal 10 produk pada versi trial.
                `;
                trialNotice.style.backgroundColor = '#ffebee';
                trialNotice.style.borderLeftColor = '#f44336';
            } else {
                trialNotice.innerHTML = `
                    Saat ini anda sedang memakai versi trial, anda dapat menambahkan hingga maksimal 10 produk.
                    <strong>(${products.length}/10 produk digunakan)</strong>
                `;
                trialNotice.style.backgroundColor = '#fff8e1';
                trialNotice.style.borderLeftColor = '#ffc107';
            }
        }
        
        // Fungsi untuk mengedit produk
        function editProduct(id) {
            const product = products.find(p => p.id === id);
            if (!product) return;
            
            // Set form values
            document.getElementById('productTitle').value = product.title;
            document.getElementById('productDescription').value = product.description;
            document.getElementById('productCategory').value = product.category;
            document.getElementById('productBarcode').value = product.barcode;
            document.getElementById('productPrice').value = product.price;
            document.getElementById('productCost').value = product.cost;
            document.getElementById('useImage').checked = product.useImage;
            document.getElementById('useShapeColor').checked = product.useShapeColor;
            
            if (product.image) {
                document.getElementById('previewImage').src = product.image;
                document.getElementById('imagePreview').style.display = 'block';
                document.getElementById('imageUploadArea').style.display = 'none';
            }
            
            // Set editing state
            editingProductId = id;
            
            // Navigate to add product page
            navigateToPage('add-product');
        }
        
        // Fungsi untuk menghapus produk
        function deleteProduct(id) {
            if (confirm('Apakah Anda yakin ingin menghapus produk ini?')) {
                products = products.filter(p => p.id !== id);
                localStorage.setItem('kaspos_products', JSON.stringify(products));
                renderProducts();
            }
        }
        
        // Fungsi untuk mengedit kategori
        function editCategory(id) {
            const category = categories.find(c => c.id === id);
            if (!category) return;
            
            // Set form values
            categoryName.value = category.name;
            
            // Show modal
            categoryModal.style.display = 'flex';
            
            // TODO: Implement update logic instead of create
        }
        
        // Fungsi untuk menghapus kategori
        function deleteCategory(id) {
            if (confirm('Apakah Anda yakin ingin menghapus kategori ini?')) {
                categories = categories.filter(c => c.id !== id);
                localStorage.setItem('kaspos_categories', JSON.stringify(categories));
                loadCategories();
                renderCategories();
            }
        }
        
        // Inisialisasi aplikasi saat halaman dimuat
        document.addEventListener('DOMContentLoaded', initApp);
        
        // Tambahkan event listener untuk tombol simulasi scan (untuk testing)
        document.addEventListener('DOMContentLoaded', function() {
            // Tambahkan tombol simulasi scan jika belum ada
            if (!document.getElementById('simulateScanBtn')) {
                const simulateBtn = document.createElement('button');
                simulateBtn.id = 'simulateScanBtn';
                simulateBtn.className = 'btn-scan btn-warning';
                simulateBtn.textContent = 'Simulasikan Scan';
                simulateBtn.style.marginTop = '10px';
                simulateBtn.addEventListener('click', simulateBarcodeScan);
                
                document.querySelector('.scanner-controls').appendChild(simulateBtn);
            }
        });
    </script>
</body>
</html>
