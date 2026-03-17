<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>小新护黄 - 智能监测系统</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap');
        body { font-family: 'Inter', -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; }
        .pb-safe { padding-bottom: env(safe-area-inset-bottom, 1rem); }
        .animate-fadeIn { animation: fadeIn 0.4s cubic-bezier(0.16, 1, 0.3, 1) forwards; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(8px); } to { opacity: 1; transform: translateY(0); } }
        .hidden { display: none; }
        .active-tab-content { display: block !important; }
    </style>
</head>
<body class="bg-[#faf8f5] text-slate-800 pb-24 min-h-screen">

    <!-- 1. 登录页面 -->
    <div id="loginPage" class="min-h-screen bg-orange-50 flex flex-col items-center justify-center p-6 relative overflow-hidden">
        <div class="absolute top-[-10%] left-[-10%] w-64 h-64 bg-orange-200 rounded-full mix-blend-multiply filter blur-3xl opacity-50"></div>
        <div class="absolute bottom-[-10%] right-[-10%] w-64 h-64 bg-red-200 rounded-full mix-blend-multiply filter blur-3xl opacity-50"></div>
        
        <div class="z-10 w-full max-w-sm bg-white/80 backdrop-blur-xl rounded-3xl p-8 shadow-xl border border-white">
            <div class="text-center mb-10">
                <div class="w-20 h-20 bg-gradient-to-tr from-orange-400 to-red-400 rounded-2xl mx-auto flex items-center justify-center mb-4 shadow-lg shadow-orange-200">
                    <i data-lucide="activity" class="text-white w-10 h-10"></i>
                </div>
                <h1 class="text-2xl font-bold text-slate-800 tracking-tight">小新护黄</h1>
                <p class="text-orange-600/80 text-sm mt-2 font-medium">SMART NEONATAL CARE</p>
            </div>

            <div class="flex bg-slate-100 p-1 rounded-xl mb-6">
                <button id="btn-phone-login" onclick="switchLoginType('phone')" class="flex-1 py-2 text-sm font-medium rounded-lg transition-all bg-white text-orange-600 shadow-sm">手机登录</button>
                <button id="btn-wechat-login" onclick="switchLoginType('wechat')" class="flex-1 py-2 text-sm font-medium rounded-lg transition-all text-slate-500">微信登录</button>
            </div>

            <!-- 手机号表单 -->
            <div id="form-phone" class="space-y-4">
                <div class="relative">
                    <i data-lucide="phone" class="absolute left-3 top-1/2 -translate-y-1/2 text-slate-400 w-4 h-4"></i>
                    <input type="tel" id="login-phone" placeholder="请输入手机号码" class="w-full bg-slate-50 border-none rounded-xl py-3 pl-10 pr-4 text-slate-700 focus:ring-2 focus:ring-orange-400 outline-none text-sm">
                </div>
                <div class="flex gap-2">
                    <input type="text" id="login-code" placeholder="验证码" class="w-full bg-slate-50 border-none rounded-xl py-3 px-4 text-slate-700 focus:ring-2 focus:ring-orange-400 outline-none text-sm">
                    <button id="btn-get-code" onclick="startCountdown()" class="whitespace-nowrap px-4 rounded-xl text-sm font-bold bg-orange-100 text-orange-600 hover:bg-orange-200 transition-all">获取验证码</button>
                </div>
                <button onclick="handleLogin()" class="w-full bg-gradient-to-r from-orange-500 to-red-500 text-white py-3.5 rounded-xl font-bold text-lg shadow-lg shadow-orange-200 hover:opacity-90 mt-4">登 录</button>
            </div>

            <!-- 微信按钮 -->
            <div id="form-wechat" class="hidden text-center py-4">
                <button onclick="handleLogin()" class="w-full bg-[#07C160] text-white py-3.5 rounded-xl font-bold text-lg shadow-lg shadow-green-200 flex items-center justify-center gap-2">
                    <i data-lucide="message-circle" class="w-6 h-6"></i> 微信授权登录
                </button>
            </div>
        </div>
    </div>

    <!-- 2. 主应用界面 -->
    <div id="appInterface" class="hidden">
        <header class="px-6 py-5 flex justify-between items-center sticky top-0 z-20 bg-[#faf8f5]/80 backdrop-blur-md">
            <div class="flex items-center gap-2">
                <div class="bg-gradient-to-tr from-orange-400 to-red-400 p-1.5 rounded-lg"><i data-lucide="activity" class="text-white w-5 h-5"></i></div>
                <h1 class="text-lg font-bold tracking-tight">小新护黄</h1>
            </div>
            <div class="flex items-center gap-1.5 text-xs text-green-600 bg-green-50 px-2.5 py-1 rounded-full border border-green-100">
                <div class="w-1.5 h-1.5 rounded-full bg-green-500 animate-pulse"></div>云端同步中
            </div>
        </header>

        <main id="mainContent" class="max-w-md mx-auto px-5">
            <!-- 首页内容 -->
            <div id="tab-home" class="tab-pane space-y-5 animate-fadeIn">
                <!-- 宝宝档案卡片 -->
                <div class="relative bg-gradient-to-br from-[#ff8c6b] to-[#ff5b60] rounded-3xl p-6 text-white shadow-xl shadow-red-200/50 overflow-hidden">
                    <div class="flex justify-between items-start mb-6 relative z-10">
                        <div class="flex items-center gap-3">
                            <div class="w-12 h-12 bg-white/20 backdrop-blur-sm rounded-full flex items-center justify-center border border-white/30"><i data-lucide="user" class="w-6 h-6"></i></div>
                            <div>
                                <h2 id="disp-name" class="text-xl font-bold">小语滋</h2>
                                <p id="disp-birth" class="text-xs text-white/80 mt-1 flex items-center gap-1"><i data-lucide="clock" class="w-3 h-3"></i> 出生于 --</p>
                            </div>
                        </div>
                        <button onclick="toggleModal(true)" class="w-8 h-8 bg-white/20 rounded-full flex items-center justify-center border border-white/20"><i data-lucide="edit-3" class="w-4 h-4"></i></button>
                    </div>
                    <div class="grid grid-cols-2 gap-4 relative z-10">
                        <div class="bg-black/10 rounded-2xl p-4 backdrop-blur-sm">
                            <div class="text-white/70 text-xs mb-1">精准日龄</div>
                            <div id="disp-age" class="text-2xl font-bold font-mono">0<span class="text-sm font-normal mx-1">天</span>0<span class="text-sm font-normal ml-1">小时</span></div>
                        </div>
                        <div class="bg-black/10 rounded-2xl p-4 backdrop-blur-sm">
                            <div class="text-white/70 text-xs mb-1">当前体重</div>
                            <div id="disp-weight" class="text-2xl font-bold font-mono">-- <span class="text-sm font-normal">kg</span></div>
                        </div>
                    </div>
                </div>

                <!-- 拍摄标准化指南 -->
                <div class="bg-white rounded-3xl p-5 shadow-sm border border-orange-100">
                    <div class="flex items-center justify-between mb-4">
                        <h3 class="font-bold text-slate-800 flex items-center gap-2 text-sm"><i data-lucide="camera" class="text-orange-500 w-4 h-4"></i> 拍摄标准化指南</h3>
                        <button onclick="toggleGuide()" id="guide-toggle-btn" class="text-[10px] font-bold text-orange-500 bg-orange-50 px-3 py-1 rounded-full border border-orange-100">展开详情</button>
                    </div>
                    <div class="grid grid-cols-3 gap-3">
                        <div class="flex flex-col items-center gap-1 text-center">
                            <div class="w-full aspect-square bg-slate-50 rounded-2xl flex items-center justify-center text-orange-400"><i data-lucide="lightbulb" class="w-6 h-6"></i></div>
                            <span class="text-[10px] text-slate-500">充足光照</span>
                        </div>
                        <div class="flex flex-col items-center gap-1 text-center">
                            <div class="w-full aspect-square bg-slate-50 rounded-2xl border-2 border-dashed border-orange-200 flex items-center justify-center text-orange-400"><i data-lucide="focus" class="w-6 h-6"></i></div>
                            <span class="text-[10px] text-slate-500">对准前额</span>
                        </div>
                        <div class="flex flex-col items-center gap-1 text-center">
                            <div class="w-full aspect-square bg-slate-50 rounded-2xl flex items-center justify-center text-orange-400"><i data-lucide="baby" class="w-6 h-6"></i></div>
                            <span class="text-[10px] text-slate-500">平躺静止</span>
                        </div>
                    </div>
                    <div id="guide-details" class="hidden mt-4 pt-4 border-t border-slate-50 space-y-3">
                        <div class="flex gap-3">
                            <div class="text-orange-500 font-bold text-sm">01</div>
                            <p class="text-[10px] text-slate-500 leading-relaxed">避免在强光直射或阴影下拍摄，建议使用自然光充足的室内环境。</p>
                        </div>
                        <div class="flex gap-3">
                            <div class="text-orange-500 font-bold text-sm">02</div>
                            <p class="text-[10px] text-slate-500 leading-relaxed">保持镜头垂直于宝宝额头平坦皮肤，距离约15-20厘米，确保自动对焦清晰。</p>
                        </div>
                    </div>
                </div>

                <div class="bg-orange-50/80 rounded-2xl p-4 border border-orange-100 flex gap-3 items-start">
                    <i data-lucide="info" class="text-orange-500 w-5 h-5"></i>
                    <div>
                        <h4 class="font-bold text-orange-900 text-sm mb-1">温馨提醒</h4>
                        <p class="text-xs text-orange-700/80 leading-relaxed">黄疸爬坡期建议每 4-6 小时监测一次，如短时间内上升过快请及时就医。</p>
                    </div>
                </div>
            </div>

            <!-- 检测内容 -->
            <div id="tab-detect" class="tab-pane hidden animate-fadeIn space-y-6">
                <div id="syncSteps" class="bg-white rounded-3xl p-6 shadow-sm border border-orange-50 space-y-6">
                    <!-- 动态步进 -->
                </div>
                
                <div id="confirmCard" class="hidden mt-8 bg-white p-6 rounded-3xl border border-orange-100 shadow-xl text-center animate-fadeIn">
                    <h3 class="text-sm font-bold text-slate-700 mb-2">本次测定结果</h3>
                    <div id="resultValue" class="text-5xl font-bold text-orange-500 mb-2 font-mono">--</div>
                    <div id="resultRisk" class="inline-block px-4 py-1.5 rounded-full text-xs font-bold bg-slate-50 border mb-8">风险评估：--</div>
                    <div class="flex gap-3">
                        <button onclick="switchTab('home')" class="flex-1 py-3.5 rounded-2xl font-bold text-slate-500 bg-white border border-slate-200 flex items-center justify-center gap-2"><i data-lucide="trash-2" class="w-4 h-4"></i> 放弃</button>
                        <button onclick="saveTempResult()" class="flex-1 py-3.5 rounded-2xl font-bold text-white bg-gradient-to-r from-orange-500 to-red-500 flex items-center justify-center gap-2 shadow-lg shadow-orange-200"><i data-lucide="save" class="w-4 h-4"></i> 保存记录</button>
                    </div>
                </div>
            </div>

            <!-- 档案内容 -->
            <div id="tab-records" class="tab-pane hidden animate-fadeIn space-y-6">
                <h2 class="text-xl font-bold flex items-center gap-2"><i data-lucide="bar-chart-2" class="text-orange-500 w-6 h-6"></i> 监测日记</h2>
                
                <div class="bg-white rounded-2xl border border-orange-100 p-2 shadow-sm relative">
                    <h3 class="text-[10px] font-bold text-slate-400 absolute top-3 left-4">mg/dL</h3>
                    <div id="chartBox" class="w-full relative h-[220px]">
                        <!-- SVG Chart -->
                    </div>
                </div>

                <div class="space-y-3 mt-6">
                    <h3 class="text-sm font-bold text-slate-500 flex items-center gap-1"><i data-lucide="history" class="w-4 h-4"></i> 详细历史数据</h3>
                    <div id="historyItems" class="space-y-3">
                        <!-- History Items -->
                    </div>
                </div>
            </div>
        </main>

        <!-- 底部导航 -->
        <nav class="fixed bottom-0 left-0 right-0 z-30 pb-safe">
            <div class="max-w-md mx-auto px-6 pb-6">
                <div class="bg-white rounded-full shadow-xl border border-slate-100 flex justify-between items-center px-6 py-2">
                    <button onclick="switchTab('home')" class="nav-btn flex flex-col items-center p-2 text-orange-500" data-tab="home"><i data-lucide="home" class="w-5 h-5"></i><span class="text-[10px] font-bold">首页</span></button>
                    <div class="relative -top-6"><button onclick="runDetectionFlow()" class="w-16 h-16 rounded-full bg-gradient-to-tr from-[#ff8c6b] to-[#ff5b60] text-white shadow-lg border-4 border-[#faf8f5] flex items-center justify-center transform active:scale-95"><i data-lucide="camera" class="w-6 h-6"></i></button></div>
                    <button onclick="switchTab('records')" class="nav-btn flex flex-col items-center p-2 text-slate-400" data-tab="records"><i data-lucide="bar-chart-2" class="w-5 h-5"></i><span class="text-[10px] font-bold">档案</span></button>
                </div>
            </div>
        </nav>
    </div>

    <!-- 编辑弹窗 -->
    <div id="editModal" class="hidden fixed inset-0 bg-slate-900/50 backdrop-blur-sm z-50 flex items-center justify-center p-4">
        <div class="bg-white w-full max-w-sm rounded-3xl overflow-hidden shadow-2xl">
            <div class="bg-orange-50 p-4 flex justify-between items-center border-b border-orange-100">
                <h3 class="font-bold">编辑宝宝资料</h3>
                <button onclick="toggleModal(false)"><i data-lucide="x" class="w-5 h-5"></i></button>
            </div>
            <form id="editForm" class="p-6 space-y-4">
                <div>
                    <label class="block text-xs font-bold text-slate-500 mb-1">宝宝昵称</label>
                    <input type="text" name="name" id="in-name" required class="w-full bg-slate-50 border rounded-xl px-4 py-2.5 text-sm outline-none focus:border-orange-400">
                </div>
                <div>
                    <label class="block text-xs font-bold text-slate-500 mb-1">出生日期与时间</label>
                    <input type="datetime-local" name="birthDate" id="in-birth" required class="w-full bg-slate-50 border rounded-xl px-4 py-2.5 text-sm outline-none focus:border-orange-400">
                </div>
                <div>
                    <label class="block text-xs font-bold text-slate-500 mb-1">出院日期与时间</label>
                    <input type="datetime-local" name="dischargeDate" id="in-discharge" required class="w-full bg-slate-50 border rounded-xl px-4 py-2.5 text-sm outline-none focus:border-orange-400">
                </div>
                <div>
                    <label class="block text-xs font-bold text-slate-500 mb-1">当前体重 (kg)</label>
                    <input type="number" step="0.1" name="weight" id="in-weight" required class="w-full bg-slate-50 border rounded-xl px-4 py-2.5 text-sm outline-none focus:border-orange-400">
                </div>
                <button type="submit" class="w-full bg-slate-800 text-white py-3.5 rounded-xl font-bold shadow-lg">保存修改</button>
            </form>
        </div>
    </div>

    <script>
        // --- 核心状态 ---
        let baby = {
            name: '小语滋',
            birthDate: '2025-06-04T08:30',
            dischargeDate: '2025-06-07T10:00',
            weight: '3.2'
        };

        let records = [
            { id: 1, date: '2025-06-08T09:00', bilirubin: 6.2, risk: '正常', color: 'text-green-500' },
            { id: 2, date: '2025-06-09T14:30', bilirubin: 8.5, risk: '关注', color: 'text-orange-500' },
            { id: 3, date: '2025-06-11T10:15', bilirubin: 13.5, risk: '危险', color: 'text-red-500' },
            { id: 4, date: '2025-06-13T09:40', bilirubin: 7.5, risk: '正常', color: 'text-green-500' }
        ];

        let tempRecord = null;
        let countdownSec = 0;

        // --- 登录逻辑 ---
        function switchLoginType(type) {
            const isPhone = type === 'phone';
            document.getElementById('form-phone').classList.toggle('hidden', !isPhone);
            document.getElementById('form-wechat').classList.toggle('hidden', isPhone);
            
            document.getElementById('btn-phone-login').className = isPhone 
                ? 'flex-1 py-2 text-sm font-medium rounded-lg transition-all bg-white text-orange-600 shadow-sm'
                : 'flex-1 py-2 text-sm font-medium rounded-lg transition-all text-slate-500';
            document.getElementById('btn-wechat-login').className = !isPhone 
                ? 'flex-1 py-2 text-sm font-medium rounded-lg transition-all bg-white text-green-600 shadow-sm'
                : 'flex-1 py-2 text-sm font-medium rounded-lg transition-all text-slate-500';
        }

        function startCountdown() {
            if (countdownSec > 0) return;
            countdownSec = 60;
            const btn = document.getElementById('btn-get-code');
            const timer = setInterval(() => {
                countdownSec--;
                if (countdownSec <= 0) {
                    clearInterval(timer);
                    btn.textContent = '获取验证码';
                    btn.disabled = false;
                } else {
                    btn.textContent = `${countdownSec}s`;
                    btn.disabled = true;
                }
            }, 1000);
        }

        function handleLogin() {
            document.getElementById('loginPage').classList.add('hidden');
            document.getElementById('appInterface').classList.remove('hidden');
            updateHomeUI();
            lucide.createIcons();
        }

        // --- 首页逻辑 ---
        function updateHomeUI() {
            document.getElementById('disp-name').textContent = baby.name;
            document.getElementById('disp-birth').innerHTML = `<i data-lucide="clock" class="w-3 h-3"></i> 出生于 ${baby.birthDate.replace('T', ' ')}`;
            document.getElementById('disp-weight').innerHTML = `${baby.weight} <span class="text-sm font-normal">kg</span>`;
            
            const birth = new Date(baby.birthDate);
            const diff = Math.abs(new Date() - birth);
            const days = Math.floor(diff / (1000 * 60 * 60 * 24));
            const hours = Math.floor((diff % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
            document.getElementById('disp-age').innerHTML = `${days}<span class="text-sm font-normal mx-1">天</span>${hours}<span class="text-sm font-normal ml-1">小时</span>`;
            lucide.createIcons();
        }

        function toggleGuide() {
            const details = document.getElementById('guide-details');
            const btn = document.getElementById('guide-toggle-btn');
            const isHidden = details.classList.toggle('hidden');
            btn.textContent = isHidden ? '展开详情' : '收起详情';
        }

        function toggleModal(show) {
            const modal = document.getElementById('editModal');
            if (show) {
                modal.classList.remove('hidden');
                document.getElementById('in-name').value = baby.name;
                document.getElementById('in-birth').value = baby.birthDate;
                document.getElementById('in-discharge').value = baby.dischargeDate;
                document.getElementById('in-weight').value = baby.weight;
            } else {
                modal.classList.add('hidden');
            }
        }

        document.getElementById('editForm').addEventListener('submit', (e) => {
            e.preventDefault();
            const fd = new FormData(e.target);
            baby.name = fd.get('name');
            baby.birthDate = fd.get('birthDate');
            baby.dischargeDate = fd.get('dischargeDate');
            baby.weight = fd.get('weight');
            toggleModal(false);
            updateHomeUI();
        });

        // --- 导航逻辑 ---
        function switchTab(tabId) {
            document.querySelectorAll('.tab-pane').forEach(p => p.classList.add('hidden'));
            document.getElementById(`tab-${tabId}`).classList.remove('hidden');
            document.querySelectorAll('.nav-btn').forEach(btn => {
                const active = btn.getAttribute('data-tab') === tabId;
                btn.classList.toggle('text-orange-500', active);
                btn.classList.toggle('text-slate-400', !active);
            });
            if (tabId === 'records') renderRecords();
            lucide.createIcons();
        }

        // --- 检测同步逻辑 ---
        async function runDetectionFlow() {
            switchTab('detect');
            document.getElementById('confirmCard').classList.add('hidden');
            const container = document.getElementById('syncSteps');
            container.innerHTML = '';
            
            const flows = [
                { label: '建立安全通信链路', icon: 'shield-check' },
                { label: '获取硬件传感器数据', icon: 'cpu' },
                { label: '云端特征校准', icon: 'cloud-upload' },
                { label: '生成评估报告', icon: 'check-circle' }
            ];

            for(let i=0; i<flows.length; i++) {
                container.innerHTML += `
                    <div class="flex gap-4 items-start animate-fadeIn">
                        <div class="w-10 h-10 rounded-full bg-orange-500 text-white flex items-center justify-center animate-pulse"><i data-lucide="${flows[i].icon}" class="w-5 h-5"></i></div>
                        <div class="pt-2"><h4 class="text-sm font-bold text-orange-600">${flows[i].label}</h4><p class="text-[10px] text-slate-400">同步中...</p></div>
                    </div>
                `;
                lucide.createIcons();
                await new Promise(r => setTimeout(r, 800));
                const last = container.lastElementChild;
                last.querySelector('div').className = "w-10 h-10 rounded-full bg-green-500 text-white flex items-center justify-center";
                last.querySelector('h4').className = "text-sm font-bold text-slate-800";
                last.querySelector('p').textContent = "已就绪";
            }

            const val = (Math.random() * 7 + 6).toFixed(1);
            tempRecord = {
                id: Date.now(),
                date: new Date().toISOString().slice(0, 16),
                bilirubin: parseFloat(val),
                risk: val > 12 ? '危险' : (val > 8 ? '关注' : '正常'),
                color: val > 12 ? 'text-red-500' : (val > 8 ? 'text-orange-500' : 'text-green-500')
            };

            document.getElementById('resultValue').innerHTML = `${val} <span class="text-sm text-slate-400 font-normal">mg/dL</span>`;
            document.getElementById('resultRisk').textContent = `风险评估：${tempRecord.risk}`;
            document.getElementById('resultRisk').className = `inline-block px-4 py-1.5 rounded-full text-xs font-bold bg-slate-50 border mb-8 ${tempRecord.color}`;
            document.getElementById('confirmCard').classList.remove('hidden');
            lucide.createIcons();
        }

        function saveTempResult() {
            records.push(tempRecord);
            records.sort((a,b) => new Date(a.date) - new Date(b.date));
            switchTab('records');
        }

        // --- 档案与图表逻辑 ---
        function renderRecords() {
            const list = document.getElementById('historyItems');
            list.innerHTML = [...records].reverse().map(r => `
                <div class="bg-white p-4 rounded-2xl shadow-sm border border-slate-100 flex justify-between items-center animate-fadeIn">
                    <div>
                        <div class="text-[10px] text-slate-400 mb-1 font-medium">${r.date.replace('T', ' ')}</div>
                        <div class="text-xl font-bold text-slate-800">${r.bilirubin} <span class="text-xs font-normal text-slate-400">mg/dL</span></div>
                    </div>
                    <div class="px-3 py-1 rounded-full bg-slate-50 text-[10px] font-bold border ${r.color}">${r.risk}</div>
                </div>
            `).join('');

            renderSVGChart();
        }

        function renderSVGChart() {
            const box = document.getElementById('chartBox');
            if(records.length < 1) return;
            
            const w = box.clientWidth;
            const h = 220;
            const p = 30;
            const maxVal = Math.max(20, ...records.map(r => r.bilirubin));
            
            const getX = (i) => p + (i * ((w - p * 2) / Math.max(1, records.length - 1)));
            const getY = (v) => h - p - (v / maxVal) * (h - p * 2);

            const points = records.map((r, i) => `${getX(i)},${getY(r.bilirubin)}`).join(' ');

            box.innerHTML = `
                <svg viewBox="0 0 ${w} ${h}" class="w-full h-full">
                    <!-- 背景风险区 -->
                    <rect x="${p}" y="${getY(12)}" width="${w-p*2}" height="${getY(0)-getY(12)}" fill="#fef2f2" opacity="0.6" />
                    <line x1="${p}" y1="${getY(12)}" x2="${w-p}" y2="${getY(12)}" stroke="#fca5a5" stroke-dasharray="4 4" />
                    
                    <!-- 曲线 -->
                    <polyline points="${points}" fill="none" stroke="#f97316" stroke-width="3" stroke-linecap="round" stroke-linejoin="round" />
                    
                    <!-- 数据点 -->
                    ${records.map((r, i) => `
                        <g class="cursor-pointer group" onclick="showChartPoint(${i}, ${getX(i)}, ${getY(r.bilirubin)})">
                            <circle cx="${getX(i)}" cy="${getY(r.bilirubin)}" r="15" fill="transparent" />
                            <circle cx="${getX(i)}" cy="${getY(r.bilirubin)}" r="5" fill="#fff" stroke="#f97316" stroke-width="2" class="group-hover:r-6 transition-all" />
                        </g>
                    `).join('')}
                </svg>
                <div id="chartTooltip" class="hidden absolute bg-slate-800/95 backdrop-blur text-white p-3 rounded-2xl text-[10px] shadow-2xl pointer-events-none z-50 w-36 -translate-x-1/2 -translate-y-full border border-slate-700">
                    <div id="tt-date" class="font-bold border-b border-slate-600 mb-2 pb-1 opacity-80">--</div>
                    <div class="flex justify-between mb-1"><span>数值:</span><span id="tt-val" class="font-bold text-orange-400 text-sm">--</span></div>
                    <div class="flex justify-between"><span>评估:</span><span id="tt-risk" class="font-bold">--</span></div>
                    <div class="absolute -bottom-1.5 left-1/2 -translate-x-1/2 w-3 h-3 bg-slate-800 rotate-45"></div>
                </div>
            `;
        }

        function showChartPoint(idx, x, y) {
            const r = records[idx];
            const tt = document.getElementById('chartTooltip');
            tt.classList.remove('hidden');
            tt.style.left = `${x}px`;
            tt.style.top = `${y}px`;
            tt.style.marginTop = "-15px";
            
            document.getElementById('tt-date').textContent = r.date.replace('T', ' ');
            document.getElementById('tt-val').textContent = r.bilirubin + ' mg/dL';
            document.getElementById('tt-risk').textContent = r.risk;
            document.getElementById('tt-risk').className = 'font-bold ' + r.color;

            // 点击外部关闭
            setTimeout(() => {
                window.onclick = () => { tt.classList.add('hidden'); window.onclick = null; };
            }, 100);
        }

        window.onload = () => {
            lucide.createIcons();
            // 预设出生日期（为了演示计算）
            const now = new Date();
            now.setDate(now.getDate() - 5);
            baby.birthDate = now.toISOString().slice(0, 16);
        };
    </script>
</body>
</html>
