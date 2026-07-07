<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>구담의밤 - 포켓몬 도감 수집하기</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Font Awesome (아이콘용) -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- QR Code Generator (부스 배치용 QR 출력용) -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>
    <!-- Html5-Qrcode (실시간 QR 코드 및 파일 스캔 통합 라이브러리) -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html5-qrcode/2.3.8/html5-qrcode.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Jua&display=swap');
        body {
            font-family: 'Jua', sans-serif;
            background: radial-gradient(circle, #e0f2fe 0%, #bae6fd 60%, #7dd3fc 100%);
            background-attachment: fixed;
        }
        /* 포켓몬 카드 흔들림 애니메이션 (신규 획득 시) */
        @keyframes bounce-in {
            0% { transform: scale(0.3); opacity: 0; }
            50% { transform: scale(1.1); }
            70% { transform: scale(0.9); }
            100% { transform: scale(1); opacity: 1; }
        }
        .animate-bounce-in {
            animation: bounce-in 0.6s ease-out forwards;
        }
        /* 뮤 전용 반짝임 배경 효과 */
        .sparkle-bg {
            background-image: radial-gradient(circle, rgba(255,182,193,0.5) 0%, rgba(255,255,255,0) 75%);
        }
    </style>
</head>
<body class="text-slate-800 min-h-screen flex flex-col justify-between pb-8">

    <!-- 상단 헤더 (몬스터볼 콘셉트의 강렬한 레드 & 블랙 스트라이프) -->
    <header class="bg-red-500 border-b-8 border-slate-900 p-5 text-center shadow-md relative">
        <div class="absolute left-1/2 bottom-0 transform -translate-x-1/2 translate-y-1/2 w-14 h-14 bg-white border-8 border-slate-900 rounded-full flex items-center justify-center z-10 shadow-md">
            <div class="w-5 h-5 bg-slate-100 border-4 border-slate-400 rounded-full animate-pulse"></div>
        </div>
        <h1 class="text-3xl sm:text-4xl text-yellow-300 tracking-wider drop-shadow-[0_3px_3px_rgba(0,0,0,0.8)]">
            <i class="fa-solid fa-circle-dot mr-2 text-white"></i>구담의밤 포켓몬 도감
        </h1>
        <p class="text-xs sm:text-sm text-red-100 mt-1 drop-shadow-[0_1px_1px_rgba(0,0,0,0.5)]">게임에서 이기고 QR 코드를 찍어 도감을 완성하세요!</p>
    </header>

    <!-- 메인 컨텐츠 영역 -->
    <main class="container mx-auto px-4 py-12 max-w-5xl flex-grow">
        
        <!-- 상태 요약 및 알림 (밝은 하늘색 & 화이트 글래스모피즘 스타일) -->
        <div class="bg-white/90 border-4 border-blue-400 rounded-3xl p-5 mb-8 flex flex-wrap items-center justify-between gap-4 shadow-lg backdrop-blur-sm">
            <div>
                <div class="flex items-center gap-2">
                    <span class="text-blue-900 text-sm font-bold"><i class="fa-solid fa-square-poll-vertical mr-1"></i>현재 도감 달성도</span>
                </div>
                <div class="flex items-center gap-3 mt-1.5">
                    <div class="w-48 bg-slate-200 h-6 rounded-full overflow-hidden border-2 border-slate-800 shadow-inner">
                        <div id="progress-bar" class="bg-gradient-to-r from-yellow-400 to-amber-400 h-full transition-all duration-500" style="width: 0%"></div>
                    </div>
                    <span id="progress-text" class="text-blue-600 text-xl font-black">0 / 5</span>
                </div>
            </div>
            
            <div class="flex gap-2">
                <!-- QR 스캐너 열기 버튼 (입체감 있는 디자인) -->
                <button onclick="openScanner()" class="bg-yellow-400 hover:bg-yellow-300 text-slate-900 font-extrabold px-6 py-3 rounded-2xl border-4 border-slate-900 shadow-[4px_4px_0px_0px_rgba(15,23,42,1)] transition-transform active:translate-y-1 active:shadow-[2px_2px_0px_0px_rgba(15,23,42,1)] flex items-center gap-2 text-lg">
                    <i class="fa-solid fa-qrcode text-xl"></i> QR 코드 스캔하기
                </button>
                <!-- 관리자 메뉴 버튼 (QR 출력 및 리셋용) -->
                <button onclick="openAdminModal()" class="bg-slate-100 hover:bg-slate-200 text-slate-700 p-3 rounded-2xl border-4 border-slate-900 shadow-[4px_4px_0px_0px_rgba(15,23,42,1)] transition-transform active:translate-y-1 active:shadow-[2px_2px_0px_0px_rgba(15,23,42,1)]" title="관리자 메뉴">
                    <i class="fa-solid fa-gear text-lg"></i>
                </button>
            </div>
        </div>

        <!-- 포켓몬 도감 그리드 -->
        <div class="grid grid-cols-2 md:grid-cols-5 gap-6" id="pokedex-grid">
            <!-- 자바스크립트에서 도감 카드를 렌더링합니다. -->
        </div>

    </main>

    <!-- 푸터 -->
    <footer class="text-center text-blue-900/60 text-sm px-4">
        <p>© 2026 구담의밤 포켓몬 도감 | 제작: 자치회</p>
    </footer>

    <!-- 1. QR 스캐너 및 이미지 파일 업로드 겸용 모달 (디버깅 완료) -->
    <div id="scanner-modal" class="fixed inset-0 bg-slate-900/80 z-50 hidden flex-col items-center justify-center p-4 backdrop-blur-sm">
        <div class="bg-white border-8 border-red-500 rounded-3xl p-6 max-w-md w-full relative shadow-2xl text-slate-800">
            <button onclick="closeScanner()" class="absolute top-4 right-4 text-slate-400 hover:text-slate-600 text-2xl">
                <i class="fa-solid fa-xmark"></i>
            </button>
            <h3 class="text-xl text-center text-red-500 font-extrabold mb-4"><i class="fa-solid fa-camera mr-2"></i>포켓몬 획득하기</h3>
            
            <!-- 카메라 스캔 영역 -->
            <div id="reader" class="w-full bg-slate-900 rounded-2xl overflow-hidden border-4 border-slate-900 flex items-center justify-center min-h-[250px] relative">
                <!-- 스캐너 내부 로딩 및 권한 요청 텍스트 -->
            </div>

            <!-- 비상용 보조 수단: QR 이미지 직접 스캔 영역 (카메라 안 켜질 때 최고 해결사) -->
            <div class="mt-4 border-t-2 border-slate-100 pt-4 text-center">
                <p class="text-xs text-slate-500 mb-2 font-bold"><i class="fa-solid fa-triangle-exclamation text-amber-500 mr-1"></i>카메라 연결 오류가 뜨나요?</p>
                <label class="inline-flex items-center gap-2 bg-blue-500 hover:bg-blue-600 text-white font-bold py-2.5 px-4 rounded-xl border-2 border-slate-900 shadow-[2px_2px_0px_0px_rgba(15,23,42,1)] cursor-pointer text-sm transition-transform active:translate-y-0.5">
                    <i class="fa-solid fa-image"></i> 실물 QR 사진 찍기/파일 업로드
                    <input type="file" id="qr-file-input" accept="image/*" class="hidden" onchange="scanLocalFile(this)">
                </label>
            </div>
            
            <div class="mt-3 text-center text-slate-400 text-xs">
                카메라가 차단된 환경에서는 위 파란 버튼을 눌러 카메라 앱으로 QR을 촬영해 주셔도 정상 등록됩니다!
            </div>
        </div>
    </div>

    <!-- 2. 성공 축하 팝업 모달 -->
    <div id="success-modal" class="fixed inset-0 bg-slate-900/80 z-50 hidden flex-col items-center justify-center p-4 backdrop-blur-sm">
        <div class="bg-white border-8 border-yellow-400 rounded-3xl p-8 max-w-sm w-full text-center relative animate-bounce-in shadow-2xl text-slate-800">
            <div class="sparkle-bg absolute inset-0 rounded-2xl -z-10"></div>
            
            <h2 class="text-3xl text-yellow-500 font-black mb-2 drop-shadow-[0_2px_0px_rgba(0,0,0,0.1)]">포켓몬 발견!</h2>
            <p id="success-message" class="text-lg text-slate-700 mb-6">피카츄를 도감에 등록했습니다!</p>
            
            <div class="w-48 h-48 mx-auto bg-slate-50 rounded-full border-4 border-yellow-400 flex items-center justify-center p-4 mb-6 shadow-md relative overflow-hidden">
                <div id="success-image-container" class="w-36 h-36 flex items-center justify-center z-10">
                    <!-- 수집한 포켓몬의 정교한 일러스트 SVG 레이아웃이 유동적으로 들어갑니다. -->
                </div>
                <!-- 도감 등록용 배경 포켓볼 형상 -->
                <div class="absolute inset-0 opacity-5 flex items-center justify-center">
                    <i class="fa-solid fa-circle-dot text-[150px]"></i>
                </div>
            </div>

            <button onclick="closeSuccessModal()" class="w-full bg-red-500 hover:bg-red-600 text-white font-bold py-3 px-6 rounded-2xl border-4 border-slate-900 shadow-[4px_4px_0px_0px_rgba(15,23,42,1)] text-lg">
                도감 확인하기
            </button>
        </div>
    </div>

    <!-- 3. 관리자/부스 운영자 전용 모달 (비밀번호 확인 후 진입) -->
    <div id="admin-auth-modal" class="fixed inset-0 bg-slate-900/80 z-40 hidden flex-col items-center justify-center p-4">
        <div class="bg-white border-4 border-slate-900 rounded-3xl p-6 max-w-sm w-full text-slate-800 shadow-xl">
            <h3 class="text-xl mb-4 text-center font-bold text-slate-800">관리자 비밀번호 입력</h3>
            <input type="password" id="admin-password" placeholder="비밀번호를 입력하세요" class="w-full bg-slate-100 border-2 border-slate-300 rounded-xl p-3 text-center text-slate-800 font-mono mb-4 focus:outline-none focus:border-blue-400">
            <div class="flex gap-2">
                <button onclick="closeAdminAuthModal()" class="w-1/2 bg-slate-200 hover:bg-slate-300 text-slate-700 font-bold p-2.5 rounded-xl">취소</button>
                <button onclick="verifyAdminPassword()" class="w-1/2 bg-yellow-400 text-slate-900 hover:bg-yellow-300 font-bold p-2.5 rounded-xl">확인</button>
            </div>
            <p id="admin-auth-error" class="text-red-500 text-sm text-center mt-2 font-bold hidden">비밀번호가 틀렸습니다.</p>
        </div>
    </div>

    <!-- 4. 관리자 제어판 모달 (QR 생성 및 리셋) -->
    <div id="admin-panel-modal" class="fixed inset-0 bg-slate-900/80 z-40 hidden flex-col items-center justify-center p-4 overflow-y-auto">
        <div class="bg-white border-8 border-blue-500 rounded-3xl p-6 max-w-4xl w-full my-8 text-slate-800 shadow-2xl">
            <div class="flex justify-between items-center mb-6 border-b-2 border-slate-100 pb-4">
                <h2 class="text-2xl text-blue-600 font-extrabold"><i class="fa-solid fa-screwdriver-wrench mr-2"></i>부스 관리자 제어판</h2>
                <button onclick="closeAdminPanel()" class="text-slate-400 hover:text-slate-600 text-2xl">
                    <i class="fa-solid fa-xmark"></i>
                </button>
            </div>

            <!-- 세션 1: 도감 전체 리셋 -->
            <div class="bg-red-50 border-2 border-red-200 p-4 rounded-2xl mb-8 flex flex-wrap items-center justify-between gap-4">
                <div>
                    <h3 class="text-lg text-red-600 font-bold">참가자 데이터 리셋</h3>
                    <p class="text-sm text-slate-500">새 참가자가 올 때 현재 수집된 모든 포켓몬 도감을 비웁니다.</p>
                </div>
                <button onclick="resetPokedex()" class="bg-red-500 hover:bg-red-600 text-white font-bold px-6 py-3 rounded-xl border-2 border-red-700 shadow-sm">
                    <i class="fa-solid fa-trash-can mr-2"></i>도감 완전히 리셋하기
                </button>
            </div>

            <!-- 세션 2: 배포용 QR 코드 생성기 -->
            <div>
                <h3 class="text-xl text-blue-600 mb-2 font-bold"><i class="fa-solid fa-print mr-2"></i>부스 배치용 QR 코드 다운로드/인쇄</h3>
                <p class="text-sm text-slate-500 mb-6">이 QR 코드들을 종이에 인쇄하거나 화면에 띄워 부스 게임 클리어 보상으로 활용하세요.</p>
                
                <div class="grid grid-cols-1 md:grid-cols-5 gap-4">
                    <!-- 포켓몬별 QR 생성 템플릿 -->
                    <div class="bg-slate-50 p-4 rounded-xl text-center border-2 border-slate-200 flex flex-col justify-between items-center shadow-sm">
                        <span class="font-bold text-amber-500">1. 피카츄</span>
                        <div id="qr-pikachu" class="bg-white p-2 rounded-lg my-3 inline-block border border-slate-200"></div>
                        <p class="text-[11px] text-slate-400 mb-2 font-mono">pokemon_pikachu</p>
                        <button onclick="printSingleQR('pikachu', '피카츄')" class="bg-blue-500 hover:bg-blue-600 text-white font-bold text-xs py-1.5 px-3 rounded-lg"><i class="fa-solid fa-print mr-1"></i>인쇄용 열기</button>
                    </div>

                    <div class="bg-slate-50 p-4 rounded-xl text-center border-2 border-slate-200 flex flex-col justify-between items-center shadow-sm">
                        <span class="font-bold text-orange-500">2. 파이리</span>
                        <div id="qr-charmander" class="bg-white p-2 rounded-lg my-3 inline-block border border-slate-200"></div>
                        <p class="text-[11px] text-slate-400 mb-2 font-mono">pokemon_charmander</p>
                        <button onclick="printSingleQR('charmander', '파이리')" class="bg-blue-500 hover:bg-blue-600 text-white font-bold text-xs py-1.5 px-3 rounded-lg"><i class="fa-solid fa-print mr-1"></i>인쇄용 열기</button>
                    </div>

                    <div class="bg-slate-50 p-4 rounded-xl text-center border-2 border-slate-200 flex flex-col justify-between items-center shadow-sm">
                        <span class="font-bold text-sky-500">3. 꼬부기</span>
                        <div id="qr-qr-squirtle" class="bg-white p-2 rounded-lg my-3 inline-block border border-slate-200"></div>
                        <p class="text-[11px] text-slate-400 mb-2 font-mono">pokemon_squirtle</p>
                        <button onclick="printSingleQR('squirtle', '꼬부기')" class="bg-blue-500 hover:bg-blue-600 text-white font-bold text-xs py-1.5 px-3 rounded-lg"><i class="fa-solid fa-print mr-1"></i>인쇄용 열기</button>
                    </div>

                    <div class="bg-slate-50 p-4 rounded-xl text-center border-2 border-slate-200 flex flex-col justify-between items-center shadow-sm">
                        <span class="font-bold text-pink-500">4. 푸린</span>
                        <div id="qr-jigglypuff" class="bg-white p-2 rounded-lg my-3 inline-block border border-slate-200"></div>
                        <p class="text-[11px] text-slate-400 mb-2 font-mono">pokemon_jigglypuff</p>
                        <button onclick="printSingleQR('jigglypuff', '푸린')" class="bg-blue-500 hover:bg-blue-600 text-white font-bold text-xs py-1.5 px-3 rounded-lg"><i class="fa-solid fa-print mr-1"></i>인쇄용 열기</button>
                    </div>

                    <div class="bg-purple-50 p-4 rounded-xl text-center border-2 border-purple-200 flex flex-col justify-between items-center shadow-md">
                        <span class="font-bold text-purple-600">🌟 5. 뮤 (스페셜)</span>
                        <div id="qr-mew" class="bg-white p-2 rounded-lg my-3 inline-block border border-purple-100"></div>
                        <p class="text-[11px] text-purple-400 mb-2 font-mono">pokemon_mew</p>
                        <button onclick="printSingleQR('mew', '뮤(Mew)')" class="bg-purple-500 hover:bg-purple-600 text-white font-bold text-xs py-1.5 px-3 rounded-lg"><i class="fa-solid fa-print mr-1"></i>인쇄용 열기</button>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- 오디오 알림음 -->
    <audio id="sound-success" src="https://assets.mixkit.co/active_storage/sfx/2013/2013-84.wav" preload="auto"></audio>
    <audio id="sound-error" src="https://assets.mixkit.co/active_storage/sfx/2568/2568-84.wav" preload="auto"></audio>
    <audio id="sound-mew" src="https://assets.mixkit.co/active_storage/sfx/1435/1435-84.wav" preload="auto"></audio>

    <script>
        // --- 1. 포켓몬 정적 디자인 데이터 ---
        const POKEMON_DATA = {
            pikachu: {
                name: "피카츄",
                num: "No.0025",
                type: "전기",
                color: "from-yellow-400 to-yellow-500 border-yellow-400",
                html: `
                <svg viewBox="0 0 100 100" class="w-24 h-24 filter drop-shadow-[0_4px_6px_rgba(0,0,0,0.15)]">
                    <path d="M15,10 C20,3 32,2 30,22" fill="#eab308" stroke="#1e293b" stroke-width="2.5" />
                    <path d="M15,10 C16,5 24,3 22,12" fill="#0f172a" />
                    <path d="M85,10 C80,3 68,2 70,22" fill="#eab308" stroke="#1e293b" stroke-width="2.5" />
                    <path d="M85,10 C84,5 76,3 78,12" fill="#0f172a" />
                    <circle cx="50" cy="55" r="36" fill="#facc15" stroke="#1e293b" stroke-width="3" />
                    <circle cx="35" cy="48" r="6" fill="#1e293b" />
                    <circle cx="33" cy="46" r="2.5" fill="#ffffff" />
                    <circle cx="65" cy="48" r="6" fill="#1e293b" />
                    <circle cx="63" cy="46" r="2.5" fill="#ffffff" />
                    <circle cx="22" cy="62" r="7" fill="#ef4444" stroke="#991b1b" stroke-width="1" />
                    <circle cx="78" cy="62" r="7" fill="#ef4444" stroke="#991b1b" stroke-width="1" />
                    <polygon points="50,56 48,53 52,53" fill="#1e293b" />
                    <path d="M44,60 Q47,63 50,60 Q53,63 56,60" fill="none" stroke="#1e293b" stroke-width="3" stroke-linecap="round" />
                </svg>
                `
            },
            charmander: {
                name: "파이리",
                num: "No.0004",
                type: "불꽃",
                color: "from-orange-400 to-red-500 border-orange-400",
                html: `
                <svg viewBox="0 0 100 100" class="w-24 h-24 filter drop-shadow-[0_4px_6px_rgba(0,0,0,0.15)]">
                    <path d="M20,70 Q10,40 40,20 Q60,40 50,80" fill="#f97316" opacity="0.3"/>
                    <path d="M30,80 Q20,50 45,35 Q55,55 45,85" fill="#facc15" opacity="0.4"/>
                    <ellipse cx="50" cy="55" r="35" ry="32" fill="#fb923c" stroke="#1e293b" stroke-width="3" />
                    <ellipse cx="34" cy="46" r="7" ry="9" fill="#1e293b" />
                    <ellipse cx="32" cy="42" r="3" ry="4" fill="#ffffff" />
                    <ellipse cx="35" cy="48" r="4" ry="2" fill="#38bdf8" />
                    <ellipse cx="66" cy="46" r="7" ry="9" fill="#1e293b" />
                    <ellipse cx="64" cy="42" r="3" ry="4" fill="#ffffff" />
                    <ellipse cx="67" cy="48" r="4" ry="2" fill="#38bdf8" />
                    <path d="M25,75 Q50,62 75,75" fill="none" stroke="#fef08a" stroke-width="6" stroke-linecap="round" />
                    <circle cx="47" cy="56" r="1.5" fill="#1e293b" />
                    <circle cx="53" cy="56" r="1.5" fill="#1e293b" />
                    <path d="M40,65 Q50,72 60,65" fill="none" stroke="#1e293b" stroke-width="3" stroke-linecap="round" />
                    <polygon points="42,65 44,68 46,65" fill="#ffffff" />
                    <polygon points="58,65 56,68 54,65" fill="#ffffff" />
                </svg>
                `
            },
            squirtle: {
                name: "꼬부기",
                num: "No.0007",
                type: "물",
                color: "from-blue-400 to-cyan-500 border-blue-400",
                html: `
                <svg viewBox="0 0 100 100" class="w-24 h-24 filter drop-shadow-[0_4px_6px_rgba(0,0,0,0.15)]">
                    <circle cx="20" cy="30" r="8" fill="#38bdf8" opacity="0.3"/>
                    <circle cx="85" cy="45" r="5" fill="#7dd3fc" opacity="0.4"/>
                    <circle cx="50" cy="52" r="35" fill="#93c5fd" stroke="#1e293b" stroke-width="3" />
                    <path d="M20,40 Q50,30 80,40" fill="none" stroke="#60a5fa" stroke-width="2" />
                    <ellipse cx="33" cy="46" r="8" ry="10" fill="#1e293b" />
                    <ellipse cx="31" cy="42" r="3.5" ry="4.5" fill="#ffffff" />
                    <ellipse cx="31" cy="49" r="5" ry="3" fill="#7f1d1d" />
                    <ellipse cx="67" cy="46" r="8" ry="10" fill="#1e293b" />
                    <ellipse cx="65" cy="42" r="3.5" ry="4.5" fill="#ffffff" />
                    <ellipse cx="65" cy="49" r="5" ry="3" fill="#7f1d1d" />
                    <path d="M42,65 Q50,60 58,65" fill="none" stroke="#1e293b" stroke-width="3" stroke-linecap="round" />
                    <ellipse cx="22" cy="58" r="4" ry="2.5" fill="#f43f5e" opacity="0.4" />
                    <ellipse cx="78" cy="58" r="4" ry="2.5" fill="#f43f5e" opacity="0.4" />
                </svg>
                `
            },
            jigglypuff: {
                name: "푸린",
                num: "No.0039",
                type: "노말/페어리",
                color: "from-pink-300 to-rose-400 border-pink-300",
                html: `
                <svg viewBox="0 0 100 100" class="w-24 h-24 filter drop-shadow-[0_4px_6px_rgba(0,0,0,0.15)]">
                    <polygon points="12,35 25,12 40,32" fill="#f472b6" stroke="#1e293b" stroke-width="2.5" stroke-linejoin="round"/>
                    <polygon points="18,31 25,18 33,29" fill="#475569" />
                    <polygon points="88,35 75,12 60,32" fill="#f472b6" stroke="#1e293b" stroke-width="2.5" stroke-linejoin="round"/>
                    <polygon points="82,31 75,18 67,29" fill="#475569" />
                    <circle cx="50" cy="55" r="36" fill="#fbcfe8" stroke="#1e293b" stroke-width="3" />
                    <path d="M40,25 Q50,15 60,28 Q50,32 40,25" fill="#fbcfe8" stroke="#1e293b" stroke-width="2.5" />
                    <path d="M43,26 C47,21 53,21 55,27" fill="none" stroke="#f472b6" stroke-width="1.5" />
                    <circle cx="32" cy="54" r="10" fill="#1e293b" />
                    <circle cx="32" cy="54" r="8" fill="#0d9488" />
                    <circle cx="32" cy="54" r="5" fill="#2dd4bf" />
                    <circle cx="29" cy="50" r="3.5" fill="#ffffff" />
                    <circle cx="68" cy="54" r="10" fill="#1e293b" />
                    <circle cx="68" cy="54" r="8" fill="#0d9488" />
                    <circle cx="68" cy="54" r="5" fill="#2dd4bf" />
                    <circle cx="65" cy="50" r="3.5" fill="#ffffff" />
                    <path d="M46,70 Q50,74 54,70" fill="none" stroke="#1e293b" stroke-width="3" stroke-linecap="round" />
                </svg>
                `
            },
            mew: {
                name: "뮤",
                num: "No.0151",
                type: "에스퍼 (전설)",
                color: "from-purple-400 to-pink-400 border-purple-400",
                isSpecial: true,
                html: `
                <svg viewBox="0 0 100 100" class="w-24 h-24 filter drop-shadow-[0_0_8px_rgba(236,72,153,0.6)]">
                    <circle cx="50" cy="50" r="42" fill="none" stroke="#f472b6" stroke-dasharray="4 4" stroke-width="1.5" class="animate-spin" style="animation-duration: 20s;" />
                    <circle cx="50" cy="50" r="38" fill="none" stroke="#c084fc" opacity="0.3" stroke-width="2" />
                    <path d="M35,35 Q50,22 65,35 Q75,40 70,55 Q62,65 50,62 Q38,65 30,55 Q25,40 35,35 Z" fill="#fbcfe8" stroke="#1e293b" stroke-width="3" />
                    <path d="M34,36 Q28,24 38,30" fill="#fbcfe8" stroke="#1e293b" stroke-width="2.5" />
                    <path d="M66,36 Q72,24 62,30" fill="#fbcfe8" stroke="#1e293b" stroke-width="2.5" />
                    <ellipse cx="40" cy="45" r="6" ry="9" fill="#1e293b" />
                    <ellipse cx="38" cy="41" r="2.5" ry="4" fill="#ffffff" />
                    <ellipse cx="41" cy="46" r="3.5" ry="5" fill="#38bdf8" />
                    <ellipse cx="60" cy="45" r="6" ry="9" fill="#1e293b" />
                    <ellipse cx="58" cy="41" r="2.5" ry="4" fill="#ffffff" />
                    <ellipse cx="61" cy="46" r="3.5" ry="5" fill="#38bdf8" />
                    <path d="M47,56 Q50,54 53,56" fill="none" stroke="#1e293b" stroke-width="2.5" stroke-linecap="round" />
                    <ellipse cx="31" cy="50" r="3" ry="1.5" fill="#f43f5e" opacity="0.4" />
                    <ellipse cx="69" cy="50" r="3" ry="1.5" fill="#f43f5e" opacity="0.4" />
                </svg>
                `
            }
        };

        // --- 2. 메모리 및 영구 복구 저장소 상태 ---
        let collectedPokemon = {
            pikachu: false,
            charmander: false,
            squirtle: false,
            jigglypuff: false,
            mew: false
        };

        let html5QrcodeScanner = null;

        // --- 3. 로딩 시 복원 가동 ---
        window.addEventListener('DOMContentLoaded', () => {
            const savedData = localStorage.getItem('pokeboot_pokedex_v1');
            if (savedData) {
                try {
                    collectedPokemon = JSON.parse(savedData);
                } catch(e) {
                    console.error("데이터 동기화 이상 없음", e);
                }
            }

            renderPokedex();
            updateProgress();
            generateQRcodes();
        });

        // --- 4. 도감 그리기 모듈 ---
        function renderPokedex() {
            const grid = document.getElementById('pokedex-grid');
            grid.innerHTML = '';

            Object.entries(POKEMON_DATA).forEach(([key, pokemon]) => {
                const isCollected = collectedPokemon[key];
                const card = document.createElement('div');
                
                if (isCollected) {
                    card.className = `bg-gradient-to-br ${pokemon.color} border-4 border-slate-900 rounded-3xl p-4 flex flex-col items-center justify-between text-center relative overflow-hidden shadow-[4px_4px_0px_0px_rgba(15,23,42,1)] transition-transform hover:scale-105 duration-300 cursor-pointer text-white`;
                    card.innerHTML = `
                        <div class="w-full flex justify-between items-center mb-1">
                            <span class="text-xs bg-black/30 px-2 py-0.5 rounded-full font-mono">${pokemon.num}</span>
                            <span class="text-[10px] bg-white/20 px-2 py-0.5 rounded-full font-bold">${pokemon.type}</span>
                        </div>
                        <div class="relative w-32 h-32 my-2 flex items-center justify-center bg-white/20 rounded-full backdrop-blur-sm shadow-inner overflow-hidden">
                            ${pokemon.html}
                        </div>
                        <h3 class="text-xl font-black tracking-wide mt-2 text-white drop-shadow-[0_1.5px_1.5px_rgba(0,0,0,0.6)]">${pokemon.name}</h3>
                        <div class="absolute -bottom-1 -right-1 bg-yellow-400 text-slate-900 border-l-4 border-t-4 border-slate-900 px-3 py-1 rounded-tl-2xl text-xs font-black">
                            <i class="fa-solid fa-check mr-1"></i>GET!
                        </div>
                    `;
                } else {
                    if (pokemon.isSpecial) {
                        card.className = "bg-white/70 border-4 border-dashed border-purple-400 rounded-3xl p-4 flex flex-col items-center justify-center text-center relative overflow-hidden h-64 shadow-md group hover:border-purple-500 transition-colors";
                        card.innerHTML = `
                            <div class="absolute top-2 left-2 text-xs bg-purple-100 px-2 py-0.5 rounded-full font-mono text-purple-600 font-bold">No.????</div>
                            <div class="w-28 h-28 my-2 rounded-full bg-purple-50 flex items-center justify-center border-2 border-purple-200 relative">
                                <span class="text-5xl font-black text-purple-400 animate-pulse">?</span>
                                <div class="absolute w-20 h-20 opacity-10 pointer-events-none rounded-xl scale-75 filter brightness-0">
                                    ${pokemon.html}
                                </div>
                            </div>
                            <h3 class="text-lg font-black text-purple-500 tracking-wider">미확인 포켓몬</h3>
                            <p class="text-[11px] text-purple-400/80 mt-1">특별한 비밀 QR이 필요합니다!</p>
                        `;
                    } else {
                        card.className = "bg-white/80 border-4 border-dashed border-sky-300 rounded-3xl p-4 flex flex-col items-center justify-center text-center relative overflow-hidden h-64 shadow-md hover:bg-white transition-all";
                        card.innerHTML = `
                            <div class="w-full flex justify-start mb-1">
                                <span class="text-xs bg-sky-50 px-2 py-0.5 rounded-full font-mono text-sky-500 font-bold">${pokemon.num}</span>
                            </div>
                            <div class="w-28 h-28 my-3 rounded-full bg-sky-50/50 border-4 border-dashed border-sky-200 flex items-center justify-center text-sky-400 relative">
                                <i class="fa-solid fa-circle-notch text-4xl animate-spin text-sky-300" style="animation-duration: 6s;"></i>
                                <span class="absolute text-xs font-bold text-sky-400/80">준비 완료</span>
                            </div>
                            <h3 class="text-lg font-black text-sky-400">${pokemon.name}</h3>
                            <p class="text-[11px] text-sky-400/70">미발견</p>
                        `;
                    }
                }
                grid.appendChild(card);
            });
        }

        // --- 5. 상태 게이지 업데이트 ---
        function updateProgress() {
            const total = Object.keys(POKEMON_DATA).length;
            const collectedCount = Object.values(collectedPokemon).filter(Boolean).length;
            
            const progressPercent = (collectedCount / total) * 100;
            document.getElementById('progress-bar').style.width = `${progressPercent}%`;
            document.getElementById('progress-text').innerText = `${collectedCount} / ${total}`;

            try {
                localStorage.setItem('pokeboot_pokedex_v1', JSON.stringify(collectedPokemon));
            } catch(e) {
                console.error("데이터 백업 이상", e);
            }
        }

        // --- 6. 스캔 분석 핵심 로직 ---
        function processScannedQR(qrCodeMessage) {
            const prefix = "pokemon_";
            if (!qrCodeMessage.startsWith(prefix)) {
                playAudio("sound-error");
                showCustomMessage("올바른 포켓몬 도감용 QR 코드가 아닙니다!");
                return;
            }

            const pokemonKey = qrCodeMessage.replace(prefix, "").toLowerCase();

            if (POKEMON_DATA.hasOwnProperty(pokemonKey)) {
                if (collectedPokemon[pokemonKey]) {
                    playAudio("sound-error");
                    showCustomMessage(`이미 도감에 등록된 포켓몬입니다! (${POKEMON_DATA[pokemonKey].name})`);
                    closeScanner();
                    return;
                }

                collectedPokemon[pokemonKey] = true;
                updateProgress();
                renderPokedex();
                closeScanner();
                showSuccessPopup(pokemonKey);
            } else {
                playAudio("sound-error");
                showCustomMessage("존재하지 않는 포켓몬 코드입니다.");
            }
        }

        // --- 7. 알림음 구동부 ---
        function playAudio(elementId) {
            const audio = document.getElementById(elementId);
            if (audio) {
                audio.currentTime = 0;
                audio.play().catch(err => console.log("오디오 재생 생략", err));
            }
        }

        // --- 8. 축하 모달 구동부 ---
        function showSuccessPopup(pokemonKey) {
            const pokemon = POKEMON_DATA[pokemonKey];
            const messageEl = document.getElementById('success-message');
            const successContainer = document.getElementById('success-image-container');
            
            if (pokemon.isSpecial) {
                playAudio("sound-mew");
                messageEl.innerHTML = `<span class="text-purple-500 font-extrabold text-2xl">✨전설의 포켓몬 발견!✨</span><br><b class="text-xl text-purple-600">[뮤]</b>가 도감에 잠금 해제되었습니다!`;
            } else {
                playAudio("sound-success");
                messageEl.innerHTML = `<b class="text-blue-600 text-xl">${pokemon.name}</b>을(를) 발견하여<br>도감에 무사히 보관했습니다!`;
            }

            successContainer.innerHTML = pokemon.html;
            
            document.getElementById('success-modal').classList.remove('hidden');
            document.getElementById('success-modal').classList.add('flex');
        }

        function closeSuccessModal() {
            document.getElementById('success-modal').classList.add('hidden');
            document.getElementById('success-modal').classList.remove('flex');
        }

        // --- 9. 스캐너 열기 및 디바이스 연동 정밀 보완 (카메라 미작동 완벽 대응) ---
        async function openScanner() {
            document.getElementById('scanner-modal').classList.remove('hidden');
            document.getElementById('scanner-modal').classList.add('flex');

            const readerContainer = document.getElementById('reader');
            
            // 초기 시스템 인식 텍스트 가시화
            readerContainer.innerHTML = `
                <div class="p-6 text-center text-slate-400">
                    <i class="fa-solid fa-spinner animate-spin text-4xl mb-3 text-blue-500"></i>
                    <p class="font-bold text-white text-base">카메라 시스템 준비 중...</p>
                    <p class="text-xs text-slate-400 mt-1.5">상단에 권한 팝업이 뜨면 "허용"을 터치해주세요!</p>
                </div>
            `;

            // 카메라 연동 시도
            try {
                // 브라우저에 명시적인 장치 권한 호출
                const stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: "environment" } })
                               .catch(async () => {
                                   // 후면 획득 불가시 일반 카메라 스트림 재시도
                                   return await navigator.mediaDevices.getUserMedia({ video: true });
                               });
                
                // 임시 스트림 즉시 정리하여 하이재킹 방지
                stream.getTracks().forEach(track => track.stop());
                
                readerContainer.innerHTML = ''; // 기존 텍스트 완벽 소거
                html5QrcodeScanner = new Html5Qrcode("reader");
                
                const config = { 
                    fps: 10, 
                    qrbox: { width: 220, height: 220 },
                    aspectRatio: 1.0
                };

                // 실시간 기기 스캔 구동
                await html5QrcodeScanner.start(
                    { facingMode: "environment" }, 
                    config, 
                    (decodedText) => {
                        processScannedQR(decodedText);
                    },
                    () => { /* 인식 실패 프레임 무시 */ }
                );

            } catch (err) {
                console.warn("실시간 카메라 구동 실패. 보조 파일 모드로 전환 가능성 제공:", err);
                
                // 만약 HTTPS 환경이 아니거나 모바일 앱 인앱 브라우저 차단 시 렌더링될 전용 비상 화면
                readerContainer.innerHTML = `
                    <div class="p-5 text-center text-slate-100 bg-slate-900 w-full h-full flex flex-col justify-center items-center">
                        <i class="fa-solid fa-video-slash text-yellow-400 text-3xl mb-2"></i>
                        <p class="font-bold text-sm text-yellow-300">카메라가 연동되지 않았습니다</p>
                        <p class="text-[11px] text-slate-400 mt-1.5 leading-relaxed">
                            보안 격리 환경이거나 권한이 차단되었습니다.<br>
                            하지만 걱정 마세요! 아래 <b class="text-blue-400">사진 업로드</b> 버튼을 누르면<br>
                            직접 사진을 찍어서 도감을 가동시킬 수 있습니다!
                        </p>
                    </div>
                `;
            }
        }

        // 비상용 수단: 로컬 찍어둔 QR 사진 또는 라이브 촬영본 분석 (Html5Qrcode 내장 파일 분석 API 활용)
        function scanLocalFile(inputElement) {
            if (!inputElement.files || inputElement.files.length === 0) return;
            
            const file = inputElement.files[0];
            const fileScanner = new Html5Qrcode("reader");
            
            showCustomMessage("사진 분석 중... 조금만 기다려주세요!");
            
            // 이미지 파일을 로컬 웹 가상 메모리에서 디코딩
            fileScanner.scanFile(file, true)
                .then(decodedText => {
                    processScannedQR(decodedText);
                    inputElement.value = ""; // 초기화
                })
                .catch(err => {
                    playAudio("sound-error");
                    showCustomMessage("⚠️ QR 코드가 선명하게 보이지 않습니다. 다시 선명하게 정면에서 촬영해주세요!");
                    inputElement.value = ""; // 초기화
                });
        }

        function closeScanner() {
            if (html5QrcodeScanner) {
                html5QrcodeScanner.stop().then(() => {
                    html5QrcodeScanner = null;
                }).catch(err => {
                    console.warn("스캐너 정리 중 세션 종료:", err);
                });
            }
            document.getElementById('scanner-modal').classList.add('hidden');
            document.getElementById('scanner-modal').classList.remove('flex');
        }

        // --- 10. 알림창 대체 모듈 ---
        function showCustomMessage(msg) {
            const alertBox = document.createElement('div');
            alertBox.className = "fixed top-5 left-1/2 transform -translate-x-1/2 bg-white border-4 border-blue-500 text-slate-800 font-extrabold py-4 px-6 rounded-2xl z-50 shadow-2xl flex items-center gap-3 animate-bounce-in max-w-sm w-11/12 text-center text-sm";
            alertBox.innerHTML = `
                <i class="fa-solid fa-circle-info text-blue-500 text-xl"></i>
                <div class="flex-1">${msg}</div>
            `;
            document.body.appendChild(alertBox);
            setTimeout(() => {
                alertBox.remove();
            }, 3500);
        }

        // --- 11. 관리자 모드 구동부 ---
        function openAdminModal() {
            document.getElementById('admin-password').value = '';
            document.getElementById('admin-auth-error').classList.add('hidden');
            document.getElementById('admin-auth-modal').classList.remove('hidden');
            document.getElementById('admin-auth-modal').classList.add('flex');
        }

        function closeAdminAuthModal() {
            document.getElementById('admin-auth-modal').classList.add('hidden');
            document.getElementById('admin-auth-modal').classList.remove('flex');
        }

        function verifyAdminPassword() {
            const passwordInput = document.getElementById('admin-password').value;
            if (passwordInput === "0000" || passwordInput === "1234") {
                closeAdminAuthModal();
                openAdminPanel();
            } else {
                document.getElementById('admin-auth-error').classList.remove('hidden');
            }
        }

        function openAdminPanel() {
            document.getElementById('admin-panel-modal').classList.remove('hidden');
            document.getElementById('admin-panel-modal').classList.add('flex');
        }

        function closeAdminPanel() {
            document.getElementById('admin-panel-modal').classList.add('hidden');
            document.getElementById('admin-panel-modal').classList.remove('flex');
        }

        function resetPokedex() {
            if (confirm("정말로 참가자의 전체 포켓몬 수집 데이터를 초기화하고 처음부터 시작하시겠습니까?")) {
                collectedPokemon = {
                    pikachu: false,
                    charmander: false,
                    squirtle: false,
                    jigglypuff: false,
                    mew: false
                };
                updateProgress();
                renderPokedex();
                closeAdminPanel();
                showCustomMessage("🎉 도감이 새롭게 준비되었습니다! 대기줄 고객을 환영해주세요.");
            }
        }

        function generateQRcodes() {
            const keys = ['pikachu', 'charmander', 'squirtle', 'jigglypuff', 'mew'];
            keys.forEach(key => {
                const targetId = `qr-${key}`;
                const container = document.getElementById(targetId);
                if (container) {
                    container.innerHTML = '';
                    new QRCode(container, {
                        text: `pokemon_${key}`,
                        width: 120,
                        height: 120,
                        colorDark : "#000000",
                        colorLight : "#ffffff",
                        correctLevel : QRCode.CorrectLevel.H
                    });
                }
            });
        }

        function printSingleQR(key, pokemonName) {
            const qrText = `pokemon_${key}`;
            const printWindow = window.open('', '_blank');
            printWindow.document.write(`
                <html>
                <head>
                    <title>${pokemonName} QR 코드 인쇄</title>
                    <style>
                        body {
                            font-family: 'Malgun Gothic', sans-serif;
                            display: flex;
                            flex-direction: column;
                            align-items: center;
                            justify-content: center;
                            height: 90vh;
                            text-align: center;
                            background-color: #f0f9ff;
                        }
                        .box {
                            border: 8px solid #0f172a;
                            padding: 40px;
                            border-radius: 30px;
                            background-color: #ffffff;
                            box-shadow: 0 10px 25px rgba(0,0,0,0.1);
                        }
                        h1 {
                            font-size: 38px;
                            margin-bottom: 5px;
                            color: #ef4444;
                        }
                        p {
                            font-size: 20px;
                            color: #334155;
                            margin-bottom: 30px;
                        }
                        .qr-container {
                            margin: 20px auto;
                            display: inline-block;
                            padding: 15px;
                            border: 4px solid #38bdf8;
                            border-radius: 20px;
                            background: white;
                        }
                        .guide {
                            margin-top: 30px;
                            font-size: 16px;
                            color: #64748b;
                            border-top: 2px dashed #cbd5e1;
                            padding-top: 15px;
                            font-weight: bold;
                        }
                    </style>
                    <script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"><\/script>
                </head>
                <body>
                    <div class="box">
                        <h1>🎁 축하합니다! 🎁</h1>
                        <p>구담의밤 축제 보상: <strong>[${pokemonName}]</strong> 획득 코드</p>
                        <div id="qrcode" class="qr-container"></div>
                        <div class="guide">스마트 패드 화면 of [QR 스캔하기] 카메라에 이 코드를 비추세요!</div>
                    </div>
                    <script>
                        new QRCode(document.getElementById("qrcode"), {
                            text: "${qrText}",
                            width: 280,
                            height: 280
                        });
                        setTimeout(() => { window.print(); }, 500);
                    <\/script>
                </body>
                </html>
            `);
            printWindow.document.close();
        }
    </script>
</body>
</html>
