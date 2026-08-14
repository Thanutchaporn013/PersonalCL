<!DOCTYPE html>
<html lang="th" class="h-full">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    
    <!-- PWA & iOS Mobile Web App Meta Tags -->
    <title>Personal Color & Beauty Ecosystem</title>
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="apple-mobile-web-app-title" content="Color Ecosystem">
    <meta name="theme-color" content="#F9F8F6">
    <meta name="description" content="ระบบนิเวศวิเคราะห์ Personal Color, ลองลิปสติก AR และจับคู่สีเสื้อผ้าสำหรับ iPhone">

    <!-- Fonts: Kanit (Thai) & Inter (English) -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=Kanit:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- Lucide Icons -->
    <script src="https://unpkg.com/lucide@latest"></script>

    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        cream: '#F9F8F6',
                        charcoal: '#1A1A1A',
                        textMuted: '#6E6E73',
                        pastelRose: '#E8C5C8',
                        softBlue: '#B4C6E7',
                        mutedSage: '#C2D8C8',
                        warmGold: '#D4AF37',
                        goldHover: '#B89528',
                        softGray: '#F2F0EC'
                    },
                    fontFamily: {
                        thai: ['Kanit', 'sans-serif'],
                        sans: ['Inter', 'Kanit', 'sans-serif']
                    },
                    boxShadow: {
                        glass: '0 8px 32px 0 rgba(0, 0, 0, 0.06)',
                        glassHover: '0 12px 40px 0 rgba(0, 0, 0, 0.1)',
                        card: '0 4px 20px -2px rgba(26, 26, 26, 0.05)',
                        nav: '0 -4px 20px rgba(0, 0, 0, 0.04)'
                    }
                }
            }
        }
    </script>

    <style>
        :root {
            --sat: env(safe-area-inset-top);
            --sab: env(safe-area-inset-bottom);
        }

        body {
            background-color: #F9F8F6;
            font-family: 'Kanit', 'Inter', sans-serif;
            color: #1A1A1A;
            -webkit-tap-highlight-color: transparent;
            padding-top: max(8px, env(safe-area-inset-top));
            padding-bottom: max(76px, calc(64px + env(safe-area-inset-bottom)));
        }

        .glass-card {
            background: rgba(255, 255, 255, 0.85);
            backdrop-filter: blur(16px);
            -webkit-backdrop-filter: blur(16px);
            border: 1px solid rgba(255, 255, 255, 0.9);
        }

        .glass-pill {
            background: rgba(255, 255, 255, 0.88);
            backdrop-filter: blur(10px);
            -webkit-backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 1);
        }

        .glass-nav {
            background: rgba(255, 255, 255, 0.92);
            backdrop-filter: blur(20px);
            -webkit-backdrop-filter: blur(20px);
            border-top: 1px solid rgba(255, 255, 255, 0.8);
        }

        /* Scanning Laser Keyframe Animation */
        @keyframes scanMove {
            0% { top: 0%; opacity: 0.8; }
            50% { top: 92%; opacity: 1; }
            100% { top: 0%; opacity: 0.8; }
        }

        .scan-line {
            animation: scanMove 2.2s ease-in-out infinite;
        }

        /* Fade and Scale Screen Transitions */
        .fade-in {
            animation: fadeIn 0.35s ease-out forwards;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(6px); }
            to { opacity: 1; transform: translateY(0); }
        }

        /* Shimmer Effect for Primary CTA */
        @keyframes shimmer {
            100% { transform: translateX(100%); }
        }
        
        .shimmer-btn {
            position: relative;
            overflow: hidden;
        }
        .shimmer-btn::after {
            position: absolute;
            top: 0; right: 0; bottom: 0; left: 0;
            transform: translateX(-100%);
            background-image: linear-gradient(
                90deg,
                rgba(255, 255, 255, 0) 0,
                rgba(255, 255, 255, 0.25) 20%,
                rgba(255, 255, 255, 0.5) 60%,
                rgba(255, 255, 255, 0)
            );
            animation: shimmer 2.5s infinite;
            content: '';
        }

        /* Smooth Accordion Height Expansion */
        .accordion-content {
            max-height: 0;
            overflow: hidden;
            transition: max-height 0.35s cubic-bezier(0.4, 0, 0.2, 1);
        }
        .accordion-content.open {
            max-height: 800px;
        }

        /* Hide scrollbars for smooth swiping */
        .no-scrollbar::-webkit-scrollbar {
            display: none;
        }
        .no-scrollbar {
            -ms-overflow-style: none;
            scrollbar-width: none;
        }
    </style>
</head>

