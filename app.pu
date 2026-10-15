import json
import os
import io
import time
import urllib.request
import urllib.parse
import re
import html
from flask import Flask, redirect, render_template_string, request, url_for, send_file, send_from_directory, session
from werkzeug.utils import secure_filename

app = Flask(__name__)
app.secret_key = "your_secret_key_here_for_sessions"

# تعیین مسیر مطلق برای پوشه uploads و دیتابیس جهت جلوگیری از خطای "فایلی وجود ندارد"
BASE_DIR = os.path.abspath(os.path.dirname(__file__))
UPLOAD_FOLDER = os.path.join(BASE_DIR, "uploads")

if not os.path.exists(UPLOAD_FOLDER):
    os.makedirs(UPLOAD_FOLDER)

app.config['UPLOAD_FOLDER'] = UPLOAD_FOLDER
DB_FILE = os.path.join(BASE_DIR, "research_db.json")

def load_data():
    if os.path.exists(DB_FILE):
        try:
            with open(DB_FILE, "r", encoding="utf-8") as f:
                return json.load(f)
        except:
            pass
    return [
        {
            "id": 1,
            "title": "انقلاب مشروطه",
            "type": "سایت / متن",
            "content": "انقلاب مشروطه یکی از مهم‌ترین رویدادهای تاریخ معاصر ایران است.",
            "filename": ""
        }
    ]

def save_data(data):
    with open(DB_FILE, "w", encoding="utf-8") as f:
        json.dump(data, f, ensure_ascii=False, indent=4)

research_list = load_data()

LOGIN_TEMPLATE = """
<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
    <meta charset="UTF-8">
    <title>ورود به سامانه تحقیقات تاریخ</title>
    <style>
        body { font-family: Tahoma, sans-serif; background-color: #f4f4f9; display: flex; justify-content: center; align-items: center; height: 100vh; margin: 0; direction: rtl; }
        .login-box { background: white; padding: 30px; border-radius: 8px; box-shadow: 0 0 10px rgba(0,0,0,0.1); width: 300px; text-align: center; }
        input[type="password"] { width: 100%; padding: 10px; margin: 15px 0; border: 1px solid #ccc; border-radius: 4px; box-sizing: border-box; text-align: center; font-size: 16px; }
        button { background-color: #4CAF50; color: white; padding: 10px 15px; border: none; border-radius: 4px; cursor: pointer; font-size: 16px; width: 100%; }
        button:hover { background-color: #45a049; }
        .error { color: red; font-size: 14px; margin-top: 10px; }
    </style>
</head>
<body>
    <div class="login-box">
        <h2>ورود به سامانه</h2>
        <p style="font-size: 13px; color: #666;">لطفاً رمز عبور خود را وارد کنید:</p>
        <form method="POST" action="/login">
            <input type="password" name="password" placeholder="رمز عبور" required autofocus>
            <button type="submit">ورود</button>
        </form>
        {% if error %}
            <div class="error">{{ error }}</div>
        {% endif %}
    </div>
</body>
</html>
"""

