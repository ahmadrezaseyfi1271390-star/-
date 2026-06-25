<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>گالری تصاویر با ذخیره‌سازی</title>

    <style>
        /* ===== استایل‌های پایه ===== */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: Tahoma, sans-serif;
        }

        body {
            background: #0f0f0f;
            color: #fff;
        }

        /* ===== هدر ===== */
        header {
            height: 60px;
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding: 0 15px;
            background: #181818;
            position: sticky;
            top: 0;
            z-index: 100;
        }

        .logo {
            font-size: 22px;
            font-weight: bold;
        }

        .menu-btn {
            font-size: 28px;
            cursor: pointer;
        }

        /* ===== سایدبار ===== */
        .sidebar {
            position: fixed;
            top: 0;
            right: -260px;
            width: 260px;
            height: 100%;
            background: #1b1b1b;
            transition: .3s;
            z-index: 1000;
            overflow: auto;
            padding-bottom: 30px;
        }

        .sidebar.active {
            right: 0;
        }

        .sidebar h2 {
            padding: 20px;
            border-bottom: 1px solid #333;
        }

        .sidebar .cat-item {
            padding: 15px 20px;
            cursor: pointer;
            border-bottom: 1px solid #2c2c2c;
        }

        .sidebar .cat-item:hover {
            background: #2c2c2c;
        }

        /* ===== جعبه جستجو ===== */
        .search-box {
            padding: 15px;
        }

        .search-box input {
            width: 100%;
            padding: 12px;
            border: none;
            border-radius: 12px;
            font-size: 16px;
            background: #222;
            color: #fff;
        }

        /* ===== فرم افزودن تصویر ===== */
        .add-form {
            background: #1a1a1a;
            padding: 15px;
            margin: 0 15px 15px 15px;
            border-radius: 18px;
            display: flex;
            flex-wrap: wrap;
            gap: 10px;
            align-items: center;
        }

        .add-form input,
        .add-form select {
            flex: 1 1 150px;
            padding: 10px 12px;
            border: none;
            border-radius: 10px;
            background: #2a2a2a;
            color: #fff;
            font-size: 14px;
        }

        .add-form input::placeholder {
            color: #888;
        }

        .add-form button {
            padding: 10px 20px;
            border: none;
            border-radius: 10px;
            background: #4caf50;
            color: #fff;
            font-weight: bold;
            cursor: pointer;
            transition: .2s;
            flex: 0 0 auto;
        }

        .add-form button:hover {
            background: #66bb6a;
        }

        /* ===== گالری ===== */
        .gallery {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
            gap: 15px;
            padding: 15px;
        }

        .card {
            background: #1a1a1a;
            border-radius: 18px;
            overflow: hidden;
            transition: .3s;
            position: relative;
        }

        .card:hover {
            transform: scale(1.03);
        }

        .card img {
            width: 100%;
            height: 200px;
            object-fit: cover;
            display: block;
            cursor: pointer;
        }

        .card .info {
            padding: 12px;
        }

        .card .title {
            font-size: 18px;
            margin-bottom: 8px;
        }

        .card .cat {
            font-size: 13px;
            opacity: .7;
        }

        /* دکمه حذف روی کارت */
        .card .delete-btn {
            position: absolute;
            top: 8px;
            left: 8px;
            background: rgba(255, 0, 0, 0.8);
            border: none;
            border-radius: 50%;
            width: 30px;
            height: 30px;
            color: #fff;
            font-size: 18px;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: .2s;
            z-index: 10;
        }

        .card .delete-btn:hover {
            background: #ff3333;
            transform: scale(1.1);
        }

        /* ===== نمایشگر بزرگ ===== */
        .viewer {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, .95);
            z-index: 2000;
            justify-content: center;
            align-items: center;
            flex-direction: column;
            padding: 20px;
        }

        .viewer img {
            max-width: 95%;
            max-height: 75%;
            border-radius: 15px;
        }

        .viewer h2 {
            margin-top: 15px;
        }

        .viewer p {
            margin-top: 10px;
            text-align: center;
            max-width: 700px;
        }

        .close {
            position: absolute;
            top: 20px;
            left: 20px;
            font-size: 35px;
            cursor: pointer;
        }

        /* ===== پیام خالی ===== */
        .empty-msg {
            text-align: center;
            padding: 50px 20px;
            font-size: 18px;
            opacity: .6;
            grid-column: 1 / -1;
        }
    </style>