<body class="min-h-screen flex flex-col justify-between items-center selection:bg-pastelRose">

    <!-- Toast Notification Overlay -->
    <div id="toast" class="fixed top-5 left-1/2 -translate-x-1/2 z-50 transform -translate-y-16 opacity-0 transition-all duration-300 pointer-events-none w-11/12 max-w-[380px]">
        <div class="glass-card bg-charcoal text-white px-4 py-3 rounded-2xl text-xs font-medium shadow-2xl flex items-center justify-between border border-white/20">
            <div class="flex items-center gap-2">
                <i data-lucide="sparkles" class="w-4 h-4 text-warmGold shrink-0"></i>
                <span id="toast-message">แจ้งเตือนระบบ</span>
            </div>
            <i data-lucide="check-circle" class="w-4 h-4 text-emerald-400 shrink-0"></i>
        </div>
    </div>

    <!-- Main Application Container (iPhone 14 Plus Constraint) -->
    <main class="w-full max-w-[428px] mx-auto px-4 flex-1 flex flex-col justify-start relative pb-6">

        <!-- GLOBAL HEADER BAR -->
        <header class="flex items-center justify-between my-2 pt-1 px-1">
            <div class="flex items-center gap-2">
                <div class="w-8 h-8 rounded-xl bg-charcoal text-white flex items-center justify-center font-bold text-xs shadow-md">
                    PC
                </div>
                <div>
                    <h1 class="text-xs font-bold text-charcoal tracking-tight font-sans uppercase">COLOR LAB ECOSYSTEM</h1>
                    <p class="text-[9px] text-textMuted leading-none font-light">AI Personal Color & Style Studio</p>
                </div>
            </div>

            <!-- Profile Mini Indicator -->
            <button type="button" onclick="switchTab('tab-scanner')" class="glass-pill px-2.5 py-1 rounded-full flex items-center gap-1.5 shadow-sm active:scale-95 transition-all">
                <div class="w-5 h-5 rounded-full overflow-hidden border border-white bg-pastelRose/30 flex items-center justify-center text-[9px] font-bold text-charcoal" id="header-avatar-box">
                    <img id="header-user-avatar" src="https://images.unsplash.com/photo-1534528741775-53994a69daeb?auto=format&fit=crop&w=150&q=80" alt="Avatar" class="w-full h-full object-cover">
                </div>
                <span class="text-[10px] font-semibold text-charcoal" id="header-season-tag">✨ Light Summer</span>
            </button>
        </header>

        <!-- ========================================== -->
        <!-- TAB 1: HOME (DASHBOARD) -->
        <!-- ========================================== -->
        <section id="tab-home" class="tab-page fade-in flex flex-col gap-4 mt-1">
            
            <!-- Daily Palette & Aura Recommendation Widget -->
            <div class="glass-card rounded-3xl p-4 shadow-glass border border-white/90 relative overflow-hidden">
                <div class="absolute -top-10 -right-10 w-32 h-32 rounded-full bg-softBlue/30 blur-2xl pointer-events-none"></div>

                <div class="flex justify-between items-start mb-2">
                    <div class="flex items-center gap-1.5">
                        <span class="px-2 py-0.5 rounded-full bg-softBlue/40 text-charcoal text-[9px] font-bold uppercase">TODAY'S PALETTE</span>
                        <span class="text-[10px] text-textMuted font-mono">14 AUG 2026</span>
                    </div>
                    <i data-lucide="sun-medium" class="w-4 h-4 text-warmGold"></i>
                </div>

                <h2 class="text-base font-bold text-charcoal leading-snug">
                    วันนี้เหมาะกับสีฟ้า พาสเทล 🌤️<br>
                    <span class="text-xs font-normal text-charcoal/80">ช่วยขับออร่าให้ผิวดูสดใส สบายตา และมีสมาธิ</span>
                </h2>

                <!-- Daily Color Swatches -->
                <div class="mt-3 pt-3 border-t border-charcoal/5 flex items-center justify-between">
                    <span class="text-[10px] text-textMuted">สีมงคลประจำวัน:</span>
                    <div class="flex items-center gap-2">
                        <div class="flex items-center gap-1 bg-white/70 px-2 py-1 rounded-xl border border-white shadow-sm">
                            <span class="w-3.5 h-3.5 rounded-full bg-[#B0C4DE] border border-black/10"></span>
                            <span class="text-[10px] font-medium text-charcoal">Powder Blue</span>
                        </div>
                        <div class="flex items-center gap-1 bg-white/70 px-2 py-1 rounded-xl border border-white shadow-sm">
                            <span class="w-3.5 h-3.5 rounded-full bg-[#E8C5C8] border border-black/10"></span>
                            <span class="text-[10px] font-medium text-charcoal">Soft Pink</span>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Profile Season Status Card -->
            <div class="glass-card rounded-3xl p-4 shadow-card border border-white/90 flex items-center justify-between">
                <div class="flex items-center gap-3">
                    <div class="w-14 h-14 rounded-2xl overflow-hidden border-2 border-white shadow-md relative shrink-0">
                        <img id="home-user-img" src="https://images.unsplash.com/photo-1534528741775-53994a69daeb?auto=format&fit=crop&w=300&q=80" alt="Profile" class="w-full h-full object-cover">
                    </div>
                    <div>
                        <div class="inline-flex items-center gap-1 px-2 py-0.5 rounded-full text-[9px] font-bold bg-pastelRose/40 text-charcoal border border-pastelRose/50" id="home-season-badge">
                            ✨ LIGHT SUMMER
                        </div>
                        <h3 class="text-sm font-bold text-charcoal mt-0.5" id="home-season-title">ไลท์ ซัมเมอร์ (Light Summer)</h3>
                        <p class="text-[10px] text-textMuted">Undertone: Cool • Contrast: Medium</p>
                    </div>
                </div>
                <button type="button" onclick="switchTab('tab-scanner')" class="p-2 rounded-xl bg-charcoal text-white hover:bg-black transition-all shadow-sm shrink-0">
                    <i data-lucide="scan" class="w-4 h-4"></i>
                </button>
            </div>

            <!-- Quick Action Shortcuts -->
            <div>
                <h3 class="text-xs font-bold text-charcoal/70 uppercase tracking-wider mb-2 px-1">ทางลัดใช้งานเร็ว (QUICK ACTIONS)</h3>
                <div class="grid grid-cols-3 gap-2">
                    <button type="button" onclick="switchTab('tab-scanner')" class="glass-card p-3 rounded-2xl flex flex-col items-center gap-2 hover:bg-white transition-all text-center group border border-white">
                        <div class="w-9 h-9 rounded-xl bg-pastelRose/30 flex items-center justify-center text-charcoal group-hover:scale-110 transition-transform">
                            <i data-lucide="camera" class="w-4 h-4"></i>
                        </div>
                        <span class="text-[11px] font-semibold text-charcoal">สแกนสีผิว AI</span>
                    </button>

                    <button type="button" onclick="switchTab('tab-beauty')" class="glass-card p-3 rounded-2xl flex flex-col items-center gap-2 hover:bg-white transition-all text-center group border border-white">
                        <div class="w-9 h-9 rounded-xl bg-softBlue/30 flex items-center justify-center text-charcoal group-hover:scale-110 transition-transform">
                            <i data-lucide="sparkles" class="w-4 h-4"></i>
                        </div>
                        <span class="text-[11px] font-semibold text-charcoal">ลองลิปสติก AR</span>
                    </button>

                    <button type="button" onclick="switchTab('tab-wardrobe')" class="glass-card p-3 rounded-2xl flex flex-col items-center gap-2 hover:bg-white transition-all text-center group border border-white">
                        <div class="w-9 h-9 rounded-xl bg-mutedSage/30 flex items-center justify-center text-charcoal group-hover:scale-110 transition-transform">
                            <i data-lucide="shirt" class="w-4 h-4"></i>
                        </div>
                        <span class="text-[11px] font-semibold text-charcoal">เช็กสีเสื้อผ้า</span>
                    </button>
                </div>
            </div>

            <!-- Style Tips Feed -->
            <div>
                <div class="flex items-center justify-between mb-2 px-1">
                    <h3 class="text-xs font-bold text-charcoal/70 uppercase tracking-wider">บทความแนะนำ (BEAUTY & STYLE TIPS)</h3>
                    <span class="text-[10px] text-warmGold font-medium">อ่านทั้งหมด</span>
                </div>

                <div class="space-y-2.5">
                    <div class="glass-card p-3 rounded-2xl border border-white flex items-center gap-3 shadow-sm hover:shadow-md transition-all cursor-pointer" onclick="showTipModal('jewelry')">
                        <div class="w-16 h-16 rounded-xl bg-amber-100 overflow-hidden shrink-0 relative">
                            <img src="https://images.unsplash.com/photo-1599643478518-a784e5dc4c8f?auto=format&fit=crop&w=200&q=80" alt="Jewelry Tip" class="w-full h-full object-cover">
                        </div>
                        <div class="flex-1">
                            <span class="text-[9px] font-bold text-warmGold uppercase">JEWELRY GUIDE</span>
                            <h4 class="text-xs font-bold text-charcoal leading-tight mt-0.5">3 เทคนิคเลือกเครื่องประดับเงิน vs ทอง ให้เข้ากับ Undertone</h4>
                            <p class="text-[10px] text-textMuted line-clamp-1 mt-0.5">เคล็ดลับเช็กผิวว่าควรใส่เครื่องประดับสีไหนแล้วหน้าไม่หมอง</p>
                        </div>
                        <i data-lucide="chevron-right" class="w-4 h-4 text-charcoal/30 shrink-0"></i>
                    </div>

                    <div class="glass-card p-3 rounded-2xl border border-white flex items-center gap-3 shadow-sm hover:shadow-md transition-all cursor-pointer" onclick="showTipModal('hair')">
                        <div class="w-16 h-16 rounded-xl bg-rose-100 overflow-hidden shrink-0 relative">
                            <img src="https://images.unsplash.com/photo-1522337360788-8b13dee7a37e?auto=format&fit=crop&w=200&q=80" alt="Hair Color Tip" class="w-full h-full object-cover">
                        </div>
                        <div class="flex-1">
                            <span class="text-[9px] font-bold text-pastelRose uppercase">HAIR COLOR TREND</span>
                            <h4 class="text-xs font-bold text-charcoal leading-tight mt-0.5">โทนสีผม Milk Tea Brown เหมาะกับฤดูไหนบ้าง?</h4>
                            <p class="text-[10px] text-textMuted line-clamp-1 mt-0.5">เจาะลึกสีผมชานมยอดฮิต ทำแล้วขับผิวไบรท์สว่างใส</p>
                        </div>
                        <i data-lucide="chevron-right" class="w-4 h-4 text-charcoal/30 shrink-0"></i>
                    </div>
                </div>
            </div>

        </section>

        <!-- ========================================== -->
        <!-- TAB 2: SCANNER (AI PHOTO ANALYSIS ENGINE) -->
        <!-- ========================================== -->
        <section id="tab-scanner" class="tab-page hidden fade-in flex-col gap-4 mt-1">
            
            <div id="upload-section" class="flex flex-col gap-4">
                <!-- Lighting Guide Box -->
                <div class="glass-card rounded-2xl p-3.5 shadow-card border border-white/90">
                    <div class="flex items-center gap-2 mb-2">
                        <div class="p-1.5 rounded-lg bg-warmGold/15 text-warmGold">
                            <i data-lucide="sun" class="w-4 h-4"></i>
                        </div>
                        <h2 class="text-xs font-semibold text-charcoal tracking-wide">คำแนะนำเพื่อผลลัพธ์ที่แม่นยำที่สุด</h2>
                    </div>
                    <p class="text-[11px] text-textMuted mb-2.5">ถ่ายในแสงธรรมชาติ ไม่แต่งหน้าหนา เพื่อความแม่นยำสูงสุด</p>
                    
                    <div class="grid grid-cols-3 gap-2 text-[11px] text-charcoal/80 text-center">
                        <div class="bg-white/60 p-2 rounded-xl flex flex-col items-center gap-1 border border-white">
                            <i data-lucide="sun-medium" class="w-3.5 h-3.5 text-warmGold"></i>
                            <span>แสงธรรมชาติ</span>
                        </div>
                        <div class="bg-white/60 p-2 rounded-xl flex flex-col items-center gap-1 border border-white">
                            <i data-lucide="sparkles" class="w-3.5 h-3.5 text-pastelRose"></i>
                            <span>หน้าสด/แต่งบาง</span>
                        </div>
                        <div class="bg-white/60 p-2 rounded-xl flex flex-col items-center gap-1 border border-white">
                            <i data-lucide="square" class="w-3.5 h-3.5 text-mutedSage"></i>
                            <span>พื้นหลังเรียบ</span>
                        </div>
                    </div>
                </div>

                <!-- Interactive Upload Box -->
                <div class="relative group">
                    <div id="drop-zone" onclick="triggerFileInput(event)" class="glass-card rounded-3xl p-5 text-center border-2 border-dashed border-charcoal/15 hover:border-warmGold/60 transition-all duration-300 relative overflow-hidden flex flex-col items-center justify-center min-h-[300px] cursor-pointer shadow-glass">
                        
                        <input type="file" id="photo-input" accept="image/*" class="hidden" onchange="handleFileSelect(event)">

                        <!-- Placeholder View -->
                        <div id="upload-placeholder" class="flex flex-col items-center justify-center gap-3 py-2">
                            <div class="w-20 h-20 rounded-full bg-softGray/80 flex items-center justify-center relative shadow-inner border border-white">
                                <i data-lucide="user" class="w-10 h-10 text-charcoal/30"></i>
                                <div class="absolute bottom-0 right-0 p-2 bg-charcoal text-white rounded-full shadow-md">
                                    <i data-lucide="camera" class="w-4 h-4"></i>
                                </div>
                            </div>
                            <div class="space-y-1">
                                <p class="text-sm font-medium text-charcoal">แตะเพื่ออัปโหลด หรือลากรูปภาพมาวาง</p>
                                <p class="text-[11px] text-textMuted">รองรับไฟล์ JPG, PNG (แนะนำแนวตั้ง)</p>
                            </div>

                            <!-- Sample Photos Quick Selector -->
                            <div class="mt-2 pt-3 border-t border-charcoal/5 w-full">
                                <p class="text-[10px] text-textMuted mb-2">หรือลองใช้ภาพตัวอย่างนี้:</p>
                                <div class="flex justify-center gap-2">
                                    <button type="button" onclick="loadSamplePhoto('light-summer', event)" class="sample-btn px-2.5 py-1 rounded-full text-[10px] bg-white/80 border border-charcoal/10 hover:border-pastelRose hover:bg-pastelRose/10 transition-all flex items-center gap-1">
                                        <span class="w-2 h-2 rounded-full bg-pastelRose"></span>
                                        <span>Light Summer</span>
                                    </button>
                                    <button type="button" onclick="loadSamplePhoto('warm-autumn', event)" class="sample-btn px-2.5 py-1 rounded-full text-[10px] bg-white/80 border border-charcoal/10 hover:border-warmGold hover:bg-warmGold/10 transition-all flex items-center gap-1">
                                        <span class="w-2 h-2 rounded-full bg-warmGold"></span>
                                        <span>Warm Autumn</span>
                                    </button>
                                    <button type="button" onclick="loadSamplePhoto('bright-spring', event)" class="sample-btn px-2.5 py-1 rounded-full text-[10px] bg-white/80 border border-charcoal/10 hover:border-softBlue hover:bg-softBlue/10 transition-all flex items-center gap-1">
                                        <span class="w-2 h-2 rounded-full bg-softBlue"></span>
                                        <span>Bright Spring</span>
                                    </button>
                                </div>
                            </div>
                        </div>

                        <!-- Image Preview View -->
                        <div id="image-preview-container" class="hidden absolute inset-0 w-full h-full bg-charcoal/5 flex items-center justify-center overflow-hidden">
                            <img id="preview-image" src="" alt="User Portrait" class="w-full h-full object-cover">
                            
                            <div class="absolute inset-0 border-[2px] border-white/40 rounded-3xl pointer-events-none flex items-center justify-center">
                                <div class="w-[62%] h-[72%] border border-dashed border-white/70 rounded-[50%] flex items-center justify-center opacity-70">
                                    <span class="text-[9px] text-white bg-black/40 px-2 py-0.5 rounded-full backdrop-blur-sm">วางใบหน้าในกรอบ</span>
                                </div>
                            </div>

                            <button type="button" onclick="clearPhoto(event)" id="change-photo-btn" class="absolute top-3 right-3 glass-pill px-2.5 py-1 rounded-full text-[11px] font-medium text-charcoal shadow-md flex items-center gap-1 hover:bg-white transition-all">
                                <i data-lucide="refresh-cw" class="w-3 h-3"></i>
                                <span>เปลี่ยนรูป</span>
                            </button>
                        </div>

                    </div>
                </div>

                <div class="grid grid-cols-2 gap-2.5">
                    <button type="button" onclick="triggerCameraInput()" class="glass-card py-3 px-4 rounded-2xl flex items-center justify-center gap-2 text-xs font-semibold text-charcoal hover:bg-white/90 active:scale-[0.98] transition-all shadow-sm">
                        <i data-lucide="camera" class="w-4 h-4 text-warmGold"></i>
                        <span>ถ่ายรูปด้วยกล้อง</span>
                    </button>
                    <button type="button" onclick="triggerFileInput()" class="glass-card py-3 px-4 rounded-2xl flex items-center justify-center gap-2 text-xs font-semibold text-charcoal hover:bg-white/90 active:scale-[0.98] transition-all shadow-sm">
                        <i data-lucide="image" class="w-4 h-4 text-pastelRose"></i>
                        <span>เลือกรูปจากคลัง</span>
                    </button>
                </div>

                <button type="button" id="analyze-btn" disabled onclick="startAnalysis()" class="shimmer-btn w-full py-4 px-6 rounded-2xl bg-charcoal text-white font-semibold text-sm tracking-wider shadow-lg disabled:opacity-50 disabled:cursor-not-allowed hover:bg-black active:scale-[0.99] transition-all flex items-center justify-center gap-2 mt-1">
                    <span>เริ่มวิเคราะห์เชิงลึก (ANALYZE NOW)</span>
                    <i data-lucide="sparkles" class="w-4 h-4 text-warmGold"></i>
                </button>
            </div>

            <!-- Diagnostic Report View -->
            <div id="report-section" class="hidden flex-col gap-4 transition-all duration-700">
                
                <div class="flex items-center justify-between px-1">
                    <button type="button" onclick="resetAnalysis()" class="text-xs font-medium text-charcoal/80 hover:text-charcoal flex items-center gap-1.5 glass-pill px-3.5 py-1.5 rounded-full shadow-sm">
                        <i data-lucide="arrow-left" class="w-3.5 h-3.5"></i>
                        <span>วิเคราะห์รูปภาพใหม่</span>
                    </button>
                    
                    <div class="flex items-center gap-1">
                        <label for="season-select" class="text-[10px] text-textMuted">ปรับฤดู:</label>
                        <select id="season-select" onchange="handleSeasonChange(event)" class="text-xs font-semibold text-charcoal bg-white/90 border border-white/80 rounded-full px-2.5 py-1 focus:outline-none shadow-sm cursor-pointer">
                            <option value="LIGHT_SUMMER" selected>✨ Light Summer</option>
                            <option value="WARM_AUTUMN">🍂 Warm Autumn</option>
                            <option value="BRIGHT_SPRING">🌸 Bright Spring</option>
                            <option value="COOL_WINTER">❄️ Cool Winter</option>
                        </select>
                    </div>
                </div>

                <!-- Personal Color Overview Card -->
                <div class="glass-card rounded-3xl p-4 shadow-glass border border-white/90 relative overflow-hidden">
                    <div id="season-glow-bg" class="absolute -top-12 -right-12 w-40 h-40 rounded-full bg-pastelRose/30 blur-2xl pointer-events-none"></div>

                    <div class="flex items-start gap-4">
                        <div class="relative flex-shrink-0">
                            <div class="w-20 h-20 rounded-2xl overflow-hidden border-2 border-white shadow-md">
                                <img id="report-user-img" src="" alt="Diagnosis Portrait" class="w-full h-full object-cover">
                            </div>
                            <span class="absolute -bottom-2 -right-1 text-[9px] font-bold bg-charcoal text-white px-2 py-0.5 rounded-full shadow-sm border border-white">AI VERIFIED</span>
                        </div>

                        <div class="flex-1 space-y-1">
                            <div class="inline-flex items-center gap-1 px-2.5 py-0.5 rounded-full text-[10px] font-bold bg-pastelRose/40 text-charcoal border border-pastelRose/50" id="report-season-badge">
                                <i data-lucide="sparkles" class="w-3 h-3 text-warmGold"></i>
                                <span id="report-season-name">✨ LIGHT SUMMER</span>
                            </div>
                            <h2 class="text-lg font-bold text-charcoal leading-tight font-sans" id="report-season-title">ไลท์ ซัมเมอร์</h2>
                            <p class="text-[11px] text-charcoal/80 font-light leading-snug" id="report-season-summary">
                                โทนสีสว่าง นุ่มนวล ละมุน ขับให้ออร่าผิวดูไบรท์ขึ้นและกระจ่างใสอย่างธรรมชาติ
                            </p>
                        </div>
                    </div>

                    <div class="mt-4 pt-3 border-t border-charcoal/5 flex items-center justify-between text-[11px]">
                        <span class="text-textMuted">อุณหภูมิหลัก: <strong id="summary-undertone" class="text-charcoal font-medium">Cool Undertone</strong></span>
                        <span class="text-textMuted">ความสว่าง: <strong id="summary-lightness" class="text-charcoal font-medium">High Lightness</strong></span>
                    </div>
                </div>

                <!-- Facial & Skin Metrics Breakdown Grid -->
                <div>
                    <h3 class="text-xs font-bold text-charcoal/70 uppercase tracking-wider mb-2 px-1 flex items-center gap-1.5">
                        <i data-lucide="sliders" class="w-3.5 h-3.5 text-warmGold"></i>
                        <span>ผลการวิเคราะห์มิติผิวและใบหน้า</span>
                    </h3>

                    <div class="grid grid-cols-2 gap-2.5">
                        <div class="glass-card p-3.5 rounded-2xl border border-white/80 shadow-sm flex flex-col justify-between">
                            <div>
                                <div class="flex justify-between items-start mb-1">
                                    <span class="text-[10px] text-textMuted font-medium">UNDERTONE</span>
                                    <i data-lucide="droplet" class="w-3.5 h-3.5 text-pastelRose"></i>
                                </div>
                                <p class="text-sm font-bold text-charcoal" id="metric-undertone-val">Cool (ชมพู/น้ำเงิน)</p>
                                <p class="text-[10px] text-textMuted mt-0.5" id="metric-undertone-desc">เส้นเลือดน้ำเงิน-ม่วง มีอมชมพู</p>
                            </div>
                            <div class="mt-2.5 pt-2 border-t border-charcoal/5">
                                <div class="w-full bg-softGray h-1.5 rounded-full overflow-hidden flex">
                                    <div id="cool-bar" class="bg-blue-400 h-full" style="width: 70%"></div>
                                    <div id="warm-bar" class="bg-amber-400 h-full" style="width: 30%"></div>
                                </div>
                                <div class="flex justify-between text-[8px] text-textMuted mt-1 font-mono">
                                    <span id="cool-ratio-text">COOL 70%</span>
                                    <span id="warm-ratio-text">WARM 30%</span>
                                </div>
                            </div>
                        </div>

                        <div class="glass-card p-3.5 rounded-2xl border border-white/80 shadow-sm flex flex-col justify-between">
                            <div>
                                <div class="flex justify-between items-start mb-1">
                                    <span class="text-[10px] text-textMuted font-medium">BRIGHTNESS</span>
                                    <i data-lucide="sun" class="w-3.5 h-3.5 text-warmGold"></i>
                                </div>
                                <p class="text-sm font-bold text-charcoal" id="metric-value-val">High (สว่างสดใส)</p>
                                <p class="text-[10px] text-textMuted mt-0.5" id="metric-value-desc">ระดับความสว่างผิวสูง โปร่งแสง</p>
                            </div>
                            <div class="mt-2.5 pt-2 border-t border-charcoal/5">
                                <div class="w-full bg-gradient-to-r from-charcoal via-amber-200 to-white h-1.5 rounded-full border border-black/10"></div>
                                <div class="flex justify-between text-[8px] text-textMuted mt-1 font-mono">
                                    <span>LOW</span>
                                    <span>MED</span>
                                    <span>HIGH ✓</span>
                                </div>
                            </div>
                        </div>

                        <div class="glass-card p-3.5 rounded-2xl border border-white/80 shadow-sm flex flex-col justify-between">
                            <div>
                                <div class="flex justify-between items-start mb-1">
                                    <span class="text-[10px] text-textMuted font-medium">CONTRAST LEVEL</span>
                                    <i data-lucide="contrast" class="w-3.5 h-3.5 text-mutedSage"></i>
                                </div>
                                <p class="text-sm font-bold text-charcoal" id="metric-contrast-val">Medium (ปานกลาง)</p>
                                <p class="text-[10px] text-textMuted mt-0.5" id="metric-contrast-desc">สีผม ตา และผิว ละมุนใกล้เคียงกัน</p>
                            </div>
                            <div class="mt-2.5 pt-2 border-t border-charcoal/5">
                                <div class="w-full bg-softGray h-1.5 rounded-full overflow-hidden relative">
                                    <div class="absolute left-[45%] w-3 h-full bg-charcoal rounded-full"></div>
                                </div>
                                <div class="flex justify-between text-[8px] text-textMuted mt-1 font-mono">
                                    <span>SOFT</span>
                                    <span>MEDIUM</span>
                                    <span>HIGH</span>
                                </div>
                            </div>
                        </div>

                        <div class="glass-card p-3.5 rounded-2xl border border-white/80 shadow-sm flex flex-col justify-between">
                            <div>
                                <div class="flex justify-between items-start mb-1">
                                    <span class="text-[10px] text-textMuted font-medium">FACE SHAPE</span>
                                    <i data-lucide="scan-face" class="w-3.5 h-3.5 text-charcoal"></i>
                                </div>
                                <p class="text-sm font-bold text-charcoal" id="metric-faceshape-val">Oval (หน้ารูปไข่)</p>
                                <p class="text-[10px] text-textMuted mt-0.5" id="metric-faceshape-desc">โหนกแก้มได้รูป คางมนสวย</p>
                            </div>
                            <div class="mt-2.5 pt-2 border-t border-charcoal/5 text-[9px] text-textMuted">
                                <span>สัดส่วนสมดุล: 1 : 1.5</span>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Best & Worst Color Swatches + Live Draping -->
                <div class="glass-card rounded-3xl p-4 shadow-glass border border-white/90 space-y-4">
                    <div>
                        <h3 class="text-xs font-bold text-charcoal flex items-center gap-1.5">
                            <i data-lucide="palette" class="w-4 h-4 text-pastelRose"></i>
                            <span>จานสีที่ใช่สำหรับคุณ (BEST COLORS)</span>
                        </h3>
                        <p class="text-[10px] text-textMuted">แตะที่เฉดสีเพื่อทดลองเทียบโทนสีผิว</p>
                    </div>

                    <div id="best-colors-container" class="grid grid-cols-5 gap-2"></div>

                    <!-- Live Interactive Draping Box -->
                    <div class="bg-white/70 rounded-2xl p-3 border border-white flex items-center gap-3 shadow-inner">
                        <div id="drape-preview-circle" class="w-12 h-12 rounded-full border-2 border-white shadow-md flex-shrink-0 transition-all duration-300 relative overflow-hidden" style="background-color: #E8C5C8;">
                            <img id="drape-user-thumb" src="" alt="Thumbnail" class="w-full h-full object-cover opacity-85">
                        </div>
                        <div class="flex-1 text-xs">
                            <div class="text-[9px] text-textMuted uppercase font-mono">LIVE DRAPING PREVIEW</div>
                            <div id="drape-color-name" class="font-bold text-charcoal text-xs">Pastel Rose</div>
                            <div id="drape-color-hex" class="text-[9px] font-mono text-textMuted">#E8C5C8</div>
                        </div>
                        <div class="text-[9px] text-textMuted bg-white px-2 py-1 rounded-lg border border-charcoal/5">
                            แตะสีด้านบนเพื่อลอง
                        </div>
                    </div>

                    <div class="pt-3 border-t border-charcoal/5">
                        <h4 class="text-xs font-bold text-red-800/80 mb-1 flex items-center gap-1">
                            <i data-lucide="alert-triangle" class="w-3.5 h-3.5 text-red-500"></i>
                            <span>สีที่ควรหลีกเลี่ยง (WORST COLORS TO AVOID)</span>
                        </h4>
                        <div id="worst-colors-container" class="grid grid-cols-4 gap-2 mt-2"></div>
                    </div>
                </div>

                <!-- Style & Beauty Recommendation Accordions -->
                <div class="space-y-2">
                    <h3 class="text-xs font-bold text-charcoal/70 uppercase tracking-wider px-1">คำแนะนำสไตล์เฉพาะบุคคล</h3>

                    <!-- Makeup Accordion -->
                    <div class="glass-card rounded-2xl overflow-hidden border border-white/90 shadow-sm">
                        <button type="button" onclick="toggleAccordion(this)" class="accordion-header w-full p-3.5 text-left font-semibold text-xs text-charcoal flex items-center justify-between hover:bg-white/40 transition-all">
                            <div class="flex items-center gap-2">
                                <div class="p-1 rounded-lg bg-pastelRose/30 text-charcoal">
                                    <i data-lucide="heart" class="w-3.5 h-3.5"></i>
                                </div>
                                <span class="font-bold">💄 การแต่งหน้า (Makeup Guide)</span>
                            </div>
                            <i data-lucide="chevron-down" class="accordion-icon w-4 h-4 text-charcoal/40 transition-transform duration-300"></i>
                        </button>
                        <div class="accordion-content">
                            <div class="p-3.5 pt-0 border-t border-charcoal/5 space-y-2 text-xs text-charcoal/80">
                                <div>
                                    <strong class="text-charcoal block mb-0.5">💋 เฉดลิปสติกที่แนะนำ:</strong>
                                    <p id="rec-lipstick" class="text-[11px] font-light bg-white/60 p-2 rounded-xl border border-white">
                                        Soft Pink, Dusty Rose, Coral Pink เบาๆ เนื้อ Gloss หรือ Velvet Matte
                                    </p>
                                </div>
                                <div>
                                    <strong class="text-charcoal block mb-0.5">🌸 บลัชออน & อายแชโดว์:</strong>
                                    <p id="rec-blush" class="text-[11px] font-light bg-white/60 p-2 rounded-xl border border-white">
                                        ชมพูนม ปัดแก้มระเรื่อ โทนตานู้ดพาสเทล ชิมเมอร์เม็ดละเอียด
                                    </p>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- Hair Accordion -->
                    <div class="glass-card rounded-2xl overflow-hidden border border-white/90 shadow-sm">
                        <button type="button" onclick="toggleAccordion(this)" class="accordion-header w-full p-3.5 text-left font-semibold text-xs text-charcoal flex items-center justify-between hover:bg-white/40 transition-all">
                            <div class="flex items-center gap-2">
                                <div class="p-1 rounded-lg bg-warmGold/20 text-charcoal">
                                    <i data-lucide="scissors" class="w-3.5 h-3.5"></i>
                                </div>
                                <span class="font-bold">💇‍♀️ สีผมและทรงผม (Hair Style)</span>
                            </div>
                            <i data-lucide="chevron-down" class="accordion-icon w-4 h-4 text-charcoal/40 transition-transform duration-300"></i>
                        </button>
                        <div class="accordion-content">
                            <div class="p-3.5 pt-0 border-t border-charcoal/5 space-y-2 text-xs text-charcoal/80">
                                <div>
                                    <strong class="text-charcoal block mb-0.5">🎨 สีผมขับผิว:</strong>
                                    <p id="rec-haircolor" class="text-[11px] font-light bg-white/60 p-2 rounded-xl border border-white">
                                        Ash Brown, Milk Tea Brown, Rose Brown น้ำตาลหม่นประกายเทา
                                    </p>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- Fashion Accordion -->
                    <div class="glass-card rounded-2xl overflow-hidden border border-white/90 shadow-sm">
                        <button type="button" onclick="toggleAccordion(this)" class="accordion-header w-full p-3.5 text-left font-semibold text-xs text-charcoal flex items-center justify-between hover:bg-white/40 transition-all">
                            <div class="flex items-center gap-2">
                                <div class="p-1 rounded-lg bg-mutedSage/30 text-charcoal">
                                    <i data-lucide="shirt" class="w-3.5 h-3.5"></i>
                                </div>
                                <span class="font-bold">👗 เครื่องแต่งกาย & โลหะ (Fashion)</span>
                            </div>
                            <i data-lucide="chevron-down" class="accordion-icon w-4 h-4 text-charcoal/40 transition-transform duration-300"></i>
                        </button>
                        <div class="accordion-content">
                            <div class="p-3.5 pt-0 border-t border-charcoal/5 space-y-2 text-xs text-charcoal/80">
                                <div class="grid grid-cols-3 gap-1.5 text-center text-[9px]">
                                    <div class="bg-white/70 p-2 rounded-xl border border-white flex flex-col items-center">
                                        <span class="font-bold text-charcoal">Silver (เงิน)</span>
                                        <span id="metal-silver-badge" class="text-[8px] text-emerald-600 font-semibold">แนะนำที่สุด ★</span>
                                    </div>
                                    <div class="bg-white/70 p-2 rounded-xl border border-white flex flex-col items-center">
                                        <span class="font-bold text-charcoal">Rose Gold</span>
                                        <span id="metal-rosegold-badge" class="text-[8px] text-emerald-600 font-semibold">เข้ากันได้ดี ✓</span>
                                    </div>
                                    <div class="bg-white/70 p-2 rounded-xl border border-white flex flex-col items-center opacity-60">
                                        <span class="font-bold text-charcoal">Yellow Gold</span>
                                        <span id="metal-gold-badge" class="text-[8px] text-textMuted">ปานกลาง</span>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>

            </div>
        </section>

        <!-- ========================================== -->
        <!-- TAB 3: WARDROBE (STYLE STUDIO & MATCH) -->
        <!-- ========================================== -->
        <section id="tab-wardrobe" class="tab-page hidden fade-in flex-col gap-4 mt-1">
            
            <!-- Seasonal Color Palette Explorer -->
            <div class="glass-card rounded-3xl p-4 shadow-glass border border-white/90">
                <div class="flex items-center justify-between mb-2">
                    <h2 class="text-xs font-bold text-charcoal uppercase tracking-wider flex items-center gap-1.5">
                        <i data-lucide="palette" class="w-4 h-4 text-warmGold"></i>
                        <span>สำรวจจานสีตู้เสื้อผ้า (SEASON PALETTES)</span>
                    </h2>
                    <span class="text-[10px] text-textMuted font-mono">16-SEASON SYSTEM</span>
                </div>

                <!-- Season Category Selector Tabs -->
                <div class="flex items-center gap-1 bg-softGray/80 p-1 rounded-2xl border border-black/5 overflow-x-auto no-scrollbar mb-3">
                    <button type="button" onclick="filterWardrobeSeason('SPRING')" id="season-tab-SPRING" class="wardrobe-season-btn px-3 py-1.5 rounded-xl text-[11px] font-medium text-charcoal/70 transition-all flex-1 text-center whitespace-nowrap">
                        🌸 Spring
                    </button>
                    <button type="button" onclick="filterWardrobeSeason('SUMMER')" id="season-tab-SUMMER" class="wardrobe-season-btn px-3 py-1.5 rounded-xl text-[11px] font-bold bg-white text-charcoal shadow-sm transition-all flex-1 text-center whitespace-nowrap">
                        ✨ Summer
                    </button>
                    <button type="button" onclick="filterWardrobeSeason('AUTUMN')" id="season-tab-AUTUMN" class="wardrobe-season-btn px-3 py-1.5 rounded-xl text-[11px] font-medium text-charcoal/70 transition-all flex-1 text-center whitespace-nowrap">
                        🍂 Autumn
                    </button>
                    <button type="button" onclick="filterWardrobeSeason('WINTER')" id="season-tab-WINTER" class="wardrobe-season-btn px-3 py-1.5 rounded-xl text-[11px] font-medium text-charcoal/70 transition-all flex-1 text-center whitespace-nowrap">
                        ❄️ Winter
                    </button>
                </div>

                <!-- Seasonal Palette Grid -->
                <div id="wardrobe-swatch-grid" class="grid grid-cols-6 gap-2">
                    <!-- Dynamic swatches rendered via JS -->
                </div>
            </div>

            <!-- Clothes Color Matcher Tool -->
            <div class="glass-card rounded-3xl p-4 shadow-glass border border-white/90 space-y-3">
                <div class="flex items-center justify-between">
                    <h3 class="text-xs font-bold text-charcoal uppercase tracking-wider flex items-center gap-1.5">
                        <i data-lucide="sparkles" class="w-4 h-4 text-pastelRose"></i>
                        <span>เครื่องมือแมตช์สีเสื้อผ้า (CLOTHES MATCH)</span>
                    </h3>
                    <span class="text-[9px] bg-warmGold/20 text-charcoal font-bold px-2 py-0.5 rounded-full">AI SIMULATOR</span>
                </div>

                <p class="text-[11px] text-textMuted">เลือกสีเสื้อท่อนบนและท่อนอ่าง เพื่อคำนวณ Match Score</p>

                <!-- Top & Bottom Selector Grid -->
                <div class="grid grid-cols-2 gap-2.5 pt-1">
                    <!-- Top Piece Picker -->
                    <div class="bg-white/70 p-3 rounded-2xl border border-white flex flex-col items-center gap-2 shadow-sm">
                        <span class="text-[10px] font-bold text-charcoal">👕 เสื้อท่อนบน (TOP)</span>
                        <div id="top-color-preview" class="w-12 h-12 rounded-xl shadow-inner border border-black/10 transition-all" style="background-color: #B0C4DE;"></div>
                        <select id="top-color-select" onchange="calculateClothMatch()" class="text-[10px] font-medium text-charcoal bg-white border border-charcoal/10 rounded-xl px-2 py-1 w-full focus:outline-none cursor-pointer">
                            <option value="#B0C4DE" selected>Powder Blue (#B0C4DE)</option>
                            <option value="#E8C5C8">Pastel Rose (#E8C5C8)</option>
                            <option value="#FFFFFF">Pure White (#FFFFFF)</option>
                            <option value="#1A1A1A">Charcoal Black (#1A1A1A)</option>
                            <option value="#E2725B">Terracotta (#E2725B)</option>
                        </select>
                    </div>

                    <!-- Bottom Piece Picker -->
                    <div class="bg-white/70 p-3 rounded-2xl border border-white flex flex-col items-center gap-2 shadow-sm">
                        <span class="text-[10px] font-bold text-charcoal">👖 กางเกง/กระโปรง (BOTTOM)</span>
                        <div id="bottom-color-preview" class="w-12 h-12 rounded-xl shadow-inner border border-black/10 transition-all" style="background-color: #DCDCDC;"></div>
                        <select id="bottom-color-select" onchange="calculateClothMatch()" class="text-[10px] font-medium text-charcoal bg-white border border-charcoal/10 rounded-xl px-2 py-1 w-full focus:outline-none cursor-pointer">
                            <option value="#DCDCDC" selected>Pearl Gray (#DCDCDC)</option>
                            <option value="#4169E1">Royal Blue (#4169E1)</option>
                            <option value="#3B2F2F">Dark Brown (#3B2F2F)</option>
                            <option value="#FFFFFF">Pure White (#FFFFFF)</option>
                            <option value="#708238">Olive Green (#708238)</option>
                        </select>
                    </div>
                </div>

                <!-- Match Score Result Card -->
                <div class="bg-charcoal text-white rounded-2xl p-3.5 flex items-center justify-between shadow-md">
                    <div>
                        <span class="text-[9px] text-white/60 uppercase font-mono tracking-wider">HARMONY SCORE</span>
                        <h4 class="text-base font-bold text-warmGold" id="cloth-match-score">96% - Perfect Aura Boost</h4>
                        <p class="text-[10px] text-white/80 mt-0.5" id="cloth-match-desc">โทนสีสว่างนุ่มนวล เข้ากับ Light Summer ขับผิวดูไบรท์เปล่งประกาย</p>
                    </div>
                    <div class="p-2 bg-white/10 rounded-xl text-warmGold shrink-0">
                        <i data-lucide="thumbs-up" class="w-5 h-5"></i>
                    </div>
                </div>
            </div>

            <!-- Curated Outfit Combinations -->
            <div>
                <h3 class="text-xs font-bold text-charcoal/70 uppercase tracking-wider mb-2 px-1">ชุดแต่งกายแนะนำประจำฤดู</h3>
                <div class="space-y-2.5">
                    
                    <div class="glass-card p-3 rounded-2xl border border-white flex items-center gap-3">
                        <div class="w-14 h-14 rounded-xl bg-pastelRose/20 border border-pastelRose/40 flex items-center justify-center shrink-0">
                            <i data-lucide="sparkles" class="w-6 h-6 text-pastelRose"></i>
                        </div>
                        <div class="flex-1">
                            <div class="flex items-center gap-1.5 mb-0.5">
                                <span class="text-[9px] font-bold bg-pastelRose/40 text-charcoal px-2 py-0.5 rounded-full">CASUAL CHIC</span>
                                <span class="text-[9px] font-mono text-textMuted">Look 01</span>
                            </div>
                            <h4 class="text-xs font-bold text-charcoal">เสื้อเชิ้ตพาสเทล + กางเกงลินินสีครีม</h4>
                            <p class="text-[10px] text-textMuted mt-0.5">ลุคสบายๆ สว่างละมุน ตาดูสดใสไม่โทรม</p>
                        </div>
                    </div>

                    <div class="glass-card p-3 rounded-2xl border border-white flex items-center gap-3">
                        <div class="w-14 h-14 rounded-xl bg-softBlue/20 border border-softBlue/40 flex items-center justify-center shrink-0">
                            <i data-lucide="briefcase" class="w-6 h-6 text-softBlue"></i>
                        </div>
                        <div class="flex-1">
                            <div class="flex items-center gap-1.5 mb-0.5">
                                <span class="text-[9px] font-bold bg-softBlue/40 text-charcoal px-2 py-0.5 rounded-full">WORKWEAR</span>
                                <span class="text-[9px] font-mono text-textMuted">Look 02</span>
                            </div>
                            <h4 class="text-xs font-bold text-charcoal">สูทเบลเซอร์สีเทาอ่อน + ซับในสีกุหลาบ</h4>
                            <p class="text-[10px] text-textMuted mt-0.5">เพิ่มความน่าเชื่อถือ มั่นใจสไตล์พรีเมียม</p>
                        </div>
                    </div>

                </div>
            </div>

        </section>

        <!-- ========================================== -->
        <!-- TAB 4: BEAUTY & SHOPPING (LIPSTICK AR & CHECKER) -->
        <!-- ========================================== -->
        <section id="tab-beauty" class="tab-page hidden fade-in flex-col gap-4 mt-1">
            
            <!-- Virtual Cosmetic Lipstick AR Tester -->
            <div class="glass-card rounded-3xl p-4 shadow-glass border border-white/90 space-y-3">
                <div class="flex items-center justify-between">
                    <div>
                        <span class="text-[9px] text-textMuted font-mono uppercase">VIRTUAL AR TESTER</span>
                        <h2 class="text-xs font-bold text-charcoal uppercase tracking-wider flex items-center gap-1.5">
                            <i data-lucide="sparkles" class="w-4 h-4 text-pastelRose"></i>
                            <span>ทดลองสีลิปสติกบนใบหน้า (LIPSTICK AR)</span>
                        </h2>
                    </div>
                    <span class="px-2 py-0.5 rounded-full bg-pastelRose/30 text-charcoal text-[9px] font-bold">LIVE PREVIEW</span>
                </div>

                <!-- Portrait Preview Canvas Overlay -->
                <div class="relative w-full h-52 rounded-2xl overflow-hidden shadow-inner border border-white bg-charcoal/5 flex items-center justify-center">
                    <img id="beauty-user-portrait" src="https://images.unsplash.com/photo-1534528741775-53994a69daeb?auto=format&fit=crop&w=600&q=80" alt="AR User Portrait" class="w-full h-full object-cover">
                    
                    <!-- Simulated Lip Overlay Glow -->
                    <div id="lip-ar-overlay" class="absolute bottom-[32%] left-[43%] w-10 h-4 rounded-full blur-[2px] opacity-75 transition-all duration-300 pointer-events-none mix-blend-multiply" style="background-color: #E8C5C8;"></div>
                    
                    <div class="absolute bottom-2 left-2 glass-pill px-2.5 py-1 rounded-full text-[9px] text-charcoal font-semibold flex items-center gap-1">
                        <span class="w-2 h-2 rounded-full bg-emerald-500 animate-ping"></span>
                        <span id="active-lipstick-name">Soft Dusty Rose (#E8C5C8)</span>
                    </div>
                </div>

                <!-- Texture Selector Buttons -->
                <div class="flex justify-center gap-2">
                    <button type="button" onclick="setLipFinish('gloss')" id="finish-gloss" class="lip-finish-btn px-3 py-1 rounded-full text-[10px] font-bold bg-charcoal text-white shadow-sm transition-all">Glossy</button>
                    <button type="button" onclick="setLipFinish('velvet')" id="finish-velvet" class="lip-finish-btn px-3 py-1 rounded-full text-[10px] font-medium bg-white/80 text-charcoal border border-charcoal/10 transition-all">Velvet Matte</button>
                    <button type="button" onclick="setLipFinish('satin')" id="finish-satin" class="lip-finish-btn px-3 py-1 rounded-full text-[10px] font-medium bg-white/80 text-charcoal border border-charcoal/10 transition-all">Satin Shine</button>
                </div>

                <!-- Lipstick Swatch Palette Selection -->
                <div class="pt-1">
                    <p class="text-[10px] text-textMuted mb-2">แตะเลือกเฉดสีลิปสติกสำหรับฤดูของคุณ:</p>
                    <div class="grid grid-cols-5 gap-2" id="lipstick-swatch-container">
                        <!-- Dynamic Swatches via JS -->
                    </div>
                </div>
            </div>

            <!-- Hair Dye Recommendation Selector -->
            <div class="glass-card rounded-3xl p-4 shadow-glass border border-white/90 space-y-3">
                <div class="flex items-center justify-between">
                    <h3 class="text-xs font-bold text-charcoal uppercase tracking-wider flex items-center gap-1.5">
                        <i data-lucide="scissors" class="w-4 h-4 text-warmGold"></i>
                        <span>สีผมแนะนำขับผิวหน้า (HAIR DYE SELECTOR)</span>
                    </h3>
                </div>

                <div class="grid grid-cols-3 gap-2 text-center text-[10px]">
                    <button type="button" onclick="selectHairDye('Ash Brown', '#695E57')" class="p-2.5 bg-white/80 rounded-2xl border border-white shadow-sm flex flex-col items-center gap-1.5 hover:border-warmGold transition-all">
                        <div class="w-7 h-7 rounded-full border border-black/10" style="background-color: #695E57;"></div>
                        <span class="font-bold text-charcoal">Ash Brown</span>
                        <span class="text-[8px] text-emerald-600 font-semibold">แนะนำ ★</span>
                    </button>

                    <button type="button" onclick="selectHairDye('Milk Tea', '#B89B86')" class="p-2.5 bg-white/80 rounded-2xl border border-white shadow-sm flex flex-col items-center gap-1.5 hover:border-warmGold transition-all">
                        <div class="w-7 h-7 rounded-full border border-black/10" style="background-color: #B89B86;"></div>
                        <span class="font-bold text-charcoal">Milk Tea</span>
                        <span class="text-[8px] text-emerald-600 font-semibold">ปังมาก ✓</span>
                    </button>

                    <button type="button" onclick="selectHairDye('Rose Brown', '#8A5A65')" class="p-2.5 bg-white/80 rounded-2xl border border-white shadow-sm flex flex-col items-center gap-1.5 hover:border-warmGold transition-all">
                        <div class="w-7 h-7 rounded-full border border-black/10" style="background-color: #8A5A65;"></div>
                        <span class="font-bold text-charcoal">Rose Brown</span>
                        <span class="text-[8px] text-emerald-600 font-semibold">ออร่าพุ่ง ✓</span>
                    </button>
                </div>
            </div>

            <!-- Product Barcode / Cosmetics Match Checker -->
            <div class="glass-card rounded-3xl p-4 shadow-glass border border-white/90 space-y-3">
                <div class="flex items-center justify-between">
                    <div>
                        <span class="text-[9px] text-textMuted font-mono uppercase">SHOPPING ASSISTANT</span>
                        <h3 class="text-xs font-bold text-charcoal uppercase tracking-wider flex items-center gap-1.5">
                            <i data-lucide="scan-barcode" class="w-4 h-4 text-charcoal"></i>
                            <span>ตรวจสอบสินค้าก่อนซื้อ (PRODUCT CHECKER)</span>
                        </h3>
                    </div>
                </div>

                <div class="p-3 bg-white/70 rounded-2xl border border-white flex flex-col gap-2">
                    <label for="product-select" class="text-[10px] text-textMuted">เลือกสินค้าคสอ. หรือเสื้อผ้าเพื่อสแกนผล:</label>
                    <div class="flex gap-2">
                        <select id="product-select" class="flex-1 text-xs font-medium text-charcoal bg-white border border-charcoal/10 rounded-xl px-3 py-2 focus:outline-none cursor-pointer">
                            <option value="lip_01">Lipstick: Dusty Rose Velvet (Soft Pink)</option>
                            <option value="lip_02">Lipstick: Neon Flame Orange (Bright Warm)</option>
                            <option value="cloth_01">Blouse: Powder Blue Silk (Light Cool)</option>
                            <option value="cloth_02">Jacket: Deep Mustard Yellow (Warm Dark)</option>
                        </select>
                        <button type="button" onclick="checkProductMatch()" class="px-4 py-2 bg-charcoal text-white font-bold text-xs rounded-xl hover:bg-black active:scale-95 transition-all shadow-sm">
                            เช็กผล
                        </button>
                    </div>
                </div>

                <!-- Product Diagnostic Pass/Avoid Result Box -->
                <div id="product-result-box" class="p-3.5 bg-emerald-50 border border-emerald-200 rounded-2xl flex items-start gap-3">
                    <div class="p-2 rounded-xl bg-emerald-500 text-white shrink-0" id="product-status-icon">
                        <i data-lucide="check-circle-2" class="w-5 h-5"></i>
                    </div>
                    <div>
                        <div class="flex items-center gap-2">
                            <span id="product-status-tag" class="text-[9px] font-bold px-2 py-0.5 rounded-full bg-emerald-200 text-emerald-900 uppercase">🟢 PASS (SUITABLE)</span>
                            <span class="text-[10px] text-textMuted">Match Score 98%</span>
                        </div>
                        <h4 id="product-title-res" class="text-xs font-bold text-charcoal mt-1">Dusty Rose Velvet Lipstick</h4>
                        <p id="product-desc-res" class="text-[10px] text-textMuted leading-relaxed mt-0.5">เฉดสีตรงกับ Light Summer undertone ขับผิวหน้าให้ดูสว่าง นุ่มนวล ละมุน ไม่ดูลอย</p>
                    </div>
                </div>
            </div>

        </section>

        <!-- TIP DETAIL MODAL -->
        <div id="tip-modal" class="fixed inset-0 z-50 bg-charcoal/60 backdrop-blur-md hidden flex-col items-center justify-center p-4">
            <div class="glass-card bg-white/95 p-5 rounded-3xl max-w-xs w-full shadow-2xl flex flex-col gap-3 border border-white fade-in">
                <div class="flex justify-between items-start">
                    <span id="modal-tip-tag" class="text-[9px] font-bold text-warmGold uppercase bg-warmGold/20 px-2 py-0.5 rounded-full">GUIDE</span>
                    <button type="button" onclick="closeTipModal()" class="p-1 rounded-full bg-charcoal/5 text-charcoal/60 hover:text-charcoal">
                        <i data-lucide="x" class="w-4 h-4"></i>
                    </button>
                </div>
                <h3 id="modal-tip-title" class="text-sm font-bold text-charcoal">หัวข้อบทความ</h3>
                <p id="modal-tip-content" class="text-[11px] text-textMuted leading-relaxed">รายละเอียดเนื้อหาบทความแบบเจาะลึก...</p>
                <button type="button" onclick="closeTipModal()" class="w-full py-2.5 rounded-xl bg-charcoal text-white font-semibold text-xs mt-1">
                    เข้าใจแล้ว (CLOSE)
                </button>
            </div>
        </div>

        <!-- AI SCANNING ANIMATION MODAL -->
        <div id="scanning-modal" class="fixed inset-0 z-50 bg-charcoal/60 backdrop-blur-md hidden flex-col items-center justify-center p-6 text-center">
            <div class="glass-card bg-white/90 p-6 rounded-3xl max-w-xs w-full shadow-2xl flex flex-col items-center gap-4 border border-white">
                
                <div class="relative w-36 h-48 rounded-2xl overflow-hidden shadow-inner border-2 border-warmGold/40">
                    <img id="scan-preview-img" src="" alt="Scanning Target" class="w-full h-full object-cover">
                    <div class="scan-line absolute left-0 right-0 h-1 bg-gradient-to-r from-transparent via-warmGold to-transparent shadow-[0_0_12px_#D4AF37]"></div>
                    
                    <svg class="absolute inset-0 w-full h-full pointer-events-none opacity-80" viewBox="0 0 100 130">
                        <circle cx="35" cy="45" r="2" fill="#D4AF37" class="animate-ping"/>
                        <circle cx="65" cy="45" r="2" fill="#D4AF37" class="animate-ping"/>
                        <circle cx="50" cy="62" r="1.5" fill="#E8C5C8"/>
                        <circle cx="50" cy="80" r="2.5" fill="#C2D8C8"/>
                        <path d="M 30,45 Q 50,30 70,45" fill="none" stroke="rgba(212,175,55,0.6)" stroke-width="0.8" stroke-dasharray="2,2"/>
                        <path d="M 25,70 Q 50,105 75,70" fill="none" stroke="rgba(232,197,200,0.6)" stroke-width="0.8" stroke-dasharray="2,2"/>
                    </svg>
                </div>

                <div class="space-y-1">
                    <h3 id="scan-status-title" class="text-sm font-bold text-charcoal font-sans">กำลังวิเคราะห์สีผิวและมิติใบหน้า...</h3>
                    <p id="scan-status-subtitle" class="text-[11px] text-textMuted">ระบบ AI Processing 16-Season System</p>
                </div>

                <div class="w-full bg-softGray rounded-full h-2 overflow-hidden border border-black/5">
                    <div id="scan-progress-bar" class="bg-gradient-to-r from-pastelRose via-warmGold to-softBlue h-full w-0 transition-all duration-300"></div>
                </div>

                <p id="scan-step-text" class="text-[10px] text-charcoal/50 tracking-wide font-mono">STEP 1/3: EXTRACTING SKIN PIGMENTS</p>
            </div>
        </div>

    </main>

    <!-- ========================================== -->
    <!-- FIXED BOTTOM NAVIGATION BAR -->
    <!-- ========================================== -->
    <nav class="fixed bottom-0 left-1/2 -translate-x-1/2 w-full max-w-[428px] glass-nav px-3 py-2 z-40 shadow-nav flex items-center justify-around rounded-t-3xl border-t border-white/80">
        
        <!-- Tab 1: Home Button -->
        <button type="button" onclick="switchTab('tab-home')" id="nav-btn-home" class="nav-btn flex flex-col items-center gap-1 p-1.5 text-charcoal transition-all relative">
            <i data-lucide="home" class="w-5 h-5"></i>
            <span class="text-[9px] font-semibold">หน้าแรก</span>
            <span class="nav-indicator w-1 h-1 rounded-full bg-charcoal absolute -bottom-0.5"></span>
        </button>

        <!-- Tab 2: Scanner Button -->
        <button type="button" onclick="switchTab('tab-scanner')" id="nav-btn-scanner" class="nav-btn flex flex-col items-center gap-1 p-1.5 text-charcoal/40 hover:text-charcoal transition-all relative">
            <i data-lucide="scan" class="w-5 h-5"></i>
            <span class="text-[9px] font-medium">สแกนสีผิว</span>
            <span class="nav-indicator w-1 h-1 rounded-full bg-charcoal hidden absolute -bottom-0.5"></span>
        </button>

        <!-- Tab 3: Wardrobe Button -->
        <button type="button" onclick="switchTab('tab-wardrobe')" id="nav-btn-wardrobe" class="nav-btn flex flex-col items-center gap-1 p-1.5 text-charcoal/40 hover:text-charcoal transition-all relative">
            <i data-lucide="shirt" class="w-5 h-5"></i>
            <span class="text-[9px] font-medium">ตู้เสื้อผ้า</span>
            <span class="nav-indicator w-1 h-1 rounded-full bg-charcoal hidden absolute -bottom-0.5"></span>
        </button>

        <!-- Tab 4: Beauty Button -->
        <button type="button" onclick="switchTab('tab-beauty')" id="nav-btn-beauty" class="nav-btn flex flex-col items-center gap-1 p-1.5 text-charcoal/40 hover:text-charcoal transition-all relative">
            <i data-lucide="sparkles" class="w-5 h-5"></i>
            <span class="text-[9px] font-medium">บิวตี้ & AR</span>
            <span class="nav-indicator w-1 h-1 rounded-full bg-charcoal hidden absolute -bottom-0.5"></span>
        </button>

    </nav>

    <script>
        // --- 16-SEASON DATABASE PROFILES ---
        const SEASON_DATABASE = {
            LIGHT_SUMMER: {
                name: "✨ LIGHT SUMMER",
                thaiName: "ไลท์ ซัมเมอร์",
                summary: "โทนสีสว่าง นุ่มนวล ละมุน ขับให้ออร่าผิวดูไบรท์ขึ้นและกระจ่างใสอย่างธรรมชาติ",
                glowColor: "rgba(232, 197, 200, 0.35)",
                undertone: "Cool (ชมพู/น้ำเงิน)",
                undertoneDesc: "เส้นเลือดสีน้ำเงิน-ม่วง มีอมชมพูละมุน",
                coolRatio: 70, warmRatio: 30,
                value: "High (สว่างสดใส)",
                valueDesc: "ระดับความสว่างผิวสูง โปร่งแสง",
                contrast: "Medium (ปานกลาง)",
                contrastDesc: "สีผม ตา และผิว ละมุนใกล้เคียงกัน",
                faceShape: "Oval (หน้ารูปไข่)",
                faceShapeDesc: "โหนกแก้มได้รูป คางมนสวย สัดส่วนสมดุล",
                summaryUndertone: "Cool Undertone",
                summaryLightness: "High Lightness",
                bestColors: [
                    { name: "Pastel Rose", hex: "#E8C5C8" },
                    { name: "Powder Blue", hex: "#B0C4DE" },
                    { name: "Lavender", hex: "#E6E6FA" },
                    { name: "Soft Mint", hex: "#C2D8C8" },
                    { name: "Pearl Gray", hex: "#DCDCDC" }
                ],
                worstColors: [
                    { name: "Neon Orange", hex: "#FF5F1F" },
                    { name: "Mustard", hex: "#E1AD01" },
                    { name: "Dark Brown", hex: "#3B2F2F" },
                    { name: "Deep Olive", hex: "#556B2F" }
                ],
                recMakeup: "Soft Pink, Dusty Rose, Coral Pink เบาๆ เนื้อ Gloss หรือ Velvet Matte",
                recBlush: "ชมพูนม ปัดแก้มระเรื่อ โทนตานู้ดพาสเทล ชิมเมอร์เม็ดละเอียด",
                recHairColor: "Ash Brown, Milk Tea Brown, Rose Brown น้ำตาลหม่นประกายเทา",
                silverBadge: "แนะนำที่สุด ★", roseGoldBadge: "เข้ากันได้ดี ✓", goldBadge: "ปานกลาง"
            },
            WARM_AUTUMN: {
                name: "🍂 WARM AUTUMN",
                thaiName: "วอร์ม ออทัมน์",
                summary: "โทนสีอบอุ่น เข้มข้น มีมิติ เสริมให้ใบหน้าดูมีเสน่ห์ หรูหรา และเปล่งประกาย",
                glowColor: "rgba(212, 175, 55, 0.35)",
                undertone: "Warm (เหลือง/ทอง)",
                undertoneDesc: "เส้นเลือดสีเขียวอมฟ้า ผิวอันเดอร์โทนเหลืองทอง",
                coolRatio: 25, warmRatio: 75,
                value: "Medium-Deep (อุ่นเข้ม)",
                valueDesc: "ความสว่างระดับปานกลางถึงเข้ม นุ่มนวล",
                contrast: "Medium (ปานกลาง)",
                contrastDesc: "สีตากับสีผมมีความเข้มและอบอุ่นสมดุล",
                faceShape: "Heart (รูปหัวใจ)",
                faceShapeDesc: "หน้าผากกว้าง คางเรียวสวย มีมิติเฉียบคม",
                summaryUndertone: "Warm Undertone",
                summaryLightness: "Medium Warmth",
                bestColors: [
                    { name: "Terracotta", hex: "#E2725B" },
                    { name: "Warm Amber", hex: "#D4AF37" },
                    { name: "Olive Green", hex: "#708238" },
                    { name: "Burnt Orange", hex: "#CC5500" },
                    { name: "Camel", hex: "#C19A6B" }
                ],
                worstColors: [
                    { name: "Icy Blue", hex: "#AFEEEE" },
                    { name: "Magenta", hex: "#FF00FF" },
                    { name: "Pure White", hex: "#FFFFFF" },
                    { name: "Cool Grey", hex: "#808080" }
                ],
                recMakeup: "Brick Red, Maple Rose, Warm Terracotta เนื้อสากหรือแมตต์",
                recBlush: "ส้มพีชอมทอง, น้ำตาลเมเปิ้ล, ไฮไลท์แชมเปญโกลด์",
                recHairColor: "Chestnut Brown, Copper Brown, Chocolate Gold",
                silverBadge: "ปานกลาง", roseGoldBadge: "เข้ากันได้ดี ✓", goldBadge: "แนะนำที่สุด ★"
            },
            BRIGHT_SPRING: {
                name: "🌸 BRIGHT SPRING",
                thaiName: "ไบรท์ สปริง",
                summary: "โทนสีสดใส สว่าง ชัดเจน เพิ่มออร่าความสดชื่น สนุกสนาน และมีชีวิตชีวา",
                glowColor: "rgba(194, 216, 200, 0.4)",
                undertone: "Warm Clear (สว่างใส)",
                undertoneDesc: "ผิวใสเปล่งปลั่ง เส้นเลือดเขียวผสมน้ำเงิน",
                coolRatio: 40, warmRatio: 60,
                value: "High (สว่างสดใส)",
                valueDesc: "ผิวกระจ่างใส มีความสดของสีสูง",
                contrast: "High (ชัดเจน)",
                contrastDesc: "แววตาสดใส ตัดกับสีผิวชัดเจน",
                faceShape: "Round (รูปกลม)",
                faceShapeDesc: "แก้มอิ่ม น่ารัก โครงหน้าละมุนอ่อนเยาว์",
                summaryUndertone: "Warm Clear",
                summaryLightness: "High Brightness",
                bestColors: [
                    { name: "Coral Red", hex: "#FF7F50" },
                    { name: "Bright Peach", hex: "#FFCBA4" },
                    { name: "Warm Yellow", hex: "#FFDB58" },
                    { name: "Apple Green", hex: "#8DB600" },
                    { name: "Turquoise", hex: "#40E0D0" }
                ],
                worstColors: [
                    { name: "Dusty Mauve", hex: "#915C83" },
                    { name: "Charcoal Navy", hex: "#1B2631" },
                    { name: "Muted Beige", hex: "#A99A86" },
                    { name: "Smoky Gray", hex: "#708090" }
                ],
                recMakeup: "Bright Coral, Peach Red, Poppy Pink ฉ่ำวาวสดใส",
                recBlush: "ส้มอมชมพูสดใส (Juicy Peach) ไฮไลท์ฉ่ำวาว",
                recHairColor: "Honey Blonde, Golden Brown, Bright Copper",
                silverBadge: "เข้ากันได้ดี ✓", roseGoldBadge: "แนะนำที่สุด ★", goldBadge: "เข้ากันได้ดี ✓"
            },
            COOL_WINTER: {
                name: "❄️ COOL WINTER",
                thaiName: "คูล วินเทอร์",
                summary: "โทนสีเย็น คมชัด คลาสสิก เสริมลุคให้ดูคมเข้ม โดดเด่น และมีออร่าทรงพลัง",
                glowColor: "rgba(176, 196, 222, 0.4)",
                undertone: "Pure Cool (เย็นคม)",
                undertoneDesc: "เส้นเลือดสีน้ำเงินเข้ม-ม่วง ผิวคมชัดชมพูเย็น",
                coolRatio: 90, warmRatio: 10,
                value: "Deep (คมเข้ม)",
                valueDesc: "ความคมชัดสูง สีตาและผมตัดกับผิวชัดเจน",
                contrast: "High (ชัดเจน)",
                contrastDesc: "ตาดำคมชัด ผิวสว่าง ตัดกันอย่างโดดเด่น",
                faceShape: "Square (รูปเหลี่ยม)",
                faceShapeDesc: "กรามสวย คมชัด มีโครงหน้าสตรองเก๋ไก๋",
                summaryUndertone: "Cool Undertone",
                summaryLightness: "High Contrast",
                bestColors: [
                    { name: "Royal Blue", hex: "#4169E1" },
                    { name: "Ruby Red", hex: "#9B111E" },
                    { name: "Emerald", hex: "#50C878" },
                    { name: "Pure Black", hex: "#000000" },
                    { name: "Icy Violet", hex: "#CF9FFF" }
                ],
                worstColors: [
                    { name: "Golden Orange", hex: "#FFB347" },
                    { name: "Mustard Gold", hex: "#E5A00D" },
                    { name: "Warm Brown", hex: "#8B5A2B" },
                    { name: "Beige Tan", hex: "#D2B48C" }
                ],
                recMakeup: "True Red, Berry Wine, Plum Pink ลิปแมตต์คมชัด",
                recBlush: "ชมพูคูลโทน (Cool Pink), กุหลาบหนาว (Frosty Rose)",
                recHairColor: "Jet Black (ดำสนิท), Dark Ash, Blue-Black",
                silverBadge: "แนะนำที่สุด ★", roseGoldBadge: "เข้ากันได้ดี ✓", goldBadge: "ปานกลาง"
            }
        };

        // --- LIPSTICK SWATCHES DATABASE ---
        const LIPSTICK_SWATCHES = [
            { name: "Soft Dusty Rose", hex: "#E8C5C8", finish: "gloss" },
            { name: "Coral Peach Pink", hex: "#F8A39D", finish: "satin" },
            { name: "Berry Plum Rose", hex: "#A8536A", finish: "velvet" },
            { name: "True Ruby Velvet", hex: "#B82538", finish: "velvet" },
            { name: "Nude Almond Beige", hex: "#D8A78F", finish: "satin" }
        ];

        // --- GLOBAL APP STATE ---
        let currentUploadedImage = "https://images.unsplash.com/photo-1534528741775-53994a69daeb?auto=format&fit=crop&w=600&q=80";
        let currentSeasonKey = "LIGHT_SUMMER";
        let activeTab = "tab-home";

        // Initialization
        document.addEventListener('DOMContentLoaded', () => {
            lucide.createIcons();
            renderWardrobeSwatches("SUMMER");
            renderLipstickSwatches();
            
            // Open first accordion item by default
            const firstAccordionHeader = document.querySelector('.accordion-header');
            if (firstAccordionHeader) {
                const content = firstAccordionHeader.nextElementSibling;
                const icon = firstAccordionHeader.querySelector('.accordion-icon');
                if (content) content.classList.add('open');
                if (icon) icon.style.transform = 'rotate(180deg)';
            }
        });

        // Tab Navigation Switcher
        function switchTab(tabId) {
            activeTab = tabId;

            // Hide all tab pages
            document.querySelectorAll('.tab-page').forEach(page => {
                page.classList.add('hidden');
                page.classList.remove('flex', 'fade-in');
            });

            // Show target page
            const targetPage = document.getElementById(tabId);
            if (targetPage) {
                targetPage.classList.remove('hidden');
                targetPage.classList.add('flex', 'fade-in');
            }

            // Update Nav Buttons active state
            document.querySelectorAll('.nav-btn').forEach(btn => {
                btn.classList.remove('text-charcoal');
                btn.classList.add('text-charcoal/40');
                const indicator = btn.querySelector('.nav-indicator');
                if (indicator) indicator.classList.add('hidden');
            });

            const activeBtn = document.getElementById(`nav-btn-${tabId.replace('tab-', '')}`);
            if (activeBtn) {
                activeBtn.classList.remove('text-charcoal/40');
                activeBtn.classList.add('text-charcoal');
                const activeIndicator = activeBtn.querySelector('.nav-indicator');
                if (activeIndicator) activeIndicator.classList.remove('hidden');
            }

            // Sync user avatar images across pages
            syncUserAvatars();
            window.scrollTo({ top: 0, behavior: 'smooth' });
            lucide.createIcons();
        }

        function syncUserAvatars() {
            document.getElementById('header-user-avatar').src = currentUploadedImage;
            document.getElementById('home-user-img').src = currentUploadedImage;
            document.getElementById('report-user-img').src = currentUploadedImage;
            document.getElementById('drape-user-thumb').src = currentUploadedImage;
            document.getElementById('beauty-user-portrait').src = currentUploadedImage;
        }

        // Photo Upload Handlers
        function triggerFileInput(event) {
            if (event) event.stopPropagation();
            const photoInput = document.getElementById('photo-input');
            photoInput.removeAttribute('capture');
            photoInput.click();
        }

        function triggerCameraInput() {
            const photoInput = document.getElementById('photo-input');
            photoInput.setAttribute('capture', 'user');
            photoInput.click();
        }

        function handleFileSelect(event) {
            const file = event.target.files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = (e) => {
                    setUploadedPhoto(e.target.result);
                };
                reader.readAsDataURL(file);
            }
        }

        function setUploadedPhoto(dataUrl) {
            currentUploadedImage = dataUrl;
            document.getElementById('preview-image').src = dataUrl;
            document.getElementById('upload-placeholder').classList.add('hidden');
            document.getElementById('image-preview-container').classList.remove('hidden');
            document.getElementById('analyze-btn').disabled = false;
            syncUserAvatars();
        }

        function clearPhoto(event) {
            if (event) event.stopPropagation();
            currentUploadedImage = "https://images.unsplash.com/photo-1534528741775-53994a69daeb?auto=format&fit=crop&w=600&q=80";
            document.getElementById('photo-input').value = '';
            document.getElementById('upload-placeholder').classList.remove('hidden');
            document.getElementById('image-preview-container').classList.add('hidden');
            document.getElementById('analyze-btn').disabled = true;
            syncUserAvatars();
        }

        function loadSamplePhoto(sampleType, event) {
            if (event) event.stopPropagation();
            let sampleUrl = '';
            if (sampleType === 'light-summer') {
                sampleUrl = 'https://images.unsplash.com/photo-1534528741775-53994a69daeb?auto=format&fit=crop&w=600&q=80';
                currentSeasonKey = 'LIGHT_SUMMER';
            } else if (sampleType === 'warm-autumn') {
                sampleUrl = 'https://images.unsplash.com/photo-1517841905240-472988babdf9?auto=format&fit=crop&w=600&q=80';
                currentSeasonKey = 'WARM_AUTUMN';
            } else if (sampleType === 'bright-spring') {
                sampleUrl = 'https://images.unsplash.com/photo-1524504388940-b1c1722653e1?auto=format&fit=crop&w=600&q=80';
                currentSeasonKey = 'BRIGHT_SPRING';
            }

            setUploadedPhoto(sampleUrl);
            showToast("โหลดรูปตัวอย่างแล้ว สามารถแตะ ANALYZE NOW ได้เลย");
        }

        // AI Analysis Simulator
        function startAnalysis() {
            if (!currentUploadedImage) return;

            const modal = document.getElementById('scanning-modal');
            const previewImg = document.getElementById('scan-preview-img');
            const progressBar = document.getElementById('scan-progress-bar');
            const statusTitle = document.getElementById('scan-status-title');
            const statusSubtitle = document.getElementById('scan-status-subtitle');
            const stepText = document.getElementById('scan-step-text');

            previewImg.src = currentUploadedImage;
            modal.classList.remove('hidden');
            modal.classList.add('flex');
            progressBar.style.width = '0%';

            setTimeout(() => {
                progressBar.style.width = '35%';
                statusTitle.textContent = "กำลังวิเคราะห์สีผิวและมิติใบหน้า...";
                statusSubtitle.textContent = "ตรวจจับการสะท้อนแสงของเม็ดสีผิว";
                stepText.textContent = "STEP 1/3: EXTRACTING PIGMENT PATTERNS";
            }, 300);

            setTimeout(() => {
                progressBar.style.width = '70%';
                statusTitle.textContent = "คำนวณ Contrast & โครงหน้า...";
                statusSubtitle.textContent = "วัดระดับความต่างของสีตา ผม และผิว";
                stepText.textContent = "STEP 2/3: MAPPING FACIAL METRICS";
            }, 1100);

            setTimeout(() => {
                progressBar.style.width = '100%';
                statusTitle.textContent = "แมตช์ จานสี 16-Season System...";
                statusSubtitle.textContent = "สร้างคำแนะนำสไตล์และเฉดสีที่ดีที่สุด";
                stepText.textContent = "STEP 3/3: GENERATING PERSONAL REPORT";
            }, 1800);

            setTimeout(() => {
                modal.classList.add('hidden');
                modal.classList.remove('flex');

                document.getElementById('upload-section').classList.add('hidden');
                
                const reportSection = document.getElementById('report-section');
                reportSection.classList.remove('hidden');
                reportSection.classList.add('flex', 'fade-in');

                document.getElementById('season-select').value = currentSeasonKey;
                renderReportUI(currentSeasonKey);

                window.scrollTo({ top: 0, behavior: 'smooth' });
            }, 2400);
        }

        function resetAnalysis() {
            const reportSection = document.getElementById('report-section');
            const uploadSection = document.getElementById('upload-section');

            reportSection.classList.add('hidden');
            reportSection.classList.remove('flex');
            uploadSection.classList.remove('hidden');

            window.scrollTo({ top: 0, behavior: 'smooth' });
        }

        function handleSeasonChange(event) {
            currentSeasonKey = event.target.value;
            renderReportUI(currentSeasonKey);
        }

        function renderReportUI(seasonKey) {
            const data = SEASON_DATABASE[seasonKey] || SEASON_DATABASE.LIGHT_SUMMER;

            // Sync Header & Home Season Badge
            document.getElementById('header-season-tag').textContent = `✨ ${data.thaiName}`;
            document.getElementById('home-season-badge').textContent = data.name;
            document.getElementById('home-season-title').textContent = `${data.thaiName} (${seasonKey.replace('_', ' ')})`;

            document.getElementById('report-season-name').textContent = data.name;
            document.getElementById('report-season-title').textContent = data.thaiName;
            document.getElementById('report-season-summary').textContent = data.summary;
            document.getElementById('season-glow-bg').style.backgroundColor = data.glowColor;

            document.getElementById('summary-undertone').textContent = data.summaryUndertone;
            document.getElementById('summary-lightness').textContent = data.summaryLightness;

            // Metrics
            document.getElementById('metric-undertone-val').textContent = data.undertone;
            document.getElementById('metric-undertone-desc').textContent = data.undertoneDesc;

            document.getElementById('cool-bar').style.width = `${data.coolRatio}%`;
            document.getElementById('warm-bar').style.width = `${data.warmRatio}%`;
            document.getElementById('cool-ratio-text').textContent = `COOL ${data.coolRatio}%`;
            document.getElementById('warm-ratio-text').textContent = `WARM ${data.warmRatio}%`;

            document.getElementById('metric-value-val').textContent = data.value;
            document.getElementById('metric-value-desc').textContent = data.valueDesc;

            document.getElementById('metric-contrast-val').textContent = data.contrast;
            document.getElementById('metric-contrast-desc').textContent = data.contrastDesc;

            document.getElementById('metric-faceshape-val').textContent = data.faceShape;
            document.getElementById('metric-faceshape-desc').textContent = data.faceShapeDesc;

            // Swatches Rendering
            const bestContainer = document.getElementById('best-colors-container');
            bestContainer.innerHTML = '';
            data.bestColors.forEach((color, index) => {
                const swatch = document.createElement('button');
                swatch.type = 'button';
                swatch.className = 'flex flex-col items-center gap-1 group focus:outline-none';
                swatch.onclick = () => selectDrapeColor(color.name, color.hex);

                swatch.innerHTML = `
                    <div class="w-full aspect-square rounded-2xl shadow-md border-2 border-white transition-all group-hover:scale-105 group-active:scale-95 relative overflow-hidden flex items-center justify-center" style="background-color: ${color.hex}">
                        ${index === 0 ? '<span class="absolute inset-0 border-2 border-charcoal/30 rounded-2xl"></span>' : ''}
                    </div>
                    <span class="text-[9px] font-medium text-charcoal/80 text-center leading-tight truncate w-full">${color.name}</span>
                `;
                bestContainer.appendChild(swatch);
            });

            if (data.bestColors.length > 0) {
                selectDrapeColor(data.bestColors[0].name, data.bestColors[0].hex);
            }

            const worstContainer = document.getElementById('worst-colors-container');
            worstContainer.innerHTML = '';
            data.worstColors.forEach((color) => {
                const swatch = document.createElement('div');
                swatch.className = 'flex items-center gap-1.5 bg-white/60 p-1.5 rounded-xl border border-white';
                swatch.innerHTML = `
                    <div class="w-4 h-4 rounded-md border border-black/10 flex-shrink-0" style="background-color: ${color.hex}"></div>
                    <span class="text-[9px] font-semibold text-charcoal truncate">${color.name}</span>
                `;
                worstContainer.appendChild(swatch);
            });

            // Recommendations
            document.getElementById('rec-lipstick').textContent = data.recMakeup;
            document.getElementById('rec-blush').textContent = data.recBlush;
            document.getElementById('rec-haircolor').textContent = data.recHairColor;

            document.getElementById('metal-silver-badge').textContent = data.silverBadge;
            document.getElementById('metal-rosegold-badge').textContent = data.roseGoldBadge;
            document.getElementById('metal-gold-badge').textContent = data.goldBadge;

            lucide.createIcons();
        }

        function selectDrapeColor(name, hex) {
            const drapeCircle = document.getElementById('drape-preview-circle');
            const drapeName = document.getElementById('drape-color-name');
            const drapeHex = document.getElementById('drape-color-hex');

            drapeCircle.style.backgroundColor = hex;
            drapeName.textContent = name;
            drapeHex.textContent = hex;

            showToast(`เทียบสี ${name} (${hex}) แล้ว`);
        }

        // Wardrobe Season Palette Explorer
        function filterWardrobeSeason(seasonCategory) {
            document.querySelectorAll('.wardrobe-season-btn').forEach(btn => {
                btn.classList.remove('bg-white', 'font-bold', 'shadow-sm');
                btn.classList.add('text-charcoal/70', 'font-medium');
            });

            const activeBtn = document.getElementById(`season-tab-${seasonCategory}`);
            if (activeBtn) {
                activeBtn.classList.add('bg-white', 'font-bold', 'shadow-sm');
                activeBtn.classList.remove('text-charcoal/70');
            }

            renderWardrobeSwatches(seasonCategory);
        }

        function renderWardrobeSwatches(category) {
            const grid = document.getElementById('wardrobe-swatch-grid');
            grid.innerHTML = '';

            const palettes = {
                SPRING: ["#FF7F50", "#FFCBA4", "#FFDB58", "#8DB600", "#40E0D0", "#FF6F61"],
                SUMMER: ["#E8C5C8", "#B0C4DE", "#E6E6FA", "#C2D8C8", "#DCDCDC", "#B4C6E7"],
                AUTUMN: ["#E2725B", "#D4AF37", "#708238", "#CC5500", "#C19A6B", "#8B5A2B"],
                WINTER: ["#4169E1", "#9B111E", "#50C878", "#000000", "#CF9FFF", "#1B2631"]
            };

            const colors = palettes[category] || palettes.SUMMER;
            colors.forEach(hex => {
                const swatch = document.createElement('div');
                swatch.className = 'w-full aspect-square rounded-2xl shadow-sm border-2 border-white cursor-pointer hover:scale-105 transition-all';
                swatch.style.backgroundColor = hex;
                swatch.onclick = () => showToast(`คัดลอกรหัสสีเสื้อผ้า ${hex}`);
                grid.appendChild(swatch);
            });
        }

        // Clothes Color Matcher Simulator
        function calculateClothMatch() {
            const topColor = document.getElementById('top-color-select').value;
            const bottomColor = document.getElementById('bottom-color-select').value;

            document.getElementById('top-color-preview').style.backgroundColor = topColor;
            document.getElementById('bottom-color-preview').style.backgroundColor = bottomColor;

            const scoreElem = document.getElementById('cloth-match-score');
            const descElem = document.getElementById('cloth-match-desc');

            if (topColor === '#B0C4DE' && bottomColor === '#DCDCDC') {
                scoreElem.textContent = "96% - Perfect Aura Boost";
                descElem.textContent = "โทนสีสว่างนุ่มนวล เข้ากับ Light Summer ขับผิวดูไบรท์เปล่งประกาย";
            } else if (topColor === '#E8C5C8' && bottomColor === '#FFFFFF') {
                scoreElem.textContent = "98% - Soft Dream Match";
                descElem.textContent = "คู่สีชมพูพาสเทลและขาว ให้ลุคละมุนอ่อนหวาน สบายตา";
            } else if (topColor === '#E2725B') {
                scoreElem.textContent = "65% - Moderate Contrast";
                descElem.textContent = "โทนสีวอร์มเข้ม อาจทำให้ผิวคูลโทนดูหมองลงเล็กน้อย แนะนำเพิ่มเครื่องประดับเงิน";
            } else {
                scoreElem.textContent = "88% - Good Harmony";
                descElem.textContent = "การแมตช์สีที่มีสมดุลกำลังดี เพิ่มความมั่นใจและคอมพลีทลุค";
            }
        }

        // Beauty AR Lipstick Tester
        function renderLipstickSwatches() {
            const container = document.getElementById('lipstick-swatch-container');
            container.innerHTML = '';

            LIPSTICK_SWATCHES.forEach((lip, index) => {
                const btn = document.createElement('button');
                btn.type = 'button';
                btn.className = 'flex flex-col items-center gap-1 group focus:outline-none';
                btn.onclick = () => applyLipstickAR(lip.name, lip.hex);

                btn.innerHTML = `
                    <div class="w-full aspect-square rounded-2xl border-2 border-white shadow-md flex items-center justify-center transition-transform group-active:scale-95" style="background-color: ${lip.hex};">
                        ${index === 0 ? '<div class="w-2 h-2 rounded-full bg-white"></div>' : ''}
                    </div>
                    <span class="text-[8px] font-medium text-charcoal/80 truncate w-full text-center">${lip.name}</span>
                `;
                container.appendChild(btn);
            });
        }

        function applyLipstickAR(name, hex) {
            const lipOverlay = document.getElementById('lip-ar-overlay');
            const nameTag = document.getElementById('active-lipstick-name');

            lipOverlay.style.backgroundColor = hex;
            nameTag.textContent = `${name} (${hex})`;
            showToast(`ทดลองสีลิป ${name} บนใบหน้าแล้ว`);
        }

        function setLipFinish(finishType) {
            document.querySelectorAll('.lip-finish-btn').forEach(btn => {
                btn.classList.remove('bg-charcoal', 'text-white');
                btn.classList.add('bg-white/80', 'text-charcoal');
            });

            const activeBtn = document.getElementById(`finish-${finishType}`);
            if (activeBtn) {
                activeBtn.classList.add('bg-charcoal', 'text-white');
                activeBtn.classList.remove('bg-white/80');
            }

            const lipOverlay = document.getElementById('lip-ar-overlay');
            if (finishType === 'gloss') {
                lipOverlay.style.opacity = '0.85';
                lipOverlay.style.filter = 'blur(1px)';
            } else if (finishType === 'velvet') {
                lipOverlay.style.opacity = '0.95';
                lipOverlay.style.filter = 'none';
            } else {
                lipOverlay.style.opacity = '0.75';
                lipOverlay.style.filter = 'blur(2px)';
            }

            showToast(`เปลี่ยนฟินิชลิปสติกเป็น ${finishType.toUpperCase()}`);
        }

        function selectHairDye(name, hex) {
            showToast(`เลือกสีผม ${name} (${hex}) เข้ากับรูปหน้าของคุณ`);
        }

        // Product Match Checker
        function checkProductMatch() {
            const productVal = document.getElementById('product-select').value;
            const resBox = document.getElementById('product-result-box');
            const statusTag = document.getElementById('product-status-tag');
            const titleRes = document.getElementById('product-title-res');
            const descRes = document.getElementById('product-desc-res');

            if (productVal === 'lip_01') {
                statusTag.className = 'text-[9px] font-bold px-2 py-0.5 rounded-full bg-emerald-200 text-emerald-900 uppercase';
                statusTag.textContent = '🟢 PASS (SUITABLE)';
                titleRes.textContent = 'Dusty Rose Velvet Lipstick';
                descRes.textContent = 'เฉดสีตรงกับ Light Summer undertone ขับผิวหน้าให้ดูสว่าง นุ่มนวล ละมุน ไม่ดูลอย';
            } else if (productVal === 'lip_02') {
                statusTag.className = 'text-[9px] font-bold px-2 py-0.5 rounded-full bg-red-200 text-red-900 uppercase';
                statusTag.textContent = '🔴 AVOID (CLASH)';
                titleRes.textContent = 'Neon Flame Orange Lipstick';
                descRes.textContent = 'โทนส้มสะท้อนแสงมีอันเดอร์โทนอุ่นจัด อาจทำให้คูลโทนดูผิวคล้ำล้าหมอง';
            } else if (productVal === 'cloth_01') {
                statusTag.className = 'text-[9px] font-bold px-2 py-0.5 rounded-full bg-emerald-200 text-emerald-900 uppercase';
                statusTag.textContent = '🟢 PASS (BEST COLOR)';
                titleRes.textContent = 'Powder Blue Silk Blouse';
                descRes.textContent = 'โทนสีฟ้าพาสเทลโปร่งแสง เพิ่มออร่าความใสเปล่งประกาย สว่างละมุน';
            } else {
                statusTag.className = 'text-[9px] font-bold px-2 py-0.5 rounded-full bg-amber-200 text-amber-900 uppercase';
                statusTag.textContent = '🟡 NEUTRAL';
                titleRes.textContent = 'Deep Mustard Yellow Jacket';
                descRes.textContent = 'สีเหลืองมัสตาร์ดมีความอุ่นจัด แนะนำใส่จับคู่กับเสื้อตัวในสีขาวเพื่อดึงสมดุลผิว';
            }

            showToast("ตรวจสอบผลแมตช์สินค้าเรียบร้อย");
        }

        // UI Helpers
        function toggleAccordion(button) {
            const content = button.nextElementSibling;
            const icon = button.querySelector('.accordion-icon');
            const isOpen = content.classList.contains('open');

            document.querySelectorAll('.accordion-content').forEach(c => c.classList.remove('open'));
            document.querySelectorAll('.accordion-icon').forEach(i => i.style.transform = 'rotate(0deg)');

            if (!isOpen) {
                content.classList.add('open');
                if (icon) icon.style.transform = 'rotate(180deg)';
            }
        }

        function showTipModal(type) {
            const modal = document.getElementById('tip-modal');
            const title = document.getElementById('modal-tip-title');
            const content = document.getElementById('modal-tip-content');
            const tag = document.getElementById('modal-tip-tag');

            if (type === 'jewelry') {
                tag.textContent = "JEWELRY GUIDE";
                title.textContent = "3 เทคนิคเลือกเครื่องประดับเงิน vs ทอง";
                content.textContent = "1. เช็กเส้นเลือดที่ข้อมือ: หากเป็นสีน้ำเงิน-ม่วง เครื่องประดับ Silver (เงิน) และ Rose Gold จะขับผิวดูสว่างที่สุด\n2. สังเกตการสะท้อนแสง: หากใส่เงินแล้วหน้าดูใสไบรท์ แสดงว่าคุณเป็น Cool Undertone\n3. ผสมโลหะอย่างลงตัว: สามารถใส่ Rose Gold คู่กับ Silver เพื่อเพิ่มความมิติเก๋ไก๋";
            } else {
                tag.textContent = "HAIR COLOR GUIDE";
                title.textContent = "โทนสีผม Milk Tea Brown ทำแล้วขับผิวไบรท์";
                content.textContent = "สีผมชานม Milk Tea Brown เป็นสีประกายหม่นผสมเบจละมุน ช่วยลดทอนความเหลืองบนใบหน้า ทำให้ผิวหน้าดูสว่าง กระจ่างใส และนุ่มนวล เหมาะสำหรับโทน Light Summer และ Soft Autumn อย่างยิ่ง!";
            }

            modal.classList.remove('hidden');
            modal.classList.add('flex');
            lucide.createIcons();
        }

        function closeTipModal() {
            const modal = document.getElementById('tip-modal');
            modal.classList.add('hidden');
            modal.classList.remove('flex');
        }

        function showToast(message) {
            const toast = document.getElementById('toast');
            const toastMsg = document.getElementById('toast-message');
            toastMsg.textContent = message;

            toast.classList.remove('-translate-y-16', 'opacity-0', 'pointer-events-none');
            toast.classList.add('translate-y-0', 'opacity-100');

            setTimeout(() => {
                toast.classList.add('-translate-y-16', 'opacity-0', 'pointer-events-none');
                toast.classList.remove('translate-y-0', 'opacity-100');
            }, 2200);
        }
    </script>
</body>
</html>
