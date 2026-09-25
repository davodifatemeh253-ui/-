<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>سایت شخصی من</title>
    <style>
        /* CSS - استایل‌های ظاهر سایت */
        :root {
            --primary-color: #2563eb;
            --dark-color: #1e293b;
            --light-color: #f8fafc;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            margin: 0;
            padding: 0;
            background-color: var(--light-color);
            color: var(--dark-color);
            line-height: 1.6;
        }

        header {
            background: linear-gradient(135deg, var(--primary-color), #1d4ed8);
            color: white;
            padding: 100px 20px;
            text-align: center;
            clip-path: polygon(0 0, 100% 0, 100% 85%, 0 100%);
        }

        .container {
            max-width: 800px;
            margin: auto;
            padding: 20px;
        }

        .card {
            background: white;
            padding: 30px;
            border-radius: 15px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.1);
            margin-top: -50px;
            text-align: center;
        }

        h1 { margin: 0; font-size: 2.5rem; }
        p { font-size: 1.2rem; color: #64748b; }

        .btn {
            display: inline-block;
            background: var(--primary-color);
            color: white;
            padding: 12px 30px;
            border-radius: 25px;
            text-decoration: none;
            margin-top: 20px;
            transition: 0.3s;
            cursor: pointer;
            border: none;
        }

        .btn:hover {
            transform: translateY(-3px);
            box-shadow: 0 5px 15px rgba(37, 99, 235, 0.4);
        }

        .section {
            margin-top: 50px;
            text-align: center;
        }

        footer {
            text-align: center;
            padding: 40px;
            font-size: 0.9rem;
            color: #94a3b8;
        }

        /* انیمیشن ساده */
        .fade-in {
            animation: fadeIn 2s ease-in;
        }

        @keyframes fadeIn {
            from { opacity: 0; }
            to { opacity: 1; }
        }
    </style>
</head>
<body>

    <header class="fade-in">
        <h1>سلام! به سایت من خوش آمدید</h1>
        <p>من یک طراح و برنامه‌نویس آینده هستم</p>
    </header>

    <div class="container">
        <div class="card">
            <h2>درباره من</h2>
            <p>این اولین سایت من است که با کمک هوش مصنوعی ساخته شده است! من در حال یادگیری نحوه کار با ابزارهای جدید هستم.</p>
            <button class="btn" onclick="showGreeting()">یک پیام برای من!</button>
            <p id="message" style="margin-top: 20px; font-weight: bold; color: var(--primary-color);"></p>
        </div>

        <div class="section">
            <h2>مهارت‌های من</h2>
            <div style="display: flex; justify-content: center; gap: 10px; flex-wrap: wrap;">
                <span style="background: #e2e8f0; padding: 5px 15px; border-radius: 20px;">هوش مصنوعی</span>
                <span style="background: #e2e8f0; padding: 5px 15px; border-radius: 20px;">طراحی وب</span>
                <span style="background: #e2e8f0; padding: 5px 15px; border-radius: 20px;">تکنولوژی</span>
            </div>
        </div>
    </div>

    <footer>
        <p>© ۲۰۲۴ | ساخته شده با عشق و هوش مصنوعی</p>
    </footer>

    <script>
        /* JavaScript - بخش تعاملی سایت */
        function showGreeting() {
            const msgElement = document.getElementById('message');
            msgElement.innerText = "موفق باشی! تو داری قدم‌های بزرگی برای آینده برمی‌داری! 🚀";
            
            // ایجاد یک افکت کوچک در کنسول مرورگر
            console.log("دکمه زده شد و پیام نمایش داده شد.");
        }
    </script>

</body>
</html>
