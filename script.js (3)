// Standalone-safe storage: gumagana kahit file:// (Chrome) o Spck preview kung blocked ang localStorage.
(function () {
  try {
    var k = '__storage_test__';
    localStorage.setItem(k, '1');
    localStorage.removeItem(k);
  } catch (e) {
    var mem = {};
    var fb = {
      getItem: function (k) { return Object.prototype.hasOwnProperty.call(mem, k) ? mem[k] : null; },
      setItem: function (k, v) { mem[k] = String(v); },
      removeItem: function (k) { delete mem[k]; },
      clear: function () { mem = {}; }
    };
    try { Object.defineProperty(window, 'localStorage', { value: fb, configurable: true }); }
    catch (e2) { window.localStorage = fb; }
  }
})();
// INITIALIZE EMAILJS WITH YOUR PUBLIC KEY
(function() {
    try { if (typeof emailjs !== 'undefined') emailjs.init("d-_OPKnvgIFALghyb"); } catch (e) { console.log('EmailJS init skipped:', e); }
})();

// EMAILJS CONFIGURATION KEYS
const EMAILJS_SERVICE_ID = "service_ymve7uk";
const EMAILJS_TEMPLATE_ID = "template_e20i1ka";

// STAFF SECURITY PIN CODE SYSTEM
let staffPIN = localStorage.getItem('cnc_staff_pin') || '1234';

// USER ACCOUNTS & SESSION STATE MANAGEMENT
// Session = per-tab (sessionStorage) para pwedeng magsabay ang admin at customer
// sa 2 tabs ng same browser. Shared data (users/products/orders) = localStorage.
// "Remember me" = naka-save ang account sa localStorage, kaya kahit isara ang
// browser o magbukas ng bagong tab, naka-login pa rin.
let registeredUsers = JSON.parse(localStorage.getItem('cnc_users')) || [];
let currentUser = null;
let rememberMe = false;
function restoreSession() {
    try {
        currentUser = JSON.parse(sessionStorage.getItem('cnc_current_user')) || null;
        if (currentUser) {
            try { rememberMe = localStorage.getItem('cnc_remember') === '1'; } catch (e) { rememberMe = false; }
            return;
        }
        // walang per-tab session: subukang ibalik ang remembered account (bagong tab / bagong bukas)
        let remembered = null;
        try { remembered = localStorage.getItem('cnc_remembered_user') || localStorage.getItem('cnc_current_user'); } catch (e) {}
        if (remembered) {
            currentUser = JSON.parse(remembered);
            rememberMe = true;
            try {
                sessionStorage.setItem('cnc_current_user', JSON.stringify(currentUser));
                localStorage.setItem('cnc_remembered_user', JSON.stringify(currentUser));
                localStorage.setItem('cnc_remember', '1');
                localStorage.removeItem('cnc_current_user'); // luma, hindi na ginagamit
            } catch (e) {}
        }
    } catch (e) { currentUser = null; }
}
restoreSession();

// STORE GCASH PAYMENT DETAILS — ito ang nakikita ni customer sa checkout.
// qrDataUrl = uploaded na TUNAY na QR galing sa GCash app (mas mainam);
// kapag wala pa, default QR muna ang ipinapakita.
const defaultStoreGcash = { accountName: 'JO***A MA**E Q.', number: '0924-233-0648', qrDataUrl: '' };
const defaultStoreGcashQr = 'https://api.qrserver.com/v1/create-qr-code/?size=150x150&data=GCash-CrustAndCrumbs-09242330648';
let storeGcash = (() => { try { return Object.assign({}, defaultStoreGcash, JSON.parse(localStorage.getItem('cnc_store_gcash') || '{}')); } catch (e) { return Object.assign({}, defaultStoreGcash); } })();

// EDITABLE CONTACT INFORMATION STATE
const defaultContactInfo = {
    location: 'Metro Manila, Philippines',
    phone: '+63 924 233 0648',
    email: 'support@crustandcrumbs.ph',
    hours: '7:00 AM - 8:00 PM (Mon - Sun)'
};
let contactInfo = JSON.parse(localStorage.getItem('cnc_contact_info')) || defaultContactInfo;

// EDITABLE ABOUT US STORY STATE
const defaultAboutStory = {
    title: 'Our Story',
    subtitle: 'Baked by hand, since 2026',
    content: 'Crust & Crumbs started as a home kitchen on Dimasalang Street. Today, our small team still laminates every croissant by hand and bakes in batches so everything reaches you fresh.'
};
let aboutStory = JSON.parse(localStorage.getItem('cnc_about_story')) || defaultAboutStory;

// OTP SYSTEM GLOBAL STATE
let generatedOTP = null;
let isOTPVerified = false;

const defaultProducts = [
    { id: 1, name: 'Butter Croissant', category: 'pastries', price: 217.51, stock: 15, isAvailable: true, desc: 'Flaky, buttery, traditional French croissant.', image: 'https://images.unsplash.com/photo-1555507036-ab1f4038808a?auto=format&fit=crop&w=500&q=80' },
    { id: 2, name: 'Chocolate Lava Cake', category: 'cakes', price: 372.88, stock: 8, isAvailable: true, desc: 'Rich chocolate cake with a molten chocolate center.', image: 'https://images.unsplash.com/photo-1606313564200-e75d5e30476c?auto=format&fit=crop&w=500&q=80' },
    { id: 3, name: 'Artisanal Sourdough', category: 'breads', price: 341.81, stock: 10, isAvailable: true, desc: 'Naturally fermented bread with a crispy crust.', image: 'https://fareisle.com/wp-content/uploads/2020/04/sourdough-bread-ecourse-06-2.jpg' },
    { id: 4, name: 'Strawberry Macaron', category: 'pastries', price: 155.37, stock: 25, isAvailable: true, desc: 'Sweet almond meringue cookie filled with jam.', image: 'https://images.unsplash.com/photo-1569864358642-9d1684040f43?auto=format&fit=crop&w=500&q=80' },
    // Red Velvet photo: CC BY 2.0 kimberlykv (Flickr) via Openverse
    { id: 5, name: 'Red Velvet Slice', category: 'cakes', price: 310.74, stock: 2, isAvailable: true, desc: 'Classic red velvet layered with cream cheese frosting.', image: 'https://user.uploads.dev/file/731545150e4112224a41520b5e8c2e02.jpg' },
    { id: 6, name: 'Cinnamon Roll', category: 'pastries', price: 248.59, stock: 18, isAvailable: true, desc: 'Warm roll spiced with cinnamon and glazed with icing.', image: 'https://images.unsplash.com/photo-1509365465985-25d11c17e812?auto=format&fit=crop&w=500&q=80' }
];

const defaultCustomOptions = {
    syrups: [
        { name: 'Chocolate Syrup', price: 10, isAvailable: true },
        { name: 'Caramel Drizzle', price: 10, isAvailable: true },
        { name: 'White Chocolate', price: 15, isAvailable: true }
    ],
    fruits: [
        { name: 'Fresh Strawberry', price: 20, isAvailable: true },
        { name: 'Cherry on Top', price: 15, isAvailable: true },
        { name: 'Blueberries', price: 25, isAvailable: true }
    ],
    icecream: [
        { name: 'Vanilla Scoop', price: 30, isAvailable: true },
        { name: 'Chocolate Scoop', price: 30, isAvailable: true },
        { name: 'Matcha Scoop', price: 35, isAvailable: true }
    ],
    nuts: [
        { name: 'Sliced Almonds', price: 15, isAvailable: true },
        { name: 'Crushed Pistachio', price: 20, isAvailable: true },
        { name: 'Walnuts', price: 15, isAvailable: true }
    ],
    crunchies: [
        { name: 'Choco Chips', price: 10, isAvailable: true },
        { name: 'Rainbow Sprinkles', price: 10, isAvailable: true },
        { name: 'Oreo Crumbs', price: 15, isAvailable: true }
    ]
};

let products = JSON.parse(localStorage.getItem('cnc_products')) || defaultProducts;
let customOptions = JSON.parse(localStorage.getItem('cnc_custom_options')) || defaultCustomOptions;
let orders = JSON.parse(localStorage.getItem('cnc_orders')) || [];
let orderHistory = JSON.parse(localStorage.getItem('cnc_order_history')) || [];
let trashHistory = JSON.parse(localStorage.getItem('cnc_trash_history')) || [];
let userTrashHistory = JSON.parse(localStorage.getItem('cnc_user_trash_history')) || [];
let dayStart = parseInt(localStorage.getItem('cnc_day_start') || '0', 10) || 0;

// CUSTOMER REVIEWS STORE — hiwalay sa orders para hindi mabura ang reviews
// kahit ma-trash, ma-delete, o ma-End Store Day ang order history.
let customerReviews = [];
try { customerReviews = JSON.parse(localStorage.getItem('cnc_reviews')) || []; } catch (e) { customerReviews = []; }
if (!Array.isArray(customerReviews)) customerReviews = [];
function persistReviews() { try { localStorage.setItem('cnc_reviews', JSON.stringify(customerReviews)); } catch (e) {} }
function upsertReviewEntry(orderId, prodName, patch) {
    const id = orderId + '||' + prodName;
    let ex = customerReviews.find(x => x.id === id);
    if (!ex) { ex = Object.assign({ id, orderId, prodName, createdAt: Date.now() }, patch); customerReviews.unshift(ex); }
    else Object.assign(ex, patch);
    persistReviews();
    return ex;
}
// One-time migration: kupyahin ang reviews na nakadikit sa orders/history/trash
// papunta sa store (mabubuhay pa ang mga review na nasa trash pa).
(function migrateOrderReviewsToStore() {
    let changed = false;
    const all = orders.concat(orderHistory, trashHistory, userTrashHistory);
    all.forEach(o => {
        if (!o.productReviews) return;
        for (let pn in o.productReviews) {
            const r = o.productReviews[pn];
            const id = (o.id || '?') + '||' + pn;
            let ex = customerReviews.find(x => x.id === id);
            if (!ex) {
                customerReviews.push({ id, orderId: o.id, prodName: pn, rating: r.rating, comment: r.comment || '', userEmail: (o.userEmail || ''), custName: (o.custName || 'Valued Customer'), orderTime: (o.timestamp || ''), heartCount: r.heartCount || 0, adminLiked: !!r.adminLiked, adminReply: r.adminReply || '', createdAt: (o.createdAt || Date.now()) });
                changed = true;
            } else {
                if ((r.heartCount || 0) > (ex.heartCount || 0)) { ex.heartCount = r.heartCount; changed = true; }
                if (r.adminLiked && !ex.adminLiked) { ex.adminLiked = true; changed = true; }
                if (r.adminReply && !ex.adminReply) { ex.adminReply = r.adminReply; changed = true; }
            }
        }
    });
    if (changed) persistReviews();
})();

// SALES LEDGER — hiwalay na record ng benta (total/cash/gcash/count) na HINDI
// nabubura kahit i-trash ang order history. End Store Day lang ang nagre-reset.
let salesLedger = null;
function loadLedger() {
    try { salesLedger = JSON.parse(localStorage.getItem('cnc_sales_ledger')) || null; } catch (e) { salesLedger = null; }
    if (!salesLedger || typeof salesLedger.total !== 'number') salesLedger = { total: 0, cash: 0, gcash: 0, count: 0, dayStart: dayStart };
    syncLedgerDay();
}
function syncLedgerDay() {
    if (!salesLedger || salesLedger.dayStart !== dayStart) {
        salesLedger = { total: 0, cash: 0, gcash: 0, count: 0, dayStart: dayStart };
        persistLedger();
    }
}
function persistLedger() {
    try { localStorage.setItem('cnc_sales_ledger', JSON.stringify(salesLedger)); } catch (e) {}
}
function reverseSaleFromLedger(o) {
    // Binawi (unverified/cancelled bago pa ma-receive): ibalik ang nairecord.
    if (!o || !o.salesRecorded) return;
    o.salesRecorded = false;
    const amt = Number(o.total) || 0;
    salesLedger.total = Math.max(0, salesLedger.total - amt);
    if ((o.payment || '').includes('GCash')) salesLedger.gcash = Math.max(0, salesLedger.gcash - amt);
    else salesLedger.cash = Math.max(0, salesLedger.cash - amt);
    salesLedger.count = Math.max(0, salesLedger.count - 1);
}
function recordSaleToLedger(o) {
    if (!o || o.salesRecorded || o.status === 'Cancelled') return;
    o.salesRecorded = true;
    const amt = Number(o.total) || 0;
    salesLedger.total += amt;
    if ((o.payment || '').includes('GCash')) salesLedger.gcash += amt;
    else salesLedger.cash += amt;
    salesLedger.count += 1;
}
loadLedger();

let isSelectionMode = false;
let selectedTrashIndices = [];
let isUserSelectionMode = false;
let selectedUserTrashIndices = [];
let cart = [];
let activeCategory = 'all';
let currentlyCustomizingProduct = null;
let selectedNumberCandles = [];

// DOM Elements
const customerView = document.getElementById('customerView');
const staffView = document.getElementById('staffView');
const tvView = document.getElementById('tvView');
const trackView = document.getElementById('trackView');
const productsGrid = document.getElementById('productsGrid');
const cartDrawer = document.getElementById('cartDrawer');
const cartBtn = document.getElementById('cartBtn');
const overlay = document.getElementById('overlay');
const cartItemsContainer = document.getElementById('cartItems');
const cartTotalDisplay = document.getElementById('cartTotal');
const cartBadge = document.getElementById('cartBadge');
const filterBtns = document.querySelectorAll('.filter-btn');
const staffProductTable = document.getElementById('staffProductTable');
const ordersTableBody = document.getElementById('ordersTableBody');
const searchInput = document.getElementById('searchInput');

function getRememberedEmail() {
    try {
        const r = localStorage.getItem('cnc_remembered_user');
        return r ? (JSON.parse(r).email || null) : null;
    } catch (e) { return null; }
}

function saveState() {
    localStorage.setItem('cnc_products', JSON.stringify(products));
    localStorage.setItem('cnc_custom_options', JSON.stringify(customOptions));
    localStorage.setItem('cnc_orders', JSON.stringify(orders));
    localStorage.setItem('cnc_order_history', JSON.stringify(orderHistory));
    localStorage.setItem('cnc_trash_history', JSON.stringify(trashHistory));
    localStorage.setItem('cnc_user_trash_history', JSON.stringify(userTrashHistory));
    localStorage.setItem('cnc_users', JSON.stringify(registeredUsers));
    localStorage.setItem('cnc_day_start', String(dayStart));
    localStorage.setItem('cnc_contact_info', JSON.stringify(contactInfo));
    localStorage.setItem('cnc_store_gcash', JSON.stringify(storeGcash));
    localStorage.setItem('cnc_about_story', JSON.stringify(aboutStory));
    persistLedger();
    persistReviews();
    try {
        if (currentUser) {
            sessionStorage.setItem('cnc_current_user', JSON.stringify(currentUser));
        } else {
            sessionStorage.removeItem('cnc_current_user');
        }
        if (rememberMe && currentUser) {
            localStorage.setItem('cnc_remembered_user', JSON.stringify(currentUser));
            localStorage.setItem('cnc_remember', '1');
        } else if (currentUser) {
            // huwag burahin ang remembered account ng IBANG tab/user;
            // burahin lang kung sa akin mismo galing (nag-opt out ako)
            try {
                const mine = getRememberedEmail();
                if (mine && mine.toLowerCase() === (currentUser.email || '').toLowerCase()) {
                    localStorage.removeItem('cnc_remembered_user');
                    localStorage.removeItem('cnc_remember');
                }
            } catch (e) {}
        }
        localStorage.removeItem('cnc_current_user'); // luma, hindi na ginagamit
    } catch (e) {}
}

// Sabay testing: admin tab + customer tab sa same browser.
// Kapag nagbago ang shared data sa kabilang tab, i-reload dito at i-render ulit.
function reloadSharedState() {
    try {
        const get = (k, fb) => { try { const v = localStorage.getItem(k); return v ? JSON.parse(v) : fb; } catch (e) { return fb; } };
        products = get('cnc_products', products);
        customOptions = get('cnc_custom_options', customOptions);
        orders = get('cnc_orders', orders);
        orderHistory = get('cnc_order_history', orderHistory);
        trashHistory = get('cnc_trash_history', trashHistory);
        userTrashHistory = get('cnc_user_trash_history', userTrashHistory);
        registeredUsers = get('cnc_users', registeredUsers);
        const ds = parseInt(localStorage.getItem('cnc_day_start') || '0', 10) || 0;
        if (ds) dayStart = ds;
        salesLedger = get('cnc_sales_ledger', salesLedger);
        syncLedgerDay();
        customerReviews = get('cnc_reviews', customerReviews);
        if (!Array.isArray(customerReviews)) customerReviews = [];
        contactInfo = get('cnc_contact_info', contactInfo);
        try { storeGcash = Object.assign({}, defaultStoreGcash, get('cnc_store_gcash', storeGcash)); } catch (e) {}
        aboutStory = get('cnc_about_story', aboutStory);
    } catch (e) {}
}
let __reloadT = null;
window.addEventListener('storage', (e) => {
    if (e.key && !e.key.startsWith('cnc_')) return;
    if (e.key === 'cnc_current_user' || e.key === 'cnc_remember' || e.key === 'cnc_remembered_user') return; // session keys, hindi shared data
    clearTimeout(__reloadT);
    __reloadT = setTimeout(() => {
        reloadSharedState();
        try {
            renderAuthHeaderUI(); renderHamburgerMenu();
            applyProductFilters(); updateCart();
            renderStaffTables(); renderTVDisplay();
            renderTrackView(); renderContactUsUI(); renderAboutStoryUI();
            // Live reviews: ipakita agad ang bagong rating/comment ni customer,
            // pero huwag i-re-render habang nagta-type si admin ng reply.
            const rv = document.getElementById('reviewsView');
            if (rv && rv.classList.contains('active')) {
                const typing = !!rv.querySelector('.admin-reply-input-group input:focus');
                const hasDraft = [...rv.querySelectorAll('.admin-reply-input-group input')].some(i => i.value !== (i.defaultValue || ''));
                if (!typing && !hasDraft) renderAllCustomerReviewsPage();
            }
        } catch (err) {}
    }, 250);
});

