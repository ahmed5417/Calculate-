# -<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="apple-mobile-web-app-title" content="حاسبتي الذكية">
    <meta name="theme-color" content="#4CAF50">
    <meta name="description" content="تطبيق حاسبة ذكية مع وضعي GOLD و BTC">
    
    <!-- أيقونات iOS -->
    <link rel="apple-touch-icon" href="icons/icon-152x152.png">
    <link rel="apple-touch-icon" sizes="152x152" href="icons/icon-152x152.png">
    <link rel="apple-touch-icon" sizes="180x180" href="icons/icon-192x192.png">
    <link rel="apple-touch-icon" sizes="167x167" href="icons/icon-152x152.png">
    
    <!-- شاشة البداية -->
    <link rel="apple-touch-startup-image" href="icons/icon-512x512.png">
    
    <!-- ملف المانيفست -->
    <link rel="manifest" href="manifest.json">
    
    <title>تسجيل الدخول - حاسبتي الذكية</title>
    <style>
        * {
            -webkit-tap-highlight-color: transparent;
            box-sizing: border-box;
        }
        
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            padding: 20px;
            background-color: #f5f5f5;
            -webkit-touch-callout: none;
            -webkit-user-select: none;
            user-select: none;
            overscroll-behavior: none;
        }
        
        .login-container {
            background-color: white;
            border-radius: 20px;
            width: 100%;
            max-width: 400px;
            box-shadow: 0 8px 24px rgba(0,0,0,0.15);
            overflow: hidden;
            padding: 30px;
        }
        
        .app-logo {
            text-align: center;
            margin-bottom: 30px;
        }
        
        .app-logo img {
            width: 80px;
            height: 80px;
            border-radius: 20px;
        }
        
        h1 {
            text-align: center;
            color: #333;
            margin-top: 0;
            margin-bottom: 30px;
            font-size: 1.8em;
        }
        
        .form-group {
            margin-bottom: 20px;
        }
        
        label {
            display: block;
            margin-bottom: 8px;
            font-weight: 500;
            color: #555;
        }
        
        input[type="text"],
        input[type="password"] {
            width: 100%;
            padding: 15px;
            border: 1px solid #ddd;
            border-radius: 10px;
            font-size: 1em;
            transition: border-color 0.3s;
            direction: rtl;
        }
        
        input[type="text"]:focus,
        input[type="password"]:focus {
            border-color: #4CAF50;
            outline: none;
        }
        
        .login-btn {
            width: 100%;
            padding: 15px;
            background-color: #4CAF50;
            color: white;
            border: none;
            border-radius: 10px;
            font-size: 1.1em;
            font-weight: bold;
            cursor: pointer;
            transition: background-color 0.3s;
        }
        
        .login-btn:active {
            background-color: #3d8b40;
        }
        
        .error-message {
            color: #e74c3c;
            text-align: center;
            margin-top: 15px;
            font-size: 0.9em;
            display: none;
        }
        
        .subscription-status {
            text-align: center;
            margin-top: 20px;
            padding: 10px;
            border-radius: 10px;
            font-size: 0.9em;
            display: none;
        }
        
        .subscription-active {
            background-color: #e8f5e9;
            color: #2e7d32;
        }
        
        .subscription-expired {
            background-color: #ffebee;
            color: #c62828;
        }
        
        .admin-link {
            text-align: center;
            margin-top: 20px;
            font-size: 0.9em;
        }
        
        .admin-link a {
            color: #4CAF50;
            text-decoration: none;
        }
        
        /* تحسينات iOS */
        @supports (-webkit-touch-callout: none) {
            body {
                padding: env(safe-area-inset-top) env(safe-area-inset-right) env(safe-area-inset-bottom) env(safe-area-inset-left);
            }
        }
    </style>