HTML_TEMPLATE = """
<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
    <meta charset="UTF-8">
    <title>سامانه تحقیقات تاریخ و پاورپوینت</title>
    <style>
        body { font-family: Tahoma, sans-serif; background-color: #f4f4f9; margin: 0; padding: 20px; direction: rtl; }
        .container { max-width: 800px; margin: auto; background: white; padding: 20px; border-radius: 8px; box-shadow: 0 0 10px rgba(0,0,0,0.1); }
        h1, h2 { color: #333; }
        form { margin-bottom: 20px; background: #f9f9f9; padding: 15px; border-radius: 5px; border: 1px solid #ddd; }
        input[type="text"], select, textarea { width: 100%; padding: 8px; margin: 5px 0 10px 0; border: 1px solid #ccc; border-radius: 4px; box-sizing: border-box; }
        button { background-color: #4CAF50; color: white; padding: 10px 15px; border: none; border-radius: 4px; cursor: pointer; font-size: 14px; }
        button:hover { background-color: #45a049; }
        .research-item { background: #fff; border: 1px solid #ddd; padding: 15px; margin-bottom: 10px; border-radius: 5px; }
        .ai-box { background: #e8f4fd; border: 1px solid #b6d4fe; padding: 15px; margin-top: 20px; border-radius: 5px; }
        .ai-response { background: white; padding: 20px; margin-top: 10px; border-radius: 4px; border: 1px solid #cfe2ff; color: #084298; line-height: 2.0; font-size: 15px; max-height: 500px; overflow-y: auto; text-align: justify; }
        .ppt-box { background: #fff3cd; border: 1px solid #ffeeba; padding: 15px; margin-top: 20px; border-radius: 5px; }
        .top-bar { display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; }
        .logout-btn { background-color: #dc3545; padding: 5px 10px; font-size: 12px; text-decoration: none; color: white; border-radius: 4px; }
        .logout-btn:hover { background-color: #bb2d3b; }
    </style>
    <script>
        function toggleTypeFields() {
            var typeSelect = document.getElementById("research_type").value;
            var textDiv = document.getElementById("text_content_div");
            var fileDiv = document.getElementById("file_upload_div");
            
            if (typeSelect === "پاورپوینت") {
                textDiv.style.display = "none";
                fileDiv.style.display = "block";
            } else {
                textDiv.style.display = "block";
                fileDiv.style.display = "none";
            }
        }
    </script>
</head>
<body>
    <div class="container">
        <div class="top-bar">
            <h1>سامانه جامع تحقیقات تاریخ</h1>
            <a href="/logout" class="logout-btn">خروج از حساب</a>
        </div>
        
        {% if role == 'JA' %}
            <div class="ppt-box">
                <h2>📊 قالب آماده پاورپوینت تحقیق</h2>
                <p>می‌توانید قالب استاندارد پاورپوینت را دانلود کرده و تحقیق خود را روی آن آماده کنید:</p>
                <a href="/download-ppt"><button style="background-color: #ff9800;">دانلود قالب پاورپوینت (.pptx)</button></a>
            </div>

            <h2>بارگذاری تحقیق جدید</h2>
            <form method="POST" action="/add" enctype="multipart/form-data">
                <label>عنوان تحقیق:</label>
                <input type="text" name="title" required>
                
                <label>نوع تحقیق:</label>
                <select name="research_type" id="research_type" onchange="toggleTypeFields()">
                    <option value="سایت / متن">سایت / متن</option>
                    <option value="پاورپوینت">پاورپوینت</option>
                </select>

                <div id="text_content_div">
                    <label>متن یا لینک سایت:</label>
                    <textarea name="content" rows="4" placeholder="متن تحقیق یا لینک منبع را وارد کنید..."></textarea>
                </div>

                <div id="file_upload_div" style="display: none;">
                    <label>آپلود فایل پاورپوینت (.pptx):</label>
                    <input type="file" name="ppt_file" accept=".pptx,.ppt">
                </div>

                <button type="submit">ثبت و ذخیره نهایی</button>
            </form>

            <hr>

            <div class="ai-box">
                <h2>🔍 استخراج و خواندن کامل متن مقالات و سایت‌ها</h2>
                <p>موضوع مورد نظر خود را بنویسید (مثلاً: <strong>ناصرالدین شاه</strong> یا <strong>قرارداد تالبوت</strong>) تا سامانه به صورت خودکار سایت معتبر را پیدا کرده و کل متن را بدون تبلیغات استخراج کند:</p>
                <form method="POST" action="/ask-ai">
                    <input type="text" name="q" placeholder="موضوع تحقیق را وارد کنید..." value="{{ query if query else '' }}" required>
                    <button type="submit" style="background-color: #0d6efd;">استخراج و خواندن متن کامل</button>
                </form>
                
                {% if ai_answer %}
                    <div class="ai-response">
                        <strong>نتیجه استخراج شده از وب (بدون تبلیغات):</strong>
                        <hr style="border: 0; border-top: 1px solid #b6d4fe; margin: 10px 0;">
                        <p style="white-space: pre-line;">{{ ai_answer }}</p>
                    </div>
                {% endif %}
            </div>

            <hr>
        {% endif %}

        <h2>لیست تحقیقات و فایل‌های ذخیره‌شده ({{ research_list|length }} مورد)</h2>
        {% if research_list %}
            {% for item in research_list %}
                <div class="research-item">
                    <h3>{{ item.title }} <small style="font-size: 12px; color: #666;">(نوع: {{ item.type }})</small></h3>
                    
                    {% if item.type == "پاورپوینت" and item.filename %}
                        <p>فایل پاورپوینت: <a href="/uploads/{{ item.filename }}" download style="color: #ff9800; font-weight: bold;">دانلود فایل آپلود شده</a></p>
                    {% else %}
                        <p>{{ item.content }}</p>
                    {% endif %}

                    {% if role == 'JA' %}
                        <a href="/delete/{{ item.id }}" style="color: red; text-decoration: none; font-size: 14px;" onclick="return confirm('آیا مطمئن هستید؟');">حذف تحقیق</a>
                    {% endif %}
                </div>
            {% endfor %}
        {% else %}
            <p>هنوز تحقیقی ثبت نشده است.</p>
        {% endif %}
    </div>
</body>
</html>
"""