function init() {
    renderAuthHeaderUI();
    renderHamburgerMenu();
    renderProducts(products);
    updateCart();
    backfillLedger();
    renderStaffTables();
    renderCustomOptionsStaffTable();
    renderTVDisplay();
    renderContactUsUI();
    renderAboutStoryUI();
    renderStoreGcash();
    setupEventListeners();
    // Kapag staff/admin ang naka-login (hal. after reload na remembered), diretso sa Staff Dashboard.
    // Dati: laging customer menu ang bungad kahit admin, kaya akala ng user ay error.
    if (currentUser && (currentUser.role === 'staff' || currentUser.role === 'admin')) {
        switchView('staff');
    }
    setInterval(() => {
        renderTVDisplay();
        renderStaffTables();
        renderTrackView();
    }, 3000);
}

function renderAboutStoryUI() {
    const titleEl = document.getElementById('displayAboutTitle');
    const subEl = document.getElementById('displayAboutSubtitle');
    const contentEl = document.getElementById('displayAboutContent');
    if (titleEl) titleEl.innerText = aboutStory.title;
    if (subEl) subEl.innerText = aboutStory.subtitle;
    if (contentEl) contentEl.innerText = aboutStory.content;

    const staffTitle = document.getElementById('staffEditAboutTitle');
    const staffSub = document.getElementById('staffEditAboutSub');
    const staffContent = document.getElementById('staffEditAboutContent');
    if (staffTitle) staffTitle.value = aboutStory.title;
    if (staffSub) staffSub.value = aboutStory.subtitle;
    if (staffContent) staffContent.value = aboutStory.content;
}

function handleSaveAboutStory(e) {
    e.preventDefault();
    aboutStory.title = document.getElementById('staffEditAboutTitle').value.trim();
    aboutStory.subtitle = document.getElementById('staffEditAboutSub').value.trim();
    aboutStory.content = document.getElementById('staffEditAboutContent').value.trim();
    saveState();
    renderAboutStoryUI();
    alert(" 'About Us' story updated successfully!");
}

/* --- VIEW NAVIGATION & Feature 4 (Cart Icon Hide on Admin/Staff) --- */
function switchView(viewName) {
    customerView.classList.remove('active');
    staffView.classList.remove('active');
    tvView.classList.remove('active');
    const _tv2 = document.getElementById('trackView');
    if (_tv2) _tv2.classList.remove('active');
    const reviewsView = document.getElementById('reviewsView');
    if (reviewsView) reviewsView.classList.remove('active');
    const aboutView = document.getElementById('aboutView');
    if (aboutView) aboutView.classList.remove('active');

    if (viewName === 'staff') {
        staffView.classList.add('active');
    } else if (viewName === 'tv') {
        tvView.classList.add('active');
        renderTVDisplay();
    } else if (viewName === 'track') {
        const _tv3 = document.getElementById('trackView');
        if (_tv3) _tv3.classList.add('active');
        renderTrackView();
    } else if (viewName === 'about') {
        if (aboutView) aboutView.classList.add('active');
    } else if (viewName === 'reviews') {
        if (reviewsView) {
            reviewsView.classList.add('active');
            renderAllCustomerReviewsPage();
        }
    } else {
        customerView.classList.add('active');
    }

    renderAuthHeaderUI();
    try { updateCart(); } catch (e) {}
}

function toggleStaffOrMenuView() {
    if (staffView.classList.contains('active')) {
        switchView('customer');
    } else {
        promptStaffPIN();
    }
}

function promptStaffPIN() {
    const pin = prompt("Enter Staff Security PIN Code:");
    if (pin === null) return;
    if (pin === staffPIN) {
        switchView('staff');
    } else {
        alert("Incorrect PIN Code! Access Denied.");
    }
}

/* --- CONTACT US REALTIME UPDATE --- */
function renderContactUsUI() {
    document.getElementById('displayContactLocation').innerText = contactInfo.location;
    document.getElementById('displayContactPhone').innerText = contactInfo.phone;
    document.getElementById('displayContactEmail').innerText = contactInfo.email;
    document.getElementById('displayContactHours').innerText = contactInfo.hours;
    document.getElementById('staffEditLocation').value = contactInfo.location;
    document.getElementById('staffEditPhone').value = contactInfo.phone;
    document.getElementById('staffEditEmail').value = contactInfo.email;
    document.getElementById('staffEditHours').value = contactInfo.hours;
}

function handleSaveContactInfo(e) {
    e.preventDefault();
    contactInfo.location = document.getElementById('staffEditLocation').value.trim();
    contactInfo.phone = document.getElementById('staffEditPhone').value.trim();
    contactInfo.email = document.getElementById('staffEditEmail').value.trim();
    contactInfo.hours = document.getElementById('staffEditHours').value.trim();
    saveState();
    renderContactUsUI();
    alert(" Contact Information updated successfully!");
}

// Ipinapakita sa customer checkout: uploaded na tunay na QR kapag meron,
// default QR muna kapag wala pa.
function storeGcashQrSrc() {
    return (storeGcash && storeGcash.qrDataUrl) || defaultStoreGcashQr;
}
function renderStoreGcash() {
    const img = document.getElementById('gcashQRImg'); if (img) img.src = storeGcashQrSrc();
    const a = document.getElementById('gcashAccountText'); if (a) a.innerText = storeGcash.accountName || '';
    const n = document.getElementById('gcashNumberText'); if (n) n.innerText = storeGcash.number || '';
    const p = document.getElementById('staffGcashQrPreview'); if (p) p.src = storeGcashQrSrc();
    setGcashQrStatus();
    const ni = document.getElementById('staffEditGcashName'); if (ni && document.activeElement !== ni) ni.value = storeGcash.accountName || '';
    const nu = document.getElementById('staffEditGcashNumber'); if (nu && document.activeElement !== nu) nu.value = storeGcash.number || '';
}
function setGcashQrStatus() {
    const el = document.getElementById('gcashQrStatus');
    if (!el) return;
    if (storeGcash && storeGcash.qrDataUrl) {
        const kb = Math.round(storeGcash.qrDataUrl.length / 1024);
        el.textContent = 'Saved QR ✓ (' + kb + ' KB) — ito ang makikita ni customer sa checkout.';
        el.style.color = '#2a9d8f';
    } else {
        el.textContent = 'Wala pang custom QR — default muna ang ipinapakita sa checkout.';
        el.style.color = '#888';
    }
}
function handleSaveGcashSettings(e) {
    e.preventDefault();
    const finish = () => {
        try { saveState(); } catch (err) {
            alert('Hindi na-save: puno na ang storage ng browser. Mas maliit na QR image ang gamitin.');
            return;
        }
        renderStoreGcash();
        renderStaffTables();
        alert(" Store GCash settings saved! Makikita na ito ni customer sa checkout.");
    };
    storeGcash.accountName = document.getElementById('staffEditGcashName').value.trim();
    storeGcash.number = document.getElementById('staffEditGcashNumber').value.trim();
    const f = document.getElementById('staffEditGcashQR').files[0];
    if (f) {
        const r = new FileReader();
        r.onload = () => {
            // Liitan ang malaking photo (hal. galing camera) para siguradong kasya sa storage
            // at mabilis mag-load sa checkout — QR scannable pa rin ito.
            const img = new Image();
            img.onload = () => {
                try {
                    const MAX = 800;
                    const sc = Math.min(1, MAX / Math.max(img.naturalWidth || 1, img.naturalHeight || 1));
                    if (sc < 1 || String(r.result).length > 500000) {
                        const cv = document.createElement('canvas');
                        cv.width = Math.max(1, Math.round((img.naturalWidth || 1) * sc));
                        cv.height = Math.max(1, Math.round((img.naturalHeight || 1) * sc));
                        cv.getContext('2d').drawImage(img, 0, 0, cv.width, cv.height);
                        storeGcash.qrDataUrl = cv.toDataURL('image/jpeg', 0.85);
                    } else {
                        storeGcash.qrDataUrl = r.result;
                    }
                } catch (err) { storeGcash.qrDataUrl = r.result; }
                document.getElementById('staffEditGcashQR').value = '';
                finish();
            };
            img.onerror = () => { alert('Hindi mabasa ang image file. Ibang file ang subukan.'); };
            img.src = r.result;
        };
        r.onerror = () => { alert('Hindi nabasa ang QR image. Subukan ulit.'); };
        r.readAsDataURL(f);
    } else {
        finish();
    }
}

/* --- OTP & SIGN UP ENFORCEMENT --- */
function checkEmailOTPState() {
    const emailInput = document.getElementById('regEmail');
    const btnGetOTP = document.getElementById('btnGetOTP');
    const emailPattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    if (emailPattern.test(emailInput.value.trim())) {
        btnGetOTP.disabled = false;
        btnGetOTP.style.background = "var(--primary-color)";
        btnGetOTP.style.cursor = "pointer";
    } else {
        btnGetOTP.disabled = true;
        btnGetOTP.style.background = "#888";
        btnGetOTP.style.cursor = "not-allowed";
    }
}

function handleGetOTP() {
    const email = document.getElementById('regEmail').value.trim();
    const firstName = document.getElementById('regFirstName').value.trim() || 'Customer';
    const btnGetOTP = document.getElementById('btnGetOTP');
    if (!email) {
        alert("Please enter a valid email address!");
        return;
    }
    generatedOTP = Math.floor(100000 + Math.random() * 900000).toString();
    isOTPVerified = false;
    btnGetOTP.disabled = true;
    btnGetOTP.innerText = "Sending...";
    btnGetOTP.style.background = "#888";

    const templateParams = {
        to_email: email,
        to_name: firstName,
        otp_code: generatedOTP
    };

    if (typeof emailjs === 'undefined') {
        document.getElementById('otpGroup').style.display = 'block';
        document.getElementById('otpStatusText').innerText = 'Demo mode (email service unavailable). Your OTP code is: ' + generatedOTP;
        document.getElementById('otpStatusText').style.color = '#0c5460';
        btnGetOTP.innerText = 'Resend OTP';
        btnGetOTP.disabled = false;
        btnGetOTP.style.background = 'var(--primary-color)';
        alert('Demo mode (email service unavailable). Your OTP code is: ' + generatedOTP);
        return;
    }
    emailjs.send(EMAILJS_SERVICE_ID, EMAILJS_TEMPLATE_ID, templateParams)
        .then(function(response) {
            document.getElementById('otpGroup').style.display = 'block';
            document.getElementById('otpStatusText').innerText = `Real OTP code sent to ${email}! Please check your Inbox / Spam folder.`;
            document.getElementById('otpStatusText').style.color = '#0c5460';
            btnGetOTP.innerText = "Resend OTP";
            btnGetOTP.disabled = false;
            btnGetOTP.style.background = "var(--primary-color)";
            alert(`Success! A real OTP verification code has been sent to ${email}. Please check your email inbox or spam folder.`);
        }, function(error) {
            btnGetOTP.innerText = "Get OTP";
            btnGetOTP.disabled = false;
            btnGetOTP.style.background = "var(--primary-color)";
            alert('Email send failed — demo fallback. Your OTP code is: ' + generatedOTP + '. Please enter it to continue.');
            document.getElementById('otpGroup').style.display = 'block';
            document.getElementById('otpStatusText').innerText = 'Demo fallback: your OTP code is ' + generatedOTP;
            document.getElementById('otpStatusText').style.color = '#0c5460';
            btnGetOTP.innerText = 'Resend OTP';
            btnGetOTP.disabled = false;
            btnGetOTP.style.background = 'var(--primary-color)';
        });
}

function verifyEnteredOTP() {
    const inputOTP = document.getElementById('regOTPInput').value.trim();
    const statusText = document.getElementById('otpStatusText');
    const btnReg = document.getElementById('btnRegisterSubmit');
    if (inputOTP === generatedOTP && generatedOTP !== null) {
        isOTPVerified = true;
        statusText.innerText = "OTP Code Verified Successfully!";
        statusText.style.color = "#2e7d32";
        btnReg.disabled = false;
        btnReg.style.backgroundColor = "var(--primary-color)";
        btnReg.style.cursor = "pointer";
        alert(" Email verified! You can now proceed to complete registration.");
    } else {
        isOTPVerified = false;
        statusText.innerText = " Invalid OTP Code. Please check and try again.";
        statusText.style.color = "#e63946";
        btnReg.disabled = true;
        btnReg.style.backgroundColor = "#888";
        btnReg.style.cursor = "not-allowed";
    }
}

/* --- AUTH & HAMBURGER DRAWER --- */
function toggleHamburgerDrawer() {
    const drawer = document.getElementById('hamburgerDrawer');
    drawer.classList.toggle('open');
    if (drawer.classList.contains('open')) {
        clearTimeout(window.__closeT);
        overlay.classList.add('active');
        lockBodyScroll(true);
        fitDrawersAboveFooter();
        scheduleDrawerRefit();
    } else {
        scheduleDrawerCleanup();
    }
}

// Pagkasara: hintaying dumulas palabas ang drawer/cart (0.3s) bago alisin
// ang dim at scroll-lock — kung hindi, kita ang puting panel na dumudulas
// sa maliwanag na page.
function scheduleDrawerCleanup() {
    clearTimeout(window.__closeT);
    window.__closeT = setTimeout(() => {
        const anyDrawer = document.getElementById('hamburgerDrawer').classList.contains('open') ||
                          document.getElementById('cartDrawer').classList.contains('open');
        const anyModal = !!document.querySelector('.custom-modal.active');
        if (!anyDrawer && !anyModal) overlay.classList.remove('active');
        if (!anyDrawer) lockBodyScroll(false);
        fitDrawersAboveFooter();
    }, 320);
}

// Ang cart ay hindi na dumudulas sa ilalim ng footer: pinapaikli mismo ang
// drawer (bottom = taas ng footer) kaya laging kita ang Total + Checkout at
// hindi sila natatakpan ng brown footer. Walang footer = full height.
function fitDrawersAboveFooter() {
    const hd = document.getElementById('hamburgerDrawer');
    const cd = document.getElementById('cartDrawer');
    const anyOpen = (hd && hd.classList.contains('open')) || (cd && cd.classList.contains('open'));
    try { document.body.classList.toggle('drawer-open', anyOpen); } catch (e) {}
    let gap = 0;
    if (cd && cd.classList.contains('open')) {
        const f = document.querySelector('.site-footer');
        if (f) {
            const r = f.getBoundingClientRect();
            gap = Math.max(0, Math.round(window.innerHeight - r.top));
            if (gap > 0) gap = Math.min(gap, window.innerHeight - 160); // laging may matirang panel
        }
    }
    if (cd) {
        cd.style.bottom = gap + 'px';
        const cf = cd.querySelector('.cart-footer');
        if (cf) cf.style.marginBottom = '';
    }
}

// Late layout shift (images/fonts na late nag-load) — ulitin ang fit ilang beses
// pagkatapos buksan ang drawer, para hindi maiwang mali ang sukat nito.
function scheduleDrawerRefit() {
    const token = (window.__refitToken = (window.__refitToken || 0) + 1);
    [400, 1200, 2500].forEach(ms => setTimeout(() => {
        try {
            if (token !== window.__refitToken) return;
            const anyDrawer = document.getElementById('hamburgerDrawer').classList.contains('open') ||
                              document.getElementById('cartDrawer').classList.contains('open');
            if (anyDrawer) fitDrawersAboveFooter();
        } catch (e) {}
    }, ms));
}

function closeAllDrawersAndModals() {
    document.getElementById('hamburgerDrawer').classList.remove('open');
    document.getElementById('cartDrawer').classList.remove('open');
    document.querySelectorAll('.custom-modal').forEach(m => m.classList.remove('active'));
    resetAllPasswordToggles();
    scheduleDrawerCleanup();
}