</head>
<body>

    <!-- ===== هدر ===== -->
    <header>
        <div class="logo">📸 گالری</div>
        <div class="menu-btn" onclick="toggleMenu()">☰</div>
    </header>

    <!-- ===== سایدبار ===== -->
    <div class="sidebar" id="sidebar">
        <h2>دسته‌بندی‌ها</h2>

        <div class="cat-item" onclick="filterCategory('همه')">همه</div>
        <div class="cat-item" onclick="filterCategory('طبیعت')">طبیعت</div>
        <div class="cat-item" onclick="filterCategory('ماشین')">ماشین</div>
        <div class="cat-item" onclick="filterCategory('انیمه')">انیمه</div>
        <div class="cat-item" onclick="filterCategory('فناوری')">فناوری</div>
        <div class="cat-item" onclick="filterCategory('سایر')">سایر</div>
    </div>

    <!-- ===== جعبه جستجو ===== -->
    <div class="search-box">
        <input type="text" id="search" placeholder="جستجو در عنوان، توضیحات یا دسته‌بندی..." onkeyup="searchImages()">
    </div>

    <!-- ===== فرم افزودن تصویر جدید ===== -->
    <div class="add-form" id="addForm">
        <input type="text" id="newTitle" placeholder="عنوان" required>
        <input type="text" id="newCategory" placeholder="دسته‌بندی (اختیاری)" value="سایر">
        <input type="text" id="newDesc" placeholder="توضیحات">
        <input type="url" id="newImage" placeholder="آدرس تصویر (URL)" required>
        <button onclick="addImage()">➕ افزودن</button>
    </div>

    <!-- ===== گالری ===== -->
    <div class="gallery" id="gallery"></div>

    <!-- ===== نمایشگر بزرگ ===== -->
    <div class="viewer" id="viewer">
        <div class="close" onclick="closeViewer()">✕</div>
        <img id="bigImg" src="" alt="تصویر بزرگ">
        <h2 id="bigTitle"></h2>
        <p id="bigDesc"></p>
    </div>

    <script>
        // ============================================================
        //  داده‌های نمونه (فقط در صورتی که localStorage خالی باشد)
        // ============================================================
        const defaultImages = [{
            title: "جنگل سرسبز",
            category: "طبیعت",
            desc: "منظره‌ای زیبا از جنگل‌های شمال ایران",
            image: "https://taw-bio.ir/f/item/image/jcsfh"   // لینک نمونه (قابل تعویض)
        }, {
            title: "خودروی اسپرت",
            category: "ماشین",
            desc: "یک ماشین اسپرت مدرن با طراحی خیره‌کننده",
            image: "https://taw-bio.ir/f/item/image/jcsfh"
        }, {
            title: "شخصیت انیمه",
            category: "انیمه",
            desc: "شخصیت محبوب از یک سریال معروف",
            image: "https://taw-bio.ir/f/item/image/jcsfh"
        }, {
            title: "لاب‌تاپ جدید",
            category: "فناوری",
            desc: "جدیدترین مدل لپ‌تاپ با صفحه‌نمایش ۴K",
            image: "https://taw-bio.ir/f/item/image/jcsfh"
        }];

        // ============================================================
        //  متغیرهای اصلی
        // ============================================================
        let allImages = [];           // آرایه کامل تصاویر (همیشه از localStorage)
        let currentFiltered = [];     // آرایه فیلتر شده فعلی برای نمایش

        // ============================================================
        //  توابع ذخیره‌سازی و بارگذاری در localStorage
        // ============================================================
        function saveToLocalStorage() {
            localStorage.setItem('galleryImages', JSON.stringify(allImages));
        }

        function loadFromLocalStorage() {
            const stored = localStorage.getItem('galleryImages');
            if (stored) {
                try {
                    allImages = JSON.parse(stored);
                } catch (e) {
                    allImages = [...defaultImages];
                }
            } else {
                allImages = [...defaultImages];
            }
            // اگر آرایه خالی بود، نمونه‌ها را بازگردان
            if (!allImages || allImages.length === 0) {
                allImages = [...defaultImages];
            }
            saveToLocalStorage();  // ذخیره برای دفعات بعد
        }

        // ============================================================
        //  رندر کردن گالری
        // ============================================================
        function render(list) {
            const gallery = document.getElementById('gallery');
            gallery.innerHTML = '';

            if (!list || list.length === 0) {
                gallery.innerHTML = `<div class="empty-msg">😕 هیچ تصویری یافت نشد.</div>`;
                return;
            }

            list.forEach((item, index) => {
                // برای حذف، نیاز به شناسه یکتا داریم؛ از index در آرایه اصلی استفاده می‌کنیم
                // اما چون ممکن است ترتیب تغییر کند، بهتره یک id اختصاصی بدهیم (در اینجا از index استفاده می‌کنیم و هنگام حذف، فیلتر بر اساس عنوان و URL انجام می‌دهیم)
                // روش ساده‌تر: وقتی حذف می‌کنیم، از روی title و image فیلتر می‌کنیم (با فرض یکتا بودن)
                const card = document.createElement('div');
                card.className = 'card';

                card.innerHTML = `
                    <button class="delete-btn" onclick="deleteImage('${item.title}', '${item.image}')">✕</button>
                    <img src="${item.image}" alt="${item.title}" onclick="openViewer('${item.image}', '${item.title}', '${item.desc}')">
                    <div class="info">
                        <div class="title">${item.title}</div>
                        <div class="cat">📂 ${item.category}</div>
                    </div>
                `;
                gallery.appendChild(card);
            });
        }

        // ============================================================
        //  نمایش بر اساس فیلتر و جستجو
        // ============================================================
        function renderCurrent() {
            render(currentFiltered);
        }

        // ============================================================
        //  افزودن تصویر جدید
        // ============================================================
        function addImage() {
            const titleInput = document.getElementById('newTitle');
            const catInput = document.getElementById('newCategory');
            const descInput = document.getElementById('newDesc');
            const imgInput = document.getElementById('newImage');

            const title = titleInput.value.trim();
            const category = catInput.value.trim() || 'سایر';
            const desc = descInput.value.trim();
            const image = imgInput.value.trim();

            if (!title || !image) {
                alert('لطفاً عنوان و آدرس تصویر را وارد کنید.');
                return;
            }

            // اعتبارسنجی ساده URL
            try {
                new URL(image);
            } catch (_) {
                alert('آدرس تصویر معتبر نیست (لطفاً با http:// یا https:// شروع شود).');
                return;
            }

            const newItem = { title, category, desc, image };
            allImages.push(newItem);
            saveToLocalStorage();

            // پاک کردن فرم
            titleInput.value = '';
            catInput.value = 'سایر';
            descInput.value = '';
            imgInput.value = '';

            // به‌روزرسانی فیلتر فعلی (همه)
            filterCategory('همه');
        }

        // ============================================================
        //  حذف تصویر
        // ============================================================
        function deleteImage(title, imageUrl) {
            if (!confirm(`آیا از حذف "${title}" مطمئن هستید؟`)) return;

            // حذف از allImages (با تطابق title و image)
            allImages = allImages.filter(item => !(item.title === title && item.image === imageUrl));
            saveToLocalStorage();

            // بازنشانی فیلتر فعلی
            filterCategory('همه');
        }

        // ============================================================
        //  فیلتر بر اساس دسته‌بندی
        // ============================================================
        function filterCategory(cat) {
            if (cat === 'همه') {
                currentFiltered = [...allImages];
            } else {
                currentFiltered = allImages.filter(item => item.category === cat);
            }
            renderCurrent();
            // بستن سایدبار (در موبایل)
            document.getElementById('sidebar').classList.remove('active');
        }

        // ============================================================
        //  جستجو
        // ============================================================
        function searchImages() {
            const q = document.getElementById('search').value.toLowerCase().trim();
            if (q === '') {
                // اگر جستجو خالی بود، همه را نشان بده (با حفظ فیلتر دسته‌بندی؟)
                // بهتر است همیشه جستجو روی allImages اعمال شود، اما فیلتر دسته‌بندی فعلی را هم در نظر بگیریم.
                // برای سادگی، روی allImages جستجو می‌کنیم و نتیجه را نمایش می‌دهیم.
                currentFiltered = [...allImages];
            } else {
                currentFiltered = allImages.filter(item =>
                    item.title.toLowerCase().includes(q) ||
                    item.desc.toLowerCase().includes(q) ||
                    item.category.toLowerCase().includes(q)
                );
            }
            renderCurrent();
        }

        // ============================================================
        //  باز و بسته کردن سایدبار
        // ============================================================
        function toggleMenu() {
            document.getElementById('sidebar').classList.toggle('active');
        }

        // ============================================================
        //  نمایشگر بزرگ
        // ============================================================
        function openViewer(img, title, desc) {
            document.getElementById('bigImg').src = img;
            document.getElementById('bigTitle').innerText = title;
            document.getElementById('bigDesc').innerText = desc;
            document.getElementById('viewer').style.display = 'flex';
        }

        function closeViewer() {
            document.getElementById('viewer').style.display = 'none';
        }

        // ============================================================
        //  مقداردهی اولیه
        // ============================================================
        function init() {
            loadFromLocalStorage();
            // نمایش همه تصاویر در ابتدا
            filterCategory('همه');
        }

        // اجرا
        init();
    </script>

</body>
</html>