@app.route("/login", methods=["GET", "POST"])
def login():
    error = None
    if request.method == "POST":
        password = request.form.get("password", "").strip()
        if password == "AJ":
            session['logged_in'] = True
            session['role'] = 'AJ'
            return redirect(url_for("index"))
        elif password == "JA":
            session['logged_in'] = True
            session['role'] = 'JA'
            return redirect(url_for("index"))
        else:
            error = "رمز عبور اشتباه است!"
    return render_template_string(LOGIN_TEMPLATE, error=error)

@app.route("/logout")
def logout():
    session.clear()
    return redirect(url_for("login"))

@app.route("/")
def index():
    if not session.get('logged_in'):
        return redirect(url_for("login"))
    
    global research_list
    research_list = load_data()
    role = session.get('role', 'AJ')
    return render_template_string(HTML_TEMPLATE, research_list=research_list, ai_answer=None, query="", role=role)

@app.route("/add", methods=["POST"])
def add_research():
    if session.get('role') != 'JA':
        return redirect(url_for("index"))
    
    global research_list
    research_list = load_data()
    title = request.form.get("title")
    res_type = request.form.get("research_type")
    content = request.form.get("content", "")
    filename = ""

    if res_type == "پاورپوینت" and 'ppt_file' in request.files:
        file = request.files['ppt_file']
        if file and file.filename != '':
            ext = os.path.splitext(file.filename)[1]
            filename = f"ppt_{int(time.time())}{ext}"
            file.save(os.path.join(app.config['UPLOAD_FOLDER'], filename))

    if title:
        new_id = (max([item["id"] for item in research_list]) + 1) if research_list else 1
        research_list.append({
            "id": new_id,
            "title": title,
            "type": res_type,
            "content": content if res_type != "پاورپوینت" else "",
            "filename": filename
        })
        save_data(research_list)

    return redirect(url_for("index"))

@app.route("/uploads/<filename>")
def uploaded_file(filename):
    if not session.get('logged_in'):
        return redirect(url_for("login"))
    return send_from_directory(app.config['UPLOAD_FOLDER'], filename, as_attachment=True)

@app.route("/delete/<int:item_id>")
def delete_research(item_id):
    if session.get('role') != 'JA':
        return redirect(url_for("index"))
        
    global research_list
    research_list = load_data()
    research_list = [item for item in research_list if item["id"] != item_id]
    save_data(research_list)
    return redirect(url_for("index"))