// Habang bukas ang drawer/cart, hindi dapat gumalaw ang page sa likod —
// kung hindi, nadadala ang footer sa ilalim ng puting panel.
// position:fixed ang ginagamit (hindi lang overflow:hidden) para totoong
// naka-freeze pati sa mobile touch-scroll, at naibabalik ang dating scroll.
let __scrollLocked = false;
let __lockedY = 0;
function lockBodyScroll(lock) {
    try {
        if (lock) {
            if (__scrollLocked) return;
            __scrollLocked = true;
            __lockedY = window.scrollY || 0;
            document.body.style.position = 'fixed';
            document.body.style.top = (-__lockedY) + 'px';
            document.body.style.width = '100%';
            document.body.style.overflow = 'hidden';
            document.documentElement.style.overflow = 'hidden';
        } else {
            const y = __lockedY || 0;
            __scrollLocked = false;
            document.body.style.position = '';
            document.body.style.top = '';
            document.body.style.width = '';
            document.body.style.overflow = '';
            document.documentElement.style.overflow = '';
            window.scrollTo(0, y);
        }
    } catch (e) {}
}

// Feature 3 & 4: Render Profile Info and Cart Visibility based on role & view
function renderAuthHeaderUI() {
    const authContainer = document.getElementById('authNavContainer');
    const cartBtn = document.getElementById('cartBtn');

    const isStaffActive = staffView && staffView.classList.contains('active');
    const isAdminUser = currentUser && (currentUser.role === 'staff' || currentUser.role === 'admin');

    if (cartBtn) {
        cartBtn.style.display = (isStaffActive || isAdminUser || !currentUser) ? 'none' : 'block';
    }

    if (authContainer) {
        if (currentUser) {
            authContainer.innerHTML = `<button class="user-account-btn" onclick="openProfileModal()">👤 ${currentUser.firstName}</button>`;
        } else {
            authContainer.innerHTML = `<button class="signin-btn" onclick="openModal('signInModal')">Sign In</button>`;
        }
    }
}

function renderHamburgerMenu() {
    const menuList = document.getElementById('hamburgerMenuList');
    if (!menuList) return;
    if (currentUser && (currentUser.role === 'staff' || currentUser.role === 'admin')) {
        menuList.innerHTML = `
            <button class="drawer-menu-btn" onclick="closeAllDrawersAndModals(); switchView('staff');">📊 Staff Dashboard</button>
            <button class="drawer-menu-btn" onclick="closeAllDrawersAndModals(); switchView('reviews');">⭐ Customer Reviews</button>
            <button class="drawer-menu-btn" style="color: var(--accent-color);" onclick="handleLogout()">🚪 Logout (Admin)</button>`;
    } else if (!currentUser) {
        menuList.innerHTML = `
            <button class="drawer-menu-btn" onclick="closeAllDrawersAndModals(); openModal('signInModal');">🔑 Login or Signup</button>
            <button class="drawer-menu-btn" onclick="closeAllDrawersAndModals(); switchView('reviews');">⭐ Customer Reviews</button>
            <button class="drawer-menu-btn" onclick="closeAllDrawersAndModals(); switchView('about');">📖 About Us (Our Story)</button>`;
    } else {
        menuList.innerHTML = `
            <button class="drawer-menu-btn" onclick="closeAllDrawersAndModals(); switchView('customer');">🏠 Home (Menu Page)</button>
            <button class="drawer-menu-btn" onclick="closeAllDrawersAndModals(); switchView('track');">🛵 Order Tracking & History</button>
            <button class="drawer-menu-btn" onclick="closeAllDrawersAndModals(); switchView('reviews');">⭐ Customer Reviews</button>
            <button class="drawer-menu-btn" style="color: var(--accent-color);" onclick="handleLogout()">👋 Logout (${currentUser.firstName})</button>`;
    }
}

function openModal(id) {
    closeAllDrawersAndModals();
    if (id === 'signInModal') {
        const form = document.getElementById('signInForm');
        if (form) form.reset();
        resetAllPasswordToggles();
        hideLoginError();
    }
    document.getElementById(id).classList.add('active');
    overlay.classList.add('active');
}

function closeModal(id) {
    document.getElementById(id).classList.remove('active');
    overlay.classList.remove('active');
}

function switchToSignUp() {
    closeModal('signInModal');
    generatedOTP = null;
    isOTPVerified = false;
    document.getElementById('otpGroup').style.display = 'none';
    document.getElementById('regOTPInput').value = "";
    document.getElementById('btnGetOTP').disabled = true;
    document.getElementById('btnRegisterSubmit').disabled = true;
    document.getElementById('btnRegisterSubmit').style.backgroundColor = "#888";
    document.getElementById('btnRegisterSubmit').style.cursor = "not-allowed";
    updateConfirmMatchText();
    openModal('signUpModal');
}

