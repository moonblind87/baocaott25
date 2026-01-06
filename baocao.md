<!doctype html>
<html lang="vi" class="h-full">
 <head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Hệ Thống Quản Lý Báo Cáo Y Tế</title>
  <script src="https://cdn.tailwindcss.com"></script>
  
  <!-- Firebase SDKs -->
  <script src="https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js"></script>
  <script src="https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js"></script>
  <script src="https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js"></script>

  <style>
    body {
      box-sizing: border-box;
    }
    
    * {
      font-family: Tahoma, Geneva, sans-serif !important;
    }
    
    .fade-in {
      animation: fadeIn 0.3s ease-in;
    }
    
    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(10px); }
      to { opacity: 1; transform: translateY(0); }
    }
    
    .input-field {
      transition: all 0.2s;
    }
    
    .input-field:focus {
      transform: translateY(-1px);
      box-shadow: 0 4px 12px rgba(99, 102, 241, 0.15);
    }
    
    .btn-primary {
      transition: all 0.2s;
    }
    
    .btn-primary:hover {
      transform: translateY(-2px);
      box-shadow: 0 6px 20px rgba(99, 102, 241, 0.3);
    }
    
    .report-card {
      transition: all 0.2s;
    }
    
    .report-card:hover {
      transform: translateY(-2px);
      box-shadow: 0 8px 24px rgba(0, 0, 0, 0.1);
    }
    
    .loading-spinner {
      border: 3px solid #f3f4f6;
      border-top: 3px solid #6366f1;
      border-radius: 50%;
      width: 24px;
      height: 24px;
      animation: spin 1s linear infinite;
    }
    
    @keyframes spin {
      0% { transform: rotate(0deg); }
      100% { transform: rotate(360deg); }
    }

    .table-container {
      overflow-x: auto;
      max-width: 100%;
      margin: 0 auto;
    }
    
    .data-table {
      min-width: 100%;
      border-collapse: collapse;
    }
    
    .data-table th,
    .data-table td {
      padding: 8px 10px;
      border: 2px solid #9ca3af;
      text-align: center;
      font-size: 14px;
    }
    
    .data-table th {
      background-color: #e5e7eb;
      font-weight: 700;
      position: sticky;
      top: 0;
      z-index: 10;
      text-align: center;
      font-size: 13px;
      line-height: 1.4;
      border: 2px solid #374151;
    }
    
    .data-table input {
      width: 100%;
      min-width: 80px;
      padding: 6px;
      border: 1px solid #d1d5db;
      border-radius: 4px;
      font-size: 14px;
    }
    
    .data-table input:focus {
      outline: none;
      border-color: #6366f1;
      box-shadow: 0 0 0 3px rgba(99, 102, 241, 0.1);
    }
    
    .section-header {
      background-color: #dbeafe;
      font-weight: 700;
      color: #1e3a8a;
      border: 2px solid #374151;
    }
    
    .indent-1 {
      padding-left: 32px;
    }
    
    .form-subtitle {
      color: #6b7280;
      font-size: 0.875rem;
      font-style: italic;
      margin-top: 4px;
    }

    .success-message {
      background: linear-gradient(135deg, #10b981 0%, #059669 100%);
      color: white;
      padding: 24px;
      border-radius: 12px;
      text-align: center;
      box-shadow: 0 10px 30px rgba(16, 185, 129, 0.3);
    }

    .toast {
      position: fixed;
      bottom: 24px;
      right: 24px;
      padding: 16px 24px;
      border-radius: 8px;
      color: white;
      font-weight: 500;
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
      z-index: 1000;
      animation: slideIn 0.3s ease-out;
    }

    @keyframes slideIn {
      from {
        transform: translateX(400px);
        opacity: 0;
      }
      to {
        transform: translateX(0);
        opacity: 1;
      }
    }

    .toast.success {
      background: linear-gradient(135deg, #10b981 0%, #059669 100%);
    }

    .toast.error {
      background: linear-gradient(135deg, #ef4444 0%, #dc2626 100%);
    }
    
    /* Responsive adjustments */
    @media (max-width: 640px) {
      .data-table th, .data-table td {
        padding: 4px 6px;
        font-size: 12px;
      }
      
      .table-container {
        border-radius: 4px;
        border: 1px solid #e5e7eb;
      }
    }
  </style>
 </head>
 <body class="h-full w-full bg-gradient-to-br from-blue-50 via-white to-indigo-50">
  <div class="h-full w-full overflow-auto">
   <div class="min-h-full w-full"><!-- Header -->
    <header class="bg-white shadow-sm border-b-4 border-indigo-500">
     <!-- Changed max-w-7xl to w-full and added px-4 for full width -->
     <div class="w-full mx-auto px-4 sm:px-6 lg:px-8 py-6">
      <div class="flex flex-col sm:flex-row justify-between items-center gap-4">
       <div>
        <h1 id="system-title" class="text-2xl sm:text-3xl font-bold text-gray-900 text-center sm:text-left">Hệ Thống Quản Lý Báo Cáo Y Tế</h1>
        <p id="welcome-message" class="text-sm text-gray-600 mt-2 text-center sm:text-left">Vui lòng điền đầy đủ thông tin báo cáo</p>
       </div><button id="view-summary-btn" class="px-6 py-2 bg-purple-600 text-white font-medium rounded-lg hover:bg-purple-700 transition-colors flex items-center gap-2">
        <svg class="w-5 h-5" fill="none" stroke="currentColor" viewbox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z"></path>
        </svg> Tổng hợp </button>
      </div>
     </div>
    </header><!-- Main Content -->
    <!-- Changed max-w-7xl to w-full for full width display -->
    <main class="w-full mx-auto px-4 sm:px-6 lg:px-8 py-8"><!-- Step 1: General Information -->
     <div id="step-general" class="fade-in">
      <div class="bg-white rounded-xl shadow-md p-6 mb-6">
       <h2 class="text-xl font-semibold text-gray-800 mb-4 flex items-center gap-2"><span class="flex items-center justify-center w-8 h-8 bg-indigo-100 text-indigo-600 rounded-full text-sm font-bold">1</span> Thông tin chung</h2>
       <div class="space-y-4">
        <div><label for="organization-name" class="block text-sm font-medium text-gray-700 mb-1">Tên cơ quan, đơn vị <span class="text-red-500">*</span></label> <input type="text" id="organization-name" class="input-field w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-transparent" placeholder="Nhập tên cơ quan, đơn vị">
        </div>
        <div><label for="position" class="block text-sm font-medium text-gray-700 mb-1">Chức vụ người báo cáo <span class="text-red-500">*</span></label> <input type="text" id="position" class="input-field w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-transparent" placeholder="Nhập chức vụ">
        </div>
        <div><label for="phone" class="block text-sm font-medium text-gray-700 mb-1">Số điện thoại <span class="text-red-500">*</span></label> <input type="tel" id="phone" class="input-field w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-transparent" placeholder="Nhập số điện thoại">
        </div>
       </div><button id="next-to-type-btn" class="btn-primary mt-6 w-full sm:w-auto px-6 py-3 bg-indigo-600 text-white font-medium rounded-lg hover:bg-indigo-700"> Tiếp tục → </button>
      </div>
     </div><!-- Step 2: Select Report Type -->
     <div id="step-type" class="hidden fade-in">
      <div class="bg-white rounded-xl shadow-md p-6 mb-6">
       <h2 class="text-xl font-semibold text-gray-800 mb-4 flex items-center gap-2"><span class="flex items-center justify-center w-8 h-8 bg-indigo-100 text-indigo-600 rounded-full text-sm font-bold">2</span> Chọn đối tượng báo cáo</h2>
       <div class="grid grid-cols-1 md:grid-cols-2 gap-4"><button class="report-type-btn report-card p-6 border-2 border-gray-200 rounded-xl text-left hover:border-indigo-500 hover:bg-indigo-50" data-type="medical">
          <div class="flex items-start gap-4">
           <div class="flex-shrink-0 w-12 h-12 bg-indigo-100 rounded-lg flex items-center justify-center">
            <svg class="w-6 h-6 text-indigo-600" fill="none" stroke="currentColor" viewbox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 21V5a2 2 0 00-2-2H7a2 2 0 00-2 2v16m14 0h2m-2 0h-5m-9 0H3m2 0h5M9 7h1m-1 4h1m4-4h1m-1 4h1m-5 10v-5a1 1 0 011-1h2a1 1 0 011 1v5m-4 0h4"></path>
            </svg>
           </div>
           <div>
            <h3 class="font-semibold text-gray-900 mb-1">Cơ sở khám bệnh, chữa bệnh BHYT</h3>
            <p class="text-sm text-gray-600">3 biểu báo cáo về nhân lực, thuốc sử dụng</p>
           </div>
          </div></button> <button class="report-type-btn report-card p-6 border-2 border-gray-200 rounded-xl text-left hover:border-green-500 hover:bg-green-50" data-type="culture">
          <div class="flex items-start gap-4">
           <div class="flex-shrink-0 w-12 h-12 bg-green-100 rounded-lg flex items-center justify-center">
            <svg class="w-6 h-6 text-green-600" fill="none" stroke="currentColor" viewbox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 20h5v-2a3 3 0 00-5.356-1.857M17 20H7m10 0v-2c0-.656-.126-1.283-.356-1.857M7 20H2v-2a3 3 0 015.356-1.857M7 20v-2c0-.656.126-1.283.356-1.857m0 0a5.002 5.002 0 019.288 0M15 7a3 3 0 11-6 0 3 3 0 016 0zm6 3a2 2 0 11-4 0 2 2 0 014 0zM7 10a2 2 0 11-4 0 2 2 0 014 0z"></path>
            </svg>
           </div>
           <div>
            <h3 class="font-semibold text-gray-900 mb-1">Phòng Văn hoá - xã hội</h3>
            <p class="text-sm text-gray-600">1 biểu về hệ thống cung ứng và nhân lực</p>
           </div>
          </div></button> <button class="report-type-btn report-card p-6 border-2 border-gray-200 rounded-xl text-left hover:border-purple-500 hover:bg-purple-50" data-type="control">
          <div class="flex items-start gap-4">
           <div class="flex-shrink-0 w-12 h-12 bg-purple-100 rounded-lg flex items-center justify-center">
            <svg class="w-6 h-6 text-purple-600" fill="none" stroke="currentColor" viewbox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2m-6 9l2 2 4-4"></path>
            </svg>
           </div>
           <div>
            <h3 class="font-semibold text-gray-900 mb-1">Trung tâm Kiểm soát dược phẩm, mỹ phẩm, thực phẩm và thiết bị y tế</h3>
            <p class="text-sm text-gray-600">1 biểu về chất lượng thuốc, nguyên liệu</p>
           </div>
          </div></button> <button class="report-type-btn report-card p-6 border-2 border-gray-200 rounded-xl text-left hover:border-orange-500 hover:bg-orange-50" data-type="cosmetics">
          <div class="flex items-start gap-4">
           <div class="flex-shrink-0 w-12 h-12 bg-orange-100 rounded-lg flex items-center justify-center">
            <svg class="w-6 h-6 text-orange-600" fill="none" stroke="currentColor" viewbox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M20 7l-8-4-8 4m16 0l-8 4m8-4v10l-8 4m0-10L4 7m8 4v10M4 7v10l8 4"></path>
            </svg>
           </div>
           <div>
            <h3 class="font-semibold text-gray-900 mb-1">Cơ sở sản xuất, nhập khẩu mỹ phẩm</h3>
            <p class="text-sm text-gray-600">1 biểu về giá trị sản xuất, nhập khẩu</p>
           </div>
          </div></button>
       </div><button id="back-to-general-btn" class="mt-6 px-6 py-2 border border-gray-300 text-gray-700 rounded-lg hover:bg-gray-50"> ← Quay lại </button>
      </div>
     </div><!-- Step 3: Fill Forms -->
     <div id="step-forms" class="hidden fade-in">
      <div class="bg-white rounded-xl shadow-md p-6 mb-6"><!-- Progress Indicator -->
       <div class="mb-6">
        <div class="flex items-center justify-between mb-2">
         <h2 class="text-xl font-semibold text-gray-800 flex items-center gap-2"><span class="flex items-center justify-center w-8 h-8 bg-indigo-100 text-indigo-600 rounded-full text-sm font-bold">3</span> Nhập dữ liệu báo cáo</h2>
         <div id="form-progress" class="text-sm text-gray-600 font-medium">
          Biểu <span id="current-form-index">1</span> / <span id="total-forms">1</span>
         </div>
        </div>
        <div class="w-full bg-gray-200 rounded-full h-2">
         <div id="progress-bar" class="bg-indigo-600 h-2 rounded-full transition-all duration-300" style="width: 0%"></div>
        </div>
       </div>
       <div id="forms-container" class="space-y-6"><!-- Forms will be dynamically inserted here -->
       </div><!-- Navigation Buttons -->
       <div class="flex flex-col sm:flex-row justify-between items-center gap-3 mt-6">
        <div class="flex gap-3 w-full sm:w-auto"><button id="back-to-type-btn" class="flex-1 sm:flex-none px-6 py-2 border border-gray-300 text-gray-700 rounded-lg hover:bg-gray-50"> ← Quay lại </button> <button id="prev-form-btn" class="flex-1 sm:flex-none hidden px-6 py-2 border border-indigo-300 text-indigo-700 rounded-lg hover:bg-indigo-50"> ← Biểu trước </button>
        </div>
        <div class="flex gap-3 w-full sm:w-auto"><button id="next-form-btn" class="flex-1 sm:flex-none hidden btn-primary px-6 py-2 bg-indigo-600 text-white rounded-lg hover:bg-indigo-700"> Biểu tiếp → </button> <button id="submit-report-btn" class="flex-1 sm:flex-none hidden btn-primary px-6 py-3 bg-green-600 text-white font-medium rounded-lg hover:bg-green-700 flex items-center justify-center gap-2"> <span id="submit-btn-text">Nộp báo cáo</span>
          <div id="submit-spinner" class="loading-spinner hidden"></div></button>
        </div>
       </div>
      </div>
     </div><!-- Success Message -->
     <div id="success-message" class="hidden fade-in">
      <div class="success-message">
       <svg class="w-16 h-16 mx-auto mb-4" fill="none" stroke="currentColor" viewbox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"></path>
       </svg>
       <h2 class="text-2xl font-bold mb-2">Nộp báo cáo thành công!</h2>
       <p class="text-lg mb-6">Dữ liệu của bạn đã được lưu trữ an toàn trong hệ thống</p><button id="submit-another-btn" class="px-8 py-3 bg-white text-green-600 font-semibold rounded-lg hover:bg-gray-100 transition-colors"> Nộp báo cáo mới </button>
      </div>
     </div><!-- Password Modal -->
     <div id="password-modal" class="hidden fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
      <div class="bg-white rounded-xl shadow-2xl p-8 max-w-md w-full mx-4 fade-in">
       <h2 class="text-2xl font-bold text-gray-900 mb-4 flex items-center gap-2">
        <svg class="w-6 h-6 text-purple-600" fill="none" stroke="currentColor" viewbox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z"></path>
        </svg> Xác thực truy cập</h2>
       <p class="text-gray-600 mb-6">Vui lòng nhập mật khẩu để xem dữ liệu tổng hợp</p>
       <div class="mb-6"><label for="password-input" class="block text-sm font-medium text-gray-700 mb-2">Mật khẩu</label> <input type="password" id="password-input" class="input-field w-full px-4 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-purple-500 focus:border-transparent" placeholder="Nhập mật khẩu">
        <p id="password-error" class="hidden text-red-600 text-sm mt-2">❌ Mật khẩu không đúng. Vui lòng thử lại.</p>
       </div>
       <div class="flex gap-3"><button id="cancel-password-btn" class="flex-1 px-6 py-3 border border-gray-300 text-gray-700 rounded-lg hover:bg-gray-50 transition-colors"> Hủy </button> <button id="submit-password-btn" class="flex-1 px-6 py-3 bg-purple-600 text-white font-medium rounded-lg hover:bg-purple-700 transition-colors"> Xác nhận </button>
       </div>
      </div>
     </div><!-- Summary View -->
     <div id="summary-view" class="hidden fade-in">
      <div class="bg-white rounded-xl shadow-md p-6 mb-6">
       <div class="flex justify-between items-center mb-6">
        <h2 class="text-2xl font-bold text-gray-900 flex items-center gap-2">
         <svg class="w-8 h-8 text-purple-600" fill="none" stroke="currentColor" viewbox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z"></path>
         </svg> Dữ liệu tổng hợp</h2><button id="close-summary-btn" class="px-6 py-2 border border-gray-300 text-gray-700 rounded-lg hover:bg-gray-50 transition-colors"> ← Quay lại </button>
       </div>
       <div id="summary-content" class="space-y-6"><!-- Summary content will be inserted here -->
       </div>
      </div>
     </div>
    </main>
   </div>
  </div>
  <script type="module">
    // Firebase Imports
    import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
    import { getAuth, signInAnonymously, onAuthStateChanged, signInWithCustomToken } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
    import { getFirestore, collection, addDoc, onSnapshot, query, orderBy } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

    // --- FIREBASE CONFIGURATION & INIT ---
    let app, auth, db;
    let appId = 'default-app-id';
    let currentUser = null;
    let allReports = [];

    try {
        if (typeof __firebase_config !== 'undefined' && __firebase_config) {
            const firebaseConfig = JSON.parse(__firebase_config);
            app = initializeApp(firebaseConfig);
            auth = getAuth(app);
            db = getFirestore(app);
        } else {
            console.warn("Firebase configuration is missing or invalid.");
        }
        
        if (typeof __app_id !== 'undefined' && __app_id) {
            appId = __app_id;
        }
    } catch (e) {
        console.error("Error initializing Firebase:", e);
    }

    // Authenticate
    const initAuth = async () => {
        if (!auth) return;
        try {
            if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
                await signInWithCustomToken(auth, __initial_auth_token);
            } else {
                await signInAnonymously(auth);
            }
        } catch(e) {
            console.error("Auth error:", e);
        }
    };

    // Listen to Auth State
    if (auth) {
        onAuthStateChanged(auth, (user) => {
            currentUser = user;
            if (user) {
                subscribeToReports();
            }
        });
    }

    // Subscribe to Firestore Data
    function subscribeToReports() {
        if (!db) return;
        // Ensure appId is valid string
        const safeAppId = String(appId || 'default-app-id');
        
        const q = query(collection(db, 'artifacts', safeAppId, 'public', 'data', 'reports'));
        
        onSnapshot(q, (snapshot) => {
            const reports = [];
            snapshot.forEach((doc) => {
                reports.push(doc.data());
            });
            
            reports.sort((a, b) => new Date(b.created_at) - new Date(a.created_at));
            
            allReports = reports;
            if (!document.getElementById('summary-view').classList.contains('hidden')) {
                generateSummaryContent();
            }
        }, (error) => {
            console.error("Error fetching reports:", error);
        });
    }
    
    initAuth();

    // --- APPLICATION LOGIC ---
    const defaultConfig = {
      system_title: "Báo cáo thống kê Dược - Mỹ phẩm Thông tư 25/2021/TT-BYT năm 2025",
      welcome_message: "Vui lòng điền đầy đủ thông tin báo cáo",
      primary_color: "#6366f1",
      secondary_color: "#10b981",
      accent_color: "#8b5cf6",
      text_color: "#1f2937"
    };

    let currentStep = 'general';
    let selectedType = '';
    let generalInfo = {};
    let currentFormIndex = 0;
    let totalFormsCount = 0;
    let allFormElements = [];
    const CORRECT_PASSWORD = 'abcdefgh';

    document.getElementById('system-title').textContent = defaultConfig.system_title;
    document.getElementById('welcome-message').textContent = defaultConfig.welcome_message;

    // Form templates (kept same as before)
    const formTemplates = {
      medical: [
        {
          name: 'Biểu 01: Tình hình nhân lực làm công tác dược lâm sàng',
          id: 'medical_form_1',
          isTable: true,
          columns: [
            { label: 'Trình độ chuyên môn', key: 'trinh_do' },
            { label: 'Tổng số', key: 'tong_so', editable: true },
            { label: 'Số lượng người chuyên trách làm công tác dược lâm sàng', key: 'chuyen_trach', editable: true },
            { label: 'Số lượng người kiêm nhiệm làm công tác dược lâm sàng', key: 'kiem_nhiem', editable: true },
            { label: 'Số lượng đã được cấp chứng chỉ hành nghề về dược lâm sàng', key: 'chung_chi', editable: true }
          ],
          rows: [
            { label: 'Tiến sĩ dược', key: 'tien_si_duoc' },
            { label: 'Thạc sĩ dược', key: 'thac_si_duoc' },
            { label: 'Dược sĩ chuyên khoa II', key: 'ds_ck2' },
            { label: 'Dược sĩ chuyên khoa I', key: 'ds_ck1' },
            { label: 'Dược sĩ đại học', key: 'ds_dai_hoc' },
            { label: 'Dược sĩ cao đẳng', key: 'ds_cao_dang' },
            { label: 'Dược sĩ trung học', key: 'ds_trung_hoc' },
            { label: 'Dược tá', key: 'duoc_ta' },
            { label: 'Cán bộ chuyên môn khác tại khoa dược', key: 'can_bo_khac' }
          ]
        },
        {
          name: 'Biểu 02: Giá trị thuốc đã sử dụng',
          id: 'medical_form_2',
          isTable: true,
          isHorizontal: true,
          columns: [
            { label: 'Tổng giá trị sử dụng thuốc', key: 'tong_gia_tri' },
            { label: 'Thuốc biệt dược gốc', key: 'biet_duoc_goc' },
            { label: 'Thuốc generic', key: 'generic' },
            { label: 'Thuốc dược liệu', key: 'duoc_lieu' },
            { label: 'Kháng sinh', key: 'khang_sinh' },
            { label: 'Vắc xin', key: 'vac_xin' },
            { label: 'Sinh phẩm', key: 'sinh_pham' },
            { label: 'Thuốc phóng xạ và hợp chất đánh dấu', key: 'phong_xa' },
            { label: 'Giá trị sử dụng thuốc bảo hiểm y tế', key: 'bhyt' },
            { label: 'Giá trị thuốc được viện trợ, viện trợ nhân đạo', key: 'vien_tro' }
          ],
          rows: [
            { label: 'Giá trị (VNĐ)', key: 'gia_tri' }
          ]
        },
        {
          name: 'Biểu 03: Tình hình sử dụng thuốc sản xuất trong nước',
          id: 'medical_form_3',
          isTable: true,
          isHorizontal: true,
          isGrouped: true,
          columnGroups: [
            {
              label: 'Tính theo số lượng mặt hàng',
              columns: [
                { label: 'Số lượng thuốc trúng thầu', key: 'so_luong_trung_thau' },
                { label: 'Số lượng thuốc sản xuất trong nước trúng thầu', key: 'so_luong_sx_trong_nuoc' },
                { label: 'Tỷ lệ mặt hàng thuốc sản xuất trong nước trúng thầu', key: 'ty_le_mat_hang' }
              ]
            },
            {
              label: 'Tính theo giá trị',
              columns: [
                { label: 'Tổng số tiền thuốc sử dụng tại cơ sở y tế', key: 'tong_tien_su_dung' },
                { label: 'Tổng số tiền thuốc sản xuất trong nước sử dụng tại cơ sở y tế', key: 'tong_tien_sx_trong_nuoc' },
                { label: 'Tỷ lệ giá trị thuốc sản xuất trong nước đã sử dụng tại cơ sở y tế', key: 'ty_le_gia_tri' }
              ]
            }
          ],
          rows: [
            { label: 'Giá trị', key: 'gia_tri' }
          ]
        }
      ],
      culture: [
        {
          name: 'HỆ THỐNG CUNG ỨNG THUỐC, SẢN XUẤT KINH DOANH MỸ PHẨM VÀ NHÂN LỰC DƯỢC',
          subtitle: 'Số lượng tính đến 31 tháng 12 năm 2025',
          id: 'culture_form_1',
          isTable: true,
          columns: [
            { label: 'Nội dung', key: 'noi_dung' },
            { label: 'Số lượng', key: 'so_luong' }
          ],
          rows: [
            { label: 'I. HỆ THỐNG CUNG ỨNG THUỐC', key: 'section_1', isHeader: true },
            { label: '1. Tổng số cơ sở bán lẻ', key: 'tong_co_so_ban_le' },
            { label: '1.1. Số nhà thuốc', key: 'so_nha_thuoc', indent: 1 },
            { label: '1.2. Số quầy thuốc', key: 'so_quay_thuoc', indent: 1 },
            { label: '1.3. Số tủ thuốc Trạm Y tế', key: 'so_tu_thuoc', indent: 1 },
            { label: '1.4. Số cơ sở bán lẻ thuốc trên mười nghìn dân', key: 'co_so_tren_10k_dan', indent: 1 },
            { label: 'II. NHÂN LỰC DƯỢC', key: 'section_2', isHeader: true },
            { label: '2. Tiến sĩ dược', key: 'tien_si_duoc' },
            { label: '3. Thạc sĩ dược', key: 'thac_si_duoc' },
            { label: '4. Dược sĩ chuyên khoa II', key: 'ds_ck2' },
            { label: '5. Dược sĩ chuyên khoa I', key: 'ds_ck1' },
            { label: '6. Dược sĩ đại học', key: 'ds_dai_hoc' },
            { label: '7. Dược sĩ cao đẳng', key: 'ds_cao_dang' },
            { label: '8. Dược sĩ trung học', key: 'ds_trung_hoc' },
            { label: '9. Dược tá', key: 'duoc_ta' }
          ]
        }
      ],
      control: [
        {
          name: 'Tình hình chất lượng thuốc, nguyên liệu làm thuốc lưu hành',
          id: 'control_form_1',
          isTable: true,
          isGrouped: true,
          columnGroups: [
            {
              label: '',
              columns: [
                { label: 'Chỉ tiêu', key: 'chi_tieu' },
                { label: 'Đơn vị tính', key: 'don_vi_tinh' },
                { label: 'Tổng số', key: 'tong_so' }
              ]
            },
            {
              label: 'Mẫu lấy là thuốc',
              columns: [
                { label: 'Tổng', key: 'thuoc_tong' },
                { label: 'Thuốc lấy tại cơ sở loại 1', key: 'thuoc_co_so_1' },
                { label: 'Thuốc lấy tại cơ sở loại 2', key: 'thuoc_co_so_2' },
                { label: 'Thuốc sản xuất trong nước', key: 'thuoc_sx_trong_nuoc' },
                { label: 'Thuốc nhập khẩu', key: 'thuoc_nhap_khau' },
                { label: 'Thuốc hóa dược, sinh phẩm', key: 'thuoc_hoa_duoc' },
                { label: 'Thuốc dược liệu', key: 'thuoc_duoc_lieu' }
              ]
            },
            {
              label: '',
              columns: [
                { label: 'Mẫu lấy là nguyên liệu làm thuốc', key: 'nguyen_lieu' }
              ]
            }
          ],
          rows: [
            { label: 'Số mẫu lấy kiểm tra chất lượng', key: 'so_mau_lay', unit: 'Mẫu' },
            { label: 'Số mẫu không đạt tiêu chuẩn chất lượng', key: 'so_mau_khong_dat', unit: 'Mẫu' },
            { label: 'Thuốc không đạt chất lượng vi phạm mức độ 1', key: 'vi_pham_md1', unit: 'Mẫu' },
            { label: 'Thuốc không đạt chất lượng vi phạm mức độ 2', key: 'vi_pham_md2', unit: 'Mẫu' },
            { label: 'Thuốc không đạt chất lượng vi phạm mức độ 3', key: 'vi_pham_md3', unit: 'Mẫu' },
            { label: 'Tỷ lệ mẫu thuốc không đạt chất lượng trên tổng số mẫu lấy để kiểm tra chất lượng', key: 'ty_le_khong_dat', unit: '%' },
            { label: 'Tỷ lệ thuốc không đạt chất lượng vi phạm mức độ 1', key: 'ty_le_md1', unit: '%' },
            { label: 'Thuốc không đạt chất lượng vi phạm mức độ 2', key: 'ty_le_md2', unit: '%' },
            { label: 'Thuốc không đạt chất lượng vi phạm mức độ 3', key: 'ty_le_md3', unit: '%' },
            { label: 'Số lô thuốc giả phát hiện được', key: 'so_lo_gia', unit: 'Lô' },
            { label: 'Tỷ lệ thuốc giả', key: 'ty_le_gia', unit: '%' },
            { label: 'Thuốc giả sản phẩm của các cơ sở sản xuất trong nước', key: 'gia_trong_nuoc', unit: '%' },
            { label: 'Thuốc giả sản phẩm của các cơ sở sản xuất nước ngoài', key: 'gia_nuoc_ngoai', unit: '%' },
            { label: 'Thuốc giả không chứa hoạt chất', key: 'gia_khong_hoat_chat', unit: '%' },
            { label: 'Thuốc giả bao bì nhãn mác', key: 'gia_bao_bi', unit: '%' }
          ]
        }
      ],
      cosmetics: [
        {
          name: 'Giá trị sản xuất, nhập khẩu mỹ phẩm',
          id: 'cosmetics_form_1',
          isTable: true,
          columns: [
            { label: 'Chỉ tiêu', key: 'chi_tieu' },
            { label: 'Giá trị (VNĐ)', key: 'gia_tri' }
          ],
          rows: [
            { label: 'Giá trị mỹ phẩm nhập khẩu', key: 'gia_tri_nhap_khau' },
            { label: 'Giá trị mỹ phẩm sản xuất trong nước', key: 'gia_tri_san_xuat' }
          ]
        }
      ]
    };

    // Helper functions (same as before)
    function showStep(step) {
      document.getElementById('step-general').classList.add('hidden');
      document.getElementById('step-type').classList.add('hidden');
      document.getElementById('step-forms').classList.add('hidden');
      document.getElementById('success-message').classList.add('hidden');
      
      if (step === 'general') {
        document.getElementById('step-general').classList.remove('hidden');
      } else if (step === 'type') {
        document.getElementById('step-type').classList.remove('hidden');
      } else if (step === 'forms') {
        document.getElementById('step-forms').classList.remove('hidden');
      } else if (step === 'success') {
        document.getElementById('success-message').classList.remove('hidden');
      }
      currentStep = step;
    }

    function validateGeneralInfo() {
      const orgName = document.getElementById('organization-name').value.trim();
      const position = document.getElementById('position').value.trim();
      const phone = document.getElementById('phone').value.trim();
      if (!orgName || !position || !phone) {
        showToast('Vui lòng điền đầy đủ thông tin!', 'error');
        return false;
      }
      generalInfo = { organization_name: orgName, position: position, phone: phone };
      return true;
    }

    function generateForms(type) {
      const container = document.getElementById('forms-container');
      container.innerHTML = '';
      allFormElements = [];
      currentFormIndex = 0;
      const forms = formTemplates[type];
      totalFormsCount = forms.length;
      
      forms.forEach((form, formIndex) => {
        const formDiv = document.createElement('div');
        formDiv.className = 'border border-gray-200 rounded-lg p-6 bg-gray-50';
        formDiv.dataset.formIndex = formIndex;
        if (formIndex > 0) formDiv.classList.add('hidden');
        
        let formHTML = `<h3 class="text-lg font-bold text-gray-800 mb-2">${form.name}</h3>`;
        if (form.subtitle) {
          formHTML += `<p class="form-subtitle mb-4">${form.subtitle}</p>`;
        } else {
          const isPersonnelForm = form.name.includes('nhân lực') || form.name.includes('NHÂN LỰC');
          formHTML += isPersonnelForm 
            ? `<p class="form-subtitle mb-4">Số có mặt đến 31 tháng 12 năm 2025</p>` 
            : `<p class="form-subtitle mb-4">Số liệu tính từ 01/01/2025 đến 31/12/2025</p>`;
        }
        
        if (form.isTable && form.rows) {
          formHTML += `<div class="table-container"><table class="data-table w-full bg-white"><thead>`;
          // Headers generation logic...
          if (form.isGrouped && form.columnGroups) {
            formHTML += `<tr>`;
            form.columnGroups.forEach(group => {
              if (group.label) formHTML += `<th colspan="${group.columns.length}" class="text-center border-r-2 border-gray-300">${group.label}</th>`;
              else group.columns.forEach(() => formHTML += `<th></th>`);
            });
            formHTML += `</tr><tr>`;
            form.columnGroups.forEach(group => group.columns.forEach(col => formHTML += `<th>${col.label}</th>`));
            formHTML += `</tr>`;
          } else if (form.columns) {
            formHTML += `<tr>`;
            form.columns.forEach((col, index) => {
              const alignClass = index === 0 ? 'text-left' : '';
              formHTML += `<th class="${alignClass}">${col.label}</th>`;
            });
            formHTML += `</tr>`;
          }
          formHTML += `</thead><tbody>`;
          
          form.rows.forEach(row => {
            const rowClass = row.isHeader ? 'section-header' : '';
            const indentClass = row.indent ? 'indent-1' : '';
            formHTML += `<tr class="${rowClass}">`;
            
            if (form.isGrouped && form.columnGroups) {
                form.columnGroups.forEach(group => {
                    group.columns.forEach(col => {
                        if (col.key === 'chi_tieu') {
                            formHTML += `<td class="font-medium">${row.label}</td>`;
                        } else if (col.key === 'don_vi_tinh') {
                            formHTML += `<td class="text-center text-gray-600">${row.unit || ''}</td>`;
                        } else {
                            const inputKey = `${row.key}_${col.key}`;
                            formHTML += `<td><input type="number" data-form="${form.id}" data-row-key="${row.key}" data-row-label="${row.label}" data-key="${inputKey}" class="form-input" placeholder="0"></td>`;
                        }
                    });
                });
            } else if (form.isHorizontal && form.columns) {
                form.columns.forEach(col => {
                    const inputKey = `${row.key}_${col.key}`;
                    formHTML += `<td><input type="number" data-form="${form.id}" data-row-key="${row.key}" data-row-label="${row.label}" data-col-key="${col.key}" data-col-label="${col.label}" data-key="${inputKey}" class="form-input" placeholder="0"></td>`;
                });
            } else if (form.columns) {
                form.columns.forEach((col, colIndex) => {
                    if (colIndex === 0) {
                        formHTML += `<td class="${indentClass} font-medium text-left">${row.label}</td>`;
                    } else if (!row.isHeader) {
                        const isEditable = col.editable !== false;
                        const inputKey = `${row.key}_${col.key}`;
                        const disabledAttr = !isEditable ? 'disabled' : '';
                        const disabledClass = !isEditable ? 'bg-gray-100 cursor-not-allowed' : '';
                        formHTML += `<td><input type="number" data-form="${form.id}" data-row-key="${row.key}" data-row-label="${row.label}" data-col-key="${col.key}" data-col-label="${col.label}" data-key="${inputKey}" class="form-input ${disabledClass}" placeholder="0" ${disabledAttr}></td>`;
                    } else {
                        formHTML += `<td></td>`;
                    }
                });
            }
            formHTML += `</tr>`;
          });
          formHTML += `</tbody></table></div>`;
        }
        formDiv.innerHTML = formHTML;
        container.appendChild(formDiv);
        allFormElements.push(formDiv);
      });
      updateFormNavigation();
    }

    function updateFormNavigation() {
      const prevBtn = document.getElementById('prev-form-btn');
      const nextBtn = document.getElementById('next-form-btn');
      const submitBtn = document.getElementById('submit-report-btn');
      const progressBar = document.getElementById('progress-bar');
      document.getElementById('current-form-index').textContent = currentFormIndex + 1;
      document.getElementById('total-forms').textContent = totalFormsCount;
      progressBar.style.width = ((currentFormIndex + 1) / totalFormsCount) * 100 + '%';
      
      prevBtn.classList.toggle('hidden', currentFormIndex === 0);
      if (currentFormIndex === totalFormsCount - 1) {
        nextBtn.classList.add('hidden');
        submitBtn.classList.remove('hidden');
      } else {
        nextBtn.classList.remove('hidden');
        submitBtn.classList.add('hidden');
      }
    }

    function showForm(index) {
      allFormElements.forEach((formEl, i) => formEl.classList.toggle('hidden', i !== index));
      currentFormIndex = index;
      updateFormNavigation();
      window.scrollTo({ top: 0, behavior: 'smooth' });
    }
    
    function nextForm() { if (currentFormIndex < totalFormsCount - 1) showForm(currentFormIndex + 1); }
    function prevForm() { if (currentFormIndex > 0) showForm(currentFormIndex - 1); }

    // Collect form data and submit
    async function submitReport() {
      if (!currentUser) {
        showToast('Vui lòng đợi kết nối hệ thống...', 'error');
        // Try re-init auth if lost
        initAuth();
        return;
      }

      const submitBtn = document.getElementById('submit-report-btn');
      const submitBtnText = document.getElementById('submit-btn-text');
      const submitSpinner = document.getElementById('submit-spinner');
      
      submitBtn.disabled = true;
      submitBtnText.textContent = 'Đang xử lý...';
      submitSpinner.classList.remove('hidden');
      
      const reportTypeNames = {
        medical: 'Cơ sở khám bệnh, chữa bệnh BHYT',
        culture: 'Phòng Văn hoá - xã hội',
        control: 'Trung tâm Kiểm soát dược phẩm, mỹ phẩm, thực phẩm và thiết bị y tế',
        cosmetics: 'Cơ sở sản xuất, nhập khẩu mỹ phẩm'
      };
      
      const reportType = reportTypeNames[selectedType];
      const baseReportId = 'RPT' + Date.now();
      const createdAt = new Date().toISOString();
      
      const inputs = document.querySelectorAll('.form-input');
      const formData = {};
      
      inputs.forEach(input => {
        const formId = input.dataset.form;
        const key = input.dataset.key;
        const value = input.value.trim() || '0';
        
        if (!formData[formId]) {
          formData[formId] = {
            formName: getFormName(formId),
            data: {}
          };
        }
        formData[formId].data[key] = value;
      });
      
      const allRecords = [];
      let recordIndex = 0;
      
      for (const formId in formData) {
        const form = formData[formId];
        const record = {
          report_id: `${baseReportId}_${recordIndex}`,
          organization_name: generalInfo.organization_name || '',
          position: generalInfo.position || '',
          phone: generalInfo.phone || '',
          report_type: reportType || '',
          form_name: form.formName || '',
          data_json: JSON.stringify(form.data || {}),
          created_at: createdAt
        };
        allRecords.push(record);
        recordIndex++;
      }
      
      let successCount = 0;
      let errorCount = 0;
      
      // Save to Firestore
      for (const record of allRecords) {
        try {
          if (db) {
            // Ensure appId is valid string
            const safeAppId = String(appId || 'default-app-id');
            await addDoc(collection(db, 'artifacts', safeAppId, 'public', 'data', 'reports'), record);
            successCount++;
          } else {
             console.error("Firestore DB instance not initialized");
             errorCount++;
          }
        } catch (e) {
          console.error("Firestore save error:", e);
          errorCount++;
        }
      }
      
      // Try Google Sheets (Non-blocking)
      try {
        const googleSheetUrl = 'https://script.google.com/macros/s/AKfycbxtXHPLJHjKu_wLFpFZPk8ndMHOFCwtE2-CP6Xl1UwjnzX551Dd50GFuoD6Xp47z_5niw/exec';
        fetch(googleSheetUrl, {
          method: 'POST',
          mode: 'no-cors',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({ data: allRecords })
        }).catch(err => console.log('Google Sheet error ignored:', err));
      } catch (error) {}
      
      submitBtn.disabled = false;
      submitBtnText.textContent = 'Nộp báo cáo';
      submitSpinner.classList.add('hidden');
      
      if (errorCount === 0) {
        showToast(`Đã lưu thành công ${successCount} biểu báo cáo!`, 'success');
        resetForm();
        showStep('success');
      } else {
        showToast(`Hoàn tất với ${errorCount} lỗi. Đã lưu ${successCount} biểu.`, 'error');
      }
    }

    function getFormName(formId) {
      for (const type in formTemplates) {
        const form = formTemplates[type].find(f => f.id === formId);
        if (form) return form.name;
      }
      return formId;
    }

    function resetForm() {
      document.getElementById('organization-name').value = '';
      document.getElementById('position').value = '';
      document.getElementById('phone').value = '';
      selectedType = '';
      generalInfo = {};
    }

    function showToast(message, type = 'info') {
      const toast = document.createElement('div');
      toast.className = `toast ${type}`;
      toast.textContent = message;
      document.body.appendChild(toast);
      setTimeout(() => {
        toast.style.opacity = '0';
        toast.style.transform = 'translateX(400px)';
        setTimeout(() => toast.remove(), 300);
      }, 4000);
    }

    function showPasswordModal() {
      document.getElementById('password-modal').classList.remove('hidden');
      document.getElementById('password-input').value = '';
      document.getElementById('password-error').classList.add('hidden');
      document.getElementById('password-input').focus();
    }

    function hidePasswordModal() {
      document.getElementById('password-modal').classList.add('hidden');
    }

    function verifyPasswordAndShowSummary() {
      const passwordInput = document.getElementById('password-input');
      const passwordError = document.getElementById('password-error');
      if (passwordInput.value === CORRECT_PASSWORD) {
        hidePasswordModal();
        showSummaryView();
      } else {
        passwordError.classList.remove('hidden');
        passwordInput.value = '';
        passwordInput.focus();
      }
    }

    function showSummaryView() {
      document.getElementById('step-general').classList.add('hidden');
      document.getElementById('step-type').classList.add('hidden');
      document.getElementById('step-forms').classList.add('hidden');
      document.getElementById('success-message').classList.add('hidden');
      document.getElementById('summary-view').classList.remove('hidden');
      generateSummaryContent();
    }

    function hideSummaryView() {
      document.getElementById('summary-view').classList.add('hidden');
      showStep('general');
    }

    function generateSummaryContent() {
      const container = document.getElementById('summary-content');
      if (allReports.length === 0) {
        container.innerHTML = `
          <div class="text-center py-12">
            <svg class="w-16 h-16 mx-auto text-gray-400 mb-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z"></path></svg>
            <h3 class="text-xl font-semibold text-gray-700 mb-2">Chưa có dữ liệu báo cáo</h3>
            <p class="text-gray-600">Hệ thống chưa có báo cáo nào được nộp</p>
          </div>
        `;
        return;
      }

      const reportsByType = {};
      allReports.forEach(report => {
        const type = report.report_type;
        if (!reportsByType[type]) reportsByType[type] = [];
        reportsByType[type].push(report);
      });

      let summaryHTML = `
        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4 mb-6">
          <div class="bg-gradient-to-br from-blue-500 to-blue-600 text-white rounded-lg p-6">
            <div class="flex items-center justify-between mb-2"><h3 class="text-sm font-medium opacity-90">Tổng báo cáo</h3></div>
            <p class="text-3xl font-bold">${allReports.length}</p>
          </div>
          <div class="bg-gradient-to-br from-green-500 to-green-600 text-white rounded-lg p-6">
            <div class="flex items-center justify-between mb-2"><h3 class="text-sm font-medium opacity-90">Loại báo cáo</h3></div>
            <p class="text-3xl font-bold">${Object.keys(reportsByType).length}</p>
          </div>
          <div class="bg-gradient-to-br from-purple-500 to-purple-600 text-white rounded-lg p-6">
            <div class="flex items-center justify-between mb-2"><h3 class="text-sm font-medium opacity-90">Cơ quan</h3></div>
            <p class="text-3xl font-bold">${new Set(allReports.map(r => r.organization_name)).size}</p>
          </div>
          <div class="bg-gradient-to-br from-orange-500 to-orange-600 text-white rounded-lg p-6">
            <div class="flex items-center justify-between mb-2"><h3 class="text-sm font-medium opacity-90">Biểu mẫu</h3></div>
            <p class="text-3xl font-bold">${new Set(allReports.map(r => r.form_name)).size}</p>
          </div>
        </div>
      `;

      for (const [reportType, reports] of Object.entries(reportsByType)) {
        summaryHTML += `
          <div class="border border-gray-200 rounded-lg p-6 mb-6 bg-gray-50">
            <h3 class="text-xl font-bold text-gray-900 mb-4 flex items-center gap-2">
              <span class="flex items-center justify-center w-8 h-8 bg-purple-100 text-purple-600 rounded-full text-sm font-bold">${Object.keys(reportsByType).indexOf(reportType) + 1}</span>
              ${reportType}
            </h3>
            <p class="text-sm text-gray-600 mb-4">Tổng số: ${reports.length} báo cáo</p>
            <div class="space-y-4">
        `;

        const reportsByForm = {};
        reports.forEach(report => {
          const formName = report.form_name;
          if (!reportsByForm[formName]) reportsByForm[formName] = [];
          reportsByForm[formName].push(report);
        });

        for (const [formName, formReports] of Object.entries(reportsByForm)) {
          summaryHTML += `<div class="bg-white rounded-lg p-4 border border-gray-200"><h4 class="font-semibold text-gray-800 mb-3">${formName}</h4><div class="space-y-2">`;
          formReports.forEach(report => {
            const createdDate = new Date(report.created_at).toLocaleString('vi-VN');
            summaryHTML += `
              <div class="flex items-center justify-between text-sm p-3 bg-gray-50 rounded border border-gray-100">
                <div class="flex-1">
                  <p class="font-medium text-gray-800">${report.organization_name}</p>
                  <p class="text-gray-600 text-xs mt-1">Người báo cáo: ${report.position} • ${report.phone}</p>
                  <p class="text-gray-500 text-xs mt-1">Thời gian: ${createdDate}</p>
                </div>
                <button class="view-detail-btn px-4 py-2 bg-purple-600 text-white text-xs rounded hover:bg-purple-700 transition-colors" data-report-id="${report.report_id}">Xem chi tiết</button>
              </div>`;
          });
          summaryHTML += `</div></div>`;
        }
        summaryHTML += `</div></div>`;
      }
      container.innerHTML = summaryHTML;
      document.querySelectorAll('.view-detail-btn').forEach(btn => {
        btn.addEventListener('click', (e) => {
          const reportId = e.target.dataset.reportId;
          const report = allReports.find(r => r.report_id === reportId);
          if (report) showReportDetail(report);
        });
      });
    }

    function showReportDetail(report) {
      const data = JSON.parse(report.data_json);
      let detailHTML = `
        <div class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50 p-4" id="detail-modal">
          <div class="bg-white rounded-xl shadow-2xl max-w-4xl w-full max-h-[90vh] overflow-auto">
            <div class="sticky top-0 bg-white border-b border-gray-200 p-6 z-10">
              <div class="flex justify-between items-start">
                <div>
                  <h3 class="text-2xl font-bold text-gray-900">${report.form_name}</h3>
                  <p class="text-sm text-gray-600 mt-2">
                    <strong>Cơ quan:</strong> ${report.organization_name}<br>
                    <strong>Người báo cáo:</strong> ${report.position} • ${report.phone}<br>
                    <strong>Thời gian:</strong> ${new Date(report.created_at).toLocaleString('vi-VN')}
                  </p>
                </div>
                <button class="close-detail-btn text-gray-500 hover:text-gray-700 text-2xl font-bold">×</button>
              </div>
            </div>
            <div class="p-6">
              <div class="table-container">
                <table class="data-table w-full bg-white">
                  <thead><tr><th class="text-left">Mục</th><th>Giá trị</th></tr></thead>
                  <tbody>
      `;
      for (const [key, value] of Object.entries(data)) {
        detailHTML += `<tr><td class="text-left font-medium">${key}</td><td class="text-center">${value}</td></tr>`;
      }
      detailHTML += `</tbody></table></div></div></div></div>`;
      document.body.insertAdjacentHTML('beforeend', detailHTML);
      const modal = document.getElementById('detail-modal');
      modal.querySelector('.close-detail-btn').addEventListener('click', () => modal.remove());
      modal.addEventListener('click', (e) => { if (e.target === modal) modal.remove(); });
    }

    // Event listeners
    document.getElementById('next-to-type-btn').addEventListener('click', () => { if (validateGeneralInfo()) showStep('type'); });
    document.getElementById('back-to-general-btn').addEventListener('click', () => showStep('general'));
    document.getElementById('back-to-type-btn').addEventListener('click', () => showStep('type'));
    document.getElementById('prev-form-btn').addEventListener('click', prevForm);
    document.getElementById('next-form-btn').addEventListener('click', nextForm);
    document.querySelectorAll('.report-type-btn').forEach(btn => {
      btn.addEventListener('click', (e) => {
        const type = e.currentTarget.dataset.type;
        selectedType = type;
        generateForms(type);
        showStep('forms');
      });
    });
    document.getElementById('submit-report-btn').addEventListener('click', submitReport);
    document.getElementById('submit-another-btn').addEventListener('click', () => showStep('general'));
    document.getElementById('view-summary-btn').addEventListener('click', showPasswordModal);
    document.getElementById('cancel-password-btn').addEventListener('click', hidePasswordModal);
    document.getElementById('submit-password-btn').addEventListener('click', verifyPasswordAndShowSummary);
    document.getElementById('password-input').addEventListener('keypress', (e) => { if (e.key === 'Enter') verifyPasswordAndShowSummary(); });
    document.getElementById('close-summary-btn').addEventListener('click', hideSummaryView);
  </script>
 </body>
</html>