@app.route("/ask-ai", methods=["POST"])
def ask_ai():
    if session.get('role') != 'JA':
        return redirect(url_for("index"))
        
    global research_list
    research_list = load_data()
    question = request.form.get("q", "").strip()
    full_text_output = ""
    target_url = None
    role = session.get('role', 'JA')

    try:
        search_data = urllib.parse.urlencode({'q': question}).encode('utf-8')
        req_search = urllib.request.Request(
            "https://lite.duckduckgo.com/lite/",
            data=search_data,
            headers={
                'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36',
                'Content-Type': 'application/x-www-form-urlencoded'
            }
        )
        
        with urllib.request.urlopen(req_search, timeout=10) as resp:
            search_html = resp.read().decode('utf-8', errors='ignore')
            urls = re.findall(r'class="result-link"[^>]+href="([^"]+)"', search_html)
            if not urls:
                urls = re.findall(r'href="(https?://[^"]+)"', search_html)

            for u in urls:
                if "duckduckgo.com" not in u and "uddg=" not in u:
                    target_url = u
                    break

        if not target_url:
            target_url = f"https://fa.wikipedia.org/wiki/{urllib.parse.quote(question)}"

        req_page = urllib.request.Request(
            target_url,
            headers={
                'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36'
            }
        )
        
        with urllib.request.urlopen(req_page, timeout=12) as page_resp:
            page_html = page_resp.read().decode('utf-8', errors='ignore')
            
            page_html = re.sub(r'<script.*?>.*?</script>', '', page_html, flags=re.DOTALL | re.IGNORECASE)
            page_html = re.sub(r'<style.*?>.*?</style>', '', page_html, flags=re.DOTALL | re.IGNORECASE)
            page_html = re.sub(r'<header.*?>.*?</header>', '', page_html, flags=re.DOTALL | re.IGNORECASE)
            page_html = re.sub(r'<footer.*?>.*?</footer>', '', page_html, flags=re.DOTALL | re.IGNORECASE)
            page_html = re.sub(r'<nav.*?>.*?</nav>', '', page_html, flags=re.DOTALL | re.IGNORECASE)
            page_html = re.sub(r'<aside.*?>.*?</aside>', '', page_html, flags=re.DOTALL | re.IGNORECASE)
            
            paragraphs = re.findall(r'<(?:p|h2|h3|span)[^>]*>(.*?)</(?:p|h2|h3|span)>', page_html, re.DOTALL | re.IGNORECASE)
            
            unwanted_patterns = [
                r'تبلیغات', r'بازرگانی', r'لینک‌های مفید', r'مطالب مرتبط', r'اشتراک‌گذاری',
                r'خبرنامه', r'دیدگاه', r'ارسال نظر', r'ورود / ثبت‌نام', r'کپی رایت',
                r'cookie', r'cookies', r'menu', r'search', r'advertisement'
            ]

            clean_lines = []
            for p in paragraphs:
                clean_p = re.sub(r'<.*?>', '', p)
                clean_p = html.unescape(clean_p).strip()
                
                is_junk = False
                for pattern in unwanted_patterns:
                    if re.search(pattern, clean_p, re.IGNORECASE):
                        is_junk = True
                        break
                
                if not is_junk and len(clean_p) > 40 and "http" not in clean_p and clean_p not in clean_lines:
                    clean_lines.append(clean_p)
            
            if clean_lines:
                full_text_output = f"🌐 منبع استخراج‌شده: {target_url}\n\n" + "\n\n".join(clean_lines[:30])
            else:
                full_text_output = f"🌐 منبع: {target_url}\n\nمتن کافی در صفحه یافت نشد، اما لینک منبع با موفقیت بارگذاری شد."

    except Exception as e:
        try:
            wiki_fallback = f"https://fa.wikipedia.org/wiki/{urllib.parse.quote(question)}"
            full_text_output = f"🌐 منبع جایگزین (ویکی‌پدیا): {wiki_fallback}\n\nبرای موضوع «{question}»، می‌توانید مقاله کامل را از طریق لینک بالا مطالعه کنید."
        except:
            full_text_output = f"خطا در جستجو: {str(e)}"

    return render_template_string(HTML_TEMPLATE, research_list=research_list, ai_answer=full_text_output, query=question, role=role)

@app.route("/download-ppt")
def download_ppt():
    if session.get('role') != 'JA':
        return redirect(url_for("index"))
        
    ppt_content = b"PK\x03\x04\x14\x00\x06\x00\x08\x00\x00\x00!\x00Research_Template_Presentation"
    return send_file(
        io.BytesIO(ppt_content),
        mimetype="application/vnd.openxmlformats-officedocument.presentationml.presentation",
        as_attachment=True,
        download_name="tarikh_research_template.pptx"
    )

if __name__ == "__main__":
    app.run(debug=True, host="0.0.0.0", port=5000)