function validateSignUpPassword() {
    const p = document.getElementById('regPassword').value;
    const hasLower = /[a-z]/.test(p);
    const hasUpper = /[A-Z]/.test(p);
    const hasNum = /[0-9]/.test(p);
    const hasSpecial = /[!@#$%^&*(),.?":{}|<>]/.test(p);
    const hasLen = p.length >= 8;
    const noSpace = !/\s/.test(p);

    toggleCheck('chk-lower', hasLower);
    toggleCheck('chk-upper', hasUpper);
    toggleCheck('chk-num', hasNum);
    toggleCheck('chk-special', hasSpecial);
    toggleCheck('chk-len', hasLen);
    toggleCheck('chk-space', noSpace && p.length > 0);
    updateConfirmMatchText();

    return hasLower && hasUpper && hasNum && hasSpecial && hasLen && noSpace;
}

function toggleCheck(elementId, isValid) {
    const el = document.getElementById(elementId);
    if (isValid) el.classList.add('valid');
    else el.classList.remove('valid');
}

// Login page: inline error text kapag mali ang password (imbes na popup lang).
function showLoginError(msg) {
    const el = document.getElementById('loginErrorText');
    if (!el) return;
    el.textContent = msg;
    el.hidden = false;
}
function hideLoginError() {
    const el = document.getElementById('loginErrorText');
    if (el) el.hidden = true;
}

// Sign up: live "passwords match" indicator sa re-enter password.
function updateConfirmMatchText() {
    const p = document.getElementById('regPassword').value;
    const c = document.getElementById('regConfirmPassword').value;
    const el = document.getElementById('confirmMatchText');
    if (!el) return;
    if (!c) { el.hidden = true; return; }
    el.hidden = false;
    if (p === c) {
        el.textContent = 'Passwords match ✓';
        el.style.color = '#2a9d8f';
    } else {
        el.textContent = 'Passwords do not match ✗';
        el.style.color = '#e63946';
    }
}

document.getElementById('signUpForm').addEventListener('submit', function(e) {
    e.preventDefault();
    if (!isOTPVerified) {
        alert("Please verify your OTP code before registering!");
        return;
    }
    const firstName = document.getElementById('regFirstName').value.trim();
    const lastName = document.getElementById('regLastName').value.trim();
    const phone = document.getElementById('regPhone').value.trim();
    const email = document.getElementById('regEmail').value.trim();
    const password = document.getElementById('regPassword').value;
    const confirmPassword = document.getElementById('regConfirmPassword').value;

    if (!validateSignUpPassword()) {
        alert(" Password does not meet security requirements!");
        return;
    }
    if (password !== confirmPassword) {
        alert("Passwords do not match!");
        return;
    }
    if (registeredUsers.some(u => u.email.toLowerCase() === email.toLowerCase())) {
        alert(" Account with this email already exists!");
        return;
    }

    const newUser = { firstName, lastName, phone, email, password, address: '', role: 'customer', createdAt: Date.now() };
    registeredUsers.push(newUser);
    currentUser = newUser;
    rememberMe = true; // bagong register: manatiling naka-login
    saveState();
    alert(" Account created successfully!");
    closeModal('signUpModal');
    renderAuthHeaderUI();
    renderHamburgerMenu();
    switchView('customer');
});

document.getElementById('signInForm').addEventListener('submit', function(e) {
    e.preventDefault();
    hideLoginError();
    const email = document.getElementById('loginEmail').value.trim().toLowerCase();
    const password = document.getElementById('loginPassword').value;

    if (email === "crustandcrumbs2026@gmail.com") {
        if (password === "admin123" || password === "crustandcrumbs2026") {
            currentUser = { firstName: "Admin", lastName: "Staff", email: email, role: "staff" };
            rememberMe = true;
            saveState();
            alert("Welcome back, Admin! Pumasok na sa Dashboard.");
            switchView('staff');
            closeModal('signInModal');
            renderAuthHeaderUI();
            renderHamburgerMenu();
            return;
        } else {
            showLoginError("Wrong password. Please try again.");
            return;
        }
    }

    const user = registeredUsers.find(u => u.email.toLowerCase() === email && u.password === password);
    if (user) {
        currentUser = user;
        const _rm = document.getElementById('loginRemember');
        rememberMe = _rm ? _rm.checked : true;
        saveState();
        alert(`Welcome back, ${user.firstName}!`);
        switchView('customer');
        closeModal('signInModal');
        renderAuthHeaderUI();
        renderHamburgerMenu();
    } else {
        showLoginError("Invalid email or password. Please try again.");
    }
});

function handleLogout() {
    const _email = currentUser ? (currentUser.email || '') : '';
    currentUser = null;
    rememberMe = false;
    cart = [];
    try {
        // burahin lang ang remembered kung sa nag-logout galing (huwag idamay ang kabilang tab)
        const mine = getRememberedEmail();
        if (mine && _email && mine.toLowerCase() === _email.toLowerCase()) {
            localStorage.removeItem('cnc_remembered_user');
            localStorage.removeItem('cnc_remember');
        }
    } catch (e) {}
    saveState();
    alert("You have logged out.");
    closeAllDrawersAndModals();
    renderAuthHeaderUI();
    renderHamburgerMenu();
    switchView('customer');
}

// Feature 3: Full customer profile details in profile modal
function openProfileModal() {
    if (!currentUser) return;
    document.getElementById('profFullName').value = `${currentUser.firstName || ''} ${currentUser.lastName || ''}`.trim();
    document.getElementById('profPhone').value = currentUser.phone || '';
    document.getElementById('profEmail').value = currentUser.email || '';

    document.getElementById('profAddress').value = currentUser.address || '';
    openModal('profileModal');
}

document.getElementById('profileUpdateForm').addEventListener('submit', function(e) {
    e.preventDefault();
    if (!currentUser) return;
    const nameParts = document.getElementById('profFullName').value.trim().split(' ');
    currentUser.firstName = nameParts[0] || currentUser.firstName;
    currentUser.lastName = nameParts.slice(1).join(' ') || currentUser.lastName;
    currentUser.phone = document.getElementById('profPhone').value.trim();
    currentUser.email = document.getElementById('profEmail').value.trim();
    currentUser.address = document.getElementById('profAddress').value.trim();
    saveState();
    alert(" Account updated!");
    closeModal('profileModal');
    renderAuthHeaderUI();
    renderHamburgerMenu();
});

function handleChangeProfilePassword() {
    const oldP = prompt("Enter Current Password:");
    if (oldP !== currentUser.password) {
        alert(" Incorrect password!");
        return;
    }
    const newP = prompt("Enter New Password:");
    if (newP && newP.length >= 8) {
        currentUser.password = newP;
        saveState();
        alert(" Password changed successfully!");
    } else {
        alert(" Invalid password length.");
    }
}

function handleResetPassword() {
    const email = prompt("Enter your registered email address:");
    if (!email) return;
    const user = registeredUsers.find(u => u.email.toLowerCase() === email.trim().toLowerCase());
    if (user) {
        const newP = prompt("Account verified! Enter new password:");
        if (newP && newP.length >= 8) {
            user.password = newP;
            saveState();
            alert(" Password reset successfully!");
        } else {
            alert(" Invalid password.");
        }
    } else {
        alert(" Email not found!");
    }
}

/* --- PRODUCTS & CUSTOMIZATION --- */
function getTopSellingProductName() {
    let salesMap = {};
    const allOrders = orders.concat(orderHistory);
    allOrders.forEach(o => {
        if (o.rawItemsList && Array.isArray(o.rawItemsList)) {
            o.rawItemsList.forEach(item => {
                salesMap[item.name] = (salesMap[item.name] || 0) + item.qty;
            });
        }
    });
    let maxQty = 0;
    let topProduct = null;
    for (let name in salesMap) {
        if (salesMap[name] > maxQty) {
            maxQty = salesMap[name];
            topProduct = name;
        }
    }
    return topProduct;
}

function applyProductFilters() {
    const query = (document.getElementById('searchInput')?.value || '').toLowerCase().trim();
    let items = products.filter(p => {
        const matchesCategory = activeCategory === 'all' || p.category === activeCategory;
        const matchesQuery = !query || p.name.toLowerCase().includes(query) || (p.desc || '').toLowerCase().includes(query);
        return matchesCategory && matchesQuery;
    });
    renderProducts(items);
}

function renderProducts(items) {
    productsGrid.innerHTML = '';
    const topSellerName = getTopSellingProductName();
    items.forEach(p => {
        const isBestSeller = (p.name === topSellerName);
        const isOutOfStock = p.stock <= 0;
        const card = document.createElement('div');
        card.className = 'product-card';
        card.innerHTML = `
            ${isBestSeller ? '<span class="best-seller-tag">★ Best Seller</span>' : ''}
            ${isOutOfStock ? '<span class="out-of-stock-badge">Out of Stock</span>' : ''}
            <img src="${p.image}" alt="${esc(p.name)}" class="product-img" style="${isOutOfStock ? 'filter: grayscale(80%);' : ''}" onerror="this.onerror=null;this.src='https://images.unsplash.com/photo-1555507036-ab1f4038808a?auto=format&fit=crop&w=500&q=80'">
            <div class="product-info">
                <h3 class="product-title">${p.name}</h3>
                <p class="product-desc">${p.desc}</p>
                <div class="product-price-row">
                    <span class="product-price">P${p.price.toFixed(2)}</span>
                    <button class="add-to-cart-btn" ${isOutOfStock ? 'disabled style="background:#888;cursor:not-allowed;"' : ''} onclick="openCustomizationModal(${p.id})">
                        ${isOutOfStock ? '🚫 Out of Stock' : '🛒 Add & Customize'}
                    </button>
                </div>
            </div>`;
        productsGrid.appendChild(card);
    });
}

function openCustomizationModal(productId) {
    if (!currentUser) {
        alert("Please sign in / login first before adding to cart!");
        openModal('signInModal');
        return;
    }
    currentlyCustomizingProduct = products.find(p => p.id === productId);
    if (!currentlyCustomizingProduct) return;
    if (currentlyCustomizingProduct.stock <= 0) {
        alert("Paumanhin, ang produktong ito ay Out of Stock na.");
        return;
    }
    document.getElementById('customModalItemTitle').innerText = `Customize: ${currentlyCustomizingProduct.name}`;
    document.querySelectorAll('input[name="dedicationType"]')[0].checked = true;
    document.getElementById('dedicationMessage').value = '';
    document.getElementById('dedicationTextGroup').style.display = 'none';
    populateAddonSelect('selSyrup', customOptions.syrups);
    populateAddonSelect('selFruit', customOptions.fruits);
    populateAddonSelect('selIceCream', customOptions.icecream);
    populateAddonSelect('selNuts', customOptions.nuts);
    populateAddonSelect('selCrunch', customOptions.crunchies);
    ['chkSyrup', 'chkFruit', 'chkIceCream', 'chkNuts', 'chkCrunch'].forEach(id => {
        document.getElementById(id).checked = false;
    });
    ['selSyrup', 'selFruit', 'selIceCream', 'selNuts', 'selCrunch'].forEach(id => {
        document.getElementById(id).disabled = true;
    });
    selectedNumberCandles = [];
    renderNumberCandlesList();
    document.getElementById('candleNumVal').value = '0';
    document.getElementById('spiralCandleQty').innerText = '0';
    document.getElementById('sparklerCandleQty').innerText = '0';
    document.getElementById('allergyNotes').value = '';
    openModal('customizationModal');
}

function populateAddonSelect(selectId, optionsList) {
    const sel = document.getElementById(selectId);
    sel.innerHTML = '';
    optionsList.filter(opt => opt.isAvailable !== false).forEach(opt => {
        const optionEl = document.createElement('option');
        optionEl.value = opt.name;
        optionEl.dataset.price = opt.price;
        optionEl.innerText = `${opt.name} (+P${opt.price.toFixed(2)})`;
        sel.appendChild(optionEl);
    });
}

function toggleDedicationTextarea() {
    const val = document.querySelector('input[name="dedicationType"]:checked').value;
    document.getElementById('dedicationTextGroup').style.display = (val === 'none') ? 'none' : 'block';
}

function toggleAddonSelect(chkId, selId) {
    const chk = document.getElementById(chkId);
    document.getElementById(selId).disabled = !chk.checked;
}

function addNumberCandleSelection() {
    const num = document.getElementById('candleNumVal').value;
    selectedNumberCandles.push(num);
    renderNumberCandlesList();
}

function removeNumberCandleSelection(idx) {
    selectedNumberCandles.splice(idx, 1);
    renderNumberCandlesList();
}

function renderNumberCandlesList() {
    const box = document.getElementById('numberCandlesList');
    box.innerHTML = '';
    selectedNumberCandles.forEach((num, idx) => {
        box.innerHTML += `
            <span style="background: #e0e0e0; padding: 2px 8px; border-radius: 12px; font-size: 0.8rem; margin-right: 4px; display: inline-flex; align-items: center; gap: 4px;">
                #${num} (+15.00) <b style="cursor:pointer; color:red;" onclick="removeNumberCandleSelection(${idx})">&times;</b>
            </span>`;
    });
}

function changeCandleQty(elementId, delta) {
    const el = document.getElementById(elementId);
    let current = parseInt(el.innerText) || 0;
    current = Math.max(0, current + delta);
    el.innerText = current;
}

function confirmAddToCartCustomized() {
    if (!currentUser) {
        alert("Please sign in / login first before adding to cart!");
        closeModal('customizationModal');
        openModal('signInModal');
        return;
    }
    if (!currentlyCustomizingProduct) return;
    let extraTotal = 0;
    let details = [];
    const dedType = document.querySelector('input[name="dedicationType"]:checked').value;
    const dedMsg = document.getElementById('dedicationMessage').value.trim();
    if (dedType === 'plate') {
        details.push(`Plate Message: "${dedMsg || 'Happy Celebration'}"`);
    } else if (dedType === 'card') {
        extraTotal += 15;
        details.push(`Greeting Card (+P15): "${dedMsg || 'Best Wishes'}"`);
    }

    const addons = [
        { chk: 'chkSyrup', sel: 'selSyrup', label: 'Syrup' },
        { chk: 'chkFruit', sel: 'selFruit', label: 'Fruit' },
        { chk: 'chkIceCream', sel: 'selIceCream', label: 'Ice Cream' },
        { chk: 'chkNuts', sel: 'selNuts', label: 'Nuts' },
        { chk: 'chkCrunch', sel: 'selCrunch', label: 'Topping' }
    ];

    addons.forEach(a => {
        if (document.getElementById(a.chk).checked) {
            const selEl = document.getElementById(a.sel);
            const optName = selEl.value;
            const optPrice = parseFloat(selEl.options[selEl.selectedIndex].dataset.price) || 0;
            extraTotal += optPrice;
            details.push(`${a.label}: ${optName} (+P${optPrice.toFixed(2)})`);
        }
    });

    if (selectedNumberCandles.length > 0) {
        const cost = selectedNumberCandles.length * 15;
        extraTotal += cost;
        details.push(`Number Candles: #${selectedNumberCandles.join(', #')} (+P${cost.toFixed(2)})`);
    }

    const spiralQty = parseInt(document.getElementById('spiralCandleQty').innerText) || 0;
    if (spiralQty > 0) {
        const cost = spiralQty * 5;
        extraTotal += cost;
        details.push(`${spiralQty}x Spiral Candle (+P${cost.toFixed(2)})`);
    }

    const sparklerQty = parseInt(document.getElementById('sparklerCandleQty').innerText) || 0;
    if (sparklerQty > 0) {
        const cost = sparklerQty * 30;
        extraTotal += cost;
        details.push(`${sparklerQty}x Sparkler Candle (+P${cost.toFixed(2)})`);
    }

    const allergy = document.getElementById('allergyNotes').value.trim();
    if (allergy) details.push(`⚠️ Note: ${allergy}`);

    const finalUnitPrice = currentlyCustomizingProduct.price + extraTotal;
    const cartItem = {
        id: Date.now(),
        productId: currentlyCustomizingProduct.id,
        name: currentlyCustomizingProduct.name,
        unitPrice: finalUnitPrice,
        qty: 1,
        details: details
    };

    cart.push(cartItem);
    updateCart();
    closeModal('customizationModal');
    // I-reset ang candle/dedication state pagkatapos mag-add, para walang maiwan
    // sa susunod na bukas ng modal (dropdown man o listahan).
    selectedNumberCandles = [];
    try {
        renderNumberCandlesList();
        document.getElementById('candleNumVal').value = '0';
        document.getElementById('spiralCandleQty').innerText = '0';
        document.getElementById('sparklerCandleQty').innerText = '0';
    } catch (e) {}
    document.getElementById('cartDrawer').classList.add('open');
    overlay.classList.add('active');
    lockBodyScroll(true);
    fitDrawersAboveFooter();
    scheduleDrawerRefit();
}

/* --- CART & CHECKOUT --- */
function updateCart() {
    cartItemsContainer.innerHTML = '';
    if (cart.length === 0) {
        cartItemsContainer.innerHTML = `<div style="text-align:center;color:#999;padding:2.5rem 1rem;font-size:0.95rem;">🛒<br><br><b>Your cart is empty.</b><br><span style="font-size:0.85rem;">Add cakes, pastries, or breads from the menu below.</span></div>`;
    }
    let total = 0;
    let itemCount = 0;
    cart.forEach((item, index) => {
        const itemTotal = item.unitPrice * item.qty;
        total += itemTotal;
        itemCount += item.qty;
        const itemEl = document.createElement('div');
        itemEl.className = 'cart-item';
        itemEl.innerHTML = `
            <div style="flex: 1; min-width: 0;">
                <div style="font-weight: bold; font-size: 0.95rem;">${item.name}</div>
                <div style="font-size: 0.8rem; color: #666;">P${item.unitPrice.toFixed(2)} each</div>
                ${item.details.length > 0 ? `<div style="font-size: 0.75rem; color: #888; margin-top:2px; overflow-wrap: anywhere;">${item.details.join(' | ')}</div>` : ''}
                <div class="qty-controls">
                    <button class="qty-btn" onclick="changeCartQty(${index}, -1)">-</button>
                    <span style="font-size: 0.85rem; font-weight: bold;">${item.qty}</span>
                    <button class="qty-btn" onclick="changeCartQty(${index}, 1)">+</button>
                </div>
            </div>
            <div style="text-align: right; flex-shrink: 0;">
                <div style="font-weight: bold; font-size: 0.95rem; color: var(--primary-color);">P${itemTotal.toFixed(2)}</div>
                <button class="delete-btn" style="padding: 2px 6px; font-size: 0.7rem; margin-top: 4px;" onclick="removeFromCart(${index})">X</button>
            </div>`;
        cartItemsContainer.appendChild(itemEl);
    });
    cartTotalDisplay.innerText = `P${total.toFixed(2)}`;
    cartBadge.innerText = itemCount;
    const fabEl = document.getElementById('cartFab');
    if (fabEl) {
        const isStaffUser = currentUser && (currentUser.role === 'staff' || currentUser.role === 'admin');
        fabEl.hidden = !(customerView.classList.contains('active') && currentUser && !isStaffUser);
        const fb = document.getElementById('cartFabBadge');
        if (fb) fb.innerText = itemCount;
    }
}

function changeCartQty(index, delta) {
    cart[index].qty += delta;
    if (cart[index].qty <= 0) cart.splice(index, 1);
    updateCart();
}

function removeFromCart(index) {
    cart.splice(index, 1);
    updateCart();
}

function checkout() {
    if (cart.length === 0) {
        alert("Your cart is empty!");
        return;
    }
    if (!currentUser) {
        alert("You need to login first!");
        openModal('signInModal');
        return;
    }
    document.getElementById('checkoutDetailsForm').reset();
    document.getElementById('custName').value = `${currentUser.firstName || ''} ${currentUser.lastName || ''}`.trim();
    document.getElementById('activeMobileNo').value = currentUser.phone || '';
    document.getElementById('deliveryAddress').value = currentUser.address || '';
    document.getElementById('orderType').value = 'delivery';
    toggleOrderTypeFields();
    document.getElementById('gcashFields').style.display = 'none';
    closeAllDrawersAndModals();
    openModal('checkoutModal');
}

function togglePaymentFields() {
    const method = document.getElementById('paymentMethod').value;
    document.getElementById('gcashFields').style.display = (method.includes('GCash')) ? 'block' : 'none';
    if (method.includes('GCash')) renderStoreGcash();
}

function toggleOrderTypeFields() {
    // Delivery-only store: no order-type choice. Kept so old calls don't break.
    const sel = document.getElementById('orderType');
    if (sel) sel.value = 'delivery';
    const df = document.getElementById('deliveryFields'); if (df) df.style.display = 'block';
    const dn = document.getElementById('deliveryNotesGroup'); if (dn) dn.style.display = 'block';
    const da = document.getElementById('deliveryAddress'); if (da) da.required = true;
    const pay = document.getElementById('paymentMethod');
    if (pay) pay.innerHTML = '<option value="Cash on Delivery (COD)">Cash on Delivery (COD)</option><option value="GCash Online Payment">GCash Online Payment</option>';
    togglePaymentFields();
}

document.getElementById('checkoutDetailsForm').addEventListener('submit', function(e) {
    e.preventDefault();
    let total = cart.reduce((sum, i) => sum + (i.unitPrice * i.qty), 0);
    const custName = document.getElementById('custName').value.trim() || 'Guest';
    const orderType = 'delivery';
    const address = document.getElementById('deliveryAddress').value.trim();
    const mapLink = document.getElementById('googleMapLink').value.trim();
    const activeNo = document.getElementById('activeMobileNo').value.trim();
    const notes = document.getElementById('riderNotes').value.trim();
    const paymentMethod = document.getElementById('paymentMethod').value;

    let typeString = `Delivery | Addr: ${address}`;
    if (mapLink) typeString += ` | Map: ${mapLink}`;
    typeString += ` | Mobile: ${activeNo}`;
    if (notes) typeString += ` | Note: ${notes}`;

    let paymentString = paymentMethod;
    if (paymentMethod.includes('GCash')) {
        const gcashRef = document.getElementById('gcashRef').value.trim();
        if (!gcashRef) {
            alert(" Please enter GCash Reference Number!");
            return;
        }
        paymentString += ` Ref:${gcashRef}`;
    }

    const rawItemsList = cart.map(item => ({
        productId: item.productId,
        name: item.name,
        qty: item.qty,
        details: item.details,
        price: item.unitPrice
    }));

    const itemsSummary = cart.map(i => `${i.qty}x ${i.name}${i.details.length > 0 ? ' [' + i.details.join('; ') + ']' : ''}`).join(', ');
    const newOrderId = '#' + Math.floor(1000 + Math.random() * 9000);

    const newOrder = {
        id: newOrderId,
        custName,
        userEmail: currentUser ? currentUser.email : '',
        type: typeString,
        items: itemsSummary,
        rawItemsList,
        total,
        status: 'Received',
        payment: paymentString,
        timestamp: new Date().toLocaleString(),
        createdAt: Date.now(),
        gcashVerified: false
    };
    newOrder.etaMinutes = 45;
    newOrder.etaAt = Date.now() + 45*60*1000;

    orders.unshift(newOrder);

    cart.forEach(c => {
        const p = products.find(prod => prod.id === c.productId);
        if (p) p.stock = Math.max(0, p.stock - c.qty);
    });

    saveState();
    playDingSound();
    renderReceiptStub(newOrder);
    closeModal('checkoutModal');
    openModal('receiptModal');
    cart = [];
    updateCart();
    renderStaffTables();
    renderTVDisplay();
    renderProducts(products);
});

// Maayos na pagkakalista ng items: bawat pastry nasa sariling block at
// naka-bold, ang toppings/add-ons ay naka-indent sa ilalim nito (↳) para
// malinaw kung aling toppings kasama ng aling pastry. Gumagamit ng
// rawItemsList kapag meron (lahat ng bagong orders); fallback sa flat string.
function formatItemsHtml(order) {
    const list = (order && order.rawItemsList && Array.isArray(order.rawItemsList) && order.rawItemsList.length) ? order.rawItemsList : null;
    if (!list) return esc(order.items);
    return list.map(it => {
        const det = (it.details && it.details.length)
            ? it.details.map(d => `<div style="padding-left:1rem; color:#555;">↳ ${esc(d)}</div>`).join('')
            : '';
        return `<div style="margin-bottom:0.35rem;"><b>${it.qty}x ${esc(it.name)}</b>${det}</div>`;
    }).join('');
}
// Compact one-line version para sa maliliit na rows (history): "1x A (+3 add-ons), 1x B"
function formatItemsCompact(order) {
    const list = (order && order.rawItemsList && Array.isArray(order.rawItemsList) && order.rawItemsList.length) ? order.rawItemsList : null;
    if (!list) return esc(order.items);
    return esc(list.map(it => {
        const n = (it.details && it.details.length) || 0;
        return `${it.qty}x ${it.name}` + (n ? ` (+${n} add-on${n > 1 ? 's' : ''})` : '');
    }).join(', '));
}

function renderReceiptStub(order) {
    document.getElementById('receiptContent').innerHTML = `
        <div style="text-align: center; border-bottom: 1px dashed #ccc; padding-bottom: 0.5rem; margin-bottom: 0.5rem;">
            <h3>Crust & Crumbs Pastry Shop</h3>
            <p style="font-size: 0.75rem;">Official Digital Claim Stub</p>
        </div>
        <div class="receipt-number">${order.id}</div>
        <div style="text-align: left;">
        <p><strong>Customer:</strong> ${esc(order.custName)}</p>
        <p><strong>Order Type:</strong> ${esc(order.type)}</p>
        <p><strong>Date & Time:</strong> ${order.timestamp}</p>
        ${(isDeliveryOrder(order) && order.etaAt) ? `<p><strong>Estimated Delivery:</strong> ${new Date(order.etaAt).toLocaleTimeString([], {hour: 'numeric', minute: '2-digit'})} (~${order.etaMinutes} min from order time)</p>` : ''}
        <p>🚚 Please show this order number to the rider upon arrival.</p>
        <hr style="margin: 0.5rem 0; border: none; border-top: 1px dashed #ccc;">
        <p><strong>Items Ordered:</strong></p>
        <div style="font-size: 0.85rem; line-height: 1.7; margin-bottom: 0.5rem;">${formatItemsHtml(order)}</div>
        <hr style="margin: 0.5rem 0; border: none; border-top: 1px dashed #ccc;">
        </div>
        <p style="font-size: 1.1rem; text-align: right;"><strong>Total Paid: P${order.total.toFixed(2)}</strong></p>
        <p style="font-size: 0.8rem; text-align: right; color: #666;">Method: ${order.payment}</p>`;
}

/* --- STAFF OPERATIONS --- */
function renderStaffTables() {
    renderOrdersTable();
    renderInventoryTable();
    renderTopSellingList();
    renderKPICards();
}

// IISANG ORDER FLOW — dito nanggagaling ang steps ng admin dropdown,
// customer tracking, at Live Order Status board para tugma lahat.
// Huwag baguhin ang `value` (nakasave ito sa bawat order); `label` lang ang pwedeng palitan.
const ORDER_FLOW = [
    { value: 'Received', label: 'Received' },
    { value: 'Preparing', label: 'Preparing' },
    { value: 'Ready for Courier Pick-Up', label: 'Ready for Pick-Up' },
    { value: 'Handed To Delivery Rider', label: 'Handed to Rider' },
    { value: 'Completed', label: 'Order Received' }
];
function orderStepOptions(selected, maxSteps) {
    return ORDER_FLOW.slice(0, maxSteps).map((s, i) =>
        `<option value="${s.value}"${selected === s.value ? ' selected' : ''}>${i + 1}. ${s.label}</option>`
    ).join('');
}
function renderOrdersTable() {
    // Huwag i-re-render habang nagta-type sa ETA inputs; pero BUTTON/SELECT
    // clicks (GCash verify, status, ETA Set) dapat mag-update agad.
    const ae = document.activeElement;
    if (ae && (ae.tagName === 'INPUT' || ae.tagName === 'TEXTAREA') && ordersTableBody.contains(ae)) return;
    ordersTableBody.innerHTML = '';
    if (orders.length === 0) {
        ordersTableBody.innerHTML = '<tr><td colspan="7" style="text-align:center; color:#888; padding:1.5rem;">No active orders. New customer orders will appear here.</td></tr>';
        return;
    }
    orders.forEach((o, index) => {
        const isGCash = o.payment.includes('GCash');
        const row = document.createElement('tr');
        const receivedNote = o.customerReceived
            ? '<small style="color:#2a9d8f; font-size:0.72rem; font-weight:bold;">✔ Customer confirmed received</small>'
            : '';
        row.innerHTML = `
            <td><strong>${o.id}</strong><br><small style="color:#777;">${o.timestamp}</small><br><small><b>Cust:</b> ${esc(o.custName)}</small></td>
            <td>${formatItemsHtml(o)}<br><span style="font-size: 0.75rem; color: var(--primary-color); font-weight: bold;">${esc(o.type)}</span></td>
            <td><strong>P${o.total.toFixed(2)}</strong><br><small style="color:#666;">${esc(o.payment)}</small><br>${isGCash ? `<button class="gcash-verify-btn ${o.gcashVerified ? 'gcash-verified' : 'gcash-unverified'}" onclick="toggleGCashVerification(${index})">${o.gcashVerified ? '✔ Verified' : '⚠ Unverified'}</button>` : ''}</td>
            <td>
                <select class="status-select status-${o.status.replace(/\s+/g, '')}" onchange="updateOrderStatus(${index}, this.value)">
                    ${orderStepOptions(o.status, 4)}
                    <option value="Cancelled" ${o.status === 'Cancelled' ? 'selected' : ''}>Cancelled</option>
                </select><br>${receivedNote}
            </td>
            <td>${getEtaCellHtml(o, index)}</td>
            <td><button class="kot-btn" onclick="printKitchenSlip(${index})"> 🖨️ Print KOT</button></td>
            <td>${isDeliveryOrder(o) ? `<button class="kot-btn" onclick="openRiderDetails(${index})"> 🛵 Rider Details</button>` : `<span style="color:#999;font-size:0.8rem;" title="Delivery orders lang may rider">—</span>`}</td>`;
        ordersTableBody.appendChild(row);
    });
}

// Staff hanggang "Handed To Delivery Rider" lang. Ang paglipat sa Order History,
// si CUSTOMER lang ang gumagawa via "Order Received" (confirmOrderReceived) —
// ibig sabihin natanggap na niya ang order bago ito maging history ni admin.
function updateOrderStatus(index, newStatus) {
    if (!orders[index]) return;
    if (newStatus === 'Completed') {
        alert('Si customer lang ang pwedeng mag-Order Received. Hintayin ang confirmation niya.');
        renderOrdersTable();
        return;
    }
    orders[index].status = newStatus;
    if (newStatus === 'Cancelled' && !orders[index].customerReceived) {
        // Na-verify man o hindi: kapag cancelled bago ma-receive, walang benta.
        reverseSaleFromLedger(orders[index]);
    }
    saveState();
    renderStaffTables();
    renderTVDisplay();
}

function toggleGCashVerification(index) {
    const o = orders[index];
    if (!o) return;
    o.gcashVerified = !o.gcashVerified;
    if (o.gcashVerified) {
        // Bayad na via GCash: pasok agad sa Total Sales + GCash breakdown,
        // hindi na hihintayin ang Order Received ni customer.
        recordSaleToLedger(o);
    } else if (!o.customerReceived) {
        // Binawi ang verification bago pa ma-receive: ibalik sa ledger.
        reverseSaleFromLedger(o);
    }
    saveState();
    renderOrdersTable();
    renderKPICards();
}

function printKitchenSlip(index) {
    const o = orders[index];
    let kotModal = document.getElementById('kotSlipModal');
    if (!kotModal) {
        kotModal = document.createElement('div');
        kotModal.id = 'kotSlipModal';
        kotModal.className = 'custom-modal';
        kotModal.style.maxWidth = '450px';
        document.body.appendChild(kotModal);
    }
    kotModal.innerHTML = `
        <div class="modal-header">
            <h3> KITCHEN ORDER TICKET (KOT)</h3>
            <button class="close-modal-btn" onclick="closeModal('kotSlipModal')">&times;</button>
        </div>
        <div style="text-align: center; font-family: monospace;">
            <div style="font-size: 1.5rem; font-weight: bold; background: #e65100; color: white; padding: 6px; border-radius: 6px; margin-bottom: 1rem;">
                ${o.id}
            </div>
            <div style="text-align: left; font-size: 0.9rem; margin-bottom: 1rem; line-height: 1.5;">
                <p><b>Customer Name:</b> ${esc(o.custName)}</p>
                <p><b>Order Option:</b> ${esc(o.type)}</p>
                <p><b>Time:</b> ${o.timestamp}</p>
            </div>
            <hr style="border: none; border-top: 1px dashed #ccc; margin: 0.8rem 0;">
            <div style="text-align: left; font-size: 0.9rem; margin-bottom: 1rem;">
                ${formatItemsHtml(o)}
            </div>
            <hr style="border: none; border-top: 1px dashed #ccc; margin: 0.8rem 0;">
            <p style="font-size: 0.75rem; color: #666; margin-bottom: 1rem;">- ISSUSULAT SA PAPEL AT IIBIGAY SA KUSINA -</p>
        </div>
        <div style="display: flex; gap: 0.5rem; flex-wrap: wrap;" class="no-print">
            <button class="form-btn" onclick="window.print()" style="background-color: #333; flex: 1;">Print Ticket</button>
            <button class="form-btn" onclick="closeModal('kotSlipModal')" style="background-color: var(--primary-color); width: auto;">Close Window</button>
        </div>`;
    openModal('kotSlipModal');
}

function openRiderDetails(index) {
    const o = orders[index];
    let riderModal = document.getElementById('riderDetailsModal');
    if (!riderModal) {
        riderModal = document.createElement('div');
        riderModal.id = 'riderDetailsModal';
        riderModal.className = 'custom-modal';
        riderModal.style.maxWidth = '450px';
        document.body.appendChild(riderModal);
    }
    const currentRiderName = o.riderName || '';
    const currentRiderContact = o.riderContact || '';

    riderModal.innerHTML = `
        <div class="modal-header">
            <h3>ASSIGN DELIVERY RIDER</h3>
            <button class="close-modal-btn" onclick="closeModal('riderDetailsModal')">&times;</button>
        </div>
        <div style="text-align: left; font-family: monospace; margin-bottom: 1rem;">
            <div style="font-size: 1.3rem; font-weight: bold; background: var(--primary-color); color: white; padding: 6px; border-radius: 6px; margin-bottom: 1rem; text-align: center;">
                Order ID: ${o.id}
            </div>
            <div style="font-size: 0.9rem; margin-bottom: 1rem; line-height: 1.5;">
                <p><b>Customer Name:</b> ${esc(o.custName)}</p>
                <p><b>Order Option:</b> ${esc(o.type)}</p>
            </div>
            <hr style="border: none; border-top: 1px dashed #ccc; margin: 0.8rem 0;">
            <form id="riderDetailsForm" onsubmit="saveRiderDetails(event, ${index})">
                <div style="margin-bottom: 0.8rem;">
                    <label style="display: block; font-size: 0.85rem; font-weight: bold; margin-bottom: 4px;">Rider Name:</label>
                    <input type="text" id="inputRiderName" value="${esc(currentRiderName)}" placeholder="Enter rider name..." required style="width: 100%; padding: 8px; border: 1px solid #ccc; border-radius: 4px; box-sizing: border-box; font-family: inherit;">
                </div>
                <div style="margin-bottom: 1.2rem;">
                    <label style="display: block; font-size: 0.85rem; font-weight: bold; margin-bottom: 4px;">Contact Number:</label>
                    <input type="text" id="inputRiderContact" value="${esc(currentRiderContact)}" placeholder="Enter contact number..." required style="width: 100%; padding: 8px; border: 1px solid #ccc; border-radius: 4px; box-sizing: border-box; font-family: inherit;">
                </div>
                <div style="display: flex; gap: 0.5rem;" class="no-print">
                    <button type="submit" class="form-btn" style="background-color: var(--primary-color); flex: 1;">OK / Save Rider Details</button>
                    <button type="button" class="form-btn" onclick="closeModal('riderDetailsModal')" style="background-color: #666; width: auto;">Close</button>
                </div>
            </form>
        </div>`;
    openModal('riderDetailsModal');
}

function saveRiderDetails(event, index) {
    event.preventDefault();
    const newName = document.getElementById('inputRiderName').value.trim();
    const newContact = document.getElementById('inputRiderContact').value.trim();
    orders[index].riderName = newName;
    orders[index].riderContact = newContact;
    saveState();
    alert("Rider details updated successfully!");
    closeModal('riderDetailsModal');
    renderOrdersTable();
    renderTVDisplay();
}

function renderInventoryTable() {
    const _ae = document.activeElement;
    if (_ae && (_ae.tagName === 'INPUT' || _ae.tagName === 'TEXTAREA') && staffProductTable.contains(_ae)) return;
    staffProductTable.innerHTML = '';
    products.forEach((p, index) => {
        const row = document.createElement('tr');
        row.innerHTML = `
            <td><strong>${p.name}</strong>${p.stock <= 5 && p.stock > 0 ? '<span class="low-stock-badge">LOW STOCK</span>' : p.stock <= 0 ? '<span class="low-stock-badge" style="background:#555;">OUT OF STOCK</span>' : ''}</td>
            <td>${p.category}</td>
            <td>P<input type="number" value="${p.price.toFixed(2)}" min="0.01" step="0.01" style="width: 80px; padding: 2px 4px;" onchange="updateProductPrice(${index}, this.value)"></td>
            <td><input type="number" value="${p.stock}" min="0" style="width: 60px; padding: 2px 4px;" onchange="updateProductStock(${index}, this.value)"></td>
            <td><button class="delete-btn" onclick="deleteProduct(${index})">Delete</button></td>`;
        staffProductTable.appendChild(row);
    });
}

function updateProductStock(index, newStock) {
    products[index].stock = parseInt(newStock) || 0;
    saveState();
    renderProducts(products);
    renderInventoryTable();
}

function updateProductPrice(index, newPrice) {
    const v = parseFloat(newPrice);
    if (!(v > 0)) { renderInventoryTable(); return; }
    products[index].price = Math.round(v * 100) / 100;
    saveState();
    renderProducts(products);
    renderInventoryTable();
}

function deleteProduct(index) {
    if (confirm("Delete this product from menu?")) {
        products.splice(index, 1);
        saveState();
        renderProducts(products);
        renderStaffTables();
    }
}

// Normalizes a pasted image link: trims spaces, adds https:// when the scheme is missing.
function normalizeImageUrl(raw) {
    let u = (raw || '').trim().replace(/^["'<>\s]+|["'<>\s]+$/g, '');
    if (!u) return u;
    if (/^(https?:\/\/|data:|blob:)/i.test(u)) return u;
    return 'https://' + u.replace(/^\/+/, '');
}

// Live preview under the Image URL field so a bad link is visible BEFORE saving.
function previewProdImage() {
    const input = document.getElementById('prodImage');
    const prev = document.getElementById('prodImagePreview');
    const err = document.getElementById('prodImageError');
    if (!input || !prev) return;
    const u = normalizeImageUrl(input.value);
    if (!u) { prev.hidden = true; prev.removeAttribute('src'); if (err) err.hidden = true; return; }
    prev.hidden = false;
    prev.onload = function() { if (err) err.hidden = true; };
    prev.onerror = function() { if (err) err.hidden = false; };
    if (prev.getAttribute('src') !== u) { if (err) err.hidden = true; prev.src = u; }
}

document.getElementById('addProductForm').addEventListener('submit', function(e) {
    e.preventDefault();
    const name = document.getElementById('prodName').value.trim();
    const category = document.getElementById('prodCategory').value;
    const price = parseFloat(document.getElementById('prodPrice').value);
    const stock = parseInt(document.getElementById('prodStock').value);
    const desc = document.getElementById('prodDesc').value.trim();
    const image = normalizeImageUrl(document.getElementById('prodImage').value);
    if (!image) {
        alert("Please paste an image link.");
        return;
    }

    products.unshift({ id: Date.now(), name, category, price, stock, isAvailable: true, desc, image });
    saveState();
    this.reset();
    const _prev = document.getElementById('prodImagePreview');
    if (_prev) { _prev.hidden = true; _prev.removeAttribute('src'); }
    renderProducts(products);
    renderStaffTables();
    alert(" Product added successfully!");
});

function renderCustomOptionsStaffTable() {
    const tableBody = document.getElementById('customOptionsTableBody');
    tableBody.innerHTML = '';
    for (let category in customOptions) {
        customOptions[category].forEach((opt, idx) => {
            const isAvail = opt.isAvailable !== false;
            const row = document.createElement('tr');
            row.innerHTML = `
                <td style="text-transform: capitalize;">${category}</td>
                <td>${opt.name}</td>
                <td>P<input type="number" value="${opt.price.toFixed(2)}" min="0" step="0.01" style="width: 80px; padding: 2px 4px;" onchange="updateCustomOptionPrice('${category}', ${idx}, this.value)"></td>
                <td>
                    <button class="availability-toggle-btn ${isAvail ? 'btn-available' : 'btn-not-available'}" onclick="toggleCustomOptionAvailability('${category}', ${idx})">
                        ${isAvail ? 'Available' : 'Not Available'}
                    </button>
                </td>
                <td><button class="delete-btn" style="padding: 2px 6px; font-size: 0.75rem;" onclick="deleteCustomOption('${category}', ${idx})">Delete</button></td>`;
            tableBody.appendChild(row);
        });
    }
}

function toggleCustomOptionAvailability(category, idx) {
    const current = customOptions[category][idx].isAvailable !== false;
    customOptions[category][idx].isAvailable = !current;
    saveState();
    renderCustomOptionsStaffTable();
}

function updateCustomOptionPrice(category, idx, newPrice) {
    const v = parseFloat(newPrice);
    if (!(v >= 0)) { renderCustomOptionsStaffTable(); return; }
    customOptions[category][idx].price = Math.round(v * 100) / 100;
    saveState();
    renderCustomOptionsStaffTable();
}

document.getElementById('addCustomOptionForm').addEventListener('submit', function(e) {
    e.preventDefault();
    const category = document.getElementById('customOptCategory').value;
    const name = document.getElementById('customOptName').value.trim();
    const price = parseFloat(document.getElementById('customOptPrice').value) || 0;

    if (!customOptions[category]) customOptions[category] = [];
    customOptions[category].push({ name, price, isAvailable: true });
    saveState();
    renderCustomOptionsStaffTable();
    this.reset();
    alert(" Custom Option added!");
});

function deleteCustomOption(category, idx) {
    if (confirm("Delete this custom option?")) {
        customOptions[category].splice(idx, 1);
        saveState();
        renderCustomOptionsStaffTable();
    }
}

function renderTopSellingList() {
    const listContainer = document.getElementById('topSellingList');
    let itemCounts = {};
    const allOrders = orders.concat(orderHistory);
    allOrders.forEach(o => {
        if (o.rawItemsList && Array.isArray(o.rawItemsList)) {
            o.rawItemsList.forEach(item => {
                itemCounts[item.name] = (itemCounts[item.name] || 0) + item.qty;
            });
        }
    });

    const sorted = Object.keys(itemCounts).sort((a, b) => itemCounts[b] - itemCounts[a]);
    if (sorted.length === 0) {
        listContainer.innerHTML = '<span style="color:#888;">No sales recorded yet.</span>';
        return;
    }
    let html = '<ol style="padding-left: 1.2rem;">';
    sorted.slice(0, 5).forEach((name, idx) => {
        html += `<li><strong>${name}</strong> - ${itemCounts[name]} sold ${idx === 0 ? '(Best Seller)' : ''}</li>`;
    });
    html += '</ol>';
    listContainer.innerHTML = html;
}

function renderKPICards() {
    // Galing sa sales ledger — hindi nababawasan kahit i-trash ang history.
    syncLedgerDay();
    const totalSales = salesLedger.total, cashSales = salesLedger.cash, gcashSales = salesLedger.gcash;
    let activeCount = 0;
    orders.forEach(o => {
        if (o.status !== 'Cancelled' && ['Received', 'Preparing', 'Ready for Courier Pick-Up', 'Handed To Delivery Rider'].includes(o.status)) activeCount++;
    });
    document.getElementById('kpiTotalSales').innerText = 'P' + totalSales.toFixed(2);
    document.getElementById('kpiCashSales').innerText = 'P' + cashSales.toFixed(2);
    document.getElementById('kpiGcashSales').innerText = 'P' + gcashSales.toFixed(2);
    document.getElementById('kpiActiveOrders').innerText = activeCount;
    document.getElementById('kpiTotalProducts').innerText = products.length;
}

function openEndStoreDayModal() {
    // Galing sa sales ledger — kasama pa rin ang nabura nang history.
    syncLedgerDay();
    const reportBox = document.getElementById('endDayReportContent');
    reportBox.innerHTML =
        '<p><strong>Total Orders Today (incl. completed):</strong> ' + salesLedger.count + '</p>' +
        '<p><strong>Total Revenue:</strong> P' + salesLedger.total.toFixed(2) + '</p>' +
        '<p><strong>Cash:</strong> P' + salesLedger.cash.toFixed(2) + '</p>' +
        '<p><strong>GCash:</strong> P' + salesLedger.gcash.toFixed(2) + '</p>';
    openModal('endDayModal');
}

function closeDayAndClearOrders() {
    dayStart = Date.now();
    orderHistory = orders.concat(orderHistory);
    orders = [];
    // Dito lang nare-reset ang sales — panibago kinabukasan.
    salesLedger = { total: 0, cash: 0, gcash: 0, count: 0, dayStart: dayStart };
    saveState();
    closeModal('endDayModal');
    renderStaffTables();
    renderTVDisplay();
    renderProducts(products);
    alert(" Store day closed and orders archived.");
}

/* --- RESET ALL DATA (parang bago: walang sales, orders, accounts) --- */
function resetAllData() {
    if (!currentUser || (currentUser.role !== 'staff' && currentUser.role !== 'admin')) {
        alert("Staff only! Mag-login bilang admin para mag-reset.");
        return;
    }
    if (!confirm("Ire-reset ang LAHAT ng data?\n- Total sales = 0\n- Orders = wala\n- Accounts = wala\nHindi na ito maibabalik!")) return;
    const code = prompt("Type RESET (malalaking letra) para kumpirmahin ang pagbura ng lahat:");
    if (!code || code.trim().toUpperCase() !== "RESET") {
        alert("Cancelled. Walang binura.");
        return;
    }
    orders = [];
    orderHistory = [];
    trashHistory = [];
    userTrashHistory = [];
    registeredUsers = [];
    currentUser = null;
    rememberMe = false;
    cart = [];
    dayStart = Date.now();
    salesLedger = { total: 0, cash: 0, gcash: 0, count: 0, dayStart: dayStart };
    try {
        Object.keys(localStorage).filter(k => k.indexOf('cnc_') === 0).forEach(k => localStorage.removeItem(k));
    } catch (e) {}
    localStorage.setItem('cnc_day_start', String(dayStart));
    saveState();
    renderAuthHeaderUI();
    renderHamburgerMenu();
    applyProductFilters();
    updateCart();
    renderStaffTables();
    renderTVDisplay();
    try { renderTrackView(); } catch (e) {}
    closeAllDrawersAndModals();
    switchView('customer');
    alert("Reset done! Parang bago ulit: walang sales, orders, at accounts.");
}

/* --- HISTORY & TRASH --- */

// Feature 6: Responsive at maayos na order history card view
// X kada order (isa-isa) + Select mode (maramihan) papuntang trash
let isHistorySelectionMode = false;
let selectedHistoryIndices = [];
function toggleHistorySelectionMode() {
    selectedHistoryIndices = [];
    const sa = document.getElementById('historyTrashSelectAll');
    if (sa) sa.checked = false;
    isHistorySelectionMode = !isHistorySelectionMode;
    document.getElementById('historySelectModeBtn').innerText = isHistorySelectionMode ? 'Cancel' : 'Select';
    document.getElementById('historySelectColHeader').style.display = isHistorySelectionMode ? 'table-cell' : 'none';
    document.getElementById('historyBulkActionBar').style.display = isHistorySelectionMode ? 'flex' : 'none';
    renderOrderHistoryTable();
}
function toggleHistorySelect(idx) {
    const pos = selectedHistoryIndices.indexOf(idx);
    if (pos === -1) selectedHistoryIndices.push(idx);
    else selectedHistoryIndices.splice(pos, 1);
    renderOrderHistoryTable();
}
function toggleAllHistorySelect(el) {
    if (el.checked) selectedHistoryIndices = orderHistory.map((_, i) => i);
    else selectedHistoryIndices = [];
    renderOrderHistoryTable();
}
function deleteSingleHistoryOrder(idx) {
    const o = orderHistory[idx];
    if (!o) return;
    if (confirm(`Move Order ${o.id} to Trash Bin?`)) {
        trashHistory.unshift(orderHistory.splice(idx, 1)[0]);
        selectedHistoryIndices = [];
        saveState();
        renderOrderHistoryTable();
    }
}
function deleteSelectedHistory() {
    if (selectedHistoryIndices.length === 0) {
        alert("Pumili muna ng isa o higit pang order (check sa gilid).");
        return;
    }
    if (confirm("Move the " + selectedHistoryIndices.length + " selected order(s) to Trash Bin?")) {
        selectedHistoryIndices.sort((a,b)=>b-a).forEach(idx => {
            if (orderHistory[idx]) trashHistory.unshift(orderHistory.splice(idx, 1)[0]);
        });
        selectedHistoryIndices = [];
        saveState();
        toggleHistorySelectionMode();
        renderOrderHistoryTable();
    }
}
function updateHistoryTrashCount() {
    const btn = document.getElementById('historyTrashCountBtn');
    if (btn) btn.innerText = `View trash bin (${trashHistory.length})`;
}
function openOrderHistoryModal() {
    isHistorySelectionMode = false;
    selectedHistoryIndices = [];
    const sb = document.getElementById('historySelectModeBtn');
    if (sb) sb.innerText = 'Select';
    const sc = document.getElementById('historySelectColHeader');
    if (sc) sc.style.display = 'none';
    const bb = document.getElementById('historyBulkActionBar');
    if (bb) bb.style.display = 'none';
    renderOrderHistoryTable();
    openModal('historyModal');
}
function renderOrderHistoryTable() {
    const body = document.getElementById('historyTableBody');
    body.innerHTML = '';
    updateHistoryTrashCount();
    if (orderHistory.length === 0) {
        body.innerHTML = '<tr><td colspan="6" style="text-align:center; color:#888;">No past order history available.</td></tr>';
        return;
    }
    orderHistory.forEach((o, idx) => {
        const row = document.createElement('tr');
        row.innerHTML = `
            ${isHistorySelectionMode ? `<td style="text-align:center;"><input type="checkbox" style="width:18px; height:18px; cursor:pointer;" ${selectedHistoryIndices.includes(idx) ? "checked" : ""} onchange="toggleHistorySelect(${idx})"></td>` : ''}
            <td><strong>${o.id}</strong><br><small style="color:#777;">${o.timestamp}</small></td>
            <td style="font-size:0.8rem; text-align:left;">${formatItemsHtml(o)}</td>
            <td>P${o.total.toFixed(2)}</td>
            <td><span class="status-select status-${o.status.replace(/\s+/g, '')}">${o.status}</span></td>
            <td><button class="delete-btn" style="padding: 2px 8px; font-size: 0.75rem;" onclick="deleteSingleHistoryOrder(${idx})" title="Move to trash">X</button></td>`;
        body.appendChild(row);
    });
}

function filterOrderHistory() {
    const query = document.getElementById('historySearchInput').value.toLowerCase();
    document.querySelectorAll('#historyTableBody tr').forEach(row => {
        row.style.display = row.innerText.toLowerCase().includes(query) ? '' : 'none';
    });
}

function openTrashModal() {
    closeModal('historyModal');
    isSelectionMode = false;
    selectedTrashIndices = [];
    document.getElementById('trashDotsMenu').classList.remove('show');
    renderTrashTable();
    openModal('trashModal');
}

function toggleDotsDropdown() {
    document.getElementById('trashDotsMenu').classList.toggle('show');
}

function emptyTrashDirectly() {
    if (trashHistory.length === 0) {
        alert("Trash bin is already empty.");
        return;
    }
    if (confirm(" Permanently delete all items in Trash Bin?")) {
        selectedTrashIndices = [];
        trashHistory = [];
        saveState();
        renderTrashTable();
        alert(" Trash emptied!");
    }
}

function toggleSelectionMode() {
    selectedTrashIndices = [];
    const sa = document.getElementById('trashSelectAll');
    if (sa) sa.checked = false;
    isSelectionMode = !isSelectionMode;
    document.getElementById('selectModeBtn').innerText = isSelectionMode ? 'Cancel' : 'Select';
    document.getElementById('selectColHeader').style.display = isSelectionMode ? 'table-cell' : 'none';
    document.getElementById('bulkActionBar').style.display = isSelectionMode ? 'flex' : 'none';
    renderTrashTable();
}

function renderTrashTable() {
    const body = document.getElementById('trashTableBody');
    body.innerHTML = '';
    if (trashHistory.length === 0) {
        body.innerHTML = '<tr><td colspan="6" style="text-align:center; color:#888;">Trash Bin is empty.</td></tr>';
        return;
    }
    trashHistory.forEach((o, idx) => {
        body.innerHTML += `
            <tr>
                ${isSelectionMode ? `<td style="text-align:center;"><input type="checkbox" style="width:18px; height:18px; cursor:pointer;" ${selectedTrashIndices.includes(idx) ? "checked" : ""} onchange="toggleTrashSelect(${idx})"></td>` : ''}
                <td><strong>${o.id}</strong><br><small style="color:#777;">${o.timestamp}</small></td>
                <td style="font-size:0.8rem; text-align:left;">${formatItemsHtml(o)}</td>
                <td>P${o.total.toFixed(2)}</td>
                <td><span class="status-select status-${o.status.replace(/\s+/g, '')}">${o.status}</span></td>
                <td><button class="delete-btn" style="padding: 2px 8px; font-size: 0.75rem;" onclick="deleteSingleTrashOrder(${idx})" title="Permanently delete this order">X</button></td>
            </tr>`;
    });
}

function deleteSingleTrashOrder(idx) {
    if (!trashHistory[idx]) return;
    if (!confirm(`Permanently delete order ${trashHistory[idx].id}? Hindi na ito maibabalik.`)) return;
    trashHistory.splice(idx, 1);
    selectedTrashIndices = [];
    saveState();
    renderTrashTable();
}

function toggleTrashSelect(idx) {
    const pos = selectedTrashIndices.indexOf(idx);
    if (pos === -1) selectedTrashIndices.push(idx);
    else selectedTrashIndices.splice(pos, 1);
    renderTrashTable();
}
function toggleAllTrashSelect(el) {
    if (el.checked) selectedTrashIndices = trashHistory.map((_, i) => i);
    else selectedTrashIndices = [];
    renderTrashTable();
}
function restoreAllTrash() {
    if (trashHistory.length === 0) { alert("Trash bin is already empty."); return; }
    if (confirm("Restore ALL items in Trash Bin back to Order History?")) {
        orderHistory = trashHistory.concat(orderHistory);
        trashHistory = [];
        selectedTrashIndices = [];
        saveState();
        renderTrashTable();
        alert("All items restored!");
    }
}

function restoreSelectedItems() {
    if (selectedTrashIndices.length === 0) {
        alert("Pumili muna ng isa o higit pang item (check sa gilid) na ire-restore. Para sa lahat, gamitin ang ⋮ > Restore All.");
        return;
    }
    selectedTrashIndices.sort((a,b)=>b-a).forEach(idx => {
        if (trashHistory[idx]) orderHistory.unshift(trashHistory.splice(idx, 1)[0]);
    });
    selectedTrashIndices = [];
    saveState();
    toggleSelectionMode();
    renderTrashTable();
    alert(" Items restored!");
}

function deleteSelectedPermanently() {
    if (selectedTrashIndices.length === 0) {
        alert("Pumili muna ng isa o higit pang item (check sa gilid) na ide-delete. Para sa lahat, gamitin ang ⋮ > Empty Trash.");
        return;
    }
    if (confirm("Permanently delete the " + selectedTrashIndices.length + " selected item(s)?")) {
        selectedTrashIndices.sort((a,b)=>b-a).forEach(idx => trashHistory.splice(idx, 1));
        selectedTrashIndices = [];
        saveState();
        toggleSelectionMode();
        renderTrashTable();
        alert(" Deleted permanently.");
    }
}

function backToOrderHistory() {
    closeModal('trashModal');
    openOrderHistoryModal();
}

/* --- USER ORDER HISTORY & TRASH LOGIC --- */
function openUserOrderHistory() {
    if (!currentUser) return;
    renderUserOrderHistoryTable();
    openModal('userOrderHistoryModal');
}

function renderUserOrderHistoryTable() {
    if (!currentUser) return;
    const body = document.getElementById('userHistoryTableBody');
    body.innerHTML = '';
    const userOrders = orders.concat(orderHistory).filter(o => (o.userEmail || '').toLowerCase() === (currentUser.email || '').toLowerCase()).sort((a, b) => (b.createdAt || 0) - (a.createdAt || 0));
    if (userOrders.length === 0) {
        body.innerHTML = '<tr><td colspan="4" style="text-align:center; color:#888;">You have no past orders.</td></tr>';
        return;
    }
    userOrders.forEach((o) => {
        const row = document.createElement('tr');
        row.className = 'longpress-row';
        row.setAttribute('title', 'Press and hold to delete item');
        row.innerHTML = `
            <td><strong>${o.id}</strong><br><small style="color:#777;">${o.timestamp}</small></td>
            <td style="font-size:0.8rem; text-align:left;">${formatItemsHtml(o)}</td>
            <td>P${o.total.toFixed(2)}</td>
            <td><span class="status-select status-${o.status.replace(/\s+/g, '')}">${o.status}</span></td>`;

        let timer = null;
        const startPress = () => {
            if (timer) return;
            timer = setTimeout(() => {
                timer = null;
                if (confirm(`Delete Order ${o.id} and move to Trash Bin?`)) {
                    let activeIdx = orders.findIndex(item => item.id === o.id);
                    if (activeIdx !== -1) {
                        userTrashHistory.unshift(orders.splice(activeIdx, 1)[0]);
                    } else {
                        let histIdx = orderHistory.findIndex(item => item.id === o.id);
                        if (histIdx !== -1) {
                            userTrashHistory.unshift(orderHistory.splice(histIdx, 1)[0]);
                        }
                    }
                    saveState();
                    renderUserOrderHistoryTable();
                }
            }, 800);
        };
        const clearPress = () => { if (timer) { clearTimeout(timer); timer = null; } };
        row.addEventListener('mousedown', startPress);
        row.addEventListener('mouseup', clearPress);
        row.addEventListener('mouseleave', clearPress);
        row.addEventListener('touchstart', startPress);
        row.addEventListener('touchend', clearPress);
        row.addEventListener('contextmenu', (e) => e.preventDefault());
        body.appendChild(row);
    });
}

function filterUserOrderHistory() {
    const query = document.getElementById('userHistorySearchInput').value.toLowerCase();
    document.querySelectorAll('#userHistoryTableBody tr').forEach(row => {
        row.style.display = row.innerText.toLowerCase().includes(query) ? '' : 'none';
    });
}

function openUserTrashModal() {
    closeModal('userOrderHistoryModal');
    isUserSelectionMode = false;
    selectedUserTrashIndices = [];
    document.getElementById('userTrashDotsMenu').classList.remove('show');
    renderUserTrashTable();
    openModal('userTrashModal');
}

function backToUserOrderHistory() {
    closeModal('userTrashModal');
    openUserOrderHistory();
}

function toggleUserTrashDotsMenu() {
    document.getElementById('userTrashDotsMenu').classList.toggle('show');
}

function emptyUserTrashDirectly() {
    if (!currentUser) return;
    const currentUsersItems = userTrashHistory.filter(o => o.userEmail === currentUser.email);
    if (currentUsersItems.length === 0) {
        alert("Trash bin is already empty.");
        return;
    }
    if (confirm(" Are you sure you want to permanently delete all items in your Trash Bin?")) {
        userTrashHistory = userTrashHistory.filter(o => o.userEmail !== currentUser.email);
        saveState();
        renderUserTrashTable();
        alert(" Trash Bin emptied successfully!");
    }
}

function toggleUserSelectionMode() {
    selectedUserTrashIndices = [];
    const sa = document.getElementById('userTrashSelectAll');
    if (sa) sa.checked = false;
    isUserSelectionMode = !isUserSelectionMode;
    document.getElementById('userSelectModeBtn').innerText = isUserSelectionMode ? 'Cancel' : 'Select';
    document.getElementById('userSelectColHeader').style.display = isUserSelectionMode ? 'table-cell' : 'none';
    document.getElementById('userBulkActionBar').style.display = isUserSelectionMode ? 'flex' : 'none';
    renderUserTrashTable();
}

function renderUserTrashTable() {
    if (!currentUser) return;
    const body = document.getElementById('userTrashTableBody');
    body.innerHTML = '';
    const userTrashItems = userTrashHistory.filter(o => o.userEmail === currentUser.email);
    if (userTrashItems.length === 0) {
        body.innerHTML = '<tr><td colspan="5" style="text-align:center; color:#888;">Trash Bin is empty.</td></tr>';
        return;
    }
    userTrashItems.forEach((o, idx) => {
        const row = document.createElement('tr');
        row.innerHTML = `
            ${isUserSelectionMode ? `<td style="text-align:center;"><input type="checkbox" style="width: 18px; height: 18px; cursor:pointer;" ${selectedUserTrashIndices.includes(idx) ? "checked" : ""} onchange="toggleUserTrashSelect(${idx})"></td>` : ''}
            <td><strong>${o.id}</strong><br><small style="color:#777;">${o.timestamp}</small></td>
            <td style="font-size:0.8rem; text-align:left;">${formatItemsHtml(o)}</td>
            <td>P${o.total.toFixed(2)}</td>
            <td><span class="status-select status-${o.status.replace(/\s+/g, '')}">${o.status}</span></td>`;
        body.appendChild(row);
    });
}

function toggleUserTrashSelect(idx) {
    const pos = selectedUserTrashIndices.indexOf(idx);
    if (pos === -1) selectedUserTrashIndices.push(idx);
    else selectedUserTrashIndices.splice(pos, 1);
    renderUserTrashTable();
}
function toggleAllUserTrashSelect(el) {
    if (!currentUser) return;
    const items = userTrashHistory.filter(o => o.userEmail === currentUser.email);
    if (el.checked) selectedUserTrashIndices = items.map((_, i) => i);
    else selectedUserTrashIndices = [];
    renderUserTrashTable();
}
function restoreAllUserTrash() {
    if (!currentUser) return;
    const items = userTrashHistory.filter(o => o.userEmail === currentUser.email);
    if (items.length === 0) { alert("Trash bin is already empty."); return; }
    if (confirm("Restore ALL items in your Trash Bin back to Order History?")) {
        items.forEach(item => orderHistory.unshift(item));
        userTrashHistory = userTrashHistory.filter(o => o.userEmail !== currentUser.email);
        selectedUserTrashIndices = [];
        saveState();
        renderUserTrashTable();
        alert("All items restored!");
    }
}

function restoreUserSelectedItems() {
    if (!currentUser) return;
    const userTrashItems = userTrashHistory.filter(o => o.userEmail === currentUser.email);
    if (selectedUserTrashIndices.length === 0) {
        alert("Pumili muna ng isa o higit pang item (check sa gilid) na ire-restore. Para sa lahat, gamitin ang ⋮ > Restore All.");
        return;
    } else {
        selectedUserTrashIndices.sort((a,b)=>b-a).forEach(idx => {
            const itemToRestore = userTrashItems[idx];
            if (itemToRestore) {
                orderHistory.unshift(itemToRestore);
                const globalIdx = userTrashHistory.findIndex(o => o.id === itemToRestore.id);
                if (globalIdx !== -1) userTrashHistory.splice(globalIdx, 1);
            }
        });
    }
    saveState();
    toggleUserSelectionMode();
    renderUserTrashTable();
    alert(" Selected items restored to Order History!");
}

function deleteUserSelectedPermanently() {
    if (!currentUser) return;
    if (selectedUserTrashIndices.length === 0) {
        alert("Pumili muna ng isa o higit pang item (check sa gilid) na ide-delete. Para sa lahat, gamitin ang ⋮ > Empty All.");
        return;
    }
    if (confirm("Are you sure you want to permanently delete the " + selectedUserTrashIndices.length + " selected item(s)?")) {
        const userTrashItems = userTrashHistory.filter(o => o.userEmail === currentUser.email);
        {
            selectedUserTrashIndices.sort((a,b)=>b-a).forEach(idx => {
                const itemToDelete = userTrashItems[idx];
                if (itemToDelete) {
                    const globalIdx = userTrashHistory.findIndex(o => o.id === itemToDelete.id);
                    if (globalIdx !== -1) userTrashHistory.splice(globalIdx, 1);
                }
            });
        }
        saveState();
        toggleUserSelectionMode();
        renderUserTrashTable();
        alert(" Items permanently deleted.");
    }
}

/* --- ORDER STATUS BOARD / TRACKING & ORDER HISTORY INTEGRATION --- */
let currentReviewingOrderId = null;

// Iponin sa ledger ang mga natapos nang order na hindi pa naire-record
// (hal. mga na-Complete bago pa idinagdag ang ledger) — isang beses lang bawat order.
function backfillLedger() {
    let changed = false;
    orders.concat(orderHistory).forEach(o => {
        if (o.status === 'Completed' && !o.salesRecorded && (o.createdAt || 0) >= dayStart) {
            recordSaleToLedger(o);
            changed = true;
        }
    });
    if (changed) saveState();
}

function confirmOrderReceived(orderId) {
    const allOrders = orders.concat(orderHistory);
    const order = allOrders.find(o => o.id === orderId);
    if (!order) return;
    
    order.status = 'Completed';
    order.customerReceived = true;
    
    // Auto move order to orderHistory if inside active orders
    const actIdx = orders.findIndex(o => o.id === orderId);
    if (actIdx !== -1) {
        orderHistory.unshift(orders.splice(actIdx, 1)[0]);
    }

    // I-record sa sales ledger (mananatili kahit i-trash ang history).
    recordSaleToLedger(order);

    saveState();
    
    alert("Salamat sa pag-confirm! Nabalitaan naming natanggap mo na ang iyong order. Pwede mo na ngayong i-rate ang mga produkto!");
    renderTVDisplay();
    renderStaffTables();
    renderTrackView();
    openProductReviewPage(orderId);
}

// Live board: WALANG tabs — isang derecho na flow list.
// Bawat order card kita agad kung nasaan sa flow (Received → Order Received),
// nakaayos mula pinakaunang stage; completed/cancelled nasa ibaba.
function boardCardHtml(o) {
    let actionBtnHtml = '';
    if (currentUser && currentUser.role !== 'staff' && currentUser.role !== 'admin' && (o.userEmail || '').toLowerCase() === (currentUser.email || '').toLowerCase()) {
        if (o.status === 'Completed') {
            if (!o.customerReceived) {
                actionBtnHtml = `
                    <button class="form-btn" style="margin-top: 0.5rem; padding: 0.4rem 0.8rem; font-size: 0.8rem; background: #2a9d8f; width: auto; font-weight: bold;" onclick="confirmOrderReceived('${o.id}')">
                        ✔ Order Received
                    </button>`;
            } else {
                actionBtnHtml = `
                    <button class="form-btn" style="margin-top: 0.5rem; padding: 0.4rem 0.8rem; font-size: 0.8rem; background: var(--primary-color); width: auto; font-weight: bold;" onclick="openProductReviewPage('${o.id}')">
                        ★ Review Products
                    </button>`;
            }
        } else if (o.status === 'Handed To Delivery Rider') {
            actionBtnHtml = `
                <button class="form-btn" style="margin-top: 0.5rem; padding: 0.4rem 0.8rem; font-size: 0.8rem; background: #2a9d8f; width: auto; font-weight: bold;" onclick="confirmOrderReceived('${o.id}')">
                    ✔ Order Received
                </button>`;
        }
    }

    const steps = o.status === 'Cancelled'
        ? '<div style="margin-top: 0.5rem;"><span class="track-pill-cancel">Cancelled</span></div>'
        : trackStepsHtml(o.status);

    return `
        <div style="background: #fff; padding: 1.2rem 1.4rem; border-radius: 12px; width: 100%; max-width: 550px; box-shadow: 0 4px 15px rgba(0,0,0,0.06); border-left: 5px solid #f4a261;">
            <div style="text-align: left;">
                <div style="font-size: 0.75rem; color: #aaa; text-transform: uppercase;">Order ID Number</div>
                <div style="font-size: 1.8rem; font-weight: bold; color: #f4a261;">${o.id}</div>
                <div style="font-size: 0.8rem; color: #666; margin-top: 4px; text-align:left;">${formatItemsHtml(o)}</div>
                <div style="font-size: 0.85rem; font-weight: bold; color: var(--primary-color); margin-top: 2px;">Total: P${o.total.toFixed(2)}</div>
                ${steps}
                ${trackEtaLine(o)}
                ${orderRiderLine(o)}
                ${actionBtnHtml}
            </div>
        </div>`;
}

function renderTVDisplay() {
    const displayContainer = document.getElementById('tvTabOrderDisplay');
    const titleContainer = document.getElementById('tvTabTitle');
    if (!displayContainer || !titleContainer) return;

    if (!currentUser) {
        titleContainer.innerText = 'Live order flow';
        displayContainer.innerHTML = '<p style="color: #aaa; font-style: italic;">Please sign in to view your order status and history.</p>';
        return;
    }

    const allOrders = orders.concat(orderHistory);
    let displayOrders = allOrders;
    if (currentUser.role !== 'staff' && currentUser.role !== 'admin') {
        displayOrders = allOrders.filter(o => (o.userEmail || '').toLowerCase() === (currentUser.email || '').toLowerCase());
    }

    const stageIdx = (o) => { const i = ORDER_FLOW.findIndex(s => s.value === o.status); return i === -1 ? 99 : i; };
    const active = displayOrders.filter(o => !['Completed', 'Cancelled'].includes(o.status))
        .sort((a, b) => stageIdx(a) - stageIdx(b) || (b.createdAt || 0) - (a.createdAt || 0));
    const done = displayOrders.filter(o => ['Completed', 'Cancelled'].includes(o.status))
        .sort((a, b) => (b.createdAt || 0) - (a.createdAt || 0));

    titleContainer.innerText = active.length + ' active • ' + done.length + ' completed';

    if (active.length === 0 && done.length === 0) {
        displayContainer.innerHTML = '<p style="color: #aaa; font-style: italic;">No orders yet.</p>';
        return;
    }

    displayContainer.innerHTML = active.map(boardCardHtml).join('')
        + (done.length
            ? '<div style="font-size: 0.9rem; font-weight: bold; color: #8a7a5f; margin-top: 0.8rem; text-transform: uppercase; letter-spacing: 1px;">Completed</div>'
              + done.map(boardCardHtml).join('')
            : '');
}

function openProductReviewPage(orderId) {
    const allOrders = orders.concat(orderHistory);
    const order = allOrders.find(o => o.id === orderId);
    if (!order) return;

    currentReviewingOrderId = order.id;
    document.getElementById('reviewPageTitle').innerText = `Review Products for Order ${order.id}`;
    const container = document.getElementById('productReviewListContainer');
    container.innerHTML = '';

    let itemsToReview = order.rawItemsList || [{ name: order.items, qty: 1 }];

    itemsToReview.forEach((item, idx) => {
        const existingReview = (order.productReviews && order.productReviews[item.name]) || { rating: 5, comment: "" };
        container.innerHTML += `
            <div style="background: #f8f9fa; padding: 1rem; border-radius: 8px; margin-bottom: 0.8rem; border: 1px solid #ddd; color: #333;">
                <h4 style="margin-bottom: 0.3rem; color: var(--primary-color);">${item.qty}x ${item.name}</h4>
                <div style="margin-bottom: 0.5rem;">
                    <label style="font-size: 0.8rem; font-weight: bold;">Per-Item Rating:</label>
                    <select id="prodRating_${idx}" class="status-select" style="width: 120px;">
                        <option value="5" ${existingReview.rating == 5 ? 'selected' : ''}>★★★★★ (5)</option>
                        <option value="4" ${existingReview.rating == 4 ? 'selected' : ''}>★★★★☆ (4)</option>
                        <option value="3" ${existingReview.rating == 3 ? 'selected' : ''}>★★★☆☆ (3)</option>
                        <option value="2" ${existingReview.rating == 2 ? 'selected' : ''}>★★☆☆☆ (2)</option>
                        <option value="1" ${existingReview.rating == 1 ? 'selected' : ''}>★☆☆☆☆ (1)</option>
                    </select>
                </div>
                <div>
                    <label style="font-size: 0.8rem; font-weight: bold;">Comment / Feedback for ${item.name}:</label>
                    <input type="text" id="prodComment_${idx}" value="${esc(existingReview.comment || '')}" placeholder="Kamusta ang lasa nitong ${esc(item.name)}?" style="width: 100%; padding: 0.4rem; border: 1px solid #ccc; border-radius: 4px; font-size: 0.85rem;">
                </div>
                <input type="hidden" id="prodName_${idx}" value="${esc(item.name)}">
            </div>`;
    });

    openModal('productReviewPageModal');
}

function saveProductReviews() {
    const allOrders = orders.concat(orderHistory);
    const order = allOrders.find(o => o.id === currentReviewingOrderId);
    if (!order) return;

    if (!order.productReviews) order.productReviews = {};
    let itemsToReview = order.rawItemsList || [{ name: order.items, qty: 1 }];

    itemsToReview.forEach((item, idx) => {
        const rating = document.getElementById(`prodRating_${idx}`).value;
        const comment = document.getElementById(`prodComment_${idx}`).value.trim();
        const prodName = document.getElementById(`prodName_${idx}`).value;
        const _prev = (order.productReviews && order.productReviews[prodName]) || {};
        const _storePrev = customerReviews.find(x => x.id === order.id + '||' + prodName) || {};
        const merged = { rating: parseInt(rating), comment, heartCount: _prev.heartCount || _storePrev.heartCount || 0, adminLiked: !!(_prev.adminLiked || _storePrev.adminLiked), adminReply: _prev.adminReply || _storePrev.adminReply || "" };
        order.productReviews[prodName] = merged;
        // i-mirror sa hiwalay na reviews store para mabuhay kahit mawala ang order
        upsertReviewEntry(order.id, prodName, {
            rating: merged.rating, comment: merged.comment,
            heartCount: merged.heartCount, adminLiked: merged.adminLiked, adminReply: merged.adminReply,
            userEmail: order.userEmail || '', custName: order.custName || 'Valued Customer',
            orderTime: order.timestamp || ''
        });
    });

    saveState();
    closeModal('productReviewPageModal');
    alert("Na-save na ang iyong mga per-item product reviews! Maraming salamat.");
    switchView('reviews');
}

// Feature 2: Admin Reply at Heart React sa Customer Reviews
// Gumagamit ng data-attributes (hindi inline string args) para gumana kahit may
// quote/apostrophe ang product name (hal. "Chef's Special").
function toggleHeartReactReview(btn) {
    const _isAdmin = currentUser && (currentUser.role === 'staff' || currentUser.role === 'admin');
    if (!_isAdmin) return;
    const orderId = btn && btn.dataset ? btn.dataset.orderId : btn;
    const prodName = btn && btn.dataset ? btn.dataset.prodName : arguments[1];
    // store-first: gumagana kahit wala na ang order (na-trash/delete na)
    const entry = customerReviews.find(x => x.id === orderId + '||' + prodName);
    if (!entry) return;
    entry.adminLiked = !entry.adminLiked;
    if (entry.adminLiked) entry.heartCount = (entry.heartCount || 0) + 1;
    else entry.heartCount = Math.max(0, (entry.heartCount || 0) - 1);
    persistReviews();
    // i-sync pabalik sa order kung buhay pa ito (para sa review modal defaults)
    const allOrders = orders.concat(orderHistory);
    const order = allOrders.find(o => o.id === orderId);
    if (order && order.productReviews && order.productReviews[prodName]) {
        const rev = order.productReviews[prodName];
        rev.adminLiked = entry.adminLiked;
        rev.heartCount = entry.heartCount;
        saveState();
    }
    renderAllCustomerReviewsPage();
}

function submitAdminReplyReview(btn) {
    const group = btn && btn.closest ? btn.closest('.admin-reply-input-group') : null;
    const inputEl = group ? group.querySelector('input') : null;
    if (!inputEl || !btn || !btn.dataset) return;
    const orderId = btn.dataset.orderId;
    const prodName = btn.dataset.prodName;
    const replyText = inputEl.value.trim();

    // store-first: gumagana kahit wala na ang order (na-trash/delete na)
    const entry = customerReviews.find(x => x.id === orderId + '||' + prodName);
    if (!entry) return;
    entry.adminReply = replyText;
    persistReviews();
    const allOrders = orders.concat(orderHistory);
    const order = allOrders.find(o => o.id === orderId);
    if (order && order.productReviews && order.productReviews[prodName]) {
        order.productReviews[prodName].adminReply = replyText;
        saveState();
    }
    renderAllCustomerReviewsPage();
    alert("Na-save ang reply sa customer review!");
}

function renderAllCustomerReviewsPage() {
    const container = document.getElementById('allCustomerReviewsList');
    if (!container) return;
    container.innerHTML = '';

    const isAdminView = currentUser && (currentUser.role === 'staff' || currentUser.role === 'admin');
    // Visibility: admin = lahat; guest (log out) = lahat; naka-login na customer = sariling reviews lang.
    let scoped = customerReviews.slice().sort((a, b) => (b.createdAt || 0) - (a.createdAt || 0));
    if (!isAdminView && currentUser) {
        const mine = (currentUser.email || '').toLowerCase();
        scoped = scoped.filter(r => (r.userEmail || '').toLowerCase() === mine);
    }
    let totalReviewsCount = 0;

    scoped.forEach(r => {
        const order = { id: r.orderId, timestamp: r.orderTime, custName: r.custName };
        const prodName = r.prodName;
        const rev = r;
        totalReviewsCount++;
                const safeRating = Math.min(5, Math.max(1, parseInt(rev.rating) || 5));
                let stars = '★'.repeat(safeRating) + '☆'.repeat(5 - safeRating);

                container.innerHTML += `
                    <div style="background: white; padding: 1.2rem; border-radius: 10px; box-shadow: 0 4px 12px rgba(0,0,0,0.06); border-left: 5px solid var(--primary-color);">
                        <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 0.5rem; flex-wrap: wrap;">
                            <h4 style="color: var(--primary-color); font-size: 1.05rem;">${esc(prodName)}</h4>
                            <div style="display: flex; align-items: center; gap: 0.6rem;">
                                ${getHeartHtml(order.id, prodName, rev, isAdminView)}
                                <span style="font-size: 0.8rem; color: #888;">Order ID: <b>${esc(order.id)}</b> | ${esc(order.timestamp)}</span>
                            </div>
                        </div>
                        <div style="margin-bottom: 0.4rem;">
                            <span style="font-size: 1.1rem; color: #f4a261;">${stars}</span>
                            <span style="font-weight: bold; font-size: 0.85rem; color: #555; margin-left: 0.5rem;">by ${esc(order.custName || 'Valued Customer')}</span>
                        </div>
                        <p style="font-size: 0.95rem; color: #333; font-style: italic; background: #fdf8f3; padding: 0.6rem 0.8rem; border-radius: 6px; border: 1px dashed #f0d0b0; margin-top: 0.4rem;">
                            "${esc(rev.comment) || 'No comment written.'}"
                        </p>

                        ${rev.adminReply ? `
                            <div class="admin-reply-box">
                                <strong>💬 Admin Reply:</strong> ${esc(rev.adminReply)}
                            </div>
                        ` : ''}

                        ${isAdminView ? `
                            <div class="admin-reply-input-group">
                                <input type="text" placeholder="Mag-reply sa review ng customer..." value="${esc(rev.adminReply || '')}">
                                <button onclick="submitAdminReplyReview(this)" data-order-id="${esc(order.id)}" data-prod-name="${esc(prodName)}" class="form-btn" style="width: auto; padding: 0.4rem 0.8rem; font-size: 0.8rem; background: var(--staff-color);">Reply</button>
                            </div>
                        ` : ''}
                    </div>`;
    });

    if (totalReviewsCount === 0) {
        if (isAdminView) return;
        const emptyMsg = currentUser
            ? 'You have no reviews yet. Order now and be the first to review!'
            : 'No customer reviews yet. Order now and be the first to review!';
        container.innerHTML = '<div style="text-align: center; padding: 2rem; color: #888; background: white; border-radius: 10px;">' + esc(emptyMsg) + '</div>';
    }
}

/* --- EVENT LISTENERS --- */
function setupEventListeners() {
    cartBtn.addEventListener('click', () => {
        if (!currentUser) {
            alert("Please sign in / login first to view your cart!");
            openModal('signInModal');
            return;
        }
        if (cartDrawer.classList.contains('open')) {
            cartDrawer.classList.remove('open');
            scheduleDrawerCleanup();
            return;
        }
        cartDrawer.classList.add('open');
        overlay.classList.add('active');
        lockBodyScroll(true);
        fitDrawersAboveFooter();
        scheduleDrawerRefit();
    });
    // Walang × button: double-click sa drawer/cart mismo o tap sa labas (overlay) ang magsasara
    document.getElementById('hamburgerDrawer').addEventListener('dblclick', closeAllDrawersAndModals);
    cartDrawer.addEventListener('dblclick', closeAllDrawersAndModals);

    filterBtns.forEach(btn => {
        btn.addEventListener('click', () => {
            filterBtns.forEach(b => b.classList.remove('active'));
            btn.classList.add('active');
            activeCategory = btn.dataset.category;
            applyProductFilters();
        });
    });

    searchInput.addEventListener('input', applyProductFilters);
    window.addEventListener('resize', fitDrawersAboveFooter);
    // Late resources (images/fonts) + mobile toolbar/visual viewport — i-fit ulit
    window.addEventListener('load', () => { try { fitDrawersAboveFooter(); } catch (e) {} });
    if (document.fonts && document.fonts.ready) document.fonts.ready.then(() => { try { fitDrawersAboveFooter(); } catch (e) {} });
    if (window.visualViewport) {
        visualViewport.addEventListener('resize', () => { try { fitDrawersAboveFooter(); } catch (e) {} });
        visualViewport.addEventListener('scroll', () => { try { fitDrawersAboveFooter(); } catch (e) {} });
    }
    // Kahit anong scroll (hal. touch-drag sa likod) — iwasang matakpan ang footer
    let __fitRaf = 0;
    window.addEventListener('scroll', () => {
        if (__fitRaf) return;
        __fitRaf = requestAnimationFrame(() => { __fitRaf = 0; try { fitDrawersAboveFooter(); } catch (e) {} });
    }, { passive: true, capture: true });
}

window.__CNC_VERSION = 'v58';
window.__cncBooted = false;
function bootOnce() {
    if (window.__cncBooted) return;
    window.__cncBooted = true;
    init();
}
if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', bootOnce);
    window.onload = bootOnce;
} else {
    bootOnce();
}

function playDingSound() {
    // McDo-kiosk style "tiuung": bright high note, then lower resolving note.
    try {
        const AC = window.AudioContext || window.webkitAudioContext;
        const ctx = new AC();
        if (ctx.state === 'suspended') ctx.resume();
        const notes = [
            { f: 1046.50, t: 0, d: 0.35 },   // C6 — "ti"
            { f: 783.99, t: 0.26, d: 0.8 }   // G5 — "uung"
        ];
        notes.forEach(n => {
            const osc = ctx.createOscillator();
            const g = ctx.createGain();
            const t0 = ctx.currentTime + n.t;
            osc.type = 'sine';
            osc.frequency.setValueAtTime(n.f, t0);
            g.gain.setValueAtTime(0.0001, t0);
            g.gain.exponentialRampToValueAtTime(0.5, t0 + 0.03);
            g.gain.exponentialRampToValueAtTime(0.0001, t0 + n.d);
            osc.connect(g);
            g.connect(ctx.destination);
            osc.start(t0);
            osc.stop(t0 + n.d + 0.05);
        });
    } catch (e) {
        console.log("Audio not supported or blocked by browser", e);
    }
}

function togglePasswordVisibility(fieldId, btnElement) {
    const inputField = document.getElementById(fieldId);
    if (!inputField) return;
    const show = inputField.type === "password";
    inputField.type = show ? "text" : "password";
    btnElement.classList.toggle("showing", show);
    btnElement.setAttribute("aria-label", show ? "Hide password" : "Show password");
}

function resetAllPasswordToggles() {
    const passwordInputs = document.querySelectorAll('.password-input-wrapper input');
    passwordInputs.forEach(input => {
        input.type = 'password';
    });
    const toggleBtns = document.querySelectorAll('.toggle-password-btn');
    toggleBtns.forEach(btn => {
        btn.classList.remove('showing');
        btn.setAttribute('aria-label', 'Show password');
    });
}

/* --- ORGANIZED ADMIN DASHBOARD TABS --- */
function switchAdminTab(tabName) {
  document.querySelectorAll('.admin-tab-btn').forEach(b => {
    b.classList.toggle('active', b.dataset.atab === tabName);
  });
  document.querySelectorAll('.admin-panel').forEach(p => {
    p.classList.toggle('active', p.id === 'adminPanel-' + tabName);
  });
}
function renderLowStockList() {
  const box = document.getElementById('lowStockList');
  if (!box) return;
  const low = products.filter(p => p.stock <= 5);
  if (low.length === 0) {
    box.innerHTML = '<span style="color:#2e7d32;">✅ All stocks healthy.</span>';
    return;
  }
  let h = '<ul style="padding-left:1.1rem;margin:0;">';
  low.forEach(p => {
    const label = p.stock <= 0 ? 'OUT OF STOCK' : ('LOW: ' + p.stock + ' left');
    const color = p.stock <= 0 ? '#b71c1c' : '#e65100';
    h += '<li><strong>' + p.name + '</strong> — <span style="color:' + color + ';font-weight:bold;">' + label + '</span></li>';
  });
  h += '</ul>';
  box.innerHTML = h;
}
const _origRenderStaffTables = renderStaffTables;
renderStaffTables = function() {
  _origRenderStaffTables();
  try {
    renderLowStockList();
    const activeCount = orders.filter(o => ['Received','Preparing','Ready for Courier Pick-Up','Handed To Delivery Rider'].includes(o.status)).length;
    const countEl = document.getElementById('adminOrdersCount');
    if (countEl) countEl.innerText = activeCount;
    const pill = document.getElementById('adminActiveOrdersPill');
    if (pill) pill.innerText = activeCount + ' active';
  } catch(e) { console.log(e); }
};

/* --- DELIVERY ETA (delivery orders only) --- */
function isDeliveryOrder(o) {
    return /delivery/i.test(o.type || '');
}
function formatEtaClock(etaAt) {
    try { return new Date(etaAt).toLocaleTimeString([], {hour: 'numeric', minute: '2-digit'}); }
    catch(e) { return ''; }
}
function getEtaCellHtml(o, index) {
    if (!isDeliveryOrder(o)) return '<span style="color:#999; font-size:0.8rem;" title="Delivery orders lang may ETA">—</span><br><small style="color:#999; font-size:0.72rem;">n/a (dine-in)</small>';
    const timeRow = '<div style="display:flex; gap:4px; margin-top:4px; align-items:center;">'
        + '<input type="time" id="etaTime_' + index + '" style="padding:3px 5px; border:1px solid #ccc; border-radius:5px; font-size:0.8rem;">'
        + '<button class="kot-btn" style="padding:4px 8px; font-size:0.72rem;" onclick="setOrderETATime(' + index + ')">Time</button>'
        + '</div>';
    if (o.etaAt) {
        const minsLeft = Math.round((o.etaAt - Date.now()) / 60000);
        const when = minsLeft > 0 ? '(in ~' + minsLeft + ' min)' : '(arriving anytime now)';
        return '<div style="font-size:0.8rem; font-weight:bold;">🕒 ' + formatEtaClock(o.etaAt) + '<br><small style="color:#666;">' + when + '</small></div>'
            + '<div style="display:flex; gap:4px; margin-top:4px; align-items:center;">'
            + '<input type="number" id="etaInput_' + index + '" value="' + (o.etaMinutes || 45) + '" min="5" max="240" step="5" style="width:56px; padding:3px 5px; border:1px solid #ccc; border-radius:5px; font-size:0.8rem;">'
            + '<button class="kot-btn" style="padding:4px 8px; font-size:0.72rem;" onclick="setOrderETA(' + index + ')">Set</button>'
            + '</div>' + timeRow
            + '<button onclick="clearOrderETA(' + index + ')" style="background:none; border:none; color:#999; font-size:0.72rem; cursor:pointer; padding:0; margin-top:2px;">Clear</button>';
    }
    return '<div style="font-size:0.78rem; color:#888; margin-bottom:4px;">No ETA set</div>'
        + '<div style="display:flex; gap:4px; align-items:center;">'
        + '<input type="number" id="etaInput_' + index + '" value="45" min="5" max="240" step="5" style="width:56px; padding:3px 5px; border:1px solid #ccc; border-radius:5px; font-size:0.8rem;">'
        + '<button class="kot-btn" style="padding:4px 8px; font-size:0.72rem;" onclick="setOrderETA(' + index + ')">Set</button>'
        + '</div>' + timeRow;
}

function setOrderETA(index) {
    const input = document.getElementById('etaInput_' + index);
    let mins = parseInt(input && input.value, 10);
    if (!mins || mins < 5) mins = 45;
    if (mins > 240) mins = 240;
    orders[index].etaMinutes = mins;
    orders[index].etaAt = Date.now() + mins*60*1000;
    saveState();
    renderStaffTables();
    renderTVDisplay();
}
function clearOrderETA(index) {
    delete orders[index].etaMinutes;
    delete orders[index].etaAt;
    saveState();
    renderStaffTables();
    renderTVDisplay();
}
function getHeartHtml(orderId, prodName, rev, isAdminView) {
    var count = rev.heartCount || 0;
    var icon = rev.adminLiked ? '❤️' : '🤍';
    if (isAdminView) {
        return '<button onclick="toggleHeartReactReview(this)" data-order-id="' + esc(orderId) + '" data-prod-name="' + esc(prodName) + '" class="heart-btn' + (rev.adminLiked ? ' liked' : '') + '" title="Tap to appreciate">' + icon + ' ' + count + '</button>';
    }
    return '<span class="heart-btn" title="Loved by admin" style="cursor:default;">' + icon + ' ' + count + '</span>';
}

function esc(s) {
    return String(s == null ? '' : s).replace(/&/g, '&amp;').replace(/</g, '&lt;').replace(/>/g, '&gt;').replace(/"/g, '&quot;').replace(/'/g, '&#39;');
}

function setOrderETATime(index) {
    const input = document.getElementById('etaTime_' + index);
    if (!input || !input.value) { alert('Piliin muna ang oras (hal. 14:30).'); return; }
    const parts = input.value.split(':');
    const d = new Date();
    d.setHours(parseInt(parts[0], 10), parseInt(parts[1], 10), 0, 0);
    let mins = Math.round((d.getTime() - Date.now()) / 60000);
    if (mins < 5) { d.setTime(Date.now()); mins = 0; }
    orders[index].etaAt = d.getTime();
    orders[index].etaMinutes = mins;
    saveState();
    renderStaffTables();
    renderTVDisplay();
}

function openCartDrawer() {
    if (!currentUser) {
        alert("Please sign in / login first to view your cart!");
        openModal('signInModal');
        return;
    }
    if (cartDrawer.classList.contains('open')) {
        cartDrawer.classList.remove('open');
        scheduleDrawerCleanup();
        return;
    }
    closeAllDrawersAndModals();
    cartDrawer.classList.add('open');
    overlay.classList.add('active');
    lockBodyScroll(true);
    fitDrawersAboveFooter();
    scheduleDrawerRefit();
}

// Pare-parehong steps saanman ipakita (customer tracking o Live board).
function trackStepsHtml(status) {
    const cur = ORDER_FLOW.findIndex(s => s.value === status);
    let h = '<div class="track-steps">';
    ORDER_FLOW.forEach((s, i) => {
        let cls, mark;
        if (status === 'Completed') { cls = 'done'; mark = '✓'; }
        else if (cur === -1) { cls = 'todo'; mark = i + 1; }
        else if (i < cur) { cls = 'done'; mark = '✓'; }
        else if (i === cur) { cls = 'now'; mark = i + 1; }
        else { cls = 'todo'; mark = i + 1; }
        h += '<div class="track-step ' + cls + '"><span class="track-dot">' + mark + '</span><span>' + s.label + '</span></div>';
    });
    return h + '</div>';
}
// Iisang ETA computation para sa track card at board.
function orderEtaParts(o) {
    if (!isDeliveryOrder(o)) return null;
    if (o.status === 'Completed' || o.status === 'Cancelled') return null;
    if (!o.etaAt) return { pending: true };
    return { clock: formatEtaClock(o.etaAt), minsLeft: Math.round((o.etaAt - Date.now()) / 60000) };
}
function trackEtaLine(o) {
    const p = orderEtaParts(o);
    if (!p) return '';
    if (p.pending) return '<div class="track-eta">⏳ Preparing — ETA to follow</div>';
    if (p.minsLeft > 0) return '<div class="track-eta">🕒 ' + p.clock + ' (in ~' + p.minsLeft + ' min)</div>';
    return '<div class="track-eta hot">🚚 Arriving anytime now</div>';
}
// Iisang rider line saanman ipakita.
function orderRiderLine(o) {
    if (!isDeliveryOrder(o) || !o.riderName) return '';
    return '<div class="track-rider">🛵 <b>Delivery Rider:</b> ' + esc(o.riderName) + ' (' + esc(o.riderContact || '') + ')</div>';
}
function renderTrackView() {
    const activeBox = document.getElementById('trackActiveList');
    const histBox = document.getElementById('trackHistoryList');
    if (!activeBox || !histBox) return;
    const tv = document.getElementById('trackView');
    if (!tv || !tv.classList.contains('active')) return;
    if (!currentUser) {
        activeBox.innerHTML = '<p style="text-align:center; color:#888;">Please sign in to track your orders.</p>';
        histBox.innerHTML = '';
        return;
    }
    const mine = orders.concat(orderHistory)
        .filter(o => (o.userEmail || '').toLowerCase() === (currentUser.email || '').toLowerCase())
        .sort((a, b) => (b.createdAt || 0) - (a.createdAt || 0));
    const live = mine.filter(o => !['Completed', 'Cancelled'].includes(o.status));
    const done = mine.filter(o => ['Completed', 'Cancelled'].includes(o.status));
    if (live.length === 0) {
        activeBox.innerHTML = '<div class="track-card"><p style="color:#888; text-align:center;">No active orders yet. <a href="#" onclick="switchView(\'customer\')" style="color:var(--primary-color); font-weight:bold;">Order now!</a></p></div>';
    } else {
        activeBox.innerHTML = live.map(o => {
            const rider = orderRiderLine(o);
            let btn = '';
            if (o.status === 'Handed To Delivery Rider') {
                btn = '<button class="form-btn track-received-btn" onclick="confirmOrderReceived(\'' + o.id + '\')">Order Received</button>';
            }
            return '<div class="track-card"><div class="track-orderid">' + o.id + '</div>'
                + '<div style="font-size:0.82rem; color:#666; text-align:left;">' + formatItemsHtml(o) + '</div>'
                + '<div style="font-weight:bold; color:var(--primary-color);">Total: P' + o.total.toFixed(2) + '</div>'
                + trackStepsHtml(o.status) + trackEtaLine(o) + rider + btn + '</div>';
        }).join('');
    }
    if (done.length === 0) {
        histBox.innerHTML = '<p style="color:#999; font-size:0.85rem; text-align:center;">No past orders yet.</p>';
    } else {
        histBox.innerHTML = done.map(o => {
            const rev = (o.status === 'Completed')
                ? '<button class="form-btn" style="background:var(--primary-color); width:auto; padding:0.35rem 0.9rem; font-size:0.8rem;" onclick="openProductReviewPage(\'' + o.id + '\')">Review</button>' : '';
            const pill = o.status === 'Completed' ? 'track-pill-done' : 'track-pill-cancel';
            return '<div class="track-history-item"><div><b>' + o.id + '</b> <span class="' + pill + '">' + o.status + '</span><br><small style="color:#777;">' + formatItemsCompact(o) + ' — P' + o.total.toFixed(2) + '</small></div>' + rev + '</div>';
        }).join('');
    }
}