</head>
<body>
    <div class="login-container">
        <div class="app-logo">
            <img src="icons/icon-152x152.png" alt="شعار التطبيق">
        </div>
        <h1>تسجيل الدخول</h1>
        <div class="form-group">
            <label for="username">اسم المستخدم</label>
            <input type="text" id="username" placeholder="أدخل اسم المستخدم">
        </div>
        <div class="form-group">
            <label for="password">كلمة المرور</label>
            <input type="password" id="password" placeholder="أدخل كلمة المرور">
        </div>
        <button class="login-btn" id="loginBtn">تسجيل الدخول</button>
        <div class="error-message" id="errorMessage"></div>
        <div class="subscription-status" id="subscriptionStatus"></div>
        <div class="admin-link" id="adminLink" style="display: none;">
            <a href="admin.html">الانتقال إلى لوحة التحكم</a>
        </div>
    </div>

    <script src="database.js"></script>
    <script>
        // تهيئة قاعدة البيانات
        let db;
        
        document.addEventListener('DOMContentLoaded', function() {
            // إنشاء كائن قاعدة البيانات
            db = new Database();
            
            // إضافة اشتراك دائم للمسؤول عند بدء التطبيق
            setTimeout(function() {
                addPermanentAdminSubscription();
            }, 1000);
            
            // التحقق من وجود جلسة مسجلة
            checkSession();
            
            // إضافة مستمع لزر تسجيل الدخول
            document.getElementById('loginBtn').addEventListener('click', login);
            
            // إضافة مستمع للضغط على Enter في حقول الإدخال
            document.getElementById('username').addEventListener('keypress', function(e) {
                if (e.key === 'Enter') {
                    document.getElementById('password').focus();
                }
            });
            
            document.getElementById('password').addEventListener('keypress', function(e) {
                if (e.key === 'Enter') {
                    login();
                }
            });
            
            // تسجيل Service Worker
            if ('serviceWorker' in navigator) {
                navigator.serviceWorker.register('service-worker.js')
                    .then(reg => console.log('تم تسجيل Service Worker', reg))
                    .catch(err => console.log('فشل تسجيل Service Worker', err));
            }
        });
        
        // إضافة اشتراك دائم للمسؤول
        function addPermanentAdminSubscription() {
            // التحقق من وجود المستخدم المسؤول
            db.getUser('admin').then(user => {
                if (user) {
                    // إضافة اشتراك دائم للمسؤول (تاريخ بعيد جداً)
                    const futureDate = new Date();
                    futureDate.setFullYear(futureDate.getFullYear() + 10); // اشتراك لمدة 10 سنوات
                    
                    db.addSubscription({
                        username: 'admin',
                        expiryDate: futureDate.toISOString()
                    }).then(() => {
                        console.log('تم إضافة اشتراك دائم للمسؤول');
                    }).catch(error => {
                        console.error('خطأ في إضافة اشتراك للمسؤول:', error);
                    });
                }
            });
        }
        
        // التحقق من وجود جلسة مسجلة
        function checkSession() {
            const session = JSON.parse(localStorage.getItem('session'));
            if (session && session.username) {
                // التحقق من صلاحية الاشتراك
                db.checkSubscriptionValidity(session.username).then(isValid => {
                    if (isValid) {
                        // الانتقال إلى صفحة التطبيق
                        redirectToApp(session.username, session.isAdmin);
                    } else {
                        // عرض رسالة انتهاء الاشتراك
                        showSubscriptionStatus(session.username, false);
                        // حذف الجلسة
                        localStorage.removeItem('session');
                    }
                });
            }
        }
        
        // تسجيل الدخول
        function login() {
            const username = document.getElementById('username').value.trim();
            const password = document.getElementById('password').value.trim();
            const errorMessage = document.getElementById('errorMessage');
            
            // التحقق من إدخال البيانات
            if (!username || !password) {
                errorMessage.textContent = 'يرجى إدخال اسم المستخدم وكلمة المرور';
                errorMessage.style.display = 'block';
                return;
            }
            
            // التحقق من صحة بيانات تسجيل الدخول
            db.validateLogin(username, password).then(user => {
                if (user) {
                    // التحقق من صلاحية الاشتراك
                    db.checkSubscriptionValidity(username).then(isValid => {
                        if (isValid) {
                            // حفظ بيانات الجلسة
                            const session = {
                                username: username,
                                isAdmin: user.isAdmin,
                                loginTime: new Date().toISOString()
                            };
                            localStorage.setItem('session', JSON.stringify(session));
                            
                            // الانتقال إلى صفحة التطبيق
                            redirectToApp(username, user.isAdmin);
                        } else {
                            // عرض رسالة انتهاء الاشتراك
                            showSubscriptionStatus(username, false);
                            
                            // إظهار رابط لوحة التحكم للمسؤول
                            if (user.isAdmin) {
                                document.getElementById('adminLink').style.display = 'block';
                            }
                        }
                    });
                } else {
                    // عرض رسالة خطأ
                    errorMessage.textContent = 'اسم المستخدم أو كلمة المرور غير صحيحة';
                    errorMessage.style.display = 'block';
                }
            }).catch(error => {
                console.error('خطأ في تسجيل الدخول:', error);
                errorMessage.textContent = 'حدث خطأ أثناء تسجيل الدخول، يرجى المحاولة مرة أخرى';
                errorMessage.style.display = 'block';
            });
        }
        
        // عرض حالة الاشتراك
        function showSubscriptionStatus(username, isValid) {
            const statusElement = document.getElementById('subscriptionStatus');
            
            if (isValid) {
                statusElement.textContent = 'الاشتراك نشط';
                statusElement.className = 'subscription-status subscription-active';
            } else {
                statusElement.textContent = 'انتهت صلاحية الاشتراك، يرجى التواصل مع المسؤول لتجديد الاشتراك';
                statusElement.className = 'subscription-status subscription-expired';
            }
            
            statusElement.style.display = 'block';
        }
        
        // الانتقال إلى صفحة التطبيق
        function redirectToApp(username, isAdmin) {
            if (isAdmin) {
                // إظهار رابط لوحة التحكم
                document.getElementById('adminLink').style.display = 'block';
            }
            
            // الانتقال إلى صفحة التطبيق
            window.location.href = 'calculator.html';
        }
        
        // إضافة دعم الاهتزاز عند الضغط على الأزرار
        document.querySelector('.login-btn').addEventListener('click', () => {
            if ('vibrate' in navigator) {
                navigator.vibrate(15);
            }
        });
    </script>
</body>
</html>
